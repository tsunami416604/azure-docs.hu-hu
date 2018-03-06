---
title: "Oktatóanyag: Konfigurálja automatikus felhasználói történő üzembe helyezéséhez az Azure Active Directoryval Workday |} Microsoft Docs"
description: "Útmutató a Workday használandó adatforrás azonosító adatok az Active Directory és az Azure Active Directory."
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2018
ms.author: asmalser
ms.openlocfilehash: 2db9e60fe2807b1aa8ed7cab7eed6f7db8059a89
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Oktatóanyag: Konfigurálja automatikus felhasználói kialakítási munkanap

Ez az oktatóanyag célja a lépéseket kell elvégeznie rendszerbe való importálás érdekében személyek a WORKDAY-ből Active Directoryból és az Azure Active Directory, az egyes attribútumok Workday választható visszaírási mutatjuk be. 



## <a name="overview"></a>Áttekintés

A [kiépítése szolgáltatáshoz Azure Active Directory-felhasználó](active-directory-saas-app-provisioning.md) integrálható a [Workday emberi erőforrások API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) ahhoz, hogy a felhasználói fiókok kiépítéséhez. Az Azure AD használja ezt a kapcsolatot a következő felhasználó munkafolyamatok kiépítés engedélyezése:

* **Az Active Directory felhasználók kiépítés** -kijelölt felhasználócsoportokhoz a WORKDAY-ből egy vagy több Active Directory-erdővel történő szinkronizálása. 

* **Kiépítés csak felhőalapú Azure Active Directory felhasználók** -megtalálhatók az Active Directory és az Azure Active Directory hibrid felhasználók létesíthetők az utóbbi használatával történő [AAD-csatlakozás](connect/active-directory-aadconnect.md). Azonban felhasználók, akik csak felhőalapú telepíthető közvetlenül a WORKDAY-ből az Azure Active Directory szolgáltatás kiépítését az Azure AD-felhasználó használata.

* **Az e-mailek visszaírási Workday-címek** -szolgáltatás kiépítését az Azure AD-felhasználó akkor írási ki az Azure AD felhasználói attribútumok vissza a Workday, például az e-mail cím.

### <a name="scenarios-covered"></a>Ismertetett forgatókönyvek

A Workday felhasználói létesítési munkafolyamatok az Azure AD-felhasználó létesítési szolgáltatás által támogatott automatizálhatják a következő emberi erőforrások és identitás életciklus-kezelési forgatókönyveket:

* **Új alkalmazottak felvétele** – Ha egy új alkalmazott Workday, megjelenik egy felhasználói fiókot automatikusan létrejönnek az Active Directory, Azure Active Directoryban, és opcionálisan Office 365 és [más Azure ADáltaltámogatottSaaS-alkalmazásokhoz](active-directory-saas-app-provisioning.md), a késleltetve visszaírt a Workday az e-mail cím.

* **Alkalmazott attribútum és profil frissítések** – amikor egy alkalmazott bejegyzés frissül a Workday (például a nevét, címét, vagy manager), a felhasználói fiók automatikusan frissül az Active Directory, Azure Active Directoryban, és opcionálisan Office 365 és [más SaaS-alkalmazásokhoz az Azure AD által támogatott](active-directory-saas-app-provisioning.md).

* **Alkalmazott végződnek** – Ha egy alkalmazott a rendszer megszakítja a munkanapok, a felhasználói fiók automatikusan le van tiltva az Active Directory, Azure Active Directoryban, és opcionálisan Office 365 és [más használható az Azure-SaaS-alkalmazásokhoz AD](active-directory-saas-app-provisioning.md).

* **Alkalmazott újra bízza** – Ha egy alkalmazott van rehired a munkanapok, a régi fiókot automatikusan újra aktiválni és újra létrehozni (attól függően, hogy igény szerint), hogy az Active Directory, Azure Active Directoryban, és opcionálisan Office 365 és [más SaaS-alkalmazásokhoz az Azure AD által támogatott](active-directory-saas-app-provisioning.md).


## <a name="planning-your-solution"></a>A megoldás tervezése

A Workday-integrációs megkezdése előtt ellenőrizze az alábbi előfeltételek teljesülését, és az aktuális Active Directory-architektúra és felhasználói követelmények létrehozását az Azure Active Directory által biztosított solution(s) felel meg a következő útmutatás olvasható.

### <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban leírt forgatókönyv feltételezi, hogy már rendelkezik a következő elemek:

* Egy érvényes Azure AD Premium P1 előfizetés globális rendszergazdai jogosultságokkal
* A Workday megvalósítási bérlő tesztelése és integrációs célokra
* Tesztelési célokra alkalmazott adatok tesztelésére Workday-integrációs rendszer felhasználót kell létrehozni, és módosíthatja a rendszergazdai jogosultságokkal
* A felhasználók átadása, az Active Directory, 2012 vagy újabb Windows-szolgáltatást futtató, tartományhoz csatlakoztatott kiszolgálóra pedig szükséges a gazdagépre a [helyszíni szinkronizálási ügynök](https://go.microsoft.com/fwlink/?linkid=847801)
* [Az Azure AD Connect](connect/active-directory-aadconnect.md) szinkronizálásához Active Directory és az Azure AD között


### <a name="solution-architecture"></a>Megoldás architektúrája

Az Azure AD kiépítési összekötők megoldásához kiépítés és életciklus Identitáskezelés a WORKDAY-ből az Active Directory, az Azure AD SaaS-alkalmazásokhoz, és túl széles skáláját biztosítja. Milyen funkciókkal fogja használni, és hogyan lehet beállítani a megoldás attól függően, hogy a szervezet környezet és a követelmények változnak. Első lépésként számát a következő jelen, és a szervezetben telepített készlete érvénybe:

* Hány Active Directory-erdők használatban vannak?
* Hány Active Directory-tartományok használatban vannak?
* Hány Active Directory szervezeti egységben (OU) használatban vannak?
* Használatban vannak, hogy hány Azure Active Directory-bérlő?
* Vannak-e felhasználók, akik ki kell építeni az Active Directory és az Azure Active Directoryban (pl. "hibrid" felhasználók) is?
* Vannak-e felhasználók, akik ki kell építeni az Azure Active Directory, de nem Active Directory (pl. "csak felhőalapú" felhasználók)?
* Felhasználói e-mail címeket kell Workday visszaírását?

Ha ezekre a kérdésekre adott válaszok, tervezze meg a Workday kiépítése a központi telepítés az alábbi útmutatást követve.

#### <a name="using-provisioning-connector-apps"></a>Üzembe helyezési összekötő-alkalmazások használata

Az Azure Active Directory előzetesen beépített létesítési összekötők támogatja a Workday és sok más SaaS-alkalmazásokhoz. 

Üzembe helyezési egyetlen összekötőt felületeihez egyetlen forrásai rendszer API-val, és lehetővé teszi a kiépítési adatokat egyetlen rendszeren való. A legtöbb létesítési összekötők, amely támogatja az Azure AD egyetlen forrás és cél rendszer (pl. Azure AD-be a ServiceNow), majd beállíthatja az alkalmazás hozzáadásával a szóban forgó az az Azure AD-alkalmazásgyűjtemény (pl. ServiceNow). 

Üzembe helyezési connector-példány és az app-példányok közötti-az-egyhez kapcsolat áll fenn az Azure ad-ben:

| A forráskiszolgálón rendszer | Célrendszer |
| ---------- | ---------- | 
| Az Azure AD-bérlő | SaaS-alkalmazáshoz |


Munkanapok és Active Directory használata, ha van azonban több forrás és cél rendszer figyelembe kell venni:

| A forráskiszolgálón rendszer | Célrendszer | Megjegyzések |
| ---------- | ---------- | ---------- |
| Munkanapok | Active Directory-erdő | Minden olyan erdőben, a rendszer egy különálló célrendszerben |
| Munkanapok | Az Azure AD-bérlő | A felhasználó esetében kötelező, csak felhőalapú |
| Active Directory-erdő | Az Azure AD-bérlő | Ez a folyamat végzi el az AAD-csatlakozás még ma |
| Az Azure AD-bérlő | Munkanapok | Az e-mail címek visszaírásához. |

Lehetővé teszi a több munkafolyamat több forrás és cél rendszerre, az Azure AD biztosít több üzembe helyezési összekötő alkalmazást, az Azure AD-alkalmazásgyűjtemény is hozzáadhat:

![Az AAD-Alkalmazásgyűjtemény](./media/active-directory-saas-workday-inbound-tutorial/WD_Gallery.PNG)

* **Az Active Directory kiépítésére WORKDAY** – az alkalmazás lehetővé teszi a felhasználói fiók kiépítése a WORKDAY-ből egyetlen Active Directory-erdőre. Ha több erdővel rendelkezik, az alkalmazás egy példányát minden egyes Active Directory erdőhöz kell kiépíteni az Azure AD-alkalmazásgyűjtemény is hozzáadhat.

* **A Azure AD-létesítési WORKDAY** – az eszköz, amellyel szinkronizálja az Active Directory-felhasználók az Azure Active Directory, az alkalmazás lehetővé teszi egyetlen Azure-ba történő átadása csak felhőalapú felhasználók kiépítése használható pedig az AAD Connect Active Directory-bérlő.

* **WORKDAY visszaírási** – az alkalmazás lehetővé teszi a felhasználó e-mail címet az Azure Active Directoryból a Workday visszaírása.

> [!TIP]
> A rendszeres "Workday" alkalmazás beállítása az egyszeri bejelentkezés Workday és az Azure Active Directory közötti szolgál. 

Állítsa be, és ezen különleges létesítési összekötő alkalmazások konfigurálása az ebben az oktatóanyagban fennmaradó részében tárgysora. A környezetben lévő bérlők kell azokat, és hogy hány Active Directory-erdők és az Azure AD rendszerek mely alkalmazások konfigurációjától függ.

![Áttekintés](./media/active-directory-saas-workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>A rendszer integrációs felhasználó Workday konfigurálása
A Workday létesítési összekötők közös követelményt igényelnek-e kapcsolódni a Workday emberi erőforrások API a Workday rendszer integrációs fiók hitelesítő adatait. Ez a szakasz ismerteti, hogyan hozhat létre integráló rendszerfiók a Workday.

> [!NOTE]
> Ez az eljárás kihagyásával a Workday globális rendszergazdai fiókját használja a rendszer integrációs fiók lehetőség. Előfordulhat, hogy jól működnek az bemutatók, de az üzemi környezetek nem ajánlott.

### <a name="create-an-integration-system-user"></a>Integrációs rendszer felhasználó létrehozása

**Integrációs rendszer felhasználó létrehozása:**

1. Jelentkezzen be arra a Workday-bérlői rendszergazdai fiók használatával. Az a **Workday munkaterület**, adja meg létrehozni a felhasználót a keresési mezőbe, és kattintson a **integrációs rendszer felhasználó létrehozása**. 
   
    ![A felhasználó létrehozása](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "a felhasználó létrehozása")
2. Fejezze be a **integrációs rendszer felhasználó létrehozása** feladatot úgy, hogy megadja a felhasználónevet és jelszót egy új integrációs rendszer felhasználó.  
 * Hagyja a **új jelszó szükséges a következő bejelentkezési a** beállítás nincs bejelölve, mert a felhasználó fog naplózása programozott módon. 
 * Hagyja a **munkamenet időkorlátja percben** az alapértelmezett érték 0, amely megakadályozza, hogy a felhasználói munkamenet időtúllépés miatt idő előtt. 
   
    ![Hozzon létre rendszer-felhasználói integrációs](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "integrációs rendszer felhasználó létrehozása")

### <a name="create-a-security-group"></a>Biztonsági csoport létrehozása
Hozzon létre egy korlátozás nélküli integrációs rendszerbiztonsági csoport, és rendelje hozzá a felhasználó kell.

**Biztonsági csoport létrehozása:**

1. Írja be a keresőmezőbe hozzon létre biztonsági csoportot, és kattintson a **biztonsági csoport létrehozása**. 
   
    ![CreateSecurity csoport](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "CreateSecurity csoport")
2. Fejezze be a **biztonsági csoport létrehozása** feladat.  
3. Integráció rendszerbiztonsági csoport kiválasztása – a korlátozás nélküli a **típus a központjaként biztonsági csoport** legördülő menüből.
4. Hozzon létre egy biztonsági csoportot, amelyhez tagokat a rendszer explicit módon hozzáadja. 
   
    ![CreateSecurity csoport](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "CreateSecurity csoport")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>Az integráció rendszer felhasználó hozzárendelése a biztonsági csoport

**Az integráció rendszer felhasználó hozzárendelése:**

1. Adja meg a biztonsági csoport szerkesztése a keresési mezőbe, és kattintson **biztonsági csoport szerkesztése**. 
   
    ![Biztonsági csoport szerkesztése](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "biztonsági csoport szerkesztése")
2. Keresse meg, és válassza ki az új integrációs biztonsági csoport neve. 
   
    ![Biztonsági csoport szerkesztése](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "biztonsági csoport szerkesztése")
3. Az új integrációs rendszer felhasználó hozzáadása az új biztonsági csoportot. 
   
    ![Rendszerbiztonsági csoport](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "rendszerbiztonsági csoport")  

### <a name="configure-security-group-options"></a>Biztonsági csoport beállítások konfigurálása
Ebben a lépésben engedélyt kell adnia lesz tartományi biztonsági házirend esetében a következő tartományi biztonsági házirend által védett adatok:


| Művelet | Tartományi biztonsági házirend |
| ---------- | ---------- | 
| GET és a Put |  Külső fiók |
| GET és a Put | Adatok: Nyilvános dolgozó jelentések |
| GET és a Put | Adatok: Minden helyzetben |
| GET és a Put | Adatok: Aktuális személyzeti információk |
| GET és a Put | Adatok: Üzleti cím munkavégző profil |
| Megtekintése és módosítása | Adatok: A munkahelyi E-mail |

**Biztonsági csoport beállítások konfigurálása:**

1. Adja meg a tartomány biztonsági házirendeket a keresési mezőbe, és kattintson a hivatkozásra **tartományi biztonsági házirendek funkcionális terület**.  
   
    ![Tartományi biztonsági házirendek](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "tartományi biztonsági házirendek")  
2. Keresse meg a rendszer, és válassza a **rendszer** funkcionális területét.  Kattintson az **OK** gombra.  
   
    ![Tartományi biztonsági házirendek](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "tartományi biztonsági házirendek")  
3. A funkcionális területen biztonsági szabályzatok listájában bontsa ki a **biztonsági felügyelet** válassza ki a tartomány biztonsági házirendje **külső fiók létrehozását**.  
   
    ![Tartományi biztonsági házirendek](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "tartományi biztonsági házirendek")  
4. Kattintson a **engedélyek módosítása**, majd a a **engedélyek módosítása** párbeszédpanel lap, az új biztonsági csoportot hozzáadni a biztonsági csoportok listájának **beolvasása** és **Put**  integrációs engedélyeket. 
   
    ![Engedélyek szerkesztése](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "engedélyek szerkesztése")  
5. Ismételje meg a működési területek kiválasztására szolgáló képernyő térjen vissza a fenti 1. lépés:, és ezúttal, keressen a személyzettel, válassza ki a **funkcionális területen személyzeti** kattintson **OK**.
   
    ![Tartományi biztonsági házirendek](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "tartományi biztonsági házirendek")  
6. A személyzeti funkcionális területen biztonsági szabályzatok listájában bontsa ki a **adatok: személyzeti** és minden egyes biztonsági házirendek fennmaradó megismétli a fenti 4:

   * Adatok: Nyilvános dolgozó jelentések
   * Adatok: Minden helyzetben
   * Adatok: Aktuális személyzeti információk
   * Adatok: Üzleti cím munkavégző profil
   
7. 1. lépés megismétlésével, a fenti való visszatéréshez kiválasztására szolgáló képernyő működési területek, és ez alkalommal keressen **elérhetőségi adatait**, jelölje ki a személyzeti funkcionális területet, és kattintson a **OK**.

8.  A személyzeti funkcionális területen biztonsági szabályzatok listájában bontsa ki a **adatok: munkahelyi elérhetőségi adatait**, és a biztonsági házirendek az alábbi megismétli a fenti 4:

    * Adatok: A munkahelyi E-mail

    ![Tartományi biztonsági házirendek](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "tartományi biztonsági házirendek")  
    
### <a name="activate-security-policy-changes"></a>Aktiválja a biztonsági házirend módosításai

**Biztonsági házirend módosításai aktiválása:**

1. Adja meg a keresőmezőbe aktiválása, és kattintson a hivatkozásra **aktiválása függőben lévő Biztonságiszabályzat-változásokat**. 
   
    ![Aktiválása](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "aktiválása") 
2. A függőben lévő biztonsági módosítások aktiválása feladat megkezdéséhez adjon meg egy megjegyzést a naplózási célokra, és kattintson a **OK**. 
   
    ![Függőben lévő biztonsági aktiválása](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "függőben lévő biztonsági aktiválása")   
3. Elvégezni a feladatot a következő képernyőn, a jelölőnégyzet bejelölésével **megerősítése**, és kattintson a **OK**. 
   
    ![Függőben lévő biztonsági aktiválása](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "függőben lévő biztonsági aktiválása")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>A felhasználók átadása a WORKDAY-ből az Active Directory konfigurálása
Kövesse ezeket az utasításokat a WORKDAY-ből minden Active Directory-erdőbe történő igénylő kiépítés felhasználói fiók beállítása.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. lépés: Az üzembe helyezési összekötő alkalmazás hozzáadása, és a Workday kapcsolat létrehozása

**Az Active Directory kiépítésére konfigurálása a Workday:**

1.  Nyissa meg a következőt: <https://portal.azure.com>

2.  Válassza ki a bal oldali navigációs sáv **Azure Active Directoryban**

3.  Válassza ki **vállalati alkalmazások**, majd **összes alkalmazás**.

4.  Válassza ki **alkalmazás hozzáadása**, és válassza ki a **összes** kategóriát.

5.  Keresse meg **Workday kiépítés az Active Directory**, és adja hozzá az alkalmazás a gyűjteményből.

6.  Az alkalmazás hozzáadása és az alkalmazás részletei képernyőn látható, jelölje be az után **kiépítés**

7.  Módosítsa a **kiépítés** **mód** való **automatikus**

8.  Fejezze be a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazda felhasználóneve** – adja meg a Workday-integrációs rendszerfiók felhasználóneve fűzött bérlői tartománynévvel. **Hasonlóan kell kinéznie: username@contoso4**

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday web services végpontnak a bérlő számára. Hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4, ahol contoso4 helyére a megfelelő bérlő neve és wd3-impl cseréli azokat a megfelelő környezet karakterlánc.

   * **Az Active Directory Erdőfelderítési -** a "Name" Active Directory-erdő, amelyet a Get-ADForest powershell-parancsmag segítségével. Ez általában egy olyan karakterlánc, például: *contoso.com*

   * **Active Directory-tároló -** adja meg a tároló karakterláncot, amely tartalmazza az összes felhasználót az Active Directory-erdőben. Példa: *OU általános jogú felhasználók, OU = = Users, DC = contoso, DC = test*

   * **E-mailben értesítést –** meg e-mail címét, és jelölje be az "e-mail küldési hiba esetén" jelölőnégyzetet.

   * Kattintson a **kapcsolat tesztelése** gombra. Ha a kapcsolat ellenőrzése sikeres, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday hitelesítő adatok érvényesek-e a Workday. 

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>2. lépés: Konfigurálja a attribútum-leképezésekhez 

Ebben a szakaszban konfigurál, hogy felhasználói adatáramlás a WORKDAY-ből az Active Directory.

1.  A kiépítés lapon a **hozzárendelések**, kattintson a **Workday dolgozók szinkronizálása OnPremises**.

2.  Az a **forrás objektum hatóköre** mezőjét, kiválaszthatja, melyik felhasználócsoportokhoz a Workday Active Directory, az alkalmazott szűrőket Attribútumalapú meghatározásával üzembe helyezéséhez hatókörében kell lennie. Az alapértelmezett hatóköre "minden felhasználó a Workday". Példa szűrők:

   * Példa: Hatókör felhasználók munkavégző azonosítók 1000000 és 2000000 között

      * Attribútum: WorkerID

      * Operátor: Reguláris KIFEJEZÉSSEL egyező

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak az alkalmazottak és a nem függő munkavállalók 

      * Attribútum: EmployeeID

      * Operátort: Nem NULL

3.  Az a **cél objektum műveletek** mezőjét, szűrheti az globálisan, milyen műveleteket kell elvégezni az Active Directory használata engedélyezett. **Hozzon létre** és **frissítés** leggyakoribb.

4.  Az a **attribútum-hozzárendelések** szakaszban meghatározhatja egyes Workday attribútumok az Active Directory-attribútumok hozzárendelését.

5. Kattintson az egy meglévő címtárattribútum-leképezésben frissíti a **új leképezés hozzáadása** hozzáadása új leképezéseket a képernyő alján. Az egyes címtárattribútum-leképezésben támogatja ezeket a tulajdonságokat:

      * **Leképezés típusa**

         * **Közvetlen** – a Workday attribútum értéke ír az AD attribútum, módosítások nélküli

         * **Állandó** -statikus, állandó karakterlánc-érték írni az AD-attribútum

         * **Kifejezés** – lehetővé teszi egy egyéni érték írható AD attribútum egy vagy több Workday-attribútumok alapján. [További információk: Ez a cikk a kifejezések](active-directory-saas-writing-expressions-for-attribute-mappings.md).

      * **Adatforrás-attribútum** -a felhasználói attribútum a WORKDAY-ből. Ha a keresett attribútum nincs jelen, lásd: [testreszabása a Workday felhasználói attribútumok listáját](#customizing-the-list-of-workday-user-attributes).

      * **Alapértelmezett érték** – nem kötelező. A forrásattribútum nem üres érték tartozik, ha a leképezés fog kiírni, ez az érték helyett.
            Általános beállítások mellett akkor hagyja üresen a mezőt.

      * **Cél attribútumának** – a felhasználói attribútum az Active Directoryban.

      * **Ezzel az attribútummal objektumok megfelelő** – függetlenül attól, ez a leképezés használjon egyedi azonosítására a felhasználók Workday és az Active Directory között. Ez általában be van állítva a munkavégző mezőt a Workday, amelyhez általában van rendelve egy, az Active Directory Alkalmazottazonosító attribútumok.

      * **Megfelelő sorrend** – több egyező attribútumok állítható be. Ha több, kiértékelésük, ez a mező által megadott sorrendben. Amint a program egyezést talál, további megfelelő attribútumok kiértékelése.

      * **Ez a leképezés alkalmazása**
       
         * **Mindig** – Ez a leképezés alkalmazni mind a felhasználó létrehozása és a műveletek frissítése

         * **Csak létrehozásakor** – Ez a leképezés csak a felhasználó létrehozási műveletek alkalmazása

6. A hozzárendelések mentéséhez kattintson **mentése** attribútum leképezési szakasz elején.

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_2.PNG)

**Az alábbiakban néhány példa néhány gyakori kifejezésekkel a Workday és az Active Directory közötti attribútum-leképezésekhez**

-   A kifejezés, amely leképezhető a parentDistinguishedName AD attribútum kiépítése a felhasználót, hogy egy adott szervezeti egység egy vagy több Workday-forrás attribútum használható. Ebben a példában a város adataikat függően különböző szervezeti felhasználók helyezi a Workday.

-   A kifejezés, amely a userPrincipalName AD attribútum van leképezve, hozzon létre egy egyszerű firstName.LastName@contoso.com. A váltja fel érvénytelen különleges karaktereket is.

-   [Nincs a dokumentáció itt kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md)

  
| WORKDAY ATTRIBÚTUM | AZ ACTIVE DIRECTORY-ATTRIBÚTUM |  EGYEZŐ AZONOSÍTÓ? | LÉTREHOZÁSA / FRISSÍTÉSE |
| ---------- | ---------- | ---------- | ---------- |
|  **WorkerID**  |  EmployeeID | **Igen** | Írt csak létrehozásakor | 
|  **Település**   |   l   |     | Hozzon létre + frissítése |
|  **Vállalati**         | Vállalati   |     |  Hozzon létre + frissítése |
|  **CountryReferenceTwoLetter**      |   CO |     |   Hozzon létre + frissítése |
| **CountryReferenceTwoLetter**    |  c  |     |         Hozzon létre + frissítése |
| **SupervisoryOrganization**  | Szervezeti egység  |     |  Hozzon létre + frissítése |
|  **PreferredNameData**  |  displayName |     |   Hozzon létre + frissítése |
| **EmployeeID**    |  cn    |   |   Írt csak létrehozásakor |
| **Fax**      | facsimileTelephoneNumber     |     |    Hozzon létre + frissítése |
| **Utónév**   | givenName       |     |    Hozzon létre + frissítése |
| **Kapcsoló (\[aktív\],, "0", "True", "1")** |  accountDisabled      |     | Hozzon létre + frissítése |
| **Mobileszköz**  |    Mobileszköz       |     |       Hozzon létre + frissítése |
| **E-mail cím**    | mail    |     |     Hozzon létre + frissítése |
| **ManagerReference**   | Manager  |     |  Hozzon létre + frissítése |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Hozzon létre + frissítése |
| **Irányítószám**  |   Irányítószám  |     | Hozzon létre + frissítése |
| **LocalReference** |  preferredLanguage  |     |  Hozzon létre + frissítése |
| **Replace(Mid(Replace(\[EmployeeID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Írt csak létrehozásakor |
| **LastName**   |   sorozatszám   |     |  Hozzon létre + frissítése |
| **CountryRegionReference** |  St     |     | Hozzon létre + frissítése |
| **AddressLineData**    |  streetAddress  |     |   Hozzon létre + frissítése |
| **PrimaryWorkTelephone**  |  TelephoneNumber   |     | Hozzon létre + frissítése |
| **BusinessTitle**   |  cím     |     |  Hozzon létre + frissítése |
| **Csatlakozás ("@", cserélje le (cserélje le (cseréje (cserélje le (cserélje le (cserélje le (cserélje le (cserélje le (cserélje (le (a név felülírandó (csere (csere (csere (csere (csere (csere (csere (csere (csere (csere (csere (csere (csere (csere (csere (a név felülírandó () Cserélje le (Csatlakozás (".", [Keresztnév], [Vezetéknév]), "([Øø])", "Outlook Express",), "[Ææ]", "ae",), "([äãàâãåáąÄÃÀÂÃÅÁĄA])", "a",), "[B]", "b",), "([CçčćÇČĆ])", "c",), "([ďĎD])", "d",), "([ëèéêęěËÈÉÊĘĚE])", "e",), "[F]", "f",), "([G])" ,, "g",), "[H]", "h",), "([ïîìíÏÎÌÍI])", "i",), "[J]", "j",), "([-K])", "-k",), "([ľłŁĽL])", "l",), "([M])", "m",), "([ñńňÑŃŇN])", "n",), "([öòőõôóÖÒŐÕÔÓO])", "o",), "([P])", "p",), "([Q])", "q",),  "([ŘŘR])", "r",), "([ßšśŠŚS])", "s",), "([TŤť])", "t",), "([üùûúůűÜÙÛÚŮŰU])", "u",), "([V])", "v",), "([w" karakter]), "w",), "([ýÿýŸÝY])", "y",), "([źžżŹŽŻZ])", "z",), "",,, "",), "contoso.com")**   | userPrincipalName     |     | Írt csak létrehozásakor                                                   
| **Kapcsoló (\[település\], "OU általános jogú felhasználók, OU = felhasználók, OU = alapértelmezett, OU = helyek, DC = = contoso, DC = com", "Dallas", "OU általános jogú felhasználók, OU = felhasználók, OU = Dallas, OU = helyek, DC = = contoso, DC = com", "Austin", "OU általános jogú felhasználók, OU = Felhasználók, OU = Austin, OU = helyek, DC = = contoso, DC = com ","Seattle"," OU általános jogú felhasználók, OU = felhasználók, OU = budapesti, OU = helyek, DC = = contoso, DC = com ","Londoni"," OU általános jogú felhasználók, OU = felhasználók, OU = London, OU = helyek, DC = = contoso, DC = com ")**  | parentDistinguishedName     |     |  Hozzon létre + frissítése |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>3. lépés: A helyszíni-szinkronizálási ügynök konfigurálása

Ahhoz, hogy a helyszíni Active Directory kiépíteni, az ügynök az Active Directory-erdő desire egy tartományhoz csatlakozó kiszolgálón telepítenie kell. Tartományi rendszergazda (vagy a vállalati rendszergazdai) hitelesítő adatokra van szükség a művelet elvégzéséhez.

**[Letöltheti a helyszíni-szinkronizálási ügynök Itt](https://go.microsoft.com/fwlink/?linkid=847801)**

Ügynök telepítése után futtassa az alábbi Powershell-parancsok a környezetnek az ügynök konfigurálása.

**A parancs #1**

> CD C:\\programfájlok\\Microsoft Azure Active Directory szinkronizálási ügynök\\modulok\\AADSyncAgent

> import-module AADSyncAgent.psd1

**A parancs #2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* Bemenet: "Könyvtárnév", adja meg az Active Directory-erdőben neve részben megadott \#2
* Bemenet: Rendszergazda felhasználónevét és jelszavát, az Active Directory-erdő

**A parancs #3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* Bemenet: Globális rendszergazda felhasználónevét és jelszavát az Azure AD-bérlő

>[!IMPORTANT]
>Jelenleg egy ismert probléma globális rendszergazdai hitelesítő adatokkal nem működik, ha azok az egyéni tartománynév (Példa: admin@contoso.com). Megoldás létrehozása, és egy globális rendszergazdai fiók használata onmicrosoft.com tartománnyal (Példa: admin@contoso.onmicrosoft.com)


**A parancs #4**

> Get-AdSyncAgentProvisioningTasks

* Művelet: Ellenőrizze, hogy adatokat ad vissza. Ez a parancs automatikusan észleli a Workday kiépítés alkalmazások az Azure AD-bérlőben. Példa a kimenetre:

> Name: Az Active Directory-erdőben
>
> Engedélyezve: igaz
>
> DirectoryName : mydomain.contoso.com
>
> Credentialed: hamis
>
> Azonosító: WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**A parancs #5**

> Start-AdSyncAgentSynchronization -Automatic

**A parancs #6**

> net stop aadsyncagent

**A parancs #7**

> a net start aadsyncagent

>[!TIP]
>A "net" parancsot a Powershell mellett a szinkronizálási ügynök szolgáltatás is elindítható, és nem használja **Services.msc**. Ha hibaüzenet jelenik meg a Powershell-parancsok futtatása, győződjön meg arról, hogy a **Microsoft Azure AD Connect kiépítési ügynök** futtatja a **Services.msc**.

![Szolgáltatások](./media/active-directory-saas-workday-inbound-tutorial/Services.png)  

**Az ügyfelek az Európai Unió további konfigurációs**

Ha az Azure Active Directory-bérlő az EU adatközpontok egyik található, majd kövesse az alábbi kiegészítő lépéseket.

1. Nyissa meg **Services.msc** , és állítsa le a **Microsoft Azure AD Connect kiépítési ügynök** szolgáltatás.
2. Nyissa meg az ügynök telepítési mappájába (Példa: C:\Program Files\Microsoft Azure AD Connect kiépítési ügynök).
3. Nyissa meg **SyncAgnt.exe.config** egy szövegszerkesztőben.
4. Cserélje le a https://manage.hub.syncfabric.windowsazure.com/Management **https://eu.manage.hub.syncfabric.windowsazure.com/Management**
5. Cserélje le a https://provision.hub.syncfabric.windowsazure.com/Provisioning **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning**
6. Mentse a **SyncAgnt.exe.config** fájlt.
7. Nyissa meg **Services.msc**, és indítsa el a **Microsoft Azure AD Connect kiépítési ügynök** szolgáltatás.

**Hibaelhárítás ügynök**

A [Windows-Eseménynapló](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) a Windows Server az ügynököt futtató számítógép az ügynök által végzett összes műveletet eseményeket tartalmazza. Ezek az események megtekintése:
    
1. Nyissa meg **Eventvwr.msc**.
2. Válassza ki **Windows-naplók > alkalmazás**.
3. A következő forrás alatt naplózott összes esemény megtekintése **AADSyncAgent**. 
4. Hibák és figyelmeztetések ellenőrzése.

Ha a megadott a Powershell-parancsokat az Active Directory vagy az Azure Active Directory hitelesítő adatokkal engedélyekkel kapcsolatos probléma, például a hiba jelenik meg: 
    
![Eseménynaplók](./media/active-directory-saas-workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>4. lépés: A szolgáltatás indítása
1-3 részből befejezése után megkezdheti a létesítési szolgáltatás vissza az Azure-portálon.

1.  Az a **kiépítési** lapján állítsa be a **kiépítési állapot** való **a**.

2. Kattintson a **Save** (Mentés) gombra.

3. Elindítja a kezdeti szinkronizálás, ami eltarthat, attól függően, hogy hány felhasználó szerepelnek a Workday óra változó számú.

4. Bármikor, ellenőrizze a **naplók** fülre az Azure portálon, hogy a létesítési szolgáltatás hajtott végre műveleteket. A naplók a létesítési szolgáltatás, például, hogy melyik felhasználók folyamatban van a Workday kívül olvassa el és majd később hozzáadott vagy frissített Active Directory által végrehajtott minden egyes szinkronizálási események sorolja fel. **[Olvassa el a felügyeleti naplók az üzembe helyezési jelentéskészítés – útmutató részletes utasításokat olvashat](active-directory-saas-provisioning-reporting.md)**

5.  Ellenőrizze a [Windows-Eseménynapló](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx) a Windows Server kapcsolatos esetleges új hibák vagy figyelmeztetések az ügynököt futtató számítógépen. Indítsa el ezeket az eseményeket is megtekinthető **Eventvwr.msc** a kiszolgálón, és kiválasztja **Windows-naplók > alkalmazás**. Kiépítés kapcsolatos összes üzenet a következő forrás alatt jelentkezett **AADSyncAgent**. 
    

6. Fejeződött be, akkor fog kiírni, összefoglaló jelentést a **kiépítési** lapon, a lent látható módon.

![Azure Portal](./media/active-directory-saas-workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>A felhasználók átadása az Azure Active Directory konfigurálása
Hogyan konfigurálja a kiépítése az Azure Active Directoryba a kiépítési követelményeknek, ahogy az az alábbi táblázat függ.

| Forgatókönyv | Megoldás |
| -------- | -------- |
| **A felhasználóknak kell kell létrehozni, hogy az Active Directory és az Azure AD** | Használjon  **[AAD-csatlakozás](connect/active-directory-aadconnect.md)** |
| **Felhasználók szükség lesz az Active Directory csak** | Használjon  **[AAD-csatlakozás](connect/active-directory-aadconnect.md)** |
| **Felhasználók ki kell építeni az Azure AD csak (csak felhőalapú)** | Használja a **Workday az Azure Active Directory kiépítésére** alkalmazás az app-katalógus |

Az Azure AD Connect beállításával kapcsolatos útmutatásért lásd: a [Azure AD Connect dokumentációját](connect/active-directory-aadconnect.md).

A következő szakaszok ismertetik a beállítás csak felhőalapú felhasználók Workday és az Azure AD közötti kapcsolat.

> [!IMPORTANT]
> Ha csak felhőalapú ki kell építeni az Azure AD-felhasználó, és nem a helyszíni Active Directory csak az alábbi eljárást követve.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. lépés: Az Azure AD létesítési összekötő alkalmazás hozzáadása, és a Workday kapcsolat létrehozása

**Munkanapok konfigurálása az Azure Active Directory kiépítésére, csak felhőalapú felhasználók számára:**

1.  Nyissa meg a következőt: <https://portal.azure.com>.

2.  Válassza ki a bal oldali navigációs sáv **Azure Active Directoryban**

3.  Válassza ki **vállalati alkalmazások**, majd **összes alkalmazás**.

4.  Válassza ki **alkalmazás hozzáadása**, majd válassza ki a **összes** kategóriát.

5.  Keresse meg **az Azure AD kiépítésére Workday**, és adja hozzá az alkalmazás a gyűjteményből.

6.  Az alkalmazás hozzáadása és az alkalmazás részletei képernyőn látható, jelölje be az után **kiépítés**

7.  Módosítsa a **kiépítés** **mód** való **automatikus**

8.  Fejezze be a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazda felhasználóneve** – adja meg a Workday-integrációs rendszerfiók felhasználóneve fűzött bérlői tartománynévvel. Hasonlóan kell kinéznie: username@contoso4

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday web services végpontnak a bérlő számára. Hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4, ahol contoso4 helyére a megfelelő bérlő neve és wd3-impl cseréli azokat a megfelelő környezet karakterlánc. Ha az URL-cím nem ismert, adjon dolgozhatnak a Workday integrációs partner vagy támogatási képviselőjére határozza meg a helyes URL-cím használatára.

   * **E-mailben értesítést –** meg e-mail címét, és jelölje be az "e-mail küldési hiba esetén" jelölőnégyzetet.

   * Kattintson a **kapcsolat tesztelése** gombra.

   * Ha a kapcsolat ellenőrzése sikeres, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday URL-cím és a hitelesítő adatok érvényesek a Workday.


### <a name="part-2-configure-attribute-mappings"></a>2. lépés: Konfigurálja a attribútum-leképezésekhez 

Ebben a szakaszban konfigurál, hogyan felhasználói adatáramlás a WORKDAY-ből az Azure Active Directory csak felhőalapú felhasználók számára.

1.  A kiépítés lapon a **hozzárendelések**, kattintson a **munkavállalók szinkronizálása az Azure AD**.

2.   Az a **forrás objektum hatóköre** mezőjét, kiválaszthatja, mely a Workday felhasználócsoportokhoz üzembe helyezéséhez az Azure AD Attribútumalapú szűrők csoportja meghatározásával hatókörében kell lennie. Az alapértelmezett hatóköre "minden felhasználó a Workday". Példa szűrők:

   * Példa: Hatókör felhasználók munkavégző azonosítók 1000000 és 2000000 között

      * Attribútum: WorkerID

      * Operátor: Reguláris KIFEJEZÉSSEL egyező

      * Érték: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Példa: Csak függő dolgozó munkatársak és nem rendszeres alkalmazottak

      * Attribútum: ContingentID

      * Operátort: Nem NULL

3.  Az a **cél objektum műveletek** mezőjét, szűrheti az globálisan, milyen műveleteket kell elvégezni az Azure AD használata engedélyezett. **Hozzon létre** és **frissítés** leggyakoribb.

4.  Az a **attribútum-hozzárendelések** szakaszban meghatározhatja egyes Workday attribútumok az Active Directory-attribútumok hozzárendelését.

5. Kattintson az egy meglévő címtárattribútum-leképezésben frissíti a **új leképezés hozzáadása** hozzáadása új leképezéseket a képernyő alján. Az egyes címtárattribútum-leképezésben támogatja ezeket a tulajdonságokat:

   * **Leképezés típusa**

      * **Közvetlen** – a Workday attribútum értéke ír az AD attribútum, módosítások nélküli

      * **Állandó** -statikus, állandó karakterlánc-érték írni az AD-attribútum

      * **Kifejezés** – lehetővé teszi egy egyéni érték írható AD attribútum egy vagy több Workday-attribútumok alapján. [További információk: Ez a cikk a kifejezések](active-directory-saas-writing-expressions-for-attribute-mappings.md).

   * **Adatforrás-attribútum** -a felhasználói attribútum a WORKDAY-ből. Ha a keresett attribútum nincs jelen, lásd: [testreszabása a Workday felhasználói attribútumok listáját](#customizing-the-list-of-workday-user-attributes).

   * **Alapértelmezett érték** – nem kötelező. A forrásattribútum nem üres érték tartozik, ha a leképezés fog kiírni, ez az érték helyett.
            Általános beállítások mellett akkor hagyja üresen a mezőt.

   * **Cél attribútumának** – a felhasználói attribútum az Azure ad-ben.

   * **Ezzel az attribútummal objektumok megfelelő** – függetlenül attól, ez a leképezés használjon egyedi azonosítására a felhasználók Workday és az Azure AD között. Ez általában be van állítva az munkavégző Azonosítót tartalmazó mezőt a Workday, amely általában hozzárendelve az alkalmazottak az ID attribútum (új), vagy a bővítmény attribútum az Azure ad-ben.

   * **Megfelelő sorrend** – több egyező attribútumok állítható be. Ha több, kiértékelésük, ez a mező által megadott sorrendben. Amint a program egyezést talál, további megfelelő attribútumok kiértékelése.

   * **Ez a leképezés alkalmazása**

     * **Mindig** – Ez a leképezés alkalmazni mind a felhasználó létrehozása és a műveletek frissítése

     * **Csak létrehozásakor** – Ez a leképezés csak a felhasználó létrehozási műveletek alkalmazása

6. A hozzárendelések mentéséhez kattintson **mentése** attribútum leképezési szakasz elején.

### <a name="part-3-start-the-service"></a>3. lépés: A szolgáltatás indítása
1-2 részek elvégzése után, megkezdheti a létesítési szolgáltatás.

1.  Az a **kiépítési** lapján állítsa be a **kiépítési állapot** való **a**.

2. Kattintson a **Save** (Mentés) gombra.

3. Elindítja a kezdeti szinkronizálás, ami eltarthat, attól függően, hogy hány felhasználó szerepelnek a Workday óra változó számú.

4. Az egyes szinkronizálási események lehet megtekinteni a **naplók** fülre. **[Olvassa el a felügyeleti naplók az üzembe helyezési jelentéskészítés – útmutató részletes utasításokat olvashat](active-directory-saas-provisioning-reporting.md)**

5. Fejeződött be, akkor fog kiírni, összefoglaló jelentést a **kiépítési** lapon, a lent látható módon.


## <a name="configuring-writeback-of-email-addresses-to-workday"></a>Az e-mail címet a Workday visszaírási konfigurálásával
Kövesse az alábbi utasításokat a felhasználó e-mail címet az Azure Active Directoryból a Workday visszaírása konfigurálásához.

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. lépés: Az üzembe helyezési összekötő alkalmazás hozzáadása, és a Workday kapcsolat létrehozása

**Az Active Directory kiépítésére konfigurálása a Workday:**

1.  Nyissa meg a következőt: <https://portal.azure.com>

2.  Válassza ki a bal oldali navigációs sáv **Azure Active Directoryban**

3.  Válassza ki **vállalati alkalmazások**, majd **összes alkalmazás**.

4.  Válassza ki **alkalmazás hozzáadása**, majd jelölje be a **összes** kategóriát.

5.  Keresse meg **Workday visszaírási**, és adja hozzá az alkalmazás a gyűjteményből.

6.  Az alkalmazás hozzáadása és az alkalmazás részletei képernyőn látható, jelölje be az után **kiépítés**

7.  Módosítsa a **kiépítés** **mód** való **automatikus**

8.  Fejezze be a **rendszergazdai hitelesítő adataival** szakasz az alábbiak szerint:

   * **Rendszergazda felhasználóneve** – adja meg a Workday-integrációs rendszerfiók felhasználóneve fűzött bérlői tartománynévvel. Hasonlóan kell kinéznie: username@contoso4

   * **Rendszergazdai jelszó –** adja meg a jelszót a Workday-integrációs rendszer fiók

   * **A bérlői URL-cím –** adja meg az URL-címet a Workday web services végpontnak a bérlő számára. Hasonlóan kell kinéznie: https://wd3-impl-services1.workday.com/ccx/service/contoso4, ahol contoso4 helyére a megfelelő bérlő neve és wd3-impl cseréli a megfelelő környezet karakterlánc (ha szükséges).

   * **E-mailben értesítést –** meg e-mail címét, és jelölje be az "e-mail küldési hiba esetén" jelölőnégyzetet.

   * Kattintson a **kapcsolat tesztelése** gombra. Ha a kapcsolat ellenőrzése sikeres, kattintson a **mentése** gombra az oldal tetején. Ha nem sikerül, ellenőrizze, hogy a Workday URL-cím és a hitelesítő adatok érvényesek a Workday.


### <a name="part-2-configure-attribute-mappings"></a>2. lépés: Konfigurálja a attribútum-leképezésekhez 


Ebben a szakaszban konfigurál, hogy felhasználói adatáramlás a WORKDAY-ből az Active Directory.

1.  A kiépítés lapon a **hozzárendelések**, kattintson a **szinkronizálás Azure AD-felhasználók Workday**.

2.  Az a **forrás objektum hatóköre** mezőjét, igény szerint szűrheti az Azure Active Directory mely felhasználócsoportokhoz kell rendelkeznie az e-mail címmel visszaírását Workday. Az alapértelmezett hatóköre "az Azure AD-minden felhasználó". 

3.  Az a **attribútum-hozzárendelések** szakaszban meghatározhatja egyes Workday attribútumok az Active Directory-attribútumok hozzárendelését. Alapértelmezés szerint az e-mail cím társítás van. Azonban a hozzá tartozó azonosító frissíteni kell, hogy a Workday a megfelelő bejegyzések az Azure AD-ben olyan felhasználókkal vesse össze. Egy népszerű egyező módja, hogy szinkronizálja a Workday munkavégző vagy extensionAttribute1-15 alkalmazott azonosítója az Azure ad-ben, majd ezt az attribútumot az Azure AD vissza a Workday felhasználók kereséséhez.

4.  A hozzárendelések mentéséhez kattintson **mentése** attribútum leképezési szakasz elején.

### <a name="part-3-start-the-service"></a>3. lépés: A szolgáltatás indítása
1-2 részek elvégzése után, megkezdheti a létesítési szolgáltatás.

1.  Az a **kiépítési** lapján állítsa be a **kiépítési állapot** való **a**.

2. Kattintson a **Save** (Mentés) gombra.

3. Elindítja a kezdeti szinkronizálás, ami eltarthat, attól függően, hogy hány felhasználó szerepelnek a Workday óra változó számú.

4. Az egyes szinkronizálási események lehet megtekinteni a **naplók** fülre. **[Olvassa el a felügyeleti naplók az üzembe helyezési jelentéskészítés – útmutató részletes utasításokat olvashat](active-directory-saas-provisioning-reporting.md)**

5. Fejeződött be, akkor fog kiírni, összefoglaló jelentést a **kiépítési** lapon, a lent látható módon.


## <a name="customizing-the-list-of-workday-user-attributes"></a>A Workday felhasználói attribútumok listáját testreszabása
Alkalmazások telepítése az Active Directory és az Azure AD egyaránt tartalmazza az alapértelmezett Workday felhasználói attribútumok listáját a munkanapok közül választhat. Azonban a fájlok nem átfogó. Munkanapok támogatja a több száz lehetséges felhasználói attribútumok, amelyek lehet standard vagy a Workday-bérlő egyedi. 

Az Azure AD szolgáltatás kiépítését támogatja a lista vagy a Workday attribútum olyan bármely felfedett attribútumok testreszabása a [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) az emberi erőforrások API működését.

Ehhez az szükséges, használjon [Workday Studio](https://community.workday.com/studio-download) kinyerni az XPath kifejezések, amely a használni kívánt attribútumokat jelöl, és adja őket a létesítési konfiguráció a speciális Attribútumszerkesztő az Azure portálon.

**A Workday felhasználói attribútum az XPath kifejezések beolvasása:**

1. Töltse le és telepítse [Workday Studio](https://community.workday.com/studio-download). Szüksége lesz egy Workday közösségi fiókot a telepítő eléréséhez.

2. Töltse le a Workday Human_Resources WDSL fájlt az URL-cím: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Indítsa el a Workday Studiót.

4. A parancssávon válassza ki a **Workday > teszt webszolgáltatás a tesztelő** lehetőséget.

5. Válassza ki **külső**, és válassza ki a letöltött Human_Resources WSDL-fájl a 2.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio1.PNG)

6. Állítsa be a **hely** mezőről `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, de cseréje "IMPL-CC" az aktuális példánytípus és "BÉRLŐBEN" a valódi bérlő neve.

7. Állítsa be **művelet** való **Get_Workers**

8.  Kattintson a kis **konfigurálása** hivatkozás alatt a kérelem/válasz ablaktábla a Workday használandó hitelesítő adatok beállításához. Ellenőrizze **hitelesítési**, és a Workday-integrációs rendszerfiók adja meg a felhasználónevet és jelszót. Ügyeljen arra, hogy a felhasználó nevét, name@tenant, és hagyja a **WS-biztonsági UsernameToken** kiválasztott beállítás.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio2.PNG)

9. Kattintson az **OK** gombra.

10. A **kérelem** ablaktáblán, illessze be az XML-fájl, az alábbi és beállított **Employee_ID** a Workday-bérlői valós felhasználónak alkalmazott azonosítója. Válasszon ki egy felhasználót, hogy a attribútummal rendelkezik, hogy szeretné-e kibontása feltöltve.

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
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```
 
11. Kattintson a **kérés küldése** (zöld nyílra) hajtsa végre a parancsot. Ha sikeres, a válasz meg kell jelennie a **válasz** ablaktáblán. Ellenőrizze az annak érdekében, hogy a megadott felhasználói azonosító adatot tartalmaz, és nem hiba.

12. Ha sikeres, másolja az XML az **válasz** ablaktáblán, és mentse egy XML-fájlt.

13. Válassza a a parancs sáv a Workday Studio **fájl > fájl megnyitása...**  , és nyissa meg az imént mentett XML-fájlt. Ekkor megnyílik a Workday Studio XML-szerkesztőben.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio3.PNG)

14. A fájl fában járja végig **/env:Envelope > env > wd:Get_Workers_Response > wd:Response_Data > wd:Worker** a felhasználói adatok kereséséhez. 

15. A **wd:Worker**, a hozzáadni kívánt attribútumot, és válassza ki azt.

16. Másolja ki a kijelölt attribútum az XPath-kifejezés az **dokumentum elérési útját** mező.

17. Távolítsa el a **/env:Envelope / env / wd:Get_Workers_Response / wd:Response_Data /** előtag a másolt kifejezésben. 

18. Ha a másolt kifejezés utolsó elemének a csomópont (Példa: "/ wd:Birth_Date"), majd fűzze **/text()** kifejezés végén. Erre nincs szükség, ha az utolsó elem attribútuma (Példa: "/@wd:type").

19. Az eredmény hasonlót kell `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Ez az az Azure portált fogja másolni.


**Az üzembe helyezési konfigurációját a Workday egyéni felhasználói attribútum hozzáadása:**

1. Indítsa el a [Azure-portálon](https://portal.azure.com), és navigáljon a kiépítés szakaszában a Workday-kiépítés alkalmazás, ez az oktatóanyag a korábban ismertetett módon.

2. Állítsa be **kiépítési állapot** való **ki**, és válassza ki **mentése**. Ez megakadályozza a módosítások életbe léptetéséhez, csak akkor, amikor készen áll.

3. A **hozzárendelések**, jelölje be **OnPremises dolgozók szinkronizálása** (vagy **munkavállalók szinkronizálása az Azure AD**).

4. A következő képernyő alján görgessen, és válassza ki **speciális beállítások megjelenítése**.

5. Válassza ki **Szerkesztés attribútumlista a Workday**.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD1.PNG)

6. A beviteli mezők esetén a attribútum lista alján görgessen.

7. A **neve**, adja meg az attribútum megjelenítendő nevét.

8. A **típus**, válassza ki a megfelelő megfelelő az attribútum típusa (**karakterlánc** leggyakrabban).

9. A **API kifejezés**, adja meg a Workday Studióból másolta az XPath-kifejezés. Példa: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Válassza ki **attribútum hozzáadása**.

    ![Workday Studio](./media/active-directory-saas-workday-inbound-tutorial/WDstudio_AAD2.PNG)

11. Válassza ki **mentése** fenti, majd **Igen** a párbeszédpanelre. Zárja be a attribútum leképezési képernyő, ha folyamatban.

12. Vissza a fő **kiépítési** lapon jelölje be **OnPremises dolgozók szinkronizálása** (vagy **munkavállalók szinkronizálása az Azure AD**) újra.

13. Válassza ki **új leképezés hozzáadása**.

14. Ekkor megjelenik az új attribútumot a **forrásattribútum** listája.

15. Adja hozzá az új attribútum társítás tetszés szerint.

16. Ha elkészült, akkor ne felejtse el beállítása **kiépítési állapot** vissza **a** és mentéséhez.


## <a name="known-issues"></a>Ismert problémák

* Futtatásakor a **Add-ADSyncAgentAzureActiveDirectoryConfiguration** Powershell parancsot, jelenleg egy ismert probléma globális rendszergazdai hitelesítő adatokkal nem működik, ha azok az egyéni tartománynév (Példa: admin@contoso.com) . A probléma megoldásához hozzon létre, és egy globális rendszergazdai fiókját használja az Azure AD egy onmicrosoft.com tartománnyal (Példa: admin@contoso.onmicrosoft.com).

* Egy vizsgálati naplóit Azure AD-bérlő található, az Európai Unió nem jelenik meg az előző problémát sikerült megoldani. Azonban további ügynök konfigurálni kell a Azure AD-bérlőt az EU-ban. További információkért lásd: [3. rész: a helyszíni-szinkronizálási ügynök konfigurálása](#Part 3: Configure the on-premises synchronization agent)


## <a name="additional-resources"></a>További források
* [Oktatóanyag: Az egyszeri bejelentkezés közötti Workday és az Azure Active Directory konfigurálása](active-directory-saas-workday-tutorial.md)
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>További lépések

* [Ismerje meg, tekintse át a naplók és jelentések készítése a kiépítés tevékenység](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
