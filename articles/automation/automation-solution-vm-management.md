---
title: "Indítása/leállítása virtuális gépek során munkaidőn kívüli megoldás (előzetes verzió) |} Microsoft Docs"
description: "A Virtuálisgép-felügyeleti megoldás elindul, és leállítja az Azure Resource Manager virtuális gépek ütemezés szerint, és a Naplóelemzési proaktív figyeli."
services: automation
documentationCenter: 
authors: eslesar
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: magoedte
ms.openlocfilehash: 4424cbb83bdb31c60e15d62f9387b4050611a98d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Indítása/leállítása virtuális gépek során munkaidőn kívüli megoldás (előzetes verzió) az Azure Automationben

A indítása/leállítása virtuális gépek során munkaidőn kívüli megoldás elindul és az Azure virtuális gépek nem felhasználó által definiált ütemezés, insights Azure Naplóelemzés keresztül biztosít, és opcionális e-maileket küld [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). A legtöbb esetben Azure Resource Manager és klasszikus virtuális gépeket is támogatja. 

A megoldás biztosít egy decentralizált automatizálási lehetősége a felhasználók számára szeretné, hogy a kiszolgáló nélküli, alacsony költségű erőforrások költségeik csökkentése érdekében. A megoldás a következőket teheti:

* Virtuális gépek indítása és leállítása ütemezni.
* Virtuális gépek indítása és leállítása növekvő sorrendben (klasszikus virtuális gépek esetén nem támogatott) Azure-címkék használatával ütemezhető.
* Virtuális gépek automatikus leállító alacsony CPU-használat alapján.

## <a name="prerequisites"></a>Előfeltételek

- A runbookok (forgatókönyvek) [Azure-futtatófiókkal](automation-offering-get-started.md#authentication-methods) használhatóak.  A Futtatás mint fiók nem az előnyben részesített hitelesítési módszer, mert a tanúsítvány alapú hitelesítést használ, amely lejár, vagy módosítsa gyakran jelszó helyett.  

- Ez a megoldás csak virtuális gépek, amelyek ugyanahhoz az előfizetéshez, mint az Azure Automation-fiók kezelése.  

- Ez a megoldás a rendszer csak a következő Azure-régiók: Ausztrália délkeleti, Kanada központi, közép-Indiában, USA keleti régiója, kelet-japán, Délkelet-Ázsiában, Egyesült Királyság déli régiója és Nyugat-Európában.  
    
    > [!NOTE]
    > A runbookok kezelése a virtuális gép ütemezés célba virtuális gépek bármely régióban.  

- Válassza ki az e-mail értesítéseket küldhet, ha a kezdési és befejezési VM runbookokat befejeződik, az Azure piactérről bevezetése közben **Igen** SendGrid telepítéséhez. 

    > [!IMPORTANT]
    > SendGrid egy olyan külső szolgáltatás. Forduljon a támogatási szolgálathoz, [SendGrid](https://sendgrid.com/contact/).  
    >
   
    A SendGrid korlátozásai a következők:

    * Egy SendGrid fiók előfizetésenként felhasználónként legfeljebb.
    * Két SendGrid fiókok előfizetésenként legfeljebb.

Ha ez a megoldás korábbi verzióját telepítette, akkor először törölni kell a fiókhoz tartozó Ez a kiadás telepítése előtt.  

## <a name="solution-components"></a>Megoldás-összetevők

Ez a megoldás előre beállított runbookok ütemezését és Log Analyticshez való integráció tartalmazza, így könnyebben igazíthatja a indítási és leállítási a virtuális gépek az üzleti igényeinek. 

### <a name="runbooks"></a>Runbookok

A következő táblázat a forgatókönyvek az Automation-fiók telepített.  Meg nem módosítja a runbook-kódot. Ehelyett írni az új szolgáltatásokat saját runbook.

> [!IMPORTANT]
> Nem közvetlenül futtassa minden olyan forgatókönyvben "gyermek" a névhez.
>

Az összes fölérendelt runbook közé tartozik a *WhatIf* paraméter. Ha beállítása **igaz**, *WhatIf* támogatja, és részletesen leírja a pontos viselkedését a runbook futtatása nélkül; a *WhatIf* paraméter, és érvényesíti a helyes-e folyamatban van a virtuális gépek céloz meg.  A runbook csak a megadott műveleteket hajtja végre amikor a *WhatIf* paraméter értéke **hamis**. 

|**A Runbook** | **Paraméterek** | **Leírás**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Csak a szülő runbook hívása. Remote forgatókönyv erőforrás alapot riasztásokat hoz létre.| 
|AutoStop_CreateAlert_Parent | WhatIf: IGAZ vagy HAMIS eredményt ad <br> VMList | Létrehozza vagy frissíti a célként megadott előfizetés vagy az erőforrás-csoportok a virtuális gépeken Azure riasztási szabályok. <br> VMList: Vesszővel elválasztva azon virtuális gépek.  Például *vm1, vm2 virtuális gépnek, vm3*.| 
|AutoStop_Disable | nincs | Letiltja a Remote riasztások és az alapértelmezett ütemezés.| 
|AutoStop_StopVM_Child | WebHookData | Csak a szülő runbook hívása. A riasztási szabályok hívja meg ezt a runbookot a virtuális gép leállítása.|  
|Bootstrap_Main | nincs | Egy alkalommal telepítéséhez használt rendszertöltő konfigurációk webhookURI, például amelyek általában nem érhető el az Azure Resource Manager. Ez a forgatókönyv a sikeres telepítés automatikusan törlődnek.|  
|ScheduledStartStop_Child | VMName <br> Művelet: Leállítása vagy elindítása <br> erőforráscsoport-név | Csak a szülő runbook hívása. Állítsa le vagy a ütemezett STOP start hajt végre.|  
|ScheduledStartStop_Parent | Művelet: Leállítása vagy elindítása <br> WhatIf: IGAZ vagy HAMIS eredményt ad | Ez hatással van az előfizetésben szereplő összes virtuális gépet. Szerkessze a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** végrehajtásához csak a cél-erőforráscsoport. Adott virtuális gépek frissítésével emellett kizárhatók a **External_ExcludeVMNames** változó. *WhatIf* ugyanaz, mint más runbookokat viselkedik.|  
|SequencedStartStop_Parent | Művelet: Leállítása vagy elindítása <br> WhatIf: IGAZ vagy HAMIS eredményt ad | Nevű címkék létrehozása **SequenceStart** és **SequenceStop** minden feladatütemezési indítása/leállítása tevékenységhez kívánt virtuális gépen. A címke értékének pozitív egész (1, 2, 3), amely megfelel a sorrendet, amelyben létre kívánja indítása vagy leállítása kell lennie. *WhatIf* ugyanaz, mint más runbookokat viselkedik. <br> **Megjegyzés:**: virtuális gépek Azure Automation-változók External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames és External_ExcludeVMNames meghatározott erőforráscsoportokon belül kell lennie. A megfelelő címkéket az érvénybe lépéséhez műveletek kell rendelkezniük.|

### <a name="variables"></a>Változók

A következő táblázat a változókat az Automation-fiókban létrehozni.  Csak módosítania kell a következő előtaggal változók **külső**. Változók módosítása a következő előtaggal **belső** nemkívánatos hatások okoz.  

|**Változó** | **Leírás**|
---------|------------|
|External_AutoStop_Condition | A feltétel beállítása előtt a riasztást kiváltó szükséges feltételes operátort. Elfogadható értékek a következők **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, és **LessThanOrEqual**.|  
|External_AutoStop_Description | A riasztás a virtuális gép leállítása, ha a Processzor-százalékos aránya meghaladja a küszöbértéket.|  
|External_AutoStop_MetricName | A teljesítmény metrika, amelyek az Azure riasztási szabály esetében be kell állítani a neve.| 
|External_AutoStop_Threshold | Az Azure riasztási szabály a változóban megadott küszöbértékét *External_AutoStop_MetricName*. Százalékos érték 1 és 100 között lehet.|  
|External_AutoStop_TimeAggregationOperator | Az idő összesítő operátor szerinti szűrése, alkalmazott a kijelölt ablak mérete, a feltétel kiértékeléséhez. Elfogadható értékek a következők **átlagos**, **minimális**, **maximális**, **teljes**, és **utolsó**.|  
|External_AutoStop_TimeWindow | Az ablak mérete időintervalluma Azure elemzi a riasztást kiváltó kijelölt metrikáit. Ez a paraméter timespan formátumú bemenetet fogad el. Lehetséges értékek: 5 perc és 6 óra.|  
|External_EmailFromAddress | Adja meg az e-mailek feladójának.|  
|External_EmailSubject | Megadja az e-mail tárgysorának szövegét.|  
|External_EmailToAddress | Adja meg az e-mail címzettjei. A neveket vesszővel használatával.|  
|External_ExcludeVMNames | Adjon meg virtuális gépek nevénél ki lesznek zárva, nevek elválasztó vessző használatával, szóközök nélkül.|  
|External_IsSendEmail | Adja meg a beállítás befejezése után e-mail értesítést küldeni.  Adja meg **Igen** vagy **nem** e-mail nem küldhető.  Ezt a beállítást kell **nem** Ha értesítő e-mailek nem engedélyezte a kezdeti üzembe helyezése során.|  
|External_Start_ResourceGroupNames | Megadja egy vagy több erőforrás csoportokat, ahol értékeket vesszővel, kezdő műveleteket a megcélzott használatával.|  
|External_Stop_ResourceGroupNames | Megadja egy vagy több erőforrás csoportokat, ahol értékeket vesszővel, stop műveleteket a megcélzott használatával.|  
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|  
|Internal_AutoSnooze_WebhookUri | Megadja a Remote forgatókönyvhöz nevű Webhook URI.|  
|Internal_AzureSubscriptionId | Az Azure-előfizetés-azonosítót határozza meg.|  
|Internal_ResourceGroupName | Megadja az automatizálási fiók erőforráscsoportjának neve.|  
|Internal_SendGridAccountName | A SendGrid-fiók nevét adja meg.|  
|Internal_SendGridPassword | Adja meg a SendGrid-fiók jelszavát.|  

<br>

Mindegyik forgatókönyvben között a **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, és **External_ExcludeVMNames** változók szükségesek. a virtuális gépek vesszővel tagolt listáját tartalmazó kivételével hajthatja végre a virtuális gépek, a **AutoStop_CreateAlert_Parent** runbook. Ez azt jelenti, hogy a virtuális gépek kell indításra cél erőforráscsoport található, és állítsa le a műveletek. A logikai egy kicsit hasonlóan működik az Azure házirend, hogy az előfizetés vagy az erőforrás-csoportot, és műveleteket újonnan létrehozott virtuális gépek által örökölt. Ezt a módszert elkerülhető, hogy nem kell külön ütemezés minden virtuális gép kezelheti és elindul, és a skála leáll.

### <a name="schedules"></a>Ütemezések

A következő táblázat felsorolja az egyes az alapértelmezett ütemezését az Automation-fiókban létrehozni.  Módosíthatja azokat, vagy hozzon létre egy saját egyéni ütemezést.  Alapértelmezés szerint minden egyes letiltottak kivételével **Scheduled_StartVM** és **Scheduled_StopVM**.

Az ütemezést, mert ez előfordulhat, hogy létre átfedő ütemezés műveletek ne engedélyezze. A legcélszerűbb határozza meg, melyik optimalizálást, hajtsa végre, és ennek megfelelően módosítsa.  Tekintse meg a kapcsolódó további tájékoztatást a áttekintés szakaszban szereplő példakörnyezetek.   

|**Az ütemezés nevének** | **Gyakoriság** | **Leírás**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként | Futtatja a AutoStop_CreateAlert_Parent runbookot 8 óránként, ami pedig leállítja a Virtuálisgép-alapú External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames, és értékeit External_ExcludeVMNames Azure Automation-változók.  Azt is megteheti virtuális gépek vesszővel tagolt listáját is megadhat a VMList paraméter használatával.|  
|Scheduled_StopVM | Felhasználó által megadott, naponta | A Scheduled_Parent runbookot futtat egy paraméterrel a *leállítása* , a megadott időpontban naponta.  Automatikusan leállítja az összes virtuális gépet, amelyek megfelelnek az eszköz változó által meghatározott szabályok. Engedélyeznie kell a kapcsolódó ütemezés **ütemezett-StartVM**.|  
|Scheduled_StartVM | Felhasználó által megadott, naponta | A Scheduled_Parent runbookot futtat egy paraméterrel a *Start* , a megadott időpontban naponta.  Minden virtuális gépek, amelyek megfelelnek a szabályok határozzák meg a megfelelő változók automatikusan elindul.  Engedélyeznie kell a kapcsolódó ütemezés **ütemezett-StopVM**.|
|Előkészített StopVM | 13:00:00 (UTC) szerint péntekenként | A Sequenced_Parent runbookot futtat egy paraméterrel a *leállítása* péntekenként, a megadott időpontban.  Egymás után (növekvő) leállítja az összes virtuális gépet a címkével ellátott **SequenceStop** határozzák meg a megfelelő változókat.  Tekintse meg a Runbookok című szakaszban talál további információt előfizetéscímkék értékeit és eszköz változókat.  Engedélyeznie kell a kapcsolódó ütemezés **Sequenced-StartVM**.|
|Előkészített StartVM | 1:00 PM idő szerint (UTC), minden hétfőn | A Sequenced_Parent runbookot futtat egy paraméterrel a *Start* minden hétfőn, a megadott időpontban. Egymás után minden virtuális gép (csökkenő) elindítja a címkével ellátott **SequenceStart** határozzák meg a megfelelő változókat.  Tekintse meg a Runbookok című szakaszban talál további információt előfizetéscímkék értékeit és eszköz változókat.  Engedélyeznie kell a kapcsolódó ütemezés **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Konfiguráció

Hajtsa végre a következő lépések végrehajtásával adja hozzá a indítása/leállítása virtuális gépek munkaidőn kívüli megoldás során az Automation-fiók, és adja meg a változók a megoldás testreszabásához.

1. Az Azure Portalon kattintson az **Új** elemre.<br> ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. A piactér panelen írjon be egy kulcsszó például **Start** vagy **indítása/leállítása**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Azt is megteheti írja be a megoldás teljes név egy vagy több kulcsszót, és nyomja le az ENTER billentyűt.  Válassza ki **indítása/leállítása virtuális gépek munkaidőn kívüli [előzetes verzió] során** a keresési eredmények.  
3. Az a **indítása/leállítása virtuális gépek során munkaidőn kívüli [előzetes verzió]** ablaktáblán a kiválasztott megoldás, ellenőrizze az összefoglaló információkat, és kattintson a **létrehozása**.  
4. A **megoldás hozzáadása** ablaktáblán jelenik meg. A megoldás konfigurálása előtt importálhatja az Automation előfizetés kéri.<br><br> ![Virtuálisgép-felügyelet – Megoldás hozzáadása panel](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  Az a **megoldás hozzáadása** ablaktáblán válassza előbb **munkaterület**. Az azonos Azure-előfizetés, amely az Automation-fiókhoz kapcsolódó OMS-munkaterület kiválasztása Ha nem rendelkezik a munkaterületen, válassza ki a **új munkaterület létrehozása**. Az a **OMS-munkaterület** ablaktáblán tegye a következőket: 
   - Adja meg az új **OMS-munkaterület** nevét.
   - Válassza ki a **előfizetés** csatolása; ehhez válassza a legördülő listából, hogy az alapértelmezett beállítás nem megfelelő.
   - A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, vagy válasszon egy meglévőt.  
   - Válasszon ki egy **helyet**.  A rendelkezésre álló csak helyek jelenleg **Ausztrália délkeleti**, **Kanada központi**, **közép-Indiában**, **USA keleti régiója**, **Kelet-Japánban**, **Délkelet-Ázsia**, **Egyesült Királyság déli régiója**, és **Nyugat-Európában**.
   - Válasszon egy tarifacsomagot a **Tarifacsomag** területen.  A megoldást kínál a két réteg: ingyenes és fizetős OMS.  Ingyenes szint van korlátozva a gyűjtött adatok mennyiségét, naponta, a megőrzési időtartam és a runbook-feladat futásidejű perc.  Az OMS fizetett csomagnál a naponta összegyűjtött adatok mennyisége nincs korlátozva.  

        > [!NOTE]
        > Bár a / GB (önálló) fizetett réteg lehetőség jelenik meg, továbbra is nem alkalmazható.  Válassza ki azt, és folytassa a megoldás létrehozása a az előfizetésében, ha sikertelen.  Csak akkor lesz választható, ha ezt a megoldást hivatalosan is kiadják.<br>A megoldás csak használ automation feladat (perc) és a napló feldolgozási.  Azt nem további OMS csomópontokat hozzáadni a környezetben.  

6. Miután megadta a szükséges adatokat a a **OMS-munkaterület** ablaktáblán kattintson a **létrehozása**.  A folyamat állapotát nyomon követheti **értesítések** a menüből, amely adja vissza, hogy a **megoldás hozzáadása** végzett ablaktáblán.  
7. Az a **megoldás hozzáadása** ablaktáblán válassza előbb **Automation-fiók**.  Ha egy új OMS-munkaterület hoz létre, szüksége is létrehozhat egy új Automation-fiók, társítani kell.  Válassza ki **Automation-fiók létrehozása**, és a a **hozzáadása Automation-fiók** panelen adja meg a következőket: 
  - A **Név** mezőbe írja be az Automation-fiók nevét.

    Egyéb beállítások automatikusan fel van töltve, a kiválasztott OMS-munkaterület alapján. Ezek a beállítások nem módosíthatók.  A megoldásban szereplő runbookok alapértelmezett hitelesítési módszere egy Azure-futtatófiók.  Miután rákattintott **OK**, a konfigurációs beállításokat érvényesíti, és az Automation-fiók létrehozása.  Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását. 

    Ellenkező esetben kiválaszthat egy meglévő Automation-futtatófiókot.  Vegye figyelembe, hogy a kiválasztott fiók már nem csatolható másik OMS-munkaterület. Ha már kapcsolva, egy üzenetet kap, és jelöljön ki egy másik Automation futtató fiókot, vagy hozzon létre egy újat kell.<br><br> ![Az Automation-fiók már kapcsolódik egy OMS-munkaterülethez](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Végül, az a **megoldás hozzáadása** ablaktáblán válassza előbb **konfigurációs**. A **paraméterek** ablaktáblán jelenik meg.<br><br> ![Paraméterek panel megoldás](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  Itt kéri:  
   - Adja meg a **céloz erőforráscsoport-nevek**. Ezek azok az erőforráscsoportok nevei, amelyek tartalmazzák a megoldás által kezelt virtuális gépek.  Adjon meg egynél több nevet, és külön alapján (értékei nem kis-és nagybetűket) vesszővel válassza el.  Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni.
   - Adja meg a **virtuális gép kizárása lista (karakterlánc)**. Azt a nevet, egy vagy több virtuális gépek a célként megadott erőforráscsoportja.  Adjon meg egynél több nevet, és külön alapján (értékei nem kis-és nagybetűket) vesszővel válassza el.  Helyettesítő karakterek használatával támogatott.
   - Válassza ki a **ütemezés**. Ez az ismétlődő dátum és idő és a virtuális gépek a cél-erőforráscsoport leállításáért.  Alapértelmezés szerint az ütemezés az UTC időzóna van konfigurálva. Egy másik régió kiválasztásával nem érhető el.  A megoldás konfigurálása után az ütemezés a megadott időzóna konfigurálásához lásd: [indítási és leállítási ütemezésének módosítása](#modifying-the-startup-and-shutdown-schedule).
   - Fogadásához **E-mail értesítések** a SendGrid, fogadja el alapértékként a **Igen** , és adjon meg egy érvényes e-mail címet. Ha **nem** , de egy későbbi időpontban döntse el, hogy szeretne kapni az e-mail értesítések, frissítheti a **External_EmailToAddress** érvényes e-mail címmel rendelkező változó, vesszővel elválasztott, majd a változó módosítása **External_IsSendEmail** értékű **Igen**.  

9. Miután konfigurálta a kezdeti beállításai, a megoldáshoz szükséges, válassza ki a **létrehozása**.  Minden beállítás ellenőrzését követően a megoldást már telepítették az előfizetéséhez.  A folyamat eltarthat néhány másodpercig befejezéséhez, és nyomon követheti a folyamat állapotát **értesítések** a menüből. 

## <a name="collection-frequency"></a>A gyűjtés gyakorisága

Automation-feladat napló és a feladat adatfolyamokat van a Naplóelemzési tárházba okozhatnak 5 percenként.  

## <a name="using-the-solution"></a>A megoldás használata

A Virtuálisgép-kezelési megoldás hozzáadásakor a **StartStopVMView** csempe az irányítópulton való rögzítéséhez a Naplóelemzési munkaterület hozzáadott Azure-portálról.  Ez a csempe számát és a runbook-feladatokhoz, amelyek sikeresen befejeződött, és elindította a megoldás grafikus ábrázolása jeleníti meg.<br><br> ![Virtuális gépek felügyelete – StartStopVM nézet csempe](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Az Automation-fiókban férhessen hozzá és felügyelhesse a megoldás kiválasztásával a **munkaterület** lehetőséget. A Naplóelemzési lapon válassza az **megoldások** a bal oldali ablaktáblán.  Az a **megoldások** lapon, válassza ki a megoldás **Start-Stop-VM [munkaterület]** a listából.<br><br> ![A Naplóelemzési megoldások listája](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Látható értesítések valamelyikének kiválasztásakor a megoldás a **Start-Stop-VM [munkaterület]** megoldás ablaktáblán. Itt megtekintheti fontos részleteket, mint a **StartStopVM** csempére. Naplóelemzési munkaterületet, mint ez a csempe számát és a runbook-feladatok a megoldás, amely indult el, és sikeresen befejeződött-e grafikus ábrázolása jeleníti meg.<br><br> ![Automatizálási frissítéskezelés megoldás lap](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Itt végezheti el további elemzése a feladatrekordok fánk csempére kattintva. A megoldás irányítópultja feladat előzményeket, és előre meghatározott napló keresési lekérdezések. A napló Analytics speciális portálra való keresésre kapcsoló a keresési lekérdezések alapján.  

Az összes fölérendelt runbook közé tartozik a *WhatIf* paraméter. Ha beállítása **igaz**, ezt támogatja, és részletesen leírja a pontos viselkedés, a runbook futtatása nélkül; a *WhatIf* paraméter, és érvényesíti a megfelelő virtuális gépek célozzák. Runbookok csak végezze el a definiált műveletek során a *WhatIf* paraméter értéke **hamis**.  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>1. forgatókönyv: Napi indítása/leállítása virtuális gépek között előfizetés vagy a cél erőforráscsoport-sablonok 

Ez a beállítás az alapértelmezett üzembe helyezésekor a megoldás.  Ön konfigurálhatja például úgy, hogy minden virtuális gép leállítása egy előfizetésből, ha az este munka hagyja, és ha áll vissza a office reggel elindítani ezeket. Az ütemezés konfigurálása során **ütemezett-StartVM** és **ütemezett-StopVM** központi telepítése során start, és állítsa le a célként kijelölt virtuális gépek.
>[!NOTE]
>Az időzóna kell az aktuális időzónában, ha az ütemezés paraméter. Azonban tárolódik az Azure Automationben UTC formátumban.  Nem kell bármely időzóna átalakítása elvégezni, mivel ez a telepítés során történik.

Szabályozhatja, amelyek a virtuális gépek hatókör úgy konfigurálja a következő változók: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, és **External_ ExcludeVMNames**.  

>[!NOTE]
> A következő **célként megadott erőforráscsoport-nevek** mindkét értékét tárolja **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames**. További granularitási ezek a változók, amelyekre a különböző erőforráscsoportokra mindegyikének módosíthatók.  Az indítási műveletet használja **External_Start_ResourceGroupNames**, és a leállítási művelet, használjon **External_Stop_ResourceGroupNames**. Virtuális gépek automatikusan hozzáadódnak a start, és állítsa le az ütemezéseket.

Teszteléséhez, és a konfiguráció érvényesítéséhez, manuálisan indítsa el a **ScheduledStartStop_Parent** runbook és a művelet paraméternek **start** vagy **leállítása** és a WHATIF a paraméter **igaz**.<br><br> ![A runbook paramétereinek a konfigurálása](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 Tekintse meg az ütemezett művelet, és az üzemi virtuális gépek elleni implementálása előtt végezze el a szükséges módosításokat.  Manuálisan hajthatja végre a runbook a paraméter **hamis**, vagy hagyhatja, hogy az automatizálás ütemezést **ütemezés-StartVM** és **ütemezés-StopVM** futtatása az előírt ütemezés automatikusan követően.

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>2. forgatókönyv: A indítása/leállítása virtuális gépek egy előfizetésből feladatütemezési címkék használatával
Egy olyan környezetben, egy elosztott terheléseknél engedélyezett támogató több virtuális gépekre két vagy több összetevője is megtalálható összetevők elindul vagy leáll, amelyben a feladatütemezési támogatása érdekében fontos.  Ez a következő lépések elvégzésével érhető el:

1. Adja hozzá a **SequenceStart** és egy **SequenceStop** pozitív egész értéket a virtuális gépekhez, amelyek a címkézés **External_Start_ResourceGroupNames** és  **External_Stop_ResourceGroupNames** változók.  A kezdési és befejezési műveletek növekvő sorrendben kell végrehajtani.  A virtuális gépek címkézésére, lásd: [címke Windows virtuális gépként az Azure-ban](../virtual-machines/windows/tag.md) és [Linux virtuális gépek címke az Azure-ban](../virtual-machines/linux/tag.md).
2. A ütemezésének módosítása **Sequenced-StartVM** és **Sequenced-StopVM** dátumát és idejét, amelyek megfelelnek az elvárásainak, és engedélyezi az ütemezést.  

Teszteléséhez, és a konfiguráció érvényesítéséhez, manuálisan indítsa el a **SequencedStartStop_Parent** runbook. A művelet paraméternek **start** vagy **leállítása** és a WHATIF paraméter **igaz**.<br><br> ![A runbook paramétereinek a konfigurálása](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Tekintse meg a műveletet, és az üzemi virtuális gépek elleni implementálása előtt végezze el a szükséges módosításokat.  Kész, manuálisan végrehajtása közben a runbook a paraméter értéke **hamis**, illetve engedélyezheti, hogy az automatizálás ütemezést **Sequenced-StartVM** és **Sequenced-StopVM** futtatása az előírt ütemezés automatikusan követően.  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>3. forgatókönyv: Automatizálásához virtuális gépek indítása/leállítása a CPU-használat alapján előfizetésből
Ez a megoldás segítséget nyújtanak a futó virtuális gépek az előfizetéshez, Azure virtuális gépek nem használt csúcsidőszakon kívüli időszakokban például óra elteltével értékelésével, és automatikusan leáll őket, ha a processzor kihasználtságát záró x % költségét.  

Alapértelmezés szerint a megoldás az átlagos kihasználtság 5 százalékkal meg, hogy a százalékos CPU metrika kiértékeléséhez előre konfigurált vagy kisebb.  Ez a következő változók vezérli, és módosítható, ha az alapértelmezett értékek nem felelnek meg a követelményeknek:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Engedélyezheti vagy a műveletet egy előfizetésre, és az erőforráscsoportot, vagy célcsoportkezeléssel virtuális gépeket, de nem mindkettőt adott listáját.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>A stop műveletet előfizetésbe és erőforráscsoportba csoport cél

1. Konfigurálja a **External_Stop_ResourceGroupNames** és **External_ExcludeVMNames** változókat, adja meg a cél virtuális gépek.  
2. Engedélyezi, és frissítse a **Schedule_AutoStop_CreateAlert_Parent** ütemezést.
3. Futtassa a **AutoStop_CreateAlert_Parent** az ACTION paraméterben beállítása runbook **start** és a WHATIF paraméter **igaz** a módosítások megtekintéséhez.

#### <a name="target-the-stop-action-by-vm-list"></a>A leállítási művelet a célzott VM listája

1. Futtassa a **AutoStop_CreateAlert_Parent** az ACTION paraméterben beállítása runbook **start**, adja hozzá a virtuális gépek vesszővel tagolt listáját a *VMList* paramétert, majd állítsa be a WHATIF paraméter **igaz**. Tekintse meg a módosításokat.  
2. Konfigurálja a **External_ExcludeVMNames** paraméter VMs (VM1, vm2 virtuális gépnek, VM3) vesszővel tagolt listáját.
3. Ez a forgatókönyv nem veszi figyelembe a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** változók.  A jelen esetben kell létrehozni a saját automatizálás ütemezést. További információkért lásd: [az Azure Automationben runbook ütemezése](../automation/automation-schedules.md).

Most, hogy leállítja a virtuális gépek CPU-használat alapján ütemezését, kell elindítani őket a következő ütemezések engedélyezhető.

* Cél indítsa el a művelet által előfizetésben és erőforráscsoportban.  Olvassa el a [1. forgatókönyv](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) a teszteléshez és engedélyezése **ütemezett-StartVM** ütemezések.
* Cél elindítja az előfizetést, az erőforráscsoport és címke műveletet.  Olvassa el a [2. forgatókönyv](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) a teszteléshez és engedélyezése **Sequenced-StartVM** ütemezések.


### <a name="configuring-email-notifications"></a>E-mail értesítések konfigurálása

E-mail értesítések beállítása után a megoldást már telepítették, a következő három változók módosítása:

* External_EmailFromAddress: Adja meg a feladó e-mail címe.
* External_EmailToAddress: E-mail címeket vesszővel tagolt listáját adja meg (user@hotmail.com, user@outlook.com) értesítési e-mailek fogadásához.
* External_IsSendEmail: Beállítása **Igen** e-mailek fogadásához. Az e-mail értesítések letiltásához értékét állítsa **nem**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Az indítási és leállítási ütemezésének módosítása

Ebben a megoldásban indítási és leállítási ütemezések kezelése követi ugyanazokat a lépéseket, a [az Azure Automationben runbook ütemezése](automation-schedules.md).     

## <a name="log-analytics-records"></a>Log Analytics-rekordok

Automatizálás az OMS-tárházban hoz létre két típusú rekordok: sikertelen feladat-naplók és a feladat-adatfolyamokat.

### <a name="job-logs"></a>Feladatnaplók

Tulajdonság | Leírás|
----------|----------|
Hívó |  A művelet kezdeményezője.  Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
Kategória | Az adattípus besorolása.  Az Automation esetében az érték JobLogs.|
CorrelationId | GUID, a runbook-feladat korrelációs azonosítója.|
JobId | GUID, a runbook-feladat azonosítója.|
operationName | Meghatározza az Azure-ban végrehajtott művelet típusát.  Az automatizáláshoz értéke feladat.|
resourceId | Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat.  Az Automation esetében az érték Azure Automation.|
ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
resultType | A runbook-feladat állapota.  Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres|
resultDescription | Ismerteti a runbook-feladat eredményállapotát.  Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött|
RunbookName | Megadja a runbook-feladat nevét.|
SourceSystem | Megadja az elküldött adatok forrásrendszerét.  Az automatizáláshoz a értéke OpsManager|
StreamType | Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés|
SubscriptionId | Megadja a feladat előfizetési azonosítóját.
Time | A runbook-feladat végrehajtásának dátuma és időpontja.|


### <a name="job-streams"></a>Feladatstreamek

Tulajdonság | Leírás|
----------|----------|
Hívó |  A művelet kezdeményezője.  Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
Kategória | Az adattípus besorolása.  Az Automation esetében az érték JobStreams.|
JobId | GUID, a runbook-feladat azonosítója.|
operationName | Meghatározza az Azure-ban végrehajtott művelet típusát.  Az automatizáláshoz értéke feladat.|
ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
resourceId | Meghatározza az Azure-ban szereplő erőforrás azonosítóját.  Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat.  Az Automation esetében az érték Azure Automation.|
ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
resultType | A runbook-feladat eredménye az esemény létrehozásának időpontjában. Egy lehetséges értéke:<br>- Folyamatban|
resultDescription | A runbook kimeneti streamjét tartalmazza.|
RunbookName | A runbook neve.|
SourceSystem | Megadja az elküldött adatok forrásrendszerét.  Az automatizáláshoz értéke OpsManager.|
StreamType | A feladatstream típusa. Lehetséges értékek:<br>-Folyamat<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes|
Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

A napló keresési kategória rekordjának visszaadó végrehajtásakor **JobLogs** vagy **JobStreams**, kiválaszthatja a **JobLogs** vagy **JobStreams**nézetet, amely a frissítéseket, a keresés által visszaadott összefoglalójához csempék megjelenítése.

## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a megoldás által összegyűjtött feladatrekordokkal kapcsolatos naplókeresési mintákat tartalmazza. 

Lekérdezés | Leírás|
----------|----------|
Feladatok található runbook ScheduledStartStop_Parent, hogy sikeresen befejeződött | Keresés kategória == "JobLogs" &#124; Ha (RunbookName_s == "ScheduledStartStop_Parent") &#124; Ha (ResultType == "Kész") &#124; AggregatedValue összefoglalója = count() ResultType, a bin (TimeGenerated, 1 óra) &#124; Rendezze a TimeGenerated desc|
Feladatok található runbook SequencedStartStop_Parent, hogy sikeresen befejeződött | Keresés kategória == "JobLogs" &#124; Ha (RunbookName_s == "SequencedStartStop_Parent") &#124; Ha (ResultType == "Kész") &#124; AggregatedValue összefoglalója = count() ResultType, a bin (TimeGenerated, 1 óra) &#124; Rendezze a TimeGenerated desc

## <a name="removing-the-solution"></a>A megoldás eltávolítása

Ha úgy dönt, hogy már nincs szüksége a megoldást kíván használni, az Automation-fiók törlése.  Csak a megoldás törlése eltávolítja a runbookokat. Az ütemezések vagy a megoldás hozzáadásakor létrehozott változók nem törli.  Ha nem használ azokat más runbookok manuálisan törölnie kell az eszközöket.  

Törölje a megoldást, hajtsa végre az alábbi lépéseket:

1.  Válassza ki az Automation-fiók **munkaterület** a bal oldali ablaktáblán.  
2.  Az a **megoldások** lapon, válassza ki a megoldás **Start-Stop-VM [munkaterület]**.  Az a **VMManagementSolution [munkaterület]** a menüből válassza a lap **törlése**.<br><br> ![Virtuálisgép-felügyeleti megoldás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Az a **megoldás törlése** ablakban győződjön meg arról, hogy szeretné-e a megoldás törlése.
4.  Az adatokat a rendszer ellenőrzi, és a megoldás törlése, nyomon követheti a folyamat állapotát **értesítések** a menüből.  A rendszer visszairányítja a **megoldások** el akarja távolítani a megoldás elindulása után lapon.  

Az Automation-fiók és a Naplóelemzési munkaterület nem törlődnek a folyamat részeként.  Ha nem szeretné megőrizni a Naplóelemzési munkaterület, törölje manuálisan szeretné.  Ehhez az Azure-portálon:

1.    Válassza ki az Azure portál kezdőképernyő, **Naplóelemzési**.
2. Az a **Naplóelemzési** ablaktáblán válassza ki a munkaterületen.
3. Válassza ki **törlése** menüjéből a beállítások munkaterület ablaktáblán.  
      
## <a name="next-steps"></a>További lépések

- Különböző keresési lekérdezések összeállításához, és tekintse át az Automation-feladat naplók és a Naplóelemzési kapcsolatos további tudnivalókért lásd: [Log Analytics-e jelentkezni a keresések](../log-analytics/log-analytics-log-searches.md).
- A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza.
- Log Analytics és a gyűjtemény adatforrások kapcsolatos további információkért lásd: [gyűjtése Azure storage adatok a Naplóelemzési – áttekintés](../log-analytics/log-analytics-azure-storage.md).






   

