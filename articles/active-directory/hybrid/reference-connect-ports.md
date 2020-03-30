---
title: A hibrid identitás szükséges portok és protokollok - Azure | Microsoft dokumentumok
description: Ez az oldal az Azure AD Connect hez megnyitandó portok technikai referencialapja
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da318840426d1c0b94eab06b89ff3152df9d26fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331094"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Hibrid identitás – szükséges portok és protokollok
A következő dokumentum egy technikai hivatkozás a hibrid identitáskezelési megoldás megvalósításához szükséges portokra és protokollokra. Használja az alábbi ábrát, és olvassa el a megfelelő táblázatot.

![Mi az az Azure AD Connect?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>1. táblázat – Az Azure AD Connect és a helyszíni AD
Ez a táblázat az Azure AD Connect-kiszolgáló és a helyszíni AD közötti kommunikációhoz szükséges portokat és protokollokat ismerteti.

| Protocol (Protokoll) | Portok | Leírás |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-keresések a célerdőben. |
| Kerberos |88 (TCP/UDP) |Kerberos-hitelesítés az AD-erdőben. |
| MS-RPC |135 (TCP) |Az Azure AD Connect varázsló kezdeti konfigurációja során használatos, amikor az AD-erdőhöz, valamint a jelszó szinkronizálása során is használatos. |
| LDAP |389 (TCP/UDP) |Az AD-ből történő adatimportáláshoz használható. Az adatok at Kerberos Sign & Seal titkosítással titkosítják. |
| SMB | 445 (TCP) |A Seamless SSO által használt számítógépfiók létrehozásához az AD erdőben. |
| LDAP/SSL |636 (TCP/UDP) |Az AD-ből történő adatimportáláshoz használható. Az adatátvitel alá van írva és titkosítva van. Csak akkor használja, ha TLS-t használ. |
| RPC |49152- 65535 (véletlenszerűen magas RPC-port)(TCP) |Az Azure AD Connect kezdeti konfigurációja során, amikor az AD-erdőkhöz kötődik, és a jelszó-szinkronizálás során. További információ: [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)és [KB224196.](https://support.microsoft.com/kb/224196) |
|WinRM  | 5985 (TCP) |Csak akkor használatos, ha az AD FS-t gMSA-val telepíti az Azure AD Connect varázslóval|
|AD DS webszolgáltatások | 9389 (TCP) |Csak akkor használatos, ha az AD FS-t gMSA-val telepíti az Azure AD Connect varázslóval |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>2. táblázat – Az Azure AD Connect és az Azure AD
Ez a táblázat az Azure AD Connect-kiszolgáló és az Azure AD közötti kommunikációhoz szükséges portokat és protokollokat ismerteti.

| Protocol (Protokoll) | Portok | Leírás |
| --- | --- | --- |
| HTTP |80 (TCP) |Visszavont tanúsítványok listájának (visszavont tanúsítványok listájának) letöltésére szolgál a TLS/SSL-tanúsítványok ellenőrzésére. |
| HTTPS |443(TCP) |Az Azure AD-vel való szinkronizálásra szolgál. |

A tűzfalon megnyitandó URL-címek és IP-címek listáját az [Office 365 URL-címei és IP-címtartományai,](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) valamint [az Azure AD Connect-kapcsolat hibaelhárítása](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard)című témakörben téshet.

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>3. táblázat – Az Azure AD Connect és az AD FS összevonási kiszolgálók/WAP
Ez a táblázat az Azure AD Connect-kiszolgáló és az AD FS összevonás/WAP-kiszolgálók közötti kommunikációhoz szükséges portokat és protokollokat ismerteti.  

| Protocol (Protokoll) | Portok | Leírás |
| --- | --- | --- |
| HTTP |80 (TCP) |Visszavont tanúsítványok listájának (visszavont tanúsítványok listájának) letöltésére szolgál a TLS/SSL-tanúsítványok ellenőrzésére. |
| HTTPS |443(TCP) |Az Azure AD-vel való szinkronizálásra szolgál. |
| WinRM |5985 |WinRM figyelő |

## <a name="table-4---wap-and-federation-servers"></a>4. táblázat – WAP- és összevonási kiszolgálók
Ez a táblázat az összevonási és a WAP-kiszolgálók közötti kommunikációhoz szükséges portokat és protokollokat ismerteti.

| Protocol (Protokoll) | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP) |Hitelesítésre szolgál. |

## <a name="table-5---wap-and-users"></a>5. táblázat - WAP és felhasználók
Ez a táblázat a felhasználók és a WAP-kiszolgálók közötti kommunikációhoz szükséges portokat és protokollokat ismerteti.

| Protocol (Protokoll) | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP) |Eszközhitelesítéshez használatos. |
| TCP |49443 (TCP) |Tanúsítványhitelesítéshez szolgál. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>6a. táblázat & 6b - Átmenő hitelesítés egyszeri bejelentkezéssel (SSO) és jelszókivonat-szinkronizálás egyszeri bejelentkezéssel (SSO)
Az alábbi táblázatok ismertetik a portok és protokollok, amelyek szükségesek az Azure AD Connect és az Azure AD közötti kommunikációhoz.

### <a name="table-6a---pass-through-authentication-with-sso"></a>6a. táblázat - Átmenő hitelesítés egyszeri szolgáltatóval
|Protocol (Protokoll)|Portszám|Leírás
| --- | --- | ---
|HTTP|80|Engedélyezze a kimenő HTTP-forgalmat a biztonsági ellenőrzéshez, például az SSL-hez. Az összekötő automatikus frissítési képességéhez is szükség van a megfelelő működéshez.
|HTTPS|443| Engedélyezze a kimenő HTTPS-forgalmat olyan műveletekhez, mint a szolgáltatás engedélyezése és letiltása, az összekötők regisztrálása, az összekötők frissítéseinek letöltése és az összes felhasználói bejelentkezési kérelem kezelése.

Emellett az Azure AD Connectnek képesnek kell lennie közvetlen IP-kapcsolatok létesítésére az [Azure adatközpont IP-tartományaihoz.](https://www.microsoft.com/download/details.aspx?id=41653)

### <a name="table-6b---password-hash-sync-with-sso"></a>6b. táblázat - Jelszókivonat-szinkronizálás az Egyszeri bejelentkezéssel

|Protocol (Protokoll)|Portszám|Leírás
| --- | --- | ---
|HTTPS|443| Engedélyezze az SSO-regisztrációt (csak az SSO regisztrációs folyamathoz szükséges).

Emellett az Azure AD Connectnek képesnek kell lennie közvetlen IP-kapcsolatok létesítésére az [Azure adatközpont IP-tartományaihoz.](https://www.microsoft.com/download/details.aspx?id=41653) Ez ismét csak az SSO regisztrációs folyamathoz szükséges.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7a & 7b táblázat – Azure AD Connect health ügynök (AD FS/Sync) és Azure AD
Az alábbi táblázatok az Azure AD Connect Health ügynökök és az Azure AD közötti kommunikációhoz szükséges végpontokat, portokat és protokollokat ismertetik.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7a. táblázat – Portok és protokollok az Azure AD Connect health ügynökhöz (AD FS/Sync) és az Azure AD-hez
Ez a táblázat a következő kimenő portokat és protokollokat ismerteti, amelyek az Azure AD Connect Health ügynökök és az Azure AD közötti kommunikációhoz szükségesek.  

| Protocol (Protokoll) | Portok | Leírás |
| --- | --- | --- |
| HTTPS |443(TCP) |Kimenő |
| Azure Service Bus |5671 (TCP) |Kimenő |

Az Azure Service Bus 5671-es portja már nem szükséges az ügynök legújabb verziójához. Az Azure AD Connect Health ügynök legújabb verziója csak a 443-as portot igényelte.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - Végpontok az Azure AD Connect health ügynök (AD FS/Sync) és az Azure AD
A végpontok listáját [az Azure AD Connect health ügynök követelmények szakaszában](how-to-connect-health-agent-install.md#requirements)található.

