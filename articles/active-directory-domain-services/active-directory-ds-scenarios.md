---
title: 'Az Azure Active Directory Domain Services: Központi telepítési forgatókönyv |} A Microsoft Docs'
description: Az Azure AD Domain Services központi telepítési forgatókönyvei
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: db2bd855300d93d832a3dd7ca0ce526478824ccc
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502569"
---
# <a name="deployment-scenarios-and-use-cases"></a>Központi telepítési forgatókönyvek és példák
Ebben a szakaszban áttekintjük, néhány forgatókönyvek és példák, amelyek az Azure Active Directory (AD) Domain Servicesben.

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Azure-beli virtuális gépek felügyelete biztonságos, könnyen
Az Azure Active Directory Domain Services segítségével kezelheti az Azure-beli virtuális gépek zökkenőmentes módon. A felügyelt tartományba, ezáltal lehetővé téve, hogy a vállalati AD hitelesítő adataival jelentkezzen be Azure-beli virtuális gépek lehet csatlakoztatni. Ezzel a módszerrel elkerülhetők a biztonsági hitelesítő adatok kezelése és például a helyi rendszergazdai fiókok, az Azure-beli virtuális gépek mindegyike karbantartása.

A felügyelt tartományhoz csatlakoztatott kiszolgáló virtuális gépek is felügyelt és biztonságos csoportházirend használatával. Szükséges biztonsági előírások vonatkoznak az Azure-beli virtuális gépek és a vállalati biztonsági irányelveknek megfelelően őket zárolását. Például szabályzat csoportkezelési képességeinek használatával korlátozza az alkalmazásokat, amelyek a két virtuális gép indíthatja el.

![Az Azure-beli virtuális gépek egyszerűsített felügyelet](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Ahogy a kiszolgálók és az egyéb infrastruktúrát eléri a teljes életciklusa, Contoso mozgatása jelenleg a felhőbe helyszíni környezetben üzemelő számos alkalmazás. A jelenlegi informatikai standard előírásoknak, hogy a vállalati alkalmazásokat üzemeltető kiszolgálók tartományhoz csatlakoztatott és a felügyelt csoportházirend használatával kell lennie. A Contoso tartományhoz való csatlakozás virtuális gépekhez az Azure-ban üzembe helyezett felügyeleti könnyebben részesíti előnyben IT-rendszergazdához. Ennek eredményeképpen a rendszergazdák és felhasználók is jelentkezzen be vállalati hitelesítői adataikkal. Egy időben a gépek csoportházirenddel szükséges biztonsági alapterveket ahhoz, hogy konfigurálhatók. Contoso nem szeretné történő üzembe helyezése, figyelheti és kezelheti a tartományvezérlőket az Azure-beli virtuális gépek védelméhez az Azure-ban. Az Azure AD Domain Services ezért egy kiválóan alkalmas a ezt a használati esetet.

**Üzembe helyezésével kapcsolatos megjegyzések**

Vegye figyelembe az alábbi fontos szempontokat a központi telepítési forgatókönyv:

* Az Azure AD Domain Services által kínált felügyelt tartományok alapértelmezés szerint biztosít egy egyetlen egybesimított (szervezeti egység) Szervezetiegység-struktúrára. Minden tartományhoz csatlakoztatott gépeket egy adott egybesimított szervezeti egység található. Előfordulhat, hogy azonban szeretne létrehozni az egyéni szervezeti egységekhez.
* Az Azure AD Domain Services egyszerű csoportházirend támogatja a beépített csoportházirend-objektumok egyes a felhasználók és számítógépek formájában tárolók. Egyéni csoportházirend-objektumok létrehozásához, és továbbíthatja azokat az egyéni szervezeti egységekhez.
* Az Azure AD tartományi szolgáltatások az alapvető AD számítógép objektum séma támogatja. A számítógép-objektum sémája nem terjeszthetők ki.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Lift-and-shift LDAP-kötés hitelesítéséhez az Azure infrastruktúra-szolgáltatásokat használó helyszíni alkalmazások
![LDAP-kötés](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso egy helyszíni alkalmazás, a független Szoftverszállító vásárolt sok éve rendelkezik. Az alkalmazás jelenleg karbantartási módban a független szoftvergyártó, és módosításokat igénylő contoso elfogadhatatlanul magas-e. Ez az alkalmazás rendelkezik egy webes előtér, amely egy webes űrlap használata felhasználói hitelesítő adatokat gyűjt, és ezután hitelesíti a felhasználókat egy LDAP-kötés a vállalati Active Directoryhoz elvégzésével. Contoso, ha az alkalmazást az Azure infrastruktúra-szolgáltatások áttelepítése. Célszerű, hogy az alkalmazás formájában is működik, bármilyen módosítása nélkül. Ezenkívül felhasználók hitelesítéséhez a meglévő vállalati hitelesítő adataikkal képesnek kell lennie felhasználókat szeretne előtérként dolgot újratanítás kellene. Más szóval lehet, hogy a végfelhasználók oblivious, ahol az alkalmazás fut, és az áttelepítés transzparens hozzájuk kell lennie.

**Üzembe helyezésével kapcsolatos megjegyzések**

Vegye figyelembe az alábbi fontos szempontokat a központi telepítési forgatókönyv:

* Győződjön meg arról, hogy az alkalmazás nem kell módosítani írható a könyvtárba. Az Azure AD Domain Services által kínált felügyelt tartományok LDAP írási hozzáférés nem támogatott.
* Jelszavak közvetlenül a felügyelt tartomány szemben nem módosítható. A végfelhasználók vagy módosíthatja a jelszavát az Azure AD önkiszolgáló jelszó-módosítási mechanizmus segítségével vagy a helyszíni címtár alapján. Ezek a változások automatikusan szinkronizálja, és elérhető a felügyelt tartományban.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Lift-and-shift LDAP használó helyszíni alkalmazások olvasási hozzáférés a címtárhoz az Azure infrastruktúra-szolgáltatások
Contoso egy helyszíni üzletági (LOB) alkalmazás kifejlesztett csaknem egy évtizede ezelőtt rendelkezik. Ez az alkalmazás tudomása könyvtára, és a Windows Server AD-hez készült. Az alkalmazás LDAP (Lightweight Directory Access Protocol) használatával olvassa el a felhasználókra vonatkozó információk/attribútumok az Active Directoryból. Az alkalmazás nem attribútumainak módosítása, vagy ellenkező esetben a könyvtárba írást. Contoso, ha az alkalmazást az Azure infrastruktúra-szolgáltatások áttelepítése, és jelenleg a ezt az alkalmazást üzemeltető elévülési a helyszíni hardver kivonása. Az alkalmazás modern directory API-k, például a REST-alapú Azure AD Graph API használatához nem kell írni. Ezért egy lift-and-shift lehetőség van szükség, amellyel az alkalmazás futtatását a felhőben, a kód módosítása vagy az alkalmazás újraírását nélkül telepíthető át.

**Üzembe helyezésével kapcsolatos megjegyzések**

Vegye figyelembe az alábbi fontos szempontokat a központi telepítési forgatókönyv:

* Győződjön meg arról, hogy az alkalmazás nem kell módosítani írható a könyvtárba. Az Azure AD Domain Services által kínált felügyelt tartományok LDAP írási hozzáférés nem támogatott.
* Győződjön meg arról, hogy az alkalmazás nem kell egyéni bővített Active Directory-sémát. Sémakiterjesztések nem támogatottak az Azure AD tartományi szolgáltatásokban.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Az Azure Infrastruktúraszolgáltatások a helyszíni szolgáltatás- vagy démonalkalmazásban alkalmazások migrálása
Egyes alkalmazások több réteget, amelyben a csomagok valamelyikére kell végrehajtania a hitelesített hívásokat indítson a háttérrendszer szintet, például az adatbázis-rétegből állnak. Active Directory-fiókok esetében ezek használati esetek gyakran használják. Lift-and-shift is az ilyen alkalmazások az Azure infrastruktúra-szolgáltatások és identitás igényeinek megfelelően, ezeket az alkalmazásokat az Azure AD tartományi szolgáltatások használata. Ha szeretné, az Azure ad-hez a helyszíni címtárból szinkronizált ugyanazt a fiókot használja. Azt is megteheti először hozzon létre egy egyéni szervezeti Egységet, és majd hozzon létre egy különálló szolgáltatásfiókhoz a szervezeti egységre, az ilyen alkalmazások központi telepítése.

![A szolgáltatásfiók WIA használatával](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso egy személyre szabott tároló szoftveralkalmazás, amely tartalmaz egy web front end, egy SQL server és a háttérkiszolgáló FTP rendelkezik. Windows-hitelesítést szolgáltatásfiókok segítségével hitelesíti a webes előtér az FTP-kiszolgálóhoz. A webes előtér-szolgáltatás fiókként történő futtatására van beállítva. A háttérkiszolgáló van konfigurálva, hogy engedélyezze a hozzáférést a szolgáltatásfiók számára a webes kezelőfelület. Contoso részesíti előnyben, hogy nem kell telepítenie egy tartomány tartományvezérlő virtuális gép áthelyezése az alkalmazást az Azure infrastruktúra-szolgáltatások a felhőben. A Contoso rendszergazdája telepítheti az előtér-webkiszolgáló, az SQL server és az FTP-kiszolgálóhoz az Azure-beli virtuális gépek üzemeltető kiszolgálók. Ezek a gépek majd csatlakoznak az Azure AD tartományi szolgáltatásokkal felügyelt tartományban. Ezt követően használhatják ugyanazt a fiókot a helyszíni címtárban az alkalmazás hitelesítési célra. Ez a szolgáltatás fiók automatikusan szinkronizálja az Azure AD tartományi szolgáltatásokkal felügyelt tartományban, és használható.

**Üzembe helyezésével kapcsolatos megjegyzések**

Vegye figyelembe az alábbi fontos szempontokat a központi telepítési forgatókönyv:

* Győződjön meg arról, hogy az alkalmazás használ-e a hitelesítéshez felhasználónév/jelszó. Tanúsítvány/intelligens kártya-alapú hitelesítés az Azure AD tartományi szolgáltatások által nem támogatott.
* Jelszavak közvetlenül a felügyelt tartomány szemben nem módosítható. A végfelhasználók vagy módosíthatja a jelszavát az Azure AD önkiszolgáló jelszó-módosítási mechanizmus segítségével vagy a helyszíni címtár alapján. Ezek a változások automatikusan szinkronizálja, és elérhető a felügyelt tartományban.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>A Windows Server távoli asztali szolgáltatások az Azure-ban üzemelő példányok
Az Azure AD Domain Services segítségével adja meg a felügyelt AD tartományi szolgáltatások a távoli asztali kiszolgálókra, az Azure-ban üzembe helyezett.

Ebben a telepítési forgatókönyvben kapcsolatos további információkért lásd: hogyan [integrálása az Azure AD tartományi szolgáltatásokat a távoli asztali szolgáltatások üzemelő példány](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Tartományhoz csatlakoztatott HDInsight-fürtök (előzetes verzió)
Beállíthat egy Azure HDInsight-fürtöt, amely az Apache Ranger engedélyezve van az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz csatlakozik. Hozzon létre, és az Apache Ranger Hive-házirendeket a alkalmazni engedélyezése a felhasználók (például adatelemzők) való csatlakozáshoz Hive ODBC-alapú eszközökkel, például az Excel, a Tableau stb. Más számítási feladatok, például a HBase, Spark- és Storm, tartományhoz csatlakoztatott HDInsight hamarosan hozzáadása a Microsoft dolgozik.

Ebben a telepítési forgatókönyvben kapcsolatos további információkért lásd: hogyan [tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása](../hdinsight/domain-joined/apache-domain-joined-configure.md)
