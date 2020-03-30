---
title: 'Oktatóanyag: Az Insight4GRC konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan építhet idát ki és vonhat vissza automatikusan a felhasználói fiókok azure AD-ről az Insight4GRC-re.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0eab8a0-571b-4609-96b1-bdbc761a25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2020
ms.author: Zhchia
ms.openlocfilehash: 1404854e054c8fc4967ba863486969b8a87db526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621516"
---
# <a name="tutorial-configure-insight4grc-for-automatic-user-provisioning"></a>Oktatóanyag: Az Insight4GRC konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti az Insight4GRC és az Azure Active Directory (Azure AD) automatikus felhasználói kiépítés konfigurálásához szükséges lépéseit. Ha konfigurálva van, az Azure AD automatikusan rendelkezik, és de-rendelkezések a felhasználók és csoportok [Insight4GRC](https://www.rsmuk.com/) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Insight4GRC-ben
> * Felhasználók eltávolítása az Insight4GRC-ben, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az Insight4GRC között
> * Csoportok és csoporttagságok kiépítése az Insight4GRC-ben
> * [Egyszeri bejelentkezés az](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial) Insight4GRC-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Az Insight4GRC rendszergazdai engedélyekkel rendelkező felhasználói fiókja.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat szeretne leképezni az [Azure AD és az Insight4GRC között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-insight4grc-to-support-provisioning-with-azure-ad"></a>2. lépés Az Insight4GRC konfigurálása az Azure AD-vel való kiépítés támogatásához

Az Insight4GRC konfigurálása az Azure AD automatikus felhasználói kiépítéséhez, engedélyeznie kell az SCIM-kiépítést az Insight4GRC-en.

1. A tulajdonosi jogkivonat beszerzéséhez a végfelhasználónak kapcsolatba kell lépnie [az ügyfélszolgálattal.](mailto:support.ss@rsmuk.com)
2. Az SCIM-végpont URL-címének beszerzéséhez készen kell készítenie az Insight4GRC tartománynevet, mivel az scim-végpont URL-címének létrehozásához használható. Az Insight4GRC tartománynevét az Insight4GRC kezdeti szoftvervásárlásának részeként kérheti le.

## <a name="step-3-add-insight4grc-from-the-azure-ad-application-gallery"></a>3. lépés Insight4GRC hozzáadása az Azure AD alkalmazásgyűjteményből

Adja hozzá az Insight4GRC-et az Azure AD alkalmazásgalériából az Insight4GRC kiépítésének megkezdéséhez. Ha korábban már beállítható Insight4GRC az SSO használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendel az Insight4GRC-hez, ki kell választania az **Alapértelmezett hozzáférés szerepkört.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-insight4grc"></a>5. lépés Automatikus felhasználói kiépítés konfigurálása az Insight4GRC alkalmazásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc-in-azure-ad"></a>Az Insight4GRC automatikus felhasználói kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Insight4GRC**lehetőséget.

    ![Az Insight4GRC hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg az SCIM végpont URL-címét a **bérlői URL-címbe.** Az enpoint URL-címének `https://<Insight4GRC Domain Name>.insight4grc.com/public/api/scim/v2 ` abban a formátumban kell lennie, amelyben az **Insight4GRC tartománynév** az előző lépésekben beolvasott érték. Adja meg a titkos **jogkivonat**korábbi beolvasása kor beolvasott tulajdonosi token értékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD csatlakozni tud az Insight4GRC-hez. Ha a kapcsolat sikertelen, győződjön meg arról, hogy az Insight4GRC-fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Kiépítés](./media/insight4grc-provisioning-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** csoportban válassza **az Azure Active Directory felhasználóinak szinkronizálása insight4GRC-vel**lehetőséget.

9. Tekintse át az Azure AD és az Insight4GRC között szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok az Insight4GRC felhasználói fiókjainak egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy módosítja az [egyező célattribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)gondoskodnia kell arról, hogy az Insight4GRC API támogatja-e a felhasználók szűrését az adott attribútum alapján. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |külső azonosító|Sztring|
   |Aktív|Logikai|
   |cím|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |e-mailek[típus eq "work"].value|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása az Insight4GRC szolgáltatással**lehetőséget.

11. Tekintse át az Azure AD és az Insight4GRC között szinkronizált csoportattribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok az Insight4GRC csoportjaival egyeztethetők a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |külső azonosító|Sztring|
      |tagok|Referencia|

10. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Insight4GRC Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **Kiépítés állapotát** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az Insight4GRC-be a **Beállítások** szakasz **Hatókör** területén a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés gombra.**

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a Beállítások szakasz **Hatókör szakaszában** definiált összes felhasználó és csoport kezdeti szinkronizálási **ciklusát.** A kezdeti ciklus végrehajtása hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek körülbelül 40 percenként fordulnak elő, amíg az Azure AD-kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta a kiépítést, a következő erőforrásoksegítségével figyelheti a központi telepítést:

* A [létesítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók kiépített sikeresen vagy sikertelenül.
* Ellenőrizze a [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) a kiépítési ciklus állapotát és a befejezéshez szükséges közelséget.
* Ha a létesítési konfiguráció úgy tűnik, hogy sérült állapotban van, az alkalmazás karanténba kerül. A karanténállapotokról itt olvashat [bővebben.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókkiépítési kezelésének kezelése.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések becsatornázásáról.](../app-provisioning/check-status-user-account-provisioning.md)
