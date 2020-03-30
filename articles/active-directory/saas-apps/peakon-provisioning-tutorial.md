---
title: 'Oktatóanyag: A Peakon automatikus felhasználói kiépítéskonfigurálása az Azure Active Directoryval | Microsoft dokumentumok'
description: Megtudhatja, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiállítsa a felhasználói fiókokat a Peakon szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 0a67dc8069ee71305a47bd5d2a724a61cec234a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063392"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Oktatóanyag: A Peakon konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Peakon és az Azure Active Directoryban (Azure AD) végrehajtandó lépéseket, hogy az Azure AD-t úgy konfigurálja, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat Peakonba.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.
## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel

* Egy Azure AD-bérlő.
* [Egy Peakon-i bérlő.](https://peakon.com/us/pricing/)
* A Peakon rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="assigning-users-to-peakon"></a>Felhasználók hozzárendelése a Peakonhoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Peakonhoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a Peakonhoz rendelheti az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Fontos tippek a felhasználók Peakonhoz való hozzárendeléséhez 

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Peakon az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót Peakonhoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-peakon-for-provisioning"></a>A Peakon beállítása kiépítésre

1.  Jelentkezzen be a [Peakon Admin konzolra.](https://app.Peakon.com/login) Kattintson a **Konfiguráció gombra.** 

    ![Peakon felügyeleti konzol](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Válassza **az Integrációk lehetőséget.**
    
    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  **Alkalmazotti kiépítés**engedélyezése .

    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Másolja az **SCIM 2.0 URL-címének** és az **OAuth bearer tokennek az értékeit.** Ezeket az értékeket a **bérlői URL-cím** és a **titkos jogkivonat** mezőben adja meg a Peakon-alkalmazás kiépítés lapján az Azure Portalon.

    ![Peakon token létrehozása](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Peakon hozzáadása a galériából

A Peakon konfigurálása az Azure AD automatikus felhasználói kiépítéshez, hozzá kell adnia a Peakon-t az Azure AD-alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Peakon**, **csúcslehetőséget** az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Peakon az eredménylistában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Automatikus felhasználói kiépítés konfigurálása Peakonba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le Peakonban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!TIP]
> Dönthet úgy is, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Peakon számára, a [Peakon Single sign-on oktatóanyag](peakon-tutorial.md)utasításait követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>A Peakon automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Peakon**lehetőséget.

    ![A Peakon hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg az **SCIM 2.0 URL-címét** és az **OAuth bearer token** értékeket, amelyeket korábban a **bérlői URL-címés** a **titkos jogkivonat** korábbi részeként kért be. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD képes legyen Peakonhoz csatlakozni. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Peakon-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

7. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

8. Kattintson a **Mentés** gombra.

9. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása Peakon szolgáltatásba**lehetőséget.

    ![Peakon felhasználói leképezések](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Tekintse át az Azure AD-ről Peakonra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Peakon felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Peakon felhasználói attribútumai](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.
    
    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által peakoni összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="connector-limitations"></a>Összekötő korlátai

* A Peakon összes egyéni felhasználói attribútumát ki kell terjeszteni a `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`Peakon egyéni SCIM felhasználói bővítményéből.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)