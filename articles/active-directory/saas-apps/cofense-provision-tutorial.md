---
title: 'Oktatóanyag: a Cofense konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Cofense.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 84fe20ef-0de0-4f7c-9b42-6385f3d834db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2020
ms.author: Zhchia
ms.openlocfilehash: d1ef09e34e44a8a4f39fb5e9c140f138d3da8d86
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761491"
---
# <a name="tutorial-configure-cofense-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Cofense konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Cofense és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat a [Cofense](https://cofense.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Cofense-ben
> * Felhasználók eltávolítása a Cofense-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Cofense között

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy standard operátori fiók a Cofense PhishMe.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése
1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Cofense között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-cofense-to-support-provisioning-with-azure-ad"></a>2. lépés Cofense konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a Cofense PhishMe. Navigáljon a címzettek > a címzett szinkronizálása elemre. 
2. Fogadja el a használati feltételeket, majd kattintson az első lépések elemre.

    ![Kedvezményezett Sync TNC](media/cofense-provisioning-tutorial/recipient-sync-toc.png)

3. Másolja az értékeket az URL-cím és a jogkivonat mezőkből.

    ![Kedvezményezett szinkronizálása](media/cofense-provisioning-tutorial/recipient-sync-getting-started.png)


## <a name="step-3-add-cofense-from-the-azure-ad-application-gallery"></a>3. lépés Cofense hozzáadása az Azure AD Application Galleryből

Vegyen fel Cofense az Azure AD-alkalmazás-katalógusból a Cofense való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a Cofense-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Felhasználók és csoportok Cofense való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-cofense"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Cofense 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy a felhasználók az Azure AD-ben való létrehozására, frissítésére és letiltására Cofense felhasználókat.

### <a name="to-configure-automatic-user-provisioning-for-cofense-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Cofense az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Cofense**lehetőséget.

    ![Az Cofense hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim 2,0 alap URL-címet és a scim hitelesítési jogkivonat** értékét, amely korábban a 2. lépésben beolvasva. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Cofense. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Cofense-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL-token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Cofense**lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő Cofense. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Cofense felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak.  A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |externalId|Sztring|
   |Active|Logikai|
   |displayName|Sztring|
   |név. formázott|Sztring|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |név. honorificSuffix|Sztring|
   |phoneNumbers [type EQ "work"]. Value|Sztring|
   |phoneNumbers [type EQ "Home"]. Value|Sztring|
   |phoneNumbers [type EQ "other"]. Value|Sztring|
   |phoneNumbers [type EQ "pager"]. Value|Sztring|
   |phoneNumbers [type EQ "Mobile"]. Value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |cím|Sztring|
   |e-mailek [type EQ "work"]. Value|Sztring|
   |e-mailek [type EQ "Home"]. Value|Sztring|
   |e-mailek [type EQ "other"]. Value|Sztring|
   |preferredLanguage|Sztring|
   |Becenév|Sztring|
   |userType|Sztring|
   |területi beállítás|Sztring|
   |timezone|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás Cofense való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Cofense kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
