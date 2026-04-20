# HTB Writeups — Juan Ignacio Ramiz Martinez

> Writeups técnicos de máquinas **retiradas** de Hack The Box.
> Todas las actividades fueron realizadas en entornos legales y controlados.

---

## Metodología

Cada writeup sigue la misma estructura profesional:

```
1. Información de la máquina
2. Reconocimiento      →  host discovery + port scanning
3. Enumeración         →  servicios, shares, directorios
4. Explotación         →  foothold con evidencia
5. Post-Explotación    →  privilege escalation
6. Resumen             →  attack path + flags
```

---

## Máquinas

### 🟢 Easy

| # | Máquina | OS | Categoría | Técnicas principales | Link |
|---|---------|-----|-----------|----------------------|------|
| 01 | **Support** | Windows Server 2022 | Active Directory | SMB enum · .NET reversing · XOR decrypt · RBCD attack | [📄 Writeup](./support/) |

### 🟡 Medium
> *Próximamente*

### 🔴 Hard
> *Próximamente*

---

## Técnicas Cubiertas

```
SMB Null Session           →  Support
.NET Binary Analysis       →  Support
XOR Password Decryption    →  Support
LDAP Enumeration           →  Support
Evil-WinRM                 →  Support
Resource-Based Constrained Delegation (RBCD)  →  Support
BloodHound AD Enumeration  →  Support
```

---

> ⚠️ Estos writeups solo cubren máquinas **retiradas** de HTB.
> Publicar writeups de máquinas activas viola los términos de servicio.
