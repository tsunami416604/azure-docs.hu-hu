---
title: 'Oktatóanyag: a munkahelyi konfiguráció konfigurálása a Facebookban a felhasználók automatikus kiépítés Azure Active Directory használatával | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a munkahely között a Facebook használatával.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7d8a7881c00427023e5f174461b3d8b24d83444
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121444"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Oktatóanyag: a munkahelyi környezet konfigurálása a Facebook használatával a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Facebook és Azure Active Directory (Azure AD) munkahelyén kell elvégezni a felhasználók automatikus kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a [Facebook](https://work.workplace.com/) számára az Azure ad kiépítési szolgáltatásával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migrálás az új munkahelyre Facebook-alkalmazással
Ha a Facebook használatával már rendelkezik munkahelyi integrációval, tekintse meg a következő szakaszt a változásokról. Ha a Facebook első indításakor beállítja a munkahelyet, kihagyhatja ezt a szakaszt, és átléphet a támogatott képességekre. 

#### <a name="whats-changing"></a>Mi változik?
* Változások az Azure AD-oldalon: a felhasználók munkahelyi üzembe helyezésének engedélyezési módszere történelmileg hosszú élettartamú titkos token volt. Hamarosan megjelenik az engedélyezési módszer, amely a OAuth engedélyezési engedélyre módosult. 
* Változások a munkahelyi oldalon: korábban az Azure AD-alkalmazás volt a Facebook-beli egyéni integráció a munkahelyen. Most az Azure AD-t a munkahelyi integrációs könyvtárban tekintheti meg harmadik féltől származó alkalmazásként. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Mit kell tennem a meglévő egyéni integráció új alkalmazásba való átállításához?
Ha van meglévő munkahelyi integrációja egy érvényes jogkivonattal, **nincs szükség beavatkozásra**. Minden héten automatikusan áttelepítik az ügyfeleket az új alkalmazásba. Ez teljesen a színfalak mögött történik. Ha nem tud várni, és manuálisan szeretné áthelyezni az új alkalmazást, hozzáadhat egy új munkahely-példányt a katalógusból, és konfigurálhatja újra a kiépítés konfigurálását. A munkahely minden új példánya automatikusan az új alkalmazás verzióját fogja használni. 

 
Ha a munkahelyi integráció karanténban van, akkor újra meg kell adnia egy érvényes tokent ahhoz, hogy áttelepítse Önt. A rendszergazdai hitelesítő adatok szakasz szürkén jelenik meg, de a következőt lehet hozzáfűzni ( **? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride = True**) az URL-címre a hitelesítő adatok újbóli mentéséhez. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Honnan tudhatom meg, hogy az alkalmazás migrálása megtörtént-e? 
Az alkalmazás migrálása után a rendszer eltávolítja az engedélyezési szakaszban található szalagcímet, amely ismerteti a beérkező módosításokat, és a titkos jogkivonat mezőt egy kék Engedélyezés gombra cseréli. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>A rendszergazdai hitelesítő adatok szakasz szürkén jelenik meg az alkalmazáson, és nem lehet menteni. Hogy miért?
Zárolta a meglévő munkahelyi ügyfelek rendszergazdai hitelesítő adatait tartalmazó szakaszát. Ha a bérlőt áttelepítette az új munkahelyi alkalmazásba, akkor újra frissítheti a rendszergazdai hitelesítő adatok szakaszt. Ha nem tud megvárni, a fenti URL-cím használatával szerkesztheti az alkalmazást. 

 
#### <a name="when-will-these-changes-happen"></a>Mikor fognak történni ezek a változások?
A munkahely összes új példánya már az új integrációs/engedélyezési módszert fogja használni. A meglévő integrációk fokozatosan, februárban lesznek áttelepítve. A Migrálás minden bérlő esetében a hónap végéig lesz végrehajtva. 

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a munkahelyen Facebook használatával
> * Felhasználók eltávolítása a munkahelyen a Facebookban, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a munkahely között a Facebook használatával
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) a munkahelyre Facebook használatával (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, Felhőbeli alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda)
* A Facebook egyszeri bejelentkezéses előfizetést használó munkaterülete

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha még nem rendelkezik Azure AD-próbaverzióval, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos próbaverziót.

## <a name="step-1-plan-your-provisioning-deployment"></a>1\. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a munkahely milyen adatleképezést szeretne a Facebook által](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>2\. lépés A munkahelyi környezet konfigurálása a Facebook használatával az Azure AD-vel való kiépítés támogatásához

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok milyen felhasználók számára férhetnek hozzá a munkahelyi Facebook-alkalmazáshoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a munkahelyéhez a Facebook-alkalmazásban:

*   Azt javasoljuk, hogy az üzembe helyezési konfiguráció teszteléséhez egyetlen Azure AD-felhasználó legyen hozzárendelve a munkahelyhez a Facebookban. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

*   Ha a Facebook által a munkahelyhez rendeli a felhasználót, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>3\. lépés Munkaterület felvétele a Facebook használatával az Azure AD Application Galleryből

Az Azure AD-alkalmazás-katalógusban a Facebook használatával is felveheti a munkahelyet, így megkezdheti a Facebook-ban való kiépítés kezelését. Ha korábban már beállította a munkahelyi beállításokat a Facebook által az SSO-hoz, ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4\. lépés Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* A felhasználók és csoportok a Facebookhoz való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **munkahely a Facebook alapján**lehetőséget.

    ![A munkahelyi Facebook-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés**elemre. A rendszer átirányítja a munkahelyre a Facebook engedélyezési oldalán. Adja meg a munkahelyi Facebook-felhasználónevet, majd kattintson a **Continue (folytatás** ) gombra. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad a Facebook segítségével csatlakozhasson a munkahelyhez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a munkahelyi Facebook-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![kiépítési](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![engedélyezik](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a munkahelyhez a Facebook**lehetőséget.

9. Tekintse át az Azure AD-ból a munkahelyre szinkronizált felhasználói attribútumokat a Facebook használatával az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Facebook által a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. Ha úgy dönt, hogy megváltoztatja a [megfelelő célként megadott attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor a Facebook API-nak az adott attribútum alapján történő szűrését támogató munkaterületet kell biztosítania. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName|Sztring|
   |displayName|Sztring|
   |aktív|Logikai|
   |Cím|Logikai|
   |e-mailek [típus eq "work"] .value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |.streetAddress címek [típus eq "work"]|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |.postalCode címek [típus eq "work"]|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |phoneNumbers [típus eq "work"] .value|Sztring|
   |phoneNumbers [típus eq "mobileszköz"] .value|Sztring|
   |phoneNumbers [típus eq "fax"] .value|Sztring|
   |externalId|Sztring|
   |preferredLanguage|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás a Facebook számára történő engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Facebook számára szeretne kiépíteni a munkahelyen a **Beállítások** **szakaszban található kívánt** értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6\. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
*  Ha egy felhasználót nem sikerült létrehozni, és egy "1789003" kóddal rendelkező naplózási esemény van, akkor a felhasználó nem ellenőrzött tartományból származik.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
