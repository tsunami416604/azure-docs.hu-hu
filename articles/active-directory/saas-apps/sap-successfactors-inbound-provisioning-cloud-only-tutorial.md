---
title: 'Oktatóanyag: a SuccessFactors bejövő kiépítés konfigurálása a Azure Active Directoryban | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja a bejövő kiépítési lehetőséget a SuccessFactors-ből az Azure AD-be
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
ms.openlocfilehash: d032bf1241f355af110ee8f4da38ff4685bd2e3f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932293"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning-preview"></a>Oktatóanyag: SAP-SuccessFactors konfigurálása az Azure AD-beli felhasználók üzembe helyezéséhez (előzetes verzió)
Ennek az oktatóanyagnak a célja, hogy megmutassa azokat a lépéseket, amelyeket el kell végeznie a munkavégző adatok SuccessFactors való kiépítéséhez a Azure Active Directoryba, az e-mail-cím opcionális visszaírásával a SuccessFactors. Ez az integráció nyilvános előzetes verzióban érhető el, és támogatja a több mint [70 felhasználói attribútum](../manage-apps/sap-successfactors-attribute-reference.md) lekérését a SuccessFactors Employee Central-ból. 

>[!NOTE]
>Akkor használja ezt az oktatóanyagot, ha a SuccessFactors kiépíteni kívánt felhasználók csak olyan felhőalapú felhasználók, akiknek nincs szükségük helyszíni AD-fiókra. Ha a felhasználók csak helyszíni AD-fiókot vagy AD-és Azure AD-fiókot igényelnek, tekintse meg az SAP- [SuccessFactors konfigurálása Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview) a felhasználók üzembe helyezése című oktatóanyagot. 

## <a name="overview"></a>Áttekintés

A [Azure Active Directory felhasználó-kiépítési szolgáltatás](../manage-apps/user-provisioning.md) integrálva van a [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) szolgáltatással a felhasználók identitási életciklusának kezelése érdekében. 

Az Azure AD-alapú felhasználó-kiépítési szolgáltatás által támogatott SuccessFactors-kiépítési munkafolyamatok lehetővé teszik a következő emberi erőforrások és az identitás-életciklus felügyeleti forgatókönyvek automatizálását:

* **Új alkalmazottak felvétele** – új alkalmazottak SuccessFactors való hozzáadásakor a rendszer automatikusan létrehoz egy felhasználói fiókot Azure Active Directory és opcionálisan az Office 365-ben és az [Azure ad által támogatott egyéb SaaS-alkalmazásokban](../manage-apps/user-provisioning.md), az e-mail-cím SuccessFactors való visszaírásával.

* **Alkalmazotti attribútumok és a profil frissítései** – ha egy alkalmazotti rekord frissül a SuccessFactors (például a nevük, a cím vagy a felettes), a felhasználói fiókja automatikusan frissül Azure Active Directory és opcionálisan az Office 365 és [Az Azure ad által támogatott egyéb SaaS-alkalmazások](../manage-apps/user-provisioning.md)esetében is.

* **Alkalmazotti megszakítások** – ha egy alkalmazott leáll a SuccessFactors-ben, a felhasználói fiókja automatikusan le lesz tiltva Azure Active Directory és opcionálisan az Office 365-ben és [Az Azure ad által támogatott egyéb SaaS-alkalmazásokban](../manage-apps/user-provisioning.md)is.

* **Alkalmazottak** újratelepítése – ha egy alkalmazottat a SuccessFactors-ben bérelnek újra, a régi fiók automatikusan újraaktiválható vagy újra kiépíthető (a beállítástól függően), hogy Azure Active Directory és opcionálisan az Office 365-et és [Az Azure ad által támogatott egyéb SaaS-alkalmazásokat](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználó-kiépítési megoldás a legmegfelelőbb?

Ez a SuccessFactors Azure Active Directory a felhasználói üzembe helyezési megoldáshoz ideális megoldást kínál a következőkhöz:

* Olyan szervezetek, amelyek előre elkészített, felhőalapú megoldást kívánnak a SuccessFactors-felhasználók üzembe helyezéséhez

* Azok a szervezetek, amelyekhez közvetlen felhasználó-kiépítés szükséges a SuccessFactors-ből Azure Active Directory

* Azok a szervezetek, amelyek megkövetelik, hogy a felhasználók kiépítsék a [SuccessFactors Employee Central (ek)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) által beszerzett adatok alapján

* Office 365-t használó szervezetek e-mailben

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a szakasz a teljes körű felhasználói üzembe helyezési megoldás architektúráját ismerteti a csak felhőalapú felhasználók számára. Két kapcsolódó folyamat létezik:

* **Mérvadó HR-adatfolyam – a SuccessFactors és a Azure Active Directory között:** Ebben a folyamatban lévő feldolgozói eseményekben (például az új bérletek, az átvitelek, a megszakítások) először a Cloud SuccessFactors Employee Central-ben fordulnak elő, és az esemény-adatforgalom Azure Active Directoryba kerül. Az eseménytől függően előfordulhat, hogy az Azure AD-ban létrehoz/frissít/engedélyez/letilt műveleteket.
* **E-mail-visszaírási folyamat – helyszíni Active Directoryról SuccessFactors:** Ha a fiók létrehozása befejeződött a Azure Active Directoryban, az Azure AD-ben létrehozott e-mail-attribútum értéke vagy UPN-je visszaírható a SuccessFactors.

  ![Áttekintés](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Végpontok közötti felhasználói adatfolyam

1. A HR-csapat munkavégző tranzakciókat (összekötőket/Mozgatókat/kimaradt vagy új bérleteket/átviteleket/megszakításokat) végez a SuccessFactors Employee Centralban
2. Az Azure AD-kiépítési szolgáltatás futtatja az identitások ütemezett szinkronizálását az SuccessFactors-től, és azonosítja azokat a módosításokat, amelyeket fel kell dolgozni a helyszíni Active Directoryval való szinkronizáláshoz.
3. Az Azure AD kiépítési szolgáltatás határozza meg a módosítást, és meghívja a felhasználó számára az Azure AD-ben a létrehozás/frissítés/engedélyezés/letiltás műveletet.
4. Ha a [SuccessFactors visszaírási-alkalmazás](sap-successfactors-writeback-tutorial.md) be van állítva, akkor a felhasználó e-mail-címe beolvasása az Azure ad-ből történik. 
5. Az Azure AD-kiépítési szolgáltatás a használt attribútum alapján visszaírja az e-mail-attribútumot a SuccessFactors.

## <a name="planning-your-deployment"></a>Az üzembe helyezés megtervezése

A Felhőbeli HR-alapú felhasználóknak a SuccessFactors-ből az Azure AD-be való konfigurálásához jelentős tervezésre van szükség a különböző szempontokat illetően, például:

* A megfelelő azonosító meghatározása 
* Attribútumleképezés
* Attribútumok átalakítása 
* Hatókörszűrők

A témakör részletes útmutatását a [Cloud HR üzembe helyezési tervében](../manage-apps/plan-cloud-hr-provision.md) találja. 

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
* Kattintson a **kész**gombra. Kattintson a **Save Changes** (Módosítások mentése) gombra.

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
* Kattintson a **Save Changes** (Módosítások mentése) gombra.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>A felhasználók üzembe helyezésének konfigurálása a SuccessFactors-ből az Azure AD-be

Ez a szakasz a SuccessFactors és az Azure AD közötti felhasználói fiókok üzembe helyezésének lépéseit ismerteti.

* [A kiépítési összekötő alkalmazás hozzáadása és a SuccessFactors-kapcsolat konfigurálása](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Attribútumok leképezésének konfigurálása](#part-2-configure-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1\. rész: a kiépítési összekötő alkalmazás hozzáadása és a SuccessFactors-kapcsolat konfigurálása

**A SuccessFactors konfigurálása az Azure AD üzembe helyezéséhez:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza a **Azure Active Directory**

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás**lehetőséget.

4. Válassza az **alkalmazás hozzáadása**lehetőséget, és válassza az **összes** kategóriát.

5. Keresse meg a **SuccessFactors Azure Active Directory a felhasználók üzembe**helyezését, és adja hozzá az alkalmazást a katalógusból.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés** lehetőséget.

7. A **kiépítési** **mód** módosítása **automatikusra**

8. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Rendszergazdai Felhasználónév** – adja meg a SuccessFactors API felhasználói fiókjának felhasználónevét a HOZZÁFŰZÖTT vállalati azonosítóval. Formátuma: **username\@companyID**

   * **Rendszergazdai jelszó –** Adja meg a SuccessFactors API felhasználói fiók jelszavát. 

   * **Bérlői URL-cím –** Adja meg a SuccessFactors OData API Services-végpont nevét. Csak a kiszolgáló állomásnevét adja meg http vagy https nélkül. Ennek az értéknek a következőhöz hasonlóan kell kinéznie: **API-Server-Name.SuccessFactors.com**.

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.
         > [!NOTE]
         > The Azure AD Provisioning Service sends email notification if the provisioning job goes into a [quarantine](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) state.

   * Kattintson a **kapcsolatok tesztelése** gombra. Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy a SuccessFactors hitelesítő adatok és az URL-cím érvényes-e.
    >[!div class="mx-imgBorder"]
    >![Azure Portalra](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Miután a hitelesítő adatok mentése sikeresen megtörtént, a **leképezések** szakasz megjeleníti az alapértelmezett leképezést, **hogy szinkronizálja a SuccessFactors felhasználókat a Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>2\. rész: attribútumok leképezésének konfigurálása

Ebben a szakaszban azt fogja konfigurálni, hogy a felhasználói adatok hogyan áramlanak a SuccessFactors a Active Directoryba.

1. A kiépítés lapon a **leképezések**területen kattintson a **SuccessFactors-felhasználók szinkronizálása Azure Active Directory**lehetőségre.

1. A **forrás objektum hatóköre** mezőben kiválaszthatja, hogy a SuccessFactors mely felhasználói csoportjai legyenek az Azure ad-be való kiépítés hatóköre az attribútum-alapú szűrők definiálásával. Az alapértelmezett hatókör a "minden felhasználó a SuccessFactors". Példa szűrők:

   * Példa: hatókör a 1000000 és 2000000 közötti personIdExternal rendelkező felhasználók számára (kivéve 2000000)

      * Attribútum: personIdExternal

      * Operátor: REGEX egyezés

      * Érték: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Példa: csak alkalmazottak és nem függő feldolgozók

      * Attribútum: Alkalmazottkód

      * Operátor: nem NULL

   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, meg kell vizsgálnia és ellenőriznie kell az attribútumok hozzárendeléseit és kifejezéseit, hogy biztosan megadja a kívánt eredményt. A Microsoft azt javasolja, hogy a **forrás objektum hatókörében** lévő hatókör-szűrők használatával tesztelje a leképezéseket a SuccessFactors néhány tesztelési felhasználója között. Miután meggyőződött arról, hogy a leképezések működnek, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

   > [!CAUTION] 
   > A kiépítési motor alapértelmezett viselkedése, hogy letiltsa/törölje a hatókörön kívüli felhasználókat. Előfordulhat, hogy a SuccessFactors nem lenne szükség az Azure AD-integrációra. Az alapértelmezett viselkedés felülbírálásához tekintse meg a [Hatókörön kívüli felhasználói fiókok törlésének kihagyása](../manage-apps/skip-out-of-scope-deletions.md) című cikket.
  
1. A **cél objektum műveletek** mezőben globálisan szűrheti, hogy a Active Directory milyen műveleteket hajtson végre. A **Létrehozás** és a **frissítés** a leggyakoribb.

1. Az **attribútum-hozzárendelések** szakaszban megadhatja, hogy az egyes SuccessFactors attribútumok hogyan képezhetők le Active Directory attribútumokra.

  >[!NOTE]
  >Az alkalmazás által támogatott SuccessFactors-attribútum teljes listájáért tekintse meg a [SuccessFactors attribútum referenciáját](../manage-apps/sap-successfactors-attribute-reference.md) .


1. Kattintson egy meglévő attribútum-hozzárendelésre a frissítéséhez, vagy kattintson az **Új leképezés hozzáadása** lehetőségre a képernyő alján új hozzárendelések hozzáadásához. Az egyes attribútumok megfeleltetése a következő tulajdonságokat támogatja:

      * **Leképezés típusa**

         * **Direct** – a SuccessFactors attribútum értékét írja a ad attribútumba, módosítás nélkül

         * **Konstans** – statikus, állandó karakterlánc-érték írása az ad-attribútumba

         * **Kifejezés** – lehetővé teszi egyéni érték írását az ad-attribútumnak egy vagy több SuccessFactors-attribútum alapján. [További információkért tekintse meg ezt a cikket a kifejezésekkel kapcsolatban](../manage-apps/functions-for-customizing-application-data.md).

      * **Forrásoldali attribútum** – a felhasználó attribútuma a SuccessFactors

      * **Alapértelmezett érték** – nem kötelező. Ha a forrás attribútum üres értékkel rendelkezik, a leképezés Ehelyett ezt az értéket fogja írni.
            A leggyakoribb konfiguráció az, hogy ezt üresen hagyja.

      * **Target attribútum** – a felhasználó attribútuma Active Directoryban.

      * **Objektumok egyeztetése ezzel az attribútummal** – függetlenül attól, hogy ezt a leképezést kell-e használni a felhasználók egyedi azonosításához a SuccessFactors és a Active Directory között. Ez az érték általában a SuccessFactors Worker ID mezőjére van beállítva, amely általában a Active Directory egyik alkalmazotti azonosító attribútumára van leképezve.

      * **Megfeleltetési prioritás** – a rendszer több egyező attribútumot is beállíthat. Ha több is van, a rendszer a mező által meghatározott sorrendben értékeli ki őket. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok.

      * **A leképezés alkalmazása**

         * **Mindig** – alkalmazza ezt a leképezést a felhasználói létrehozási és frissítési műveletekre is

         * **Csak a létrehozás során** – alkalmazza ezt a leképezést kizárólag felhasználói létrehozási műveletekre

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

* [További információ a bejövő kiépítés támogatott SuccessFactors attribútumairól](../manage-apps/sap-successfactors-attribute-reference.md)
* [Ismerje meg, hogyan konfigurálhatja az e-mailek visszaírási a SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../manage-apps/check-status-user-account-provisioning.md)
* [Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a SuccessFactors és a Azure Active Directory között](successfactors-tutorial.md)
* [Ismerje meg, hogyan integrálhat más SaaS-alkalmazásokat a Azure Active Directory](tutorial-list.md)
* [Útmutató a kiépítési konfigurációk exportálásához és importálásához](../manage-apps/export-import-provisioning-configuration.md)


