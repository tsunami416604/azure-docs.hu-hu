---
title: "Azure Automation grafikus szerzői |} Microsoft Docs"
description: "Grafikus szerzői lehetővé teszi a runbookok létrehozását az Azure Automation kód használata nélkül. Ez a cikk ismerteti a grafikus szerzői bemutatása és grafikus forgatókönyvnek létrehozásához szükséges összes adatot."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 4b6f840c-e941-4293-a728-b33407317943
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 59f1f8c544c7ab3dce9373d65e0f6cbaa62c8f67
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafikus készítése az Azure Automationben
## <a name="introduction"></a>Bevezetés
Grafikus szerzői műveletek lehetővé teszi a runbookok létrehozása az Azure Automation nélkül az alapul szolgáló Windows PowerShell vagy a PowerShell munkafolyamat kód összetettségét. Tevékenységek hozzáadása a vászon parancsmagok és a runbookok a könyvtárból, kapcsolja össze, és konfigurálja a munkafolyamat létrehozásához.  Ha valaha is dolgozott System Center Orchestrator és Service Management Automation (SMA), majd ez Bizonyára ismerős lesz Önnek.   

Ez a cikk ismerteti a grafikus szerzői bemutatása és az elveket, grafikus runbook létrehozása a kezdéshez.

## <a name="graphical-runbooks"></a>Grafikus forgatókönyvek
Minden, az Azure Automation runbookjai Windows PowerShell-munkafolyamatok.  Grafikus és grafikus PowerShell-munkafolyamati forgatókönyvek az Automation dolgozók futtatott PowerShell-kódot létrehozni, de nem tudunk tekinthető meg és nem módosítható közvetlenül.  Egy grafikus forgatókönyvnek lemezekké lehet alakítani egy grafikus PowerShell-munkafolyamati forgatókönyv és fordítva, de nem alakítható át szöveges forgatókönyvként. Egy meglévő szöveges forgatókönyvként nem lehet importálni a grafikus szerkesztő.  

## <a name="overview-of-graphical-editor"></a>Grafikus szerkesztő áttekintése
Megnyithatja a grafikus szerkesztőt az Azure portálon létrehozásával vagy szerkesztésével grafikus forgatókönyv.

![Grafikus munkaterület](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

A következő szakaszok ismertetik a vezérlők grafikus-szerkesztőben.

### <a name="canvas"></a>Vászonra
A vászon, ahol a runbook tervezésekor.  A könyvtár vezérlőben csomópontjából tevékenységek hozzáadása a runbookhoz, és csatlakoztassa őket megadhatók a runbook logikája hivatkozásokkal.

A vezérlők a vászon alján segítségével nagyítás növelésére és csökkentésére.

![Grafikus munkaterület](media/automation-graphical-authoring-intro/runbook-canvas-controls.png)

### <a name="library-control"></a>Szalagtár-vezérlő
A könyvtár vezérlő esetén, ahol ki kell választania [tevékenységek](#activities) hozzáadása a runbookhoz.  Akkor adja hozzá a vásznon, ahol csatlakoztatja őket más tevékenységeket.  Ez magában foglalja az alábbi táblázatban ismertetett négy részből áll.

| Section | Leírás |
|:--- |:--- |
| Parancsmagok |A runbook használható összes parancsmagot tartalmazza.  Parancsmagok modul szerint vannak rendszerezve.  Az automation-fiók a telepített modulok mindegyikének lesz elérhető. |
| Runbookok |A forgatókönyvek az automation-fiók tartalmazza. Ezek a runbookok gyermek runbookként használandó vászon lehet hozzáadni. Csak az éppen szerkesztett runbook azonos core típusú forgatókönyvek jelennek meg; a grafikus forgatókönyvek csak PowerShell-alapú forgatókönyvek jelennek meg, amíg a runbookok grafikus PowerShell-munkafolyamati forgatókönyvek csak PowerShell-munkafolyamat-alapú jelennek meg. |
| Objektumok |Magában foglalja a [automation eszközök](http://msdn.microsoft.com/library/dn939988.aspx) az automation-fiókban a runbookban használható.  Amikor egy eszköz ad hozzá egy runbookot, felveszi egy munkafolyamat-tevékenységet, amely lekérdezi a kijelölt objektumhoz.  Változó eszközök esetén azt is megadhatja, hogy adjon hozzá olyan tevékenységet, a változó beolvasására, vagy állítsa be a változót. |
| Runbook-vezérlés |Runbook-vezérlés tevékenységek használható a jelenlegi runbook tartalmazza. A *csatlakozási* több bemeneti adatokat fogad, és megvárja, amíg az összes nem rendelkezik a munkafolyamat folytatása előtt. A *kód* tevékenység fut, a PowerShell vagy a PowerShell-munkafolyamati kód grafikus forgatókönyvnek típusától függően egy vagy több sort.  Ez a tevékenység használhatja egyéni kód vagy egyéb tevékenységeket tartalmazó elérése funkciókat. |

### <a name="configuration-control"></a>Konfiguráció-ellenőrzés
A konfiguráció-ellenőrzés, ahol részletek meg kell adnia a vásznon kiválasztott objektumhoz. A tulajdonságok érhetők el a vezérlő kijelölt objektum típusától függ.  Amikor kiválaszt egy beállítást a konfiguráció-ellenőrzés, akkor további paneleken nyílik meg ahhoz, hogy további információkkal.

### <a name="test-control"></a>Ellenőrző tesztelése
A teszt vezérlő nem jelenik meg a grafikus szerkesztő első indításakor. Mikor nyitja meg interaktív módon [grafikus forgatókönyv-tesztelési](#graphical-runbook-procedures).  

## <a name="graphical-runbook-procedures"></a>Grafikus forgatókönyv eljárások
### <a name="exporting-and-importing-a-graphical-runbook"></a>Egy grafikus forgatókönyv importálása és exportálása
Csak exportálhatja egy grafikus forgatókönyv közzétett változata.  Ha a runbook még nem lett közzétéve, akkor a **közzétett exportálási** gomb le lesz tiltva.  Amikor rákattint az **közzétett exportálási** gombra, a runbookot a rendszer letölti a helyi számítógépen.  A fájl neve megegyezik a runbookban egy *graphrunbook* bővítmény.

![Közzétett exportálása](media/automation-graphical-authoring-intro/runbook-export.png)

Egy grafikus vagy grafikus PowerShell-munkafolyamati forgatókönyv fájl importálásához kiválasztásával a **importálása** lehetőséget egy runbook felvételekor.   Az importálandó fájl kijelölésekor hálózati adaptere esetében megtarthatja az azonos **neve** , vagy adjon meg egy újat.  A Runbook mezőben jelenik a típusú forgatókönyvet, akkor értékeli a kijelölt fájl, és válassza ki a más típusú, helytelen kísérli meg, ha egy üzenetet fog érzékelni megállapítva lehetséges ütközések, és az átalakítás során lehetnek szintaktikai hibák után.  

![Forgatókönyv importálása](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Grafikus runbook tesztelése
Egy runbook vázlatverzióját tesztelheti az Azure-portálon során a forgatókönyv közzétett változata változatlanul, vagy tesztelheti egy új runbookot, ahhoz, hogy közzé lett téve. Ez lehetővé teszi, hogy ellenőrizze, hogy a runbook megfelelően működik, mielőtt lecseréli a közzétett verziót. Egy runbook tesztelésekor a vázlatát hajtja végre, és minden elvégzett műveletet végrehajt befejezését. Nincs feladatelőzményekben létrejött, de a kimenet jelenik meg a Tesztkimenet ablaktáblán. 

Nyissa meg a teszt vezérlő egy runbook nyissa meg szerkesztésre a runbookot, és kattintson a a **teszt ablaktábla** gombra.

![Teszt gomb](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

A teszt vezérlő arra fogja kérni a bemeneti paramétereket, és a runbook indításához kattintson a a **Start** gombra.

![Teszt gomb](media/automation-graphical-authoring-intro/runbook-test-start.png)

### <a name="publishing-a-graphical-runbook"></a>Grafikus runbook közzététele
Az Azure Automationben minden runbook rendelkezik, egy Piszkozat és egy közzétett verziója. Csak a közzétett verziót lehet futtatható legyen, és kizárólag a piszkozat verzió szerkeszthető. A közzétett verziója nincs hatással a piszkozat verzióban végrehajtott módosítások. Amikor a piszkozat verzióban elérhető készen áll, majd beállíthatja azt is, amely felülírja a közzétett verziót a vázlatként megjelölt verziót.

Nyissa meg szerkesztésre, és kattintson a runbook egy grafikus forgatókönyvnek közzéteheti a **közzététel** gombra.

![Közzététel gomb](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

Amikor egy runbook még nem lett közzétéve, állapotba került **új**.  A közzétett, állapotba került **közzétett**.  Azt is közzé lett téve, és a Vázlat és egy közzétett verzió eltérőek után szerkeszteni a runbookot, ha a runbook állapotba került **szerkesztési**.

![A Runbook állapota](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png) 

Lehetősége is van a runbook közzétett változatának visszaállítása.  Ez a jelez számítógépnél minden módosítás el, mert a runbook utolsó közzétett, és a runbook vázlatverzióját lecseréli a közzétett verziót.

![Visszaállítás a közzétett gomb](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)

## <a name="activities"></a>Tevékenységek
Tevékenységek a runbookok építőkövei.  Egy tevékenység lehet egy PowerShell-parancsmag, a gyermek runbook vagy a munkafolyamat-tevékenységet.  Jobb gombbal kattintson rá a szalagtár-vezérlő, majd válassza a runbook a tevékenység hozzáadása. **vászonra Hozzáadás**.  Ezután kattintson és húzza bárhová a vásznon, amely tetszés helyezze el a tevékenységet.  Helye az a vásznon a tevékenység nincs hatással a runbook semmilyen módon a műveletet.  Is elrendezés a runbook azonban találjuk a legmegfelelőbb jelenítheti meg a műveletet. 

![Felvétele a vászonra](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Válassza ki a tevékenységet, a tulajdonságok és a paraméterek konfigurálása a Configuration panelen a vásznon.  Módosíthatja a **címke** leíró, hogy a tevékenység egy.  Továbbra is az eredeti parancsmag futtatása, egyszerűen módosítani a megjelenített név, amely a grafikus szerkesztőben használható.  A címke a runbookon belül egyedinek kell lennie. 

### <a name="parameter-sets"></a>A paraméter beállítása
A paraméterhalmaz határozza meg a kötelező és választható paramétereit, amely elfogadja a parancsmag adott értékek.  Az összes parancsmag be legalább egy paramétere lehet, és több rendelkeznek.  Ha a parancsmag több paraméterkészletei, majd ki kell választania melyiket paraméterek konfigurálása előtt fogja használni.  A paraméterek konfigurálható a választott paraméterkészletet alakítanak függ.  Módosíthatja a paraméterhalmaz kiválasztásával egy tevékenység által használt **paraméter** és egy másik készlet kiválasztása.  Ebben az esetben konfigurált paraméterértékeket elvesznek.

A következő példában a Get-AzureRmVM parancsmag három paraméterkészletei rendelkezik.  Paraméterértékeket nem konfigurálható, amíg a paraméterkészletei valamelyikét választja.  A ListVirtualMachineInResourceGroupParamSet paraméterhalmaz ad vissza az összes virtuális gép egy erőforráscsoportban, és egy nem kötelező paraméter tartozik.  A GetVirtualMachineInResourceGroupParamSet szolgál a virtuális gép visszaállítani, és két kötelező és egy nem kötelező paraméter megadásával.

![A paraméterhalmaz](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>A paraméterértékek
Amikor megad egy paraméter értékét, ki kell választania egy adatforrást határozza meg, hogyan adható meg érték.  Egy adott paraméter elérhető adatforrások függ, hogy a paraméter érvényes értékei.  Null például nem lesz elérhető lehetőségként az egyik paraméter, amely nem engedélyezi a null értékeket.

| Adatforrás | Leírás |
|:--- |:--- |
| Konstans érték |Adja meg a paraméter értékét.  Ez a tulajdonság csak a következő típusok esetében érhető el: Int32, Int64, String, Boolean, DateTime, kapcsoló. |
| Tevékenység kimeneti |Egy tevékenység, amely megelőzi az aktuális tevékenység a munkafolyamat kimenetét.  Az összes érvényes tevékenység megjelenik.  Válassza ki a használandó kimenetét a paraméter értéke csak a tevékenység.  Ha a tevékenység kimenete több tulajdonságait azonosítójú objektum, majd beírhatja a tulajdonság nevében a tevékenység kiválasztása után. |
| A Runbook bemeneti |Jelölje ki a forgatókönyv bemeneti paramétert a tevékenység-paraméter számára. |
| Változóeszköz |Válassza ki a egy automatizálási változó bemeneti adatként. |
| Hitelesítőadat-eszköz |Válassza ki az automatizálási hitelesítő adatok bemeneti adatként. |
| Tanúsítványeszköz |Válassza ki az Automation-tanúsítvány bemeneti adatként. |
| Kapcsolat eszköz |Válassza ki az automatizálási kapcsolat bemeneti adatként. |
| PowerShell-kifejezés |Adja meg az egyszerű [PowerShell-kifejezést](#powershell-expressions).  A kifejezéssel kiértékelendő előtt a tevékenységet, és az eredmény a paraméter értékét használja.  Változók segítségével tekintse meg a kimeneti tevékenységet vagy egy runbook bemeneti paraméter. |
| Nincs konfigurálva |Törli a korábban beállított értéket. |

#### <a name="optional-additional-parameters"></a>További választható paraméterek:
Az összes parancsmag lesz további paramétereket is megadhat.  Ezek a PowerShell általános paramétereket, illetve más egyéni paramétereket.  A szövegmezőben, ahol megadhatja a paramétereket a PowerShell-szintaxis jelenik meg.  Ahhoz például, hogy használja a **részletes** általános paraméter lehet megadni **"-Verbose: $True"**.

### <a name="retry-activity"></a>Ismételje meg a tevékenység
**Újrapróbálási viselkedés** lehetővé teszi, hogy egy tevékenység többször kell futtatnia, amíg egy bizonyos feltétel nem teljesül, hasonlóan a hurok.  A szolgáltatás használható tevékenységek, amelyek többször kell futtatnia, nagyon eséllyel fordulnak elő hiba, és előfordulhat, hogy egynél több kísérlet történt a sikeres, vagy az érvényes adatok tevékenység kimeneti információi tesztelhetők.    

Ha egy tevékenység újra engedélyezi, beállíthatja a késést és egy feltétel.  A késleltetés az az idő (másodperceken vagy perceken kifejezve), hogy a runbook tevékenység ismételt futtatása előtt megvárja-e.  Ha késleltetés van megadva, a tevékenység fog futtassa újból a befejezését követően azonnal. 

![Tevékenység újrapróbálkozási késleltetést](media/automation-graphical-authoring-intro/retry-delay.png)

Az újrapróbálkozási feltétele minden alkalommal, amikor a tevékenység futtatása után kiértékelt PowerShell-kifejezést.  Ha a kifejezés igaz értékre oldható fel, majd a tevékenység, ismét elindul.  Ha a kifejezés FALSE oldja fel a tevékenység nem futtassa újból a, és a következő tevékenységnek helyezi át a runbook. 

![Tevékenység újrapróbálkozási késleltetést](media/automation-graphical-authoring-intro/retry-condition.png)

Az újrapróbálkozási feltétel használhatja a tevékenység-újrapróbálkozások információ hozzáférést biztosító $RetryData nevű változó.  Ez a változó a következő táblázatban tulajdonságokkal rendelkezik.

| Tulajdonság | Leírás |
|:--- |:--- |
| NumberOfAttempts |Száma, amelyek a tevékenység futott. |
| Kimenet |A legutóbbi futtatás a tevékenység kimenetét. |
| Teljesidőtartam |Időtúllépés történt a tevékenység az első alkalommal elindítása óta eltelt. |
| StartedAt |A tevékenység UTC formátumú először lett elindítva. |

Az alábbiakban példák tevékenység ismételje meg a feltételeket.

    # Run the activity exactly 10 times.
    $RetryData.NumberOfAttempts -ge 10 

    # Run the activity repeatedly until it produces any output.
    $RetryData.Output.Count -ge 1 

    # Run the activity repeatedly until 2 minutes has elapsed. 
    $RetryData.TotalDuration.TotalMinutes -ge 2

Miután egy tevékenység újrapróbálkozási feltétel beállítása, a tevékenység két vizuális jelek, jelezve, magában foglalja.  Egy jelennek meg a tevékenység, és a másik akkor, ha a tevékenység konfigurációjának áttekintése.

![Tevékenység újrapróbálkozási Visual mutatók](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Munkafolyamat-parancsprogram-vezérlők
Forráskód-kezelési egy, speciális tevékenységen, amely fogadja a PowerShell vagy a PowerShell-munkafolyamati parancsprogram alatt létrehozott ahhoz, hogy ellátni, amelyek egyébként nem áll rendelkezésre grafikus forgatókönyvnek típusától függően.  Nem fogadnak el paramétereket, de használni tudja változók tevékenység kimeneti és runbook-bemeneti paraméterekhez.  A tevékenység kimenete kerül az adatbuszba mindaddig nem kimenő hivatkozás ebben az esetben a runbook kimenete kerül.

Például az alábbi kód számításokat dátum $NumberOfDays nevű runbook bemeneti változóra használatával.  A runbook soron következő tevékenységek által használható kimenetként a számítás dátuma és időpontja ezután elküldi.

    $DateTimeNow = (Get-Date).ToUniversalTime()
    $DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
    $DateTimeStart


## <a name="links-and-workflow"></a>Hivatkozások és a munkafolyamat
A **hivatkozás** egy grafikus runbook összeköti a két tevékenység.  Akkor jelenik meg a vásznon a céltevékenységre a forrástevékenység mutató nyíl.  A tevékenységek futtassa a irányát, úgy, hogy a céltevékenységre, kezdve a forrástevékenység befejeződését követően.  

### <a name="create-a-link"></a>Hivatkozás létrehozása
Hozzon létre jelölje ki a forrás tevékenység, a kör az alakzat alján kattintson két tevékenység közötti kapcsolatot.  Húzza a nyíl a céltevékenység és kiadása.

![Hivatkozás létrehozása](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Válassza ki a hivatkozásra kattintva konfigurálja a tulajdonságait a konfigurációs paneljén.  Ide tartoznak a kapcsolat típusa, aminek ismertetését a következő táblázatban.

| Kapcsolat típusa | Leírás |
|:--- |:--- |
| Folyamat |A céltevékenységre van egyszeri futtatás minden objektum kimeneti a forrás tevékenységből.  A céltevékenységre nem működik, ha a forrás tevékenység nincs kimenet eredményez.  A forrástevékenység kimeneti objektumként érhető el. |
| Szekvencia |A céltevékenységre csak egyszer fut le.  A forrás tevékenységből kap egy objektumokból álló tömb.  A forrástevékenység kimeneti objektumokat tömbként érhető el. |

### <a name="starting-activity"></a>Kezdő tevékenység
Egy grafikus forgatókönyvnek a tevékenységeket, amelyek nem rendelkeznek olyan bejövő hivatkozás indul el.  Ez gyakran lesz volna el a kezdő tevékenység a runbook csak egy tevékenységgel.  Ha több tevékenység nem rendelkezik olyan bejövő hivatkozás, majd a runbook indul el, párhuzamosan futtatásával.  Akkor lesz majd hivatkozásokból az egyéb tevékenységeket futtatnak, mivel minden egyes befejezése.

### <a name="conditions"></a>Feltételek
Ha egy feltételt ad meg egy hivatkozást, a céltevékenységre csak akkor fut, ha oldja fel a feltétel igaz.  Általában szüksége lesz egy $ActivityOutput változó egy feltételt a forrás tevékenységből a kimeneti beolvasása.  

Egy folyamatkapcsolódása egyetlen objektumhoz olyan feltételt, és a feltétel értékeli ki a minden objektum kimeneti forrás tevékenység által.  A céltevékenységre ezután minden objektum, amely eleget tesz a feltétel fut.  Például a Get-AzureRmVm forrás tevékenységgel, a következő szintaxissal használható feltételes folyamatkapcsolódása beolvasása az erőforráscsoport neve csak virtuális gépek *csoport1*.  

    $ActivityOutput['Get Azure VMs'].Name -match "Group1"

Egy feladatütemezési hivatkozásra a feltétel csak értékeli egyszer, mert a forrás tevékenység kimenetét összes objektumokat tartalmazó egyetlen tömböt adott vissza.  Emiatt egy feladatütemezési hivatkozás nem használható, például egy folyamatkapcsolódása szűréshez, de egyszerűen határozza meg a következő tevékenység fut-e. A hálózatról például a következők emelendők tevékenységek a virtuális gép elindítása a runbook.<br> ![A feladatütemezések feltételes hivatkozás](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)<br>
Három különböző hivatkozásokat tartalmaz, amely a megadott értékek két forgatókönyv bemeneti paraméterekhez képviselő a virtuális gép nevét és az erőforráscsoport nevét annak megállapítására, amely a megfelelő művelet - ellenőrzését egy virtuális indításához indítsa el a kívánt erőforrás csoportban lévő összes virtuális gépet, vagy egy előfizetésben található összes virtuális gép van.  A csatlakozás az Azure és a Get egyetlen virtuális gép közötti feladatütemezési hivatkozás Ez a feltétel programot:

    <# 
    Both VMName and ResourceGroupName runbook input parameters have values 
    #>
    (
    (($VMName -ne $null) -and ($VMName.Length -gt 0))
    ) -and (
    (($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
    )

Egy feltételes hivatkozás használata esetén érhetők el a forrástevékenység más tevékenységeknek a fiókirodában lévő adatok a feltétel szűri.  Ha a tevékenység a forrás és a több kapcsolatot, majd minden egyes fiókiroda tevékenységekkel rendelkezésre álló adatok függ a feltétel a hivatkozásban, a fiókirodában csatlakozik.

Például a **Start-AzureRmVm** tevékenységnek a forgatókönyvben az alábbi összes virtuális gép elindul.  Két feltételes hivatkozások rendelkezik.  A kifejezést használ, az első feltételes hivatkozás *$ActivityOutput ["Start-AzureRmVM"]. IsSuccessStatusCode - eq $true* szűréséhez, ha a Start-AzureRmVm tevékenység sikeresen befejeződött.  A kifejezést használ, a második *$ActivityOutput ["Start-AzureRmVM"]. IsSuccessStatusCode - ne $true* szűréséhez, ha a Start-AzureRmVm tevékenység nem tudta elindítani a virtuális gépet.  

![Feltételes hivatkozás – példa](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Minden olyan tevékenységet, amely az első hivatkozás és használja a Get-AzureVM tevékenység kimenete csak kap a virtuális gépek, a Get-AzureVM futtatott időpontban elindított következő.  A második hivatkozásra a következő tevékenység csak kap a Get-AzureVM futtatásának időpontjában leállított virtuális gépek.  A harmadik hivatkozás a következő tevékenység megkapja az összes virtuális gép függetlenül a futó állapotot.

### <a name="junctions"></a>Elhelyezni pontokra
Elágazás egy megvárja, amíg az összes bejövő ágak befejeződött, speciális tevékenységen.  Ez lehetővé teszi több tevékenység párhuzamosan futnak, és győződjön meg arról, hogy az összes lépés előtt végrehajtotta.

Míg a szinkronizációs pont a Bejövő hivatkozások korlátlan számú, a hivatkozások nem egynél több folyamat lehet.  Bejövő sorozat hivatkozások száma nincs korlátozva.  A csatlakozási több bejövő adatcsatorna-kapcsolatot létrehozni, és mentse el a runbookot engedélyezett, de nem futtatásakor.

Az alábbi példában egy runbookot, amely elindítja a virtuális gépek halmaza ezeket a gépeket alkalmazandó javítások egyidejűleg letöltéskor részét képezi.  Elágazás annak biztosítására szolgál, hogy mindkét folyamat befejeződött, a forgatókönyv folytatása előtt.

![Szinkronizációs pont](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciklusok
A ciklus akkor, ha vissza a forrástevékenység vagy egy másik tevékenységgel, végül a forrás-hivatkozásokat biztonsági rendeltetési tevékenység hivatkozásainak.  Ciklus jelenleg nem engedélyezettek grafikus szerzői.  Ha a runbook rendelkezik ciklust, megfelelően menti, de egy hibaüzenetet fog kapni, amikor fut az.

![Ciklus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Tevékenységek adatok megosztása
Olyan adatot, amely által egy kimenő kapcsolat nélküli tevékenységet íródik a *adatbuszba* a runbookhoz.  A runbook minden tevékenysége segítségével adatokat a program az adatbuszba paraméterértékek feltöltése vagy a parancsprogramkódot.  Egy tevékenység a munkafolyamat előző tevékenység kimenete érheti el.     

Az adatok írása az adatbuszba hogyan attól függ, hogy a tevékenység a kapcsolat típusát.  Az egy **csővezeték**, az adatok egy kimenet Többszörösök objektumként.  Az egy **feladatütemezési** hivatkozásra, az adatok egy tömbként kimenet.  Ha csak egy érték, egy egyszeres elemnek tömbként kimeneti lesz.

Adatokat a program az adatbuszba két módszer egyikével érheti el.  Először használ egy **tevékenység kimeneti** egy másik tevékenység paraméter értékét meghatározó adatforrás.  Ha a kimeneti objektum, megadhat egy-egy tulajdonság.

![Tevékenység kimeneti](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Tevékenység kimeneti is le egy **PowerShell-kifejezést** adatforrás vagy egy **munkafolyamat-parancsfájl** egy ActivityOutput változó tevékenységet.  Ha a kimeneti objektum, megadhat egy-egy tulajdonság.  ActivityOutput változók használja a következő szintaxist.

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName 

### <a name="checkpoints"></a>Ellenőrzőpontok
Beállíthatja azt [ellenőrzőpontokat](automation-powershell-workflow.md#checkpoints) egy grafikus PowerShell-munkafolyamati forgatókönyv kiválasztásával a *ellenőrzőpont runbook* olyan tevékenységeket.  Ennek hatására a tevékenység futtatása után kell beállítani egy ellenőrzőpontot.

![Ellenőrzőpont](media/automation-graphical-authoring-intro/set-checkpoint.png)

Ellenőrzőpontok csak engedélyezett grafikus PowerShell munkafolyamat-forgatókönyvekről, a grafikus forgatókönyvek nem érhető el.  Ha a runbook Azure-parancsmagokat használ, kövesse az Add-AzureRMAccount bármely alkulcsaihoz tevékenységet, ha a runbook fel van függesztve, és újraindítja a ezt az ellenőrzőpontot a különböző munkavégző. 

## <a name="authenticating-to-azure-resources"></a>Azure-erőforrások hitelesítéséhez
Azure-erőforrások kezeléséhez, az Azure Automation Runbookjai az Azure-bA hitelesítés szükséges.  A [Futtatás mint fiók](automation-offering-get-started.md#creating-an-automation-account) (más néven egy egyszerű szolgáltatásnév) a alapértelmezett mód az Automation-runbook előfizetés Azure Resource Manager erőforrásaihoz.  Adhat hozzá ezt a funkciót egy grafikus forgatókönyvnek hozzáadásával a **AzureRunAsConnection** kapcsolódási eszköz, amely a powershellel [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) parancsmagot, és [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) parancsmag a vászonra. Ezt az alábbi példa szemlélteti.<br>![A futtató hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)<br>
Az beszerzése Futtatás mint kapcsolat tevékenység (pl. Get-AutomationConnection) nevű AzureRunAsConnection konstans adatforrás van konfigurálva.<br>![Futtatás mint kapcsolat konfigurációja](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)<br>
A következő tevékenységnek, az Add-AzureRmAccount, a hitelesített Futtatás mint fiók használható a runbook ad hozzá.<br>
![Adja hozzá-AzureRmAccount paraméterkészletet alakítanak](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)<br>
A paraméterek **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, és **TENANTID** akkor adja meg a mező elérési útja a tulajdonság nevét, mert a tevékenység kimenete több tulajdonságait azonosítójú objektum.  Ellenkező esetben a runbook végrehajtásakor meghiúsul hitelesítésére tett kísérlet.  Ez a szükséges minimális hitelesíteni a runbookban a futtató fiókhoz.

A visszamenőleges kompatibilitás karbantartása előfizetők felvételéhez hozott létre az automatizálási fiók használatával egy [Azure AD felhasználói fiók](automation-create-aduser-account.md) kezelése az Azure klasszikus üzembe helyezési, vagy az Azure Resource Manager-erőforrások, a módszert az Add-AzureAccount parancsmagot egy [hitelesítőadat-eszköz](automation-credentials.md) , amely jelöli az Active Directory-felhasználó, aki hozzáfér az Azure-fiókjába.

Ez a funkció adhat egy grafikus forgatókönyvnek, ha egy hitelesítőadat-eszköz felvétele a vászonra, az Add-AzureAccount tevékenység követi.  Adja hozzá AzureAccount a hitelesítő adatok tevékenység a bemeneti használja.  Ezt az alábbi példa szemlélteti.

![Hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authentication-activities.png)

A runbook, és minden ellenőrzőpont után először hitelesíteni kell.  Ez azt jelenti, továbbá Add-AzureAccount tevékenység hozzáadása után bármely ellenőrzőpont-munkafolyamat tevékenysége. Óta is használhatja ugyanazt, nem szükséges hitelesítő adatok tevékenység hozzáadása 

![Tevékenység kimeneti](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>A forgatókönyv bemeneti és kimeneti
### <a name="runbook-input"></a>A Runbook bemeneti
Egy runbook lehet szükség a felhasználó, amikor elindítja a runbookot, az Azure portálon keresztül vagy egy másik runbookból bemeneti, a jelenlegivel gyermek használata.
Például ha egy runbookot, amely létrehoz egy virtuális gépet, akkor esetleg például információkat biztosítanak a nevet a virtuális gép és egyéb tulajdonságok minden alkalommal, amikor elindítja a runbookot.  

Elfogadja a runbook a megadott meghatározhat egy vagy több bemeneti paraméterek.  Értékeket ad meg a következő paraméterek minden alkalommal, amikor a runbook indítását.  Amikor elindít egy forgatókönyvet az Azure portálon, akkor arra fogja kérni, hogy adjon értéket az egyes, a forgatókönyv bemeneti paramétereket.

Egy runbook bemeneti paraméterek eléréséhez kattintson a **bemeneti és kimeneti** runbook gombjára.  

![A Runbook bemeneti](media/automation-graphical-authoring-intro/runbook-edit-input-output.png) 

Ekkor megnyílik a **bemeneti és kimeneti** vezérlő, ahol szerkesztheti a meglévő bemeneti paramétert, vagy hozzon létre egy újat kattintva **bemenet hozzáadása**. 

![Bemenet hozzáadása](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Minden egyes bemeneti paraméter a következő táblázat a tulajdonságok határozzák meg.

| Tulajdonság | Leírás |
|:--- |:--- |
| Név |A paraméternek egyedi neve.  Ez csak alfanumerikus karaktereket tartalmazhat, és nem tartalmazhat szóközt. |
| Leírás |A bemeneti paraméter nem kötelező leírása. |
| Típus |A paraméter értéke a várt adattípus.  Az Azure-portálon bemeneti során egy megfelelő vezérlő biztosítja az egyes paramétereket az adattípus. |
| Kötelező |Meghatározza, hogy értéket kell megadni a paraméterben.  A runbook nem indítható el, ha nem ad meg értéket minden kötelező paraméter, amely nem rendelkezik alapértelmezett értékkel. |
| Alapértelmezett érték |Itt adhatja meg, milyen értéket a paraméter akkor használatos, ha nincs megadva.  Ez lehet Null vagy egy adott értéket. |

### <a name="runbook-output"></a>Runbook kimenete
Egy kimenő hivatkozás nem rendelkező tevékenység által létrehozott adatok nem kerülnek be a [a runbook kimenete](http://msdn.microsoft.com/library/azure/dn879148.aspx).  A kimeneti mentik a runbook-feladat, és készen áll a szülőrunbookból, a runbook gyermek használata esetén.  

## <a name="powershell-expressions"></a>PowerShell kifejezések
Egy grafikus szerzői előnye van elérhető a PowerShell alapos ismeretére runbookok készítéséhez.  Jelenleg akkor ismerniük kell a PowerShell bit, ha az egyes feltöltését szolgáló [paraméterértékek](#activities) és beállítás [hivatkozási feltételek](#links-and-workflow).  Ez a témakör egy gyors Bevezetés a PowerShell kifejezések azoknak a felhasználóknak, akik tisztában van, nem lehet.  PowerShell teljes részletek [a Windows PowerShell parancsfájlok](http://technet.microsoft.com/library/bb978526.aspx). 

### <a name="powershell-expression-data-source"></a>PowerShell kifejezés adatforrása
Használhatja a PowerShell-kifejezést adatforrásként értékének feltöltése egy [tevékenység-paraméter](#activities) néhány PowerShell-kódjába eredményeivel.  Ennek oka lehet egy egyszerű szövegsor néhány egyszerű függvény vagy több sor néhány komplex logikai végző kódot.  Bármely olyan kimenete, amelyhez nem tartozik egy változóhoz parancs kimenetét a paraméter értéke. 

Például a következő parancsot kellene kimenő az aktuális dátumot. 

    Get-Date

Az alábbi parancsokat hozza létre az aktuális dátum utáni karakterlánc, és rendelje hozzá egy változó.  A változó tartalmát majd kerülnek a kimenetbe 

    $string = "The current date is " + (Get-Date)
    $string

A következő parancsok értékeli az aktuális dátumot, és az aktuális nap a hétvégi vagy a hét napja voltát jelző adja vissza. 

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }


### <a name="activity-output"></a>Tevékenység kimeneti
Egy előző tevékenység kimenete használata a runbookokban, a következő szintaxissal használja a $ActivityOutput változó.

    $ActivityOutput['Activity Label'].PropertyName

Például előfordulhat, hogy egy tevékenység, amelyhez a virtuális gép neve tulajdonsággal ebben az esetben használhatja az alábbi kifejezés.

    $ActivityOutput['Get-AzureVm'].Name

Ha a tulajdonság, amely a virtuális gép szükséges objektum helyett csak egy tulajdonság, majd meg meghaladná a teljes objektum a következő szintaxis használatával.

    $ActivityOutput['Get-AzureVm']

Egy tevékenység, amely összefűzi a szöveget a virtuális gép nevét a következő összetett kifejezésben használhatja.

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### <a name="conditions"></a>Feltételek
Használjon [összehasonlító operátorok](https://technet.microsoft.com/library/hh847759.aspx) értékek összehasonlítása, vagy felfedheti, ha egy értéke megegyezik-e egy adott mintával.  Összehasonlítás $true vagy $false értéket ad vissza.

Például a következő feltételt határozza meg, hogy a virtuális gép tevékenység nevű *Get-AzureVM* jelenleg *leállt*. 

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

Az alábbi feltételeket ellenőrzi, hogy ugyanaz a virtuális gép állapotban van, minden más, mint *leállt*.

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

Több feltételt is összekapcsolhat egy [logikai operátor](https://technet.microsoft.com/library/hh847789.aspx) például **- és** vagy **- vagy**.  Például az alábbi feltételeket-e ellenőrzi, hogy az azonos virtuális gépen az előző példában a állapotban van *leállt* vagy *leállítása*.

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping") 


### <a name="hashtables"></a>Szórótáblában
[Szórótáblában](http://technet.microsoft.com/library/hh847780.aspx) név/érték párok, amelyek hasznosak lehetnek egy értékhalmazt ad vissza a rendszer.  Az egyes tevékenységek tulajdonságok ettől a egy kivonattáblát egy egyszerű érték helyett.  Mint dictionary néven hashtable is találkozhat. 

A következő szintaxissal létrehozni egy kivonattáblát.  Egy kivonattáblát tetszőleges számú bejegyzést tartalmazhat, de egyes határozzák meg a nevét és értékét.

    @{ <name> = <value>; [<name> = <value> ] ...}

Például az alábbi kifejezés egy kivonattáblát a várt egy kivonattáblát egy internetes keresési értékekkel tevékenység-paraméter használható az adatforrás hoz létre.

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

Az alábbi példában nevű tevékenység *Twitter kapcsolat beolvasása* egy kivonattáblát feltöltéséhez.

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## <a name="next-steps"></a>Következő lépések
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md) 
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* Szeretné megtudni, hogyan hitelesítheti az Automation Futtatás mint fiók használatával, lásd: [konfigurálása Azure futtató fiók](automation-sec-configure-azure-runas-account.md)

