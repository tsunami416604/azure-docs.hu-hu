---
title: "A Log Analyticshez való hozzáférés kezelése | Microsoft Docs"
description: "A Log Analyticshez való hozzáférés kezeléséhez különféle adminisztratív feladatokat alkalmazhat a felhasználókon, fiókokon, OMS-munkaterületeken és Azure-fiókokon."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: jwhit
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/02/2016
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fbd35f9fdd5165a2dd4bc7dd47bd70c890539e38


---
# <a name="manage-access-to-log-analytics"></a>A Log Analyticshez való hozzáférés kezelése
A Log Analyticshez való hozzáférés kezeléséhez különféle adminisztratív feladatokat végezhet el a felhasználókon, fiókokon, OMS-munkaterületeken és Azure-fiókokon. Egy új Operations Management Suite- (OMS-) munkaterület létrehozásához válasszon egy nevet, társítsa a fiókjához, és válasszon egy földrajzi helyet. A munkaterület lényegében egy tároló, amely a fiók adatait és egyszerű konfigurációs adatait tartalmazza. Ön vagy a szervezet más tagjai több OMS-munkaterületet is használhatnak az informatikai infrastruktúra egészéből vagy egyes részeiből begyűjtött különböző adatkészletek kezeléséhez.

Az [Ismerkedés a Log Analytics szolgáltatással](log-analytics-get-started.md) című cikkből megismerheti a gyors telepítés és futtatás módját. A cikk többi része gyakran használt feladatokat ismertet, például:

* A szükséges munkaterületek számának meghatározása
* Fiókok és felhasználók kezelése
* Csoport hozzáadása meglévő munkaterülethez
* Meglévő munkaterület csatolása Azure-előfizetéshez
* Munkaterület frissítése fizetős adatforgalmi díjcsomagra
* Adatforgalmi díjcsomag típusának módosítása
* Azure Active Directory-szervezet hozzáadása meglévő munkaterülethez
* OMS-munkaterület törlése

## <a name="determine-the-number-of-workspaces-you-need"></a>A szükséges munkaterületek számának meghatározása
A munkaterület egy Azure-erőforrás, továbbá egy tároló is, amelyben az adatok összegyűjtése, összesítése, elemzése és az OMS-portálon való megjelenítése történik.

Több OMS Log Analytics-munkaterület is létrehozható, és a felhasználók egy vagy több munkaterülethez is hozzáférhetnek. A munkaterületek számának minimalizálásával a legtöbb adat lekérdezhető és összevethető. Ez a szakasz azt ismerteti, hogy mikor lehet hasznos egynél több munkaterület létrehozása.

Jelenleg a Log Analytics-munkaterületek a következőket biztosítják:

* Egy földrajzi helyet az adattárolás számára
* A számlázáshoz szükséges részletes adatokat
* Az adatok elkülönítését

A fenti jellemzők alapján a következő esetekben lehet érdemes több munkaterületet létrehozni:

* Globális vállalatként az adatokat elkülönítési vagy megfelelőségi okok miatt külön régiókban kell tárolnia.
* Az Azure-t használja, és a kimenő adatátviteli díjak elkerülése érdekében ugyanabban a régióban szeretne létesíteni egy Log Analytics-munkaterületet, amelyikben az általa kezelt Azure-erőforrás is található.
* Használat alapján szeretné meghatározni a különböző részlegekre vagy üzleti csoportokra eső költségeket. Ha külön munkaterületet hoz létre az egyes részlegek vagy üzleti csoportok számára, az Azure-számlán és a használati kimutatásban külön jelennek meg az egyes munkaterületekre vonatkozó költségek.
* Felügyelt szolgáltatást kínál, és az egyes felügyelt ügyfelek naplóelemzési adatait külön kell tárolnia a többi ügyfél adataitól.
* Több ügyfelet is kezel, és azt szeretné, hogy az egyes ügyfelek, részlegek és üzleti csoportok lássák ugyan a saját adataikat, de a másokét ne.

Ha az adatgyűjtéshez ügynököket használ, konfigurálhatja úgy az egyes ügynököket, hogy a kívánt munkaterületnek küldjenek jelentést.

A System Center Operations Manager használata esetén az Operations Manager egyes felügyeleti csoportjai csak egy munkaterülethez csatlakoztathatók. Telepítheti a Microsoft Monitoring Agentet az Operations Manager által felügyelt számítógépekre, és beállíthatja az ügynököt úgy, hogy az Operations Managernek és egy másik Log Analytics-munkaterületnek is küldjön jelentést.

### <a name="workspace-information"></a>Munkaterület-információk
Az OMS-portálon megtekintheti a munkaterülete adatait, és beállíthatja, hogy kíván-e információkat kapni a Microsofttól.

#### <a name="view-workspace-information"></a>Munkaterület adatainak megtekintése
1. Az OMS-ben kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre.
3. Kattintson a **Munkaterület-információk** fülre.  
   ![Munkaterület-információk](./media/log-analytics-manage-access/workspace-information.png)

## <a name="manage-accounts-and-users"></a>Fiókok és felhasználók kezelése
Minden munkaterülethez több fiókot lehet hozzárendelni, és minden egyes felhasználói fiók (Microsoft-fiók vagy szervezeti fiók) több OMS-munkaterülethez férhet hozzá.

Az alapértelmezett beállítások szerint a munkaterület létrehozásához használt Microsoft-fiók vagy szervezeti fiók lesz a munkaterület rendszergazdája. A rendszergazda további Microsoft-fiókokat hívhat meg, illetve felhasználókat emelhet be a Azure Active Directory szolgáltatásból.

Az OMS-munkaterülethez való hozzáférést két helyen lehet szabályozni:

* Az Azure szolgáltatásban szerepköralapú hozzáférés-vezérléssel szabályozható az Azure-előfizetéshez és a kapcsolódó Azure-erőforrásokhoz való hozzáférés. Ezek az engedélyek vonatkoznak a PowerShell és a REST API hozzáféréseire is.
* Az OMS-portálon csak az OMS-portál érhető el, a társított Azure-előfizetés nem.

A felhasználók nem látnak adatokat a Backup- és a Site Recovery-megoldás csempéiben, ha csak az OMS-portálhoz biztosít nekik hozzáférést, a társított Azure-előfizetéshez viszont nem.
Amennyiben az összes felhasználó számára szeretné lehetővé tenni , hogy megtekintsék a megoldások adatait, legalább **olvasó** hozzáférést kell adnia nekik az OMS-munkaterülethez társított Backup-tárolóhoz, illetve Site Recovery-tárolóhoz.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>A Log Analytics hozzáférésének szabályozása az Azure Portal használatával
Amennyiben Azure-engedélyekkel, például az Azure Portal felületén keresztül hozzáférést biztosít a Log Analytics munkaterülethez, az érintett felhasználók a Log Analytics portálhoz is hozzáférhetnek. A felhasználók az Azure Portalról az OMS-portálra ugorhatnak az **OMS-portál** feladatra kattintva a Log Analytics munkaterület erőforrás megtekintésekor.

Néhány dolog, amit érdemes észben tartani az Azure Portal kapcsán:

* Ez nem *szerepköralapú hozzáférés-vezérlés*. Amennyiben legalább *Olvasó* hozzáférési engedéllyel rendelkezik az Azure Portal Log Analytics munkaterületéhez, akkor az OMS-portált használva módosításokat hajthat végre. Az OMS-portál a következőket ismeri: Rendszergazda, Közreműködő és ReadOnly felhasználók. Amennyiben a bejelentkezésre használt fiók a munkaterülethez társított Azure Active Directory szolgáltatásban található, akkor Ön Rendszergazda felhasználó lesz az OMS-portálon, egyéb esetben pedig Közreműködő.
* Amikor a http://mms.microsoft.com webhelyről jelentkezik be az OMS-portálra, akkor alapértelmezés szerint a **Munkaterület kiválasztása** lista jelenik meg. Ezen kizárólag az OMS-portállal létrehozott munkaterületek jelennek meg. Az Azure-előfizetéssel elérhető munkaterületek csak akkor lesznek láthatók, ha az URL-cím részeként megad egy bérlőt. Példa:
  
  `mms.microsoft.com/?tenant=contoso.com` A bérlőazonosító gyakran a bejelentkezéshez használt e-mail cím utolsó része.
* Amennyiben a bejelentkezéshez használt fiók a bérlő Azure Active Directory szolgáltatáshoz tartozó fiók, akkor *Rendszergazda* lesz az OMS-portálon. Ez mindenkire igaz, aki nem felhőszolgáltatóként (CSP) jelentkezik be.  Amennyiben a fiókja nem a bérlő Azure Active Directory szolgáltatásában található, akkor Ön *Felhasználó* az OMS-portálon.
* Amennyiben közvetlenül kíván elérni egy portált, amelyhez Azure-engedélyekkel rendelkezik, akkor meg kell adni az erőforrást az URL-cím részeként. Ezt az URL-címet a PowerShell használatával is elő lehet állítani.
  
  Például: `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.
  
  Az URL-cím az alábbihoz hasonló: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Az OMS-portál felhasználóinak kezelése
A felhasználók és csoportok kezelése a Beállítások oldal **Fiókok** lapjának **Felhasználók kezelése** fülére kattintva történik.   

![felhasználók kezelése](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>Felhasználó hozzáadása meglévő munkaterülethez
A következő lépésekkel adhat hozzá egy felhasználót vagy csoportot egy OMS-munkaterülethez.

1. Az OMS-ben kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. A **Felhasználók kezelése** részen válassza ki a hozzáadni kívánt fióktípust: **Szervezeti fiók**, **Microsoft-fiók**, **Microsoft ügyfélszolgálat**.
   
   * Amennyiben a Microsoft-fiók lehetőséget választja, írja be Microsoft-fiókhoz tartozó felhasználó e-mail-címét.
   * Amennyiben a Szervezeti fiók lehetőséget választja, elég beírni a felhasználó vagy csoport nevének vagy e-mail-aliasának egy részét, és a legördülő menüben megjelenik a feltételeknek megfelelő felhasználók és csoportok listája. Válasszon ki egy felhasználót vagy csoportot.
   * A Microsoft ügyfélszolgálatán keresztül átmeneti hozzáférést biztosíthat a munkaterületéhez a Microsoft ügyfélszolgálat egyik szakemberének vagy más alkalmazottjának, hogy el tudja végezni a hibaelhárítást.
     
     > [!NOTE]
     > Az optimális teljesítmény érdekében legfeljebb három Active Directory-csoport legyen társítva egy OMS-fiókhoz. Az egyik legyen a rendszergazdák, a második a közreműködők, a harmadik pedig a csak olvasási hozzáféréssel rendelkező felhasználók csoportja. Ha ennél több csoportot használ, az befolyásolhatja a Log Analytics teljesítményét.
     > 
     > 
4. Válassza ki a hozzáadni kívánt felhasználó vagy csoport típusát: **Rendszergazda**, **Közreműködő** vagy **ReadOnly felhasználó**.  
5. Kattintson az **Add** (Hozzáadás) parancsra.
   
   Microsoft-fiók hozzáadásakor egy meghívót kap a munkaterület használatára a korábban megadott e-mail címére. Ha a felhasználó követi a meghívóban szereplő, az OMS használatára vonatkozó utasításokat, hozzáférhet ehhez az OMS-munkaterülethez.
   Szervezeti fiók hozzáadásakor a felhasználó azonnal hozzáférést kaphat a Log Analytics megoldáshoz.  
   ![meghívó e-mail](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

#### <a name="edit-an-existing-user-type"></a>Meglévő felhasználótípus hozzáadása
Az OMS-fiókjával lehetősége van módosítani egy társított felhasználó fiókjának szerepét. A következő szerepek közül választhat:

* *Rendszergazda*: Kezelheti a felhasználókat, látja a riasztásokat és reagálhat rájuk, hozzáadhat és eltávolíthat kiszolgálókat.
* *Közreműködő*: Látja a riasztásokat és reagálhat rájuk, hozzáadhat és eltávolíthat kiszolgálókat.
* *ReadOnly felhasználó*: A csak olvasási jogosultsággal rendelkező felhasználók nem tudják végrehajtani a következőket:
  
  1. Megoldások hozzáadása és eltávolítása. A megoldástár nem látható.
  2. Csempék hozzáadása, módosítása és eltávolítása a **Saját irányítópult** felületen.
  3. A **Beállítások** oldalak megtekintése. Ezek az oldalak rejtve maradnak.
  4. A Keresés nézetben a PowerBI konfigurálása, a Mentett keresések és a Riasztás feladatok rejtve maradnak.

#### <a name="to-edit-an-account"></a>Fiók szerkesztése
1. Az OMS-ben kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. Jelölje ki a felhasználó módosítani kívánt szerepét.
4. A megerősítési párbeszédpanelen kattintson az **Igen** gombra.

### <a name="remove-a-user-from-an-oms-workspace"></a>Felhasználó eltávolítása az OMS-munkaterületről
A következő lépésekkel távolíthat el egy felhasználót egy OMS-munkaterületről. A felhasználó eltávolításával nem záródik be a munkaterület. Csupán eltávolítja a felhasználó és a munkaterület közötti társítást. Ha egy felhasználóhoz több munkaterület is társítva van, a felhasználó továbbra is bejelentkezhet az OMS-be, hogy megtekintse a többi munkaterületet.

1. Az OMS-ben kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd a **Felhasználók kezelése** fülre.
3. Kattintson az eltávolítani kívánt felhasználó neve mellett található **Eltávolítás** gombra.
4. A megerősítési párbeszédpanelen kattintson az **Igen** gombra.

### <a name="add-a-group-to-an-existing-workspace"></a>Csoport hozzáadása meglévő munkaterülethez
1. Járjon el a fenti „Felhasználó hozzáadása meglévő munkaterülethez” szakasz 1-4. lépése szerint.
2. A **Felhasználó vagy csoport kiválasztása** pontban válassza a **Csoport** lehetőséget.
   ![csoport hozzáadása meglévő munkaterülethez](./media/log-analytics-manage-access/add-group.png)
3. Írja be a hozzáadni kívánt csoport megjelenítendő nevét vagy e-mail-címét.
4. Jelölje ki a csoportot a kapott listán, majd kattintson a **Hozzáadás** gombra.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Meglévő munkaterület csatolása Azure-előfizetéshez
Minden 2016. szeptember 26. után létrehozott munkaterület a létrehozásukkor kell társítani egy Azure-előfizetéshez. Az e dátum előtt létrehozott munkaterületeket a legközelebbi bejelentkezéskor kell társítani egy munkaterülethez. 

> [!IMPORTANT]
> Munkaterület társításához az Azure-fióknak már hozzáféréssel kell rendelkeznie a társítani kívánt munkaterülethez.  Másként fogalmazva az Azure Portal eléréséhez **ugyanazt** a fiókot kell használni, mint az OMS-munkaterület eléréshez. Ha ez nem igaz, kövesse a [Felhasználó hozzáadása meglévő munkaterülethez](#add-a-user-to-an-existing-workspace) szakasz lépéseit.
> 
> 

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Munkaterület társítása egy Azure-előfizetéshez az OMS-portálon
Ahhoz, hogy egy munkaterületet egy Azure-előfizetéshez társítson az OMS-portálon, a bejelentkezett felhasználónak már rendelkeznie kell egy fizetős Azure-fiókkal.

1. Az OMS-ben kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok** fülre, majd az **Azure-előfizetés és adatforgalmi díjcsomag** fülre.
3. Kattintson a használni kívánt adatforgalmi díjcsomagra.
4. Kattintson a **Save** (Mentés) gombra.  
   ![előfizetés és adatforgalmi díjcsomagok](./media/log-analytics-manage-access/subscription-tab.png)

Az új adatforgalmi díjcsomag megjelenik az OMS-portál menüszalagján a webhely felső részén.

![OMS menüszalag](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Munkaterület társítása egy Azure-előfizetéshez az Azure Portalon
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a **Log Analytics (OMS)** elemet, majd jelölje ki.
3. Ekkor megjelenik a meglévő munkaterületek listája. Kattintson az **Add** (Hozzáadás) parancsra.  
   ![munkaterületek listája](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. Az **OMS-munkaterület** részen kattintson a **Vagy meglévő társítása** gombra.  
   ![meglévő társítása](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Kattintson a **Kötelező beállítások konfigurálása** lehetőségre.  
   ![kötelező beállítások megadása](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Megjelenik a munkaterület listája, amelyek még nincsenek társítva az Azure-fiókhoz. Jelöljön ki egy munkaterületet.  
   ![munkaterület kijelölése](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Ha szükséges, a következő elemek értékeit módosíthatja:
   * Előfizetés
   * Erőforráscsoport
   * Hely
   * Tarifacsomag  
     ![értékek módosítása](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Kattintson a **Létrehozás** gombra. A munkaterület az Azure-fiókhoz van társítva.

> [!NOTE]
> Amennyiben nem találja a társítani kívánt munkaterületet, akkor az Azure-előfizetése nem rendelkezik hozzáféréssel az OMS webhelyen létrehozott OMS-munkaterülethez.  Ekkor az OMS-portálon hozzáférési jogot kell biztosítani ehhez a fiókhoz. A tennivalókat itt találja: [Felhasználó hozzáadása meglévő munkaterülethez](#add-a-user-to-an-existing-workspace).
> 
> 

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Munkaterület frissítése fizetős díjcsomagra
Az OMS három munkaterület díjcsomagtípust ismer: **Ingyenes**, **Önálló** és **OMS**.  Amennyiben *Ingyenes* díjcsomagot használ, naponta legfeljebb 500 MB adatot küldhet a Log Analytics szolgáltatásnak.  Ha túllépi ezt a mennyiséget, munkaterületét fizetős díjcsomagra kell módosítania, hogy ennél több adatot gyűjthessen be. A díjcsomag típusa bármikor módosítható.  Az OMS díjszabásról a [díjszabás](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing) leírásában tájékozódhat.

### <a name="using-entitlements-from-an-oms-subscription"></a>Az OMS-előfizetésből származó jogosultságok használata
Ha szeretné használni az OMS E1, OMS E2 OMS vagy a System Center OMS-bővítményének megvásárlásából származó jogosultságokat, válassza az OMS Log Analytics *OMS* csomagját.

OMS-előfizetés megvásárlásakor a jogosultságok bekerülnek a Nagyvállalati szerződésbe. A megállapodás hatálya alatt létrehozott Azure-előfizetések mind rendelkeznek ezekkel a jogosultságokkal. Így például több olyan OMS-munkaterülete lehet, amely az OMS-előfizetések jogosultságát használja.

Ha biztosítani kívánja, hogy az OMS-munkaterületén érvényesek legyenek az OMS-előfizetésből származó jogosultságok, a következőket kell tennie:

1. OMS-munkaterület létrehozása egy olyan Azure-előfizetésben, amely egy Nagyvállalati szerződés része, és amely az OMS-előfizetést is tartalmazza
2. *OMS* adatforgalmi díjcsomag kiválasztása a munkaterület számára

> [!NOTE]
> Amennyiben munkaterületét 2016. szeptember 26. előtt hozta létre, és *Prémium* szintű Log Analytics tarifacsomagot használ, akkor ez a munkaterület a System Center OMS-bővítményéből származó jogosultságokat fogja használni. Jogosultságait az *OMS* tarifacsomagra való váltással is használhatja. 
> 
> 

Az OMS-előfizetés jogosultságai nem jelennek meg az Azure- vagy OMS-portálon. A jogosultságokat és használatot az Enterprise Portalon tekintheti meg.  

Amennyiben módosítania kell az Azure-előfizetést, amelyhez az OMS-munkaterületet társította, az Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) parancsmagot használja.

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Azure kötelezettségvállalás használata egy Nagyvállalati szerződésből
Amennyiben Ön nem rendelkezik OMS-előfizetéssel, az OMS egyes összetevőiért külön-külön kell fizetnie, és a használat megjelenik az Azure-számláján.

Ha rendelkezik Azure pénzügyi kerettel a nagyvállalati beléptetés részeként, amelyhez az Azure-előfizetést társította, akkor a Log Analytics automatikusan felhasználja az esetleg rendelkezésre álló pénzügyi keretet.

Amennyiben módosítania kell az Azure-előfizetést, amelyhez az OMS-munkaterületet társította, az Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx) parancsmagját használja.  

### <a name="change-a-workspace-to-a-paid-data-plan"></a>Munkaterület módosítása fizetős adatforgalmi díjcsomagra
1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Keresse meg a **Log Analytics** elemet, majd jelölje ki.
3. Ekkor megjelenik a meglévő munkaterületek listája. Jelöljön ki egy munkaterületet.  
   ![munkaterületek listája](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4. A **Beállítások** oldalon kattintson a **Tarifacsomag** gombra.  
   ![tarifacsomag](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5. A **Tarifacsomag** lapon válassza ki az adatforgalmi díjcsomagot, majd kattintson a **Kiválasztás** gombra.  
   ![díjcsomag kiválasztása](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Amikor frissíti az Azure Portal nézetét, láthatja, hogy a **Tarifacsomag** a választott díjcsomagra módosult.  
   ![tarifacsomag frissítése](./media/log-analytics-manage-access/manage-access-change-plan04.png)

## <a name="add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Azure Active Directory-szervezet hozzáadása meglévő munkaterülethez
Log Analytics munkaterületét egy Azure Active Directory-tartományhoz társíthatja, és címtárából felhasználókat adhat hozzá az OMS-munkaterülethez.

Amikor az Azure Portalon hoz létre egy munkaterületet, vagy ha munkaterületét egy Azure-előfizetéshez társítja, az Azure Active Directory a szervezeti fiókjaként lesz társítva.

Amikor az OMS-portálon hoz létre egy munkaterületet, a rendszer felajánlja, hogy társítsa Azure-előfizetését egy szervezeti fiókhoz, mivel az Azure-előfizetés a Log Analytics-munkaterülethez van társítva.

### <a name="to-add-an-azure-active-directory-organization-to-an-existing-workspace"></a>Azure Active Directory-szervezet hozzáadása meglévő munkaterülethez
Egy Azure Active Directory-szervezet hozzáadásával lehetővé válik, hogy abból a címtárból felhasználókat és csoportokat vegyen fel a munkaterületre.

1. Az OMS Beállítások oldalán kattintson a **Fiókok** lehetőségre, majd a **Felhasználók kezelése** fülre.  
2. Tekintse át a szervezeti fiókok adatait, majd kattintson a **Szervezet hozzáadása** gombra.  
    ![szervezet hozzáadása](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Adja meg Azure Active Directory-tartománya rendszergazdájának identitását. Ezután megjelenik a visszaigazolás, hogy munkaterülete társítva lett Azure Active Directory-tartományához.
    ![társított munkaterületek visszaigazolása](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

Ha más címtárból kell felhasználókat hozzáadni, a *Szervezet módosítása* gombra kattintva társíthatja a munkaterületet másik címtárral.

## <a name="delete-your-log-analytics-workspace"></a>Log Analytics-munkaterület törlése
Amikor töröl egy Log Analytics-munkaterületet, 30 napon belül a munkaterülethez kapcsolódó összes adat törlődik az OMS szolgáltatásból.

Ha Ön rendszergazda, és több felhasználó van társítva a munkaterülethez, a felhasználók és a munkaterület közötti társítás megszűnik. Ha a felhasználók más munkaterületekhez is társítva vannak, továbbra is használhatják az OMS-t azokkal a munkaterületekkel. Ha azonban nincsenek más munkaterületekhez társítva, az OMS használatához létre kell hozniuk egy munkaterületet.

### <a name="to-delete-an-oms-workspace"></a>OMS-munkaterület törlése
1. Az OMS-ben kattintson a **Beállítások** csempére.
2. Kattintson a **Fiókok**, majd a **Munkaterület-információk** lapra.
3. Kattintson a **Munkaterület bezárása** elemre.
4. Válassza ki a munkaterület bezárásának okát, vagy írjon be egy, a megjelenőktől eltérő okot a szövegmezőbe.
5. Kattintson a **Munkaterület bezárása** elemre.

## <a name="next-steps"></a>Következő lépések
* Az ügynökök hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Connect Windows computers to Log Analytics](log-analytics-windows-agents.md) (Windows rendszerű számítógépek csatlakoztatása a Log Analyticshez).
* A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).
* Ha a szervezete proxykiszolgálót vagy tűzfalat használ annak érdekében, hogy az ügynökök kommunikálni tudjanak a Log Analytics szolgáltatással: [Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md) (Proxy- és tűzfalbeállítások konfigurálása a Log Analyticsben).




<!--HONumber=Nov16_HO2-->


