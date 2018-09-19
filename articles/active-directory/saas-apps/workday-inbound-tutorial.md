---
title: 'Oktatóanyag: Felhasználók automatikus átadása az Azure Active Directoryval a Workday konfigurálása |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az Azure Active Directoryban történő automatikus kiépítésének és megszüntetésének Workday felhasználói fiókokat.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2018
ms.author: asmalser
ms.openlocfilehash: 917baa4b0d983df858c64cd0fa5b697b0fbb316c
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46298268"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-preview"></a>Oktatóanyag: Felhasználók automatikus kiépítési (előzetes verzió) a Workday konfigurálása

Ez az oktatóanyag célja, a lépéseket kell elvégeznie, importálja a személyek Workday az Active Directory és az Azure Active Directory, az egyes attribútumok workdaybe választható visszaírási mutatni.

## <a name="overview"></a>Áttekintés

A [-kiszolgáló üzembe helyezése az Azure Active Directory-felhasználó](../manage-apps/user-provisioning.md) integrálható a [Workday emberierőforrás API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) felhasználói fiókok üzembe helyezéséhez. Azure ad-ben Ez a kapcsolat engedélyezéséhez a következő felhasználó kiépítési munkafolyamatnak használja:

* **Az Active Directory felhasználók kiépítése** – a felhasználók Workday-ből a kiválasztott csoportok szinkronizálása be egy vagy több Active Directory-erdők.

* **Kizárólag felhőalapú Azure Active Directory felhasználók kiépítése** - forgatókönyvekben, ahol a helyszíni Active Directory nem használják, felhasználók bővítheti közvetlenül a Workday-ből az Azure Active Directory-kiszolgáló üzembe helyezése az Azure AD-felhasználót használatával. 

* **A Jelszóvisszaíró e-mail-címek Workday** -kiszolgáló üzembe helyezése az Azure AD-felhasználót is írhat az Azure AD-felhasználók e-mail címét vissza Workday. 

### <a name="what-human-resources-scenarios-does-it-cover"></a>Milyen emberi erőforrások forgatókönyvek terjed ki?

A Workday felhasználói kiépítési munkafolyamatokat az Azure AD-felhasználó kiépítési szolgáltatás által támogatott a következő emberi erőforrások és az identitás-életciklus felügyeleti forgatókönyvek automatizálását engedélyezése:

* **Új alkalmazottak felvétele** – egy új alkalmazotti Workday, való felvételekor egy felhasználói fiók automatikusan létrejön az Active Directory, Azure Active Directory, és igény szerint Office 365-höz és [Azure ADáltaltámogatottmásSaaS-alkalmazásokhoz](../manage-apps/user-provisioning.md), a késleltetve visszaírt a Workday e-mail-címe.

* **Alkalmazott attribútum és a profil frissítések** – amikor egy alkalmazotti rekordot a Workdayben frissítésekor (például a nevét, a cím vagy a kezelő), a felhasználói fiókjának automatikusan frissül az Active Directory, Azure Active Directory, és igény szerint Office 365-höz és [más SaaS-alkalmazásokhoz az Azure AD által támogatott](../manage-apps/user-provisioning.md).

* **Alkalmazott végződnek** – amikor egy alkalmazott megszakad a Workdayben, a felhasználói fiókjának automatikusan le van tiltva az Active Directory, Azure Active Directory, és igény szerint Office 365-höz és [más SaaS-alkalmazásokhoz az Azure által támogatott AD](../manage-apps/user-provisioning.md).

* **Alkalmazott újra hires** – amikor egy alkalmazott van rehired WORKDAY, a régi fiókot automatikusan lehet aktiválni vagy újra létrehozni (rendelkezésére), hogy az Active Directory, Azure Active Directoryban, és igény szerint Office 365-höz és [más SaaS-alkalmazásokhoz az Azure AD által támogatott](../manage-apps/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Akik esetén a felhasználó kiépítési megoldás ajánlott használhatók?

Ez a megoldás kiépítése Workday felhasználó jelenleg nyilvános előzetes verzióban érhető el, és ideális:

* Szervezetek számára, amelyek egy előre elkészített, felhőalapú megoldást Workday felhasználókiépítés igényel

* Szervezetek számára, amelyek közvetlenül a felhasználó Active Directory vagy az Azure Active Directory-kiépítés Workday-ből megkövetelése

* Szervezetek számára, amelyek a felhasználóknak ki kell építeni-adatok a Workday HCM modul kapott (lásd: [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Szervezetek számára, amely esetében a csatlakozás helyez át, és maradjon a felhasználók egy vagy több Active Directory-erdők szinkronizálja, tartományok és szervezeti egységek alapján csak a módosítsa információk a Workday HCM-modul észlelhető (lásd: [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Használja az Office 365 e-mailek szervezetek

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="planning-your-solution"></a>A megoldás tervezése

Mielőtt megkezdené a Workday-integráció, az alábbi előfeltételek ellenőrzése, és olvassa el az aktuális Active Directory architektúrájának és követelmények kiépítése az Azure Active Directory által biztosított megoldások a felhasználó megfelelő az alábbi útmutatást.

### <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy Ön már rendelkezik a következőkkel:

* Globális rendszergazdai hozzáféréssel rendelkező érvényes Azure AD Premium P1 szintű előfizetésre
* A Workday megvalósítási bérlői tesztelése és integrációs célokra
* Rendszergazdai engedélyekkel, hozzon létre egy rendszer-integrációs felhasználót, és módosítja a WORKDAY alkalmazotti adatokat tesztelési célú tesztelése
* A felhasználó kiépítése az Active Directoryhoz, 2012 vagy újabb Windows-szolgáltatást futtató, tartományhoz csatlakoztatott kiszolgálóra szükség, gazdagépre a [a helyszíni szinkronizálási ügynök](https://go.microsoft.com/fwlink/?linkid=847801)
* [Az Azure AD Connect](../hybrid/whatis-hybrid-identity.md) szinkronizálásához Active Directory és az Azure AD között

### <a name="solution-architecture"></a>Megoldás architektúrája

Az Azure AD egy kiépítése a kiépítési és identitás életciklus-kezelést Workday-ből az Active Directory, Azure AD-ben a SaaS-alkalmazásokhoz és más alkalmazásokhoz megoldásához összekötők széles skáláját biztosítja. Milyen funkciókkal fogja használni, és hogyan állíthatja be a megoldás a szervezet környezet és követelmények függően változhat. Első lépésként hajtsa végre a készlet számát, a következő jelen, és a szervezetben telepített:

* Hány Active Directory-erdők használatban vannak?
* Hány Active Directory-tartományok használatban vannak?
* Hány Active Directory szervezeti egységek (OU) használatban vannak?
* Hány Azure Active Directory-bérlő van használatban?
* Felhasználók, akik szükség lesz az Active Directory és az Azure Active Directory (például "hibrid" felhasználók) is vannak?
* Vannak-e felhasználók, akik szükség lesz az Azure Active Directory, de nem Active Directory (például a "csak felhőalapú" felhasználó)?
* Felhasználó e-mail-címei szükséges visszaírását a Workday?

Ha ezekre a kérdésekre adott válaszokat, megtervezheti a Workday kiépítése a központi telepítés az alábbi útmutatást.

#### <a name="using-provisioning-connector-apps"></a>Üzembe helyezési összekötő-alkalmazások használata

Az Azure Active Directory előre integrált üzembe helyezési összekötők támogatja a Workday és sok más SaaS-alkalmazásokhoz.

Egy kiépítési összekötőt felületek, egy egyetlen forrásrendszer API-val, és segít a kiépítési adatokat a rendszer egyetlen. A legtöbb kiépítési összekötők, amely támogatja az Azure ad-ben az egyetlen forrás- és rendszerek (például a ServiceNow az Azure AD), és beállíthatja az alkalmazás hozzáadásával az adott, az Azure AD-alkalmazásgyűjtemény (például a ServiceNow).

Az Azure AD-kiépítési connector-példány és alkalmazáspéldány között-az-egyhez kapcsolat áll fenn:

| Forrásrendszer | Célrendszer |
| ---------- | ---------- |
| Az Azure AD-bérlő | SaaS-alkalmazás |

Azonban az Workday és az Active Directory használatakor van több forrás és cél rendszer kell figyelembe venni:

| Forrásrendszer | Célrendszer | Megjegyzések |
| ---------- | ---------- | ---------- |
| Munkanap | Active Directory-erdő | Minden olyan erdőben, a rendszer különböző számít |
| Munkanap | Az Azure AD-bérlő | Csak felhőbeli felhasználók szükség szerint |
| Active Directory-erdő | Az Azure AD-bérlő | Ez a folyamat kezeli az AAD Connect még ma |
| Az Azure AD-bérlő | Munkanap | Az e-mail-címek visszaírása |

Megkönnyítése érdekében ezek több munkafolyamat több forrás és cél rendszerekhez, az Azure AD biztosítja, amely az Azure AD-alkalmazásgyűjtemény adhat hozzá több üzembe helyezési összekötő alkalmazás:

![AAD-alkalmazás katalógusában](./media/workday-inbound-tutorial/WD_Gallery.PNG)

* **Az Active Directory kiépítés WORKDAY** – az alkalmazás lehetővé teszi a felhasználói fiók kiépítése Workday-ből való egyetlen Active Directory-erdőben. Ha több erdővel rendelkezik, az Azure AD-alkalmazásgyűjtemény kell kiépíteni az Active Directory erdőhöz, az alkalmazás egy példányát is hozzáadhat.

* **Az Azure AD-kiépítési WORKDAY** – AAD Connect pedig az eszköz, amellyel szinkronizálja az Active Directory-felhasználók számára az Azure Active Directory, az alkalmazás segítségével egyetlen Azure-bA Workday-ből csak felhőbeli felhasználók átadásának megkönnyítése Active Directory-bérlő.

* **A Jelszóvisszaíró WORKDAY** – az alkalmazás elősegíti a felhasználó e-mail-címek az Azure Active Directoryból workdaybe visszaírása.

> [!TIP]
> A normál "Munkanap" alkalmazás beállítása az egyszeri bejelentkezés Workday és az Azure Active Directory közötti szolgál. 

Állítsa be, és speciális üzembe helyezési összekötő alkalmazások konfigurálása az ebben az oktatóanyagban a fennmaradó részéből tárgysora. Mely alkalmazások konfigurációjától függ, a környezetében lévő bérlők kiépítése, és hány Active Directory-erdők és az Azure AD kell rendszerek.

![Áttekintés](./media/workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>A Workday rendszer integrációs felhasználó konfigurálása
Általános követelmény a Workday kiépítési összekötők szükségük a Workday emberierőforrás API csatlakozni a Workday rendszer integrációs fiók hitelesítő adatait. Ez a szakasz ismerteti a rendszert integráló fiók létrehozása a Workday.

> [!NOTE]
> Akkor lehet kerülni ezt az eljárást, és Ehelyett használjon Workday globális rendszergazdai fiókot a rendszer-integrációs fiókot. Előfordulhat, hogy jól működnek a bemutatókat, de nem javasolt éles környezetekben üzemelő példányok.

### <a name="create-an-integration-system-user"></a>Egy integrációs rendszer felhasználó létrehozása

**Egy integrációs rendszer felhasználó létrehozása:**

1. Jelentkezzen be a Workday-bérlői rendszergazdai fiók használatával. Az a **Workday Workbench**, adja meg felhasználó létrehozása a keresési mezőbe, és kattintson a **integrációs rendszer felhasználó létrehozása**.

    ![Felhasználó létrehozása](./media/workday-inbound-tutorial/IC750979.png "felhasználó létrehozása")
2. Végezze el a **integrációs rendszer felhasználó létrehozása** feladat egy új integrációs rendszer felhasználó a felhasználónév és jelszó megadásával.  
 * Hagyja a **új jelszó megkövetelése a következő bejelentkezés** beállítás nincs bejelölve, mert ez a felhasználó követően a naplózandó programozott módon.
 * Hagyja a **munkamenet időkorlátja perc** az az alapértelmezett érték 0, amely megakadályozza, hogy a felhasználói munkamenet időtúllépés miatt megszakadt.

    ![Hozzon létre a rendszer-felhasználói integrációs](./media/workday-inbound-tutorial/IC750980.png "integrációs rendszer felhasználó létrehozása")

### <a name="create-a-security-group"></a>Biztonsági csoport létrehozása
Hozzon létre egy korlátozás integrációs rendszer biztonsági csoportot, és rendelje hozzá a felhasználót kell.

**Biztonsági csoport létrehozása:**

1. Adjon meg biztonsági csoport létrehozása a keresési mezőbe, és kattintson a **biztonsági csoport létrehozása**.

    ![CreateSecurity csoport](./media/workday-inbound-tutorial/IC750981.png "CreateSecurity csoport")
2. Végezze el a **biztonsági csoport létrehozása** feladat.  
3. Válassza ki **integrációs rendszer biztonsági csoport (nem korlátozott lehetőséget választja)** származó a **írja be a Bérlős biztonsági csoport** legördülő listából.
4. Hozzon létre egy biztonsági csoportot, amelynek tagjai explicit módon hozzáadja.

    ![CreateSecurity csoport](./media/workday-inbound-tutorial/IC750982.png "CreateSecurity csoport")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Az integrációs rendszer felhasználó hozzárendelése a biztonsági csoport

**Az integrációs rendszer felhasználó hozzárendelése:**

1. Adja meg a biztonsági csoport szerkesztése a keresési mezőbe, és kattintson **biztonsági csoport szerkesztése**.

    ![Biztonsági csoport szerkesztése](./media/workday-inbound-tutorial/IC750983.png "biztonsági csoport szerkesztése")
1. Keresse meg, és válassza ki az új integrációs biztonsági csoport neve.

    ![Biztonsági csoport szerkesztése](./media/workday-inbound-tutorial/IC750984.png "biztonsági csoport szerkesztése")
2. Az új integrációs rendszer felhasználó hozzáadása az új biztonsági csoportot. 

    ![Rendszerbiztonsági csoport](./media/workday-inbound-tutorial/IC750985.png "rendszerbiztonsági csoport")  

### <a name="configure-security-group-options"></a>Biztonsági csoport beállításainak megadása
Ebben a lépésben, fog tartományi biztonsági házirend engedélyek megadása a feldolgozói adatok a biztonsági csoporthoz.

**Biztonsági csoport beállítások konfigurálása:**

1. Adja meg **tartományi biztonsági szabályzatok** a keresési mezőbe, és kattintson a hivatkozásra a **tartományi biztonsági szabályzatok funkcionális terület**.  

    ![Tartományi biztonsági szabályzatok](./media/workday-inbound-tutorial/IC750986.png "tartományi biztonsági szabályzatok")  
2. Keresse meg a rendszer, és válassza ki a **rendszer** funkcionális területet.  Kattintson az **OK** gombra.  

    ![Tartományi biztonsági szabályzatok](./media/workday-inbound-tutorial/IC750987.png "tartományi biztonsági szabályzatok")  
3. A rendszer működési terület vonatkozó biztonsági szabályzatok listájában bontsa ki a **biztonsági felügyeleti** , és válassza ki a tartomány biztonsági házirendje **külső fiók üzembe helyezésének**.  

    ![Tartományi biztonsági szabályzatok](./media/workday-inbound-tutorial/IC750988.png "tartományi biztonsági szabályzatok")  
1. Kattintson a **engedélyeinek szerkesztése**, majd a a **engedélyeinek szerkesztése** párbeszédpanel oldalon az új biztonsági csoportot hozzáadni a biztonsági csoportok listájának **lekérése** és **Put**  integrációs engedélyeket.

    ![Engedély szerkesztése](./media/workday-inbound-tutorial/IC750989.png "engedély szerkesztése")  

1. Minden, a fennmaradó biztonsági házirendek ismételje meg a fenti 1 – 4:

| Művelet | Tartományi biztonsági házirend |
| ---------- | ---------- | 
| GET és Put | : Feldolgozó nyilvános feldolgozó jelentések |
| GET és Put | Feldolgozó adatok: Munkahelyi kapcsolattartási adatokat |
| Lekérés | Feldolgozó adatok: Minden helyzetben |
| Lekérés | Worker adatok: Volt szükség Létszámnövelésre Information jelenlegi |
| Lekérés | : Feldolgozó üzleti címe Worker-profilok |


### <a name="activate-security-policy-changes"></a>Aktiválja a biztonsági házirend módosításai

**Biztonsági házirend módosításai aktiválása:**

1. Adja meg az aktiválást a keresőmezőbe, majd kattintson a hivatkozásra **aktiválása folyamatban lévő biztonsági házirend módosításai**.

    ![Aktiválja](./media/workday-inbound-tutorial/IC750992.png "aktiválása") 
2. Adjon meg egy megjegyzést a naplózási célokra aktiválása folyamatban lévő biztonsági házirend módosításai feladat megkezdése, és kattintson a **OK**. 

    ![Függőben lévő biztonsági aktiválása](./media/workday-inbound-tutorial/IC750993.png "függőben lévő biztonsági aktiválása")  
1. A jelölőnégyzet bejelölésével a következő képernyőn a feladat befejezéséhez **megerősítése**, és kattintson a **OK**.

    ![Függőben lévő biztonsági aktiválása](./media/workday-inbound-tutorial/IC750994.png "függőben lévő biztonsági aktiválása")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Felhasználókiépítés konfigurálása Workday-ből az Active Directoryhoz
Kövesse az alábbi utasításokat a Workday-ből való minden egyes Active Directory-erdőben való üzembe helyezést igénylő üzembe helyezést, a felhasználói fiók konfigurálása.

### <a name="planning"></a>Tervezés

Active Directory-erdőben történő felhasználókiépítés konfigurálása, előtt fontolja meg az alábbi kérdésekre. Az ezekre a kérdésekre adott válaszokat meghatározza, hogyan a Hatókörszűrő és attribútumleképezések meg kell adnia. 

* **Mi a felhasználók WORKDAY szükség lesz az Active Directory erdőben?**

   * *Példa: Felhasználók, ahol a Workday "Vállalati" attribútum tartalmazza a "Contoso" értéket, és a "Worker_Type" attribútum tartalmazza a "Regular"*

* **Hogyan legyenek átirányítva a felhasználók be különböző szervezeti egységben (OU)?**

   * *Példa: Felhasználók legyenek átirányítva a szervezeti egységek, amelyek egy office-hely a Workday "Település" és "Country_Region_Reference" attribútumok meghatározott*

* **Hogyan kell a következő attribútumokkal kell feltöltenie az Active Directory?**

   * Köznapi név (cn)
      * *Példa: Használja a Workday User_ID érték beállított emberi erőforrások*
      
   * Alkalmazott azonosítója (employeeId)
      * *Példa: A Workday Worker_ID értéket használja.*
      
   * SAM-fiók neve (sAMAccountName)
      * *Példa: Használja a Workday a USER_ID paraméter értékét értéket, szűrt keresztül az Azure AD létesítési kifejezés érvénytelen karakterek eltávolítása*
      
   * Egyszerű felhasználónév (userPrincipalName)
      * *Példa: A Workday User_ID érték használata egy Azure AD létesítési kifejezés hozzáfűzni tartománynév*

* **Hogyan felhasználók megfelelő Workday és Active Directory között?**

  * *Példa: Felhasználók egy adott Workday "Worker_ID" értéket a rendszer összehasonlítja az Active Directory-felhasználók, ahol a "employeeID" ugyanazzal az értékkel rendelkezik. Ha a Worker_ID érték nem található az Active Directoryban, hozzon létre egy új felhasználót.*
  
* **Az Active Directory-erdő már tartalmaz a felhasználó működéséhez szükséges a megfelelő logikai azonosítók?**

  * *Példa: Ha ez egy új Workday-telepítést, erősen ajánlott, hogy az Active Directory előre ki lesz töltve a megfelelő Workday Worker_ID értékek (vagy egyedi azonosító értéket választott), hogy a megfelelő logikai lehető legegyszerűbb legyen.*
    
    
### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. rész: A kiépítési összekötő alkalmazás hozzáadása, és a Workday-kapcsolat

**Az Active Directory-kiépítés konfigurálása a Workday:**

1.  Nyissa meg a következőt: <https://portal.azure.com>

2.  A bal oldali navigációs sávon válassza **Azure Active Directory**

3.  Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

4.  Válassza ki **alkalmazás hozzáadása**, és válassza ki a **összes** kategória.

5.  Keresse meg **Workday az Active Directory-kiépítés**, és adja hozzá az alkalmazást a katalógusból.

6.  Miután az alkalmazás adnak, és a Részletek képernyő látható, válassza **kiépítés**

7.  Módosítsa a **kiépítés** **mód** való **automatikus**

8.  Végezze el a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – adja meg a Workday integrációs rendszer fiók felhasználóneve a hozzáfűzi a bérlő tartományneve. **Hasonlóan kell kinéznie: username@contoso4**

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday webes szolgáltatások végpontra a bérlő számára. Hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, amelyben contoso4 a megfelelő bérlő neve helyére, és a megfelelő környezetben karakterlánc helyére wd3-impl.

   * **Active Directory-erdő -** "Name" Active Directory-erdő, amelyet a Get-ADForest powershell-parancsmag segítségével. Ez általában a egy karakterlánc, például: *contoso.com*

   * **Active Directory-tároló -** adja meg a tároló karakterláncot, amely tartalmazza az AD-erdőben lévő összes felhasználó számára. Példa: *OU általános jogú felhasználók, OU = = Users, DC = contoso, DC = test*

   * **Értesítő e-mailt –** adja meg az e-mail-címét, és jelölje be az "e-mail küldése a hiba akkor fordul elő, ha" jelölőnégyzetet.

   * Kattintson a **kapcsolat tesztelése** gombra. Ha a kapcsolat tesztelése sikeres volt, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday hitelesítő adatok érvényesek-e a Workday. 

![Azure Portal](./media/workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>2. rész: Attribútum-leképezések konfigurálása 

Ebben a szakaszban konfigurálhatja felhasználói adatfolyamok Workday-ből az Active Directoryhoz.

1.  A kiépítés lapon a **leképezések**, kattintson a **Workday-feldolgozók szinkronizálása OnPremises**.

2.  Az a **Forrásobjektum hatóköre** mezőhöz, kiválaszthatja, hogy mely felhasználók WORKDAY részhalmazához Attribútumalapú szűrőkészlet definiálásával AD, a kiépítés hatókörébe kell lennie. Az alapértelmezett hatóköre a "WORKDAY minden felhasználó". Példa szűrők:

   * Példa: Hatóköre a felhasználók számára, feldolgozó azonosítók 1000000 és 2000000 között

      * Attribútum: WorkerID

      * Üzemeltető: Reguláris kifejezés egyeztetése

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak az alkalmazottak és a nem a függő feldolgozók 

      * Attribútum: EmployeeID

      * Operátort: Nem NULL

3.  Az a **célobjektum-műveletek** mezőbe globálisan szűrheti milyen műveleteket kell elvégezni az Active Directory használata engedélyezett. **Hozzon létre** és **frissítés** legtöbb.

4.  Az a **attribútum-leképezések** területen megadhat egyes Workday attribútumok leképezése az Active Directory-attribútumok.

5. Kattintson a frissíteni egy meglévő attribútumleképzés, vagy kattintson a **új megfeleltetés hozzáadása** új leképezéseket hozzáadásához a képernyő alján. Az egyes attribútumleképezés támogatja ezeket a tulajdonságokat:

      * **Leképezés típusa**

         * **Közvetlen** – a Workday attribútum ír, nem kell módosítania az AD-attribútum

         * **Állandó** – egy statikus, állandó karakterláncértéket írni az AD-attribútum

         * **Kifejezés** – lehetővé teszi, hogy az AD attribútuma, egyéni értékké írni egy vagy több Workday-attribútumok alapján. [További információ: Ez a cikk a kifejezések](../manage-apps/functions-for-customizing-application-data.md).

      * **Adatforrás-attribútum** – Workday-ből a felhasználói attribútum. Ha az Ön által keresett attribútum nem található, lásd: [Workday felhasználói attribútumok listája testre szabható](#customizing-the-list-of-workday-user-attributes).

      * **Alapértelmezett érték** – nem kötelező. Az adatforrás-attribútum értéke egy üres, ha a leképezés inkább ezt az értéket fogja írni.
            Leggyakrabban használt beállítások mellett akkor hagyja üresen a mezőt.

      * **Célattribútum** – a felhasználói attribútum az Active Directoryban.

      * **Ezzel az attribútummal objektumok** – Ez a leképezés használandó egyedi azonosítására a felhasználók Workday és az Active Directory közötti-e. Ez általában be van állítva a feldolgozó azonosító mező a Workday, amely általában van hozzárendelve egy, az alkalmazott azonosítója attribútumok az Active Directoryban.

      * **Megfeleltetési prioritás** – több egyező attribútumok beállítása. Ha több, akkor ez a mező által meghatározott sorrendben értékeli. Amint egyezést talál, további megfelelő attribútumok értékeli ki.

      * **Leképezés alkalmazása**
       
         * **Mindig** – ezt a hozzárendelést alkalmazni, mind a felhasználó létrehozása és a frissítési műveletek

         * **Csak a létrehozásakor** -leképezés alkalmazása csak a felhasználó-létrehozási műveletek

6. A hozzárendelések mentéséhez kattintson az **mentése** az attribútum-hozzárendelési szakasz elején.

![Azure Portal](./media/workday-inbound-tutorial/WD_2.PNG)

**Az alábbiakban néhány például attribútumleképezések Workday és az Active Directory közötti néhány gyakori kifejezés**

-   A kifejezés, amely leképezi a parentDistinguishedName attribútum segítségével a felhasználók Workday adatforrás egy vagy több attribútum alapján különböző szervezeti egységekhez. Itt ebben a példában a különböző szervezeti alapú helyezi a felhasználók melyik városban lévő vannak.

-   A userPrincipalName attribútum az Active Directoryban jön létre elkülönített változó összefűzésével előállítjuk a Workday-felhasználói Azonosítót és a tartományutótag

-   [Van itt kifejezések írása dokumentációért](../manage-apps/functions-for-customizing-application-data.md). Ez magában foglalja a példák a speciális karakterek eltávolítása.

  
| MUNKANAPI ATTRIBÚTUM | AZ ACTIVE DIRECTORY-ATTRIBÚTUM |  EGYEZŐ AZONOSÍTÓ? | LÉTREHOZÁSA / FRISSÍTÉSE |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Igen** | Írt csak létrehozáskor |
| **Felhasználói azonosító**    |  CN    |   |   Írt csak létrehozáskor |
| **Csatlakozás ("@", [felhasználónév], "contoso.com")**   | userPrincipalName     |     | Írt csak létrehozáskor 
| **Cserélje le (Mid (cserélje le (\[UserID\],, "(\[ \\ \\ / \\ \\ \\ \\ \\ \\ \[ \\\\\]\\\\:\\\\;\\ \\|\\\\=\\\\,\\\\+\\\\\*\\ \\? \\ \\ &lt; \\ \\ &gt; \]) "," ",), 1, 20)," ([\\\\.) \* \$] (file:///\\.) *$)", , "", , )**      |    sAMAccountName            |     |         Írt csak létrehozáskor |
| **Switch (\[aktív\], "0", "True", "1")** |  accountDisabled      |     | Létrehozás + frissítése |
| **Keresztnév**   | givenName       |     |    Létrehozás + frissítése |
| **Vezetéknév**   |   sorozatszám   |     |  Létrehozás + frissítése |
| **PreferredNameData**  |  displayName |     |   Létrehozás + frissítése |
| **Vállalati**         | Vállalati   |     |  Létrehozás + frissítése |
| **SupervisoryOrganization**  | Szervezeti egység  |     |  Létrehozás + frissítése |
| **ManagerReference**   | kezelő  |     |  Létrehozás + frissítése |
| **BusinessTitle**   |  cím     |     |  Létrehozás + frissítése | 
| **AddressLineData**    |  streetAddress  |     |   Létrehozás + frissítése |
| **Község**   |   l   |     | Létrehozás + frissítése |
| **CountryReferenceTwoLetter**      |   CO |     |   Létrehozás + frissítése |
| **CountryReferenceTwoLetter**    |  c  |     |         Létrehozás + frissítése |
| **CountryRegionReference** |  St     |     | Létrehozás + frissítése |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Létrehozás + frissítése |
| **Irányítószám**  |   Irányítószám  |     | Létrehozás + frissítése |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Létrehozás + frissítése |
| **Fax**      | facsimileTelephoneNumber     |     |    Létrehozás + frissítése |
| **mobil**  |    mobil       |     |       Létrehozás + frissítése |
| **LocalReference** |  preferredLanguage  |     |  Létrehozás + frissítése |                                               
| **Kapcsoló (\[település\], "szervezeti egység általános jogú felhasználók, OU = felhasználók, OU = alapértelmezett, OU = helyek, DC = = contoso, DC = com", "Dallas", "szervezeti egység általános jogú felhasználók, OU = felhasználók, OU = Dallas, OU = helyek, DC = = contoso, DC = com", "Austin", "szervezeti egység általános jogú felhasználók, OU = Felhasználók, OU = Austin, OU = helyek, DC = = contoso, DC = com ","Seattle"," szervezeti egység általános jogú felhasználók, OU = felhasználók, OU = Budapest, OU = helyek, DC = = contoso, DC = com ","London"," szervezeti egység általános jogú felhasználók, OU = felhasználók, OU = London, OU = helyek, DC = = contoso, DC = com ")**  | parentDistinguishedName     |     |  Létrehozás + frissítése |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>3. rész: A helyszíni szinkronizálási ügynök konfigurálása

Annak érdekében, hogy hozza létre a helyszíni Active Directory, telepítve kell egy ügynököt egy tartományhoz csatlakoztatott kiszolgálóra a el kívánja az Active Directory-erdőben. Hitelesítő adatok szükségesek az eljárás végrehajtásához tartományi rendszergazda (vagy vállalati rendszergazdai).

**[A helyszíni szinkronizálási ügynök Itt töltheti](https://go.microsoft.com/fwlink/?linkid=847801)**

Az ügynök telepítése után futtassa az alábbi Powershell-parancsok a környezetnek az ügynök konfigurálásához.

**A parancs #1**

> CD "C:\Program Files\Microsoft Azure AD Connect Agent\Modules\AADSyncAgent kiépítési" ügynök\\modulok\\AADSyncAgent

> Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Modules\AADSyncAgent\AADSyncAgent.psd1"

**A parancs #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Bemenet: (Directory Name), adja meg az AD-erdő neve részben megadott \#2
* Bemenet: Rendszergazdai felhasználónevét és jelszavát, az Active Directory-erdő

>[!TIP]
> Ha "az elsődleges tartomány és a megbízható tartomány közötti kapcsolatot nem sikerült" a hibaüzenetet kapja, van, mert a helyi számítógép olyan környezetben, ahol több Active Directory-erdők vagy tartományok vannak konfigurálva, és legalább egy konfigurált megbízhatósági kapcsolat meghibásodott, vagy nem működő. A probléma elhárításához javítsa ki vagy távolítsa el a megszakadt megbízhatósági kapcsolatról.

**A parancs #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Bemenet: Globális rendszergazdai felhasználónevét és jelszavát az Azure AD-bérlő

>[!IMPORTANT]
>Jelenleg nincs olyan ismert probléma, globális rendszergazdai hitelesítő adataival nem működik, ha azok egy egyéni tartományt (például: admin@contoso.com). Áthidaló megoldásként hozzon létre, és a egy globális rendszergazdai fiók használata.onmicrosoft.com-tartománnyal (például: admin@contoso.onmicrosoft.com)

>[!IMPORTANT]
>Jelenleg nincs olyan ismert probléma, globális rendszergazdai hitelesítő adataival nem működik, ha a többtényezős hitelesítés engedélyezve van. A probléma megoldásához tiltsa le a multi-factor authentication szolgáltatást a globális rendszergazda.

**A parancs #4**

> Get-AdSyncAgentProvisioningTasks

* Művelet: Ellenőrizze, hogy adatokat ad vissza. Ez a parancs automatikusan felderíti a Workday-alkalmazások az Azure AD-bérlő kiépítése. Példa a kimenetre:

> Name: Az AD-erdőhöz
>
> Engedélyezve: igaz
>
> DirectoryName : mydomain.contoso.com
>
> Szakember: False (hamis)
>
> Azonosító: WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**A parancs #5**

> Start-AdSyncAgentSynchronization -Automatic

**A parancs #6**

> net stop aadsyncagent

**A parancs #7**

> a net start aadsyncagent

>[!TIP]
>A "net" parancsokat a Powershell mellett a szinkronizálási ügynök szolgáltatás szerint is elindítható, és nem használja **Services.msc**. Ha hibaüzenet jelenik meg a Powershell-parancsok futtatásakor, győződjön meg arról, hogy a **Microsoft Azure AD Connect kiépítési ügynök** fut-e **Services.msc**.

![Szolgáltatások](./media/workday-inbound-tutorial/Services.png)  

**További konfigurációs ügyfeleknek az Európai Unió**

Ha az Azure Active Directory-bérlő a EU adatközpontjainak egyikében található, majd kövesse az alábbi kiegészítő lépéseket.

1. Nyissa meg **Services.msc**, és állítsa le a **Microsoft Azure AD Connect kiépítési ügynök** szolgáltatás.
2. Nyissa meg az ügynök telepítési mappáját (például: C:\Program Files\Microsoft Azure AD Connect kiépítési ügynök).
3. Nyissa meg **SyncAgnt.exe.config** egy szövegszerkesztőben.
4. Cserélje le https://manage.hub.syncfabric.windowsazure.com/Management az **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Cserélje le https://provision.hub.syncfabric.windowsazure.com/Provisioning az **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Mentse a **SyncAgnt.exe.config** fájlt.
7. Nyissa meg **Services.msc**, és indítsa el a **Microsoft Azure AD Connect kiépítési ügynök** szolgáltatás.

**Az ügynök hibáinak elhárítása**

A [Windows Eseménynapló](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) a Windows Server az ügynököt futtató gép eseményeket az ügynök által végrehajtott összes műveletet tartalmaz. Ezek az események megjelenítése:
    
1. Nyissa meg **Eventvwr.msc**.
2. Válassza ki **Windows-naplók > alkalmazás**.
3. Forrás alatt naplózott összes esemény megtekintése **AADSyncAgent**. 
4. Ellenőrizze a hibák és figyelmeztetések.

Ha az Active Directory vagy az Azure Active Directory hitelesítő adatokkal a Powershell-parancsok megadott engedélyek probléma merül fel, például a hiba jelenik meg: 
    
![Eseménynaplók](./media/workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>4. lépés: A szolgáltatás indítása
1 – 3 részből befejezése után elkezdheti a kiépítési szolgáltatás az Azure Portalon.

1.  Az a **kiépítési** lapra, és állítsa a **üzembe helyezési állapotra** való **a**.

2. Kattintson a **Save** (Mentés) gombra.

3. Ekkor elindul a kezdeti szinkronizálás, amely változó számú, attól függően, hogy hány felhasználó van WORKDAY órát is igénybe vehet.

4. Bármikor, ellenőrizze a **Auditnaplók** lap meg, milyen műveletet végzett el a kiépítési szolgáltatás, az Azure Portalon. A naplók a kiépítési szolgáltatás, például, hogy melyik felhasználók vannak kívül workdayjel képes olvasni és majd ezt követően hozzáadása vagy frissítése az Active Directory által végrehajtott minden egyes szinkronizálási események listája. **[Tekintse meg a kiépítési jelentéskészítési útmutató részletes útmutatás a naplók olvasása](../manage-apps/check-status-user-account-provisioning.md)**

1.  Ellenőrizze a [Windows Eseménynapló](https://technet.microsoft.com/library/cc722404(v=ws.11).aspx) a Windows Server az esetleges új hibák vagy figyelmeztetések az ügynököt futtató gépen. Ezek az események teljesítményobjektumok elindításával **Eventvwr.msc** a kiszolgálón, majd válasszon **Windows-naplók > alkalmazás**. Az összes kiépítési kapcsolatos üzeneteket forrás alatt jelentkezett **AADSyncAgent**.

6. Befejeződött, akkor fog kiírni, összefoglaló jelentést a **kiépítési** lapon, a lent látható módon.

![Azure Portal](./media/workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>Azure Active Directoryban történő felhasználókiépítés konfigurálása
Hogy hogyan konfigurálhat Azure Active Directorynak az üzembe helyezési követelményeket, az alábbi táblázatban leírt módon függ.

| Forgatókönyv | Megoldás |
| -------- | -------- |
| **A felhasználóknak kell kell létrehozni, hogy az Active Directory és az Azure ad-ben** | Használat  **[az AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Felhasználók szükség lesz az Active Directory csak** | Használat  **[az AAD Connect](../hybrid/whatis-hybrid-identity.md)** |
| **Felhasználók szükség lesz az Azure AD csak (csak a felhőben)** | Használja a **Workday Azure Active Directory-kiépítés** alkalmazást az alkalmazáskatalógusban |

Az Azure AD Connect beállításával kapcsolatos utasításokért lásd: a [Azure AD Connect dokumentációját](../hybrid/whatis-hybrid-identity.md).

A következő szakaszok ismertetik a Workday és az Azure AD kizárólag felhőbeli felhasználók közötti kapcsolat beállítása.

> [!IMPORTANT]
> Ha csak felhőalapú felhasználói, szükség lesz az Azure ad-hez, és nem a helyszíni Active Directory csak kövesse az alábbi eljárást.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. rész: Az Azure AD-kiépítési összekötő alkalmazás hozzáadása, és a Workday-kapcsolat

**A csak felhőbeli felhasználók kiépítése az Azure Active Directory konfigurálása a Workday:**

1.  Nyissa meg a következőt: <https://portal.azure.com>.

2.  A bal oldali navigációs sávon válassza **Azure Active Directory**

3.  Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

4.  Válassza ki **alkalmazás hozzáadása**, majd válassza ki a **összes** kategória.

5.  Keresse meg **Workday az Azure AD-kiépítés**, és adja hozzá az alkalmazást a katalógusból.

6.  Miután az alkalmazás adnak, és a Részletek képernyő látható, válassza **kiépítés**

7.  Módosítsa a **kiépítés** **mód** való **automatikus**

8.  Végezze el a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – adja meg a Workday integrációs rendszer fiók felhasználóneve a hozzáfűzi a bérlő tartományneve. Hasonlóan kell kinéznie: username@contoso4

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday webes szolgáltatások végpontra a bérlő számára. Hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, amelyben contoso4 a megfelelő bérlő neve helyére, és a megfelelő környezetben karakterlánc helyére wd3-impl. Ha az URL-cím nem ismert, együttműködve a Workday integrációs partner vagy támogatási képviselő meghatározásához használja a megfelelő URL-címe.

   * **Értesítő e-mailt –** adja meg az e-mail-címét, és jelölje be az "e-mail küldése a hiba akkor fordul elő, ha" jelölőnégyzetet.

   * Kattintson a **kapcsolat tesztelése** gombra.

   * Ha a kapcsolat tesztelése sikeres volt, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday URL-címet és a hitelesítő adatok érvényesek a Workday.

### <a name="part-2-configure-attribute-mappings"></a>2. rész: Attribútum-leképezések konfigurálása 

Ebben a szakaszban konfigurálhatja felhasználói adatfolyamok Workday-ből az Azure Active Directoryhoz csak felhőbeli felhasználók.

1. A kiépítés lapon a **leképezések**, kattintson a **feldolgozók szinkronizálása az Azure AD**.

2. Az a **Forrásobjektum hatóköre** mezőhöz, kiválaszthatja, hogy mely felhasználók WORKDAY részhalmazához definiálását a szűrők attribútum alapján az Azure AD-kiépítés hatókörébe kell lennie. Az alapértelmezett hatóköre a "WORKDAY minden felhasználó". Példa szűrők:

   * Példa: Hatóköre a felhasználók számára, feldolgozó azonosítók 1000000 és 2000000 között

      * Attribútum: WorkerID

      * Üzemeltető: Reguláris kifejezés egyeztetése

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Például: Egyetlen függő dolgozó munkatársak és az alkalmazottak nem rendszeres

      * Attribútum: ContingentID

      * Operátort: Nem NULL

3. Az a **célobjektum-műveletek** mezőbe globálisan szűrheti milyen műveleteket kell elvégezni az Azure AD használata engedélyezett. **Hozzon létre** és **frissítés** legtöbb.

4. Az a **attribútum-leképezések** területen megadhat egyes Workday attribútumok leképezése az Active Directory-attribútumok.

5. Kattintson a frissíteni egy meglévő attribútumleképzés, vagy kattintson a **új megfeleltetés hozzáadása** új leképezéseket hozzáadásához a képernyő alján. Az egyes attribútumleképezés támogatja ezeket a tulajdonságokat:

   * **Leképezés típusa**

      * **Közvetlen** – a Workday attribútum ír, nem kell módosítania az AD-attribútum

      * **Állandó** – egy statikus, állandó karakterláncértéket írni az AD-attribútum

      * **Kifejezés** – lehetővé teszi, hogy az AD attribútuma, egyéni értékké írni egy vagy több Workday-attribútumok alapján. [További információ: Ez a cikk a kifejezések](../manage-apps/functions-for-customizing-application-data.md).

   * **Adatforrás-attribútum** – Workday-ből a felhasználói attribútum. Ha az Ön által keresett attribútum nem található, lásd: [Workday felhasználói attribútumok listája testre szabható](#customizing-the-list-of-workday-user-attributes).

   * **Alapértelmezett érték** – nem kötelező. Az adatforrás-attribútum értéke egy üres, ha a leképezés inkább ezt az értéket fogja írni.
            Leggyakrabban használt beállítások mellett akkor hagyja üresen a mezőt.

   * **Célattribútum** – az Azure ad-ben a felhasználói attribútum.

   * **Ezzel az attribútummal objektumok** – Ez a leképezés használandó egyedi azonosítására a felhasználók Workday és az Azure AD-e. Ez általában be van állítva a feldolgozó azonosító mező a Workday, amely általában hozzárendelve az alkalmazott azonosítója attribútum (új), vagy bővítményattribútum az Azure ad-ben.

   * **Megfeleltetési prioritás** – több egyező attribútumok beállítása. Ha több, akkor ez a mező által meghatározott sorrendben értékeli. Amint egyezést talál, további megfelelő attribútumok értékeli ki.

   * **Leképezés alkalmazása**

     * **Mindig** – ezt a hozzárendelést alkalmazni, mind a felhasználó létrehozása és a frissítési műveletek

     * **Csak a létrehozásakor** -leképezés alkalmazása csak a felhasználó-létrehozási műveletek

6. A hozzárendelések mentéséhez kattintson az **mentése** az attribútum-hozzárendelési szakasz elején.

### <a name="part-3-start-the-service"></a>3. rész: A szolgáltatás indítása
1 – 2 részek befejezése után elkezdheti a kiépítési szolgáltatás.

1. Az a **kiépítési** lapra, és állítsa a **üzembe helyezési állapotra** való **a**.

2. Kattintson a **Save** (Mentés) gombra.

3. Ekkor elindul a kezdeti szinkronizálás, amely változó számú, attól függően, hogy hány felhasználó van WORKDAY órát is igénybe vehet.

4. Egyes szinkronizálási események lehet megtekinteni a **Auditnaplók** fülre. **[Tekintse meg a kiépítési jelentéskészítési útmutató részletes útmutatás a naplók olvasása](../manage-apps/check-status-user-account-provisioning.md)**

5. Befejeződött, akkor fog kiírni, összefoglaló jelentést a **kiépítési** lapon, a lent látható módon.

## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Az e-mail címek workdaybe a jelszóvisszaíró konfigurálása
Kövesse az alábbi utasításokat a felhasználó e-mail-címek az Azure Active Directoryból workdaybe a visszaírás konfigurálásával.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. rész: A kiépítési összekötő alkalmazás hozzáadása, és a Workday-kapcsolat

**Az Active Directory-kiépítés konfigurálása a Workday:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza **Azure Active Directory**

3. Válassza ki **vállalati alkalmazások**, majd **minden alkalmazás**.

4. Válassza ki **alkalmazás hozzáadása**, majd válassza ki a **összes** kategória.

5. Keresse meg **Workday visszaírási**, és adja hozzá az alkalmazást a katalógusból.

6. Miután az alkalmazás adnak, és a Részletek képernyő látható, válassza **kiépítés**

7. Módosítsa a **kiépítés** **mód** való **automatikus**

8. Végezze el a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazdai felhasználónév** – adja meg a Workday integrációs rendszer fiók felhasználóneve a hozzáfűzi a bérlő tartományneve. Hasonlóan kell kinéznie: username@contoso4

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday webes szolgáltatások végpontra a bérlő számára. Hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources, amelyben a megfelelő bérlő neve contoso4 helyére, és wd3-impl helyére a megfelelő környezetben karakterláncot (ha szükséges).

   * **Értesítő e-mailt –** adja meg az e-mail-címét, és jelölje be az "e-mail küldése a hiba akkor fordul elő, ha" jelölőnégyzetet.

   * Kattintson a **kapcsolat tesztelése** gombra. Ha a kapcsolat tesztelése sikeres volt, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday URL-címet és a hitelesítő adatok érvényesek a Workday.

### <a name="part-2-configure-attribute-mappings"></a>2. rész: Attribútum-leképezések konfigurálása 

Ebben a szakaszban konfigurálhatja felhasználói adatfolyamok Workday-ből az Active Directoryhoz.

1. A kiépítés lapon a **leképezések**, kattintson a **szinkronizálása az Azure AD-felhasználók Workday**.

2. Az a **Forrásobjektum hatóköre** mezőben, igény szerint szűrheti az Azure Active Directory felhasználók melyik részhalmazához írásos engedélye szükséges. az e-mail címeket vissza a Workday. Az alapértelmezett hatóköre az "Azure AD-ben minden felhasználó". 

3. Az a **attribútum-leképezések** szakaszban, a megfelelő azonosító jelzi az attribútum az Azure Active Directoryban a Workday feldolgozó vagy alkalmazott azonosítója tároló módosítása. Egy népszerű egyező metódust, hogy a Workday feldolgozó vagy extensionAttribute1-15-re alkalmazott azonosítója szinkronizálása az Azure ad-ben, és majd segítségével ez az attribútum az Azure AD vissza a Workday felhasználók egyeztetéséhez. 

4. A hozzárendelések mentéséhez kattintson az **mentése** az attribútum-hozzárendelési szakasz elején.

### <a name="part-3-start-the-service"></a>3. rész: A szolgáltatás indítása
1 – 2 részek befejezése után elkezdheti a kiépítési szolgáltatás.

1. Az a **kiépítési** lapra, és állítsa a **üzembe helyezési állapotra** való **a**.

2. Kattintson a **Save** (Mentés) gombra.

3. Ekkor elindul a kezdeti szinkronizálás, amely változó számú, attól függően, hogy hány felhasználó van WORKDAY órát is igénybe vehet.

4. Egyes szinkronizálási események lehet megtekinteni a **Auditnaplók** fülre. **[Tekintse meg a kiépítési jelentéskészítési útmutató részletes útmutatás a naplók olvasása](../manage-apps/check-status-user-account-provisioning.md)**

5. Befejeződött, akkor fog kiírni, összefoglaló jelentést a **kiépítési** lapon, a lent látható módon.

## <a name="customizing-the-list-of-workday-user-attributes"></a>Workday felhasználói attribútumok listája testre szabható
Az alkalmazások kiépítése az Active Directory és az Azure AD egyaránt tartalmazza alapértelmezett Workday felhasználói attribútumok listáját a Workday közül választhat. Ezek a listák azonban ne legyenek átfogó. WORKDAY támogatja a több száz lehetséges felhasználói attribútumok, amelyek a standard vagy a Workday-bérlői egyedi lehet. 

Az Azure AD létesítési szolgáltatás támogatja a lista vagy a Workday attribútum olyan bármely attribútumok érhető el a testre szabhatók a [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) emberi erőforrások API-művelet.

Ehhez meg kell használnia [Workday Studio](https://community.workday.com/studio-download) kibontani a XPath kifejezés, amely a használni kívánt attribútumok jelölésére, és adja hozzá a kiépítési konfigurációjának a speciális attribútum-szerkesztő használatával az Azure Portalon.

**A Workday felhasználói attribútum XPath kifejezés lekéréséhez:**

1. Töltse le és telepítse [Workday Studio](https://community.workday.com/studio-download). Szüksége lesz egy Workday közösségi fiók eléréséhez a telepítőt.

2. Töltse le a Workday Human_Resources WDSL fájlját az URL-cím: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Indítsa el a Workday Studiót.

4. A parancssávon válassza ki a **Workday > teszt webszolgáltatás Tester** lehetőséget.

5. Válassza ki **külső**, és válassza ki a letöltött Human_Resources WSDL-fájl a 2. lépésben.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/WDstudio1.PNG)

6. Állítsa be a **hely** mezőt `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, de "IMPL-CC" cserélje le a tényleges példánytípus, és a bérlő valódi neve az "BÉRLŐ".

7. Állítsa be **művelet** való **Get_Workers**

8.  Kattintson a kis **konfigurálása** a Workday hitelesítő adatok beállítása a kérelem/válasz ablaktábla alatti hivatkozásra. Ellenőrizze **hitelesítési**, majd adja meg a felhasználónevet és jelszót a Workday-integrációs system fiók. Ügyeljen arra, hogy a felhasználó nevét, name@tenant, és a továbbiakban hagyja a **WS-biztonsági UsernameToken** lehetőség van kijelölve.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/WDstudio2.PNG)

9. Kattintson az **OK** gombra.

10. A **kérelem** panelen illessze be az XML-fájl az alábbi és beállított **Employee_ID** az alkalmazott azonosítója egy valódi felhasználónak a Workday-bérlőben. Válasszon ki egy felhasználót, hogy a attribútummal rendelkezik, amelyeket meg szeretne kinyerni feltöltve.

    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
            <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Kattintson a **kérés küldése** (zöld nyíl), hajtsa végre a parancsot. Ha sikeres, a válaszban meg kell jelennie a **válasz** ablaktáblán. Ellenőrizze a választ, ne legyen a megadott felhasználói azonosító adatait, és nem hiba.

12. Ha sikeres, másolja az XML az **válasz** ablaktáblán, és mentse egy XML-fájlt.

13. Az a parancs sáv a Workday Studióban válassza **fájl > fájl megnyitása...**  , és nyissa meg az XML-fájlt mentette. Ekkor megnyílik a Workday Studio XML-szerkesztőben.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/WDstudio3.PNG)

14. A fájl konzolfáján navigáljon a keresztül **/env: boríték > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: feldolgozó** a felhasználói adatok kereséséhez. 

15. A **wd: feldolgozó**, keresse meg a hozzáadni kívánt attribútum, és kattintson rá.

16. Másolja a kijelölt attribútum közül az XPath-kifejezést a **Hibadokumentum elérési útjának** mező.

1. Távolítsa el a **/env:Envelope / env:Body / wd:Get_Workers_Response / wd:Response_Data /** előtag a másolt kifejezés.

18. Ha a másolt kifejezés utolsó eleme-e a csomópont (Példa: "/ wd: Birth_Date"), majd fűzze **/text()** kifejezés végén. Ez nem szükséges, ha az utolsó elem egy attribútum (Példa: "/@wd: típusa").

19. Az eredmény lehet hasonló `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Ez az az Azure Portalra fogja másolni.


**Az egyéni Workday felhasználói attribútum hozzáadása az üzembe helyezési konfiguráció:**

1. Indítsa el a [az Azure portal](https://portal.azure.com), és lépjen a kiépítési szakaszra, az alkalmazás, kiépítés munkanap ebben az oktatóanyagban korábban leírtak szerint.

2. Állítsa be **üzembe helyezési állapotra** való **ki**, és válassza ki **mentése**. Ez akkor hasznos, győződjön meg arról, csak akkor, amikor készen áll a módosítások érvénybe léptetéséhez.

3. Alatt **leképezések**válassza **szinkronizálása a munkavállalók OnPremises** (vagy **feldolgozók szinkronizálása az Azure AD**).

4. A következő képernyő alján, és válassza ki **speciális beállítások megjelenítése**.

5. Válassza ki **Workday attribútumlistájának szerkesztése**.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. Hol tárolja a rendszer az adatbeviteli mezők attribútum lista alján görgessen.

7. A **neve**, adja meg az attribútum megjelenítendő nevét.

8. A **típus**, válassza ki a megfelelő megfelelő az attribútum típusa (**karakterlánc** leggyakrabban).

9. A **API kifejezés**, írja be a kimásolt Workday studióból XPath-kifejezés. Például: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Válassza ki **attribútum hozzáadása**.

    ![Munkanapi Studio](./media/workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Válassza ki **mentése** a fenti, majd **Igen** a párbeszédpanelre. Zárja be a attribútumleképezés képernyő, ha folyamatban.

12. Vissza a fő **kiépítési** lapon jelölje be **szinkronizálása a munkavállalók OnPremises** (vagy **feldolgozók szinkronizálása az Azure AD**) újra.

13. Válassza ki **új megfeleltetés hozzáadása**.

14. Az új attribútumot meg kell jelennie a **forrásattribútum** listája.

15. Adja hozzá az új attribútumot leképezése igény szerint.

16. Amikor végzett, ne felejtse el beállítása **üzembe helyezési állapotra** vissza **a** , és mentse.

## <a name="known-issues"></a>Ismert problémák

* Ha fut a **Add-ADSyncAgentAzureActiveDirectoryConfiguration** Powershell parancsot, jelenleg nincs olyan ismert probléma, globális rendszergazdai hitelesítő adataival nem működik, ha azok egy egyéni tartományt (Példa: admin@contoso.com) . Áthidaló megoldásként hozzon létre, és a egy globális rendszergazdai fiók használata az Azure AD-.onmicrosoft.com-tartománnyal (például: admin@contoso.onmicrosoft.com).

* A thumbnailPhoto felhasználói attribútum a helyszíni Active Directoryban írja az adatokat jelenleg nem támogatott.

* Az "Azure ad-hez a Workday" összekötő jelenleg nem támogatott az Azure AD-bérlőt, ahol a AAD Connect engedélyezve van-e.  

* A vizsgálati naplók nem jelennek meg az Azure AD-bérlőt, az Európai Unióban található egy előző probléma megoldódott. Azonban további ügynök konfigurációs szükség az EU-ban az Azure AD-bérlőt. További információkért lásd: [3. rész: a helyszíni szinkronizálási ügynök konfigurálása](#Part 3: Configure the on-premises synchronization agent)

## <a name="managing-personal-data"></a>Személyes adatok kezelése

A Workday megoldás kiépítése az Active Directory szükséges a szinkronizálási ügynököt egy tartományhoz csatlakoztatott kiszolgálóra kell telepíteni, és ez az ügynök a Windows eseménynaplóban, személyazonosításra alkalmas adatokat tartalmazó naplókat hoz létre.

## <a name="next-steps"></a>További lépések

* [Tekintse át a naplók és jelentések készítése a tevékenység kiépítése](../manage-apps/check-status-user-account-provisioning.md)
* [Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez Workday és az Azure Active Directory között](workday-tutorial.md)
* [Ismerje meg, hogyan integrálhatja más SaaS-alkalmazásokhoz az Azure Active Directoryval](tutorial-list.md)

