---
title: "Munkaidőn kívüli megoldás során indítása/leállítása VMs |} Microsoft Docs"
description: "A Virtuális gépek felügyelete megoldás egy ütemezés alapján elindítja és leállítja az Azure Resource Manager virtuális gépeit, és proaktívan figyeli azokat a Log Analytics szolgáltatásból."
services: automation
documentationCenter: 
authors: georgewallace
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: e6f1189b9729c57718a5cd6d6f6a583b94f6f142
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Indítása/leállítása virtuális gépek során munkaidőn kívüli megoldás az Azure Automationben

A munkaidőn kívüli megoldás elindul, és az Azure virtuális gépeken nem felhasználó által definiált ütemezés, insights Naplóelemzési keresztül biztosít, és választható e-mailt küld, ami alatt indítása/leállítása VMs [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). A legtöbb esetben Azure Resource Manager és klasszikus virtuális gépeket is támogatja. 

Ez a megoldás decentralizált automation képességet biztosít az ügyfelek számára, akik a kiszolgáló nélküli, az alacsony költségű erőforrásokat alkalmazhat költségek csökkentése érdekében. Funkciók:

* Ütemezés virtuális gép indítása/leállítása
* Virtuális gép indítása/leállítása növekvő sorrendben (klasszikus virtuális gépek esetén nem támogatott) Azure-címkék használatával ütemezése
* Virtuális gépek alacsony CPU-használat alapján automatikus leállítása

## <a name="prerequisites"></a>Előfeltételek

- A runbookok (forgatókönyvek) [Azure-futtatófiókkal](automation-offering-get-started.md#authentication-methods) használhatóak.  A futtatófiók az előnyben részesített hitelesítési módszer, mivel az tanúsítványalapú hitelesítést használ a jelszó helyett, ami lejárhat vagy gyakran változhat.  

- Ez a megoldás csak kezelheti a virtuális gépek, amelyek ugyanahhoz az előfizetéshez, ahol az Automation-fiók található.  

- Ez a megoldás csak a következő Azure-régiók - Ausztrália délkeleti, Kanada központi, közép-Indiában, USA keleti régiója, kelet-japán, Délkelet-Ázsiában, Egyesült Királyság déli régiója és Nyugat-Európában központilag telepíti.  
    
    > [!NOTE]
    > A runbookok kezelése a virtuális gép ütemezés célba virtuális gépek bármely régióban.  

- E-mail értesítéseket küldhet a kezdési és befejezési VM runbookok Azure piactérről bevezetése közben befejeződése után ki kell jelölnie válassza **Igen** SendGrid telepítéséhez. 

    > [!IMPORTANT]
    > SendGrid egy külső szolgáltatás, a sendgrid támogatásért lépjen kapcsolatba [SendGrid](https://sendgrid.com/contact/).  
    >
   
    A SendGrid korlátozásai a következők:

    * Legfeljebb egy SendGrid fiók előfizetésenként felhasználónként
    * Legfeljebb két SendGrid fiókok előfizetésenként

Ha ez a megoldás korábbi verziójának telepített, akkor először törli a fiókhoz tartozó Ez a kiadás telepítése előtt.  

## <a name="solution-components"></a>Megoldás-összetevők

Ez a megoldás tartalmaz előre beállított runbookok ütemezése és, amely lehetővé teszi a indításakor és leállásakor a virtuális gépek suite testre szabni az üzleti Log Analyticshez való integráció kell. 

### <a name="runbooks"></a>Runbookok

A következő táblázat felsorolja a forgatókönyvek az Automation-fiók telepített.  Nem ajánlott, hogy módosítja a runbook-kód, de ahelyett, hogy az új szolgáltatásokat saját runbook írása.

> [!NOTE]
> Közvetlenül ne futtassa a minden olyan forgatókönyvben annak nevét a rendszer "Gyermek" néven.
>

Minden szülő runbook magában foglalja a *WhatIf* paraméter, amikor beállítása **igaz**, és részletesen leírja a runbook; nélkül futtatásakor pontos viselkedését támogatja a *WhatIf* a paraméter, és érvényesíti a megfelelő virtuális gépek irányuló.  A Runbookok csak a megadott műveleteket hajtja végre amikor a *WhatIf* paraméter értéke **hamis**. 

|**A Runbook** | **Paraméterek** | **Leírás**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Csak a szülő runbook hívása. Remote forgatókönyv / erőforrás alapját riasztásokat hoz létre.| 
|AutoStop_CreateAlert_Parent | WhatIf: IGAZ vagy HAMIS eredményt ad <br> VMList | Létrehozza vagy frissíti a célként megadott előfizetés vagy az erőforrás-csoportok a virtuális gépeken Azure riasztási szabályok. <br> VMList: Vesszővel tagolt virtuális gépek listája.  Például *vm1, vm2 virtuális gépnek, vm3*| 
|AutoStop_Disable | nincs | Tiltsa le a Remote riasztások és az alapértelmezett ütemezés.| 
|AutoStop_StopVM_Child | WebHookData | Csak a szülő runbook hívása. Riasztási szabályok meghívja ezt a runbookot, és az hajtja végre a virtuális gép leállítása a munkáját.|  
|Bootstrap_Main | nincs | A beállításról a rendszerindítási beállításokat, például a webhookURI, amely általában nem érhető el az Azure Resource Manager egyszer használható. Ezt a runbookot a rendszer automatikusan eltávolítja, ha a telepítés sikeresen állapotba került.|  
|ScheduledStartStop_Child | VMName <br> Művelet: Leállítása vagy elindítása <br> erőforráscsoport-név | Csak a szülő runbook hívása. A tényleges végrehajtásának leállítása vagy elindítása az ütemezett leállásra nem.|  
|ScheduledStartStop_Parent | Művelet: Leállítása vagy elindítása <br> WhatIf: IGAZ vagy HAMIS eredményt ad | Ez a lép érvénybe minden virtuális gép az előfizetés kivéve, ha szerkeszti a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** beállítás korlátozza, amely úgy, hogy csak ezek a végrehajtási cél-erőforráscsoport. Adott virtuális gépek frissítésével emellett kizárhatók a **External_ExcludeVMNames** változó. WhatIf viselkedik ugyanaz, mint más runbookokat.|  
|SequencedStartStop_Parent | Művelet: Leállítása vagy elindítása <br> WhatIf: IGAZ vagy HAMIS eredményt ad | Nevű címke létrehozásához **SequenceStart** és egy másik címke nevű **SequenceStop** minden egyes virtuális gépen használni kívánt feladatütemezési start\\állítsa le a tevékenységet. A címke értékének pozitív egész szám (1,2,3), amely megfelel a elindítja a sorrend kell\\befejezése növekvő sorrendben. WhatIf viselkedik ugyanaz, mint más runbookokat. <br> **Megjegyzés: Virtuális gépek az Azure Automation-változók meghatározott External_Start_ResourceGroupNames External_Stop_ResourceGroupNames és External_ExcludeVMNames erőforráscsoportokon belül legyen, és rendelkezik a megfelelő címkéket az érvénybe lépéséhez műveletek.**|

### <a name="variables"></a>Változók

A következő táblázat felsorolja az Automation-fiókban létrehozott változókat.  Csak a következő előtaggal változók módosítása ajánlott **külső**, változók módosítása a következő előtaggal **belső** nemkívánatos hatások okoz.  

|**Változó** | **Leírás**|
---------|------------|
|External_AutoStop_Condition | Ez a feltétel beállítása előtt a riasztást kiváltó szükséges a feltételes operátort. Elfogadható értékek a következők **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, **LessThanOrEqual**.|  
|External_AutoStop_Description | Riasztás, hogy a virtuális gép leállítása, ha a CPU aránya túllépi a küszöbértéket.|  
|External_AutoStop_MetricName | A teljesítmény a metrika az Azure riasztási szabály neve nem lehet konfigurálni az.| 
|External_AutoStop_Threshold | Az Azure riasztási szabály a változóban megadott küszöbértékét *External_AutoStop_MetricName*. Százalékos érték 1 és 100 között lehet.|  
|External_AutoStop_TimeAggregationOperator | Az idő összesítő operátor alkalmazandó a kijelölt ablak mérete, a feltétel kiértékeléséhez. Elfogadható értékek a következők **átlagos**, **minimális**, **maximális**, **teljes**, **utolsó**.|  
|External_AutoStop_TimeWindow | Az ablak mérete, amelyben Azure elemzi a riasztást kiváltó kiválasztott metrika. Ez a paraméter timespan formátumú bemenetet fogad el. Lehetséges értékek: 5 perc és 6 óra.|  
|External_EmailFromAddress | Adja meg az e-mailek feladójának.|  
|External_EmailSubject | Megadja az e-mail tárgysorának szövegét.|  
|External_EmailToAddress | Megadja az e-mail címzettjét/címzettjeit. A neveket vesszővel válassza el.|  
|External_ExcludeVMNames | Adjon meg virtuális gépek nevénél ki lesznek zárva, neveket, szóközök nélkül vesszővel elválasztva.|  
|External_IsSendEmail | Adja meg a beállítás befejezése után e-mail értesítést küldeni.  Adja meg **Igen** vagy **nem** e-mail nem küldhető.  Ezt a beállítást kell **nem** Ha SendGrid nem hozta létre, a kezdeti üzembe helyezése során.|  
|External_Start_ResourceGroupNames | Adja meg egy vagy több erőforráscsoportok, ahol vesszővel válassza el, érvényes kezdő műveletek az értékeket.|  
|External_Stop_ResourceGroupNames | Adja meg egy vagy több erőforrás olyan csoportot, ahol értékeket vesszővel válassza el egymástól, Stop műveleteket a megcélzott.|  
|Internal_AutomationAccountName | Megadja az Automation-fiók nevét.|  
|Internal_AutoSnooze_WebhookUri | Megadja a Remote forgatókönyvhöz nevű Webhook URI.|  
|Internal_AzureSubscriptionId | Az Azure-előfizetés-azonosítót határozza meg.|  
|Internal_ResourceGroupName | Adja meg az Azure Automation szolgáltatásbeli fiók erőforráscsoportjának neve.|  
|Internal_SendGridAccountName | A SendGrid-fiók nevét adja meg.|  
|Internal_SendGridPassword | Adja meg a SendGrid-fiók jelszavát.|  

<br>

Mindegyik forgatókönyvben között a **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, és **External_ExcludeVMNames** változók szükségesek. a virtuális gépek célzásához kivételével biztosító vesszővel elválasztott a virtuális gépek a **AutoStop_CreateAlert_Parent** runbook. Ez azt jelenti, hogy a virtuális gépek cél erőforráscsoport indítása/leállítása műveletekhez megtörténik kell lennie. A logikai kicsit hasonlóan működik Azure házirend, az előfizetés vagy az erőforrás csoportot célozza, és műveleteket újonnan létrehozott virtuális gépek által örökölt. Ez a megközelítés elkerülhető, hogy nem kell külön ütemezés minden virtuális gép kezelheti és a skála indítása/leállítása.

### <a name="schedules"></a>Ütemezések

A következő táblázat felsorolja az egyes az alapértelmezett ütemezését az Automation-fiókban létrehozni.  Módosíthatja azokat, vagy hozzon létre egy saját egyéni ütemezést.  Alapértelmezés szerint minden ezeket az ütemezéseket le vannak tiltva kivéve **Scheduled_StartVM** és **ütemezett-StopVM**.

Nem ajánlott az összes ütemtervét engedélyezni, ez hozható létre, hogy milyen ütemezés végrehajt egy műveletet átfedésben.  Ehelyett lenne meghatározni, melyik optimalizálást, hajtsa végre, és ennek megfelelően módosítsa a legjobb.  Tekintse meg a kapcsolódó további tájékoztatást a áttekintés szakaszban szereplő példakörnyezetek.   

|**ScheduleName** | **Gyakoriság** | **Leírás**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8 óránként fut. | 8 óránként, ami viszont a továbbiakban nem a virtuális gép alapján értékek a "External_Start_ResourceGroupNames", "External_Stop_ResourceGroupNames" és "External_ExcludeVMNames" Azure Automation-változók a AutoStop_CreateAlert_Parent runbook fut.  Másik lehetőségként a "VMList" paraméter használatával virtuális gépek vesszővel elválasztott listáját is megadhat.|  
|Scheduled_StopVM | Felhasználó által megadott, naponta | Futtatja a Scheduled_Parent runbook egyik paramétere "Stop" a megadott időpontban naponta.  Minden virtuális gép, amelyek megfelelnek a megadott eszköz változók keresztül szabályok automatikusan leáll.  Javasoljuk, hogy a testvérének engedélyezése ütemezése, ütemezett-StartVM.|  
|Scheduled_StartVM | Felhasználó által megadott, naponta | A Scheduled_Parent runbookot futtat egy paraméterrel a *Start* minden nap adott időpontban.  Automatikusan elindítja az összes virtuális gép, amelyek megfelelnek a megadott szabályok és a megfelelő változók része.  Engedélyezi a testvérének ajánlott ütemezés szerint **ütemezett-StopVM**.|
|Előkészített StopVM | 13:00:00 (UTC) szerint péntekenként | A Sequenced_Parent runbookot futtat egy paraméterrel a *leállítása* péntekenként az adott időpontban.  Egymás után lesznek a címkével ellátott tartozó összes virtuális gép (növekvő) stop **SequenceStop** meghatározott és a megfelelő változók része.  Tekintse meg a Runbookok című szakaszban talál további információt előfizetéscímkék értékeit és eszköz változókat.  Azt javasoljuk, engedélyezi a testvérének ütemezés szerint **Sequenced-StartVM**.|
|Előkészített StartVM | 1:00 PM idő szerint (UTC), minden hétfőn | A Sequenced_Parent runbookot futtat egy paraméterrel a *Start* minden hétfőn, a megadott időpontban.  Egymás után lesznek a címkével ellátott tartozó összes virtuális gép (csökkenő) kezdő **SequenceStart** meghatározott és a megfelelő változók része.  Tekintse meg a Runbookok című szakaszban talál további információt előfizetéscímkék értékeit és eszköz változókat.  Engedélyezi a testvérének ajánlott ütemezés szerint **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Konfiguráció

Hajtsa végre az alábbi lépéseket, hogy a Virtuális gépek indítása/leállítása munkaidőn kívül [előzetes verzió] megoldást hozzáadhassa az Automation-fiókjához, majd a megoldás testreszabásához konfigurálja a változókat.

1. Az Azure Portalon kattintson az **Új** elemre.<br> ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. Írja be a piactér ablaktáblában **VM indítása**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **indítása/leállítása virtuális gépek munkaidőn kívüli [előzetes verzió] során** a keresési eredmények.  
3. Az a **indítása/leállítása virtuális gépek során munkaidőn kívüli [előzetes verzió]** ablaktáblán a kiválasztott megoldás, ellenőrizze az összefoglaló információkat, és kattintson a **létrehozása**.  
4. A **megoldás hozzáadása** ablaktáblán jelenik meg, ahová kéri a megoldás konfigurálása előtt importálhatja az Automation-előfizetés.<br><br> ![Virtuálisgép-felügyelet – Megoldás hozzáadása panel](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  Az a **megoldás hozzáadása** panelen válassza **munkaterület** és itt választhatja ki az OMS-munkaterület, amelyek azonos Azure-előfizetést, amely az Automation-fiókhoz csatolva van, vagy hozzon létre egy új munkaterületet.  Ha Ön nem rendelkezik a munkaterületen, kiválaszthat **új munkaterület létrehozása** és az a **OMS-munkaterület** ablaktáblán tegye a következőket: 
   - Adja meg az új **OMS-munkaterület** nevét.
   - A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   - Az **Erőforráscsoport** területen létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt.  
   - Válasszon ki egy **helyet**.  A kijelölés csak helyeken jelenleg **Ausztrália délkeleti**, **Kanada központi**, **közép-Indiában**, **USA keleti régiója**, **Kelet-Japánban**, **Délkelet-Ázsia**, **Egyesült Királyság déli régiója**, és **Nyugat-Európában**.
   - Válasszon egy tarifacsomagot a **Tarifacsomag** területen.  A megoldás két csomagban választható: ingyenes és OMS fizetett csomag.  Az ingyenes csomagnál korlátozva van a naponta összegyűjtött adatok mennyisége, a megőrzési időtartam és a runbook-feladatok futásideje (perc).  Az OMS fizetett csomagnál a naponta összegyűjtött adatok mennyisége nincs korlátozva.  

        > [!NOTE]
        > A / GB (önálló) fizetett réteg lehetőség jelenik meg, amíg nincs alkalmazható.  Ha kiválasztja, és ezt a megoldást akarja létrehozni az előfizetésben, akkor az sikertelen lesz.  Csak akkor lesz választható, ha ezt a megoldást hivatalosan is kiadják.<br>Ha ezt a megoldást használja, az csak az automatizálási feladat idejét (percben), illetve a naplófeldolgozást fogja használni.  A megoldás nem egészíti ki további OMS-csomópontokkal a környezetét.  

6. Miután az **OMS-munkaterület** panelen megadta a szükséges adatokat, kattintson a **Létrehozás** gombra.  Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet.  Ezután visszatér a **Megoldás hozzáadása** panelre.  
7. A **Megoldás hozzáadása** panelen válassza ki az **Automation-fiók** elemet.  Új OMS-munkaterület létrehozásakor egy hozzá társított új Automation-fiókot is létre kell hoznia, valamint meg kell adnia az Azure-előfizetését, az erőforráscsoportot és a régiót.  Kiválaszthatja az **Automation-fiók létrehozása** lehetőséget, és az **Automation-fiók hozzáadása** panelen megadhatja a következő adatokat: 
  - A **Név** mezőbe írja be az Automation-fiók nevét.

    A rendszer a kiválasztott OMS-munkaterület alapján automatikusan kitölti az összes többi beállítást, amelyek utána nem módosíthatók.  A megoldásban szereplő runbookok alapértelmezett hitelesítési módszere egy Azure-futtatófiók.  Miután rákattintott az **OK** gombra, a rendszer érvényesíti a konfigurációs beállításokat, és létrehozza az Automation-fiókot.  Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását. 

    Ellenkező esetben kiválaszthat egy meglévő Automation-futtatófiókot.  Vegye figyelembe, hogy nem választhat ki már egy másik OMS-munkaterülethez kapcsolódó fiókot, ellenkező esetben a panelen megjelenik egy tájékoztató üzenet.  Ha a kiválasztott fiók már kapcsolódik, egy másik Automation-futtatófiókot kell választania, vagy létre kell hoznia egy újat.<br><br> ![Az Automation-fiók már kapcsolódik egy OMS-munkaterülethez](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Végül a **Megoldás hozzáadása** panelen válassza ki a **Konfiguráció** elemet, és ekkor megjelenik a **Paraméterek** panel.<br><br> ![Paraméterek panel megoldás](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  A **Paraméterek** panelen a rendszer az alábbi műveletek elvégzésére kéri:  
   - Adja meg a **Cél erőforráscsoport nevét**, ami annak az erőforráscsoportnak a neve, amely a megoldás által felügyelendő virtuális gépeket tartalmazza.  Adjon meg egynél több nevet, és külön az egyes (értékei nem kis-és nagybetűket) vesszővel válassza el.  Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni.
   - Specity a **virtuális gép kizárása lista (karakterlánc)**, a nevét vagy a célként megadott erőforráscsoportja további virtuális gépeket.  Adjon meg egynél több nevet, és külön az egyes (értékei nem kis-és nagybetűket) vesszővel válassza el.  Helyettesítő karakterek használatával támogatott.
   - Válasszon ki egy olyan **ütemezést**, amely a cél erőforráscsoport(ok)ban lévő virtuális gépek ismétlődő elindítási és leállítási dátuma és időpontja.  Alapértelmezés szerint az ütemezés az UTC időzóna van konfigurálva, és egy másik régió kiválasztásával nem érhető el.  Ha a megoldás konfigurálása után az ütemezés a megadott időzóna konfigurálásának [indítási és leállítási ütemezésének módosítása](#modifying-the-startup-and-shutdown-schedule) alatt.
   - Fogadásához **E-mail értesítések** a SendGrid, fogadja el alapértékként a **Igen** , és adjon meg egy érvényes e-mail címet.  Ha **nem** és később úgy dönt, azt szeretné, ha e-mail értesítést szeretne kapni, akkor hozza létre újra az Azure piactérről megoldás.  

10. Miután befejezte a megoldás kezdeti beállításainak konfigurálását, válassza ki a **Létrehozás** lehetőséget.  A rendszer érvényesíti az összes beállítást, majd megpróbálja üzembe helyezni a megoldást az előfizetésében.  A folyamat eltarthat néhány másodpercig, az előrehaladását nyomon követheti az **Értesítések** menüpont alatt. 

## <a name="collection-frequency"></a>A gyűjtés gyakorisága

Automation-feladat napló és a feladat adatfolyamokat van a Naplóelemzési tárházba okozhatnak 5 perc.  

## <a name="using-the-solution"></a>A megoldás használata

A Virtuálisgép-kezelési megoldás a Naplóelemzési munkaterület az Azure portálról való hozzáadásakor a **StartStopVM nézet** csempe az irányítópulton való rögzítéséhez kerül.  Ez a csempe megjeleníti egy számát és a megoldás, amelyek sikeresen befejeződött, és elindította a runbookok feladatok grafikus ábrázolása.<br><br> ![Virtuális gépek felügyelete – StartStopVM nézet csempe](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Az Automation-fiókban férhessen hozzá és felügyelhesse a megoldás kiválasztásával a **munkaterület** lehetőséget, majd a Naplóelemzési lapon válassza az **megoldások** a bal oldali ablaktáblán.  Az a **megoldások** lapon, válassza ki a megoldás **Start-Stop-VM [munkaterület]** a listából.<br><br> ![A Naplóelemzési megoldások listája](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Megjeleníti a megoldás kiválasztása a **Start-Stop-VM [munkaterület]** megoldás panelen, melyen áttekintheti a fontos részletek, mint a **StartStopVM** csempére, például a Naplóelemzési munkaterületet, amely egy szám és a runbook-feladatok a megoldás, amely indult el, és sikeresen befejeződtek grafikus ábrázolása jeleníti meg.<br><br> ![Automatizálási frissítéskezelés megoldás lap](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Itt is elvégezheti további a feladatrekordok elemzése a fánk csempére kattintva és a megoldás irányítópultja a feladatelőzményekben, előre definiált naplófájl-keresési lekérdezések, jeleníti meg, és a keresési lekérdezések alapján kapcsolót, hogy a napló Analytics speciális portal kereséséhez.  

Minden szülő runbook magában foglalja a *WhatIf* paraméter, amikor beállítása **igaz**, és részletesen leírja a runbook; nélkül futtatásakor pontos viselkedését támogatja a *WhatIf* a paraméter, és érvényesíti a megfelelő virtuális gépek irányuló.  A Runbookok csak a megadott műveleteket hajtja végre amikor a *WhatIf* paraméter értéke **hamis**.  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>1. forgatókönyv: Naponta stop/start virtuális gépek egy előfizetésből vagy célzott erőforráscsoportok 

Ez a beállítás az alapértelmezett üzembe helyezésekor a megoldás.  Ön konfigurálhatja például úgy, hogy minden virtuális gép leállítása a este, ha munkahelyi hagyja, és indítsa el őket, amikor áll vissza a office délelőtt előfizetés között. Az ütemezés konfigurálása során ** ütemezett-StartVM "és **ütemezett-StopVM** központi telepítése során start, és állítsa le a célként kijelölt virtuális gépek.
>[!NOTE]
>Az időzóna kell az aktuális időzónában, ha az ütemezés paraméter; azonban tárolódik az Azure Automationben UTC formátumban.  Nem kell bármely időzóna átalakítása elvégezni, mivel ez a telepítés során történik.

Szabályozhatja, amelyek a virtuális gépek hatókör úgy konfigurálja a két változók - **External_Start_ResourceGroupNames**, ** External_Stop_ResourceGroupNames, és **External_ExcludeVMNames**.  

>[!NOTE]
> A változó értékét **célként megadott erőforráscsoport-nevek**"értékkel egyaránt tárolja **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupNames** változók. További granularitási ezek a változók, amelyekre a különböző erőforráscsoportokra mindegyikének módosíthatók.  Kezdő művelet használatra **External_Start_ResourceGroupNames** és leállítási művelet használatra **External_Stop_ResourceGroupNames** helyette. Új virtuális gépek automatikusan hozzáadódnak a start, és állítsa le az ütemezéseket.

Teszteléséhez, és a konfiguráció érvényesítéséhez, manuálisan indítsa el a **ScheduledStartStop_Parent** runbook és állítsa be a *művelet* paramétert **start** vagy **leállítása**  és a *WhatIf* paramétert **igaz**.<br><br> ![A runbook paramétereinek a konfigurálása](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Ez lehetővé teszi, hogy tekintse meg a műveletet, akkor kerül sor, és bármely módosításához szükség szerint üzemi virtuális gépek elleni végrehajtása előtt.  Miután kényelmes, manuálisan hajthatja végre a runbook a paraméter értéke **hamis** vagy lehetővé teszik az automatizálás ütemezést **ütemezés-StartVM** és **ütemezés-StopVM**futtassa a következő automatikusan a a előírt ütemezést.

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>2. forgatókönyv: A feladatütemezési leállítása vagy elindítása virtuális gépek címkék használatával egy előfizetésből
Egy olyan környezetben, egy elosztott terheléseknél engedélyezett támogató több virtuális gépekre két vagy több összetevője is megtalálható a sorozatot, amelynek összetevői sorrendben indítása/leállítása támogató fontos.  Ez a következő lépések elvégzésével érhető el.

1. Hozzáadása egy **SequenceStart** és **SequenceStop** egy pozitív egész értéket a virtuális gépek között, amelyek az előfizetése megjelölés **External_Start_ResourceGroupNames**és  **External_Stop*ResourceGroupNames** változók.  A kezdési és befejezési műveletek növekvő sorrendben történik.  A virtuális gépek címkézésére, lásd: [címke Windows virtuális gépként az Azure-ban](../virtual-machines/windows/tag.md) és [Linux virtuális gépek címke az Azure-ban](../virtual-machines/linux/tag.md)
2. A ütemezésének módosítása **Sequenced-StartVM** és **Sequenced-StopVM** az a dátum és idő a követelményeknek, és engedélyezi az ütemezést.  

Teszteléséhez, és a konfiguráció érvényesítéséhez, manuálisan indítsa el a **SequencedStartStop_Parent** runbook és állítsa be a *művelet* paramétert **start** vagy **leállítása**  és a *WhatIf* paramétert **igaz**.<br><br> ![A runbook paramétereinek a konfigurálása](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Ez lehetővé teszi, hogy tekintse meg a műveletet, akkor kerül sor, és bármely módosításához szükség szerint üzemi virtuális gépek elleni végrehajtása előtt.  Miután kényelmes, manuálisan hajthatja végre a runbook a paraméter értéke **hamis** vagy lehetővé teszik az automatizálás ütemezést **Sequenced-StartVM** és **Sequenced-StopVM**futtassa a következő automatikusan a a előírt ütemezést.  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>3. eset: Automatikus leállítási/indítás virtuális gépek a CPU-használat alapján előfizetésből
Kezeléséhez a futó virtuális gépek az előfizetéshez költség, ez a megoldás segítségével történő kiértékelésével x %-nál kisebb Azure virtuális gépeken, amelyet nem használ, csúcsidőszakon kívüli időszakokban például óra elteltével automatikusan le azokat, ha a processzor kihasználtságát.  

Alapértelmezés szerint a megoldás a százalékos CPU metrika kiértékeléséhez előre konfigurált pedig ha átlagos kihasználtság 5 % vagy kevesebb.  Ez a következő változók vezérli, és módosítható, ha az alapértelmezett értékek nem felelnek meg a szükséges:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Csak akkor tudja engedélyezni vagy célzó előfizetést és erőforráscsoportot, vagy virtuális gépek, de nem mindkettőt adott listája a műveletet.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>A stop műveletet előfizetésbe és erőforráscsoportba csoport cél

1. Konfigurálja a **External_Stop_ResourceGroupNames** és **External_ExcludeVMNames** változókat, adja meg a cél virtuális gépek.  
2. Engedélyezi, és frissítse a **Schedule_AutoStop_CreateAlert_Parent** ütemezést.
3. Futtassa a **AutoStop_CreateAlert_Parent** rendelkező runbook a *művelet* paraméter **start** és a *WhatIf* paraméterértéke **Igaz** a módosítások megtekintéséhez.

#### <a name="target-stop-action-by-vm-list"></a>Cél leállítási művelet által a virtuális gép listája

1. Futtassa a **AutoStop_CreateAlert_Parent** rendelkező runbook a *művelet* paraméter **start**, adja hozzá a virtuális gépek által egy vesszővel elválasztott listáját a *VMList* paramétert, és a *WhatIf* paraméter **igaz** a módosítások megtekintéséhez.  
2. Konfigurálja a **External_ExcludeVMNames** paraméter vesszővel tagolt (VM1, vm2 virtuális gépnek, VM3) virtuális gépek listája.
3. Ez a forgatókönyv nem veszi figyelembe a **External_Start_ResourceGroupNames** és **External_Stop_ResourceGroupnames** varabies.  A jelen esetben szüksége lesz saját automatizálás ütemezést létrehozni, azt. További információkért lásd: [az Azure Automationben runbook ütemezése](../automation/automation-schedules.md).

Most, hogy a virtuális gépek leállítása CPU-használat alapján lehet ütemezni a egyikét engedélyezni kell az alábbi ütemezésekkel elindítani ezeket.

* Cél indítsa el a művelet által előfizetésben és erőforráscsoportban.  Olvassa el a [#1. forgatókönyv](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) a teszteléshez és engedélyezése **ütemezett-StartVM** ütemezést.
* Cél-indítási műveletet előfizetés, erőforráscsoport, és a címke.  Olvassa el a [#2. forgatókönyv](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) ütemezni a teszteléshez és a "Sequenced-StartVM" engedélyezése.


### <a name="configuring-e-mail-notifications"></a>E-mail-értesítések konfigurálása

E-mail értesítések beállítása után a megoldást már telepítették, a következő három változók módosíthatja:

* External_EmailFromAddress - adja meg a feladó e-mail címe
* External_EmailToAddress - vesszővel elválasztott e-mail címekből álló listát (user@hotmail.com, user@outlook.com) értesítési e-mailek fogadásához
* External_IsSendEmail - Ha beállítása **Igen**, először e-mailt kap, tiltsa le az értesítő e-mailek, adja meg értéket **nem**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Az indítási és leállítási ütemezésének módosítása

Ebben a megoldásban indítási és leállítási ütemezések kezelése követi ugyanazokat a lépéseket, a [az Azure Automationben runbook ütemezése](automation-schedules.md).     

## <a name="log-analytics-records"></a>Log Analytics-rekordok

Az Automation két rekordtípust hoz létre az OMS-adattárban.

### <a name="job-logs"></a>Feladatnaplók

Tulajdonság | Leírás|
----------|----------|
Hívó |  A művelet kezdeményezője.  Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
Kategória | Az adattípus besorolása.  Az Automation esetében az érték JobLogs.|
CorrelationId | GUID, a runbook-feladat korrelációs azonosítója.|
JobId | GUID, a runbook-feladat azonosítója.|
operationName | Meghatározza az Azure-ban végrehajtott művelet típusát.  Az Automation esetében az érték Job.|
resourceId | Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat.  Az Automation esetében az érték Azure Automation.|
ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
resultType | A runbook-feladat állapota.  Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres|
resultDescription | Ismerteti a runbook-feladat eredményállapotát.  Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött|
RunbookName | Megadja a runbook-feladat nevét.|
SourceSystem | Megadja az elküldött adatok forrásrendszerét.  Az Automation esetében az érték OpsManager|
StreamType | Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés|
SubscriptionId | Megadja a feladat előfizetési azonosítóját.
Time | A runbook-feladat végrehajtásának dátuma és időpontja.|


### <a name="job-streams"></a>Feladatstreamek

Tulajdonság | Leírás|
----------|----------|
Hívó |  A művelet kezdeményezője.  Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer.|
Kategória | Az adattípus besorolása.  Az Automation esetében az érték JobStreams.|
JobId | GUID, a runbook-feladat azonosítója.|
operationName | Meghatározza az Azure-ban végrehajtott művelet típusát.  Az Automation esetében az érték Job.|
ResourceGroup | Meghatározza a runbook-feladat erőforráscsoportjának nevét.|
resourceId | Meghatározza az Azure-ban szereplő erőforrás azonosítóját.  Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
ResourceProvider | Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat.  Az Automation esetében az érték Azure Automation.|
ResourceType | Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók.|
resultType | A runbook-feladat eredménye az esemény létrehozásának időpontjában.  Lehetséges értékek:<br>- Folyamatban|
resultDescription | A runbook kimeneti streamjét tartalmazza.|
RunbookName | A runbook neve.|
SourceSystem | Megadja az elküldött adatok forrásrendszerét.  Az Automation esetében az érték OpsManager|
StreamType | A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes|
Time | A runbook-feladat végrehajtásának dátuma és időpontja.|

A **JobLogs** vagy **JobStreams** kategória rekordjait visszaadó bármely naplókeresés végrehajtásakor kiválaszthatja a **JobLogs** vagy **JobStreams** nézetet, amely megjeleníti a keresés által visszaadott frissítéseket összefoglaló csempék készletét.

## <a name="sample-log-searches"></a>Naplókeresési minták

A következő táblázat a megoldás által összegyűjtött feladatrekordokkal kapcsolatos naplókeresési mintákat tartalmazza. 

Lekérdezés | Leírás|
----------|----------|
Runbook ScheduledStartStop_Parent sikeresen végrehajtott feladatok keresése | Keresés kategória == "JobLogs" &#124; Ha (RunbookName_s == "ScheduledStartStop_Parent") &#124; Ha (ResultType == "Kész") &#124; AggregatedValue összefoglalója = count() ResultType, a bin (TimeGenerated, 1 óra) &#124; Rendezze a TimeGenerated desc|
Runbook SequencedStartStop_Parent sikeresen végrehajtott feladatok keresése | Keresés kategória == "JobLogs" &#124; Ha (RunbookName_s == "SequencedStartStop_Parent") &#124; Ha (ResultType == "Kész") &#124; AggregatedValue összefoglalója = count() ResultType, a bin (TimeGenerated, 1 óra) &#124; Rendezze a TimeGenerated desc

## <a name="removing-the-solution"></a>A megoldás eltávolítása

Ha úgy dönt, hogy már nem kell használnia a megoldás minden további, az Automation-fiók törlése.  A megoldás csak törlésével a runbookokat, a művelet nem törli a ütemezések vagy változók, amelyek létrehozásakor a megoldás hozzá lett adva.  Az eszközöket, szüksége lesz, ha éppen nem használja másik runbookok kézi törléséhez.  

Törölje a megoldást, hajtsa végre az alábbi lépéseket:

1.  Válassza ki az Automation-fiók **munkaterület** a bal oldali ablaktáblán.  
2.  Az a **megoldások** lapon, válassza ki a megoldás **Start-Stop-VM [munkaterület]**.  Az a **VMManagementSolution [munkaterület]** oldal, a menüben kattintson **törlése**.<br><br> ![Virtuálisgép-felügyeleti megoldás törlése](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Az a **megoldás törlése** ablakban ellenőrizze, hogy törli a megoldás.
4.  Az adatokat a rendszer ellenőrzi, és a megoldás törlése, nyomon követheti a folyamat állapotát **értesítések** a menüből.  A rendszer visszairányítja a **megoldások** el akarja távolítani a megoldás elindulása után lapon.  

Az Automation-fiók és a Naplóelemzési munkaterület nem törlődnek a folyamat részeként.  Ha nem szeretné megőrizni a Naplóelemzési munkaterület, akkor törölje manuálisan.  Ehhez is az Azure portálról.   Kezdőképernyőn az Azure portálon válassza **Naplóelemzési** majd a a **Naplóelemzési** panelen válassza ki a munkaterületet, és kattintson **törlése** menüjéből a Munkaterület-beállítások panelen.  
      
## <a name="next-steps"></a>Következő lépések

- A különböző keresési lekérdezések összeállításával és az Automation-feladatnaplók Log Analytics-szel történő megtekintésével kapcsolatos további tudnivalókat a [Naplókeresések a Log Analytics-ben](../log-analytics/log-analytics-log-searches.md) című rész tartalmazza
- A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza
- Log Analytics és a gyűjtemény adatforrások kapcsolatos további információkért lásd: [gyűjtése Azure storage adatok a Naplóelemzési – áttekintés](../log-analytics/log-analytics-azure-storage.md)






   

