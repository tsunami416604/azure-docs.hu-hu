---
title: A Azure Active Directory Domain Services áttekintése | Microsoft Docs
description: Ebből az áttekintésből megtudhatja, hogy milyen Azure Active Directory Domain Services biztosít, és hogyan használhatja azt a szervezetében, hogy identitás-szolgáltatásokat nyújtson a Felhőbeli alkalmazásokhoz és szolgáltatásokhoz.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: iainfou
ms.openlocfilehash: 472ff9de069e7d95cb1753a6b05830649806d2fc
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734554"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Mi az Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, a Lightweight Directory Access Protocol (LDAP) és a Kerberos/NTLM hitelesítés. Ezeket a tartományi szolgáltatásokat a tartományvezérlők (DCs) Felhőbeli üzembe helyezése, kezelése és javítása nélkül kell használni.

A felügyelt tartomány a DNS-névtér és a hozzá tartozó könyvtár. A felügyelt tartomány integrálható a meglévő Azure AD-Bérlővel, ami lehetővé teszi a felhasználók számára a meglévő hitelesítő adatokkal való bejelentkezést. Meglévő csoportokat és felhasználói fiókokat is használhat az erőforrásokhoz való hozzáférés biztosítására, amely a helyszíni erőforrások zökkenőmentesebb átemelését teszi lehetővé az Azure-ban.

Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi a felhasználók számára, hogy a meglévő hitelesítő adataik használatával bejelentkezzenek a felügyelt tartományhoz csatlakozó szolgáltatásba és alkalmazásokba. Meglévő csoportokat és felhasználói fiókokat is használhat az erőforrásokhoz való hozzáférés biztosításához. Ezek a funkciók zökkenőmentesebb átállást biztosítanak a helyszíni erőforrások számára az Azure-hoz.

> [!div class="nextstepaction"]
> [Első lépésként hozzon létre egy Azure AD DS felügyelt tartományt a Azure Portal használatával][tutorial-create]

Az Azure AD DS az Azure AD-ből replikálja az azonosító adatokat, így az Azure AD-bérlők csak felhőben működnek, vagy a helyszíni Active Directory tartományi szolgáltatások (AD DS) környezettel szinkronizálhatók. Ugyanezek az Azure AD DS-funkciók is léteznek mindkét környezetben.

* Ha meglévő helyszíni AD DS-környezettel rendelkezik, a felhasználói fiókadatok szinkronizálásával konzisztens identitást biztosíthat a felhasználóknak. További információ: [az objektumok és a hitelesítő adatok szinkronizálása felügyelt tartományokban][synchronization].
* A csak felhőalapú környezetekhez nincs szükség hagyományos helyszíni AD DS környezetre az Azure AD DS központi identitási szolgáltatásainak használatához.

A felügyelt tartományok felügyeletének megismeréséhez tekintse meg a [felhasználói fiókok, jelszavak és felügyelet fogalmait az Azure ad DSban][administration-concepts].

Az alábbi videó áttekintést nyújt arról, hogyan integrálható az Azure AD DS az alkalmazásokkal és a számítási feladatokkal a Felhőbeli Identity Services biztosításához:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>A Felhőbeli identitási megoldások nyújtásának gyakori módjai

Amikor meglévő számítási feladatokat telepít át a felhőbe, a címtárat támogató alkalmazások LDAP-t használhatnak a helyszíni AD DS-címtárhoz való olvasási vagy írási hozzáféréshez. A Windows Serveren futó alkalmazások jellemzően tartományhoz csatlakoztatott virtuális gépeken (VM-EK) vannak telepítve, hogy biztonságosan felügyelhetők legyenek Csoportházirend használatával. A végfelhasználók hitelesítéséhez az alkalmazások Windows-integrált hitelesítéssel is támaszkodhatnak, például Kerberos-vagy NTLM-hitelesítéssel.

A rendszergazdák gyakran használják az alábbi megoldások egyikét az Azure-ban futó alkalmazások azonosítására:

* Helyek közötti VPN-kapcsolat konfigurálása az Azure-ban és egy helyszíni AD DS környezetben futó munkaterhelések között.
    * A helyszíni tartományvezérlők ezután a VPN-kapcsolaton keresztül biztosítják a hitelesítést.
* Hozzon létre replika tartományvezérlőket az Azure Virtual Machines (VM) használatával, hogy kiterjessze a AD DS tartományt/erdőt a helyszíni környezetből.
    * Az Azure-beli virtuális gépeken futó tartományvezérlők hitelesítést biztosítanak, és címtáradatokat replikálnak a helyszíni AD DS környezet között.
* Önálló AD DS-környezet üzembe helyezése az Azure-ban Azure-beli virtuális gépeken futó tartományvezérlők használatával.
    * Az Azure-beli virtuális gépeken futó tartományvezérlők hitelesítést biztosítanak, de a helyszíni AD DS környezetből nem replikálódnak a címtáradatok.

Ezekkel a megközelítésekkel a helyszíni címtárhoz való VPN-kapcsolatok sebezhetővé teszik az alkalmazásokat az átmeneti hálózati hibák és kimaradások tekintetében. Ha a tartományvezérlőket az Azure-beli virtuális gépek használatával telepíti, az informatikai csapatnak kezelnie kell a virtuális gépeket, majd biztonságossá, javítani, figyelnie, biztonsági mentést kell készítenie és elhárítani azokat.

Az Azure AD DS alternatívákat biztosít a VPN-kapcsolatok helyszíni AD DS környezetbe való visszaállításához, illetve a virtuális gépek Azure-beli futtatásához és kezeléséhez az Identity Services biztosításához. Felügyelt szolgáltatásként az Azure AD DS csökkenti az összetettséget, hogy integrált identitás-megoldást hozzon létre mind a hibrid, mind a csak felhőalapú környezetekhez.

> [!div class="nextstepaction"]
> [Az Azure AD DS összehasonlítása az Azure AD-vel és az Azure-beli virtuális gépeken, illetve a helyszínen található, önállóan felügyelt AD DSekkel][compare]

## <a name="how-does-azure-ad-ds-work"></a>Hogyan működik az Azure AD DS?

Az Identity Services biztosításához az Azure létrehoz egy AD DS felügyelt tartományt egy tetszőleges virtuális hálózaton. A háttérben az Azure-beli virtuális gépeken futó Windows Server-tartományvezérlők pár jön létre. Ezeket a tartományvezérlőket nem kell kezelnie, konfigurálnia vagy frissítenie. Az Azure platform az Azure AD DS szolgáltatás részeként kezeli a tartományvezérlőket.

A felügyelt tartomány úgy van konfigurálva, hogy egy egyirányú szinkronizálást végezzen az Azure AD-ből, hogy hozzáférést biztosítson a felhasználók, csoportok és hitelesítő adatok központi készletéhez. Erőforrásokat közvetlenül a felügyelt tartományban hozhat létre, de nem szinkronizálja őket az Azure AD-vel. Az Azure-ban az ehhez a virtuális hálózathoz kapcsolódó alkalmazások, szolgáltatások és virtuális gépek közös AD DS szolgáltatásokat használhatnak, például a tartományhoz való csatlakozást, a csoportházirendet, az LDAP-t és a Kerberos/NTLM-hitelesítést.

A helyszíni AD DS környezettel rendelkező hibrid környezetekben a [Azure ad Connect][azure-ad-connect] az Azure ad-vel szinkronizálja az identitási adatokat, amelyet ezután szinkronizál az Azure AD DS.

![Szinkronizálás Azure AD Domain Services az Azure AD-vel és helyszíni Active Directory tartományi szolgáltatások az AD-kapcsolat használatával](./media/active-directory-domain-services-design-guide/sync-topology.png)

Ha működés közben szeretné megtekinteni az Azure AD DS, tekintsük át a következő példát:

* [Azure-AD DS hibrid szervezeteknél](#azure-ad-ds-for-hybrid-organizations)
* [Azure-AD DS csak felhőalapú szervezeteknek](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure-AD DS hibrid szervezeteknél

Számos szervezet olyan hibrid infrastruktúrát futtat, amely magában foglalja a Felhőbeli és a helyszíni alkalmazások számítási feladatait is. A lift és a SHIFT stratégia részeként az Azure-ba migrált örökölt alkalmazások hagyományos LDAP-kapcsolatokat használhatnak az azonosító adatok biztosításához. A hibrid infrastruktúra támogatásához a helyszíni AD DS-környezetből származó azonosító adatokat szinkronizálhatja egy Azure AD-Bérlővel. Az Azure AD DS ezeket az örökölt alkalmazásokat az Azure-ban identitás-forrással biztosítja, anélkül, hogy konfigurálni és kezelni kellene az alkalmazások kapcsolatait a helyszíni címtárszolgáltatások számára.

Tekintsük át például a Litware Corporation, egy hibrid szervezet, amely a helyszíni és az Azure-erőforrásokat is futtatja:

![Azure Active Directory Domain Services a helyszíni szinkronizálást tartalmazó hibrid szervezetek számára](./media/overview/synced-tenant.png)

* A tartományi szolgáltatásokat igénylő alkalmazások és kiszolgálói munkaterhelések üzembe helyezése az Azure-beli virtuális hálózaton történik.
    * Ebbe beletartozhatnak az Azure-ba migrált örökölt alkalmazások a felvonó és a váltási stratégia részeként.
* A helyszíni címtárból az Azure AD-bérlőre való adatszinkronizáláshoz a Litware Corporation üzembe helyezi [Azure ad Connect][azure-ad-connect].
    * A szinkronizált azonosító adatok felhasználói fiókokat és csoporttagságokat tartalmaznak.
* A Litware IT csapata lehetővé teszi, hogy az Azure AD DS az Azure AD-bérlője számára, vagy egy egyenrangú virtuális hálózatot.
* Az Azure Virtual Networkben üzembe helyezett alkalmazások és virtuális gépek ezután az Azure AD DS funkcióit használhatják, például a tartományhoz való csatlakozás, az LDAP-olvasás, az LDAP-kötés, az NTLM és a Kerberos-hitelesítés, valamint a Csoportházirend.

> [!IMPORTANT]
> A Azure AD Connect csak a helyszíni AD DS környezetekkel való szinkronizálásra kell telepíteni és konfigurálni. A felügyelt tartományokban való Azure AD Connect telepítése nem támogatott az objektumok Azure AD-be való visszaszinkronizálásához.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure-AD DS csak felhőalapú szervezeteknek

A csak felhőalapú Azure AD-bérlő nem rendelkezik helyszíni Identity forrással. A felhasználói fiókok és csoporttagságok például közvetlenül az Azure AD-ben jönnek létre és kezelhetők.

Most nézzük meg a contoso egy példáját, amely egy csak felhőalapú szervezet, amely az Azure AD-t használja az identitáshoz. Az Azure AD-ben az összes felhasználói identitás, a hitelesítő adatai és a csoporttagságok is létrejönnek és kezelhetők. A helyszíni címtárból származó összes identitási információ szinkronizálása nem Azure AD Connect további konfigurációval.

![Azure Active Directory Domain Services csak felhőalapú szervezet számára helyszíni szinkronizálás nélkül](./media/overview/cloud-only-tenant.png)

* A tartományi szolgáltatásokat igénylő alkalmazások és kiszolgálói munkaterhelések üzembe helyezése az Azure-beli virtuális hálózaton történik.
* A contoso informatikai csapata lehetővé teszi, hogy az Azure AD DS az Azure AD-bérlője számára, vagy egy társ virtuális hálózatot.
* Az Azure Virtual Networkben üzembe helyezett alkalmazások és virtuális gépek ezután az Azure AD DS funkcióit használhatják, például a tartományhoz való csatlakozás, az LDAP-olvasás, az LDAP-kötés, az NTLM és a Kerberos-hitelesítés, valamint a Csoportházirend.

## <a name="azure-ad-ds-features-and-benefits"></a>Az Azure AD DS funkciói és előnyei

A felhőben lévő alkalmazásokhoz és virtuális gépekhez az Azure AD DS teljes mértékben kompatibilis a hagyományos AD DS környezettel olyan műveletekhez, mint a tartományhoz való csatlakozás, a biztonságos LDAP (LDAPs), a Csoportházirend, a DNS-kezelés és az LDAP-kötés, valamint az olvasási támogatás. Az LDAP-írási támogatás az Azure AD DS felügyelt tartományban létrehozott objektumok számára érhető el, az Azure AD-ből nem szinkronizált erőforrások azonban nem.

Ha többet szeretne megtudni az identitási lehetőségekről, [hasonlítsa össze az azure AD DS az Azure ad-vel, az Azure-beli virtuális gépek Active Directory tartományi szolgáltatásokával és a helyszíni Active Directory tartományi szolgáltatásokával][compare].

Az Azure AD DS alábbi funkciói egyszerűsítik az üzembe helyezési és felügyeleti műveleteket:

* **Egyszerűsített üzembe helyezési élmény:** Az Azure AD DS a Azure Portal egyetlen varázslójának használatával engedélyezhető az Azure AD-bérlő számára.
* **Az Azure ad-vel integrált:** Az Azure AD-bérlő automatikusan elérhetővé teszi a felhasználói fiókokat, a csoporttagságok és a hitelesítő adatokat. Az Azure AD-bérlőből vagy a helyszíni AD DS környezetből származó attribútumok új felhasználóit, csoportjait vagy módosításait a rendszer automatikusan szinkronizálja az Azure AD DSba.
    * Az Azure AD-hez csatolt külső címtárakban lévő fiókok nem érhetők el az Azure AD DSban. A hitelesítő adatok nem érhetők el ezen külső könyvtárak esetében, ezért nem szinkronizálhatók Azure AD DS felügyelt tartományba.
* **Vállalati hitelesítő adatok/jelszavak használata:** Az Azure AD DSban lévő felhasználók jelszavai ugyanazok, mint az Azure AD-bérlőben. A felhasználók a vállalati hitelesítő adataikat a tartományhoz csatlakozó számítógépekhez használhatják, interaktív módon vagy távoli asztalon is bejelentkezhetnek, és az Azure AD DS felügyelt tartományon keresztül hitelesíthetők.
* **NTLM-és Kerberos-hitelesítés:** Az NTLM-és Kerberos-hitelesítés támogatása lehetővé teszi a Windows-alapú hitelesítésre támaszkodó alkalmazások központi telepítését.
* **Magas rendelkezésre állás:** Az Azure AD DS több tartományvezérlőt is tartalmaz, amelyek magas rendelkezésre állást biztosítanak a felügyelt tartomány számára. Ez a magas rendelkezésre állás garantálja a szolgáltatás üzemidőét és a hibákhoz való rugalmasságot.
    * A [Azure Availability Zonest][availability-zones]támogató régiókban ezek a tartományvezérlők a további rugalmasság érdekében a zónák között is eloszthatók.

Az Azure AD DS felügyelt tartományának néhány kulcsfontosságú aspektusa a következőket foglalja magában:

* Az Azure AD DS Managed domain önálló tartomány. Nem egy helyszíni tartomány kiterjesztése.
    * Szükség esetén egyirányú kimenő erdőszintű megbízhatósági kapcsolatot hozhat létre az Azure AD DS egy helyszíni AD DS környezetbe. További információ: [Az Azure AD DS erőforrás-erdővel kapcsolatos fogalmak és szolgáltatások][ forest-trusts].
* Az informatikai csapatnak nem kell az Azure AD DS felügyelt tartományhoz tartozó tartományvezérlőket kezelnie, megjavítania vagy figyelnie.

A helyszíni AD DSt futtató hibrid környezetek esetében nem szükséges az AD-replikáció kezelése az Azure AD DS felügyelt tartományba. A helyszíni címtárból származó felhasználói fiókok, csoporttagság és hitelesítő adatok az Azure AD-vel [Azure ad Connecton][azure-ad-connect]keresztül szinkronizálhatók. Ezek a felhasználói fiókok, csoporttagság és hitelesítő adatok automatikusan elérhetők az Azure AD DS felügyelt tartományon belül.


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure AD DS más identitási megoldásokkal való összehasonlításáról és a szinkronizálás működéséről, tekintse meg a következő cikkeket:

* [Az Azure AD DS összehasonlítása az Azure AD-vel, az Azure-beli virtuális gépek Active Directory tartományi szolgáltatások és a helyszíni Active Directory tartományi szolgáltatások][compare]
* [Ismerje meg, hogyan szinkronizálja Azure AD Domain Services az Azure AD-címtárral][synchronization]

Első lépésként [hozzon létre egy felügyelt tartományt a Azure Portal használatával][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
