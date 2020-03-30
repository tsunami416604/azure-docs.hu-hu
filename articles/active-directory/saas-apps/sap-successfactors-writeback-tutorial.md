---
title: 'Oktatóanyag: A SuccessFactors visszaíráskonfigurálása az Azure Active Directoryban | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az attribútum-visszaírást a SuccessFactors szolgáltatáshoz az Azure AD-ből
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060048"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Oktatóanyag: Attribútum-visszaírás konfigurálása az Azure AD-től az SAP SuccessFactors (előzetes verzió) szolgáltatáshoz
Ez az oktatóanyag célja, hogy megjelenítse azokat a lépéseket, amelyeket az Azure AD-től a SuccessFactors Employee Central-ig az attribútumok visszaírásához kell végrehajtania. A visszaíráshoz jelenleg támogatott egyetlen attribútum az e-mail attribútum. 

## <a name="overview"></a>Áttekintés

Miután beállította a bejövő kiépítési integrációt a SuccessFactors használatával [a helyszíni AD-kiépítési](sap-successfactors-inbound-provisioning-tutorial.md) alkalmazásba vagy [a SuccessFactors az Azure AD-kiépítési alkalmazásba,](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) opcionálisan konfigurálhatja a SuccessFactors Writeback alkalmazást, hogy az e-mail címet visszaírja a SuccessFactors-nak. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználói kiépítési megoldás a legalkalmasabb?

Ez a SuccessFactors Writeback felhasználói létesítési megoldás ideális a következőkhöz:

* Az Office 365-öt használó szervezetek, amelyek vissza kívánják írni az IT által kezelt mérvadó attribútumokat (például e-mail-címet) a SuccessFactors-nak

## <a name="configuring-successfactors-for-the-integration"></a>A SuccessFactors konfigurálása az integrációhoz

A SuccessFactors kiépítési összekötők közös követelménye, hogy a SuccessFactors-fiók hitelesítő adataira van szükség a SuccessFactors OData API-k meghívásához megfelelő engedélyekkel. Ez a szakasz a Service-fiók sikerestényezőkben történő létrehozásának lépéseit ismerteti, és megadja a megfelelő engedélyeket. 

* [API felhasználói fiók létrehozása/azonosítása a SuccessFactors ban](#createidentify-api-user-account-in-successfactors)
* [API-engedélyszerepkör létrehozása](#create-an-api-permissions-role)
* [Engedélycsoport létrehozása az API-felhasználó számára](#create-a-permission-group-for-the-api-user)
* [Engedélyszerepkör megadása az engedélycsoportnak](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API felhasználói fiók létrehozása/azonosítása a SuccessFactors ban
Együttműködve a SuccessFactors felügyeleti csapatával vagy a megvalósítási partnerrel, hogy hozzon létre vagy azonosítson egy felhasználói fiókot a SuccessFactors ban, amely et az OData API-k meghívására fog használni. A fiók felhasználónevének és jelszavának hitelesítő adatai szükségesek a kiépítési alkalmazások azure AD-ben történő konfigurálásakor. 

### <a name="create-an-api-permissions-role"></a>API-engedélyszerepkör létrehozása

* Jelentkezzen be az SAP SuccessFactors egy felhasználói fiókkal, amely hozzáfér a Felügyeleti központhoz.
* Keressen *engedélyszerepkörök kezelése ,* majd válassza **az Engedélyszerepkörök kezelése** a keresési eredmények között.
  ![Engedélyszerepkörök kezelése](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Az Engedélyszerepkör-listában kattintson az **Új létrehozása gombra.**
  > [!div class="mx-imgBorder"]
  > ![Új engedélyszerepkör létrehozása](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Adja hozzá az új engedélyszerepkör **szerepkörnevét** és **leírását.** A névnek és a leírásnak azt kell jeleznie, hogy a szerepkör API-használati engedélyekhez kapcsolódik.
  > [!div class="mx-imgBorder"]
  > ![Engedélyszerepkör részletei](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* Az Engedélybeállítások csoportban kattintson az **Engedély... gombra,** majd görgessen lefelé az engedélylistában, és kattintson az **Integrációs eszközök kezelése**parancsra. Jelölje be az **OData API-hoz való hozzáférés engedélyezése egyszerű hitelesítéssel**jelölőnégyzet.
  > [!div class="mx-imgBorder"]
  > ![Integrációs eszközök kezelése](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Görgessen le ugyanabban a mezőben, és válassza **az Employee Central API**lehetőséget. Adja hozzá az engedélyeket az alábbiak szerint az ODATA API használatával történő olvasáshoz és az ODATA API használatával történő szerkesztéshez. Válassza a szerkesztési lehetőséget, ha ugyanazt a fiókot kívánja használni a Visszaértékelés a SuccessFactors forgatókönyvhöz. 
  > [!div class="mx-imgBorder"]
  > ![Írási engedélyek olvasása](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Kattintson a **Kész gombra.** Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-a-permission-group-for-the-api-user"></a>Engedélycsoport létrehozása az API-felhasználó számára

* A SuccessFactors Felügyeleti központban keresse meg az *Engedélycsoportok kezelése*kifejezést, majd válassza az **Engedélycsoportok kezelése lehetőséget** a keresési eredmények között.
  > [!div class="mx-imgBorder"]
  > ![Engedélycsoportok kezelése](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* Az Engedélycsoportok kezelése ablakban kattintson az **Új létrehozása gombra.**
  > [!div class="mx-imgBorder"]
  > ![Új csoport hozzáadása](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Adja hozzá az új csoport nevét. A csoport nevének azt kell jeleznie, hogy a csoport API-felhasználókszámára van.
  > [!div class="mx-imgBorder"]
  > ![Engedélycsoport neve](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Tagok felvétele a csoportba. Válassza például a **Felhasználónevet** a Személyek készlete legördülő menüből, majd adja meg az integrációhoz használt API-fiók felhasználónevét. 
  > [!div class="mx-imgBorder"]
  > ![Csoporttagok hozzáadása](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Az engedélycsoport létrehozásának befejezéséhez kattintson a **Kész** gombra.

### <a name="grant-permission-role-to-the-permission-group"></a>Engedélyszerepkör megadása az engedélycsoportnak

* A SuccessFactors Felügyeleti központban keresse meg az *Engedélyszerepek kezelése kifejezést,* majd válassza az **Engedélyszerepek kezelése lehetőséget** a keresési eredmények között.
* Az **Engedélyszerepkör-listában**válassza ki az API-használati engedélyekhez létrehozott szerepkört.
* A **Szerep megadása csoportban**kattintson a **Hozzáadás...** gombra.
* Válassza az **Engedélycsoport...** lehetőséget a legördülő menüben, majd a **Kijelölés parancsra** a Csoportok ablak megnyitásához és a fent létrehozott csoport kiválasztásához. 
  > [!div class="mx-imgBorder"]
  > ![Engedélycsoport hozzáadása](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Tekintse át az engedélyszerepkör-támogatást az engedélycsoportnak. 
  > [!div class="mx-imgBorder"]
  > ![Engedélyszerepkör és csoport részletei](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Kattintson a **Save Changes** (Módosítások mentése) gombra.

## <a name="configuring-successfactors-writeback"></a>SuccessFactors visszaíráskonfigurálása

Ez a rész a 

* [Adja hozzá a kiépítési összekötő alkalmazást, és konfigurálja a kapcsolatot a SuccessFactors alkalmazáshoz](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Attribútumleképezések konfigurálása](#part-2-configure-attribute-mappings)
* [Felhasználói kiépítés engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1. rész: Adja hozzá a kiépítési összekötő alkalmazást, és konfigurálja a kapcsolatot a SuccessFactors alkalmazáshoz

**A SuccessFactors visszaírásának konfigurálása:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza az **Azure Active Directory lehetőséget.**

3. Válassza **a Vállalati alkalmazások**lehetőséget, majd az Összes **alkalmazás**lehetőséget.

4. Válassza **az Alkalmazás hozzáadása**lehetőséget, majd az **Összes** kategóriát.

5. Keressen a **SuccessFactors Writeback**elemre, és adja hozzá az alkalmazást a gyűjteményből.

6. Az alkalmazás hozzáadása és az alkalmazás részletei képernyő megjelenítése után válassza **a Kiépítés** lehetőséget.

7. A **létesítési** **mód** módosítása **automatikusra**

8. Töltse ki a **Rendszergazdai hitelesítő adatok szakaszt** az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – Adja meg a SuccessFactors API felhasználói fiókjának felhasználónevét a hozzáfűzendő vállalatazonosítóval. Ez a formátum: **felhasználónév\@companyID**

   * **Rendszergazdai jelszó –** Adja meg a SuccessFactors API felhasználói fiók jelszavát. 

   * **Bérlő url-címe –** Adja meg a SuccessFactors OData API-szolgáltatások végpontjának nevét. Csak http vagy https nélküli kiszolgáló állomásnevét adja meg. Ennek az értéknek a következőkre kell kinéznie: **api-server-name.successfactors.com**.

   * **Értesítési e-mail –** Adja meg e-mail címét, és jelölje be az "E-mail küldése hiba esetén" jelölőnégyzetet.
    > [!NOTE]
    > Az Azure AD-kiépítési szolgáltatás e-mail értesítést küld, ha a létesítési feladat [karanténállapotba](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) kerül.

   * Kattintson a **Kapcsolat tesztelése** gombra. Ha a kapcsolatteszt sikeres, kattintson **a** mentés gombra a tetején. Ha nem sikerül, ellenőrizze, hogy a SuccessFactors hitelesítő adatok és URL-cím érvényesek-e.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * A hitelesítő adatok sikeres mentése után a **Leképezések** szakasz megjeleníti az azure **Active Directory-felhasználók szinkronizálásának alapértelmezett szinkronizálását a SuccessFactors programba**

### <a name="part-2-configure-attribute-mappings"></a>2. rész: Attribútumleképezések konfigurálása

Ebben a szakaszban konfigurálhatja, hogy a felhasználói adatok hogyan áramlanak a SuccessFactors szolgáltatásból az Active Directoryba.

1. A **Hozzárendelések**csoport Kiépítés lapján kattintson az **Azure Active Directory felhasználóinak szinkronizálása a SuccessFactors szolgáltatással**elemre.

1. A **Forrásobjektum-hatókör** mezőben kiválaszthatja, hogy az Azure AD-ben mely felhasználók at kell figyelembe venni a visszaíráshoz, attribútumalapú szűrők készletének meghatározásával. Az alapértelmezett hatókör "az Azure AD összes felhasználója". 
   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, tesztelnie és ellenőriznie kell az attribútumleképezéseket és -kifejezéseket, hogy megbizonyosodjon arról, hogy az a kívánt eredményt adja. A Microsoft azt javasolja, hogy a **forrásobjektum-hatókör** hatóköre hatóköre segítségével tesztelje a leképezéseket néhány azure AD-s tesztfelhasználóval. Miután meggyőződött arról, hogy a leképezések működnek, eltávolíthatja a szűrőt, vagy fokozatosan kibonthatja, hogy több felhasználóval is felvegye.

1. A **Célobjektum-műveletek** mező csak a **Frissítés** műveletet támogatja.

1. Az **Attribútum-leképezések** szakaszban csak módosíthatja a megfelelő azonosítót, amely a SuccessFactors felhasználói profil azure AD-felhasználóval való összekapcsolására szolgál, és amely attribútum az Azure AD-ben az e-mailek forrásaként szolgál. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >A SuccessFactors visszaírás csak az e-mail attribútumot támogatja. Új attribútumok hozzáadásához ne használja **az Új leképezés hozzáadása** használatát. 

1. A leképezések mentéséhez kattintson a **Mentés** gombra az Attribútumleképezés szakasz tetején.

Miután az attribútumleképezés konfigurációja befejeződött, most [már engedélyezheti és elindíthatja a felhasználói kiépítési szolgáltatást.](#enable-and-launch-user-provisioning)

## <a name="enable-and-launch-user-provisioning"></a>Felhasználói kiépítés engedélyezése és elindítása

Miután a SuccessFactors kiépítése alkalmazás konfigurációk befejeződött, bekapcsolhatja a kiépítési szolgáltatás az Azure Portalon.

> [!TIP]
> Alapértelmezés szerint, ha bekapcsolja a létesítési szolgáltatás, akkor kezdeményezi a létesítési műveletek et a hatókörben lévő összes felhasználó számára. Ha hibák vannak a leképezési vagy workday-i adatproblémákban, akkor a létesítési feladat sikertelen lehet, és a karantén állapotba kerülhet. Ennek elkerülése érdekében ajánlott eljárásként azt javasoljuk, hogy konfigurálja **a Forrásobjektum-hatókör** szűrőt, és tesztelje az attribútum-leképezéseket néhány tesztfelhasználóval, mielőtt elindítaná a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és a kívánt eredményt adják, eltávolíthatja a szűrőt, vagy fokozatosan kibonthatja, hogy több felhasználót vegyen fel.

1. A **Kiépítés** lapon állítsa a **Kiépítési állapot** ot **Bekapcsolva**.

2. Kattintson a **Mentés** gombra.

3. Ez a művelet elindítja a kezdeti szinkronizálást, amely változó számú órát vehet igénybe attól függően, hogy hány felhasználó van a SuccessFactors bérlőben. Ellenőrizheti a folyamatjelzősávot a szinkronizálási ciklus előrehaladásának nyomon követéséhez. 

4. Bármikor tekintse meg a **naplózási naplók** lapon az Azure Portalon, hogy milyen műveleteket hajtott végre a létesítési szolgáltatás. A naplózási naplók felsorolja a kiépítési szolgáltatás által végrehajtott összes egyes szinkronizálási eseményt, például azt, hogy mely felhasználókat olvassa fel a Workday szolgáltatásból, majd ezt követően hozzáadják vagy frissítik az Active Directoryban. 

5. Miután a kezdeti szinkronizálás befejeződött, egy naplózási összefoglaló jelentést fog írni a **Kiépítés** lapon, az alábbiak szerint.

   > [!div class="mx-imgBorder"]
   > ![Kiépítés folyamatjelző](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>További lépések

* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogy miként konfigurálható az egyszeri bejelentkezés a SuccessFactors és az Azure Active Directory között](successfactors-tutorial.md)
* [További információ arról, hogyan integrálhat más SaaS-alkalmazásokat az Azure Active Directoryval](tutorial-list.md)
* [További információ a kiépítési konfigurációk exportálásáról és importálásáról](../app-provisioning/export-import-provisioning-configuration.md)

