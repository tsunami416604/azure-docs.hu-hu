---
title: "Virtuális gépek indítása/leállítása munkaidőn kívül [előzetes verzió] megoldás | Microsoft Docs"
description: "A Virtuális gépek felügyelete megoldás egy ütemezés alapján elindítja és leállítja az Azure Resource Manager virtuális gépeit, és proaktívan figyeli azokat a Log Analytics szolgáltatásból."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/07/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b0fec06e4a167e615381fca17def46923d9f0f1b


---
# <a name="startstop-vms-during-offhours-preview-solution-in-automation"></a>Virtuális gépek indítása/leállítása munkaidőn kívül [előzetes verzió] megoldás az Automation szolgáltatásban
A virtuális gépek indítása/leállítása munkaidőn kívül [előzetes verzió] megoldás elindítja és leállítja a felhasználó Azure Resource Manager virtuális gépeit a felhasználó által meghatározott ütemezés szerint, valamint betekintést nyújt a felhasználó virtuális gépeit elindító és leállító Automation-feladatokba az OMS Log Analytics segítségével.  

## <a name="prerequisites"></a>Előfeltételek
* A runbookok (forgatókönyvek) [Azure-futtatófiókkal](automation-sec-configure-azure-runas-account.md) használhatóak.  A futtatófiók az előnyben részesített hitelesítési módszer, mivel az tanúsítványalapú hitelesítést használ a jelszó helyett, ami lejárhat vagy gyakran változhat.  
* Ez a megoldás csak olyan virtuális gépek felügyeletére alkalmas, amelyek ugyanabban az előfizetésben és erőforráscsoportban vannak, mint amelyikben az Automation-fiók található.  
* Ez a megoldás csak a következő Azure-régiókban helyezhető üzembe: Délkelet-Ausztrália, az USA keleti régiója, Délkelet-Ázsia és Nyugat-Európa.  A virtuális gépek ütemezését kezelő runbookok bármely régió virtuális gépeit megcélozhatják.  
* Ha e-mail-értesítéseket szeretne kapni a virtuális gépeket indító és leállító runbookok befejezéséről, az Office 365 üzleti besorolású előfizetésére van szükség.  

## <a name="solution-components"></a>Megoldás-összetevők
Ez a megoldás a következő erőforrásokból áll, amelyek az Automation-fiókjába lesznek importálva és felvéve.

### <a name="runbooks"></a>Runbookok
| Forgatókönyv | Leírás |
| --- | --- |
| CleanSolution-MS-Mgmt-VM |Ez a runbook eltávolítja az összes ott található erőforrást és ütemezést, amikor törli a megoldást az előfizetésből. |
| SendMailO365-MS-Mgmt |Ez a runbook e-mailt küld az Office 365 Exchange-en keresztül. |
| StartByResourceGroup-MS-Mgmt-VM |Ennek a runbooknak azokat a virtuális gépeket kell elindítania (klasszikus és ARM-alapú virtuális gépeket egyaránt), amelyek az Azure-erőforráscsoport(ok) adott listáján szerepelnek. |
| StopByResourceGroup-MS-Mgmt-VM |Ennek a runbooknak azokat a virtuális gépeket kell leállítania (klasszikus és ARM-alapú virtuális gépeket egyaránt), amelyek az Azure-erőforráscsoport(ok) adott listáján szerepelnek. |

<br>

### <a name="variables"></a>Változók
| Változó | Leírás |
| --- | --- |
| **SendMailO365-MS-Mgmt** runbook | |
| SendMailO365-IsSendEmail-MS-Mgmt |Meghatározza, hogy a StartByResourceGroup-MS-Mgmt-VM és a StopByResourceGroup-MS-Mgmt-VM runbook küldjön-e e-mail-értesítést a művelet befejezésekor.  Az e-mail-riasztások engedélyezéséhez válassza a **True** (Igaz) lehetőséget, vagy a letiltásához a **False** (Hamis) lehetőséget. Az alapértelmezett érték a **False** (Hamis). |
| **StartByResourceGroup-MS-Mgmt-VM** runbook | |
| StartByResourceGroup-ExcludeList-MS-Mgmt-VM |Írja be a felügyeleti műveletből kizárandó virtuális gépek nevét; a neveket pontosvesszővel (;) válassza el egymástól. Az értékeknél különbséget kell tenni a kis-és a nagybetűk között, és a helyettesítő karakter (csillag) használata támogatott. |
| StartByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt |Az e-mail-üzenet törzsének elejéhez fűzhető szöveg. |
| StartByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt |Megadja annak az Automation-fióknak a nevét, amely az e-mail-runbookot tartalmazza.  **Ne módosítsa ezt a változót.** |
| StartByResourceGroup-SendMailO365-EmailRunbookName-MS-Mgmt |Megadja az e-mail-runbook nevét.  A StartByResourceGroup-MS-Mgmt-VM és a StopByResourceGroup-MS-Mgmt-VM runbook ezt használja e-mailek küldéséhez.  **Ne módosítsa ezt a változót.** |
| StartByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt |Megadja annak az erőforráscsoportnak a nevét, amely az e-mail-runbookot tartalmazza.  **Ne módosítsa ezt a változót.** |
| StartByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt |Megadja az e-mail tárgysorának szövegét. |
| StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt |Megadja az e-mail címzettjét/címzettjeit.  Adja meg a neveket pontosvesszővel (;) elválasztva egymástól. |
| StartByResourceGroup-TargetResourceGroups-MS-Mgmt-VM |Írja be a felügyeleti műveletből kizárandó virtuális gépek nevét; a neveket pontosvesszővel (;) válassza el egymástól. Az értékeknél különbséget kell tenni a kis-és a nagybetűk között, és a helyettesítő karakter (csillag) használata támogatott.  Az alapértelmezett érték (csillag) tartalmazni fogja az előfizetésben szereplő összes erőforráscsoportot. |
| StartByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM |Megadja azt az előfizetést, amely a megoldás által kezelendő virtuális gépeket tartalmazza.  Ugyanannak az előfizetésnek kell lennie, mint ami a megoldáshoz tartozó Automation-fiókot tartalmazza. |
| **StopByResourceGroup-MS-Mgmt-VM** runbook | |
| StopByResourceGroup-ExcludeList-MS-Mgmt-VM |Írja be a felügyeleti műveletből kizárandó virtuális gépek nevét; a neveket pontosvesszővel (;) válassza el egymástól. Az értékeknél különbséget kell tenni a kis-és a nagybetűk között, és a helyettesítő karakter (csillag) használata támogatott. |
| StopByResourceGroup-SendMailO365-EmailBodyPreFix-MS-Mgmt |Az e-mail-üzenet törzsének elejéhez fűzhető szöveg. |
| StopByResourceGroup-SendMailO365-EmailRunBookAccount-MS-Mgmt |Megadja annak az Automation-fióknak a nevét, amely az e-mail-runbookot tartalmazza.  **Ne módosítsa ezt a változót.** |
| StopByResourceGroup-SendMailO365-EmailRunbookResourceGroup-MS-Mgmt |Megadja annak az erőforráscsoportnak a nevét, amely az e-mail-runbookot tartalmazza.  **Ne módosítsa ezt a változót.** |
| StopByResourceGroup-SendMailO365-EmailSubject-MS-Mgmt |Megadja az e-mail tárgysorának szövegét. |
| StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt |Megadja az e-mail címzettjét/címzettjeit.  Adja meg a neveket pontosvesszővel (;) elválasztva egymástól. |
| StopByResourceGroup-TargetResourceGroups-MS-Mgmt-VM |Írja be a felügyeleti műveletből kizárandó virtuális gépek nevét; a neveket pontosvesszővel (;) válassza el egymástól. Az értékeknél különbséget kell tenni a kis-és a nagybetűk között, és a helyettesítő karakter (csillag) használata támogatott.  Az alapértelmezett érték (csillag) tartalmazni fogja az előfizetésben szereplő összes erőforráscsoportot. |
| StopByResourceGroup-TargetSubscriptionID-MS-Mgmt-VM |Megadja azt az előfizetést, amely a megoldás által kezelendő virtuális gépeket tartalmazza.  Ugyanannak az előfizetésnek kell lennie, mint ami a megoldáshoz tartozó Automation-fiókot tartalmazza. |

<br>

### <a name="schedules"></a>Ütemezések
| Ütemezés | Leírás |
| --- | --- |
| StartByResourceGroup-Schedule-MS-Mgmt |A StartByResourceGroup runbook ütemezése, amely a jelen megoldás által felügyelt virtuális gépek elindítását hajtja végre. |
| StopByResourceGroup-Schedule-MS-Mgmt |A StopByResourceGroup runbook ütemezése, amely a jelen megoldás által felügyelt virtuális gépek leállítását hajtja végre. |

### <a name="credentials"></a>Hitelesítő adatok
| Hitelesítő adat | Leírás |
| --- | --- |
| O365Credential |Megad egy érvényes Office 365 felhasználói fiókot az e-mail-üzenetek küldéséhez.  Csak akkor szükséges, ha a SendMailO365-IsSendEmail-MS-Mgmt változó **True** (Igaz) értékre van beállítva. |

## <a name="configuration"></a>Konfiguráció
Hajtsa végre az alábbi lépéseket, hogy a Virtuális gépek indítása/leállítása munkaidőn kívül [előzetes verzió] megoldást hozzáadhassa az Automation-fiókjához, majd a megoldás testreszabásához konfigurálja a változókat.

1. Az Azure Portal kezdőképernyőjén válassza ki a **Piactér** csempét.  Ha a csempe már nincs a kezdőképernyőhöz rögzítve, a bal oldali navigációs ablaktáblában jelölje ki az **Új** elemet.  
2. A Piactér panelen írja be a keresőmezőbe a **VM indítása** kifejezést, majd a keresési eredmények közül válassza ki a **Virtuális gépek indítása/leállítása munkaidőn kívüli [előzetes verzió]** megoldást.  
3. A kiválasztott megoldáshoz tartozó **Virtuális gépek indítása/leállítása munkaidőn kívüli [előzetes verzió]** panelen tekintse át az összefoglaló adatokat, majd kattintson a **Létrehozás** gombra.  
4. A **Megoldás hozzáadása** panel ott jelenik meg, ahol a felhasználót a rendszer a megoldás konfigurálására kéri annak az Automation-előfizetésbe történő importálása előtt.<br><br> ![Virtuálisgép-felügyelet – Megoldás hozzáadása panel](media/automation-solution-vm-management/vm-management-solution-add-solution-blade.png)<br><br>
5. A **Megoldás hozzáadása** panelen válassza ki a **Munkaterület** elemet, majd itt válassza ki az Automation-fiókot tartalmazó Azure-előfizetéshez kapcsolódó OMS-munkaterületet, vagy hozzon létre egy újat.  Ha nem rendelkezik OMS-munkaterülettel, válassza ki az **Új munkaterület létrehozása** lehetőséget és az **OMS-munkaterület** panelen végezze el az alábbiakat: 
   
   * Adja meg az új **OMS-munkaterület** nevét.
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** területen létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt.  
   * Válasszon ki egy **helyet**.  Jelenleg csak a következő helyek közül választhat: **Délkelet-Ausztrália**, **az USA keleti régiója**, **Délkelet-Ázsia** és **Nyugat-Európa**.
   * Válasszon egy tarifacsomagot a **Tarifacsomag** területen.  A megoldás két csomagban választható: ingyenes és OMS fizetett csomag.  Az ingyenes csomagnál korlátozva van a naponta összegyűjtött adatok mennyisége, a megőrzési időtartam és a runbook-feladatok futásideje (perc).  Az OMS fizetett csomagnál a naponta összegyűjtött adatok mennyisége nincs korlátozva.  
     
     > [!NOTE]
     > Amíg az Önálló fizetett csomag megjelenik lehetőségként, ez a megoldás nem alkalmazható.  Ha kiválasztja, és ezt a megoldást akarja létrehozni az előfizetésben, akkor az sikertelen lesz.  Csak akkor lesz választható, ha ezt a megoldást hivatalosan is kiadják.<br>Ha ezt a megoldást használja, az csak az automatizálási feladat idejét (percben), illetve a naplófeldolgozást fogja használni.  A megoldás nem egészíti ki további OMS-csomópontokkal a környezetét.  
     > 
     > 
6. Miután az **OMS-munkaterület** panelen megadta a szükséges adatokat, kattintson a **Létrehozás** gombra.  Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet.  Ezután visszatér a **Megoldás hozzáadása** panelre.  
7. A **Megoldás hozzáadása** panelen válassza ki az **Automation-fiók** elemet.  Új OMS-munkaterület létrehozásakor egy hozzá társított új Automation-fiókot is létre kell hoznia, valamint meg kell adnia az Azure-előfizetését, az erőforráscsoportot és a régiót.  Kiválaszthatja az **Automation-fiók létrehozása** lehetőséget, és az **Automation-fiók hozzáadása** panelen megadhatja a következő adatokat: 
   
   * A **Név** mezőbe írja be az Automation-fiók nevét.
     
     A rendszer a kiválasztott OMS-munkaterület alapján automatikusan kitölti az összes többi beállítást, amelyek utána nem módosíthatók.  A megoldásban szereplő runbookok alapértelmezett hitelesítési módszere egy Azure-futtatófiók.  Miután rákattintott az **OK** gombra, a rendszer érvényesíti a konfigurációs beállításokat, és létrehozza az Automation-fiókot.  Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását. 
     
     Ellenkező esetben kiválaszthat egy meglévő Automation-futtatófiókot.  Vegye figyelembe, hogy nem választhat ki már egy másik OMS-munkaterülethez kapcsolódó fiókot, ellenkező esetben a panelen megjelenik egy tájékoztató üzenet.  Ha a kiválasztott fiók már kapcsolódik, egy másik Automation-futtatófiókot kell választania, vagy létre kell hoznia egy újat.<br><br> ![Az Automation-fiók már kapcsolódik egy OMS-munkaterülethez](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>
8. Végül a **Megoldás hozzáadása** panelen válassza ki a **Konfiguráció** elemet, és ekkor megjelenik a **Paraméterek** panel.  A **Paraméterek** panelen a rendszer az alábbi műveletek elvégzésére kéri:  
   
   * Adja meg a **Cél erőforráscsoport nevét**, ami annak az erőforráscsoportnak a neve, amely a megoldás által felügyelendő virtuális gépeket tartalmazza.  Több nevet is megadhat, pontosvesszővel elválasztva őket egymástól (az értékek megkülönböztetik a kis- és a nagybetűket).  Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni.
   * Válasszon ki egy olyan **ütemezést**, amely a cél erőforráscsoport(ok)ban lévő virtuális gépek ismétlődő elindítási és leállítási dátuma és időpontja.  
9. Miután befejezte a megoldás kezdeti beállításainak konfigurálását, válassza ki a **Létrehozás** lehetőséget.  A rendszer érvényesíti az összes beállítást, majd megpróbálja üzembe helyezni a megoldást az előfizetésében.  A folyamat eltarthat néhány másodpercig, az előrehaladását nyomon követheti az **Értesítések** menüpont alatt. 

## <a name="collection-frequency"></a>A gyűjtés gyakorisága
Az Automation-feladatnapló és -feladatstream adatai 5 percenként betöltődnek az OMS-adattárba.  

## <a name="using-the-solution"></a>A megoldás használata
A virtuális gépek felügyelete megoldás hozzáadásakor az OMS-munkaterületen a **StartStopVM nézet** csempe felkerül az OMS-irányítópultra.  Ez a csempe azon runbook-feladatok számát és grafikus ábrázolását jeleníti meg a megoldáshoz, amelyek elindultak és sikeresen befejeződtek.<br><br> ![Virtuális gépek felügyelete – StartStopVM nézet csempe](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Az Automation-fiókjában úgy érheti el és kezelheti a megoldást, ha kiválasztja a **Megoldások** csempét, majd a **Megoldások** panelen található listáról kiválasztja **Virtuális gépek indítása és leállítása [munkaterület]** elemet.<br><br> ![Automation-megoldások listája](media/automation-solution-vm-management/vm-management-solution-autoaccount-solution-list.png)  

A megoldás kiválasztásakor megjelenik a **Virtuális gépek indítása és leállítása [munkaterület]** megoldáspanel, ahol áttekintheti a fontos részleteket, például a **StartStopVM** csempét, ami az OMS-munkaterülethez hasonlóan a megoldás azon runbook-feladatoknak a számát és grafikus ábrázolását jeleníti meg, amelyek elindultak és sikeresen befejeződtek.<br><br> ![Automation VM-megoldás panel](media/automation-solution-vm-management/vm-management-solution-solution-blade.png)  

Innen megnyithatja OMS-munkaterületét is, és további elemzéseket végezhet a feladatrekordokon.  Csak kattintson az **Összes beállítás** lehetőségre, majd a **Beállítások** panelen válassza ki a **Gyors üzembe helyezés** elemet, végül pedig a **Gyors üzembe helyezés** panelen válassza ki az **OMS-portál** lehetőséget.   Ezzel megnyílik egy új lap vagy egy új böngésző-munkamenet, és megjelenik az Automation-fiókjához és -előfizetéséhez tartozó OMS-munkaterülete.  

### <a name="configuring-email-notifications"></a>E-mail-értesítések konfigurálása
E-mail-értesítések engedélyezéséhez a virtuális gépek indítását és leállítását kezelő runbookok befejeződése után módosítania kell az **O365Credential** hitelesítő adatot és legalább az alábbi változókat:

* SendMailO365-IsSendEmail-MS-Mgmt
* StartByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt
* StopByResourceGroup-SendMailO365-EmailToAddress-MS-Mgmt

Az **O365Credential** hitelesítőadat konfigurálásához hajtsa végre a következő lépéseket:

1. Az Automation-fiókjában kattintson az ablak tetején található **Összes beállítás** gombra. 
2. Az **Automation-erőforrások** szakasz alatt található **Beállítások** panelen válassza ki az **Adategységek** elemet. 
3. Az **Adategységek** panelen válassza ki a **Hitelesítő adat** csempét, majd a **Hitelesítő adat** panelen válassza ki az **O365Credential** elemet.  
4. Adjon meg egy érvényes Office 365-felhasználónevet és -jelszót, majd kattintson a **Mentés** gombra a módosítások mentéséhez.  

A korábban kiemelt változók konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az Automation-fiókjában kattintson az ablak tetején található **Összes beállítás** gombra. 
2. Az **Automation-erőforrások** szakasz alatt található **Beállítások** panelen válassza ki az **Adategységek** elemet. 
3. Az **Adategységek** panelen válassza ki a **Változók** csempét, és a **Változók** panelen válassza ki a fent felsorolt változót, majd módosítsa az értékét a korábbi [Változó](##variables) szakaszban megadott leírásnak megfelelően.  
4. Kattintson a **Mentés** gombra a változó módosított értékeinek mentéséhez.   

### <a name="modifying-the-startup-and-shutdown-schedule"></a>Az indítási és leállítási ütemezés módosítása
Ebben a megoldásban az indítási és leállítási ütemezés felügyelete a [Runbook ütemezése az Azure Automationben](automation-scheduling-a-runbook.md) szakaszban ismertetett lépéseket követi.  Ne feledje, hogy az ütemezési konfiguráció nem módosítható.  Le kell tiltania a meglévő ütemezést, és létre kell hoznia egy újat, majd csatolnia kell azt ahhoz a **StartByResourceGroup-MS-Mgmt-VM** vagy **StopByResourceGroup-MS-Mgmt-VM** runbookhoz, amelyiknél alkalmazni szeretné.   

## <a name="log-analytics-records"></a>Log Analytics-rekordok
Az Automation két rekordtípust hoz létre az OMS-adattárban.

### <a name="job-logs"></a>Feladatnaplók
| Tulajdonság | Leírás |
| --- | --- |
| Hívó |A művelet kezdeményezője.  Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Kategória |Az adattípus besorolása.  Az Automation esetében az érték JobLogs. |
| CorrelationId |GUID, a runbook-feladat korrelációs azonosítója. |
| JobId |GUID, a runbook-feladat azonosítója. |
| operationName |Meghatározza az Azure-ban végrehajtott művelet típusát.  Az Automation esetében az érték Job. |
| resourceId |Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók. |
| ResourceGroup |Meghatározza a runbook-feladat erőforráscsoportjának nevét. |
| ResourceProvider |Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat.  Az Automation esetében az érték Azure Automation. |
| ResourceType |Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók. |
| resultType |A runbook-feladat állapota.  Lehetséges értékek:<br>- Elindítva<br>- Leállítva<br>- Felfüggesztve<br>- Sikertelen<br>- Sikeres |
| resultDescription |Ismerteti a runbook-feladat eredményállapotát.  Lehetséges értékek:<br>- A feladat elindult<br>- A feladat nem sikerült<br>- A feladat befejeződött |
| RunbookName |Megadja a runbook-feladat nevét. |
| SourceSystem |Megadja az elküldött adatok forrásrendszerét.  Az Automation esetében az érték OpsManager |
| StreamType |Megadja az esemény típusát. Lehetséges értékek:<br>- Részletes<br>- Kimenet<br>- Hiba<br>- Figyelmeztetés |
| SubscriptionId |Megadja a feladat előfizetési azonosítóját. |
| Time |A runbook-feladat végrehajtásának dátuma és időpontja. |

### <a name="job-streams"></a>Feladatstreamek
| Tulajdonság | Leírás |
| --- | --- |
| Hívó |A művelet kezdeményezője.  Lehetséges értékek: egy e-mail-cím vagy egy ütemezett feladatokat tartalmazó rendszer. |
| Kategória |Az adattípus besorolása.  Az Automation esetében az érték JobStreams. |
| JobId |GUID, a runbook-feladat azonosítója. |
| operationName |Meghatározza az Azure-ban végrehajtott művelet típusát.  Az Automation esetében az érték Job. |
| ResourceGroup |Meghatározza a runbook-feladat erőforráscsoportjának nevét. |
| resourceId |Meghatározza az Azure-ban szereplő erőforrás azonosítóját.  Az Automation esetében az érték a runbookhoz társított Automation-fiók. |
| ResourceProvider |Meghatározza, hogy melyik Azure-szolgáltatás biztosítja az üzembe helyezhető és kezelhető erőforrásokat.  Az Automation esetében az érték Azure Automation. |
| ResourceType |Meghatározza az Azure-ban szereplő erőforrás típusát.  Az Automation esetében az érték a runbookhoz társított Automation-fiók. |
| resultType |A runbook-feladat eredménye az esemény létrehozásának időpontjában.  Lehetséges értékek:<br>- Folyamatban |
| resultDescription |A runbook kimeneti streamjét tartalmazza. |
| RunbookName |A runbook neve. |
| SourceSystem |Megadja az elküldött adatok forrásrendszerét.  Az Automation esetében az érték OpsManager |
| StreamType |A feladatstream típusa. Lehetséges értékek:<br>- Folyamatban<br>- Kimenet<br>- Figyelmeztetés<br>- Hiba<br>- Hibakeresés<br>- Részletes |
| Time |A runbook-feladat végrehajtásának dátuma és időpontja. |

A **JobLogs** vagy **JobStreams** kategória rekordjait visszaadó bármely naplókeresés végrehajtásakor kiválaszthatja a **JobLogs** vagy **JobStreams** nézetet, amely megjeleníti a keresés által visszaadott frissítéseket összefoglaló csempék készletét.

## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött feladatrekordokkal kapcsolatos naplókeresési mintákat tartalmazza. 

| Lekérdezés | Leírás |
| --- | --- |
| A StartVM runbook sikeresen befejeződött feladatainak megkeresése |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Succeeded &#124; count() mérése JobId_g szerint |
| A StopVM runbook sikeresen befejeződött feladatainak megkeresése |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" ResultType=Failed &#124; count() mérése JobId_g szerint |
| A StartVM és a StopVM runbook feladatainak állapotmegjelenítése az idő múlásával |Category=JobLogs RunbookName_s="StartByResourceGroup-MS-Mgmt-VM" OR "StopByResourceGroup-MS-Mgmt-VM" NOT(ResultType="started") |

## <a name="next-steps"></a>Következő lépések
* A különböző keresési lekérdezések összeállításával és az Automation-feladatnaplók Log Analytics-szel történő megtekintésével kapcsolatos további tudnivalókat a [Naplókeresések a Log Analytics-ben](../log-analytics/log-analytics-log-searches.md) című rész tartalmazza
* A runbook végrehajtásával, a runbook-feladatok figyelésével, illetve az egyéb technikai részletekkel kapcsolatos további tudnivalókat a [Runbook-feladatok nyomon követése](automation-runbook-execution.md) című rész tartalmazza
* Az OMS Log Analytics használatával és adatgyűjtési forrásokkal kapcsolatos további tudnivalókat lásd: az [Azure-tárfiókbeli adatok Log Analytics-ben történő gyűjtésének az áttekintése](../log-analytics/log-analytics-azure-storage.md)




<!--HONumber=Nov16_HO2-->


