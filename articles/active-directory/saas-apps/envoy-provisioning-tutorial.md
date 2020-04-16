---
title: 'Oktatóanyag: A követ konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és kiépíteni a felhasználói fiókokat az Azure AD-ről az Envoy szolgáltatásba.
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
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 68e17ba1dd5981e565e56d6c8137f77d33ad755b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393494"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Oktatóanyag: A követ konfigurálása az automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti az okat a lépéseket, amelyeket az Envoy és az Azure Active Directoryban (Azure AD) kell végrehajtani az automatikus felhasználói kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan rendelkezik, és de-rendelkezések a felhasználók és csoportok [a megbízott](https://envoy.com/pricing/) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Követben
> * Felhasználók eltávolítása a Követben, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az Envoy között
> * Csoportok és csoporttagságok kiépítése a követben
> * [Egyszeri bejelentkezés a](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-tutorial) követhez (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* [Egy követ bérlője.](https://envoy.com/pricing/)
* Felügyeleti engedélyekkel rendelkező felhasználói fiók az Envoy-ban.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat szeretne leképezni az [Azure AD és a Követ között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-envoy-to-support-provisioning-with-azure-ad"></a>2. lépés Az AD-vel való kiépítés támogatásának konfigurálása

1. Jelentkezzen be a [megbízott ikiszolgálói konzoljára.](https://dashboard.envoy.com/login) Kattintson az **Integrációk gombra.**

    ![Követ integrációk](media/envoy-provisioning-tutorial/envoy01.png)

2. Kattintson a **Telepítés gombra** a **Microsoft Azure SCIM-integrációhoz.**

    ![Követ telepítése](media/envoy-provisioning-tutorial/envoy02.png)

3. Kattintson a **Mentés** **szinkronizálásra az összes felhasználó .** 

    ![Követ mentése](media/envoy-provisioning-tutorial/envoy03.png)

4. Másolja az **OAUTH BEARER TOKEN -t.** Ezt az értéket a **titkos jogkivonat** mezőben adja meg a megbízott alkalmazás azure-portálon létesítési lapján.
    
    ![OAUTH követ](media/envoy-provisioning-tutorial/envoy04.png)

## <a name="step-3-add-envoy-from-the-azure-ad-application-gallery"></a>3. lépés Követ hozzáadása az Azure AD alkalmazásgyűjteményből

Adja hozzá az Azure AD alkalmazáskatalógusból az akta, hogy megkezdje a kiépítést az Envoy számára. Ha korábban már bevan állítva az SSO-hoz, használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendel a Követhez, az Alapértelmezett hozzáférés szolgáltatástól eltérő szerepkört kell **kijelölnie.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-envoy"></a>5. lépés Automatikus felhasználói kiépítés konfigurálása az Envoy számára 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Az automatikus felhasználói kiépítés konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Követ lehetőséget.**

    ![A Követ hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://app.envoy.com/scim/v2` csoportban adja meg a **bérlői URL-címet.** Adja meg az **OAUTH BEARER TOKEN** értéket, amelyet korábban a **Titkos jogkivonatban**olvas fel. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud az Envoy-hoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a megbízottfiókja rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

   ![Kiépítés](./media/envoy-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása követhez**lehetőséget.

9. Tekintse át az Azure AD-ről az **Attribútum-leképezés** szakaszban szinkronizált felhasználói attribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok az Envoy felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy módosítja az [egyező célattribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)gondoskodnia kell arról, hogy az Envoy API támogatja-e a felhasználók szűrését az adott attribútum alapján. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |külső azonosító|Sztring|
   |displayName|Sztring|
   |cím|Sztring|
   |e-mailek[típus eq "work"].value|Sztring|
   |preferredLanguage (elsődleges nyelv)|Sztring|
   |Részleg|Sztring|
   |addresses[type eq "work"].country|Sztring|
   |addresses[type eq "work"].locality|Sztring|
   |addresses[type eq "work"].region|Sztring|
   |addresses[type eq "work"].postalCode|Sztring|
   |addresses[type eq "work"].formatált|Sztring|
   |addresses[type eq "work"].streetAddress|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |name.formatált|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |locale|Sztring|

10. A **Leképezések** csoportban válassza **az Azure Active Directory-csoportok szinkronizálása a követnek**lehetőséget.

11. Tekintse át az Azure AD-ről az **Attribútum-leképezés** szakaszban szinkronizált csoportattribútumokat. Az **Egyező** tulajdonságokként kijelölt attribútumok a csoportokkal egyeztethetők meg a Követben a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |külső azonosító|Sztring|
      |tagok|Referencia|

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-létesítési szolgáltatás engedélyezése a megbízott számára, módosítsa a **kiépítési állapot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a követnek a **beállítások** szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **Hatókör szakaszában** definiált összes felhasználó és csoport kezdeti szinkronizálási **ciklusát.** A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta a kiépítést, a következő erőforrásoksegítségével figyelheti a központi telepítést:

* A [létesítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen vagy sikertelenül kiépítve
* Ellenőrizze a [folyamatjelzőt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) a kiépítési ciklus állapotának és a befejezéshez szükséges közelségnek a megtekintéséhez.
* Ha a létesítési konfiguráció úgy tűnik, hogy sérült állapotban van, az alkalmazás karanténba kerül. A karanténállapotokról itt olvashat [bővebben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../manage-apps/check-status-user-account-provisioning.md)