---
title: 'Oktatóanyag: Rollbar konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok rollbarba történő automatikus kiépítésére és kiépítésének visszavonására.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d737aa16-8ab4-4c0c-a68b-2911623b41eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 27a26a0c8378f34794afd87cf11b6bb878f7b53c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248447"
---
# <a name="tutorial-configure-rollbar-for-automatic-user-provisioning"></a>Oktatóanyag: A rollbar konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti a rollbar és az Azure Active Directory (Azure AD) automatikus felhasználói kiépítés konfigurálásához szükséges lépéseket. Ha konfigurálva van, az Azure AD automatikusan rendelkezik, és de-rendelkezések a felhasználók és csoportok [Rollbar](https://rollbar.com/pricing/) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Rollbar-ban
> * Felhasználók eltávolítása a Rollbar-ban, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Rollbar között
> * Csoportok és csoporttagságok kiépítése a Rollbar-ban
> * [Egyszeri bejelentkezés a](https://docs.microsoft.com/azure/active-directory/saas-apps/rollbar-tutorial) Rollbarra (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Vállalati csomaggal rendelkező [Rollbar-bérlő.](https://rollbar.com/pricing/)
* A Rollbar felügyeleti engedélyekkel rendelkező felhasználói fiókja.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat kell [leképezni az Azure AD és a Rollbar között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-rollbar-to-support-provisioning-with-azure-ad"></a>2. lépés A Rollbar konfigurálása az Azure AD-vel való kiépítés támogatásához

A Rollbar konfigurálása az Azure AD automatikus felhasználói kiépítés, engedélyeznie kell az SCIM kiépítése a Rollbar.

1. Jelentkezzen be a [Rollbar Felügyeleti konzolba.](https://rollbar.com/login/) Kattintson a **Fiókbeállítások gombra.**

    ![Rollbar felügyeleti konzol](media/rollbar-provisioning-tutorial/image00.png)

2. Nyissa meg a **rollbar-bérlői nevét > identitásszolgáltatót.**

    ![Rollbar identitásszolgáltató](media/rollbar-provisioning-tutorial/idp.png)

3. Görgessen le a **Kiépítés i beállításaihoz.** Másolja a hozzáférési jogkivonatot. Ez az érték a **Titkos jogkivonat** mezőben lesz megadva a Rollbar-alkalmazás azure-beli alkalmazás kiépítési lapján. Jelölje be a **Felhasználó és a csapat kiépítésének engedélyezése** jelölőnégyzetet, és kattintson a Mentés **gombra.**

    ![Rollbar hozzáférési jogkivonat](media/rollbar-provisioning-tutorial/token.png)


## <a name="step-3-add-rollbar-from-the-azure-ad-application-gallery"></a>3. lépés Rollbar hozzáadása az Azure AD alkalmazásgyűjteményéből

Rollbar hozzáadása az Azure AD alkalmazáskatalógusból a rollbar-ra való kiépítés kezeléséhez. Ha korábban már beállította a Rollbar sso-t, használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendel a Rollbarhoz, az Alapértelmezett hozzáférés programtól eltérő szerepkört kell **kijelölnie.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-rollbar"></a>5. lépés Automatikus felhasználói kiépítés konfigurálása a Rollbar alkalmazásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-rollbar-in-azure-ad"></a>A Rollbar automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Rollbar**lehetőséget.

    ![A Görgetősáv hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg a **titkos jogkivonat**korábbi részében beolvasott hozzáférési jogkivonat-értéket. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Rollbarhoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Rollbar-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Kiépítés](./media/rollbar-provisioning-tutorial/admin.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása rollbarra**lehetőséget.

9. Tekintse át az Azure AD-ről rollbarra szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Rollbar felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy módosítja az [egyező célattribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)gondoskodnia kell arról, hogy a Rollbar API támogatja-e a felhasználók szűrését az adott attribútum alapján. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |külső azonosító|Sztring|
   |Aktív|Logikai|
   |name.familyName|Sztring|
   |name.givenName|Sztring|
   |e-mailek[típus eq "munka"]|Sztring|

10. A **Leképezések** csoportban válassza **az Azure Active Directory-csoportok szinkronizálása rollbar-ra**lehetőséget.

11. Tekintse át az Azure AD-ről rollbarra szinkronizált csoportattribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a Rollbar csoportjainak egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |külső azonosító|Sztring|
      |tagok|Referencia|

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás rollbar engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Rollbar-ba a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **Hatókör szakaszában** definiált összes felhasználó és csoport kezdeti szinkronizálási **ciklusát.** A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta a kiépítést, a következő erőforrásoksegítségével figyelheti a központi telepítést:

1. A [létesítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen vagy sikertelenül kiépítve
2. Ellenőrizze a [folyamatjelzőt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) a kiépítési ciklus állapotának és a befejezéshez szükséges közelségnek a megtekintéséhez.
3. Ha a létesítési konfiguráció úgy tűnik, hogy sérült állapotban van, az alkalmazás karanténba kerül. A karanténállapotokról itt olvashat [bővebben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>További források

* [Felhasználói fiókok kiépítési kezeléséa vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../manage-apps/check-status-user-account-provisioning.md)
