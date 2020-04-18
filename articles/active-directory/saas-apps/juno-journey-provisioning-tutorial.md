---
title: 'Oktatóanyag: A Juno Journey konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és kiépíteni a felhasználói fiókokat az Azure AD-ről a Juno Journey-re.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 08de07a52d1e43dea91e6684d33027d8bcad61fc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641848"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Oktatóanyag: A Juno Journey konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti a Juno Journey és az Azure Active Directory (Azure AD) automatikus felhasználói kiépítés konfigurálásához szükséges lépéseit. Ha konfigurálva van, az Azure AD automatikusan rendelkezik, és de-rendelkezések a felhasználók és csoportok [Juno Journey](https://www.junojourney.com/) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Juno Journey-ben
> * Távolítsa el a felhasználókat a Juno Journey-ből, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Juno Journey között
> * Egyszeri bejelentkezés a Juno [Journey-re](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
*  A [Juno Journey bérlője.](https://www.junojourney.com/getstartedwithjuno)
*  A Juno Journey felhasználói fiókja rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat szeretne leképezni az [Azure AD és a Juno Journey között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>2. lépés A Juno Journey konfigurálása az Azure AD-vel való kiépítés támogatásához

1. A **titkos jogkivonat** és a bérlő support@the-juno.com **URL-címével** kapcsolatban a (Juno Journey) támogatási csapata a címen érhető el. Ez az érték a **Titkos jogkivonat** és a **Bérlő URL-címmezőkben** lesz megadva a Juno Journey alkalmazás Kiépítés lapján az Azure Portalon. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>3. lépés Juno Journey hozzáadása az Azure AD alkalmazásgyűjteményből

Adja hozzá a Juno Journey-t az Azure AD alkalmazásgalériából a Juno Journey kiépítésének megkezdéséhez. Ha korábban már beállította a Juno Journey for SSO-t, használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendel a Juno Journey programhoz, ki kell választania az **Alapértelmezett hozzáférés szerepkört.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>5. lépés Automatikus felhasználói kiépítés konfigurálása a Juno Journey szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>A Juno Journey automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Juno Journey**lehetőséget.

    ![A Juno Journey hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** csoportban adja meg a **bérlői URL-cím**korábbi részében beolvasott bérlői URL-értéket. Adja meg a titkos jogkivonat ot, amelyet korábban beolvastak a **Titkos jogkivonatban.** Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud a Juno Journey szolgáltatáshoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a Juno Journey-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Kiépítés](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory-felhasználók szinkronizálása juno utazással**lehetőséget.

9. Tekintse át az Azure AD és a Juno Journey között szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **egyező** tulajdonságokként kijelölt attribútumok a Juno Journey felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)akkor meg kell győződnie arról, hogy a Juno Journey API támogatja a felhasználók szűrését az adott attribútum alapján. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

   |Változó|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |külső azonosító|Sztring|
   |displayName|Sztring|
   |cím|Sztring|
   |Aktív|Logikai|
   |preferredLanguage (elsődleges nyelv)|Sztring|
   |e-mailek[típus eq "work"].value|Sztring|
   |addresses[type eq "work"].country|Sztring|
   |addresses[type eq "work"].region|Sztring|
   |addresses[type eq "work"].locality|Sztring|
   |addresses[type eq "work"].postalCode|Sztring|
   |addresses[type eq "work"].formatált|Sztring|
   |addresses[type eq "work"].streetAddress|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |name.middleName|Sztring|
   |name.formatált|Sztring|
   |phoneNumbers[type eq "fax"].érték|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:részleg|Sztring|
   |urn:ietf:params:scim:sémák:kiterjesztés:vállalat:2.0:Felhasználó:alkalmazottSzám|Sztring|
   |urn:ietf:params:scim:sémák:kiterjesztés:vállalat:2.0:Felhasználó:costCenter|Sztring|
   urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:osztás|Sztring|
   urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:menedzser|Sztring|
   urn:ietf:params:scim:sémák:kiterjesztés:vállalat:2.0:Felhasználó:szervezet|Sztring|


10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

11. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a Juno Journey, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni a Juno Utazásba a kívánt értékek kiválasztásával a **Beállítások** szakasz **hatókörében.**

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

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
