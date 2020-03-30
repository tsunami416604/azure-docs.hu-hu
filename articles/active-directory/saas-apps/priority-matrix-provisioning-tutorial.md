---
title: 'Oktatóanyag: A prioritási mátrix konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok elsőbbségi mátrixba való automatikus kiépítésére és kiépítésének kimerülésével.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 80ffaba6713027d216958e0be2cd4ae35a8d2d70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063441"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Oktatóanyag: A prioritásmátrix konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a prioritási mátrixban és az Azure Active Directoryban (Azure AD) végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok prioritási mátrixba történő automatikus kiépítéséhez és kiépítésének kiteljesítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban van. Az előzetes verziójú funkciók általános Microsoft Azure-használati feltételeiről a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő
* [Prioritásmátrix-bérlő](https://appfluence.com/pricing/)
* Rendszergazdai engedélyekkel rendelkező prioritási mátrix felhasználói fiókja.

## <a name="assign-users-to-priority-matrix"></a>Felhasználók hozzárendelése a prioritási mátrixhoz

Az Azure Active Directory egy hozzárendelések nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók és/vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Priority Matrixhoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a Prioritási Mátrixhoz rendelheti az alábbi utasítások szerint:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Fontos tippek a felhasználók prioritásmátrixhoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve priority matrix az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Prioritási mátrixhoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="set-up-priority-matrix-for-provisioning"></a>Prioritási mátrix beállítása kiépítéshez

A Priority Matrix konfigurálása az Azure AD automatikus felhasználói kiépítés, le kell kérnie néhány létesítési információkat priority matrix.

1. Jelentkezzen be a [Priority Matrix Felügyeleti konzolra.](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning)

3. Kattintson **az Oauth bejelentkezési token reklamációs** tokenre az Priority Matrix esetében

    ![Prioritási mátrix SCIM hozzáadása](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Kattintson az **ÚJ TOKEN BESZEREZNI** gombra. Másolja a **tokenkarakterláncot**. Ezt az értéket a **Titkos jogkivonat** mezőben adja meg a Priority Matrix alkalmazás kiépítés lapján az Azure Portalon. 

    ![Prioritási mátrix token létrehozása](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Prioritásmátrix hozzáadása a gyűjteményből

A Priority Matrix konfigurálásához az Azure AD automatikus felhasználói kiépítéséhez hozzá kell adnia a Priority Matrix-ot az Azure AD alkalmazásgyűjteményéből a felügyelt SaaS-alkalmazások listájához.

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **ablaktábla** tetején található Új alkalmazás gombra.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Prioritásmátrix**kifejezést, és válassza a **Prioritásmátrix** lehetőséget az eredménypanelen. 

    ![Prioritásmátrix az eredménylistában](common/search-new-app.png)

5. Válassza ki a **Regisztráció prioritásmátrixra** gombot, amely átirányítja a Priority Matrix bejelentkezési oldalára. 

    ![Prioritásmátrix OIDC hozzáadása](media/priority-matrix-provisioning-tutorial/signup.png)

6. Mivel a Priority Matrix egy OpenIDConnect alkalmazás, a Microsoft munkahelyi fiókjával jelentkezzen be a Priority Matrix szolgáltatásba.

    ![Prioritásmátrix OIDC bejelentkezés](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Sikeres hitelesítés után fogadja el a hozzájárulási kérést a jóváhagyási oldalon. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Prioritási Mátrix-fiókba.

    ![Prioritásmátrix OIDC hozzájárulása](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Automatikus felhasználói kiépítés konfigurálása az Priority Matrix ba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Priority Matrix ban az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

> [!NOTE]
> Ha többet szeretne megtudni a Priority Matrix SCIM-végpontjáról, olvassa el a [Felhasználói kiépítés és a Prioritásmátrix](https://appfluence.com/help/article/user-provisioning/)című pontot.

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Az Azure AD-ben a Priority Matrix automatikus felhasználói kiépítésének konfigurálása:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Prioritásmátrix lehetőséget.**

    ![Az Alkalmazások listában található Prioritási mátrix hivatkozás](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://sync.appfluence.com/scim/v2/` csoportban adja meg a **bérlői URL-címet.** Adja meg azt az értéket, amelyet korábban beolvasott és mentett a titkos **jogkivonat**prioritásmátrixából. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Prioritási mátrixhoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Prioritási Mátrix fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bérlői URL + jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be a jelölőnégyzetet – **E-mail értesítés küldése hiba esetén.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása prioritásmátrixba**lehetőséget.

    ![Prioritási mátrix felhasználói leképezései](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ről a prioritási mátrixra szinkronizált felhasználói attribútumokat az **Attribútumleképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a prioritási mátrix felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Prioritási mátrix felhasználói attribútumai](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-létesítési szolgáltatás elsőbbségi mátrix engedélyezéséhez módosítsa a **Kiépítési állapot** ot **bekapcsolva** a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Prioritási mátrixba szeretne kiépíteni, ha a **Beállítások** szakasz **hatókörében** kiválasztja a kívánt értékeket.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a prioritási mátrixon végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)


