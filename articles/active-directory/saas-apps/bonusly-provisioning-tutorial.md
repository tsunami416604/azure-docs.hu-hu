---
title: 'Oktatóanyag: Bónusz beállítása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan állíthatja be az Azure Active Directoryt úgy, hogy automatikusan kiépítse és megszüntetje a felhasználói fiókokat a Bonusly szolgáltatásba.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058875"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Oktatóanyag: Bónusz beállítása automatikus felhasználói kiépítéshez

Ez az oktatóanyag célja, hogy bemutassa a Bonusly és az Azure Active Directoryban (Azure AD) végrehajtandó lépéseket, hogy az Azure AD-t úgy konfigurálja, hogy automatikusan kiépítse és dekontizáljon felhasználókat és/vagy csoportokat a Bonusly szolgáltatásba.

> [!NOTE]
> Ez az oktatóanyag az Azure AD felhasználói létesítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Egy Azure AD-bérlő
* [A bónusz bérlő](https://bonus.ly/pricing)
* A Bonusly felhasználói fiókja rendszergazdai engedélyekkel

> [!NOTE]
> Az Azure AD-kiépítési integráció a [Bónusz szolgáltatás API-t,](https://konghq.com/solutions/gateway/)amely elérhető a bónuszfejlesztők számára.

## <a name="adding-bonusly-from-the-gallery"></a>Bónusz hozzáadása a galériából

A Bonusly konfigurálása az Azure AD automatikus felhasználói kiépítéshez, hozzá kell adnia az Azure AD alkalmazásgyűjteményből a felügyelt SaaS-alkalmazások listájához bónuszt.

**Ha az Azure AD alkalmazásgyűjteményből szeretne bónuszt hozzáadni, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Bónusz**, válassza **a Bónusz az** eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Bónusz az eredménylistában](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Felhasználók hozzárendelése a Bónuszhoz

Az Azure Active Directory a "hozzárendelések" nevű koncepciót használja annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak a felhasználók és/vagy csoportok, amelyek "hozzárendelt" egy alkalmazás az Azure AD-ben szinkronizálva vannak. 

Az automatikus felhasználói kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjainak kell hozzáférniük a Bonusly szolgáltatáshoz. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat a Bonusly-hoz rendelheti az alábbi utasításokat követve:

* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Fontos tippek a felhasználók bónuszhoz való hozzárendeléséhez

* Javasoljuk, hogy egyetlen Azure AD-felhasználó van rendelve bonusly az automatikus felhasználói kiépítési konfiguráció teszteléséhez. Később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor egy felhasználót a Bonusly-hoz rendel, a hozzárendelési párbeszédpanelen ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Automatikus felhasználói kiépítés konfigurálása bónuszként

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a Bonusly szolgáltatásban az Azure AD-ben lévő felhasználói és/vagy csoport-hozzárendelések alapján.

> [!TIP]
> Azt is választhatja, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Bonusly-ra, a [Bónusz egyszeri bejelentkezési útmutatóutasításait](bonus-tutorial.md)követve. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>A Bónusz szolgáltatás automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure portalra,](https://portal.azure.com) és válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Bónusz**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Bónusz**lehetőséget.

    ![A Bónusz link az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg bónuszszámlájának **titkos tokent** a 6.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. A bónuszszámlájához szükséges titkos token az Admin > Company > Integrations .the **Secret Token** for your Bonusly account is located in Admin > Company > **Integrations**. Ha **kódolni szeretne,** kattintson az **API-> új API-hozzáférési jogkivonat létrehozása elemre** egy új titkos jogkivonat létrehozásához.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. A következő képernyőn írja be a hozzáférési jogkivonat nevét a megadott szövegmezőbe, majd nyomja le az **Api-kulcs létrehozása gombot.** Az új hozzáférési jogkivonat néhány másodpercig megjelenik egy előugró ablakban.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/Token02.png)

8. Az 5. **Test Connection** Ha a kapcsolat megszakad, győződjön meg róla, hogy a Bónusz fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Kattintson a **Mentés** gombra.

11. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása bónuszba**lehetőséget.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Tekintse át az Azure AD-ből az **Attribútumleképezés** szakaszban a bónuszként szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kiválasztott attribútumok a Bónusz a frissítési műveletek felhasználói fiókjainak egyeztetésére szolgálnak. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

14. Az Azure AD-kiépítési szolgáltatás bónuszok engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** **Beállítások** szakaszban.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket bónuszként szeretne kiépíteni, ha kiválasztja a kívánt értékeket a **Hatókör** területen a **Beállítások** szakaszban.

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Bónuszos kiépítés](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a Beállítások szakasz **hatókörében** definiált összes felhasználó és/vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és kövesse a kiépítési tevékenység jelentésre mutató hivatkozásokat, amely ismerteti az Azure AD-kiépítési szolgáltatás által a Bonusly-on végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat bővebben.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
