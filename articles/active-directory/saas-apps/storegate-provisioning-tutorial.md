---
title: 'Oktatóanyag: A Storegate konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a Felhasználói fiókok storegate-be való automatikus kiépítésére és kiépítésének kiépítésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064257"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Oktatóanyag: A Storegate konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Storegate és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok storegate-be történő automatikus kiépítéséhez és de-provision kiépítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Storegate-bérlő](https://www.storegate.com)
* Rendszergazdai engedélyekkel rendelkező Storegate-fiók.

## <a name="assign-users-to-storegate"></a>Felhasználók hozzárendelése a Storegate-hez

Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Storegate-hez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Storegate-hez az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Fontos tippek a felhasználók Storegate-hez való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve a Storegate az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót hozzárendel a Storegate-hez, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-storegate-for-provisioning"></a>A Storegate beállítása a kiépítéshez

A Storegate konfigurálása az Azure AD automatikus felhasználói kiépítés, le kell kérnie néhány létesítési információt a Storegate.For configuring Storegate for automatic user provisioning with Azure AD, you will need to retrieve some provisioning information from Storegate.

1. Jelentkezzen be a [Storegate Felügyeleti konzolra,](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) és keresse meg a beállításokat a jobb felső sarokban lévő felhasználó ikonra kattintva, és válassza a **Fiókbeállítások lehetőséget.**

    ![Storegate hozzáadása SCIM](media/storegate-provisioning-tutorial/admin.png)

2. A beállításokközött nyissa meg a **Csapat > beállítások at,** és ellenőrizze, hogy a váltókapcsoló be **van-e** kapcsolva az egyszeri bejelentkezés szakaszban.

    ![Storegate-beállítások](media/storegate-provisioning-tutorial/team.png)

    ![Storegate váltógomb](media/storegate-provisioning-tutorial/sso.png)

3. Másolja a **bérlői URL-címet** és **a jogkivonatot.** Ezek az értékek a **bérlői URL-cím** és a **titkos jogkivonat** mezőket, illetve a Kiépítés fülön a Storegate alkalmazás az Azure Portalon. 

    ![Storegate token létrehozása](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Storegate hozzáadása a gyűjteményből

A Storegate konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a Storegate-et az Azure AD-alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához.

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Storegate**, válassza **a Storegate** lehetőséget az eredménypanelen. 

    ![Storegate az eredménylistában](common/search-new-app.png)

5. Válassza ki a **Regisztráció a Storegate-re** gombot, amely átirányítja önt a Storegate bejelentkezési oldalára. 

    ![Storegate OIDC hozzáadása](media/storegate-provisioning-tutorial/signup.png)

6.  Jelentkezzen be a [Storegate Felügyeleti konzolra,](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) és keresse meg a beállításokat a jobb felső sarokban lévő felhasználó ikonra kattintva, és válassza a **Fiókbeállítások lehetőséget.**

    ![Storegate bejelentkezés](media/storegate-provisioning-tutorial/admin.png)

7. A beállításokon belül keresse meg a **Team > Settings-t,** és kattintson a kapcsolóra az Egyszeri bejelentkezés szakaszban, ez elindítja a hozzájárulási folyamatot. Kattintson az **Aktiválás gombra.**

    ![Storegate csapat](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate aktiválása](media/storegate-provisioning-tutorial/activate.png)

8. Mivel a Storegate egy OpenIDConnect alkalmazás, a Microsoft munkahelyi fiókjával jelentkezik be a Storegate alkalmazásba.

    ![Storegate OIDC bejelentkezés](media/storegate-provisioning-tutorial/login.png)

9. Sikeres hitelesítés után fogadja el a hozzájárulási kérést a jóváhagyási oldalon. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Storegate-fiókjába.

    ![Storegate OIDc hozzájárulása](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Automatikus felhasználói kiépítés konfigurálása a Storegate szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Storegate-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!NOTE]
> Ha többet szeretne megtudni a Storegate SCIM-végpontjáról, olvassa el [ezt](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)a .

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>A Storegate automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Storegate**lehetőséget.

    ![A Storegate hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://dialpad.com/scim` csoportban adja meg a **bérlői URL-címet.** Adja meg azt az értéket, amelyet korábban beolvasott és mentett a Storegate-ből **titkos jogkivonatban.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Storegate-hez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Storegate-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Storegate szolgáltatásba**lehetőséget.

    ![Storegate felhasználói leképezések](media/storegate-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből a Storegate-hez szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Storegate felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Storegate felhasználói attribútumok](media/storegate-provisioning-tutorial/userattributes.png)

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-létesítési szolgáltatás engedélyezéséhez a Storegate, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Storegate-be, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Storegate-en végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
