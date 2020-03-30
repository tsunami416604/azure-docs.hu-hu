---
title: 'Oktatóanyag: A SuccessFactors bejövő kiépítéskonfigurálása az Azure Active Directoryban | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja a bejövő kiépítést a SuccessFactors és az Azure AD között
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: fac4f61e-d942-4429-a298-9ba74db95077
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 09501a80d6ddcbbc9fa6cc08e36f47beb13d1663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063222"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Oktatóanyag: Sap SuccessFactors konfigurálása az Azure AD-felhasználók kiépítéséhez (előzetes verzió)
Ez az oktatóanyag célja, hogy megjelenítse azokat a lépéseket, amelyeket el kell végeznie a dolgozói adatok nak a SuccessFactors Employee Central-ból az Azure Active Directoryba történő kiépítéséhez, az e-mail cím nem kötelező visszaírásával a SuccessFactors számára. Ez az integráció nyilvános előzetes verzióban érhető el, és több mint [70 felhasználói attribútum](../app-provisioning/sap-successfactors-attribute-reference.md) beolvasását támogatja a SuccessFactors Employee Central alkalmazástól. 

>[!NOTE]
>Akkor használja ezt az oktatóanyagot, ha a SuccessFactors alkalmazásból kiépíteni kívánt felhasználók csak felhőalapú felhasználók, akiknek nincs szükségük helyszíni AD-fiókra. Ha a felhasználók csak a helyszíni AD-fiók vagy az AD és az Azure AD-fiók, majd olvassa el az [oktatóanyag-ra konfigurálni SAP SuccessFactors az Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview) felhasználói kiépítés. 

## <a name="overview"></a>Áttekintés

Az [Azure Active Directory felhasználói kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) integrálja a [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) a felhasználók identitáséletciklusának kezelése érdekében. 

Az Azure AD felhasználói létesítési szolgáltatás által támogatott SuccessFactors felhasználói kiépítési munkafolyamatok lehetővé teszik a következő emberi erőforrások és identitáséletciklus-kezelési forgatókönyvek automatizálását:

* **Új alkalmazottak felvétele** – Ha új alkalmazottat ad hozzá a SuccessFactors szolgáltatáshoz, a rendszer automatikusan létrehoz egy felhasználói fiókot az Azure Active Directoryban, és adott esetben az Office 365-ben és [az Azure AD által támogatott más SaaS-alkalmazásokban,](../app-provisioning/user-provisioning.md)az e-mail cím nek a SuccessFactors számára történő visszaírásával.

* **Alkalmazotti attribútum- és profilfrissítések** – Amikor egy alkalmazotti rekordot frissíta SuccessFactors (például a nevük, címük vagy kezelőjük) frissítését, felhasználói fiókjuk automatikusan frissíti az Azure Active Directoryt, és adott esetben az Office 365-öt és [az Azure AD által támogatott egyéb SaaS-alkalmazásokat.](../app-provisioning/user-provisioning.md)

* **Alkalmazottak megszűnése** – Ha egy alkalmazott a SuccessFactors szolgáltatásban megszűnik, a felhasználói fiók automatikusan le lesz tiltva az Azure Active Directoryban, és adott esetben az Office 365-ben és [az Azure AD által támogatott egyéb SaaS-alkalmazásokban.](../app-provisioning/user-provisioning.md)

* **Alkalmazotti újrahires** – Ha egy alkalmazottat újra felvesznek a SuccessFactors szolgáltatásban, a régi fiókja automatikusan újraaktiválható vagy újra kiépíthető (a preferenciától függően) az Azure Active Directoryra, és adott esetben az Office 365-re és [az Azure AD által támogatott egyéb SaaS-alkalmazásokra.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználói kiépítési megoldás a legalkalmasabb?

Ez a SuccessFactors az Azure Active Directory felhasználói kiépítési megoldás ideális a következőkhöz:

* Azok a szervezetek, amelyek előre elkészített, felhőalapú megoldást szeretnének a SuccessFactors felhasználói kiépítéshez

* Közvetlen felhasználói kiépítést igénylő szervezetek a SuccessFactors és az Azure Active Directory között

* Olyan szervezetek, amelyek megkövetelik a felhasználók kiépítését a [SuccessFactors Employee Central (EK)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) által beszerzett adatok felhasználásával

* Szervezetek, amelyek az Office 365-ös levelezést használják

## <a name="solution-architecture"></a>Megoldás architektúrája

Ez a szakasz a kizárólag felhőalapú felhasználók végpontok között lévő felhasználói kiépítési megoldásarchitektúrát ismerteti. Két kapcsolódó folyamat létezik:

* **Mérvadó HR-adatfolyam – a SuccessFactorstól az Azure Active Directoryig:** Ebben a folyamatban a feldolgozóesemények (például az új bérletek, transzferek, felmondások) először a cloud SuccessFactors Employee Central, majd az esemény adatok az Azure Active Directoryba. Az eseménytől függően előfordulhat, hogy az Azure AD-ben hozzon létre/update/enable/disable műveleteket.
* **E-mail visszaírási folyamat – a helyszíni Active Directorytól a SuccessFactors-ig:** Miután a fiók létrehozása befejeződött az Azure Active Directoryban, az Azure AD-ben létrehozott e-mail attribútum-érték vagy UPN visszaírható a SuccessFactors szolgáltatásba.

  ![Áttekintés](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Végpontok között lévő felhasználói adatfolyam

1. A HR-csapat munkavégző tranzakciókat hajt végre (Joiners/Mozgatók/Kilépők vagy Új bérlők/Transzferek/Felmondások) a SuccessFactors Employee Central-ban
2. Az Azure AD-létesítési szolgáltatás futtatja az identitások ütemezett szinkronizálását a SuccessFactors EC-től, és azonosítja azokat a módosításokat, amelyeket fel kell dolgozni a helyszíni Active Directoryval való szinkronizáláshoz.
3. Az Azure AD-kiépítési szolgáltatás határozza meg a változást, és meghívja a create/update/enable/disable műveletet a felhasználó számára az Azure AD-ben.
4. Ha a [SuccessFactors Writeback alkalmazás](sap-successfactors-writeback-tutorial.md) konfigurálva van, majd a felhasználó e-mail-címét az Azure AD-ből olvassa be. 
5. Az Azure AD létesítési szolgáltatás a használt megfelelő attribútum alapján visszaküldi az e-mail attribútumot a SuccessFactors-nak.

## <a name="planning-your-deployment"></a>A telepítés megtervezése

A Cloud HR-vezérelt felhasználók kiépítésének konfigurálása a SuccessFactors és az Azure AD között jelentős tervezést igényel, amely különböző szempontokat fed le, például:

* Az egyező azonosító meghatározása 
* Attribútumleképezés
* Attribútumátalakítás 
* Hatókörszűrők

Kérjük, olvassa el a [felhő HR-telepítési terv](../app-provisioning/plan-cloud-hr-provision.md) átfogó iránymutatásokat ezekről a témakörökről. 

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

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Felhasználói kiépítés konfigurálása a SuccessFactors és az Azure AD között

Ez a szakasz a felhasználói fiókok nak a SuccessFactors és az Azure AD között történő kiépítésének lépéseit ismerteti.

* [Adja hozzá a kiépítési összekötő alkalmazást, és konfigurálja a kapcsolatot a SuccessFactors alkalmazáshoz](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Attribútumleképezések konfigurálása](#part-2-configure-attribute-mappings)
* [Felhasználói kiépítés engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1. rész: Adja hozzá a kiépítési összekötő alkalmazást, és konfigurálja a kapcsolatot a SuccessFactors alkalmazáshoz

**A SuccessFactors konfigurálása az Azure AD-kiépítéshez:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza az **Azure Active Directory lehetőséget.**

3. Válassza **a Vállalati alkalmazások**lehetőséget, majd az Összes **alkalmazás**lehetőséget.

4. Válassza **az Alkalmazás hozzáadása**lehetőséget, majd az **Összes** kategóriát.

5. Keressen **meg successfaktorokat az Azure Active Directory felhasználói kiépítéséhez,** és adja hozzá az alkalmazást a katalógusból.

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
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * A hitelesítő adatok sikeres mentése után a **Leképezések** szakasz megjeleníti az alapértelmezett **A Sync SuccessFactors-felhasználók és** az Azure Active Directory adatait

### <a name="part-2-configure-attribute-mappings"></a>2. rész: Attribútumleképezések konfigurálása

Ebben a szakaszban konfigurálhatja, hogy a felhasználói adatok hogyan áramlanak a SuccessFactors szolgáltatásból az Active Directoryba.

1. A **Hozzárendelések**csoport Kiépítés lapján kattintson a **SuccessFactors-felhasználók szinkronizálása az Azure Active Directoryval**elemre.

1. A **Forrásobjektum-hatókör** mezőben kiválaszthatja, hogy a SuccessFactors mely felhasználói csoportoknak kell az Azure AD-be való kiépítés hatókörében lennie, attribútumalapú szűrők készletének meghatározásával. Az alapértelmezett hatókör a "SuccessFactors összes felhasználója". Példa szűrők:

   * Példa: Hatókör a personIdExternal felhasználók számára 1000000 és 2000000 között (2000000 kivételével)

      * Attribútum: personIdExternal

      * Operátor: REGEX Match

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak alkalmazottak és nem függő dolgozók

      * Attribútum: Alkalmazottazonosító

      * Operátor: NEM NULL

   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, tesztelnie és ellenőriznie kell az attribútumleképezéseket és -kifejezéseket, hogy megbizonyosodjon arról, hogy az a kívánt eredményt adja. A Microsoft azt javasolja, hogy a **Forrásobjektum-hatókör** hatóköre hatóköre segítségével tesztelje a leképezéseket néhány tesztfelhasználóval a SuccessFactors alkalmazásból. Miután meggyőződött arról, hogy a leképezések működnek, eltávolíthatja a szűrőt, vagy fokozatosan kibonthatja, hogy több felhasználóval is felvegye.

   > [!CAUTION] 
   > A kiépítési motor alapértelmezett viselkedése a hatókörön kívül eső felhasználók letiltása/törlése. Ez nem feltétlenül kívánatos a SuccessFactors az Azure AD-integráció. Az alapértelmezett viselkedés felülbírálásához olvassa el a [hatókörön kívül eső felhasználói fiókok törlésének kihagyása című cikket.](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. A **Célobjektum-műveletek mezőben** globálisan szűrheti, hogy milyen műveleteket hajt végre az Active Directoryban. **A létrehozás** és **a frissítés** a leggyakoribb.

1. Az **Attribútumleképezések** szakaszban meghatározhatja, hogy az egyes SuccessFactors attribútumok hogyan feleljenek meg az Active Directory-attribútumokhoz.

  >[!NOTE]
  >Az alkalmazás által támogatott SuccessFactors attribútum teljes listáját a [SuccessFactors attribútum hivatkozási száma tartalmazza.](../app-provisioning/sap-successfactors-attribute-reference.md)


1. A frissítéshez kattintson egy meglévő attribútumleképezésre, vagy kattintson az **Új leképezés hozzáadása** gombra a képernyő alján az új leképezések hozzáadásához. Egy egyéni attribútumleképezés támogatja a következő tulajdonságokat:

      * **Leképezés típusa**

         * **Közvetlen** – Az AD attribútum SuccessFactors attribútumának értékét írja be, módosítások nélkül

         * **Állandó** – írjon statikus, állandó karakterláncértéket az AD attribútumba

         * **Kifejezés** – Lehetővé teszi, hogy egyéni értéket írjon az AD attribútumba egy vagy több SuccessFactors attribútum alapján. [További információt a kifejezésekről szóló cikkben talál.](../app-provisioning/functions-for-customizing-application-data.md)

      * **Forrásattribútum** – A SuccessFactors felhasználói attribútuma

      * **Alapértelmezett érték** – nem kötelező. Ha a forrásattribútum üres értékkel rendelkezik, a leképezés ezt az értéket írja be helyette.
            A leggyakoribb konfiguráció ezt üresen hagyja.

      * **Célattribútum** – A felhasználói attribútum az Active Directoryban.

      * **Az ezzel az attribútummal egyeztethető kontakaró objektumok** – Azt jelzi, hogy ezt a leképezést kell-e használni a felhasználók és az Active Directory közötti egyedi azonosításhoz. Ez az érték általában a SuccessFactors munkavégzőazonosító mezőjében van beállítva, amely általában az Active Directory egyik alkalmazotti azonosító attribútumához van leképezve.

      * **Egyező sorrend** – Több egyező attribútum is beállítható. Ha több van, akkor a program az ebben a mezőben meghatározott sorrendben értékeli ki őket. Amint egyezést talál, a rendszer nem értékeli ki a további egyező attribútumokat.

      * **A leképezés alkalmazása**

         * **Mindig** – A leképezés alkalmazása a felhasználó létrehozási és frissítési műveletekre is

         * **Csak a létrehozás során** – A leképezés alkalmazása csak a felhasználó létrehozási műveleteire

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

* [További információ a támogatott SuccessFactors attribútumokról a bejövő kiépítéshez](../app-provisioning/sap-successfactors-attribute-reference.md)
* [További információ az e-mailek visszaírásának konfigurálásáról a SuccessFactors számára](sap-successfactors-writeback-tutorial.md)
* [További információ a naplók áttekintéséről és a kiépítési tevékenységről szóló jelentések beésének módjáról](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogy miként konfigurálható az egyszeri bejelentkezés a SuccessFactors és az Azure Active Directory között](successfactors-tutorial.md)
* [További információ arról, hogyan integrálhat más SaaS-alkalmazásokat az Azure Active Directoryval](tutorial-list.md)
* [További információ a kiépítési konfigurációk exportálásáról és importálásáról](../app-provisioning/export-import-provisioning-configuration.md)


