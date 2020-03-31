---
title: Tartományhoz való csatlakozás – problémamegoldás az Azure AD tartományi szolgáltatásokkal | Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el a gyakori problémákat, amikor megpróbál csatlakozni egy virtuális géphez, vagy egy alkalmazást az Azure Active Directory tartományi szolgáltatásokhoz csatlakoztatni, és nem tud csatlakozni vagy hitelesíteni a felügyelt tartományt.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299108"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Tartománycsatlakozási problémák elhárítása az Azure AD tartományi szolgáltatások által kezelt tartománnyal

Amikor megpróbál csatlakozni egy virtuális géphez (VM), vagy egy alkalmazást egy Azure Active Directory tartományi szolgáltatások (AD DS) felügyelt tartományhoz csatlakoztat, előfordulhat, hogy hibaüzenetet kap, amelyet nem tud megtenni. A tartományhoz való csatlakozásokkal kapcsolatos problémák elhárításához tekintse át, hogy az alábbi pontok közül melyiknél jelentkezik probléma:

* Ha nem kap hitelesítési kérdést, a virtuális gép vagy az alkalmazás nem tud csatlakozni az Azure AD DS felügyelt tartományhoz.
    * Kezdje el elhárítani [a tartományhoz való csatlakozással kapcsolatos kapcsolódási problémákat.](#connectivity-issues-for-domain-join)
* Ha hibaüzenetet kap a hitelesítés során, az Azure AD DS felügyelt tartományával való kapcsolat sikeres.
    * Kezdje el elhárítani a [hitelesítő adatokkal kapcsolatos problémákat a tartományhoz való csatlakozás során.](#credentials-related-issues-during-domain-join)

## <a name="connectivity-issues-for-domain-join"></a>Kapcsolódási problémák a tartományhoz való csatlakozáshoz

Ha a virtuális gép nem találja az Azure AD DS felügyelt tartományt, általában hálózati kapcsolat vagy konfigurációs probléma merül fel. A probléma megkereséséhez és megoldásához tekintse át az alábbi hibaelhárítási lépéseket:

1. Győződjön meg arról, hogy a virtuális gép ugyanahhoz a virtuális hálózathoz vagy egy társviszony-létesített virtuális hálózathoz csatlakozik, amely engedélyezve van az Azure AD DS számára. Ha nem, a virtuális gép nem találja, és csatlakozhat a tartományhoz, hogy csatlakozzon.
    * Ha a virtuális gép nem csatlakozik ugyanahhoz a virtuális hálózathoz, ellenőrizze, hogy a virtuális hálózati társviszony-létesítés vagy a VPN-kapcsolat *aktív* vagy *csatlakoztatott,* hogy a forgalom megfelelően folyjon.
1. Próbálja meg pingelni a tartományt az Azure AD `ping aaddscontoso.com`DS felügyelt tartományának tartománynevével, például .
    * Ha a pingelési válasz sikertelen, próbálja meg pingolni az Azure AD DS felügyelt tartományának `ping 10.0.0.4`portálján megjelenő tartomány IP-címeit, például .
    * Ha sikeresen pingeli az IP-címet, de a tartományt nem, előfordulhat, hogy a DNS helytelenül van konfigurálva. Győződjön meg arról, hogy konfigurálta az Azure AD DS felügyelt tartományi DNS-kiszolgálók a virtuális hálózathoz.
1. Próbálja meg kiüríteni a DNS-feloldó `ipconfig /flushdns`gyorsítótárát a virtuális gépen, például .

### <a name="network-security-group-nsg-configuration"></a>Hálózati biztonsági csoport (NSG) konfigurációja

Amikor létrehoz egy Azure AD DS felügyelt tartományt, egy hálózati biztonsági csoport is létrejön a sikeres tartományi művelethez szükséges megfelelő szabályokkal. Ha további hálózati biztonsági csoportszabályokat szerkeszt vagy hoz létre, véletlenül leblokkolhatja az Azure AD DS-hez szükséges portokat a kapcsolati és hitelesítési szolgáltatások biztosításához. Ezek a hálózati biztonsági csoportszabályok olyan problémákat okozhatnak, mint például a jelszószinkronizálás nem teljesedése, a felhasználók nem tudnak bejelentkezni, vagy a tartományhoz való csatlakozással kapcsolatos problémák.

Ha továbbra is csatlakozási problémákmerülnek fel, tekintse át az alábbi hibaelhárítási lépéseket:

1. Ellenőrizze az Azure AD DS által felügyelt tartomány állapotát az Azure Portalon. Ha az *AADDS001-re*vonatkozó annektusza figyelmeztetést kap, a hálózati biztonsági csoport szabálya blokkolja a hozzáférést.
1. Tekintse át a [szükséges portokra és hálózati biztonsági csoportszabályokra vonatkozó szabályokat.][network-ports] Győződjön meg arról, hogy nincs hálózati biztonsági csoport szabályok a virtuális gép vagy a virtuális hálózat csatlakozik blokkolja ezeket a hálózati portokat.
1. A hálózati biztonsági csoport konfigurációs problémáinak megoldása után az *AADDS001* riasztás körülbelül 2 óra alatt eltűnik az állapotlapról. A hálózati kapcsolat már elérhető, próbálja meg a tartomány-csatlakozás a virtuális gép újra.

## <a name="credentials-related-issues-during-domain-join"></a>Hitelesítő adatokkal kapcsolatos problémák a tartományhoz való csatlakozás során

Ha kap egy párbeszédpanelt, amely hitelesítő adatokat kér az Azure AD DS felügyelt tartományhoz való csatlakozáshoz, a virtuális gép az Azure virtuális hálózaton keresztül csatlakozhat a tartományhoz. A tartomány-csatlakozási folyamat nem hitelesíti a tartományt, vagy engedélyezi a tartomány-csatlakozási folyamat befejezéséhez a hitelesítő adatok at.

A hitelesítő adatokkal kapcsolatos problémák elhárításához tekintse át az alábbi hibaelhárítási lépéseket:

1. Próbálja meg az upn formátumot használni `dee@aaddscontoso.onmicrosoft.com`a hitelesítő adatok megadásához, például . Győződjön meg arról, hogy ez az upn megfelelően van konfigurálva az Azure AD-ben.
    * A fiók *SAMAccountName* automatikusan létrehozható, ha több felhasználó rendelkezik ugyanazzal az UPN előtaggal a bérlőben, vagy ha az UPN-előtag túl hosszú. Ezért a fiók *SAMAccountName* formátuma eltérhet attól, amit a helyszíni tartományban elvár vagy használ.
1. Próbálja meg használni a hitelesítő adatokat egy felhasználói fiók, amely része az Azure AD DS felügyelt tartományban, hogy csatlakozzon a virtuális gépek a felügyelt tartományhoz.
1. Győződjön meg arról, hogy [engedélyezte a jelszó-szinkronizálást,][enable-password-sync] és elég sokáig várt a kezdeti jelszószinkronizálás befejezéséhez.

## <a name="next-steps"></a>További lépések

Az Active Directory-folyamatoknak a tartományhozvaló csatlakoztatási művelet részeként való mélyebb megértését lásd: [Csatlakozás és hitelesítési problémák][join-authentication-issues].

Ha továbbra is problémái vannak a virtuális gépnek az Azure AD DS felügyelt tartományhoz való csatlakozásával, [kérjen segítséget, és nyisson meg egy támogatási jegyet az Azure Active Directoryhoz.][azure-ad-support]

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
