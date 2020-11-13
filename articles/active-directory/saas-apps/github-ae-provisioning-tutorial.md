---
title: 'Oktatóanyag: a GitHub AE beállítása az automatikus felhasználó-kiépítés Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a GitHub AE-be.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d9818c05-e279-45b4-8aad-0fa156abd74e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2020
ms.author: Zhchia
ms.openlocfilehash: c1a20f35884e03d5fae53e5465e96c275a39131e
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593348"
---
# <a name="tutorial-configure-github-ae-for-automatic-user-provisioning"></a>Oktatóanyag: a GitHub AE konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a GitHub AE és Azure Active Directory (Azure AD) alkalmazásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. A konfigurálást követően az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és/vagy csoportokat az Azure AD-létesítési szolgáltatás használatával a GitHub AE számára. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a GitHub AE-ben
> * Távolítsa el a felhasználókat a GitHub AE-ben, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a GitHub AE között
> * Egyszeri bejelentkezés a [GITHUB AE](https://docs.microsoft.com/azure/active-directory/saas-apps/github-ae-tutorial) -be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* GitHub AE, teljesen [inicializálva](https://docs.github.com/github-ae@latest/admin/configuration/initializing-github-ae) és konfigurálva az [SAML SSO](https://docs.github.com/github-ae@latest/admin/authentication/configuring-authentication-and-provisioning-for-your-enterprise-using-azure-ad) -val való BEJELENTKEZÉShez az Azure ad-bérlőn keresztül.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a GITHUB AE milyen adatleképezést](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)kíván. 

## <a name="step-2-configure-github-ae-to-support-provisioning-with-azure-ad"></a>2. lépés A GitHub AE konfigurálása az Azure AD-vel való kiépítés támogatásához

Megtudhatja, hogyan engedélyezheti a GitHub AE üzembe helyezését [itt](https://docs.github.com/github-ae@latest/admin/authentication/configuring-user-provisioning-for-your-enterprise).

## <a name="step-3-add-github-ae-from-the-azure-ad-application-gallery"></a>3. lépés A GitHub AE hozzáadása az Azure AD Application Galleryből

Vegye fel a GitHub AE-t az Azure AD-alkalmazás-katalógusból a GitHub AE-re való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a GitHub AE-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó és/vagy csoport attribútumai alapján kiépített hatókör kiosztását. Ha azt a hatókört választja, hogy a hozzárendelés alapján ki lesz kiépítve az alkalmazáshoz, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és/vagy csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, aki kizárólag a felhasználó és/vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint hatókör-szűrőt is használhat. 

* Amikor felhasználókat rendel a GitHub AE-hoz, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps). 

* Kezdje kicsiben. Tesztelje a felhasználókat és/vagy csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználók és/vagy csoportok értékre van beállítva, akkor ezt úgy szabályozhatja, hogy egy vagy két felhasználót és/vagy csoportot rendel az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-github-ae"></a>5. lépés A felhasználók automatikus üzembe helyezésének beállítása a GitHub AE-be 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-github-ae-in-azure-ad"></a>A GitHub AE automatikus felhasználói üzembe helyezésének beállítása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **GITHUB AE** elemet.

    ![A GitHub AE-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a GitHub AE **bérlői URL-címét** és a korábban a 2. lépésben beolvasott **titkos jogkivonatot** . Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a GitHub AE-hez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a GitHub AE-fiókja rendelkezik rendszergazdai jogosultságokkal, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása** a **GitHub AE** lehetőséget.

9. Tekintse át az Azure AD-ból a GitHub AE-ra szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a GitHub AE felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a GITHUB AE API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |externalId|Sztring|
   |emails[type eq "work"].value|Sztring|
   |active|Logikai|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |displayName|Sztring|

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a GitHub AE számára, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a GitHub AE számára szeretne kiépíteni a **Beállítások** szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.  

## <a name="additional-resources"></a>További erőforrások

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
