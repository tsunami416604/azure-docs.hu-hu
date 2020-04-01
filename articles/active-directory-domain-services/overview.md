---
title: Az Azure Active Directory tartományi szolgáltatások áttekintése | Microsoft dokumentumok
description: Ebből az áttekintésből megtudhatja, hogy mit nyújt az Azure Active Directory tartományi szolgáltatások, és hogyan használhatja azt a szervezetben identitásszolgáltatások nyújtásához a felhőben lévő alkalmazások és szolgáltatások számára.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: cf961b00c4823d0c9efcf44b0db7bb75a80d3dfa
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474334"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Mi az Azure Active Directory tartományi szolgáltatások?

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint a tartományhoz való csatlakozás, a csoportházirend, a könnyű címtár-hozzáférési protokoll (LDAP) és a Kerberos / NTLM-hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryval. Ezeket a tartományi szolgáltatásokat anélkül használhatja, hogy tartományvezérlőket kellene telepítenie, kezelnie és javítania a felhőben. Az Azure AD DS integrálható a meglévő Azure AD-bérlővel, amely lehetővé teszi a felhasználók számára, hogy meglévő hitelesítő adataikkal jelentkezzenek be. A meglévő csoportok és felhasználói fiókok használatával is biztosíthatja az erőforrásokhoz való hozzáférést, ami zökkenőmentesebb lift-and-shift helyszíni erőforrások at Azure-ba.

Az Azure AD DS replikálja az Azure AD-ből származó identitásadatokat, így együttműködik a csak felhőalapú vagy helyszíni Active Directory tartományi szolgáltatásokkal (AD DS) környezettel szinkronizált Azure AD-bérlőkkel. Az Azure AD DS-szolgáltatások mindkét környezetben léteznek.

* Ha már rendelkezik helyszíni Active Angeles-i Active Angeles-i szerepkör-fiókkörnyezettel, szinkronizálhatja a felhasználói fiók adatait, hogy a felhasználók konzisztens identitást biztosítsanak.
* Csak felhőalapú környezetekben nincs szükség hagyományos helyszíni AD DS-környezetre az Azure AD DS központi identitásszolgáltatási szolgáltatásainak használatához.

Az alábbi videó áttekintést nyújt arról, hogy az Azure AD DS hogyan integrálódik az alkalmazásokkal és a számítási feladatokkal, hogy identitásszolgáltatásokat nyújtson a felhőben:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Az identitáskezelési megoldások felhőbeli biztosításának gyakori módjai

Amikor a meglévő számítási feladatokat áttelepíti a felhőbe, a címtárbarát alkalmazások ldap-t használhatnak a helyszíni Active Directory tartományi szolgáltatások címtárának olvasási vagy írási hozzáféréséhez. A Windows Server rendszeren futó alkalmazások általában tartományhoz (virtuális gépeken) vannak telepítve, így a csoportházirend del biztonságosan kezelhetők. A végfelhasználók hitelesítéséhez az alkalmazások windowsos integrált hitelesítésre is támaszkodhatnak, például Kerberos vagy NTLM-hitelesítésre.

A rendszergazdák gyakran az alábbi megoldások egyikét használják az Azure-ban futó alkalmazások identitásszolgáltatásának biztosításához:

* Konfiguráljon egy helyek közötti VPN-kapcsolatot az Azure-ban futó munkaterhelések és a helyszíni ActiveAngeles-szolgáltatások környezete között.
    * A helyszíni tartományvezérlők ezután a VPN-kapcsolaton keresztül biztosítják a hitelesítést.
* Hozzon létre replika tartományvezérlők segítségével Azure virtuális gépek (VM-ek) az AD DS-tartomány /erdő helyszíni kibővítéséhez.
    * Az Azure virtuális gépeken futó tartományvezérlők hitelesítést biztosítanak, és replikálják a címtáradatokat a helyszíni Active Directory tartományi szolgáltatások környezete között.
* Üzembe helyezhet egy önálló AD DS-környezetet az Azure-ban az Azure virtuális gépeken futó tartományvezérlők használatával.
    * Az Azure virtuális gépeken futó tartományvezérlők hitelesítést biztosítanak, de nincsenek a helyszíni ActiveDirectory tartományi szolgáltatások környezetéből replikált címtáradatok.

Ezekkel a megközelítésekkel a helyszíni címtárhoz való VPN-kapcsolatok sebezhetővé teszik az alkalmazásokat az átmeneti hálózati hibák vagy kimaradások miatt. Ha virtuális gépekhasználatával telepíti a tartományvezérlőket az Azure-ban, az informatikai csapatnak kezelnie kell a virtuális gépeket, majd biztonságosnak kell lennie, javítania kell, rögzítenie, figyelnie, biztonsági mentésével és hibaelhárításával kell kezelnie őket.

Az Azure AD DS alternatívát kínál a VPN-kapcsolatok helyszíni AD DS-környezethez való visszakapcsolásának szükségességére, vagy virtuális gépek futtatására és kezelésére az Azure-ban az identitásszolgáltatások biztosításához. Felügyelt szolgáltatásként az Azure AD DS csökkenti a komplexitást, hogy integrált identitásmegoldást hozzon létre mind a hibrid, mind a csak felhőalapú környezetekben.

## <a name="azure-ad-ds-features-and-benefits"></a>Az Azure AD DS funkciói és előnyei

Az Azure AD DS teljes mértékben kompatibilis a hagyományos AD DS-környezettel olyan műveletekhez, mint a tartomány-csatlakozás, a biztonságos LDAP (LDAPS), a csoportházirend, a DNS-kezelés és az LDAP-kötési és olvasási támogatás. LDAP írási támogatás érhető el az Azure AD DS felügyelt tartományban létrehozott objektumok, de az Azure AD-ből szinkronizált erőforrásokhoz nem érhető el. Az Azure AD DS következő szolgáltatásai egyszerűsítik az üzembe helyezési és felügyeleti műveleteket:

* **Egyszerűsített telepítési élmény:** Az Azure AD DS engedélyezve van az Azure AD-bérlő egyetlen varázsló használatával az Azure Portalon.
* **Integrálva az Azure AD-vel:** A felhasználói fiókok, a csoporttagságok és a hitelesítő adatok automatikusan elérhetők az Azure AD-bérlőből. Az Azure AD-bérlővagy a helyszíni AD DS-környezet attribútumainak új felhasználói, csoportjai vagy attribútumainak módosításai automatikusan szinkronizálódnak az Azure AD DS szolgáltatással.
    * Az Azure AD-hez kapcsolódó külső könyvtárak fiókjai nem érhetők el az Azure AD DS-ben. A hitelesítő adatok nem érhetők el a külső könyvtárak, így nem szinkronizálható egy Azure AD DS felügyelt tartományban.
* **Vállalati hitelesítő adatok/jelszavak használata:** Az Azure AD DS-ben a felhasználók számára megadott jelszavak megegyeznek az Azure AD-bérlővel. A felhasználók használhatják a vállalati hitelesítő adatokat a tartományhoz való csatlakozás gépek, jelentkezzen be interaktívan vagy távoli asztalon, és hitelesíti az Azure AD DS felügyelt tartományban.
* **NTLM és Kerberos hitelesítés:** Az NTLM és a Kerberos hitelesítés támogatásával olyan alkalmazásokat telepíthet, amelyek windowsos alapú hitelesítésre támaszkodnak.
* **Magas rendelkezésre állás:** Az Azure AD DS több tartományvezérlőt tartalmaz, amelyek magas rendelkezésre állást biztosítanak a felügyelt tartomány számára. Ez a magas rendelkezésre állás garantálja a szolgáltatás üzemidejét és a hibákkal szembeni rugalmasságot.
    * Az Azure [rendelkezésre állási zónákat][availability-zones]támogató régiókban ezek a tartományvezérlők is zónák között vannak elosztva a további rugalmasság érdekében.

Az Azure AD DS által felügyelt tartomány néhány fő szempontja a következő:

* Az Azure AD DS felügyelt tartomány egy önálló tartomány. Ez nem egy helyszíni tartomány kiterjesztése.
    * Szükség esetén létrehozhat egyirányú kimenő erdőszintű bizalmi kapcsolatokat az Azure AD DS-ből egy helyszíni AD DS-környezetbe. További információ: [Erőforrás erdő fogalmak és szolgáltatások az Azure AD DS.][ forest-trusts]
* Az informatikai csapatnak nem kell kezelnie, javítania vagy figyelnie a tartományvezérlőket ehhez az Azure AD DS felügyelt tartományhoz.

A helyszíni Activeád-szolgáltatások futtatásához futó hibrid környezetekben nem kell kezelnie az AD-replikációt az Azure AD DS felügyelt tartományába. A helyszíni címtár felhasználói fiókjai, csoporttagságai és hitelesítő adatai szinkronizálódnak az Azure AD-vel az [Azure AD Connect en][azure-ad-connect]keresztül. Ezek a felhasználói fiókok, csoporttagságok és hitelesítő adatok automatikusan elérhetők az Azure AD DS felügyelt tartományban.

## <a name="how-does-azure-ad-ds-work"></a>Hogyan működik az Azure AD DS?

Az identitáskezelési szolgáltatások biztosításához az Azure létrehoz egy AD DS-példányt egy ön által választott virtuális hálózaton. A színfalak mögött egy pár Windows Server-tartományvezérlők jönnek létre, amelyek az Azure virtuális gépeken futnak. Nem kell kezelnie, konfigurálnia vagy frissítenie ezeket a tartományvezérlőket. Az Azure platform kezeli a tartományvezérlők az Azure AD DS szolgáltatás részeként.

Az Azure AD DS felügyelt tartomány úgy van konfigurálva, hogy az Azure AD egyirányú szinkronizálása a felhasználók, csoportok és hitelesítő adatok központi készletéhez való hozzáférést biztosítson. Az erőforrásokat közvetlenül az Azure AD DS felügyelt tartományban hozhat létre, de azok nincsenek szinkronizálva az Azure AD-vel. Az Azure-ban a virtuális hálózathoz csatlakozó alkalmazások, szolgáltatások és virtuális gépek ezután használhatják az AD DS közös szolgáltatásait, például a tartományhoz való csatlakozást, a csoportházirendet, az LDAP-t és a Kerberos / NTLM-hitelesítést.

A helyszíni ActiveÁd-ds-környezettel rendelkező hibrid környezetben az [Azure AD Connect][azure-ad-connect] szinkronizálja az identitásadatokat az Azure AD-vel, amelyet ezután szinkronizál az Azure AD DS-szel.

![Szinkronizálás az Azure AD tartományi szolgáltatásokban az Azure AD-vel és a helyszíni Active Directory tartományi szolgáltatásokkal az AD Connect használatával](./media/active-directory-domain-services-design-guide/sync-topology.png)

Az Azure AD DS működés közbeni megtekintéséhez tekintsen meg néhány példát:

* [Azure AD DS hibrid szervezetekszámára](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS csak felhőalapú szervezetekszámára](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS hibrid szervezetekszámára

Számos szervezet olyan hibrid infrastruktúrát futtat, amely felhőbeli és helyszíni alkalmazásbeli számítási feladatokat is tartalmaz. A lift- és shift-stratégia részeként az Azure-ba áttelepített örökölt alkalmazások hagyományos LDAP-kapcsolatokat használhatnak az identitásadatok megadásához. A hibrid infrastruktúra támogatása érdekében a helyszíni Activeád-ds-környezetből származó identitásadatok szinkronizálhatók egy Azure AD-bérlővel. Az Azure AD DS ezután biztosítja ezeket az örökölt alkalmazásokat az Azure-ban egy identitásforrással, anélkül, hogy konfigurálnia és kezelnie kellene az alkalmazáskapcsolatot a helyszíni címtárszolgáltatásokhoz.

Nézzünk meg egy példát a Litware Corporation, egy hibrid szervezet, amely fut mind a helyszíni és az Azure-erőforrások:

![Azure Active Directory tartományi szolgáltatások egy olyan hibrid szervezet számára, amely helyszíni szinkronizálást is tartalmaz](./media/overview/synced-tenant.png)

* A tartományi szolgáltatásokat igénylő alkalmazások és kiszolgálói munkaterhelések az Azure-beli virtuális hálózatban vannak telepítve.
    * Ez magában foglalhatja az Azure-ba egy növekedési és váltási stratégia részeként áttelepített örökölt alkalmazásokat.
* A Litware Corporation telepíti az [Azure AD Connect][azure-ad-connect]et a helyszíni címtárból származó identitásadatok szinkronizálásához.
    * A szinkronizált identitásadatok közé tartoznak a felhasználói fiókok és a csoporttagságok.
* A Litware informatikai csapata engedélyezi az Azure AD DS-t az Azure AD-bérlőszámára ebben, vagy egy társviszony-létesített virtuális hálózatban.
* Az Azure virtuális hálózatban üzembe helyezett alkalmazások és virtuális gépek ezután használhatják az Azure AD DS-funkciókat, például a tartományhoz való csatlakozást, az LDAP-olvasást, az LDAP-kötést, az NTLM- és Kerberos-hitelesítést, valamint a csoportházirendet.

> [!IMPORTANT]
> Az Azure AD Connect csak telepíteni és konfigurálni a helyszíni AD DS-környezetekkel való szinkronizáláshoz. Nem támogatott az Azure AD Connect telepítése egy Azure AD DS által felügyelt tartományban az objektumok Azure AD-vel való szinkronizálásához.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS csak felhőalapú szervezetekszámára

Csak felhőalapú Azure AD-bérlő nem rendelkezik helyszíni identitásforrással. A felhasználói fiókok és a csoporttagságok például közvetlenül az Azure AD-ben jönnek létre és kezelhetők.

Most nézzünk meg egy példát a Contoso, egy csak felhőalapú szervezet, amely az Azure AD identitását használja. Minden felhasználói identitások, a hitelesítő adatok és a csoporttagságok jönnek létre és kezelik az Azure AD.All user identity, their credentials, and group memberships are created and managed in Azure AD. Nincs további konfiguráció az Azure AD Connect szinkronizálásához identitásadatok at a helyszíni címtárból.

![Az Azure Active Directory tartományi szolgáltatások csak felhőalapú, helyszíni szinkronizálás nélküli szervezetek számára](./media/overview/cloud-only-tenant.png)

* A tartományi szolgáltatásokat igénylő alkalmazások és kiszolgálói munkaterhelések az Azure-beli virtuális hálózatban vannak telepítve.
* A Contoso informatikai csapata engedélyezi az Azure AD DS-t az Azure AD-bérlőszámára ebben, vagy egy társviszony-létesített virtuális hálózatban.
* Az Azure virtuális hálózatban üzembe helyezett alkalmazások és virtuális gépek ezután használhatják az Azure AD DS-funkciókat, például a tartományhoz való csatlakozást, az LDAP-olvasást, az LDAP-kötést, az NTLM- és Kerberos-hitelesítést, valamint a csoportházirendet.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogy az Azure AD DS összehasonlítható-e más identitásmegoldásokkal és a szinkronizálás működésével, tekintse meg az alábbi cikkeket:

* [Az Azure AD DS és az Azure AD, az Azure virtuális gépeken futó Active Directory tartományi szolgáltatások és a helyszíni Active Directory tartományi szolgáltatások összehasonlítása][compare]
* [Ismerje meg, hogyan szinkronizálja az Azure AD tartományi szolgáltatásokat az Azure AD-címtárral][synchronization]

Első lépésekhez [hozzon létre egy Azure AD DS felügyelt tartományt az Azure Portalon][tutorial-create]keresztül.

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
