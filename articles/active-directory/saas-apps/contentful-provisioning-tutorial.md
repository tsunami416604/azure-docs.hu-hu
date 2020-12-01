---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés megadásának beállítása a Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a tartalomhoz.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: Zhchia
ms.openlocfilehash: 4ff08e51f6e3b2ae72da43052c25046be8bb5397
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352150"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Oktatóanyag: a felhasználók automatikus kiépítési felállításához szükséges tartalom konfigurálása

Ez az oktatóanyag leírja, milyen lépéseket kell végrehajtania a tartalom és a Azure Active Directory (Azure AD) számára az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a [felhasználókat](https://www.contentful.com/) és csoportokat az Azure ad kiépítési szolgáltatásával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása tartalommal
> * Ha már nincs szükség hozzáférésre, távolítsa el a felhasználókat a tartalomból
> * A felhasználói attribútumok szinkronizálása az Azure AD és a tartalmas szolgáltatás között
> * Csoportok és csoporttagságok kiépítése a tartalomból
> * [Egyszeri bejelentkezés](./contentful-tutorial.md) a tartalomra (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy SCIM-kiépítést támogató előfizetéssel rendelkező, tartalommal rendelkező szervezeti fiók. [support@contentful.com](mailto:support@contentful.com)Ha kérdése van a szervezet előfizetésével kapcsolatban, forduljon a tartalomhoz.
 
## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy milyen adatokat kell [leképezni az Azure ad és a tartalommal](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-contentful-to-support-provisioning-with-azure-ad"></a>2. lépés A tartalom konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Hozzon létre egy **szolgáltatás felhasználói** fiókot a tartalomból. Az Azure-hoz tartozó összes kiépítési engedély ezen a fiókon keresztül lesz elérhető. Ajánlott a **tulajdonost** a fiókhoz tartozó szervezeti szerepkörként kiválasztani.

2. Jelentkezzen be a tartalomba az előző lépésben létrehozott **szolgáltatás-felhasználóként** .

3. Navigáljon a **bal oldali csúszka**  ->  **szervezeti beállítások**  ->  **hozzáférési eszközök**  ->  **felhasználó üzembe** helyezése elemre.

    ![Menü](media/contentful-provisioning-tutorial/access.png)

4. Másolja és mentse a **scim URL-címét**. Ez az érték a Azure Portalban a tartalommal rendelkező alkalmazás üzembe helyezés lapján lesz megadva.

5. Kattintson a **személyes hozzáférési jogkivonat előállítása** elemre.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. A modális ablakban adjon egy értelmes nevet a személyes hozzáférési tokennek, és kattintson a "létrehozás" gombra.
    
7. A rendszer létrehoz egy **scim URL-címet** és a **titkos jogkivonatot** . Másolja és mentse ezeket az értékeket. Ezek az értékek bekerülnek a tartalommal rendelkező alkalmazás üzembe helyezés lapján a Azure Portal.

    ![hozzáférés](media/contentful-provisioning-tutorial/token.png)


[support@contentful.com](mailto:support@contentful.com)Ha kérdése van a kiépítés konfigurálásához a felderített felügyeleti konzolon, akkor elérheti.


## <a name="step-3-add-contentful-from-the-azure-ad-application-gallery"></a>3. lépés Tartalom hozzáadása az Azure AD Application Galleryből

A kiépítés a tartalomhoz való felügyeletének megkezdéséhez vegyen fel tartalmat az Azure AD-alkalmazás-katalógusból. Ha korábban már beállította az egyszeri bejelentkezést, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* A felhasználók és csoportok tartalomhoz rendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-contentful"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a tartalomhoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben való tartalomhoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **tartalom** elemet.

    ![Az alkalmazások listában található, a tartalomra mutató hivatkozás](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a tartalommal rendelkező bérlői URL-címet és a titkos jogkivonatot. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen a tartalommal való kapcsolódásra. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a tartalomszolgáltató fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a tartalomhoz** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található tartalomhoz. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt felhasználói fiókok egyeztetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő célként megadott attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a tartalomszolgáltató API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |name.givenName|Sztring|
   |name.familyName|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a tartalommal** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az **attribútum-hozzárendelési** szakaszban található tartalomhoz. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha szeretné engedélyezni az Azure AD-kiépítési szolgáltatást a tartalomhoz, módosítsa a **kiépítési állapotot** **a következőre a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni a tartalom kiépítéséhez, ha a **Settings (beállítások** ) szakaszban a kívánt értékeket választja ki a **hatókörben** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)