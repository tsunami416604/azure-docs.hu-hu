---
title: 'Azure AD Connect szinkronizálás: Konfigurációmódosítás az Azure AD Connect szinkronizálásában'
description: Végigvezeti, hogyan módosíthatja a konfigurációt az Azure AD Connect szinkronizálásában.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d77882817934d5ad98f16965aeb9dc246931c495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261163"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect szinkronizálás: Módosítsa az alapértelmezett konfigurációt
Ez a cikk célja, hogy végigvezeti, hogyan módosíthatja az alapértelmezett konfiguráció az Azure Active Directory (Azure AD) Connect szinkronizálás. Néhány gyakori forgatókönyv lépéseit ismerteti. Ezzel a tudással, akkor képesnek kell lennie arra, hogy egyszerű változtatásokat a saját konfiguráció alapján a saját üzleti szabályokat.

> [!WARNING]
> Ha módosítja az alapértelmezett out-of-box szinkronizálási szabályokat, majd ezeket a módosításokat felülírja a következő alkalommal, amikor az Azure AD Connect frissül, ami váratlan és valószínűleg nem kívánt szinkronizálási eredményeket eredményez.
>
> Az alapértelmezett beépített szinkronizálási szabályok ujjlenyomattal rendelkeznek. Ha módosítja ezeket a szabályokat, az ujjlenyomat már nem egyezik. Előfordulhat, hogy a jövőben problémák merülnek fel, amikor megpróbálja alkalmazni az Azure AD Connect új kiadását. Csak a cikkben leírt módon módosítsa a cikket.

## <a name="synchronization-rules-editor"></a>Szinkronizálási szabályok szerkesztője
A Szinkronizálási szabályok szerkesztője az alapértelmezett konfiguráció megtekintésére és módosítására szolgál. Az **Azure AD Connect** csoport **Start** menüjében található.  
![Start menü a Szinkronizálási szabályszerkesztővel](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Amikor megnyitja a szerkesztőt, megjelennek az alapértelmezett beépített szabályok.

![Szabályszerkesztő szinkronizálása](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigálás a szerkesztőben
A szerkesztő tetején található legördülő lista segítségével gyorsan megtalálhatja az adott szabályt. Ha például meg szeretné tekinteni azokat a szabályokat, amelyekben a proxyAddresses attribútum szerepel, a legördülő lista a következőre módosítható:  
![SRE szűrés](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
A szűrés visszaállításához és egy új konfiguráció betöltéséhez nyomja le az F5 billentyűt a billentyűzeten.

A jobb felső sarokban található az **Új szabály hozzáadása** gomb. Ezzel a gombbal saját egyéni szabályt hozhat létre.

Az alján vannak gombok a kijelölt szinkronizálási szabályra való reagáláshoz. **A Szerkesztés** és **a Törlés** azt teszi, amit elvár tőlük. **Az exportálás** egy PowerShell-parancsfájlt hoz létre a szinkronizálási szabály újbóli létrehozásához. Ezzel az eljárással áthelyezheti a szinkronizálási szabályt egyik kiszolgálóról a másikra.

## <a name="create-your-first-custom-rule"></a>Az első egyéni szabály létrehozása
A leggyakoribb változások az attribútumfolyamatok. Előfordulhat, hogy a forráskönyvtárban lévő adatok nem egyeznek meg az Azure AD-ben. Ebben a szakaszban a példában győződjön meg arról, hogy a felhasználó megadott neve mindig a *megfelelő esetben*.

### <a name="disable-the-scheduler"></a>Az ütemező letiltása
Az [ütemező](how-to-connect-sync-feature-scheduler.md) alapértelmezés szerint 30 percenként fut. Győződjön meg arról, hogy nem indul el, miközben módosítja és elhárítja az új szabályokat. Az ütemező ideiglenes letiltásához indítsa `Set-ADSyncScheduler -SyncCycleEnabled $false`el a PowerShellt, és futtassa a programot.

![Az ütemező letiltása](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>A szabály létrehozása
1. Kattintson **az Új szabály hozzáadása gombra.**
2. A **Leírás** lapon adja meg a következőket:  
   ![Bejövő szabályszűrés](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Név**: Adjon a szabálynak leíró nevet.
   * **Leírás**: Adjon némi pontosítást, hogy valaki más is megértse, mire való a szabály.
   * **Csatlakoztatott rendszer**: Ez az a rendszer, amelyben az objektum megtalálható. Ebben az esetben válassza az **Active Directory-összekötő lehetőséget.**
   * **Csatlakoztatott rendszer/metaverzum objektumtípusa:** Válassza ki **a Felhasználó** és **a Személy**lehetőséget.
   * **Hivatkozás típusa**: Módosítsa ezt az értéket **Csatlakozás értékre.**
   * **Elsőbbség**: Egyedi értéket adjon meg a rendszerben. Az alacsonyabb numerikus érték magasabb prioritást jelez.
   * **Címke**: Hagyja üresen. Csak a Microsoft beépített szabályainak kell ezt a mezőt értékkel feltölteni.
3. A **Hatókör szűrő** lapon adja meg **a givenName ISNOTNULL**( keresztnév .  
   ![Bejövő szabályhatókör szűrője](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Ez a szakasz határozza meg, hogy a szabály mely objektumokra vonatkozzon. Ha üresen marad, a szabály az összes felhasználói objektumra vonatkozik. Ez azonban magában foglalja a konferenciatermeket, a szolgáltatásfiókokat és más nem személyeket használó objektumokat.
4. A **Join rules (Illesztési szabályok)** lapon hagyja üresen a mezőt.
5. Az **Átalakítások** lapon módosítsa a **FlowType típust** **kifejezésre.** A **Cél attribútum csoportban**válassza **a givenName lehetőséget.** A **Forrás**mezőbe írja be a **PCase([givenName]) értéket.**
   ![Bejövő szabályátalakítások](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   A szinkronizálási motor a funkció nevét és az attribútum nevét is megkülönböztetést kér a kis- és nagybetűk között. Ha valami rosszat ír be, a szabály hozzáadásakor figyelmeztetés jelenik meg. Mentheti és folytathatja, de újra meg kell nyitnia és ki kell javítania a szabályt.
6. A szabály mentéséhez kattintson a **Hozzáadás** gombra.

Az új egyéni szabálynak láthatónak kell lennie a rendszer többi szinkronizálási szabályával.

### <a name="verify-the-change"></a>A módosítás ellenőrzése
Ezzel az új módosítással meg kell győződnie arról, hogy a várt módon működik, és nem okoz hibákat. A megtartott objektumok számától függően ezt a lépést kétféleképpen teheti meg:

- Teljes szinkronizálás futtatása az összes objektumon.
- Egyetlen objektumon futtasson előnézetet és teljes szinkronizálást.

Nyissa meg a **Szinkronizálási szolgáltatást** a **Start** menüből. Az ebben a szakaszban található lépések mind ebben az eszközben találhatók.

**Teljes szinkronizálás az összes objektumon**  

   1. Válassza az **Összekötők** lehetőséget a tetején. Azonosítsa az előző szakaszban módosított összekötőt (ebben az esetben az Active Directory tartományi szolgáltatásokat), és jelölje ki. 
   2. A **Műveletek területen**válassza a Futtatás **lehetőséget.**
   3. Válassza **a Teljes szinkronizálás**lehetőséget, majd az **OK**gombot.
   ![Teljes szinkronizálás](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Az objektumok most frissülnek a metaverzumban. Ellenőrizze a módosításokat a metaverzumban lévő objektummal.

**Előnézet és teljes szinkronizálás egyetlen objektumon**  

   1. Válassza az **Összekötők** lehetőséget a tetején. Azonosítsa az előző szakaszban módosított összekötőt (ebben az esetben az Active Directory tartományi szolgáltatásokat), és jelölje ki.
   2. Válassza a **Keresési összekötő tér ben**lehetőséget. 
   3. A **Hatókör** segítségével megkeresheti azt az objektumot, amelyet a módosítás teszteléséhez kíván használni. Jelölje ki az objektumot, és kattintson az **Előnézet gombra.** 
   4. Az új képernyőn válassza az **Előzetes véglegesítés lehetőséget.**  
   ![Véglegesítés előnézete](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   A változás most elkötelezett a metaverse.

**Az objektum megtekintése a metaverzumban**  

1. Válasszon néhány mintaobjektumot, és győződjön meg arról, hogy az érték várható, és hogy a szabály alkalmazva van. 
2. Felülről válassza a **Metaverzum-keresés lehetőséget.** Adja hozzá a megfelelő objektumok megkereséséhez szükséges szűrőt. 
3. A keresési eredményből nyisson meg egy objektumot. Tekintse meg az attribútumértékeket, és ellenőrizze a **Szabályok szinkronizálása** oszlopban, hogy a szabály a várt módon lett-e alkalmazva.  
![Keresés a metaverzumban](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Az ütemező engedélyezése
Ha minden a várt módon történik, újra engedélyezheti az ütemezőt. A PowerShell `Set-ADSyncScheduler -SyncCycleEnabled $true`ben futtassa a futtassa a futtassa a futtassa a

## <a name="other-common-attribute-flow-changes"></a>Egyéb gyakori attribútumfolyamat-változások
Az előző szakasz ban ismertetett, hogyan lehet módosítani egy attribútumfolyamatot. Ebben a szakaszban néhány további példa is rendelkezésre áll. A szinkronizálási szabály létrehozásának lépései rövidítve vannak, de az előző szakaszban található teljes lépések találhatók.

### <a name="use-an-attribute-other-than-the-default"></a>Az alapértelmezettattribútumtól eltérő attribútum használata
Ebben a Fabrikam-forgatókönyvben van egy erdő, ahol a helyi ábécét használják a keresztnév, a vezetéknév és a megjelenítendő név. Ezeknek az attribútumoknak a latin karakterábrázolása a bővítményattribútumokban található. Globális címlista létrehozásához az Azure AD és az Office 365, a szervezet szeretné használni ezeket az attribútumokat helyette.

Az alapértelmezett konfigurációesetén a helyi erdőből származó objektum így néz ki:  
![Attribútumfolyamat 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Ha más attribútumfolyamatokkal szeretne szabályt létrehozni, tegye a következőket:

1. Nyissa meg a **Szinkronizálási szabályok szerkesztőjét** a **Start** menüből.
2. Ha **a Bejövő még** mindig balra van kijelölve, kattintson az Új szabály **hozzáadása** gombra.
3. Adjon nevet és leírást a szabálynak. Válassza ki a helyszíni Active Directory-példányt és a megfelelő objektumtípusokat. A **Hivatkozás típusa csoportban**válassza a **Csatlakozás**lehetőséget. A **Prioritás**területen válasszon olyan számot, amelyet más szabály nem használ. A beépített szabályok 100-zal kezdődnek, így az 50 érték ebben a példában használható.
  ![Attribútumfolyamat 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Hagyja üresen **a hatókörszűrőt.** (Ez azt illeti, az erdő összes felhasználói objektumára vonatkoznia kell.)
5. Hagyja üresen **a Csatlakozás szabályokat.** (Ez azt, hogy hagyja, hogy a beépített szabály kezelje az illesztéseket.)
6. Az **Átalakítások**alkalmazásban hozza létre a következő folyamatokat:  
  ![Attribútumfolyamat 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. A szabály mentéséhez kattintson a **Hozzáadás** gombra.
8. Nyissa meg a **Szinkronizálási szolgáltatáskezelő t.** Az **összekötőken**jelölje ki azt az összekötőt, amelyhez hozzáadta a szabályt. Válassza a **Futtatás**lehetőséget, majd a **Teljes szinkronizálás lehetőséget.** A teljes szinkronizálás újraszámítja az összes objektumot az aktuális szabályok használatával.

Ez az eredmény ugyanahhoz az objektumhoz ehhez az egyéni szabályhoz:  
![Attribútumfolyamat 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Jellemzők hossza
A karakterlánc-attribútumok alapértelmezés szerint indexelhetők, a maximális hossz 448 karakter. Ha olyan karakterlánc-attribútumokkal dolgozik, amelyek többet tartalmazhatnak, ügyeljen arra, hogy a következőket foglalja bele az attribútumfolyamatba:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>A userPrincipalSuffix módosítása
Az Active Directory userPrincipalName attribútumát a felhasználók nem mindig ismerik, és előfordulhat, hogy nem alkalmas bejelentkezési azonosítóként. Az Azure AD Connect szinkronizálási telepítővarázslójával másik attribútumot választhat – például *a leveleket.* De bizonyos esetekben az attribútumot ki kell számítani.

A Contoso vállalat például két Azure AD-könyvtárral rendelkezik, amelyek közül az egyik éles, a másik pedig tesztelésre szolgál. Azt szeretnék, hogy a tesztbérlőben lévő felhasználók egy másik utótagot használjanak a bejelentkezési azonosítóban:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Ebben a kifejezésben vegyen ki @-sign mindent az elsőből (Word) és egy rögzített karakterlánccal fűzze össze.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Többértékű attribútum átalakítása egyetlen értékké
Az Active Directory egyes attribútumai többértékűek a sémában, még akkor is, ha egyértékűnek tűnnek az Active Directory – felhasználók és számítógépek beépülő modulban. Ilyen például a leírás attribútuma:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Ebben a kifejezésben, ha az attribútumnak van értéke, vegye be az első elemet (*Item*) az attribútumban, távolítsa el a kezdő és záró szóközöket (*Trim*), majd tartsa meg az első 448 karaktert (*balra*) a karakterláncban.

### <a name="do-not-flow-an-attribute"></a>Attribútum ot ne folytasson
A szakasz forgatókönyvének hátteréről az [Attribútumfolyamat vezérlése](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process)című témakörben található.

Kétféleképpen lehet nem folyatni egy attribútumot. Az első a [kijelölt attribútumok eltávolításával](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)a telepítővarázsló valamezenkeresztül történik. Ez a beállítás akkor működik, ha még soha nem szinkronizálta az attribútumot. Azonban ha elkezdte szinkronizálni ezt az attribútumot, és később távolítsa el ezzel a funkcióval, a szinkronizálási motor leáll az attribútum kezelése, és a meglévő értékek maradnak az Azure AD-ben.

Ha el szeretné távolítani egy attribútum értékét, és meg szeretné győződni arról, hogy az nem folyik a jövőben, létre kell hoznia egy egyéni szabályt.

Ebben a Fabrikam forgatókönyvben rájöttünk, hogy a felhővel szinkronizált attribútumok némelyikének nem kell ott lennie. Azt szeretnénk, hogy megbizonyosodjon arról, ezek az attribútumok törlődnek az Azure AD.We want to make sure these attributes are removed from Azure AD.  
![Hibás kiterjesztési attribútumok](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Hozzon létre egy új bejövő szinkronizálási szabályt, és feltöltse a leírást.
  ![Leírások](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Attribútumfolyamatok létrehozása **kifejezéssel** a **FlowType kifejezéssel** és a **AuthoritativeNull** for **Source**értékkel. A literális **AuthoritativeNull** azt jelzi, hogy az értéknek üresnek kell lennie a metaverzumban, még akkor is, ha egy alacsonyabb prioritású szinkronizálási szabály megpróbálja felnagyítja az értéket.
  ![Kiterjesztési attribútumok átalakítása](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Mentse a szinkronizálási szabályt. Indítsa el a **Szinkronizálási szolgáltatást,** keresse meg az összekötőt, válassza a **Futtatás**lehetőséget, majd a **Teljes szinkronizálás lehetőséget.** Ez a lépés újraszámítja az összes attribútumfolyamatot.
4. Ellenőrizze, hogy a tervezett módosítások exportálásra kerülnek-e az összekötőtérben való kereséssel.
  ![Szakaszos törlés](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Szabályok létrehozása a PowerShell használatával
A szinkronizálási szabályszerkesztő használata akkor működik, ha csak néhány módosítást kell végrehajtania. Ha számos módosítást kell végrehajtania, a PowerShell jobb megoldás lehet. Egyes speciális funkciók csak a PowerShell érhető el.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>A PowerShell-parancsfájl beszereznie egy beépített szabályhoz
A beépített szabályt létrehozó PowerShell-parancsfájl megtekintéséhez jelölje ki a szabályt a szinkronizálási szabályok szerkesztőjében, és kattintson az **Exportálás gombra.** Ez a művelet adja meg a PowerShell-parancsfájlt, amely létrehozta a szabályt.

### <a name="advanced-precedence"></a>Speciális elsőbbség
A beépített szinkronizálási szabályok 100-as prioritási értékkel kezdődnek. Ha sok erdővel rendelkezik, és sok egyéni módosítást kell végrehajtania, akkor előfordulhat, hogy a 99 szinkronizálási szabályok nem elegendőek.

Utasíthatja a szinkronizálási motort, hogy a beépített szabályok előtt további szabályokat szeretne beszúrni. A viselkedés eléréséhez kövesse az alábbi lépéseket:

1. Jelölje meg az első beépített szinkronizálási szabályt (**Be az AD-felhasználó csatlakozásából)** a szinkronizálási szabályok szerkesztőjében, és válassza az **Exportálás**lehetőséget. Másolja az SR-azonosító értékét.  
![PowerShell a módosítás előtt](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Hozza létre az új szinkronizálási szabályt. A szinkronizálási szabályok szerkesztőjével létrehozhatja azt. Exportálja a szabályt egy PowerShell-parancsfájlba.
3. A **Prioritás előtte**tulajdonságban szúrja be az Azonosító értéket a beépített szabályból. Állítsa a **prioritást** **0-ra.** Győződjön meg arról, hogy az Azonosító attribútum egyedi, és hogy nem használja fel újra a GUID egy másik szabály. Győződjön meg arról is, hogy az **ImmutableTag** tulajdonság nincs beállítva. Ezt a tulajdonságot csak beépített szabályhoz szabad beállítani.
4. Mentse a PowerShell-parancsfájlt, és futtassa azt. Az eredmény az, hogy az egyéni szabály 100-as prioritási értékkel van hozzárendelve, és az összes többi beépített szabály növekszik.  
![PowerShell a módosítás után](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Számos egyéni szinkronizálási szabályt használhat, ha szükség esetén ugyanazt a **Prioritást használja.**

## <a name="enable-synchronization-of-usertype"></a>UserType szinkronizálásának engedélyezése
Az Azure AD Connect támogatja a **UserType** attribútum szinkronizálását az 1.1.524.0-s és újabb verziójú **felhasználói** objektumokhoz. Konkrétabban a következő változtatásokat vezették be:

- A séma az objektum típusa **Felhasználó** az Azure AD-összekötő ki van terjesztve a UserType attribútum, amely a karakterlánc, és egyértékű.
- A metaverzumban lévő **Person** objektumtípus sémája kiterjesztve lesz a UserType attribútummal, amely karakterlánc típusú és egyértékű.

Alapértelmezés szerint a UserType attribútum nincs engedélyezve a szinkronizáláshoz, mert nincs megfelelő UserType attribútum a helyszíni Active Directoryban. A szinkronizálást manuálisan kell engedélyeznie. Ezt megelőzően figyelembe kell vennie az Azure AD által kényszerített alábbi viselkedést:

- Az Azure AD csak két értéket fogad el a UserType attribútumhoz: **tag** és **vendég.**
- Ha a UserType attribútum nincs engedélyezve a szinkronizáláshoz az Azure AD Connectben, a címtár-szinkronizálással létrehozott Azure AD-felhasználók a UserType attribútumot **tagra**állították volna.
- Az Azure AD nem teszi lehetővé a UserType attribútum meglévő Azure AD-felhasználók által módosítani az Azure AD Connect. Csak az Azure AD-felhasználók létrehozása során állítható be, és a [Powershellen keresztül módosítható.](/powershell/module/azuread/set-azureaduser?view=azureadps-2.0)

A UserType attribútum szinkronizálásának engedélyezése előtt először el kell döntenie, hogy az attribútum hogyan származik a helyszíni Active Directoryból. A leggyakoribb megközelítések a következők:

- Jelöljön ki egy nem használt helyszíni AD attribútumot (például az ExtensionAttribute1 attribútumot), amelyet forrásattribútumként kell használni. A kijelölt helyszíni AD attribútumnak **karakterlánc**típusúnak kell lennie, egyértékűnek kell lennie, és tartalmaznia kell a **Tag** vagy **vendég**értéket. 

    Ha ezt a módszert választja, meg kell győződnie arról, hogy a kijelölt attribútum a megfelelő értékkel van feltöltve a helyszíni Active Directory ban lévő összes meglévő felhasználói objektumhoz, amelyek szinkronizálva vannak az Azure AD-vel, mielőtt engedélyezne a UserType attribútum szinkronizálását. .

- Másik lehetőségként a UserType attribútum értékét más tulajdonságokból is levezetheti. Például az összes felhasználót **vendégként** szeretné szinkronizálni, ha a helyszíni AD <em>@partners.fabrikam123.org</em>userPrincipalName attribútumuk tartományi részlel végződik. 

    Mint korábban említettük, az Azure AD Connect nem teszi lehetővé a UserType attribútum meglévő Azure AD-felhasználók által módosítani az Azure AD Connect. Ezért győződjön meg arról, hogy a logika úgy döntött, konzisztens azzal, hogy a UserType attribútum már konfigurálva van az összes meglévő Azure AD-felhasználók a bérlőben.

A UserType attribútum szinkronizálásának engedélyezéséhez a következő lépések foglalhatók össze:

1.  Tiltsa le a szinkronizálási ütemezőt, és ellenőrizze, hogy nincs-e folyamatban szinkronizálás.
2.  Adja hozzá a forrásattribútumot a helyszíni AD-összekötő sémához.
3.  Adja hozzá a UserType az Azure AD-összekötő séma.
4.  Hozzon létre egy bejövő szinkronizálási szabályt az attribútumérték helyszíni Active Directoryból történő áramlásához.
5.  Hozzon létre egy kimenő szinkronizálási szabályt az attribútumérték azure AD-be való áramlását.
6.  Futtasson teljes szinkronizálási ciklust.
7.  Engedélyezze a szinkronizálási ütemezőt.

>[!NOTE]
> A szakasz további részei ismertetik ezeket a lépéseket. Ezeket egy egyerdős topológiával és egyéni szinkronizálási szabályok nélkül egy Azure AD-telepítés környezetében ismertetik. Ha többerdős topológiával rendelkezik, egyéni szinkronizálási szabályok vannak konfigurálva, vagy átmeneti kiszolgálóval rendelkezik, ennek megfelelően módosítania kell a lépéseket.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1. lépés: Tiltsa le a szinkronizálási ütemezőt, és ellenőrizze, hogy nincs-e folyamatban szinkronizálás
Az Azure AD nem kívánt módosításainak exportálásának elkerülése érdekében győződjön meg arról, hogy nem történik szinkronizálás, miközben a szinkronizálási szabályok frissítése közben történik. A beépített szinkronizálási ütemező letiltása:

 1. PowerShell-munkamenet indítása az Azure AD Connect-kiszolgálón.
 2. Tiltsa le az ütemezett szinkronizálást a `Set-ADSyncScheduler -SyncCycleEnabled $false`parancsmag futtatásával.
 3. Nyissa meg a Szinkronizálási szolgáltatáskezelőt a Szinkronizálási szolgáltatás **indítása** > **kor.**
 4. Nyissa meg a **Műveletek lapot,** és ellenőrizze, hogy nincs folyamatban *lévő*állapotú művelet.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>2. lépés: A forrásattribútum hozzáadása a helyszíni AD-összekötő sémához
Nem minden Azure AD-attribútumok importálva a helyszíni AD-összekötő tér. A forrásattribútum hozzáadása az importált attribútumok listájához:

 1. Nyissa meg az **Összekötők** lapot a Szinkronizálási szolgáltatáskezelőben.
 2. Kattintson a jobb gombbal a helyszíni AD-összekötőre, és válassza **a Tulajdonságok parancsot.**
 3. Az előugró ablakban nyissa meg az **Attribútumok kijelölése** lapot.
 4. Ellenőrizze, hogy a forrásattribútum be van-e jelölve az attribútumlistában.
 5. A mentéshez kattintson az **OK** gombra.
![Forrásattribútum hozzáadása a helyszíni AD-összekötő sémához](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-to-the-azure-ad-connector-schema"></a>3. lépés: A UserType hozzáadása az Azure AD-összekötő sémájához
Alapértelmezés szerint a UserType attribútum nem importálva az Azure AD Connect Space. A UserType attribútum hozzáadása az importált attribútumok listájához:

 1. Nyissa meg az **Összekötők** lapot a Szinkronizálási szolgáltatáskezelőben.
 2. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza **a Tulajdonságok parancsot.**
 3. Az előugró ablakban nyissa meg az **Attribútumok kijelölése** lapot.
 4. Győződjön meg arról, hogy a UserType attribútum be van jelölve az attribútumlistában.
 5. A mentéshez kattintson az **OK** gombra.

![Forrásattribútum hozzáadása az Azure AD Connector sémához](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4. lépés: Hozzon létre egy bejövő szinkronizálási szabályt az attribútumérték helyszíni Active Directoryból történő áramlásához
A bejövő szinkronizálási szabály lehetővé teszi, hogy az attribútumértéke a forrásattribútumból az helyszíni Active Directoryból a metaverzumba folyjon:

1. Nyissa meg a Szinkronizálási szabályok szerkesztőjét a**Szinkronizálási szabályok szerkesztő** **elindításával.** > 
2. Állítsa be, hogy az **Irány** keresési szűrő **bejövő**legyen.
3. Új bejövő szabály létrehozásához kattintson az **Új szabály hozzáadása** gombra.
4. A **Leírás** lapon adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | --- | --- | --- |
    | Név | *Adjon nevet* | Például, *Az AD - User UserType* |
    | Leírás | *Leírás megadása* |  |
    | Csatlakoztatott rendszer | *A helyszíni AD-összekötő választása* |  |
    | Csatlakoztatott rendszerobjektum-típus | **Felhasználó** |  |
    | Metaverzum objektum típusa | **Személy** |  |
    | Hivatkozás típusa | **Csatlakozás** |  |
    | Precedencia | *Válasszon egy számot 1–99 között* | Az 1–99 egyéni szinkronizálási szabályok számára van fenntartva. Ne válasszon olyan értéket, amelyet egy másik szinkronizálási szabály használ. |

5. Nyissa meg a **Hatókörszűrő** lapot, és adjon hozzá **egyetlen hatókörszűrő-csoportot** a következő záradékkal:

    | Attribútum | Művelet | Érték |
    | --- | --- | --- |
    | Admindescription | NOTSTARTWITH | Felhasználó\_ |

    A hatókörszűrő határozza meg, hogy mely helyszíni AD-objektumokra alkalmazza ezt a bejövő szinkronizálási szabályt. Ebben a példában ugyanazt a hatókörszűrőt használjuk az *In from AD – User Common* out-of-box szinkronizálási szabály, amely megakadályozza, hogy a szinkronizálási szabály alkalmazása az Azure AD user visszaírási szolgáltatás on keresztül létrehozott felhasználói objektumok. Előfordulhat, hogy módosítania kell a hatókörszűrőt az Azure AD Connect központi telepítésének megfelelően.

6. Nyissa meg az **Átalakítás** lapot, és valósítsa meg a kívánt átalakítási szabályt. Ha például egy nem használt helyszíni AD attribútumot (például az ExtensionAttribute1 attribútumot) jelölt meg a UserType forrásattribútumaként, akkor közvetlen attribútumfolyamatot valósíthat meg:

    | Folyamat típusa | Cél attribútum | Forrás | Alkalmazás egyszer | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    | Direct | UserType (Felhasználótípus) | extensionAttribute1 | Nincs bejelölve | Frissítés |

    Egy másik példában a UserType attribútum értékét más tulajdonságokból szeretné levezetni. Például az összes felhasználót vendégként szeretné szinkronizálni, ha a helyszíni AD <em>@partners.fabrikam123.org</em>userPrincipalName attribútumuk tartományi részlel végződik. Az ilyen kifejezéseket a következőkhez hasonló kifejezések valósíthatják meg:

    | Folyamat típusa | Cél attribútum | Forrás | Alkalmazás egyszer | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    | Kifejezés | UserType (Felhasználótípus) | IIF(IsPresent([userPrincipalName]), IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0"),"Tag""Vendég"),Hiba("UserPrincipalName nincs jelen a UserType meghatározásához")) | Nincs bejelölve | Frissítés |

7. A bejövő szabály létrehozásához kattintson a **Hozzáadás** gombra.

![Bejövő szinkronizálási szabály létrehozása](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5. lépés: Hozzon létre egy kimenő szinkronizálási szabályt az attribútumérték azure AD-be való áramlásához
A kimenő szinkronizálási szabály lehetővé teszi, hogy az attribútumérték a metaverzumból a UserType attribútumba folyjon az Azure AD-ben:

1. Nyissa meg a Szinkronizálási szabályok szerkesztőjét.
2. Állítsa be, hogy az **Irány** keresési szűrő **kimenő**legyen.
3. Kattintson az **Új szabály hozzáadása** gombra.
4. A **Leírás** lapon adja meg a következő konfigurációt:

    | Attribútum | Érték | Részletek |
    | ----- | ------ | --- |
    | Név | *Adjon nevet* | Például, *Out to AAD – User UserType* |
    | Leírás | *Leírás megadása* ||
    | Csatlakoztatott rendszer | *Az AAD-összekötő kijelölése* ||
    | Csatlakoztatott rendszerobjektum-típus | **Felhasználó** ||
    | Metaverzum objektum típusa | **Személy** ||
    | Hivatkozás típusa | **Csatlakozás** ||
    | Precedencia | *Válasszon egy számot 1–99 között* | Az 1–99 egyéni szinkronizálási szabályok számára van fenntartva. Ne válasszon olyan értéket, amelyet egy másik szinkronizálási szabály használ. |

5. Lépjen a **Hatókörszűrő** lapra, és adjon hozzá egy két záradékkal rendelkező **hatókörszűrő-csoportot:**

    | Attribútum | Művelet | Érték |
    | --- | --- | --- |
    | sourceObjectType | Egyenlő | Felhasználó |
    | cloudMastered | NEM EGYENLŐ | True (Igaz) |

    A hatókörszűrő határozza meg, hogy mely Azure AD-objektumokra alkalmazza ezt a kimenő szinkronizálási szabályt. Ebben a példában ugyanazt a hatókörszűrőt használjuk az Out to *AD – User Identity* out-of-box szinkronizálási szabályból. Megakadályozza, hogy a szinkronizálási szabály olyan felhasználói objektumokra legyen alkalmazva, amelyek nincsenek szinkronizálva a helyszíni Active Directoryból. Előfordulhat, hogy módosítania kell a hatókörszűrőt az Azure AD Connect központi telepítésének megfelelően.

6. Nyissa meg az **Átalakítás** lapot, és valósítsa meg a következő átalakítási szabályt:

    | Folyamat típusa | Cél attribútum | Forrás | Alkalmazás egyszer | Egyesítés típusa |
    | --- | --- | --- | --- | --- |
    | Direct | UserType (Felhasználótípus) | UserType (Felhasználótípus) | Nincs bejelölve | Frissítés |

7. A kimenő szabály létrehozásához kattintson a **Hozzáadás** gombra.

![Kimenő szinkronizálási szabály létrehozása](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>6. lépés: Teljes szinkronizálási ciklus futtatása
Általában egy teljes szinkronizálási ciklusra van szükség, mert új attribútumokat adtunk hozzá mind az Active Directory, mind az Azure AD Connector sémákhoz, és egyéni szinkronizálási szabályokat vezettünk be. Ellenőrizni szeretné a módosításokat, mielőtt exportálja őket az Azure AD-be. 

A következő lépésekkel ellenőrizheti a módosításokat, miközben manuálisan futtatja a teljes szinkronizálási ciklust kifutó lépéseket.

1. Teljes **importálás futtatása** a **helyszíni AD-összekötőn:**

   1. Nyissa meg a Szinkronizálási szolgáltatáskezelő **Műveletek** lapját.
   2. Kattintson a jobb gombbal a **helyszíni AD-összekötőre,** és válassza a **Futtatás parancsot.**
   3. Az előugró párbeszédpanelen válassza a **Teljes importálás** lehetőséget, majd kattintson az **OK**gombra.
   4. Várja meg, amíg a művelet befejeződik.

      > [!NOTE]
      > Kihagyhatja a helyszíni AD-összekötő teljes importálását, ha a forrásattribútum már szerepel az importált attribútumok listájában. Más szóval a [2.](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema)

2. Teljes **importálás futtatása** az **Azure AD-összekötőn:**

   1. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza a **Futtatás parancsot.**
   2. Az előugró párbeszédpanelen válassza a **Teljes importálás** lehetőséget, majd kattintson az **OK**gombra.
   3. Várja meg, amíg a művelet befejeződik.

3. Ellenőrizze a szinkronizálási szabály változásait egy meglévő felhasználói objektumon:

    A helyszíni Active Directory és a UserType az Azure AD-ből a forrásattribútumot importálta a megfelelő összekötő helyekre. A teljes szinkronizálás folytatása előtt végezze el az **előzetes verziót** egy meglévő felhasználói objektumon a helyszíni AD összekötő térben. A kiválasztott objektumnak a forrásattribútumot kell kitöltenie.
    
    A metaverzumba feltöltött UserType sikeres **előzetes verzió** jó mutatója annak, hogy megfelelően konfigurálta a szinkronizálási szabályokat. Az **előzetes verzió**ról a [Módosítás ellenőrzése](#verify-the-change)című szakaszban tájékozódhat.

4. Teljes **szinkronizálás futtatása** a **helyszíni AD-összekötőn:**

   1. Kattintson a jobb gombbal a **helyszíni AD-összekötőre,** és válassza a **Futtatás parancsot.**
   2. Az előugró párbeszédpanelen válassza a **Teljes szinkronizálás** lehetőséget, majd kattintson az **OK**gombra.
   3. Várja meg, amíg a művelet befejeződik.

5. Az Azure AD **függőben lévő exportálásának** ellenőrzése:

   1. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza a **Keresési összekötő tér ben parancsot.**
   2. A **Keresésösszekötő tér** legördülő ablakában:

      - A **Hatókör beállítása** **függőexportálásra.**
      - Jelölje be mind a három jelölőnégyzetet: **Hozzáadás,** **Módosítás**és **Törlés**.
      - Kattintson a **Keresés** gombra az exportálandó módosításokat tartalmazó objektumok listájának lereményéhez. Egy adott objektum módosításának vizsgálatához kattintson duplán az objektumra.
      - Ellenőrizze, hogy a várt módosítások várhatók-e.

6. Exportálás **futtatása** az **Azure AD-összekötőn:**

   1. Kattintson a jobb gombbal az **Azure AD-összekötőre,** és válassza a **Futtatás parancsot.**
   2. Az **Összekötő futtatása** előugró párbeszédpanelen kattintson az **Exportálás gombra,** majd kattintson az **OK**gombra.
   3. Várja meg, amíg befejeződik az exportálás az Azure AD-be.

> [!NOTE]
> Ezek a lépések nem tartalmazzák a teljes szinkronizálási és exportálási lépéseket az Azure AD-összekötő. Ezek a lépések nem szükségesek, mert az attribútumértékek a helyszíni Active Directoryból csak az Azure AD-be áramlanak.

### <a name="step-7-re-enable-the-sync-scheduler"></a>7. lépés: A szinkronizálási ütemező újbóli engedélyezése
A beépített szinkronizálási ütemező újbóli engedélyezése:

1. PowerShell-munkamenet indítása.
2. Engedélyezze újra az ütemezett szinkronizálást a parancsmag `Set-ADSyncScheduler -SyncCycleEnabled $true`futtatásával.


## <a name="next-steps"></a>További lépések
* A konfigurációs modellről a [Deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című témakörben olvashat bővebben.
* A kifejezés nyelvéről a [Deklaratív kiépítési kifejezések ismertetése című](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)témakörben olvashat bővebben.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
