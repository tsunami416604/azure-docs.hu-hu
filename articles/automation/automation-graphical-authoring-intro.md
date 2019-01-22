---
title: Grafikus létrehozás az Azure Automationben
description: Grafikus létrehozásról lehetővé teszi a runbookok létrehozása az Azure Automation-kód használata nélkül. A cikk ismerteti a grafikus létrehozásról bemutatása és a grafikus runbookok létrehozásának megkezdéséhez szükséges összes részleteit.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6d637436721ff464f58e41069bb00746fcd82410
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427236"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafikus létrehozás az Azure Automationben

Grafikus szerzői műveletek lehetővé teszi runbookok létrehozása az Azure Automation az alapul szolgáló Windows PowerShell vagy a PowerShell-munkafolyamati kód vesződni. Tevékenységek hozzáadása a vászonra a parancsmagok és a runbookok tárból, kapcsolja őket össze, és konfigurálja úgy, hogy az űrlap-munkafolyamatot. Ha minden eddiginél dolgozott a System Center Orchestrator és Service Management Automation (SMA), majd ez Bizonyára ismerős lesz Önnek

A cikk ismerteti a grafikus létrehozásról bemutatása, és azokat az elveket, hogyan kezdheti el grafikus runbook létrehozása.

## <a name="graphical-runbooks"></a>Grafikus runbookokban

Az összes, az Azure Automation runbookjai Windows PowerShell-munkafolyamatok. Grafikus és a grafikus PowerShell-munkafolyamati runbookok, amelyek az Automation-feldolgozók által futtatott PowerShell-kód előállítása, de Ön nem tudni megtekinteni azt, vagy közvetlenül módosítja. A grafikus runbookok a grafikus PowerShell-munkafolyamati forgatókönyv, és fordítva sem lehet alakítani, de azok nem alakítható át szöveges runbookok. Meglévő szöveges forgatókönyv nem lehet importálni a grafikus szerkesztő.

## <a name="overview-of-graphical-editor"></a>A grafikus szerkesztő – áttekintés

A grafikus szerkesztő létrehozásával vagy szerkesztésével a grafikus runbookok megnyithatja az Azure Portalon.

![Grafikus munkaterületet](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

A következő szakaszok ismertetik a vezérlőelemek a grafikus szerkesztőben.

### <a name="canvas"></a>Vászon

A vászon az, ahol a runbook tervezésekor. A könyvtár vezérlőben a csomópontokat a tevékenységek hozzáadása a forgatókönyvhöz, és csatlakoztassa őket a runbook a logika definiálható mutató hivatkozásokat tartalmaz.

A vászon alján a vezérlők használatával nagyíthat és.

### <a name="library-control"></a>Könyvtár vezérlőben

A könyvtár vezérlőben, ahol ki kell választania [tevékenységek](#activities) hozzáadása a runbookhoz. Hozzáadja őket a vásznon, ahol Ön csatlakoztathatja őket a többi tevékenység. Tartalmazza az alábbi táblázatban ismertetett négy részből áll:

| Section | Leírás |
|:--- |:--- |
| Parancsmagok |A runbook felhasználható összes parancsmagot tartalmazza. Parancsmagok modul szerint vannak rendszerezve. A modulokat az automation-fiókban telepített összes érhetők el. |
| Runbookok |Tartalmazza a forgatókönyvek az automation-fiók. Ezek a runbookok gyermek runbookként használható vásznon lehet hozzáadni. Csak az azonos core típusú, a runbook szerkesztett forgatókönyvek jelennek meg; a grafikus runbookok csak PowerShell-alapú forgatókönyvek jelennek meg, amíg a grafikus PowerShell-munkafolyamati runbookok csak PowerShell-munkafolyamat-alapú forgatókönyvek jelennek meg. |
| Objektumok |Magában foglalja a [automation-adategységeket](https://msdn.microsoft.com/library/dn939988.aspx) az automation-fiókban a runbookban használható. Egy objektumot ad hozzá egy runbookot, amikor hozzáadja a munkafolyamat-tevékenység, amely lekérdezi a kiválasztott eszköz. Változó adategységek esetén azt is kiválaszthatja, hogy egy tevékenység, a változó vagy állítsa be a változó hozzáadása. |
| Runbook-vezérlés |Használható a runbook-vezérlési tevékenységek az aktuális runbookot tartalmazza. A *csatlakozási* több bemenet tart, és megvárja, amíg az összes munkafolyamat folytatása előtt befejeződött. A *kód* tevékenység fut egy vagy több sort a PowerShell vagy a PowerShell-munkafolyamati kód a grafikus forgatókönyv típusától függően. Ezt a tevékenységet is használhatja, egyéni kódot, vagy más tevékenységekkel elérése a funkciókat. |

### <a name="configuration-control"></a>Konfiguráció-ellenőrzés

A konfiguráció-ellenőrzés, ahol meg kell részleteit egy objektum kijelölve a vásznon. A tulajdonságok a vezérlő érhetők el a kijelölt objektum típusától függ. Amikor kiválaszt egy beállítást a konfiguráció-ellenőrzés, további paneleken megnyílik annak érdekében, hogy további információkat.

### <a name="test-control"></a>Ellenőrző tesztelése

A Test-vezérlő nem jelenik meg a grafikus szerkesztő első indításakor. Mikor van megnyitva, interaktív módon [grafikus runbook tesztelése](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Grafikus forgatókönyv eljárások

### <a name="exporting-and-importing-a-graphical-runbook"></a>Grafikus runbook importálása és exportálása

Grafikus runbook közzétett verziójának csak exportálhatja. Ha a runbook még nem lett közzétéve, akkor a **exportálása** gomb le van tiltva. Amikor rákattint a **exportálása** gombra, a runbook letöltődik a helyi számítógépen. A fájl neve megegyezik-e a runbookban egy *graphrunbook* bővítmény.

Egy grafikus vagy a grafikus PowerShell-munkafolyamati forgatókönyv fájl importálásához válassza a **importálása** lehetőséget egy runbook felvételekor. Amikor kiválasztja az importálandó fájl, megtarthatja azonos **neve** , vagy adjon meg egy újat. A Runbook típusa mező után azt értékeli, hogy a kijelölt fájl, és válassza ki egy más típusú, amely nem megfelelő kísérli meg, ha egy üzenet jelenik megállapítva lehetséges ütközések és a konvertálás során, lehetséges, hogy szintaxis jelenik meg a runbook típusa hibák.

![Runbook importálása](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Grafikus runbook tesztelése

Tesztelheti egy runbook vázlatként megjelölt verzióját az Azure Portalon, a runbook közzétett verziójának változatlanul, vagy akkor tesztelheti egy új runbookot, mielőtt azt közzé lett téve. Ez lehetővé teszi, hogy ellenőrizze, hogy a runbook megfelelően működik, mielőtt lecseréli a közzétett verziót. Egy runbook tesztelésekor a program a runbook vázlatát hajtja végre, és minden olyan műveleteket, amelyeket végrehajt a végezhető el. Az előzményeket nem feladat jön létre, de kimeneti megjelennek a Tesztkimenet ablaktáblán.

Nyissa meg a runbook tesztelése vezérlő nyissa meg szerkesztésre a runbookot, és kattintson a a **teszt panel** gombra.

A teszt vezérlő kérni fogja a bemeneti paramétereket, és a runbook indításához kattintson a a **Start** gombra.

### <a name="publishing-a-graphical-runbook"></a>Grafikus runbook közzététele

Az Azure Automationben minden runbook rendelkezik egy Vázlat és egy közzétett verziója. Csak a közzétett verziót lehet futtatni, és kizárólag a piszkozat verzió szerkeszthető. A közzétett verzióra nincsenek hatással a piszkozat verzióban végrehajtott módosítások. Ha készen áll a érhető el a vázlatként megjelölt verziót, majd közzéteszi, gyakorlatilag felülírja a közzétett verziót a piszkozattal.

Nyissa meg a runbook szerkesztésre, és kattintson a grafikus runbookok közzéteheti a **közzététel** gombra.

Amikor egy runbook még nem lett közzétéve, állapotba került **új**. Ha közzé van téve, állapotba került **közzétett**. A runbook szerkesztése után azt közzé lett téve, és a Vázlat és egy közzétett verzió eltérőek, a runbook rendelkezik egy állapotát **szerkesztés alatt**.

![A Runbook állapota](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Akkor is lehetőséget egy runbook közzétett változatának visszaállítása. Ez azonnal jelez a runbook legutóbbi közzétételének, és a runbook vázlatverzióját lecseréli a közzétett verzió óta végrehajtott módosításokat.

## <a name="activities"></a>Tevékenységek

Tevékenységek a runbookok építőkövei. Egy tevékenység lehet egy PowerShell-parancsmag, a gyermek runbook vagy se aktivita pracovního postupu. A könyvtár vezérlőben kattintson jobb gombbal, majd válassza a runbooknak a tevékenység hozzáadása. **adja hozzá a vászonhoz**. Ezután kattintson és húzza a tevékenységet, hogy vigye bárhová a vásznon, adja meg. Helyét a tevékenység a vásznon, nem befolyásolja a runbook semmilyen módon a műveletet. Elrendezése a runbook azonban látja azt leginkább megfelelő jeleníthetik meg a műveletet.

![Felvétel a vászonra](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Válassza ki a tevékenységet, a tulajdonságok és a paraméterek konfigurálása a konfiguráció panelen a vásznon. Módosíthatja a **címke** valami a tevékenység, amely, hogy beszédes legyen. Az eredeti parancsmag továbbra is fut, egyszerűen módosítja a megjelenítendő nevét, amely a grafikus szerkesztőben szolgál. A címke a runbookon belül egyedinek kell lennie.

### <a name="parameter-sets"></a>Paraméterkészlettel

Paraméterkészlet határozza meg, hogy fogadja el az értékeket egy adott parancsmag kötelező és választható paramétereket. Minden parancsmag, állítsa be legalább egy paramétert, és néhány több rendelkeznek. Ha a parancsmag több paraméterkészlettel rendelkezik, majd jelöljön ki melyiket előtt konfigurálhatja úgy a paramétereket. A paraméterek, amelyeket konfigurálhat attól függ, hogy az Ön által választott paraméterkészletet. Módosíthatja a paraméterkészletet használja tevékenység kiválasztásával **paraméterkészlet** és a egy másik készlet kiválasztása. Ebben az esetben a konfigurált paraméterértékeket is elvesznek.

A következő példában a Get-AzureRmVM parancsmag három paraméterkészlettel rendelkezik. Paraméter értéke nem lehet konfigurálni, mindaddig, amíg a paraméterkészlettel valamelyikét választja. A ListVirtualMachineInResourceGroupParamSet paraméterkészletet egy erőforráscsoportba tartozó összes virtuális gép visszaadó és a egy nem kötelező paraméter. A **GetVirtualMachineInResourceGroupParamSet** van a virtuális gép szeretné olvasni, és két kötelező és a egy nem kötelező paraméter megadására.

![Paraméterkészlet](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Paraméterértékek

Ha megad egy értéket a paraméterhez, ki kell választania egy adatforrás határozza meg, hogy az érték van megadva. Az adatforrásokat, amelyek egy adott paraméter függ, hogy a paraméter érvényes értékei érhető el. Például Null lehetőség nem érhető el az egyik paraméter, amely nem engedélyezi a null értékeket.

| Adatforrás | Leírás |
|:--- |:--- |
| Konstans érték |Adja meg a paraméter értékét. Ez a tulajdonság csak a következő adattípusokat érhető el: Int32, Int64, String, logikai értéket, DateTime, váltson. |
| Tevékenység kimenete |Egy tevékenységgel, amely szerepel az aktuális tevékenység a munkafolyamat kimenetét. Az összes érvényes tevékenységek jelennek meg. Válassza ki a csak a tevékenység kimenetét használja a paraméter értéke. Ha a tevékenység kimenete egy több tulajdonsággal rendelkező objektumot, majd beírhatja be a tulajdonság nevét a tevékenység kiválasztása után. |
| Forgatókönyv-bemenet |Válassza ki a forgatókönyv bemeneti paramétere a tevékenység-paraméter bemeneteként. |
| Változóeszköz |Válassza ki a egy automatizálási változó bemenetként. |
| Hitelesítőadat-eszköz |Válassza ki az automatizálási hitelesítő adatok bemenetként. |
| Tanúsítványobjektum |Válassza ki az Automation-tanúsítvány bemenetként. |
| Kapcsolatobjektum |Válassza ki az automatizálási kapcsolat bemenetként. |
| PowerShell-kifejezés |Adja meg az egyszerű [PowerShell-kifejezés](#powershell-expressions). A kifejezés előtt a tevékenység és az eredmény a paraméter értéke a használt lesz kiértékelve. Változók használatával tekintse meg a kimeneti tevékenység vagy a forgatókönyv bemeneti paramétere. |
| Nincs beállítva |Törli a korábban beállított értéket. |

#### <a name="optional-additional-parameters"></a>További nem kötelező paraméterek

Minden parancsmag lehetősége a további paramétereket adja meg. Ezek a PowerShell általános paramétereivel vagy más egyéni paraméterek. Megnyílik egy szövegmező, ahol megadhatja a paramétereket a PowerShell-szintaxis használatával. Használja például a **részletes** általános paramétert kell megadni **"-Verbose: $True"**.

### <a name="retry-activity"></a>Ismételje meg a tevékenység

**Újrapróbálkozási viselkedés** lehetővé teszi, hogy egy tevékenység futtatásának többször addig, amíg egy bizonyos feltétel teljesül, hasonlóan egy hurkot. Ez a funkció, amely több alkalommal kell futnia, a hibáknak tevékenységek is használhat, és előfordulhat, hogy egynél több kísérlet sikeres, vagy kimeneti információi tesztelhetők az érvényes adatok szempontjából a tevékenység.

Ha engedélyezi a tevékenység retry, beállíthatja a késleltetés és a egy feltételt. A késleltetés az az idő, (másodperceken vagy perceken mérve), hogy a runbook várakozzon, mielőtt ismét elindul, a tevékenység. Ha nincs késleltetés van megadva, majd a tevékenység fognak újra futni befejezését követően azonnal.

![Tevékenység újrapróbálkozási késleltetést](media/automation-graphical-authoring-intro/retry-delay.png)

Az újrapróbálkozási feltétel, amely minden alkalommal, amikor a tevékenység futtatása után abban az esetben egy PowerShell-kifejezéssel. Ha a kifejezés igaz értékre, majd a tevékenységet, ismét elindul. Ha a kifejezés false (hamis), majd a tevékenység nem fut újra, és a következő tevékenységnek mozgatása a runbookot.

![Tevékenység újrapróbálkozási késleltetést](media/automation-graphical-authoring-intro/retry-condition.png)

Az újrapróbálkozási feltétel használhatja, amely hozzáférést biztosít a tevékenység-újrapróbálkozások kapcsolatos adatokhoz $RetryData nevű változóra. Ezt a változót a következő táblázat a tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| NumberOfAttempts |Száma, amelyek a tevékenység futott. |
| Kimenet |A tevékenység a legutóbbi futtatás kimenete. |
| TotalDuration |Lejárt az idő a tevékenység az első alkalommal indítása óta eltelt idő. |
| StartedAt |Ideje UTC formátumban, a tevékenység először lett elindítva. |

Az alábbiakban példákat tevékenység ismételje meg a feltételeket.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Miután konfigurált egy tevékenység újrapróbálkozási feltétel, a tevékenység tartalmaz két vizuális jelek emlékeztesse. Egy tevékenység jelenik meg, és más amikor a tevékenység konfigurálását.

![Tevékenység újrapróbálkozási Visual mutatók](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Munkafolyamat-parancsfájl-vezérlés

A kód vezérlő egy, speciális tevékenységen, amely elfogadja a PowerShell vagy a PowerShell-munkafolyamati parancsprogram éppen létrehozott annak érdekében, hogy az funkciók, amelyek egyébként nem áll rendelkezésre grafikus runbook típusától függően. Nem fogad el paramétereket, de a változókat akkor használhatja tevékenység kimeneti és runbook-bemeneti paraméterekhez. A tevékenység kimenetet adnak hozzá a program az adatbuszba mindaddig nem kimenő hivatkozás ebben az esetben a runbook kimenetének megjelenik.

A következő kód például dátum számítások $NumberOfDays nevű runbook bemeneti változóval hajt végre. Majd küld egy számított dátum-idő a runbook soron következő tevékenységek által használható kimenetként.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Hivatkozások és a munkafolyamat

A **hivatkozás** grafikus runbookokban köti össze a két tevékenységet. Akkor jelenik meg a vásznon a céltevékenység a forrástevékenység a mutató nyíllal. A tevékenységek futtatási irányát, az a és a céltevékenység a forrástevékenység befejeződését követően indítása.

### <a name="create-a-link"></a>Hivatkozás létrehozása

Hozzon létre egy hivatkozást a forrásoldali tevékenység kiválasztva, majd kattintson a körre, az alakzat alján két tevékenység között. Húzza a nyilat a céltevékenység és kiadás.

![Hivatkozás létrehozása](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Válassza ki a hivatkozásra kattintva konfigurálja a tulajdonságait, a konfiguráció panelen. Ez tartalmazza a hivatkozás típusát, amely az alábbi táblázatban ismertetett:

| Hivatkozás típusa | Leírás |
|:--- |:--- |
| Folyamat |A céltevékenység fut egyszer minden egyes objektum kimeneti a forrásoldali tevékenységnek. A céltevékenység nem működik, ha a forrásoldali tevékenységnek nincs kimenet eredményez. A forrásoldali tevékenység kimenete objektumként érhető el. |
| Szekvencia |A céltevékenység csak egyszer fut le. A forrásoldali tevékenység Eszközindítási objektumokból álló tömb. A forrásoldali tevékenység kimenete objektumok tömbjeként érhető el. |

### <a name="starting-activity"></a>Kezdő tevékenység

Egy grafikus runbook olyan tevékenységet, amely nem rendelkezik olyan bejövő hivatkozás kezdődik. Ez a gyakran csak egy tevékenységet, amely a kezdő tevékenység a runbook-kiszolgálóként lenne. Ha több tevékenység nem rendelkezik olyan bejövő hivatkozás, akkor a runbook elindítja párhuzamos futtatásával. A hivatkozások más tevékenységek futtatásához, minden egyes befejezése következik.

### <a name="conditions"></a>Feltételek

Ha egy feltétel hivatkozást ad meg, a céltevékenység csak akkor fut, ha a feltétel igaz. Általában egy $ActivityOutput változó használatakor a feltétel a forrásoldali tevékenység a kimeneti lekérése

Egy folyamat hivatkozás olyan egyetlen objektumhoz tartozó feltételt, és a feltétel kiértékelése minden objektum kimeneti a forrásoldali tevékenység. A céltevékenység ezután futtassa az egyes objektumok, amely megfelel a feltételnek. Például egy a Get-AzureRmVm forrás tevékenységgel, a következő szintaxissal használható egy feltételes folyamatkapcsolódása lehet beolvasni az erőforráscsoport neve csak virtuális gépek *csoport1*.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

A feladatütemezési hivatkozást a feltétel csak akkor történik meg egyszer, mert a forrásoldali tevékenység kimenetében minden objektumokat tartalmazó egy egyetlen olyan tömböt adott vissza. Emiatt egy feladatütemezési hivatkozás nem használható egy folyamatkapcsolódása lehet például szűréshez, de egyszerűen határozza meg a következő tevékenység fut-e. Vegyük például a következő tevékenységkészletet a forgatókönyvünk VM indítása.

![A feladatütemezések feltételes hivatkozás](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Nincsenek a három különböző feladatütemezési hivatkozások, amelyek a megadott értékek száma két runbook bemeneti paramétereinek jelölő a virtuális gép neve és erőforráscsoport-nevet, hogy megállapítsuk a megfelelő művelet érvénybe - ellenőrizni egyetlen virtuális gép indításához indítsa el az összes virtuális gép és az erőforrás csoport vagy egy adott előfizetés összes virtuális gépet. A feladatütemezési hivatkozás csatlakozás az Azure és a Get egyetlen virtuális gép között itt látható a feltételes logika:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Egy feltételes hivatkozás használata esetén elérhető a forrásoldali tevékenység más tevékenységek a fiókirodában lévő adatok a feltétel alapján szűri. Ha a tevékenység az adatforrás, több kapcsolatot, a tevékenységek az egyes ágakhoz adatait az a feltétel, a hivatkozás a fiókirodában csatlakozik a függenek.

Ha például a **Start-AzureRmVm** tevékenységnek a forgatókönyvben az alábbi összes virtuális gép elindul. Két feltételes hivatkozásokat tartalmaz. Az első feltételes hivatkozás a kifejezést használja *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - eq $true* szűréséhez, ha a Start-AzureRmVm tevékenység sikeresen befejeződött. A kifejezést használ, a második *$ActivityOutput ['Start-AzureRmVM']. IsSuccessStatusCode - ne $true* szűréséhez, ha a Start-AzureRmVm tevékenység nem tudta elindítani a virtuális gépet.

![Példa feltételes hivatkozás](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Minden olyan tevékenységnél, amely az első hivatkozásra, és használja a Get-AzureVM tevékenység kimenetét csak fog kapni a virtuális gépek, a Get-AzureVM futtatott időpontban elindított követi. Bármely, a második hivatkozásra követő tevékenység csak a Get-AzureVM futtatásának időpontjában leállított virtuális gépek beolvasása. A harmadik hivatkozás következő lekérdezi az összes virtuális gép függetlenül a futó állapotot.

### <a name="junctions"></a>Elhelyezni pontokra

A szinkronizációs pont egy, speciális tevékenységen, amely megvárja, amíg az összes bejövő ág befejeződött. Ez lehetővé teszi, hogy párhuzamosan több tevékenységet futnak, és győződjön meg arról, hogy az összes lépés előtt végrehajtotta.

A szinkronizációs pont is van a Bejövő hivatkozások korlátlan számú, hivatkozások egyikét nem lehet hosszabb folyamat lehet. Bejövő feladatütemezési hivatkozások száma nem korlátozott. A szinkronizációs pont létrehozásához több bejövő folyamat hivatkozásokkal, és mentse a forgatókönyvet engedélyezett, de ez nem sikerül, ha futtatja.

Az alábbi példa egy runbookot, amely elindítja a virtuális gépek egyidejűleg az ezeken a gépeken alkalmazandó javítások letöltése közben részét képezi. A szinkronizációs pont segítségével győződjön meg arról, hogy mindkét folyamat befejeződött, a runbook folytatása előtt.

![Szinkronizációs pont](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciklusok

Egy ciklus akkor, ha egy cél tevékenység hivatkozások vissza a forrásoldali tevékenység vagy egy másik tevékenység hivatkozó végül forrásként. Ciklusok jelenleg nem engedélyezett a grafikus létrehozásról. Ha a runbook egy ciklust, megfelelően menti, de hibaüzenetet kap, futtatásakor.

![Ciklus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Tevékenységek között adatokat megosztó

Egy kimenő kapcsolatot a tevékenység kimeneti adatok íródik a *adatbuszba* a runbookhoz. A runbook minden tevékenysége segítségével adatokat a program az adatbuszba paraméterértékek feltöltése vagy a programkódot. Egy tevékenység hozzáférhet minden előző tevékenység a munkafolyamat kimenetét.

Hogyan írja az adatokat az adatbuszba attól függ, hogy a tevékenység a hivatkozás típusát. Az egy **folyamat**, az adatok egy kimenet Többszörösök objektumként. Az egy **feladatütemezési** hivatkozásra, az adatok egy tömbként kimenet. Ha csak egy érték, egy egyetlen elem tömbként egy kimenet.

Az adatokat a program az adatbuszba két módszer egyikével érheti el. Először használ egy **tevékenység kimenete** adatforrást egy másik tevékenység paraméter feltöltéséhez. Ha a kimenet egy objektumot, egy-egy tulajdonság is megadhat.

![Tevékenység kimenete](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Is lekérhet egy tevékenység kimenetét egy **PowerShell-kifejezés** adatforrás és a egy **munkafolyamat-parancsprogram** tevékenységet egy ActivityOutput változó. Ha a kimenet egy objektumot, egy-egy tulajdonság is megadhat. ActivityOutput változókat használja a következő szintaxist.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Ellenőrzőpontok

Beállíthat [ellenőrzőpontok](automation-powershell-workflow.md#checkpoints) a grafikus PowerShell-munkafolyamati runbook kiválasztásával *ellenőrzőpont-forgatókönyv* minden olyan tevékenységnél. Ennek hatására a tevékenység futtatása után kell beállítani egy ellenőrzőpontot.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Az ellenőrzőpontok csak akkor engedélyezett, a grafikus PowerShell-munkafolyamati runbookok, a grafikus runbookok nem érhető el. Ha a runbook használja az Azure-parancsmagokon, kövesse a Connect-AzureRmAccount bármely alkulcsaihoz tevékenység abban az esetben a runbook fel van függesztve, és újraindítja az ellenőrzőpont egy másik feldolgozón.

## <a name="authenticating-to-azure-resources"></a>Azure-erőforrásokhoz való hitelesítése

Azure-erőforrások kezeléséhez, az Azure Automation Runbookjai az Azure-bA hitelesítésre van szükség. A [Futtatás mint fiók](automation-create-runas-account.md) (más néven egyszerű szolgáltatás) a alapértelmezett mód az Automation-runbookok az előfizetésében lévő Azure Resource Manager-erőforrások eléréséhez. Adja hozzá egy grafikus runbook ezt a funkciót is hozzáadhat a **AzureRunAsConnection** kapcsolatobjektum, amely a powershellel [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) parancsmagot, és [ Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot a vászonhoz. Ezt a következő példa mutatja be:

![A futtató hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A futtató kapcsolat beszerzése tevékenységet (vagyis a Get-AutomationConnection) az állandó érték zdroj dat AzureRunAsConnection van konfigurálva.

![Futtatás mint kapcsolatának konfigurálása](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A következő tevékenységnek, a Connect-AzureRmAccount, hozzáadja a hitelesített fiókhoz tartozó futtató fiók használata a runbook.

![Connect-AzureRmAccount Parameter Set](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> **Add-AzureRmAccount** alias már **Connect-AzureRMAccount**. Ha a Keresés a szalagtár elemmel, ha nem látja, akkor **Connect-AzureRMAccount**, használhat **Add-AzureRmAccount**, vagy frissítheti az a modulokat az Automation-fiókban.

A paraméterek **APPLICATIONID**, **CERTIFICATETHUMBPRINT**, és **TENANTID** meg kell adnia a mező elérési útja a tulajdonság nevét, mert a tevékenység egy több tulajdonsággal rendelkező objektumot jelenít meg. Ellenkező esetben a runbook végrehajtásakor sikertelen hitelesítésére tett kísérlet. Ez az, hogy mit kell legalább a runbookban a futtató fiókkal hitelesítést.

A visszamenőleges kompatibilitás karbantartása-előfizető, aki egy automatizálási fiók használatával hozott létre egy [Azure AD felhasználói fiók](automation-create-aduser-account.md) kezelése az Azure klasszikus üzembe helyezés, vagy az Azure Resource Manager-erőforrásokon, hitelesíteni a metódus a -AzureAccount parancsmagot egy [hitelesítőeszközt](automation-credentials.md) , amely egy Active Directory-hozzáféréssel rendelkező felhasználó jelöli az Azure-fiókjába.

Ez a funkció adhat a grafikus runbookok hitelesítőadat-eszköz ad hozzá a vászonhoz az Add-AzureAccount tevékenység követi. -AzureAccount tevékenységgel hitelesítő adatokat a bemeneti. Ezt a következő példa mutatja be:

![A hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authentication-activities.png)

A runbook, és minden egyes ellenőrzőpont után kezdetekor hitelesíteni kell. Ez azt jelenti, hogy emellett Add-AzureAccount tevékenység hozzáadása után bármely Checkpoint-Workflow tevékenységet. Mivel ugyanaz használhatja, nem kell hitelesítő adat hozzáadása tevékenység

![Tevékenység kimenete](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook bemeneti és kimeneti

### <a name="runbook-input"></a>Forgatókönyv-bemenet

Runbook bemeneti vagy egy felhasználó, amikor elindítja a runbookot, az Azure Portalon keresztül, vagy egy másik runbookból lehet szükség, a jelenlegivel gyermek használata esetén.
Például ha egy runbookot, amely létrehoz egy virtuális gépet, szükség lehet az információ, például a nevét, a virtuális gép és egyéb tulajdonságok minden alkalommal, amikor a runbook elindításához.

Runbook bemeneti egy definiálásával fogadja el, vagy több bemeneti paraméterek. Azt adja meg ezeket a paramétereket a runbook minden egyes indításakor. Amikor elindít egy runbookot az Azure Portallal, megkéri, hogy adjon meg értékeket a runbook bemeneti paraméterei.

Runbook bemeneti paramétereinek kattintva elérheti a **bemeneti és kimeneti** gombra a runbook eszköztáron.

Ekkor megnyílik a **bemeneti és kimeneti** vezérlő, ahol szerkesztheti a meglévő bemeneti paraméter, vagy hozzon létre egy új kattintva **bemenet hozzáadása**.

![Bemenet hozzáadása](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Egyes bemeneti paramétereket határozzák meg a tulajdonságait az alábbi táblázatban:

| Tulajdonság | Leírás |
|:--- |:--- |
| Name (Név) |A paraméternek egyedi neve. Ez csak alfanumerikus karaktereket tartalmazhat, és nem tartalmazhat szóközt. |
| Leírás |A bemeneti paraméter leírását. |
| Típus |A paraméter értéke a várt adattípus. Az Azure Portalon kimondása a bemeneti biztosít az egyes paraméterekhez tartozó adattípus egy megfelelő vezérlőt. |
| Kötelező |Itt adhatja meg, hogy értéket kell adni a paraméter. A runbook nem indítható el, ha nem ad meg értéket minden kötelező paraméter, amely nem rendelkezik meghatározott alapértelmezett értéket. |
| Alapértelmezett érték |Itt adhatja meg, milyen értéket a paraméterhez szolgál, ha nincs megadva. Ez lehet null értékű vagy egy adott érték. |

### <a name="runbook-output"></a>Runbook kimenete

Nem rendelkezik egy kimenő kapcsolatot tevékenység által létrehozott adatokat a rendszer menti a [a runbook kimenetének](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). A kimeneti is mentve lesz a runbook-feladat és a szülő runbook a runbook gyermek használatakor.

## <a name="powershell-expressions"></a>PowerShell-kifejezés

Az egyik előnye, a grafikus létrehozásról biztosít, lehetővé teszi az alapos ismeretére PowerShell runbookok készítéséhez. Jelenleg ellenére, hogy az egyes feltöltése a PowerShell egy kis ismernie kell [paraméterértékek](#activities) és beállítás [hivatkozási feltételek](#links-and-workflow). Ez a szakasz röviden bemutatja a PowerShell kifejezésekre azoknak a felhasználóknak arról, hogy nem ismeri azt tartalmazza. Részletes információk a PowerShell esetén érhető el [parancsfájlkezelés a Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx).

### <a name="powershell-expression-data-source"></a>PowerShell-kifejezés adatforrása
Használhatja a PowerShell-kifejezés adatforrásként való feltöltéséhez értékét egy [tevékenység-paraméter](#activities) PowerShell kód eredményét. Ez lehet egyetlen sornyi kódot, amely néhány egyszerű függvényt vagy több sor néhány összetett logikát végrehajtó hajt végre. Bármely olyan kimenete egy parancsot, amely nincs hozzárendelve egy változót egy paraméter értékét a kimenet.

Például a következő parancsot kellene kimeneti az aktuális dátumot.

Például a következő parancsot kellene kimeneti az aktuális dátumot.

```powershell-interactive
Get-Date
```

A következő parancsok hozhat létre egy karakterláncot, az aktuális dátumot, és rendelje hozzá egy változóhoz. A változó tartalmát majd érkeznek a kimenet

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Az alábbi parancsokat az aktuális dátumot értékeli, és a egy karakterlánc, amely az aktuális nap-e a hétvégi vagy a hét napjának visszaadása.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Tevékenység kimenete

A runbook egy előző tevékenység kimenetét használja, használja a következő szintaxissal $ActivityOutput változó.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Például előfordulhat, hogy rendelkezik olyan tulajdonságot, amely szükség van a virtuális gép tevékenységet ebben az esetben használhatja a következő kifejezést:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

A tulajdonság, amely a virtuális gép szükséges objektum helyett csak egy tulajdonságot, ha Ön a következő szintaxis használatával a teljes objektumot lenne visszaadása.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Egy tevékenység kimenetét egy összetett kifejezésben például a következő szöveget, a virtuális gép neve összefűző is használható.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Feltételek

Használat [összehasonlító operátorok](https://technet.microsoft.com/library/hh847759.aspx) értékek összehasonlítása, vagy felfedheti, ha a egy értéke megegyezik-e a megadott mintával. Összehasonlítás $true vagy $false értéket ad vissza.

Például a következő feltételt határozza meg, hogy e tevékenység a virtuális gép nevű *Get-AzureVM* jelenleg *leállt*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

Az alábbi feltételeket ellenőrzi, hogy ugyanaz a virtuális gép állapotban van, minden más, *leállt*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Használatával több feltételt is összekapcsolhat egy [logikai operátor](https://technet.microsoft.com/library/hh847789.aspx) például **- és** vagy **- vagy**. Ha például a a következő feltétel ellenőrzések ugyanahhoz a virtuális géphez, az előző példában az olyan állapotban van-e *leállt* vagy *leállítása*.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Kivonattáblák

[Kivonattáblák](https://technet.microsoft.com/library/hh847780.aspx) név/érték párok, melyek egy értékhalmazt visszaadó vannak. Az egyes tevékenységek tulajdonságok előfordulhat, hogy várhatóan egy kivonattáblát egy egyszerű érték helyett. Mint egy szótárt néven kivonattábla is megjelenhetnek.

A következő szintaxissal létrehozni egy kivonattáblát. Egy kivonattáblát tetszőleges számú bejegyzést tartalmazhat, de egyes határozza meg egy nevet és egy értéket.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Például a következő kifejezést hoz létre egy kivonattáblát a az adatforrás egy tevékenység-paraméter várt egy kivonattáblát egy internetes keresés értékekkel használható.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Az alábbi példában egy úgynevezett tevékenység kimenetének *Twitter-kapcsolat beszerzése* egy kivonattáblát feltöltéséhez.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>További lépések

* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* Megtudhatja, hogyan az Automation futtató fiók használatával történő hitelesítéshez, tekintse meg a [konfigurálása Azure futtató fiók](automation-sec-configure-azure-runas-account.md)
