---
title: Gyakori telepítési forgatókönyvek az Azure AD tartományi szolgáltatásokhoz | Microsoft dokumentumok
description: Ismerje meg az Azure Active Directory tartományi szolgáltatások néhány gyakori forgatókönyvét és használati esetét az érték biztosításához és az üzleti igények kielégítéséhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917230"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Gyakori használati esetek és forgatókönyvek az Azure Active Directory tartományi szolgáltatásokhoz

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) felügyelt tartományi szolgáltatásokat, például tartományhoz való csatlakozást, csoportházirendet, könnyű címtár-hozzáférési protokollt (LDAP) és Kerberos / NTLM-hitelesítést biztosít. Az Azure AD DS integrálható a meglévő Azure AD-bérlővel, amely lehetővé teszi a felhasználók számára, hogy meglévő hitelesítő adataikkal jelentkezzenek be. Ezeket a tartományi szolgáltatásokat anélkül használhatja, hogy üzembe kellene helyeznie, kezelnie és javítania kellene a tartományvezérlőket a felhőben, ami a helyszíni erőforrások azure-ba történő zökkenőmentesebb továbbítását és áthelyezését biztosítja.

Ez a cikk néhány gyakori üzleti forgatókönyvet vázol fel, ahol az Azure AD DS értéket biztosít, és megfelel ezeknek az igényeknek.

## <a name="secure-administration-of-azure-virtual-machines"></a>Az Azure virtuális gépek biztonságos felügyelete

Az AD-hitelesítő adatok egyetlen készletének használatához az Azure virtuális gépek (VM-ek) csatlakozhatnak egy Azure AD DS felügyelt tartományhoz. Ez a megközelítés csökkenti a hitelesítő adatok kezelésével kapcsolatos problémákat, például a helyi rendszergazdai fiókok karbantartása minden virtuális gépen, vagy külön fiókok és jelszavak környezetek között.

Az Azure AD DS felügyelt tartományhoz csatlakozott virtuális gépek is kezelhetők és biztonságosak a csoportházirend használatával. A szükséges biztonsági alaptervek a virtuális gépekre alkalmazhatók a vállalati biztonsági irányelveknek megfelelő zárolásukhoz. Például a csoportházirend-kezelési képességek segítségével korlátozhatja a virtuális gépen indítható alkalmazások típusait.

![Az Azure virtuális gépek egyszerűsített felügyelete](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Nézzünk meg egy gyakori példa forgatókönyv. Mivel a kiszolgálók és más infrastruktúra eléri az élettartam a végét, a Contoso át kívánja helyezni a jelenleg a helyszínen tárolt alkalmazásokat a felhőbe. A jelenlegi informatikai szabvány azt írja elő, hogy a vállalati alkalmazásokat tároló kiszolgálókat tartományhoz kell csatlakoztatni, és csoportházirenddel kell kezelni. A Contoso informatikai rendszergazdája inkább az Azure-ban üzembe helyezett virtuális gépekhez szeretne csatlakozni, hogy megkönnyítse a felügyeletet, mivel a felhasználók ezután bejelentkezhetnek a vállalati hitelesítő adataikkal. A tartományhoz való csatlakozáskor a virtuális gépek is konfigurálhatók úgy, hogy csoportházirend-objektumok (Csoportházirend-objektumok) használatával megfeleljenek a szükséges biztonsági alapterveknek. A Contoso nem szeretné üzembe helyezni, figyelni és kezelni saját tartományvezérlőit az Azure-ban.

Az Azure AD DS kiválóan alkalmas erre a használati esetre. Az Azure AD DS felügyelt tartomány lehetővé teszi a tartományhoz csatlakozó virtuális gépek, egyetlen hitelesítő adatok használata, és alkalmazza a csoportházirendet. Felügyelt tartományként nem kell saját maga konfigurálnia és karbantartania a tartományvezérlőket.

### <a name="deployment-notes"></a>Telepítési megjegyzések

A következő üzembe helyezési szempontok vonatkoznak erre a példa használati esetre:

* Az Azure AD DS által felügyelt tartományok alapértelmezés szerint egyetlen, egysíkú szervezeti egység (OU) struktúrát használnak. Minden tartományhoz csatlakozó virtuális gép egyetlen szervezeti egységben található. Ha szükséges, létrehozhat egyéni ous-ok.
* Az Azure AD DS egy beépített gpo-t használ a felhasználók és a számítógépek tárolóihoz. További vezérlésérdekében létrehozhat egyéni csoportházirend-csoportokat, és egyéni operációs rendszercsoportokra célozhatja őket.
* Az Azure AD DS támogatja az alap AD számítógép-objektum séma. A számítógép-objektum sémája nem bővíthető.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>LDAP-kötéshitelesítést használó helyszíni lift-és shift alkalmazások

Mintaforgatókönyvként a Contoso rendelkezik egy helyszíni alkalmazással, amelyet sok évvel ezelőtt vásárolt egy független eszköztől. Az alkalmazás jelenleg karbantartási módban az ISV és az alkalmazás módosításának kérése megfizethetetlenül drága. Ez az alkalmazás egy webalapú előtérrel rendelkezik, amely egy webes űrlap használatával gyűjti a felhasználói hitelesítő adatokat, majd hitelesíti a felhasználókat a helyszíni AD DS-környezethez való LDAP-kötés végrehajtásával.

![LDAP-kötés](./media/active-directory-domain-services-scenarios/ldap-bind.png)

A Contoso szeretné áttelepíteni ezt az alkalmazást az Azure-ba. Az alkalmazásnak továbbra is úgy kell működnie, ahogy van, és nincs szükség változtatásra. Emellett a felhasználók nak képesnek kell lennie arra, hogy hitelesítse a meglévő vállalati hitelesítő adatok használatával és további képzés nélkül. Az alkalmazás futtatásának helye szerinti végfelhasználók számára átláthatónak kell lennie.

Ebben a forgatókönyvben az Azure AD DS lehetővé teszi, hogy az alkalmazások ldap-kötéseket hajtsanak végre a hitelesítési folyamat részeként. Az örökölt helyszíni alkalmazások feloldhatják és áthelyezhetik az Azure-ba, és zökkenőmentesen hitelesíthetik a felhasználókat a konfiguráció vagy a felhasználói élmény megváltoztatása nélkül.

### <a name="deployment-notes"></a>Telepítési megjegyzések

A következő üzembe helyezési szempontok vonatkoznak erre a példa használati esetre:

* Győződjön meg arról, hogy az alkalmazásnak nem kell módosítania/írnia a könyvtárba. LDAP írási hozzáférés egy Azure AD DS felügyelt tartomány nem támogatott.
* A jelszavakat nem módosíthatja közvetlenül egy Azure AD DS felügyelt tartományban. A végfelhasználók módosíthatják a jelszavukat az Azure AD önkiszolgáló jelszó-módosítási mechanizmusával vagy a helyszíni címtárral. Ezek a módosítások ezután automatikusan szinkronizálódik, és elérhető az Azure AD DS felügyelt tartományban.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>A címtár eléréséhez LDAP olvasást használó helyszíni lift-and-shift alkalmazások

Az előző példaforgatókönyvhöz hasonlóan tegyük fel, hogy a Contoso rendelkezik egy helyszíni üzletági (LOB) alkalmazással, amelyet csaknem egy évtizeddel ezelőtt fejlesztettek ki. Ez az alkalmazás könyvtár-tisztában van, és úgy tervezték, hogy ldap használatával olvassa információk / attribútumok a felhasználók a DS. Az alkalmazás nem módosítja az attribútumokat, és más módon nem ír a könyvtárba.

A Contoso át szeretné telepíteni ezt az alkalmazást az Azure-ba, és ki szeretné vonni az alkalmazás jelenleg üzemeltető, helyszíni hardverelévét. Az alkalmazás nem írható újra a modern címtár API-k, például a REST-alapú Microsoft Graph API használatához. A lift-and-shift lehetőség szükséges, ahol az alkalmazás áttelepíthető a felhőben való futtatásra, kód módosítása vagy az alkalmazás újraírása nélkül.

Ebben a forgatókönyvben az Azure AD DS lehetővé teszi, hogy az alkalmazások ldap-olvasásokat hajtsanak végre a felügyelt tartományon, hogy lekérhessenek a szükséges attribútumadatokat. Az alkalmazást nem kell újraírni, így az Azure-ba való feloldás és áthelyezés lehetővé teszi a felhasználók számára, hogy továbbra is használják az alkalmazást anélkül, hogy észrevennék, hogy megváltozott a futási helye.

### <a name="deployment-notes"></a>Telepítési megjegyzések

A következő üzembe helyezési szempontok vonatkoznak erre a példa használati esetre:

* Győződjön meg arról, hogy az alkalmazásnak nem kell módosítania/írnia a könyvtárba. LDAP írási hozzáférés egy Azure AD DS felügyelt tartomány nem támogatott.
* Győződjön meg arról, hogy az alkalmazásnak nincs szüksége egyéni/kiterjesztett Active Directory-sémára. Séma-bővítmények nem támogatottak az Azure AD DS.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Helyszíni szolgáltatás vagy démonalkalmazás áttelepítése az Azure-ba

Egyes alkalmazások több réteget tartalmaznak, ahol az egyik rétegnek hitelesített hívásokat kell végrehajtania egy háttérréteghez, például egy adatbázishoz. Az AD szolgáltatásfiókokat ezekben az esetekben gyakran használják. Amikor az Azure-ba emeli és áthelyezi az alkalmazásokat, az Azure AD DS lehetővé teszi, hogy továbbra is ugyanúgy használja a szolgáltatásfiókokat. Választhat, hogy ugyanazt a szolgáltatásfiókot, amely szinkronizálva van a helyszíni könyvtárból az Azure AD, vagy hozzon létre egy egyéni szervezeti egységet, és hozzon létre egy külön szolgáltatásfiókot, hogy a szervezeti egység. Mindkét megközelítés, alkalmazások továbbra is ugyanúgy működik, hogy a hitelesített hívások más szintek és szolgáltatások.

![Szolgáltatásfiók wia használatával](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Ebben a példában a Contoso rendelkezik egy egyéni szoftvertároló-alkalmazással, amely egy webes előtér-kiszolgálót, egy SQL-kiszolgálót és egy háttér-FTP-kiszolgálót tartalmaz. A szolgáltatásfiókokat használó Windows-alapú hitelesítés hitelesíti a webelőtért az FTP-kiszolgálón. A webes előtér szolgáltatásfiókként való futtatásra van beállítva. A háttérkiszolgáló úgy van beállítva, hogy engedélyezze a webes előtér szolgáltatásfiókjából való hozzáférést. A Contoso nem szeretné üzembe helyezni és kezelni a saját tartományvezérlő virtuális gépeit a felhőben az alkalmazás Azure-ba való áthelyezéséhez.

Ebben a forgatókönyvben a webelőtér, az SQL-kiszolgáló és az FTP-kiszolgáló tüzemeltető kiszolgálók áttelepíthetők az Azure virtuális gépekre, és csatlakozhatnak egy Azure AD DS felügyelt tartományhoz. A virtuális gépek ezután használhatja ugyanazt a szolgáltatásfiókot a helyszíni könyvtárban az alkalmazás hitelesítési célokra, amely szinkronizálja az Azure AD-n keresztül az Azure AD Connect használatával.

### <a name="deployment-notes"></a>Telepítési megjegyzések

A következő üzembe helyezési szempontok vonatkoznak erre a példa használati esetre:

* Győződjön meg arról, hogy az alkalmazások felhasználónevet és jelszót használnak a hitelesítéshez. Tanúsítvány vagy intelligenskártya-alapú hitelesítés nem támogatja az Azure AD DS.
* A jelszavakat nem módosíthatja közvetlenül egy Azure AD DS felügyelt tartományban. A végfelhasználók módosíthatják a jelszavukat az Azure AD önkiszolgáló jelszó-módosítási mechanizmusával vagy a helyszíni címtárral. Ezek a módosítások ezután automatikusan szinkronizálódik, és elérhető az Azure AD DS felügyelt tartományban.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>A Windows Server távoli asztali szolgáltatások központi telepítéseaz Azure-ban

Az Azure AD DS használatával felügyelt tartományi szolgáltatásokat nyújthat az Azure-ban telepített távoli asztali kiszolgálóknak. A telepítési forgatókönyvről további információt az [Azure AD tartományi szolgáltatások RDS-telepítéssel való integrálása című témakörben talál.][windows-rds]

## <a name="domain-joined-hdinsight-clusters"></a>Tartományhoz csatlakozott HDInsight-fürtök

Beállíthat egy Azure HDInsight-fürtöt, amely egy Azure AD DS által felügyelt tartományhoz csatlakozik, és engedélyezve van az Apache Ranger. Hozhat létre és alkalmazhat Hive-szabályzatokat az Apache Rangeren keresztül, és engedélyezheti a felhasználóknak, például az adatszakértőknek, hogy ODBC-alapú eszközökkel, például excel vagy tabló használatával csatlakozzanak a Hive-hoz. Továbbra is dolgozunk azon, hogy más számítási feladatokat is hozzáadjunk, például a HBase, a Spark és a Storm a tartományhoz csatlakozott HDInsighthoz.

A telepítési forgatókönyvről további információt a [tartományhoz csatlakozott HDInsight-fürtök konfigurálása című témakörben talál.][hdinsight]

## <a name="next-steps"></a>További lépések

Első lépésekhez [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
