---
title: 'Oktatóanyag: a Samanage konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a Samanage felhasználói fiókjait.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060490"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Samanage konfigurálása

Ez az oktatóanyag bemutatja, hogyan kell végrehajtani a Samanage és a Azure Active Directory (Azure AD) az Azure AD konfigurálását a felhasználók és csoportok automatikus kiépítéséhez és kiépítéséhez a Samanage.

> [!NOTE]
> Ez az oktatóanyag egy, az Azure AD-felhasználó kiépítési szolgáltatására épülő összekötőt ismertet. További információ a szolgáltatás működéséről, működéséről és gyakori kérdéseiről: a felhasználók kiépítésének [automatizálása és a szolgáltatott szoftveres (SaaS) alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik a következővel:

* Egy Azure AD-bérlő.
* Egy [Samanage-bérlő](https://www.samanage.com/pricing/) a professzionális csomaggal.
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Samanage-ben.

> [!NOTE]
> Az Azure AD kiépítési integrációja a [Samanage REST API](https://www.samanage.com/api/)-ra támaszkodik. Ez az API Samanage-fejlesztők számára érhető el a professzionális csomaggal rendelkező fiókok számára.

## <a name="add-samanage-from-the-azure-marketplace"></a>Samanage hozzáadása az Azure Marketplace-ről

Mielőtt konfigurálja a Samanage-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, adja hozzá a Samanage-t az Azure piactéren a felügyelt SaaS-alkalmazások listájához.

Ha Samanage szeretne hozzáadni a piactéren, kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory ikon](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Samanage** kifejezést, és válassza az **Samanage** elemet az eredmény panelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás**lehetőséget.

    ![Samanage az eredmények listájában](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Felhasználók Samanage rendelése

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói vagy csoportjai férhetnek hozzá a Samanage. A felhasználók vagy csoportok Samanage való hozzárendeléséhez kövesse a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)című témakör utasításait.

### <a name="important-tips-for-assigning-users-to-samanage"></a>Fontos Tippek a felhasználók Samanage való hozzárendeléséhez

*    Napjainkban a Samanage szerepkörei automatikusan és dinamikusan fel lesznek töltve a Azure Portal felhasználói felületén. Mielőtt Samanage-szerepköröket rendel a felhasználókhoz, győződjön meg arról, hogy a Samanage-bérlő legújabb szerepköreinek beolvasásához a Samanage a kezdeti szinkronizálást hajtja végre.

*    Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen a Samanage-hez a kezdeti automatikus felhasználó-kiépítési konfiguráció teszteléséhez. A tesztek sikerességét követően később további felhasználókat és csoportokat is hozzárendelhet.

*    Amikor Samanage rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen válassza ki a megfelelő alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Automatikus felhasználó-kiépítés beállítása a Samanage

Ez a szakasz végigvezeti az Azure AD kiépítési szolgáltatás konfigurálásának lépésein. Ezzel a beállítással felhasználókat vagy csoportokat hozhat létre, frissíthet és letilthat a Samanage-ben az Azure AD-ben felhasználói vagy csoport-hozzárendelések alapján.

> [!TIP]
> A Samanage SAML-alapú egyszeri bejelentkezés is engedélyezhető. Kövesse az [Samanage egyszeri bejelentkezési oktatóanyag](samanage-tutorial.md)utasításait. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása a Samanage az Azure AD-ben

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások** > **minden alkalmazás** > **Samanage**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Samanage**lehetőséget.

    ![Az Samanage hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Samanage kiépítés](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Samanage **bérlői URL-címét** és a **titkos jogkivonatot**. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Samanage. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Samanage-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Samanage-tesztelési kapcsolatok](./media/samanage-provisioning-tutorial/provisioning.png)

6. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, aki a kiépítési hibákra vonatkozó értesítéseket szeretné kapni. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Samanage értesítő E-mail](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Samanage**lehetőséget.

    ![Samanage-felhasználó szinkronizálása](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelések** szakasz Samanage. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Samanage felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások mentéséhez válassza a **Mentés**lehetőséget.

    ![Samanage egyező felhasználói attribútumok](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. A csoportok leképezésének engedélyezéséhez a **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Samanage**lehetőséget.

    ![Samanage csoport szinkronizálása](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. A csoportok szinkronizálásához **engedélyezze** az **Igen** beállítást. Tekintse át az Azure AD-ből szinkronizált Samanage az attribútumok **leképezései** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Samanage felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások mentéséhez válassza a **Mentés**lehetőséget.

    ![Samanage egyező csoport attribútumai](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. A hatóköri szűrők konfigurálásához kövesse a hatókör- [szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Samanage számára, a **Beállítások** szakaszban módosítsa a **kiépítési állapot** beállítást **a**következőre:.

    ![Samanage kiépítési állapota](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Adja meg a Samanage kiépíteni kívánt felhasználókat vagy csoportokat. A **Beállítások** szakaszban válassza ki a **hatókörben**használni kívánt értékeket. Ha bejelöli a **minden felhasználó és csoport szinkronizálása** lehetőséget, vegye figyelembe a korlátozásokat az "összekötő-korlátozások" című szakaszban leírtak szerint.

    ![Samanage hatóköre](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Ha készen áll a létesítésre, válassza a **Mentés**lehetőséget.

    ![Samanage mentése](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Körülbelül 40 percenként történnek, amíg az Azure AD-kiépítési szolgáltatás fut. 

A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésének hivatkozásait. A jelentés az Azure AD-kiépítési szolgáltatás által a Samanage-on végrehajtott összes műveletet ismerteti.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

Ha a **minden felhasználó és csoport szinkronizálása** lehetőséget választja, és beállítja a Samanage **roles** attribútum értékét, akkor az alapértelmezett érték alatti érték, **Ha a null (nem kötelező)** mezőt a következő formátumban kell megadni:

- {"displayName": "role"}, ahol a szerepkör a kívánt alapértelmezett érték.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
