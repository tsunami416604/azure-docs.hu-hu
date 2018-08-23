---
title: Hibrid identitás – szükséges portok és protokollok – Azure |} A Microsoft Docs
description: Ez a lap nyitva az Azure AD Connect szükséges portokon műszaki útmutató oldal
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
ms.openlocfilehash: 55100d14ab7acbff87a726615ad9fa8b1fe9a3c8
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054707"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Hibrid identitás – szükséges portok és protokollok
A következő dokumentum a technikai útmutató a szükséges portok és protokollok a hibrid identitáskezelési megoldás megvalósítása. Használja a következő ábra és a kapcsolódó táblázat.

![Mi az az Azure AD Connect?](./media/active-directory-aadconnect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>1. táblázat – Azure AD Connect és a helyszíni AD
Ez a táblázat ismerteti a portok és protokollok, amelyek szükségesek az Azure AD Connect-kiszolgáló közötti kommunikációhoz és a helyszíni AD.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| DNS |53-AS (TCP/UDP) |DNS-keresések a cél-erdőben. |
| Kerberos |88 (TCP/UDP) |Kerberos-hitelesítés az AD-erdőhöz. |
| MS-RPC |A 135-ÖS (TCP/UDP) |Ha az AD-erdőhöz van kötve az Azure AD Connect varázsló a kezdeti konfiguráció során és a jelszó-szinkronizálás során használt. |
| LDAP |389-ES (TCP/UDP) |Adatok importálása az AD-ből használja. A Kerberos-bejelentkezési & lezárása titkosítja az adatokat. |
| RPC | 445-ÖS (TCP/UDP) |Közvetlen egyszeri bejelentkezés az AD-erdő egy számítógép-fiók létrehozásához használt. |
| LDAP/SSL |A 636-OS (TCP/UDP) |Adatok importálása az AD-ből használja. Az adatátvitel aláírt és titkosított. Csak akkor használja az SSL használata. |
| RPC |49152 – 65535 (véletlenszerű magas RPC Port)(TCP/UDP) |Az Azure AD Connect, ha az AD-erdőhöz van kötve a kezdeti konfiguráció során, és a jelszó-szinkronizálás során használt. Lásd: [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), és [KB224196](https://support.microsoft.com/kb/224196) további információt. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>2. táblázat – Azure AD Connect és az Azure AD
Ez a táblázat bemutatja a portok és protokollok, az Azure AD Connect-kiszolgáló és az Azure AD közötti kommunikációhoz szükséges.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTP |80 (A TCP/UDP) |Töltse le a CRL-ek (tanúsítvány-visszavonási listákat) ellenőrzése az SSL-tanúsítványok segítségével. |
| HTTPS |443(TCP/UDP) |Használja az Azure AD szinkronizálása. |

Az URL-címek és IP címeket meg kell nyitnia a tűzfalon, lásd: [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>3. táblázat – Azure AD Connect és az AD FS összevonási kiszolgálók/WAP
Ez a táblázat bemutatja a portok és protokollok, az Azure AD Connect-kiszolgáló és AD FS összevonási és WAP-kiszolgálók közötti kommunikációhoz szükséges.  

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTP |80 (A TCP/UDP) |Töltse le a CRL-ek (tanúsítvány-visszavonási listákat) ellenőrzése az SSL-tanúsítványok segítségével. |
| HTTPS |443(TCP/UDP) |Használja az Azure AD szinkronizálása. |
| WinRM |5985 |WinRM-figyelőhöz |

## <a name="table-4---wap-and-federation-servers"></a>4. táblázat – WAP és az összevonási kiszolgálók
Ez a táblázat bemutatja a portok és protokollok, amelyek az összevonási kiszolgálók és a WAP-kiszolgálók közötti kommunikációhoz szükséges.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Hitelesítéshez használt. |

## <a name="table-5---wap-and-users"></a>5. táblázat – WAP és a felhasználók
Ez a táblázat bemutatja a portok és protokollok, amelyek a felhasználók és a WAP-kiszolgálók közötti kommunikációhoz szükséges.

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Eszköz-hitelesítéshez használt. |
| TCP |49443 (TCP) |Használja a tanúsítvány hitelesítéséhez. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tábla 6a & 6b - átmenő hitelesítés az egyszeri bejelentkezés (SSO) és a Jelszókivonat-szinkronizálás az egyszeri bejelentkezés (SSO)
Az alábbi táblázat ismerteti a portok és protokollok, az Azure AD Connect és az Azure AD közötti kommunikációhoz szükséges.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tábla 6a - átmenő hitelesítés SSO-val
|Protokoll|Portszám|Leírás
| --- | --- | ---
|HTTP|80|Engedélyezze a kimenő HTTP-forgalom biztonsági ellenőrzés céljából, például az SSL Használatát. Az összekötő az automatikus frissítés funkció megfelelő működéséhez emellett szükséges.
|HTTPS|443| Engedélyezze a kimenő HTTPS-forgalmat a műveletek, például a engedélyezése és letiltása a funkció, összekötők regisztrálásához, összekötő frissítések letöltése és az összes felhasználói bejelentkezési kérelmek kezelését.

Emellett az Azure AD Connect képesnek kell lennie, hogy a közvetlen IP-kapcsolatot a [Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>6b - Jelszókivonatok szinkronizálása az egyszeri bejelentkezési táblára

|Protokoll|Portszám|Leírás
| --- | --- | ---
|HTTPS|443| Engedélyezze az egyszeri bejelentkezés regisztrációs (csak az SSO-regisztrációs folyamathoz szükséges).

Emellett az Azure AD Connect képesnek kell lennie, hogy a közvetlen IP-kapcsolatot a [Azure adatközpont IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Újra, ez a tulajdonság csak az SSO-regisztrációs folyamathoz szükséges.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tábla 7a & 7b – az Azure AD Connect Health agent for (AD FS/szinkronizálása) és az Azure ad-ben
Az alábbi táblázatok ismertetik a végpontokat, portok és protokollok, amelyek szükségesek az Azure AD Connect Health-ügynökök és az Azure AD közötti kommunikáció

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tábla 7a - portok és protokollok a az Azure AD Connect Health agent for (AD FS/szinkronizálása) és az Azure AD
Ez a táblázat bemutatja a következő kimenő portokat és protokollokat, az Azure AD Connect Health-ügynökök és az Azure AD közötti kommunikációhoz szükséges.  

| Protokoll | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Kimenő |
| Azure Service Bus |5671-ES (TCP/UDP) |Kimenő |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - végpontjai az Azure AD Connect Health agent for (AD FS/szinkronizálása) és az Azure AD
Végpontok listáját lásd: [a követelmények szakaszt az Azure AD Connect Health-ügynök](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).

