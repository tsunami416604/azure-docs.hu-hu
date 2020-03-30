---
title: 'Oktatóanyag: A Meta Networks Connector konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Felhasználói fiókok metahálózatok összekötőbe való automatikus kiépítésére és kiépítésének kiteljesítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061358"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Oktatóanyag: A Meta Networks összekötő konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Meta Networks Connector és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok metahálózatok-összekötőbe történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Meta Networks összekötő bérlője](https://www.metanetworks.com/)
* A Meta Networks Connector rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-meta-networks-connector"></a>Felhasználók hozzárendelése a Meta Networks összekötőhöz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Meta Networks Connector-hoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Meta Networks Connector-hoz az alábbi utasításokat követve:
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Fontos tippek a felhasználók Meta Networks Connector hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Meta Networks Connector az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendel a Meta Networks Connector-hoz, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Metanetworks összekötő beállítása a kiépítéshez

1. Jelentkezzen be a [Meta Networks Connector Felügyeleti konzoljára](https://login.metanetworks.com/login/) a szervezet nevével. Nyissa meg **a Felügyeleti > API-kulcsokat.**

    ![Meta Networks Connector felügyeleti konzol](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Kattintson a plusz jel a jobb felső sarokban a képernyő, hogy hozzon létre egy új **API-kulcs**.

    ![A Meta Networks Connector plus ikonja](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Állítsa be az **API-kulcs nevét** és **az API-kulcs leírását.**

    ![Meta Networks összekötő token létrehozása](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Kapcsolja be az **írási** jogosultságokat **csoportok** és **felhasználók számára.**

    ![Meta Networks összekötői jogosultságok](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Kattintson a **Hozzáadás gombra.** Másolja a **TITKOS és** mentse el, mivel ez lesz az egyetlen alkalom, akkor megtekintheti azt. Ez az érték a Meta Networks connector alkalmazás azure-portálon a Kiépítés lapján a Titkos jogkivonat mezőben lesz megadva.

    ![Meta Networks összekötő token létrehozása](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  IdP hozzáadása a **Felügyeleti > beállítások > IdP-> új létrehozása**elemre való navigálással.

    ![Meta Networks összekötő IdP hozzáadása](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Az **IdP konfigurációs** lapon **elnevezheti** az IdP-konfigurációt, és választhat egy **ikont.**

    ![Meta Networks összekötő IdP-neve](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Meta Networks Összekötő IdP ikonja](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Az **SCIM konfigurálása** csoportban válassza ki az előző lépésekben létrehozott API-kulcsnevet. Kattintson a **Mentés gombra.**

    ![Meta Networks Connector konfigurálása SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Nyissa meg **a Felügyeleti > beállítások > IdP lapot.** Az **IdP-azonosító**megtekintéséhez kattintson az előző lépésekben létrehozott IdP-konfiguráció nevére. Ez az **azonosító** hozzáadódik a **bérlői URL-cím** végéhez, miközben beírja az értéket a **Bérlő URL-cím** mezőjébe a Meta Networks connector alkalmazás kiépítés lapján az Azure Portalon.

    ![Metanetworks összekötő azonosítója](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Meta Networks összekötő hozzáadása a gyűjteményből

A Meta Networks Connector konfigurálása az Azure AD automatikus felhasználói kiépítés, hozzá kell adnia a Meta Networks Connector az Azure AD alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

**A Meta Networks Connector hozzáadása az Azure AD alkalmazásgyűjteményből, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Meta Networks Connector**( Meta Networks Connector ) kifejezést, válassza a **Meta Networks Connector** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Meta Networks Connector az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Automatikus felhasználói kiépítés konfigurálása a Meta Networks összekötőhöz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Meta Networks Connectorban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Meta Networks Connector számára, a [Meta Networks Connector Single sign-on oktatóanyag](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)utasításait követve. Az egyszeri bejelentkezés az automatikus felhasználói kiépítéstől függetlenül konfigurálható, bár ez a két funkció kiegészíti egymást

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>A Meta Networks Connector automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Meta Networks Connector (Meta Networks Connector) lehetőséget.**

    ![A Meta Networks összekötő hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://api.metanetworks.com/v1/scim/<IdP ID>` csoportban adja meg a **bérlői URL-címet.** Adja meg a **titkos jogkivonat**korábbi, beolvasott **SCIM hitelesítési token** értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Meta Networks Connector-hoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Meta Networks Connector-fiók rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása metahálózatok összekötővel lehetőséget.**

    ![Metahálózatok összekötő felhasználói leképezései](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD és a Meta Networks Connector között szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Meta Networks Connector felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Meta Networks összekötő felhasználói attribútumai](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása metahálózatok összekötőjével**lehetőséget.

    ![Metahálózatok összekötőcsoport-leképezései](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD és a Meta Networks Connector között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Meta Networks Connector csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Metahálózatok összekötőcsoport-attribútumai](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-létesítési szolgáltatás engedélyezése a Meta Networks Connector, módosítsa a **kiépítési állapot** **be van kapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Meta Networks Connector alkalmazásba a **Beállítások** szakasz **Hatókör** csoportjában a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-létesítési szolgáltatás által a Meta Networks Connector-on végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

