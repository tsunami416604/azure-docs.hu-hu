---
title: 'Oktatóanyag: Új ereklye(Korlátozott kiadás) konfigurálása az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Megtudhatja, hogyan lehet automatikusan kiépíteni és kiépíteni a felhasználói fiókokat az Azure AD-ről az Új ereklye(Korlátozott kiadás) szolgáltatásba.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 7fd0d976-4f70-4ce5-992e-3ea4ed8e5d60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: 363b50a4835ead52b3a3a85978d4ea8210add7b2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727345"
---
# <a name="tutorial-configure-new-reliclimited-release-for-automatic-user-provisioning"></a>Oktatóanyag: Új ereklye(Korlátozott kiadás) konfigurálása automatikus felhasználói kiépítéshez

Ez az oktatóanyag ismerteti az új relic(limited release) és az Azure Active Directory (Azure AD) automatikus felhasználói kiépítés konfigurálásához szükséges lépéseket. Ha konfigurálva van, az Azure AD automatikusan rendelkezik, és de-rendelkezések a felhasználók és csoportok [új ereklye](https://newrelic.com/) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Új ereklyében(Korlátozott kiadás)
> * Felhasználók eltávolítása a New Relic(Limited Release) programban, ha már nincs szükségük hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az Új ereklye(Korlátozott kiadás) között
> * Csoportok és csoporttagságok kiépítése az Új ereklye(Korlátozott kiadás) szolgáltatásban
> * [Egyszeri bejelentkezés a](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) New Relic(Limited Release) alkalmazásba (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Egy vagy több fiók a New Relic(Limited Release) oldalon, amelyhez a felhasználóknak hozzá kell férniük. 

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat kell [leképezni az Azure AD és az Új Erek (Korlátozott kiadás) között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-new-reliclimited-release-to-support-provisioning-with-azure-ad"></a>2. lépés Új relikvia(korlátozott kiadás) konfigurálása az Azure AD-vel való kiépítés támogatására

A fiók képviselőjével együttműködve, vagy a support.newrelic.com-nál kaphat támogatást az SCIM és az SSO konfigurálásához a szervezetszámára. Meg kell adnia a fiók képviselőjének a következőket:

- A szervezet neve
- A szervezethez társítható új reliklic fiókazonosítók listája

Ezzel az információval a fiók képviselője létrehoz egy szervezeti rekordot az Új rendszerünkben, és társítja fiókjait a szervezethez.

A fiók képviselője a következő információkat adja meg, amelyeket be kell állítania az új relic SCIM/SSO alkalmazásnak az identitásszolgáltatójához:

- SCIM-végpont (bérlői URL)
- SCIM-tulajdonosi token (titkos token)

Az SCIM-tulajdonosi token lehetővé teszi a felhasználók üzembe létesítését a New Relic, ezért kérjük, tartsa az értéket biztonságban. A fiók képviselője biztonságos módon továbbítja Önnek az SCIM-tulajdonosi tokent.

## <a name="step-3-add-new-reliclimited-release-from-the-azure-ad-application-gallery"></a>3. lépés Új relic(korlátozott kiadás) hozzáadása az Azure AD alkalmazásgyűjteményéből

Új relic(Limited Release) hozzáadása az Azure AD alkalmazáskatalógusból az új relic(korlátozott kiadás) kiépítés kezelésének megkezdéséhez. Ha korábban már beállította az SSO új relic(limited release) beállítását, használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor felhasználókat és csoportokat rendel az Új relic(Korlátozott kiadás) értékhez, az Alapértelmezett hozzáférés beállítástól eltérő szerepkört kell **választania.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-new-reliclimited-release"></a>5. lépés Automatikus felhasználói kiépítés konfigurálása az Új ereklye(Korlátozott kiadás) szolgáltatásba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-new-reliclimited-release-in-azure-ad"></a>Az Új relic(Korlátozott kiadás) automatikus felhasználói kiépítéskonfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Új ereklye(Korlátozott kiadás)** lehetőséget.

    ![Az Új ereklye hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő** adatok `https://scim-provisioning.service.newrelic.com/scim/v2` szakaszban adja meg a bérlői URL-címet. Adja meg a **titkos jogkivonat**korábbi, beolvasott SCIM hitelesítési tokenértékét. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD képes legyen csatlakozni az új ereklyéhez. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy az új relic fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Kiépítés](./media/new-relic-limited-release-provisioning-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** szakaszban válassza **az Azure Active Directory felhasználóinak szinkronizálása új rellic(Limited Release)** lehetőséget.

9. Tekintse át az Azure AD-ről az Új relic(Limited Release) között szinkronizált felhasználói attribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok a frissítési műveletek új relic(korlátozott kiadás) felhasználói fiókjainak egyeztetésére szolgálnak. Ha úgy dönt, hogy módosítja az [egyező célattribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)gondoskodnia kell arról, hogy az Új relic(Limited Release) API támogatja-e a felhasználók szűrését az adott attribútum alapján. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |külső azonosító|Sztring|
   |Aktív|Logikai|
   |e-mailek[típus eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.formatált|Sztring|
   |Timezone|Sztring|

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása új rellic(korlátozott kiadás) lehetőséget.**

11. Tekintse át az Azure AD-ről az Új relic(Limited Release) kapcsolatra szinkronizált csoportattribútumokat az **Attribútum-leképezés** szakaszban. Az **Egyező** tulajdonságokként kijelölt attribútumok az Új relic(Korlátozott kiadás) csoportban lévő csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |külső azonosító|Sztring|
      |tagok|Referencia|

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez az új relic(korlátozott kiadás) beállításához módosítsa a **Kiépítési állapotot** **bekapcsolva** értékre a **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket ki szeretne építeni az Új relic(Limited Release) alkalmazásba a **Beállítások** szakasz **Hatókör** területén a kívánt értékek kiválasztásával.

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
