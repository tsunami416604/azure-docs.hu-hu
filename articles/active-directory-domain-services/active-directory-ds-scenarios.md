---
title: "Az Azure Active Directory tartományi szolgáltatások: Központi telepítési forgatókönyvek |} Microsoft Docs"
description: "Az Azure AD tartományi szolgáltatásokhoz központi telepítési forgatókönyvek"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: b73bfc7703d79681f0de345f4ec994da540aa2a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-scenarios-and-use-cases"></a>Központi telepítési forgatókönyvek és példák
Ebben a szakaszban úgy tekintünk, néhány forgatókönyvek és használati esetet, amelyekkel igénybe vehesse az Azure Active Directory (AD) tartományi szolgáltatásokban.

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Az Azure virtuális gépek a biztonságos, könnyen felügyelete
Azure Active Directory tartományi szolgáltatások az Azure virtuális gépek kezeléséhez zökkenőmentes módon használhatja. Azure virtuális gépekhez társíthatók a felügyelt tartományra, ezáltal lehetővé téve, hogy a vállalati AD hitelesítő adatok használatát a bejelentkezéshez. Ez a megközelítés azzal elkerülhetők az credential felügyeleti kellemetlenségektől például a helyi rendszergazdai fiókkal az Azure virtuális gépek minden egyes karbantartása.

A felügyelt tartományra tartományhoz csatlakoztatott kiszolgáló virtuális gépek is kezelhető és a csoportházirend használatával biztonságossá. Szükséges biztonsági alapkonfigurációk alkalmazása az Azure virtuális gépeken, és a vállalati biztonsági irányelveknek megfelelően őket zárolását. Csoport házirend-kezelési képességek segítségével például alkalmazásokat, amelyek indíthatja a virtuális gépeken körének korlátozásához.

![Azure virtuális gépek egyszerűbb felügyeletét](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Kiszolgálók és egyéb infrastruktúrával eléri az élettartam végi, mert a Contoso mozgatása számos alkalmazás jelenleg futó helyi a felhőbe. Az aktuális informatikai standard arra, hogy a vállalati alkalmazások üzemeltető kiszolgálók tartományhoz csatlakoztatott és a felügyelt csoportházirend használatával kell lennie. Contoso-rendszergazda inkább a tartományhoz csatlakoztatás virtuális gépek üzembe helyezett Azure, a felügyelet megkönnyítése. Ennek köszönhetően a rendszergazdák és felhasználók jelentkezhetnek a vállalati hitelesítő adataival. Egy időben a gépek csoportházirenddel szükséges biztonsági alapterveket ahhoz, hogy konfigurálhatók. Contoso nem szeretné telepíteni, felügyelete és kezelése az Azure szolgáltatásban az Azure virtuális gépek biztonságos tartományvezérlők rendelkezik. Ezért a Azure AD tartományi szolgáltatások, a használati eset kiváló alkalmasnak.

**Üzembe helyezésével kapcsolatos megjegyzések**

Vegye figyelembe a következő fontos szempontokat a központi telepítési forgatókönyv:

* Azure AD tartományi szolgáltatások által biztosított felügyelt tartományok alapértelmezés szerint egyetlen egyszerű OU (szervezeti egységhez) alapot biztosítanak. Minden tartományhoz gép egy adott strukturálatlan szervezeti egység található. Azonban választhatja, hogy egyéni szervezeti egységek létrehozásához.
* Azure AD tartományi szolgáltatások beépített csoportházirend-objektum minden egyes felhasználókhoz és számítógépekhez formájában támogatja az egyszerű csoportházirend tárolók. Egyéni csoportházirend-objektumok létrehozása és a cél azokat egyéni szervezeti egységekhez.
* Azure AD tartományi szolgáltatások az alap AD számítógép objektum séma támogatja. A számítógép-objektum sémája nem bővíthető.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Növekedési-és-shift egy helyszíni LDAP-kötési hitelesítés az Azure infrastruktúra-szolgáltatásokat használó alkalmazás
![LDAP-kötés](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso szereztek független sok éve helyszíni alkalmazás rendelkezik. Az alkalmazás jelenleg az ISV karbantartási módban van, és a kért módosításokat a rendszer a Contoso elfogadhatatlanul magas. Ez az alkalmazás rendelkezik egy webes előtér, amely egy webes űrlap használata felhasználói hitelesítő adatokat gyűjt, és majd hitelesíti a felhasználókat a vállalati Active Directory egy LDAP-kötés elvégzésével. Contoso szeretné áttelepíteni az alkalmazás Azure infrastruktúra-szolgáltatásokat. Kívánatos, hogy az alkalmazás működik, mivel, módosítása nélkül. Emellett felhasználók kell hitelesíteni tudná a meglévő vállalati hitelesítő adatok használatával és anélkül, hogy a felhasználók dolgot eltérően nem működik. Ez azt jelenti kell, hogy a végfelhasználók oblivious, ahol az alkalmazás fut, és az áttelepítés átlátszó hozzájuk kell lennie.

**Üzembe helyezésével kapcsolatos megjegyzések**

Vegye figyelembe a következő fontos szempontokat a központi telepítési forgatókönyv:

* Győződjön meg arról, hogy az alkalmazás nem kell módosítani írható/könyvtárba. Azure AD tartományi szolgáltatások által biztosított felügyelt tartományok LDAP írási hozzáférést nem támogatott.
* Nem módosítható közvetlenül a felügyelt tartomány alapján jelszavakat. A végfelhasználók vagy is módosíthatják jelszavukat az Azure AD önkiszolgáló jelszó-módosítási mechanizmus segítségével vagy a helyszíni címtár. Ezeket a változásokat a felügyelt tartományra automatikusan szinkronizálja, és elérhető szerepelnek.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Növekedési és shift LDAP használó helyszíni alkalmazások olvassa el az Azure infrastruktúra-szolgáltatásokat a könyvtár eléréséhez
Contoso rendelkezik egy helyszíni üzletági (LOB) alkalmazás kifejlesztett majdnem egy évtizedben telt el. Ez az alkalmazás kompatibilis könyvtár, és a Windows Server AD hez készült. Az alkalmazás adatokat/attribútumok felhasználókra vonatkozó olvasni az Active Directory LDAP (Lightweight Directory Access Protocol) használja. Az alkalmazás nem attribútumok módosítása vagy más módon számára a könyvtárba írást. Contoso áttelepítése az Azure infrastruktúra-szolgáltatásokat az alkalmazást, majd a készülék kivonása a korosodási helyszíni hardverre, ezt az alkalmazást tartalmazó szeretné. Az alkalmazás modern directory API-k, például a REST-alapú Azure AD Graph API használatához nem kell írni. Ezért a növekedési és shift lehetőség van szükség, amellyel az alkalmazás futtatásához a felhőben kód módosítása vagy az alkalmazás újraírását nélkül telepíthetők át.

**Üzembe helyezésével kapcsolatos megjegyzések**

Vegye figyelembe a következő fontos szempontokat a központi telepítési forgatókönyv:

* Győződjön meg arról, hogy az alkalmazás nem kell módosítani írható/könyvtárba. Azure AD tartományi szolgáltatások által biztosított felügyelt tartományok LDAP írási hozzáférést nem támogatott.
* Győződjön meg arról, hogy az alkalmazás nem kell egyéni bővített Active Directory-sémát. Sémakiterjesztések nem támogatottak az Azure AD tartományi szolgáltatásokban.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Azure infrastruktúra-szolgáltatásokat a helyszíni szolgáltatás vagy démon alkalmazások áttelepítése
Egyes alkalmazások a többrétegű konfigurációk, ahol a rétegek egyik szükséges-e a hitelesített hívásokon egy háttér-réteghez, például az adatbázis-rétegből állnak. Active Directory-fiókok gyakran használják az alábbi használati eseteket. Beállíthatja a növekedési és shift Azure infrastruktúra-szolgáltatásokat, és az identitás kell az alkalmazások az Azure AD tartományi szolgáltatások az ilyen alkalmazások. Ha szeretné, használja ugyanazt a fiókot, amely a helyszíni címtárból az Azure AD szinkronizálása. Alternatív megoldásként először hozzon létre egy egyéni szervezeti Egységet, és majd hozzon létre egy különálló szolgáltatásfiókhoz szervezeti egységre, az ilyen alkalmazások központi telepítése.

![A szolgáltatásfiók WIA használatával](./media/active-directory-domain-services-scenarios/wia-service-account.png)

A Contoso egy egyedi tároló alkalmazás, amely tartalmaz egy előtér-webkiszolgáló, egy SQL server és a háttérkiszolgáló FTP rendelkezik. Windows-hitelesítést szolgáltatásfiókok előtér az FTP-kiszolgáló hitelesítésére szolgál. Az előtér-webkiszolgáló szolgáltatás fiókként futtatni be van állítva. A háttérkiszolgáló az előtér-webkiszolgáló szolgáltatás-fiókból hozzáférés engedélyezésére van konfigurálva. Contoso inkább nem szeretné, hogy a tartomány a tartományvezérlő virtuális gép a felhőben az alkalmazást a Azure infrastruktúra-szolgáltatásokat a telepítéséhez. Contoso-rendszergazda telepítheti az előtér-webkiszolgáló, az SQL server és az Azure virtuális gépek az FTP-kiszolgálót üzemeltető kiszolgálók. Ezek a gépek majd csatlakozott az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz. Majd, azokat is ugyanazt a fiókot a helyi címtárban hitelesítési célokra használni az alkalmazást. Ennek a szolgáltatásfióknak az Azure AD tartományi szolgáltatások által kezelt tartomány szinkronizálja, és használható.

**Üzembe helyezésével kapcsolatos megjegyzések**

Vegye figyelembe a következő fontos szempontokat a központi telepítési forgatókönyv:

* Győződjön meg arról, hogy az alkalmazás felhasználónév/jelszó használja a hitelesítéshez. -Alapú tanúsítvány/intelligens kártyás hitelesítés Azure AD tartományi szolgáltatások által nem támogatott.
* Nem módosítható közvetlenül a felügyelt tartomány alapján jelszavakat. A végfelhasználók vagy is módosíthatják jelszavukat az Azure AD önkiszolgáló jelszó-módosítási mechanizmus segítségével vagy a helyszíni címtár. Ezeket a változásokat a felügyelt tartományra automatikusan szinkronizálja, és elérhető szerepelnek.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>A Windows Server távoli asztali szolgáltatások az Azure-ban központi telepítések
Azure AD tartományi szolgáltatások segítségével felügyelt AD tartományi szolgáltatások biztosításához az Azure szolgáltatásba telepített távoli asztali kiszolgálók.

Ez a telepítési forgatókönyv kapcsolatos további információkért lásd: hogyan [integrálni Azure AD tartományi szolgáltatásokat a távoli asztali szolgáltatások telepítés](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Tartományhoz csatlakozó HDInsight-fürtök (előzetes verzió)
Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz tartományhoz csatlakoztatott Apache Pletyka engedélyezve van az Azure HDInsight-fürtök állíthat be. Hozzon létre és Pletyka Apache Hive-házirendeket alkalmazni, és engedélyezése a felhasználók (például adatszakértőkön) való kapcsolódáshoz a Hive ODBC-alapú eszközökkel, például az Excel, a Tableau stb. A Microsoft egyéb munkaterhelések, például a HBase, Spark és Storm, hozzáadása HDInsight tartományhoz hamarosan dolgozik.

Ez a telepítési forgatókönyv kapcsolatos további információkért lásd: hogyan [tartományhoz a HDInsight-fürtök konfigurálása](../hdinsight/hdinsight-domain-joined-configure.md)
