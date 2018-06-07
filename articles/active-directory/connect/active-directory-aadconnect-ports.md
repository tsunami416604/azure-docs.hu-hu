---
title: Hibrid identitás szükséges portok és protokollok - Azure |} Microsoft Docs
description: Ezen a lapon egy technikai referencia jellegű lap-portokhoz, amelyek szükségesek az Azure AD Connect nyitva kell lennie
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 317c94abdf14d3d88e07e32ab16769bd1f641438
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591296"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Hibrid identitás – szükséges portok és protokollok
A következő dokumentum a szükséges portok és protokollok a hibrid identitáskezelési megoldás megvalósításának műszaki hivatkozást. Használja az alábbi ábrán látható, és tekintse meg a megfelelő táblázatot.

![Mi az az Azure AD Connect?](./media/active-directory-aadconnect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>1. táblázat – Azure AD Connect és a helyszíni AD
Ez a táblázat ismerteti a portok és az Azure AD Connect-kiszolgáló közötti kommunikációhoz szükséges protokollok és a helyszíni AD.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-keresések a cél-erdőre. |
| Kerberos |88 (TCP/UDP) |Kerberos-hitelesítés az AD-erdőben. |
| MS-RPC |135-ÖS (TCP/UDP) |Ha az AD-erdőben is kötődik az Azure AD Connect varázsló a kezdeti konfiguráció során és a jelszó-szinkronizálás során használt. |
| LDAP |389 (TCP/UDP) |Adatok importálása az Active Directoryból használatos. A Kerberos-aláírás és a titkosítással titkosítja az adatokat. |
| RPC | 445 (TCP/UDP) |Zökkenőmentes SSO használják a számítógép-fiók létrehozása az Active Directory-erdőben. |
| LDAP/SSL |636 (TCP/UDP) |Adatok importálása az Active Directoryból használatos. Az adatátvitel van aláírását és titkosítását. Csak akkor használható, ha SSL-t használ. |
| RPC |49152 és 65535 (véletlenszerű magas RPC Port)(TCP/UDP) |Az Azure AD Connect, amikor is kötve van az AD-erdőkkel a kezdeti konfiguráció során, és a jelszó-szinkronizálás során használt. Lásd: [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), és [KB224196](https://support.microsoft.com/kb/224196) további információt. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>2. táblázat – Azure AD Connect és az Azure AD
Ez a táblázat ismerteti a portok és protokollok, az Azure AD Connect-kiszolgáló és az Azure AD közötti kommunikációhoz szükséges.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Töltse le a visszavonási listák (tanúsítvány-visszavonási listákat) SSL-tanúsítványok ellenőrzésére használt. |
| HTTPS |443(TCP/UDP) |Az Azure ad-val szinkronizálásához használni. |

Listáját URL-címek és IP-címek, meg kell nyitnia a tűzfalon, lásd: [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>3. táblázat – Azure AD Connect és az AD FS összevonási kiszolgálók/WAP
Ez a táblázat ismerteti a portok és protokollok, az Azure AD Connect-kiszolgáló és az AD FS összevonási/WAP-kiszolgálókkal közötti kommunikációhoz szükséges.  

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Töltse le a visszavonási listák (tanúsítvány-visszavonási listákat) SSL-tanúsítványok ellenőrzésére használt. |
| HTTPS |443(TCP/UDP) |Az Azure ad-val szinkronizálásához használni. |
| WinRM |5985 |A WinRM figyelő |

## <a name="table-4---wap-and-federation-servers"></a>4. táblázat – WAP és az összevonási kiszolgálók
Ez a táblázat ismerteti a portok és protokollok, az összevonási kiszolgálók és a WAP-kiszolgálókkal közötti kommunikációhoz szükséges.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |A hitelesítéshez használt. |

## <a name="table-5---wap-and-users"></a>5. táblázat – WAP és felhasználók
Ez a táblázat ismerteti a portok és protokollok, a felhasználók és a WAP-kiszolgálókkal közötti kommunikációhoz szükséges.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Eszköz-hitelesítéshez használt. |
| TCP |49443 (TCP) |Tanúsítvány-hitelesítéshez használt. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tábla 6a & 6b - áteresztő hitelesítés az egyszeri bejelentkezés (SSO) és a Jelszókivonat-szinkronizálás az egyszeri bejelentkezés (SSO)
Az alábbi táblázatok a portok és protokollok, amelyek szükségesek az Azure AD Connect és az Azure AD közötti kommunikáció ismerteti.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tábla 6a - áteresztő hitelesítéses egyszeri Bejelentkezéssel
|Protokoll|Portszám|Leírás
| --- | --- | ---
|HTTP|80|Engedélyezze a kimenő HTTP-forgalom biztonsági ellenőrzés céljából, például az SSL Használatát. Az összekötő automatikus frissítési funkció megfelelő működéséhez is szükség.
|HTTPS|443| Kimenő HTTPS-forgalom engedélyezése és letiltása a funkció, összekötők regisztrálása, összekötő frissítések letöltése és kezelési minden felhasználói bejelentkezési kérelem hasonló műveletek engedélyezése.

Ezenkívül az Azure AD Connect képesnek kell lennie a közvetlen IP-kapcsolatot létesíteni a [Azure adatközpont IP-címtartományok](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tábla 6b - alapú egyszeri Jelszókivonat-szinkronizálás

|Protokoll|Portszám|Leírás
| --- | --- | ---
|HTTPS|443| Engedélyezze az egyszeri bejelentkezés regisztrációt (csak az SSO-regisztrációs folyamathoz szükséges).

Ezenkívül az Azure AD Connect képesnek kell lennie a közvetlen IP-kapcsolatot létesíteni a [Azure adatközpont IP-címtartományok](https://www.microsoft.com/en-us/download/details.aspx?id=41653). Ebben az esetben ez a tulajdonság csak az SSO-regisztrációs folyamathoz szükséges.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tábla 7a & 7b - a (AD FS/szinkronizálási) Azure AD Connect Health ügynök és az Azure AD
Az alábbi táblázatban láthatók a végpontok, portok és protokollok, amelyek szükségesek az Azure AD Connect Health-ügynökök és az Azure AD közti kommunikációhoz.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tábla 7a - portok és protokollok a Azure AD Connect Health agent számára (AD FS/szinkronizálása) és az Azure AD
Ez a táblázat ismerteti a következő kimenő portok és protokollok, az Azure AD Connect Health-ügynökök és az Azure AD közötti kommunikációhoz szükséges.  

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Kimenő |
| Azure Service Bus |5671 (TCP/UDP) |Kimenő |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - végpontok Azure AD Connect Health Agent számára (AD FS/szinkronizálása) és az Azure AD
A végpontok listájáért lásd: [az követelményeket ismertető részben az Azure AD Connect Health-ügynök](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).

