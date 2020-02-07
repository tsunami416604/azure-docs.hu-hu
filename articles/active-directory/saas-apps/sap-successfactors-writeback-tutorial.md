---
title: 'Oktatóanyag: a SuccessFactors visszaírási konfigurálása a Azure Active Directoryban | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az visszaírási attribútumokat az Azure AD SuccessFactors
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060048"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Oktatóanyag: az visszaírási attribútum konfigurálása az Azure AD-ből az SAP SuccessFactors (előzetes verzió)
Ennek az oktatóanyagnak a célja, hogy megjelenítse a visszaírási attribútumoknak az Azure AD-ből a SuccessFactors Employee Central-be történő elvégzéséhez szükséges lépéseket. A visszaírási által jelenleg támogatott attribútum az e-mail-attribútum. 

## <a name="overview"></a>Áttekintés

Miután beállította a bejövő üzembe helyezési integrációt [a SuccessFactors-ből a helyszíni ad-](sap-successfactors-inbound-provisioning-tutorial.md) létesítési alkalmazásba vagy a [SUCCESSFACTORS az Azure ad](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) kiépítési alkalmazásba, beállíthatja, hogy a SuccessFactors visszaírási alkalmazás az e-mail-címeket SuccessFactors-re írjon vissza. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználó-kiépítési megoldás a legmegfelelőbb?

Ez a SuccessFactors visszaírási-alapú felhasználói üzembe helyezési megoldás ideális a következőkhöz:

* Az Office 365-et használó szervezetek, amelyek az informatikai részleg (például e-mail-cím) által felügyelt mérvadó visszaírási kívánnak SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Az integráció SuccessFactors konfigurálása

A SuccessFactors-létesítési összekötők általános követelménye, hogy a SuccessFactors OData API-k meghívásához a megfelelő engedélyekkel rendelkező SuccessFactors-fiók hitelesítő adatait igénylik. Ez a szakasz a szolgáltatásfiók SuccessFactors-ben való létrehozásának és a megfelelő engedélyek megadásának lépéseit ismerteti. 

* [API-felhasználói fiók létrehozása/azonosítása a SuccessFactors-ben](#createidentify-api-user-account-in-successfactors)
* [API-engedélyek szerepkör létrehozása](#create-an-api-permissions-role)
* [Engedélyezési csoport létrehozása az API-felhasználó számára](#create-a-permission-group-for-the-api-user)
* [Engedélyezési szerepkör megadása az engedélyezési csoportnak](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-felhasználói fiók létrehozása/azonosítása a SuccessFactors-ben
A SuccessFactors felügyeleti csapatával vagy a megvalósítási partnerrel együttműködve hozzon létre vagy azonosítson egy olyan felhasználói fiókot a SuccessFactors, amelyet a OData API-k meghívásához fog használni. A fiók felhasználónevének és jelszavának hitelesítő adatai a kiépítési alkalmazások Azure AD-ben való konfigurálásakor szükségesek. 

### <a name="create-an-api-permissions-role"></a>API-engedélyek szerepkör létrehozása

* Jelentkezzen be az SAP SuccessFactors egy olyan felhasználói fiókkal, amely hozzáféréssel rendelkezik a felügyeleti központhoz.
* Keressen rá az *engedélyek kezelése*lehetőségre, majd válassza a **jogosultsági szerepkörök kezelése** elemet a keresési eredmények közül.
  ![az engedélyezési szerepkörök kezelése](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Az engedélyezési szerepkör listából válassza az **új létrehozása**lehetőséget.
  > [!div class="mx-imgBorder"]
  > ![új engedélyezési szerepkör létrehozása](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adja hozzá az új engedély szerepkörhöz tartozó **szerepkör nevét** és **leírását** . A név és a Leírás azt jelzi, hogy a szerepkör API-használati engedélyekkel rendelkezik.
  > [!div class="mx-imgBorder"]
  > ![engedélyezési szerepkör részletei](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Az engedély beállításai területen kattintson az **engedély...** elemre, majd görgessen le az engedélyezési listáról, és kattintson az **integrációs eszközök kezelése**lehetőségre. Jelölje be a **rendszergazda számára a ODATA API alapszintű hitelesítéssel való elérésének engedélyezése**jelölőnégyzetet.
  > [!div class="mx-imgBorder"]
  > ![integrációs eszközök kezelése](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Görgessen le ugyanabban a mezőben, és válassza az **Employee Central API**elemet. Az alább látható engedélyek hozzáadásával olvassa el a ODATA API-t és a szerkesztést a ODATA API használatával. Válassza a szerkesztés lehetőséget, ha azt tervezi, hogy ugyanazt a fiókot használja a visszaírási SuccessFactors-forgatókönyvhöz. 
  > [!div class="mx-imgBorder"]
  > Olvasási írási engedély ![](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Kattintson a **kész**gombra. Kattintson a **módosítások mentése**gombra.

### <a name="create-a-permission-group-for-the-api-user"></a>Engedélyezési csoport létrehozása az API-felhasználó számára

* A SuccessFactors felügyeleti központban keressen rá az *engedélyek kezelése csoportra*, majd válassza az **engedélyek kezelése** lehetőséget a keresési eredmények közül.
  > [!div class="mx-imgBorder"]
  > ![az engedélyek kezelése](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* A jogosultsági csoportok kezelése ablakban kattintson az **új létrehozása**elemre.
  > [!div class="mx-imgBorder"]
  > ![új csoport hozzáadása](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Adja hozzá a csoport nevét az új csoporthoz. A csoport nevének azt kell jeleznie, hogy a csoport API-felhasználók számára készült.
  > [!div class="mx-imgBorder"]
  > ![engedély csoportjának neve](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Tagok hozzáadása a csoporthoz. Például kiválaszthatja a **Felhasználónév** elemet a személyek készlet legördülő menüből, majd megadhatja az integrációhoz használni kívánt API-fiók felhasználónevét. 
  > [!div class="mx-imgBorder"]
  > ![Csoporttagok hozzáadása](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Kattintson a **kész** gombra az engedély csoport létrehozásának befejezéséhez.

### <a name="grant-permission-role-to-the-permission-group"></a>Engedélyezési szerepkör megadása az engedélyezési csoportnak

* A SuccessFactors felügyeleti központban keressen rá az *engedélyezési szerepkörök kezelése*lehetőségre, majd válassza az **engedélyezési szerepkörök kezelése** lehetőséget a keresési eredmények közül.
* Az **engedélyezési szerepkör listából**válassza ki az API-használati engedélyekhez létrehozott szerepkört.
* Az adja **meg ezt a szerepkört a következőhöz:...** kattintson a **Hozzáadás...** gombra.
* Válassza az **engedély csoport.** .. lehetőséget a legördülő menüből, majd kattintson a **kiválasztás...** elemre a csoportok ablak megnyitásához, és válassza ki a fenti létrehozott csoportot. 
  > [!div class="mx-imgBorder"]
  > ![engedélyezési csoport hozzáadása](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Tekintse át az engedélyezési szerepkör engedélyezését az engedély csoport számára. 
  > [!div class="mx-imgBorder"]
  > ![engedély szerepkör és a csoport részletei](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Kattintson a **módosítások mentése**gombra.

## <a name="configuring-successfactors-writeback"></a>SuccessFactors-visszaírási konfigurálása

Ez a szakasz a következő lépéseit tartalmazza: 

* [A kiépítési összekötő alkalmazás hozzáadása és a SuccessFactors-kapcsolat konfigurálása](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Attribútumok leképezésének konfigurálása](#part-2-configure-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1\. rész: a kiépítési összekötő alkalmazás hozzáadása és a SuccessFactors-kapcsolat konfigurálása

**SuccessFactors-visszaírási konfigurálása:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza a **Azure Active Directory**

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás**lehetőséget.

4. Válassza az **alkalmazás hozzáadása**lehetőséget, és válassza az **összes** kategóriát.

5. Keresse meg a **SuccessFactors visszaírási**, és adja hozzá az alkalmazást a gyűjteményből.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés** lehetőséget.

7. A **kiépítési** **mód** módosítása **automatikusra**

8. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Rendszergazdai Felhasználónév** – adja meg a SuccessFactors API felhasználói fiókjának felhasználónevét a HOZZÁFŰZÖTT vállalati azonosítóval. Formátuma: **username\@companyID**

   * **Rendszergazdai jelszó –** Adja meg a SuccessFactors API felhasználói fiók jelszavát. 

   * **Bérlői URL-cím –** Adja meg a SuccessFactors OData API Services-végpont nevét. Csak a kiszolgáló állomásnevét adja meg http vagy https nélkül. Ennek az értéknek a következőhöz hasonlóan kell kinéznie: **API-Server-Name.SuccessFactors.com**.

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.
    > [!NOTE]
    > Az Azure AD kiépítési szolgáltatás e-mailes értesítést küld, ha a kiépítési feladatok [karantén](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) állapotba kerülnek.

   * Kattintson a **kapcsolatok tesztelése** gombra. Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy a SuccessFactors hitelesítő adatok és az URL-cím érvényes-e.
    >[!div class="mx-imgBorder"]
    >![Azure Portalra](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Miután a hitelesítő adatok mentése sikeresen megtörtént, a **leképezések** szakasz megjeleníti az alapértelmezett leképezések **szinkronizálását Azure Active Directory felhasználókat a SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>2\. rész: attribútumok leképezésének konfigurálása

Ebben a szakaszban azt fogja konfigurálni, hogy a felhasználói adatok hogyan áramlanak a SuccessFactors a Active Directoryba.

1. A kiépítés lapon a **leképezések**területen kattintson a **szinkronizálás Azure Active Directory felhasználók SuccessFactors**elemre.

1. A **forrás objektum hatóköre** mezőben kiválaszthatja, hogy az Azure ad mely felhasználói készleteit kell figyelembe venni a visszaírási számára az attribútumok alapján megadott szűrők definiálásával. Az alapértelmezett hatókör a "minden felhasználó az Azure AD-ben". 
   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, meg kell vizsgálnia és ellenőriznie kell az attribútumok hozzárendeléseit és kifejezéseit, hogy biztosan megadja a kívánt eredményt. A Microsoft azt javasolja, hogy a **forrás objektum hatókörében** lévő hatókör-szűrők használatával tesztelje a leképezéseket néhány, az Azure ad-ből származó tesztelési felhasználóval. Miután meggyőződött arról, hogy a leképezések működnek, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

1. A **cél objektum műveletek** mező csak a **frissítési** műveletet támogatja.

1. Az **attribútum-hozzárendelések** szakaszban csak a SuccessFactors felhasználói profilnak az Azure ad-felhasználóval való összekapcsolásához és az Azure ad-ben az e-mailek forrásaként szolgáló attribútumhoz tartozó azonosítót lehet módosítani. 
    >[!div class="mx-imgBorder"]
    >![Azure Portalra](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >A SuccessFactors visszaírási csak az e-mail-attribútumot támogatja. Új attribútumok hozzáadásához ne használja az **Új leképezés hozzáadása** lehetőséget. 

1. A leképezések mentéséhez kattintson a **Save (Mentés** ) gombra az attribútum-leképezési szakasz tetején.

Miután az attribútum-hozzárendelési konfiguráció elkészült, mostantól [engedélyezheti és elindíthatja a felhasználó kiépítési szolgáltatását](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>A felhasználók üzembe helyezésének engedélyezése és elindítása

Miután befejezte az SuccessFactors-létesítési alkalmazás konfigurációját, bekapcsolhatja a kiépítési szolgáltatást a Azure Portal.

> [!TIP]
> Alapértelmezés szerint a kiépítési szolgáltatás bekapcsolásakor a rendszer kiépítési műveleteket kezdeményez a hatókörben lévő összes felhasználó számára. Ha hibák léptek fel a leképezési vagy a munkanapokon tárolt adatokkal kapcsolatban, a kiépítési feladat meghiúsulhat, és a karanténba helyezési állapotba kerülhet. Ennek elkerüléséhez ajánlott eljárásként Azt javasoljuk, hogy a **forrás objektum hatókör** -szűrőjét konfigurálja, és tesztelje az attribútumok hozzárendeléseit néhány tesztelési felhasználóval, mielőtt elindítja a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és megadja a kívánt eredményeket, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

1. A **létesítés** lapon állítsa be a **kiépítési állapotot** **a**következőre:.

2. Kattintson a **Save** (Mentés) gombra.

3. Ez a művelet elindítja a kezdeti szinkronizálást, amely a SuccessFactors-bérlőben lévő felhasználók számától függően több órát is igénybe vehet. A folyamatjelző sáv a szinkronizálási ciklus előrehaladásának nyomon követésére használható. 

4. A Azure Portal **naplók** lapján bármikor megtekintheti a kiépítési szolgáltatás által végrehajtott műveleteket. A naplók a kiépítési szolgáltatás által végrehajtott összes egyéni szinkronizálási eseményt felsorolják, például hogy mely felhasználók olvasnak el munkanapokon, majd ezt követően hozzáadják vagy frissítették Active Directory. 

5. A kezdeti szinkronizálás befejezésekor a rendszer egy naplózási összesítő jelentést ír a **létesítés** lapon az alább látható módon.

   > [!div class="mx-imgBorder"]
   > ![kiépítési folyamatjelző sáv](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Következő lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a SuccessFactors és a Azure Active Directory között](successfactors-tutorial.md)
* [Ismerje meg, hogyan integrálhat más SaaS-alkalmazásokat a Azure Active Directory](tutorial-list.md)
* [Útmutató a kiépítési konfigurációk exportálásához és importálásához](../app-provisioning/export-import-provisioning-configuration.md)

