---
title: 'Oktatóanyag: A Looop konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok automatikus kiépítésére és a Looop-ra való kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e3e25a8c27b9a5c1bc1e7673300ac8aca9377c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057438"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Oktatóanyag: A Looop konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Looop és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok looop-ba történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [A Looop bérlő](https://www.looop.co/pricing/)
* Rendszergazdai engedélyekkel rendelkező Looop felhasználói fiókja.

## <a name="assign-users-to-looop"></a>Felhasználók hozzárendelése a Looop-hoz

Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Looophoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Looophoz az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Fontos tippek a felhasználók Looop-hoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve Looop az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Looop-hoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-looop-for-provisioning"></a>A Looop beállítása kiépítéshez

A Looop konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, le kell kérnie néhány létesítési információt a Looop-tól.

1. Jelentkezzen be a [Looop Admin konzolra,](https://app.looop.co/#/login) és válassza **a Fiók lehetőséget.** A **Fiókbeállítások csoportban** válassza a **Hitelesítés**lehetőséget.

    ![Looop hozzáadása SCIM](media/looop-provisioning-tutorial/admin.png)

2. Hozzon létre egy új jogkivonatot az **SCIM-integráció**alatti **Jogkivonat visszaállítása** gombra kattintva.

    ![Looop hozzáadása SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Másolja az **SCIM-végpontot** és a **tokent.** Ezeket az értékeket a **bérlői URL-cím** és a **titkos jogkivonat** mezők a Kiépítés lapon a Looop alkalmazás az Azure Portalon. 

    ![Looop token létrehozása](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Looop hozzáadása a galériából

Konfigurálása Looop automatikus felhasználói kiépítés az Azure AD-vel, hozzá kell adnia a Looop az Azure AD-alkalmazáskatalógusa a felügyelt SaaS-alkalmazások listájához.

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Looop**, válassza **looop** az eredménypanelen. 

    ![Looop az eredmények listájában](common/search-new-app.png)

5. Válassza ki a **Regisztráció a Looop** gombra, amely átirányítja, hogy Looop bejelentkezési oldalon. 

    ![Looop OIDC hozzáadása](media/looop-provisioning-tutorial/signup.png)

6. Mivel a Looop egy OpenIDConnect alkalmazás, a Microsoft munkahelyi fiókjával jelentkezik be a Looop-ba.

    ![Looop OIDC bejelentkezés](media/looop-provisioning-tutorial/msftlogin.png)

7. Sikeres hitelesítés után fogadja el a hozzájárulási kérést a jóváhagyási oldalon. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és átirányítja a Looop fiókjába.

    ![Looop OIDc hozzájárulása](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Automatikus felhasználói kiépítés konfigurálása a Looop szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Looop-ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>A Looop automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Looop**lehetőséget.

    ![A Looop link az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://<organisation_domain>.looop.co/scim/v2` csoportban adja meg a **bérlői URL-címet.** Például: `https://demo.looop.co/scim/v2`. Adja meg azt az értéket, amelyet korábban beolvasott és mentett a Looop-ból **titkos tokenben.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Looophoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Looop-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Looop szolgáltatásba**lehetőséget.

    ![Looop felhasználói térképezések](media/looop-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ről a Looop-ra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Looop felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Looop felhasználói attribútumok](media/looop-provisioning-tutorial/userattributes.png)

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása metahálózatok összekötőjével**lehetőséget.

    ![Looop Csoport feltérképezése](media/looop-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD és a Meta Networks Connector között szinkronizált csoportattribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Meta Networks Connector csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Looop csoport attribútumok](media/looop-provisioning-tutorial/groupattributes.png)

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Looop, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Looop-ba, ha kiválasztja a kívánt értékeket a **Hatókör** területen a **Beállítások** szakaszban.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Looop-on végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)


