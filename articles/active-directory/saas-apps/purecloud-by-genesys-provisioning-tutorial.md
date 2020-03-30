---
title: 'Oktatóanyag: A PureCloud konfigurálása a Genesys által az Azure Active Directoryval való automatikus felhasználói kiépítéshez | Microsoft dokumentumok'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és kiépíteni a felhasználói fiókokat az Azure AD-ről a PureCloudba a Genesys által.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370680"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Oktatóanyag: Konfigurálja a PureCloud-ot a Genesys-től az automatikus felhasználói üzembe építéshez

Ez az oktatóanyag ismerteti azokat a lépéseket, amelyeket a Genesys és az Azure Active Directory (Azure AD) által a PureCloudban kell végrehajtania az automatikus felhasználói kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan rendelkezik a felhasználók és csoportok [PureCloud a Genesys](https://www.genesys.com) az Azure AD-kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekről az Automatikus felhasználói kiépítés és a [SaaS-alkalmazások üzembe helyezésének automatizálása az Azure Active Directoryval.](../manage-apps/user-provisioning.md) 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a PureCloud-ban a Genesys segítségével
> * Távolítsa el a felhasználókat a PureCloud-ban a Genesys-ben, ha már nincs szükségük hozzáférésre
> * Az Azure AD és a PureCloud között a Genesys által szinkronizált felhasználói attribútumok szinkronizálása
> * Csoportok és csoporttagságok kiépítése a PureCloud ban a Genesys által
> * [Egyszeri bejelentkezés a](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) PureCloudba a Genesys által (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Az Azure AD-ben a kiépítés konfigurálására [vonatkozó engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) rendelkező felhasználói fiók (pl. alkalmazásrendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* PureCloud [szervezet.](https://help.mypurecloud.com/?p=81984)
* Oauth-ügyfél létrehozására [engedéllyel](https://help.mypurecloud.com/?p=24360) rendelkező felhasználó.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési telepítés megtervezése
1. További információ [a kiépítési szolgáltatás működéséről.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Határozza meg, hogy ki lesz a [kiépítés hatóköre.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Határozza meg, hogy milyen adatokat szeretne [leképezni az Azure AD és a PureCloud között a Genesys](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)által. 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>2. lépés A PureCloud konfigurálása a Genesys által az Azure AD-vel való kiépítés támogatásához

1. Hozzon létre egy [Oauth klienst](https://help.mypurecloud.com/?p=188023) a PureCloud szervezetben konfigurálva.
2. Hozzon létre egy tokent [az oauth kliensével.](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)
3. Ha automatikusan csoporttagságot szeretne létrehozni a PureCloudon belül, létre kell [hoznia a Csoportokat](https://help.mypurecloud.com/?p=52397) a PureCloudban, amelynek a csoport nevével azonos az Azure AD-ben.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>3. lépés PureCloud hozzáadása a Genesys-től az Azure AD alkalmazásgalériából

Adja hozzá a PureCloud by Genesys-t az Azure AD alkalmazásgalériából, és kezdje el kezelni a Genesys által a PureCloud-ra való kiépítést. Ha korábban beállította a PureCloud by Genesys for SSO-t, használhatja ugyanazt az alkalmazást. Azonban ajánlott, hogy hozzon létre egy külön alkalmazást, amikor az integráció tesztelése kezdetben. További információ az alkalmazások hozzáadásáról a galériából [itt.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD-létesítési szolgáltatás lehetővé teszi, hogy a hatókör, aki ki lesz építve az alkalmazáshoz való hozzárendelés és vagy a felhasználó /csoport attribútumai alapján. Ha úgy dönt, hogy hatókör, aki ki lesz építve az alkalmazás hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) felhasználókat és csoportokat rendelhet az alkalmazáshoz. Ha úgy dönt, hogy a hatókört, aki kilesz építve alapján kizárólag attribútumok a felhasználó vagy csoport, akkor egy hatókörszűrő az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint . 

* Amikor a Genesys hozzárendeli a felhasználókat és csoportokat a PureCloudhoz, ki kell választania az **Alapértelmezett hozzáféréstől eltérő szerepkört.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítésből, és a kiépítési naplókban nem megfelelőként lesznek megjelölve. Ha az alkalmazáson elérhető egyetlen szerepkör az alapértelmezett hozzáférési szerepkör, [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) további szerepkörök hozzáadásához. 

* Kezdjük kicsiben. Tesztelje a felhasználók és csoportok egy kis csoportját, mielőtt mindenki számára kivezetne. Ha a kiépítési hatókör hozzárendelt felhasználókra és csoportokra van beállítva, ezt szabályozhatja egy vagy két felhasználó vagy csoport hozzárendelésével az alkalmazáshoz. Ha a hatókör az összes felhasználóra és csoportra van beállítva, megadhat egy [attribútumalapú hatókörszűrőt.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>5. lépés A Genesys automatikus felhasználói kiépítésének konfigurálása a PureCloud ba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltson le a TestApp-ben az Azure AD felhasználói és/vagy csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>A PureCloud automatikus felhasználói kiépítésének konfigurálása a Genesys számára az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Válassza **a Vállalati alkalmazások**lehetőséget, majd a Minden **alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **PureCloud by Genesys lehetőséget.**

    ![A PureCloud by Genesys hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **létesítési módot** **Automatikus**ra.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban adja meg a PureCloud a Genesys API URL-t és oauth token a **bérlői URL-cím** és a **titkos jogkivonat** mezőket. Az API URL-címe `{{API Url}}/api/v2/scim/v2`a PureCloud [Fejlesztői központpurecloud-régiójának](https://developer.mypurecloud.com/api/rest/index.html)API-URL-címének használatával lesz felépítve. Kattintson **a Kapcsolat tesztelése** elemre, hogy az Azure AD a Genesys által csatlakozhasson a PureCloudhoz. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a PureCloud by Genesys-fiók rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Kiépítés](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Az **Értesítési e-mail mezőbe** írja be annak a személynek vagy csoportnak az e-mail címét, akinek meg kell kapnia a létesítési hibaértesítéseket, és jelölje be az **E-mail értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítési e-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések** szakaszban válassza **az Azure Active Directory-felhasználók szinkronizálása a PureCloud szolgáltatással a Genesys által**lehetőséget.

9. Tekintse át az Azure AD-ről a PureCloud-ra a Genesys által az **Attribútum-leképezés** szakaszban szinkronizált felhasználói attribútumokat. Az **egyező** tulajdonságokként kiválasztott attribútumok a Genesys által a PureCloud felhasználói fiókjainak egyeztetésére szolgálnak frissítési műveletekhez. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)akkor meg kell győződnie arról, hogy a PureCloud a Genesys API támogatja a felhasználók szűrése alapján az adott attribútum. A **módosítások** véglegesítéséhez kattintson a Mentés gombra.

     |Attribútum|Típus|
     |---|---|
     |userName (Felhasználónév)|Sztring|
     |Aktív|Logikai|
     |displayName|Sztring|
     |e-mailek[típus eq "work"].value|Sztring|
     |cím|Sztring|
     |phoneNumbers[type eq "mobile"].value|Sztring|
     |phoneNumbers[type eq "work"].value|Sztring|
     |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:részleg|Sztring|
     |urn:ietf:params:scim:sémák:kiterjesztés:enterprise:2.0:Felhasználó:menedzser|Referencia|

10. A **Leképezések** szakaszban válassza **az Azure Active Directory-csoportok szinkronizálása a PureCloud szolgáltatással a Genesys által**lehetőséget.

11. Tekintse át az Azure AD-ről a PureCloud-ra a Genesys által az **Attribútum-leképezés** szakaszban szinkronizált csoportattribútumokat. Az **egyező** tulajdonságokként kiválasztott attribútumok a Genesys által a PureCloud-ban a frissítési műveletekhez használt csoportokat használják. A **módosítások** véglegesítéséhez kattintson a Mentés gombra. A PureCloud by Genesys nem támogatja a csoport létrehozását vagy törlését, és csak a csoportok frissítését támogatja.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |külső azonosító|Sztring|
      |tagok|Referencia|

12. A hatókörszűrők konfigurálásához olvassa el a [Hatókörszűrő oktatóanyagában](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)található alábbi utasításokat.

13. Az Azure AD-kiépítési szolgáltatás engedélyezéséhez a PureCloud a Genesys, módosítsa a **kiépítés állapota** **be a** **Beállítások** szakaszban.

    ![Kiépítési állapot bevan kapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Genesys által szeretne kiépíteni a PureCloud-ra a **Beállítások** szakasz **hatókörében** a kívánt értékek kiválasztásával.

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
