---
title: Gyakori üzembe helyezési forgatókönyvek Azure AD Domain Serviceshoz | Microsoft Docs
description: Ismerkedjen meg a gyakori forgatókönyvekkel és a Azure Active Directory Domain Services az üzleti igények kielégítéséhez szükséges példákkal.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917230"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Gyakori használati esetek és forgatókönyvek Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, a Lightweight Directory Access Protocol (LDAP) és a Kerberos/NTLM hitelesítés. Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel, ami lehetővé teszi a felhasználók számára, hogy a meglévő hitelesítő adataikkal jelentkezzenek be. Ezeket a tartományi szolgáltatásokat a tartományvezérlők üzembe helyezése, kezelése és javítása nélkül használhatja a felhőben, ami zökkenőmentesebb átállást biztosít a helyszíni erőforrások számára az Azure-ba.

Ez a cikk néhány olyan gyakori üzleti forgatókönyvet mutat be, ahol az Azure AD DS értékkel rendelkezik, és megfelel ezeknek az igényeknek.

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure-beli virtuális gépek biztonságos felügyelete

Az Active Directory-beli hitelesítő adatok egyetlen készletének használatához az Azure Virtual Machines (VM) csatlakoztatható egy Azure AD DS felügyelt tartományhoz. Ez a megközelítés csökkenti a hitelesítő adatok kezelésével kapcsolatos problémákat, például a helyi rendszergazdai fiókok karbantartását minden virtuális gépen, illetve a különböző fiókokat és jelszavakat.

Az Azure AD DS felügyelt tartományhoz csatlakoztatott virtuális gépeket a csoportházirend használatával is felügyelheti és biztonságossá teheti. A szükséges biztonsági alaptervek alkalmazhatók a virtuális gépekre, hogy a vállalati biztonsági irányelveknek megfelelően zárolják őket. A Csoportházirend kezelése funkció segítségével például korlátozhatja a virtuális gépen indítható alkalmazások típusát.

![Azure-beli virtuális gépek egyszerűbb felügyelete](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Nézzük meg egy gyakori példát. Mivel a kiszolgálók és más infrastruktúra eléri a teljes élettartamot, a contoso a helyileg üzemeltetett alkalmazásokat szeretné áthelyezni a felhőbe. Az aktuális IT standard megbízatása szerint a vállalati alkalmazásokat üzemeltető kiszolgálóknak tartományhoz kell csatlakozniuk, és a csoportházirend használatával kell felügyelni őket. A contoso informatikai rendszergazdája szívesebben szeretné az Azure-ban üzembe helyezett virtuális gépeket csatlakozni a felügyelet megkönnyítéséhez, mivel a felhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be. A tartományhoz csatlakoztatott virtuális gépek úgy is konfigurálhatók, hogy a csoportházirend-objektumok (GPO-k) használatával betartsák a szükséges biztonsági alapterveket. A contoso szívesebben nem helyezheti üzembe, figyelheti és felügyelheti saját tartományvezérlőit az Azure-ban.

Az Azure AD DS kiválóan alkalmas ehhez a használati esethez. Az Azure AD DS felügyelt tartományok lehetővé teszi a tartományhoz való csatlakozást, a hitelesítő adatok egyetlen készletének használatát és a csoportházirend alkalmazását. Felügyelt tartományként nem szükséges a tartományvezérlők konfigurálása és karbantartása.

### <a name="deployment-notes"></a>Üzembe helyezési megjegyzések

A következő üzembe helyezési szempontok vonatkoznak erre a példa használatára:

* Az Azure AD DS felügyelt tartományok alapértelmezés szerint egyetlen, lapos szervezeti egység (OU) struktúrát használnak. Minden tartományhoz csatlakoztatott virtuális gép egyetlen szervezeti egységben található. Ha szükséges, létrehozhat egyéni szervezeti egységeket is.
* Az Azure AD DS egy beépített csoportházirend-objektumot használ a felhasználók és számítógépek tárolók számára. További szabályozáshoz létrehozhat egyéni csoportházirend-objektumokat, és megcélozhatja azokat egyéni szervezeti egységekre.
* Az Azure AD DS támogatja az alapszintű AD számítógép-objektum sémáját. A számítógép-objektum sémája nem terjeszthető ki.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>LDAP-kötési hitelesítést használó helyszíni alkalmazások átemelése és átirányítása

Példaként a contoso egy olyan helyszíni alkalmazást tartalmaz, amelyet számos éve vásárolt egy ISV-ben. Az alkalmazás jelenleg karbantartási módban van az ISV számára, és az alkalmazás módosításainak kérelmezése megfizethetetlenül drága. Az alkalmazás webes felülettel rendelkezik, amely webes űrlap használatával gyűjti a felhasználói hitelesítő adatokat, majd a helyszíni AD DS környezethez LDAP-kötést hajt végre.

![LDAP-kötés](./media/active-directory-domain-services-scenarios/ldap-bind.png)

A contoso szeretné áttelepíteni az alkalmazást az Azure-ba. Az alkalmazásnak továbbra is működőképesnek kell lennie, és nincs szükség módosításra. Emellett a felhasználóknak a meglévő vállalati hitelesítő adataikkal és további képzés nélkül kell tudniuk hitelesíteni magukat. Transzparensnek kell lennie az alkalmazást futtató végfelhasználók számára.

Ebben az esetben az Azure AD DS lehetővé teszi, hogy az alkalmazások LDAP-kötéseket végezzenek a hitelesítési folyamat részeként. A régi helyszíni alkalmazások a konfiguráció vagy a felhasználói élmény módosítása nélkül is elvégezhetik az Azure-ba való áttérést és a felhasználók zökkenőmentes hitelesítését.

### <a name="deployment-notes"></a>Üzembe helyezési megjegyzések

A következő üzembe helyezési szempontok vonatkoznak erre a példa használatára:

* Győződjön meg arról, hogy az alkalmazásnak nem kell módosítania/írnia a könyvtárat. Az Azure AD DS felügyelt tartományhoz való LDAP írási hozzáférés nem támogatott.
* A jelszavakat nem lehet közvetlenül egy Azure AD DS felügyelt tartományon módosítani. A végfelhasználók módosíthatják a jelszavukat az Azure AD önkiszolgáló jelszó-módosítási mechanizmusával vagy a helyszíni címtárral. Ezeket a módosításokat a rendszer automatikusan szinkronizálja és elérhetővé válik az Azure AD DS felügyelt tartományában.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Az LDAP-t használó helyszíni alkalmazások átemelése a címtár eléréséhez

Az előző példához hasonlóan tegyük fel, hogy a contoso egy olyan helyszíni üzletági (LOB) alkalmazással rendelkezik, amelyet majdnem egy évtizeden át fejlesztettünk ki. Ez az alkalmazás a címtárral tisztában van, és a rendszer az LDAP használatával olvasta el a felhasználókkal kapcsolatos információkat és attribútumokat AD DS. Az alkalmazás nem módosítja az attribútumokat, vagy más módon ír a könyvtárba.

A contoso szeretné áttelepíteni ezt az alkalmazást az Azure-ba, és kivonni az alkalmazást futtató, a helyszíni helyi hardvert. Az alkalmazás nem írható át olyan modern címtár-API-k használatára, mint például a REST-alapú Microsoft Graph API. A rendszer egy emelt szintű váltási lehetőséget választ arra az esetre, ha az alkalmazás a felhőben való futtatásra, kód módosítása vagy az alkalmazás átírása nélkül telepíthető.

Ebben az esetben az Azure AD DS lehetővé teszi, hogy az alkalmazások LDAP-olvasási műveleteket végezzenek a felügyelt tartományon, hogy megkapják a szükséges attribútum-információkat. Az alkalmazást nem kell újraírni, így az Azure-ba való átállással a felhasználók továbbra is használhatják az alkalmazást anélkül, hogy megtörtént a futtatásuk.

### <a name="deployment-notes"></a>Üzembe helyezési megjegyzések

A következő üzembe helyezési szempontok vonatkoznak erre a példa használatára:

* Győződjön meg arról, hogy az alkalmazásnak nem kell módosítania/írnia a könyvtárat. Az Azure AD DS felügyelt tartományhoz való LDAP írási hozzáférés nem támogatott.
* Győződjön meg arról, hogy az alkalmazásnak nincs szüksége egyéni/kiterjesztett Active Directory sémára. A séma-bővítmények nem támogatottak az Azure AD DSban.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Helyszíni szolgáltatás vagy Daemon-alkalmazás migrálása az Azure-ba

Néhány alkalmazás több szintet is tartalmaz, amelyekben az egyik rétegnek hitelesített hívásokat kell végrehajtania egy háttérrendszer-réteghez, például egy adatbázishoz. Az AD-szolgáltatásfiókok általában ezekben a forgatókönyvekben használatosak. Ha az Azure-ba emelt szintű alkalmazásokat helyez át, az Azure AD DS lehetővé teszi, hogy a szolgáltatásfiókok ugyanúgy működjenek. Azt is megteheti, hogy ugyanazt a szolgáltatásfiókot használja, amelyet a helyszíni címtárból az Azure AD-ba Szinkronizál, vagy létrehozhat egy egyéni szervezeti egységet, majd létrehoz egy külön szolgáltatásfiókot a szervezeti egységben. Mindkét módszer esetében az alkalmazások továbbra is ugyanúgy működnek, mint a hitelesített hívásokat más rétegekbe és szolgáltatásokra.

![Szolgáltatásfiók a WIA használatával](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Ebben a példában a contoso egy egyéni kialakítású, a webes kezelőfelülettel, egy SQL Server-kiszolgálóval és egy háttérbeli FTP-kiszolgálóval elkészített szoftver-tároló alkalmazást tartalmaz. A Windows-alapú hitelesítés a szolgáltatásfiókok használatával hitelesíti a webes előtért az FTP-kiszolgáló felé. A webes kezelőfelület úgy van beállítva, hogy szolgáltatásfiókként fusson. A háttér-kiszolgáló úgy van konfigurálva, hogy engedélyezze a hozzáférést a webes kezelőfelülethez tartozó szolgáltatásfiók számára. A contoso nem szeretné telepíteni és felügyelni a saját tartományvezérlő virtuális gépeket a felhőben az alkalmazás Azure-ba való áthelyezéséhez.

Ebben az esetben a webes előtért, az SQL Servert és az FTP-kiszolgálót futtató kiszolgálók áttelepíthetők az Azure-beli virtuális gépekre, és egy Azure AD DS felügyelt tartományhoz csatlakoznak. A virtuális gépek ezután ugyanazt a szolgáltatásfiókot használhatják a helyszíni címtárban az alkalmazás hitelesítési céljaihoz, amelyet az Azure AD-val szinkronizál a Azure AD Connect használatával.

### <a name="deployment-notes"></a>Üzembe helyezési megjegyzések

A következő üzembe helyezési szempontok vonatkoznak erre a példa használatára:

* Győződjön meg arról, hogy az alkalmazások felhasználónevet és jelszót használnak a hitelesítéshez. Az Azure AD DS nem támogatja a tanúsítvány-vagy intelligenskártya-alapú hitelesítést.
* A jelszavakat nem lehet közvetlenül egy Azure AD DS felügyelt tartományon módosítani. A végfelhasználók módosíthatják a jelszavukat az Azure AD önkiszolgáló jelszó-módosítási mechanizmusával vagy a helyszíni címtárral. Ezeket a módosításokat a rendszer automatikusan szinkronizálja és elérhetővé válik az Azure AD DS felügyelt tartományában.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server Távoli asztali szolgáltatások üzembe helyezése az Azure-ban

Az Azure AD DS használatával felügyelt tartományi szolgáltatásokat biztosíthat az Azure-ban üzembe helyezett távoli asztali kiszolgálókhoz. További információ erről az üzembe helyezési forgatókönyvről: [Azure ad Domain Services integrálása az RDS][windows-rds]-környezettel.

## <a name="domain-joined-hdinsight-clusters"></a>Tartományhoz csatlakoztatott HDInsight-fürtök

Beállíthat olyan Azure HDInsight-fürtöt, amely egy Azure AD DS felügyelt tartományhoz csatlakozik, és engedélyezve van az Apache Ranger. Az Apache Ranger segítségével hozhat létre és alkalmazhat kaptár-házirendeket, és lehetővé teheti a felhasználók, például az adatszakértők számára, hogy az ODBC-alapú eszközök, például az Excel vagy a tabló használatával csatlakozzanak a Kaptárhoz. Továbbra is dolgozunk további munkaterhelések, például a HBase, a Spark és a Storm hozzáadásával a tartományhoz csatlakoztatott HDInsight.

További információ erről az üzembe helyezési forgatókönyvről: [tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása][hdinsight]

## <a name="next-steps"></a>Következő lépések

Első lépésként [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services példányt][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
