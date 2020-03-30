---
title: 'Oktatóanyag: A Samanage konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok samanage-i automatikus kiépítésére és megszüntetésére.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060490"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Oktatóanyag: A Samanage konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag bemutatja a Samanage és az Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és csoportok Samanage-ba történő automatikus kiépítéséhez és a samanage-i szolgáltatások hozadékának kiépítéséhez és megszüntetéséhez.

> [!NOTE]
> Ez az oktatóanyag egy összekötőt, amely az Azure AD felhasználói kiépítési szolgáltatásra épül. A szolgáltatás működéséről, működéséről és a gyakori kérdésekről a [Felhasználói kiépítés automatizálása és a szoftverszolgáltatásként (SaaS) alkalmazások létesítésének automatizálása az Azure Active Directoryval című](../app-provisioning/user-provisioning.md)témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy a következőket ismerteel:

* Egy Azure AD-bérlő.
* Egy [Samanage-bérlő](https://www.samanage.com/pricing/) a Professional csomaggal.
* A Samanage rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

> [!NOTE]
> Az Azure AD-kiépítési integráció a [Samanage Rest API-t](https://www.samanage.com/api/)támogatja. Ez az API a Samanage-fejlesztők számára érhető el a Professional csomaggal rendelkező fiókok hoz.

## <a name="add-samanage-from-the-azure-marketplace"></a>Samanage hozzáadása az Azure Piactérről

Mielőtt konfigurálja a Samanage-t az Azure AD-vel való automatikus felhasználói kiépítéshez, adja hozzá a Samanage-t az Azure Piactérről a felügyelt SaaS-alkalmazások listájához.

Samanage hozzáadása a Piactérről, kövesse az alábbi lépéseket.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali navigációs ablakban válassza az **Azure Active Directory**lehetőséget.

    ![Az Azure Active Directory ikonja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a **Minden alkalmazás**lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget a párbeszédpanel tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Samanage parancsot,** és válassza a **Samanage** lehetőséget az eredménypanelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás**lehetőséget.

    ![Samanage az eredménylistában](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Felhasználók hozzárendelése a Samanage-hoz

Az Azure Active Directory egy *hozzárendelések* nevű koncepciót használ annak meghatározására, hogy mely felhasználók nak kell hozzáférést kapniuk a kiválasztott alkalmazásokhoz. Az automatikus felhasználói kiépítés környezetében csak az Azure AD-ben egy alkalmazáshoz rendelt felhasználók vagy csoportok vannak szinkronizálva.

Az automatikus felhasználói üzembe építés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak vagy csoportjainak kell hozzáférniük a Samanage-hoz. Ha ezeket a felhasználókat vagy csoportokat hozzá szeretné rendelni a Samanage-hoz, kövesse a [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)című útmutató utasításait.

### <a name="important-tips-for-assigning-users-to-samanage"></a>Fontos tippek a felhasználók Samanage-hoz való hozzárendeléséhez

*    Ma a Samanage-szerepkörök automatikusan és dinamikusan töltődnek fel az Azure Portal felhasználói felületén. Mielőtt Samanage szerepköröket rendelne a felhasználókhoz, győződjön meg arról, hogy a Samanage-vel való kezdeti szinkronizálás befejeződött a Samanage-bérlő legújabb szerepkörei beolvasásához.

*    Azt javasoljuk, hogy rendeljen hozzá egy Azure AD-felhasználó Samanage a kezdeti automatikus felhasználói kiépítési konfiguráció teszteléséhez. A tesztek sikeresse után később további felhasználókat és csoportokat rendelhet hozzá.

*    Amikor egy felhasználót hozzárendel a Samanage-hoz, a hozzárendelés párbeszédpanelen jelöljön ki egy érvényes alkalmazásspecifikus szerepkört, ha rendelkezésre áll. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Automatikus felhasználói kiépítés konfigurálása a Samanage szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépéseit. Segítségével hozhat létre, frissíthet és letilthatja a felhasználókat vagy csoportokat a Samanage-ben az Azure AD felhasználói vagy csoport-hozzárendelései alapján.

> [!TIP]
> Saml-alapú egyszeri bejelentkezést is engedélyezhet a Samanage számára. Kövesse a [Samanage egyszeri bejelentkezési oktatóanyag](samanage-tutorial.md)utasításait. Egyszeri bejelentkezés konfigurálható az automatikus felhasználói kiépítéstől függetlenül, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>A Samanage automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások** > minden alkalmazás**Samanage****All applications** > lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Samanage lehetőséget.**

    ![A Samanage hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Samanage kiépítés](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg a Samanage-bérlői **URL-címet** és **a titkos jogkivonatot.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Samanage-hez. Ha a kapcsolat sikertelen, győződjön meg arról, hogy a Samanage-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Samanage tesztkapcsolat](./media/samanage-provisioning-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be a személy vagy csoport e-mail címét a kiépítési hibaértesítések fogadásához. Jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Samanage értesítési e-mail](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása a Samanage szolgáltatásba**lehetőséget.

    ![A felhasználók szinkronizálásának samenkezelése](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Tekintse át az Azure AD-ből az **Attribútumleképezések** szakaszban szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kijelölt attribútumok a Samanage felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Samanage egyező felhasználói attribútumok](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. A csoportleképezések engedélyezéséhez a **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a Samanage szolgáltatással**lehetőséget.

    ![Samanage csoport szinkronizálása](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. A csoportok szinkronizálásához állítsa **az Engedélyezve** **értéket Igen** értékre. Tekintse át az Azure AD-ből az **Attribútumleképezések** szakaszban szinkronizált csoportattribútumokat. Az **egyező** tulajdonságokként kijelölt attribútumok a Samanage felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés gombot.**

    ![Samanage egyező csoportattribútumok](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. A hatókörszűrők konfigurálásához kövesse a [hatókörszűrő oktatóanyagának utasításait.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Az Azure AD-létesítési szolgáltatás Samanage engedélyezéséhez a **Beállítások** szakaszban módosítsa a **Kiépítés állapotát** **Be**állapotra.

    ![Samanage kiépítés állapota](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Határozza meg azokat a felhasználókat vagy csoportokat, amelyeket ki szeretne építeni a Samanage-ba. A **Beállítások csoportban** jelölje ki a kívánt értékeket a **Hatókör területen.** Ha az **Összes felhasználó és csoport szinkronizálása lehetőséget választja,** vegye figyelembe a korlátozásokat a következő szakaszban ismertetett "Összekötő korlátozások" című szakaszban leírtak szerint.

    ![Samanage hatókör](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Ha készen áll a kiépítésre, válassza a **Mentés gombot.**

    ![Samanage Mentés](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Ez a művelet elindítja a Beállítások szakasz Hatókör **szakaszában** definiált összes felhasználó vagy csoport kezdeti **szinkronizálását.** A kezdeti szinkronizálás végrehajtása hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Ezek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

A Szinkronizálás **részletei** szakasz segítségével figyelheti az előrehaladást, és követheti a kiépítési tevékenység jelentésre mutató hivatkozásokat. A jelentés ismerteti az Azure AD-létesítési szolgáltatás samanage-n végrehajtott összes műveletet.

Az Azure AD-kiépítési naplók olvasásáról a [Felhasználói fiókok automatikus kiépítésről szóló jelentéskészítéscímű témakörben](../app-provisioning/check-status-user-account-provisioning.md)olvashat.

## <a name="connector-limitations"></a>Összekötő korlátai

Ha bejelöli az **Összes felhasználó és csoport szinkronizálása** beállítást, és beállít egy értéket a Samanage **szerepkörök** attribútumhoz, az Alapértelmezett érték alatt, **ha null (nem kötelező)** mező ben a következő formátumban kell megadni:

- {"displayName":"role"}, ahol a szerepkör a kívánt alapértelmezett érték.

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
