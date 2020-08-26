---
title: 'Oktatóanyag: az SAP Analytics-felhő konfigurálása automatikus felhasználó-kiépítés Azure Active Directory használatával | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből az SAP Analytics-felhőbe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2020
ms.author: Zhchia
ms.openlocfilehash: 03764dc9999b233153588f4908a2c10587697e40
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861058"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>Oktatóanyag: SAP Analytics-felhő konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ez az oktatóanyag ismerteti a SAP Analytics-felhőben és Azure Active Directoryban (Azure AD) végrehajtandó lépéseket az automatikus felhasználó-kiépítés konfigurálásához. A konfigurálást követően az Azure AD automatikusan kiépíti a felhasználókat és csoportokat az [SAP Analytics-felhőbe](https://www.sapanalytics.cloud/) az Azure ad-létesítési szolgáltatás használatával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók üzembe helyezésének [automatizálása és az SaaS-alkalmazások kiépítése Azure Active Directory használatával](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az SAP Analytics-felhőben
> * Az SAP Analytics-felhőben lévő felhasználók eltávolítása, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az SAP Analytics-felhő között
> * [Egyszeri bejelentkezés](sapboc-tutorial.md) az SAP Analytics-felhőbe (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* SAP Analytics felhőalapú bérlő
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók az SAP Identity kiépítési felügyeleti konzolon. Győződjön meg arról, hogy rendelkezik hozzáféréssel a proxy rendszerekhez az Identity kiépítési felügyeleti konzolon. Ha nem látja a **proxy rendszerek** csempét, hozzon létre egy incidenst a **BC-iam-IPS** összetevőhöz, hogy hozzáférést kérjen ehhez a csempéhez.
* Egy OAuth-ügyfél, amely az SAP Analytics-felhőben engedélyezte az ügyfél hitelesítő adatait. További információ: a [OAuth-ügyfelek és a megbízható identitás-szolgáltatók kezelése](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés A kiépítési üzembe helyezés megtervezése

1. A kiépítési [szolgáltatás működésének](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)megismerése.
2. Határozza meg, hogy kik lesznek a [kiépítés hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és az SAP Analytics-felhő között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>2. lépés SAP Analytics-felhő konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be az SAP Identity üzembe helyezési [felügyeleti konzolra](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/) a rendszergazdai fiókjával, majd válassza a **proxy rendszerek**elemet.

   ![SAP-proxy rendszerek](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. Válassza ki a **Tulajdonságok** elemet.

   ![SAP-proxy rendszerek tulajdonságai](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. Másolja ki az **URL-címet** , és illessze `/api/v1/scim` be az URL-címre. Mentse ezt később a **bérlői URL-cím** mezőben való használatra.

   ![SAP-proxy rendszerek tulajdonságai URL-címe](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. A [Poster](https://www.postman.com/) használatával HTTPS-hívást hajthat végre a következő címre: `<Token URL>?grant_type=client_credentials` hol `Token URL` található az URL-cím a **OAuth2TokenServiceURL** mezőben. Ez a lépés szükséges ahhoz, hogy létrehozzon egy hozzáférési jogkivonatot, amelyet a titkos jogkivonat mezőben kell használni az automatikus kiépítés konfigurálásakor.

   ![SAP IP-proxy rendszerek tulajdonságai OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. A Poster-ben használja az **egyszerű hitelesítést**, és állítsa be a OAuth ügyfél-azonosítót a felhasználóként és a titkos kulcsot jelszóként. Ez a hívás egy hozzáférési jogkivonatot ad vissza. Ezt a másolást később is megtarthatja a **titkos jogkivonat** mezőben való használathoz.

   ![Post Post kérelem](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>3. lépés SAP Analytics-felhő hozzáadása az Azure AD Application Galleryből

SAP Analytics-felhő hozzáadása az Azure AD-alkalmazás-katalógusból az SAP Analytics-felhőbe való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította az SAP Analytics Cloud for SSO szolgáltatást, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó/csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat és csoportokat rendel az SAP Analytics-felhőhöz, ki kell választania az **alapértelmezett hozzáféréstől**eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kis kezdés. Tesztelje a felhasználókat és a csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>5. lépés Az automatikus felhasználó-kiépítés beállítása az SAP Analytics-felhőbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>Az SAP Analytics-felhő automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**lehetőséget, majd válassza **a minden alkalmazás**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **SAP Analytics-felhő**elemet.

    ![Az SAP Analytics felhőalapú hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **kiépítési módot** **automatikus**értékre.

    ![Kiépítés lap](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a bérlő URL **-címében**korábban LEKÉRT bérlői URL-cím értékét. Adja meg a korábban a **titkos tokenben**lekért hozzáférési jogkivonat értékét. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a InVision. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az SAP Analytics felhőalapú fiókja rendelkezik rendszergazdai jogosultságokkal, és próbálkozzon újra.

    ![kiépítési](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. Az **értesítő e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítő E-mail](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése**lehetőséget.

9. Tekintse át az Azure AD-ból az SAP Analytics-felhőbe szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az SAP Analytics felhő felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), biztosítania kell, hogy az SAP Analytics Cloud API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |név. givenName|Sztring|
   |név. familyName|Sztring|
   |Active|Logikai érték|
   |e-mailek [type EQ "work"]. Value|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Sztring|

10. A hatóköri szűrők konfigurálásához tekintse meg az alábbi utasításokat a [hatókör szűrője oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Az Azure AD-kiépítési szolgáltatás SAP Analytics-felhőben való engedélyezéséhez módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítés állapota bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket az SAP Analytics-felhőbe szeretne kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Kiépítési hatókör](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, kattintson a **Mentés**gombra.

    ![Kiépítési konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Miután konfigurálta az üzembe helyezést, a következő erőforrásokkal figyelheti az üzemelő példányt:

1. A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül
2. Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a Befejezés befejezéséhez.
3. Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a karanténba [helyezett állapotokról](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
