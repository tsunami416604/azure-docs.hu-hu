---
title: 'Oktatóanyag: felhasználó kiépítés a Slackhez – Azure AD'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok Tartalékidőre való automatikus kiépítéséhez és üzembe helyezéséhez.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b7fa5aea835329be8f65a3bb1775ba5b0d97d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389860"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Oktatóanyag: a tartalékidő konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy megmutassa, milyen lépéseket kell elvégeznie a Slack és az Azure AD-ben, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Azure AD-ből a Slackbe. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Slackben
> * Felhasználók eltávolítása a Slackben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Slack között
> * Csoportok és csoporttagságok kiépítése a Slackben
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) a slackbe (ajánlott)


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő elemekkel:

* [Egy Azure ad-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* Egy Slack-bérlő a [plusz csomaggal](https://aadsyncfabric.slack.com/pricing) vagy a jobb engedélyezéssel.
* Felhasználói fiók a Slackben a csapat rendszergazdai engedélyeivel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Slack között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>2. lépés Slack hozzáadása az Azure AD Application Galleryből

Az Azure AD-alkalmazás-katalógusban a Slack hozzáadásával megkezdheti a kiépítés kezelését. Ha korábban már telepítette a Slack for SSO-t, ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>3. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat és csoportokat rendel a Slackhez, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>4. lépés Az automatikus felhasználó-kiépítés beállítása a Slackre 

Ez a szakasz végigvezeti az Azure AD-nek a Slack felhasználói fiók létesítési API-val való összekapcsolásán, valamint a kiépítési szolgáltatás konfigurálásának beállításán az Azure AD-ben a felhasználó-és csoport-hozzárendelésen alapuló elosztott felhasználói fiókok létrehozásához, frissítéséhez és letiltásához.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>A felhasználói fiókok automatikus üzembe helyezésének beállítása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **tartalékidő**elemet.

    ![A Slack hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés**elemre. Ekkor megnyílik a tartalékidő-engedélyezési párbeszédpanel egy új böngészőablakban.

    ![Engedélyezés](media/slack-provisioning-tutorial/authorization.png)


6. Az új ablakban jelentkezzen be a Slack használatával a csapat rendszergazdai fiókjával. az eredményül kapott hitelesítés párbeszédpanelen válassza ki azt a Slack-csoportot, amely számára engedélyezni szeretné a kiépítés beállítást, majd válassza az **Engedélyezés**lehetőséget. Ha elkészült, térjen vissza a Azure Portal a létesítési konfiguráció befejezéséhez.

    ![Engedélyezési párbeszédpanel](./media/slack-provisioning-tutorial/slackauthorize.png)

7. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a Slack-alkalmazáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Slack-fiókja rendelkezik rendszergazdai jogosultságokkal, majd próbálja megismételni az "engedélyezés" lépést.

8. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A leképezések szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a slackhez**lehetőséget.

11. Az **attribútum-hozzárendelések** szakaszban tekintse át azokat a felhasználói attribútumokat, amelyeket szinkronizálni fog az Azure ad-ből a slackbe. Vegye figyelembe, hogy a **megfeleltetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez a Slack felhasználói fiókjainak megfelelően lesznek felhasználva. A módosítások elvégzéséhez kattintson a Save (Mentés) gombra.

   |Attribútum|Típus|
   |---|---|
   |Active|Logikai|
   |externalId|Sztring|
   |displayName|Sztring|
   |név. familyName|Sztring|
   |név. givenName|Sztring|
   |cím|Sztring|
   |e-mailek [type EQ "work"]. Value|Sztring|
   |userName (Felhasználónév)|Sztring|
   |Becenév|Sztring|
   |címek [type EQ "nem típusos"]. streetAddress|Sztring|
   |címek [type EQ "nem típusos"]. helység|Sztring|
   |címek [típus EQ "nem típusos"]. régió|Sztring|
   |címek [type EQ "nem típusos"]. irányítószám|Sztring|
   |címek [típus EQ "nem típusos"]. ország|Sztring|
   |phoneNumbers [type EQ "Mobile"]. Value|Sztring|
   |phoneNumbers [type EQ "work"]. Value|Sztring|
   |szerepkörök [elsődleges EQ "true"]. Value|Sztring|
   |területi beállítás|Sztring|
   |név. honorificPrefix|Sztring|
   |fényképek [type EQ "Photo"]. Value|Sztring|
   |profileUrl|Sztring|
   |timezone|Sztring|
   |userType|Sztring|
   |urn: scim: sémák: bővítmény: Enterprise: 1.0. Department|Sztring|
   |urn: scim: sémák: bővítmény: Enterprise: 1.0. Manager|Hivatkozás|
   |urn: scim: sémák: bővítmény: Enterprise: 1.0. employeeNumber|Sztring|
   |urn: scim: sémák: bővítmény: Enterprise: 1.0. costCenter|Sztring|
   |urn: scim: sémák: bővítmény: Enterprise: 1.0. szervezet|Sztring|
   |urn: scim: sémák: bővítmény: Enterprise: 1.0. Division|Sztring|

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a slackhez**lehetőséget.

13. Az **attribútum-hozzárendelések** szakaszban tekintse át az Azure ad-ből a slackbe szinkronizálni kívánt csoport attribútumait. Vegye figyelembe, hogy a **megfeleltetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletek során a tartalékidőben lévő csoportoknak megfelelően lesznek felhasználva. A módosítások elvégzéséhez kattintson a Save (Mentés) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |tagok|Hivatkozás|

14. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Slack számára, módosítsa a **kiépítési állapotot** a következőre a **Beállítások** **szakaszban:**

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

16. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni a Slackhez a **Beállítások** szakaszban **lévő kívánt** értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

17. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-5-monitor-your-deployment"></a>5. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Slack **DisplayName** attribútumának konfigurálásakor vegye figyelembe a következő viselkedéseket:

  * Az értékek nem teljesen egyediek (például 2 felhasználó rendelkezhet ugyanazzal a megjelenítendő névvel)

  * Támogatja a nem angol nyelvű karaktereket, szóközöket és nagybetűket. 
  
  * Az engedélyezett írásjelek közé tartoznak az időszakok, az aláhúzások, a kötőjelek, az aposztrófok, a zárójelek (például **([{}])**) és az elválasztók (például: **/;**).
  
  * Csak akkor frissül, ha ez a két beállítás konfigurálva van a Slack munkahelyi/szervezeti **profiljának szinkronizálásához** , és a **felhasználók nem változtathatják meg a megjelenítendő nevüket**.
  
* A Slack **username** attribútumának 21 karakternél rövidebbnek kell lennie, és egyedi értékkel kell rendelkeznie.

* A Slack csak a **felhasználónévvel** és az **e-mail-címmel**való megfeleltetést engedélyezi.  

## <a name="change-log"></a>Változási napló

* 06/16/2020 – a módosított DisplayName attribútum csak az új felhasználó létrehozásakor frissül.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)