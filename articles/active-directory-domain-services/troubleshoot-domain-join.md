---
title: A tartományhoz való csatlakozás hibáinak megoldása – Azure AD Domain Services | Microsoft Docs
description: Ismerje meg, hogy miként lehet elhárítani a gyakori problémákat, amikor egy virtuális géphez próbál csatlakozni, vagy egy alkalmazást csatlakoztat Azure Active Directory Domain Serviceshoz, és nem tud csatlakozni a felügyelt tartományhoz, illetve nem végezheti el a hitelesítést.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 73a76c4442bb8af70168e54a294f2cb100ff653c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703658"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services felügyelt tartományhoz való csatlakozással kapcsolatos problémák elhárítása

Amikor megpróbál csatlakozni egy virtuális géphez (VM), vagy egy alkalmazást egy Azure Active Directory Domain Services (AD DS) felügyelt tartományhoz csatlakoztat, hibaüzenetet kaphat, hogy ezt nem tudja elvégezni. A tartományhoz való csatlakozással kapcsolatos problémák elhárításához tekintse át a következő pontok problémáit:

* Ha nem kap hitelesítési kérést, a virtuális gép vagy az alkalmazás nem tud csatlakozni az Azure AD DS felügyelt tartományhoz.
    * A [tartományhoz való csatlakozással kapcsolatos csatlakozási problémák](#connectivity-issues-for-domain-join)elhárításának megkezdése.
* Ha a hitelesítés során hibaüzenetet kap, az Azure AD DS felügyelt tartományhoz való csatlakozás sikeres.
    * A [hitelesítő adatokkal kapcsolatos problémák](#credentials-related-issues-during-domain-join)elhárításának megkezdése a tartományhoz való csatlakozás során.

## <a name="connectivity-issues-for-domain-join"></a>A tartományhoz való csatlakozáshoz kapcsolódó csatlakozási problémák

Ha a virtuális gép nem találja az Azure AD DS felügyelt tartományt, általában hálózati kapcsolatok vagy konfigurációs problémák merülnek fel. A probléma megkereséséhez és megoldásához tekintse át a következő hibaelhárítási lépéseket:

1. Győződjön meg arról, hogy a virtuális gép ugyanahhoz a virtuális géphez van csatlakoztatva, vagy egy olyan, az Azure AD DS számára engedélyezett virtuális hálózathoz csatlakozik. Ha nem, a virtuális gép nem találja és nem tud csatlakozni a tartományhoz a csatlakozáshoz.
    * Ha a virtuális gép nem csatlakozik ugyanahhoz a virtuális hálózathoz, győződjön meg arról, hogy a virtuális hálózati társ-vagy VPN-kapcsolat *aktív* vagy *csatlakoztatva* van, hogy a forgalom megfelelően működjön.
1. Próbálja meg pingelni a tartományt az Azure AD DS felügyelt tartományának tartományneve (például `ping aadds.contoso.com`) használatával.
    * Ha a pingelési válasz sikertelen, próbálja meg pingelni a tartományhoz tartozó IP-címeket a portál áttekintés lapján az Azure AD DS felügyelt tartományhoz, például `ping 10.0.0.4`hoz.
    * Ha sikeresen Pingeli az IP-címet, de a tartományt nem, a DNS helytelenül van konfigurálva. Győződjön meg arról, hogy konfigurálta az Azure AD DS felügyelt tartomány DNS-kiszolgálóit a virtuális hálózathoz.
1. Próbálja meg kiüríteni a DNS-feloldó gyorsítótárát a virtuális gépen, például `ipconfig /flushdns`.

### <a name="network-security-group-nsg-configuration"></a>Hálózati biztonsági csoport (NSG) konfigurációja

Azure AD DS felügyelt tartomány létrehozásakor a rendszer a sikeres tartományi művelethez is létrehoz egy hálózati biztonsági csoportot a megfelelő szabályokkal. Ha további hálózati biztonsági csoportokra vonatkozó szabályokat szerkeszt vagy hoz létre, előfordulhat, hogy véletlenül letiltja az Azure AD DS számára szükséges portokat a kapcsolódási és hitelesítési szolgáltatások biztosításához. Ezek a hálózati biztonsági csoportokra vonatkozó szabályok olyan problémákat okozhatnak, mint például a jelszó-szinkronizálás nem fejeződik be, a felhasználók nem tudnak bejelentkezni, vagy a tartományhoz való csatlakozással kapcsolatos problémák.

Ha továbbra is fennáll a kapcsolódási problémák, tekintse át a következő hibaelhárítási lépéseket:

1. Az Azure AD DS felügyelt tartomány állapotának ellenőrzését a Azure Portal. Ha a *AADDS001*riasztása van, a hálózati biztonsági csoport szabálya blokkolja a hozzáférést.
1. Tekintse át a [szükséges portokat és a hálózati biztonsági csoportra vonatkozó szabályokat][network-ports]. Győződjön meg arról, hogy a virtuális GÉPRE vagy a hálózati portok blokkolásához használt virtuális hálózatra nem vonatkoznak hálózati biztonsági csoportra vonatkozó szabályok.
1. A hálózati biztonsági csoport konfigurációs problémáinak elhárítása után a *AADDS001* riasztás eltűnik az állapot lapról körülbelül 2 órával. A hálózati kapcsolat már elérhető, próbálja meg újra tartományhoz csatlakoztatni a virtuális gépet.

## <a name="credentials-related-issues-during-domain-join"></a>Hitelesítő adatokkal kapcsolatos problémák a tartományhoz való csatlakozás során

Ha olyan párbeszédpanelt kap, amely a hitelesítő adatok megadását kéri az Azure AD DS felügyelt tartományhoz való csatlakozáshoz, a virtuális gép az Azure Virtual Network használatával tud csatlakozni a tartományhoz. A tartományhoz való csatlakozás folyamata nem sikerül a tartományhoz való hitelesítéskor, vagy a tartományhoz való csatlakozás engedélyezéséhez a hitelesítő adatok használatával.

A hitelesítő adatokkal kapcsolatos problémák elhárításához tekintse át a következő hibaelhárítási lépéseket:

1. Próbálja meg az UPN formátumot használni a hitelesítő adatok megadásához, például `dee@contoso.onmicrosoft.com`. Győződjön meg arról, hogy az UPN helyesen van konfigurálva az Azure AD-ben.
    * Előfordulhat, hogy a fiókhoz tartozó *sAMAccountName* automatikusan létrejön, ha több felhasználó rendelkezik UGYANAZZAL az UPN-előtaggal a bérlőben, vagy ha az UPN-előtag túl hosszú. Ezért előfordulhat, hogy a fiók *sAMAccountName* -formátuma eltér a helyszíni tartományban várttól vagy használattól.
1. Próbáljon meg egy olyan felhasználói fiók hitelesítő adatait használni, amely a *HRE DC-rendszergazdák* csoportba tartozik, és csatlakoztassa a virtuális gépeket az Azure AD DS felügyelt tartományhoz.
1. Győződjön meg arról, hogy [engedélyezte a jelszó-szinkronizálást][enable-password-sync] , és elég sokáig várt a jelszó-szinkronizálás kezdeti befejezéséhez.

## <a name="next-steps"></a>Következő lépések

A tartományhoz való csatlakozás művelet részeként a Active Directory folyamatok mélyebb megismeréséhez tekintse meg a [csatlakoztatási és hitelesítési problémák][join-authentication-issues]című témakört.

Ha továbbra sem sikerül csatlakoztatni a virtuális gépet az Azure AD DS felügyelt tartományhoz, [keressen segítséget a Azure Active Directory támogatási jegyének megnyitásához][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
