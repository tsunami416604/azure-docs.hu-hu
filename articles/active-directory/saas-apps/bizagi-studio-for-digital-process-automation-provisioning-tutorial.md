---
title: 'Oktatóanyag: a bizarr Studio konfigurálása a digitális folyamatok automatizálásához az automatikus felhasználó-kiépítés Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a bizarr studióba a digitális folyamatok automatizálásához.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: d177931429642436ceccae9c9051106ba5880ada
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180302"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Oktatóanyag: a bizarr Studio konfigurálása a digitális folyamatok automatizálásához a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a bizarr Studióban kell elvégezni a digitális folyamatok automatizálásához és Azure Active Directory (Azure AD) az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a bizarr Studio-hoz a [digitális folyamatok automatizálásához](https://www.bizagi.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a bizarr Studióban a digitális folyamatok automatizálásához
> * A bizarr Studióban lévő felhasználók eltávolítása a digitális folyamatok automatizálásához, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a bizarr Studio között a digitális folyamatok automatizálása érdekében
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) a bizarr studióba a digitális folyamatok automatizálásához (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* A bizarr Studio for digitális Process Automation 11.2.4.2 X vagy újabb verziója.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a bizarr Studio milyen adatleképezést biztosít a digitális folyamatok automatizálásához](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-bizagi-studio-for-digital-process-automation-to-support-provisioning-with-azure-ad"></a>2. lépés A bizarr Studio konfigurálása a digitális folyamatok automatizálásához az Azure AD-vel való kiépítés támogatásához


1. Jelentkezzen be a munkahelyi portálra **rendszergazdai engedélyekkel** rendelkező felhasználóként.

2. Navigáljon a **rendszergazda – > biztonság – > OAuth 2 alkalmazásra**.

   ![beállítások](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Kattintson a Hozzáadás gombra.
4. Válasszon **tulajdonosi jogkivonatot** a Grant típusban. Válassza az **API** és a **felhasználói szinkronizálás** lehetőséget az engedélyezett hatókörben, majd kattintson a Mentés gombra.

   ![api](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Másolja ki és mentse az **ügyfél titkos kulcsát**. Ez az érték a bizarr Studio for digitális Process Automation-alkalmazás létesítés lapjának **titkos jogkivonat** mezőjében jelenik meg a Azure Portal.

   ![jogkivonat](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="step-3-add-bizagi-studio-for-digital-process-automation-from-the-azure-ad-application-gallery"></a>3. lépés A bizarr Studio hozzáadása a digitális folyamatok automatizálásához az Azure AD Application Galleryből

A bizarr Studio for digitális folyamatok automatizálása az Azure AD-alkalmazás-galériából a bizarr Studio for digitális folyamatok automatizálásához való kiépítés kezelésének megkezdéséhez. Ha korábban már telepítette a bizarr Studio alkalmazást az egyszeri bejelentkezéshez használt digitális folyamatok automatizálásához, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a bizarr studióhoz a digitális folyamatok automatizálásához, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-bizagi-studio-for-digital-process-automation"></a>5. lépés Az automatikus felhasználó-kiépítés konfigurálása a bizarr studióhoz a digitális folyamatok automatizálásához 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Az Azure AD-ben a digitális folyamatok automatizálására szolgáló bizarr Studio automatikus felhasználó-kiépítési beállítása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **bizarr Studio lehetőséget a digitális folyamatok automatizálásához**.

    ![A bizarr Studio for Digital Process Automation hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a bizarr Studio for Digital Process Automation-bérlő URL-címét és a titkos tokent. 

      * **Bérlői URL-cím:** Adja meg a bizarr SCIM végpontot a következő struktúrával: <Your_Bizagi_Project>/scim/v2/példa: `https://my-company.bizagi.com/scim/v2/`

      * **Titkos jogkivonat:** Ez az érték a 2. lépésben lekéri a korábbi értéket.

      Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a bizarr studióhoz a digitális folyamatok automatizálásához. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a bizarr Studio for digitális Process Automation-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

   ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a bizarr Studio digitális folyamatok automatizálásához** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat a digitális folyamatok automatizálására szolgáló bizarr az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a bizarr Studio felhasználói fiókjainak egyeztetésére használhatók a frissítési műveletekhez a digitális folyamatok automatizálásához. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), gondoskodnia kell arról, hogy a bizarr Studio for digitális Process Automation API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   
10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a bizarr Studio számára a digitális folyamatok automatizálásához, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a bizarr Studio for digitális folyamatok automatizálásához szeretne kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
