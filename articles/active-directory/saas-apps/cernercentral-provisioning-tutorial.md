---
title: 'Oktatóanyag: Felhasználói kiépítés a Cerner Central - Azure AD'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt úgy, hogy a felhasználók automatikusan kiépítsék a Cerner Central egy névsorát.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ed04d8fdcc2d79c66e2ebc53c737c78664e4621
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058316"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Oktatóanyag: A Cerner Central konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy megmutassa a Cerner Central és az Azure AD által végrehajtandó lépéseket a felhasználói fiókok automatikus kiépítéséhez és az Azure AD-ből a Cerner Central felhasználói névsorába történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* Egy Azure Active Directory-bérlő
* A Cerner Központi bérlő

> [!NOTE]
> Az Azure Active Directory integrálható a Cerner Central az [SCIM](http://www.simplecloud.info/) protokoll használatával.

## <a name="assigning-users-to-cerner-central"></a>Felhasználók hozzárendelése a Cerner Centralhoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói fiók kiépítése, csak a felhasználók és csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak. 

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-ben mely felhasználók és/vagy csoportok képviselik azon felhasználókat, akiknek hozzáférésre van szükségük a Cerner Centralhoz. Miután eldöntötte, ezeket a felhasználókat hozzárendelheti a Cerner Centralhoz az alábbi utasításokat követve:

[Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Fontos tippek a felhasználók Cerner Centralhoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Cerner Centralhoz a létesítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Miután a kezdeti tesztelés befejeződött egyetlen felhasználó számára, a Cerner Central azt javasolja, hogy a Cerner-megoldáshoz (nem csak a Cerner Centralhoz) hozzáférésre szánt felhasználók teljes listáját rendeljék hozzá a Cerner felhasználói listájához.  Más Cerner-megoldások is használhatják a felhasználói névjegyzékben szereplő felhasználók listáját.

* Amikor egy felhasználót a Cerner Central hoz rendel, ki kell **választania** a Felhasználói szerepkört a hozzárendelési párbeszédpanelen. Az "Alapértelmezett hozzáférés" szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Felhasználói kiépítés konfigurálása a Cerner Central szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-nek a Cerner Central felhasználói névsorához való csatlakoztatásával a Cerner SCIM felhasználói fiók létesítési API-jával, és konfigurálja a kiépítési szolgáltatást a hozzárendelt felhasználói fiókok létrehozásához, frissítéséhez és letiltásához a Cerner Central ban felhasználói és csoporthozzárendelés az Azure AD-ben.

> [!TIP]
> Az [Azure Portalon](https://portal.azure.com)megadott utasításokat követve engedélyezheti az SAML-alapú egyszeri bejelentkezést a Cerner Central számára. Egyszeri bejelentkezés konfigurálható az automatikus kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást. További információt a [Cerner Central egyszeri bejelentkezési oktatóanyagában talál.](cernercentral-tutorial.md)

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Az automatikus felhasználói fiók-kiépítés konfigurálása az Azure AD Cerner Central szolgáltatásába:

A Cerner Central felhasználói fiókok kiépítéséhez cerner központi rendszerfiókot kell kérnie a Cernertől, és létre kell hoznia egy OAuth-tulajdonosi jogkivonatot, amelyet az Azure AD a Cerner SCIM-végponthoz való csatlakozáshoz használhat. Azt is javasoljuk, hogy az integráció tegy cerner sandbox környezetben kell végrehajtani, mielőtt éles környezetben üzembe helyezne.

1. Az első lépés annak biztosítása, hogy a Cerner és az Azure AD-integrációt kezelő személyek rendelkezhessenek egy CernerCare-fiókkal, amely az utasítások végrehajtásához szükséges dokumentáció eléréséhez szükséges. Ha szükséges, használja az alábbi URL-címeket a CernerCare-fiókok létrehozásához minden adott környezetben.

   * Homokozó:https://sandboxcernercare.com/accounts/create

   * Termelés:https://cernercare.com/accounts/create  

2. Ezután létre kell hozni egy rendszerfiókot az Azure AD-hez. Az alábbi utasításokat követve rendszerfiókot kérhet a sandboxhoz és az éles környezethez.

   * Utasításokat:https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Homokozó:https://sandboxcernercentral.com/system-accounts/

   * Termelés:https://cernercentral.com/system-accounts/

3. Ezután hozzon létre egy OAuth-tulajdonosi jogkivonatot az egyes rendszerfiókokhoz. Ehhez kövesse az alábbi utasításokat.

   * Utasításokat:https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Homokozó:https://sandboxcernercentral.com/system-accounts/

   * Termelés:https://cernercentral.com/system-accounts/

4. Végül be kell szereznie a felhasználói beosztás realm azonosítókat mind a sandbox és az éles környezetek Cerner a konfiguráció befejezéséhez. A beszerzésmódjáról a következő https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIMtémakörben talál tájékoztatást: . 

5. Most már konfigurálhatja az Azure AD-t, hogy felhasználói fiókokat állítson ki a Cernerbe. Jelentkezzen be az [Azure Portalon](https://portal.azure.com), és keresse meg az **Azure Active Directory > Vállalati alkalmazások > minden alkalmazás** szakaszban.

6. Ha már konfigurálta a Cerner Central-t egyszeri bejelentkezéshez, keresse meg a Cerner Central példányát a keresőmező használatával. Ellenkező esetben válassza **a Hozzáadás** és keresés a **Cerner Central** elemet az alkalmazásgyűjteményben. Válassza a Cerner Central elemet a keresési eredmények között, és adja hozzá az alkalmazások listájához.

7. Válassza ki a Cerner Central példányát, majd válassza a **Kiépítés** lapot.

8. Állítsa a **létesítési módot** **Automatikus**ra.

   ![Cerner központi kiépítés](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Töltse ki a következő mezőket a **Rendszergazdai hitelesítő adatok**csoportban:

   * A **Bérlő URL-címe** mezőben adjon meg egy URL-címet az alábbi formátumban, és cserélje ki a "User-Roster-Realm-ID" szót a #4 lépésben beszerzett bazm-azonosítóval.

    > Homokozó:https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Termelés:https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * A **Titkos jogkivonat** mezőbe írja be a #3 lépésben létrehozott OAuth-jelntró-tokent, és kattintson a **Kapcsolat tesztelése**gombra.

   * A portál jobb felső részén sikeres értesítést kell látnia.

1. Adja meg annak a személynek vagy csoportnak az e-mail címét, akinek kiépítési hibaértesítéseket kell kapnia az **Értesítési e-mail** mezőben, és jelölje be az alábbi jelölőnégyzetet.

1. Kattintson a **Mentés** gombra.

1. Az **Attribútum-leképezések** szakaszban tekintse át az Azure AD-ről a Cerner Central ra szinkronizálandó felhasználói és csoportattribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a Cerner Central felhasználói fiókjainak és csoportjainak megfelelően szolgálnak a frissítési műveletekhez. A módosítások véglegesítéséhez kattintson a Mentés gombra.

1. Az Azure AD-létesítési szolgáltatás cerner central, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban

1. Kattintson a **Mentés** gombra.

Ezzel elindítja a Cerner Centralhoz rendelt felhasználók és/vagy csoportok kezdeti szinkronizálását a Felhasználók és csoportok szakaszban. A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenységnaplókra mutató hivatkozásokat, amelyek a kiépítési szolgáltatás által a Cerner Central alkalmazásban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Cerner Central: Identitásadatok közzététele az Azure AD használatával](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Oktatóanyag: A Cerner Central konfigurálása egyszeri bejelentkezéshez az Azure Active Directoryval](cernercentral-tutorial.md)
* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések becsatornázásáról.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
