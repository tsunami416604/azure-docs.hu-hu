---
title: 'Oktatóanyag: a Clarizen konfigurálása az automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből egy Clarizen.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 133e12450f01e14f2204810f0d24cd28b5f948f8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795239"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Oktatóanyag: a Clarizen konfigurálása az automatikus felhasználó-kiépítés számára

Ez az oktatóanyag leírja, milyen lépéseket kell végrehajtania a Clarizen egy és Azure Active Directory (Azure AD) az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és a csoportokat, hogy az Azure AD-létesítési szolgáltatással [Clarizen egyet](https://www.clarizen.com/) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Clarizen-ben
> * Felhasználók eltávolítása a Clarizen-ben, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a Clarizen között
> * Csoportok és csoporttagságok kiépítése a Clarizen-ben
> * [Egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/clarizen-tutorial) az Clarizen (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* A Clarizen egyik felhasználói fiókja az **Integration User** és a **Lite rendszergazdai** [engedélyekkel](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)rendelkezik.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Clarizen között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>2. lépés A Clarizen konfigurálása az Azure AD-vel való kiépítés támogatásához
1. Válassza ki a négy alábbi bérlői URL-cím egyikét a Clarizen egy környezet és egy adatközpont szerint:
      * Az USA termelési adatközpontja: https://servicesapp2.clarizen.com/scim/v2
      * Az EU termelési adatközpontja: https://serviceseu1.clarizen.com/scim/v2
      * Az USA-beli sandbox adatközpontja: https://servicesapp.clarizentb.com/scim/v2
      * Az EU sandbox adatközpontja: https://serviceseu.clarizentb.com/scim/v2

2. API- [kulcs](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)létrehozása. Ezt az értéket a Clarizen egyik alkalmazásának a **titkos jogkivonat** mezőjében kell megadni a Azure Portalban.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>3. lépés Clarizen hozzáadása az Azure AD Application Galleryből

Adja hozzá a Clarizen egyet az Azure AD-alkalmazás-katalógusból, hogy megkezdje a Clarizen való kiépítés kezelését. Ha korábban már telepítette a Clarizen az egyszeri bejelentkezéshez, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel hozzá a Clarizen, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>5. lépés Automatikus felhasználó-kiépítés beállítása Clarizen 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben való Clarizen:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza ki a **Clarizen egyet** .

    ![Az Clarizen egy hivatkozást az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Clarizen egy bérlői URL-címet és egy titkos jogkivonatot. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a Clarizen. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Clarizen egy fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók Clarizen egyet** .

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Clarizen. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Clarizen lévő felhasználói fiókok egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), gondoskodnia kell arról, hogy az CLARIZEN egy API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |displayName|Sztring|
   |active|Logikai|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |e-mailek [type EQ "Home"]. Value|Sztring|
   |e-mailek [type EQ "other"]. Value|Sztring|
   |preferredLanguage|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |name.honorificPrefix|Sztring|
   |név. honorificSuffix|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |phoneNumbers [type EQ "Home"]. Value|Sztring|
   |phoneNumbers [type EQ "other"]. Value|Sztring|
   |phoneNumbers [type EQ "pager"]. Value|Sztring|
   |externalId|Sztring|
   |nickName|Sztring|
   |területi beállítás|Sztring|
   |szerepkörök [elsődleges EQ "true". type]|Sztring|
   |szerepkörök [elsődleges EQ "true". Value]|Sztring|
   |timezone|Sztring|
   |userType|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget, hogy Clarizen egyet** .

11. Tekintse át az Azure AD-ból szinkronizált Clarizen, amely az **attribútum-hozzárendelési** szakaszban található. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Clarizen egyikében a frissítési műveletekhez használatosak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD kiépítési szolgáltatás Clarizen való engedélyezéséhez módosítsa a **kiépítési állapotot** a **Beállítások** szakaszban. **On**

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a Clarizen kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.  

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
Ha egy Clarizen egy gyűjtemény-alkalmazáshoz rendel hozzá, válassza a csak **felhasználói** szerepkör lehetőséget. Az alább látható szerepkörök érvénytelenek.

* Adminisztrátor (rendszergazda)
* E-mail jelentéskészítési felhasználó
* Külső felhasználó
* Pénzügyi felhasználó
* Közösségi felhasználó
* Felügyelői
* Az idő & a költségek felhasználója



## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
