---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés a Azure Active Directory-mel való automatikus konfigurálása | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058875"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Oktatóanyag: prémium konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa a Bonus és Azure Active Directory (Azure AD) szolgáltatásban elvégzendő lépéseket, hogy az Azure AD konfigurálja a felhasználókat és/vagy csoportokat a bónuszok automatikus kiépítéséhez és kiépítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Azure AD-bérlő
* [Prémium bérlő](https://bonus.ly/pricing)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók

> [!NOTE]
> Az Azure AD kiépítési integrációja a prémium rendelkezésre állású [API](https://konghq.com/solutions/gateway/)-ra támaszkodik, amely a prémium fejlesztők számára érhető el.

## <a name="adding-bonusly-from-the-gallery"></a>Bónuszok hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználói kiépítés előtt bónuszokat kell hozzáadnia az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájára.

**Az Azure AD-alkalmazás-katalógusban található bónuszok hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Bonus**, válassza a **bónuszok** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Bónuszok az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Felhasználók kiosztása bónuszhoz

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában a rendszer csak azokat a felhasználókat és/vagy csoportokat szinkronizálja, akik az Azure AD-alkalmazáshoz lettek rendelve. 

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználóknak és/vagy csoportoknak milyen jogosultságokkal kell rendelkezniük a bónuszok eléréséhez. A döntés megkezdése után a felhasználókat és/vagy csoportokat a következő utasítások követésével lehet bónuszhoz rendelni:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Fontos Tippek a felhasználók bónuszok hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen kiosztva az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha egy felhasználót a bónuszhoz rendel hozzá, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Automatikus felhasználó-kiépítés beállítása a bónuszok megadásához

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a felhasználókat és/vagy csoportokat.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is lehetővé teszi a prémium szintű egyszeri [bejelentkezésre vonatkozó oktatóanyagban](bonus-tutorial.md)foglalt utasítások alapján. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben való Többlettámogatáshoz:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **bónuszok**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **bónuszok**lehetőséget.

    ![Az alkalmazások listájában szereplő prémium hivatkozás](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Bonus-fiók **titkos jogkivonatát** a 6. lépésben leírtak szerint.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. A prémium fiók **titkos jogkivonata** a következő helyen található: **Admin > vállalati > integrációk**. Ha a **kódot szeretné használni** , kattintson az **API-> új API-hozzáférési jogkivonat létrehozása** lehetőségre új titkos jogkivonat létrehozásához.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. A következő képernyőn írja be a hozzáférési jogkivonat nevét a megadott szövegmezőbe, majd nyomja le az API- **kulcs létrehozása**elemet. Az új hozzáférési jogkivonat néhány másodpercig megjelenik egy előugró ablakban.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/Token02.png)

8. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad tud csatlakozni a bónuszokhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a prémium fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba**esetén jelölőnégyzetet.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Kattintson a **Save** (Mentés) gombra.

11. A **leképezések** szakaszban válassza a **Azure Active Directory-felhasználók szinkronizálása bónuszra**lehetőséget.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok használatával a rendszer a frissítési műveletekhez bónuszban lévő felhasználói fiókokat is egyezteti. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Ahhoz, hogy az Azure AD-kiépítési szolgáltatás bónuszok **után is engedélyezve** legyen, módosítsa a **kiépítési állapotot** a **Beállítások** szakaszban.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket ki szeretne próbálni a bónuszok kiválasztásához. Ehhez válassza a **Settings (beállítások** ) szakaszban a kívánt értékeket a **hatókörben** .

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Prémium kiépítés](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységek jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a bónuszok után végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
