---
title: Grafikus szerzői műveletek Azure Automation
description: A grafikus szerzői műveletek lehetővé teszik, hogy runbookok hozzon létre Azure Automation a kód használata nélkül. Ez a cikk bemutatja a grafikus szerzői műveletek és a grafikus runbook létrehozásának megkezdéséhez szükséges összes részlet bevezetését.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 8c1b864eb83a9ffb69c0cb532dc2061636010c60
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450749"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafikus szerzői műveletek Azure Automation

A grafikus készítés lehetővé teszi, hogy az alapul szolgáló Windows PowerShell-vagy PowerShell munkafolyamat-kód bonyolultsága nélkül hozza létre a Azure Automation runbookok. A vászonhoz a parancsmagok és a runbookok könyvtárában adhat hozzá tevékenységeket, összekapcsolhatja őket, és konfigurálhatja a munkafolyamatok kialakítását. Ha valaha is dolgozott a System Center Orchestrator vagy a Service Management Automation (SMA) használatával, akkor ez ismerősnek tűnik

Ez a cikk bevezetést nyújt a grafikus létrehozással és a grafikus runbook létrehozásához szükséges fogalmakkal.

## <a name="graphical-runbooks"></a>Grafikus runbookok

A Azure Automation összes runbookok Windows PowerShell-munkafolyamatok. A grafikus és a grafikus PowerShell-munkafolyamat runbookok az Automation-feldolgozók által futtatott PowerShell-kódot hoz, de nem tudja megtekinteni vagy közvetlenül módosítani. Grafikus runbook alakíthatók át grafikus PowerShell-munkafolyamat runbook, és fordítva, de nem alakíthatók át szöveges runbook. Egy meglévő szöveges runbook nem importálható a grafikus szerkesztőbe.

## <a name="overview-of-graphical-editor"></a>A grafikus szerkesztő áttekintése

A Azure Portal grafikus szerkesztőjét grafikus runbook létrehozásával vagy szerkesztésével nyithatja meg.

![Grafikus munkaterület](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

A következő szakaszok a grafikus szerkesztőben lévő vezérlőket ismertetik.

### <a name="canvas"></a>Vászon

A vászon a runbook megtervezése. A könyvtár vezérlőelem csomópontjain tevékenységeket adhat hozzá a runbook, és összekapcsolhatja azokat hivatkozásokkal a runbook logikájának meghatározásához.

A vászon alján található vezérlők használatával nagyíthatja és kicsinyítheti a nagyítást.

### <a name="library-control"></a>Könyvtár-vezérlőelem

A könyvtár vezérlőelemben választhatja ki a runbook hozzáadni kívánt [tevékenységeket](#activities) . Hozzáadja azokat a vászonhoz, amelyhez más tevékenységekhez csatlakozik. Négy szakaszt tartalmaz a következő táblázatban:

| Section | Leírás |
|:--- |:--- |
| Parancsmagok |Tartalmazza az összes olyan parancsmagot, amely használható a runbook. A parancsmagok modul szerint vannak rendszerezve. Az Automation-fiókba telepített összes modul elérhető. |
| Runbookok |Az Automation-fiók runbookok tartalmazza. Ezek a runbookok hozzáadhatók a vászonhoz, hogy gyermek runbookok legyenek felhasználva. Csak a szerkesztett runbook azonos alaptípusú runbookok jelennek meg; a grafikus runbookok csak a PowerShell-alapú runbookok jelennek meg, míg a grafikus PowerShell-munkafolyamat runbookok csak a PowerShell-munkafolyamat-alapú runbookok láthatók. |
| Objektumok |Az Automation-fiókban található [Automation-eszközöket](/previous-versions/azure/dn939988(v=azure.100)) tartalmazza, amelyeket a runbook használhat. Ha hozzáad egy adategységet egy runbook, hozzáad egy munkafolyamat-tevékenységet, amely beolvassa a kijelölt objektumot. Változó eszközök esetén kiválaszthatja, hogy szeretne-e tevékenységet hozzáadni a változóhoz, vagy beállítja a változót. |
| Runbook-vezérlő |Magában foglalja az aktuális runbook használható runbook-vezérlési tevékenységeket is. Egy *elágazás* több bemenetet is igénybe vesz, és addig vár, amíg az összes befejeződött, mielőtt folytatná a munkafolyamatot. A *kód* tevékenység a grafikus runbook függően a PowerShell-vagy a PowerShell-munkafolyamat kódjának egy vagy több sorát futtatja. Ezt a tevékenységet egyéni kódokhoz vagy olyan funkciókhoz használhatja, amelyeket nehéz elérni más tevékenységekkel. |

### <a name="configuration-control"></a>Konfiguráció vezérlő

A konfiguráció vezérlőelemben megadhatja a vásznon kiválasztott objektum részleteit. Az ebben a vezérlőben elérhető tulajdonságok a kiválasztott objektum típusától függenek. Amikor kiválaszt egy beállítást a konfiguráció vezérlőelemben, további lapokat nyit meg a további információk megadásához.

### <a name="test-control"></a>Tesztelési vezérlő

A tesztelési vezérlő nem jelenik meg a grafikus szerkesztő első elindításakor. Akkor nyílik meg, amikor interaktívan [tesztel egy grafikus runbook](#graphical-runbook-procedures).

## <a name="graphical-runbook-procedures"></a>Grafikus runbook eljárások

### <a name="exporting-and-importing-a-graphical-runbook"></a>Grafikus runbook exportálása és importálása

Csak grafikus runbook közzétett verzióját exportálhatja. Ha a runbook még nem tették közzé, az **Exportálás** gomb le lesz tiltva. Az **Exportálás** gombra kattintva a rendszer letölti a runbook a helyi számítógépre. A fájl neve megegyezik a runbook *graphrunbook* -bővítménnyel rendelkező nevével.

Egy grafikus vagy grafikus PowerShell munkafolyamat runbook-fájl importálásához válassza az **Importálás** lehetőséget a runbook hozzáadásakor. Ha kijelöli az importálandó fájlt, megtarthatja ugyanazt a **nevet** , vagy megadhat egy újat. A Runbook típusa mező megjeleníti a Runbook típusát, miután kivizsgálta a kiválasztott fájlt, és ha megpróbál kijelölni egy másik helytelen típust, akkor egy üzenet jelenik meg, amely a lehetséges ütközések és az átalakítás során nem megfelelő. hibák.

![Runbook importálása](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>Grafikus runbook tesztelése

Tesztelheti egy runbook piszkozatának verzióját a Azure Portalban, miközben a runbook közzétett verzióját változatlanul hagyja, vagy egy új runbook is tesztelheti a közzététel előtt. Ezzel leellenőrizheti, hogy a runbook megfelelően működik-e, mielőtt lecseréli a közzétett verziót. A runbook tesztelésekor a program a runbook vázlatát hajtja végre, és minden elvégzett műveletet végrehajt. Nem jön létre a feladatütemezés, de a kimenet a teszt kimenet ablaktáblán jelenik meg.

Nyissa meg a runbook tesztelési vezérlőjét úgy, hogy megnyitja a runbook a szerkesztéshez, majd kattintson a **teszt panel** gombra.

A teszt vezérlőelem bármilyen bemeneti paramétert kér, és a **Start** gombra kattintva elindíthatja a runbook.

### <a name="publishing-a-graphical-runbook"></a>Grafikus runbook közzététele

Azure Automation minden runbook van egy Piszkozat és egy közzétett verzió. Kizárólag a Közzétett verziót lehet futtatni, és kizárólag a Piszkozat verzió szerkeszthető. A Közzétett verzióra nincsenek hatással a Piszkozat verzióban végrehajtott módosítások. Ha a Piszkozat verziója készen áll a rendelkezésre állásra, tegye közzé, amely felülírja a közzétett verziót a Piszkozat verziójával.

Grafikus runbook úgy teheti közzé, hogy megnyitja a runbook a szerkesztéshez, majd a **Közzététel** gombra kattint.

Ha egy runbook még nem tették közzé, az állapota **új**. Közzétételkor a **közzétett állapota közzétételre**kerül. Ha a közzététel után szerkeszti a runbook, és a Piszkozat és a közzétett verziók eltérnek, a runbook állapota **Szerkesztés**.

![Runbook-állapotok](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Lehetősége van arra is, hogy visszaállítson egy runbook közzétett verzióját. Ez elveti a runbook legutóbbi közzététele óta történt módosításokat, és lecseréli a runbook Piszkozat verzióját a közzétett verzióra.

## <a name="activities"></a>Activities (Tevékenységek)

A tevékenységek a runbook építőelemei. Egy tevékenység lehet PowerShell-parancsmag, gyermek-runbook vagy munkafolyamat-tevékenység. Vegyen fel egy tevékenységet a runbook. ehhez kattintson a jobb gombbal a könyvtár vezérlőelemre, és válassza a **Hozzáadás a vászonhoz**lehetőséget. Ezután rákattinthat a tevékenységre, és húzással bárhová elhelyezheti azt a vászonon, amelyet szeretne. A tevékenység helye a vásznon nem befolyásolja semmilyen módon a runbook működését. Kiderítheti a runbook, de úgy találja, hogy a legjobban alkalmas a művelet megjelenítésére.

![Hozzáadás a vászonhoz](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Válassza ki a tevékenységet a vásznon a tulajdonságok és paraméterek konfigurálásához a konfiguráció panelen. Megváltoztathatja a tevékenység **címkéjét** olyanra, amely leíró jellegű. Az eredeti parancsmag továbbra is fut, egyszerűen megváltoztatja a grafikus szerkesztőben használt megjelenítendő nevet. A címkének egyedinek kell lennie a runbook belül.

### <a name="parameter-sets"></a>Paraméterek készletei

A beállításhalmaz meghatározza azokat a kötelező és választható paramétereket, amelyek egy adott parancsmag értékeit fogadják el. Minden parancsmaghoz legalább egy paraméter van beállítva, néhány pedig több is. Ha egy parancsmagnak több paramétere is van, akkor a paraméterek konfigurálása előtt ki kell választania, hogy melyiket használja. A konfigurálható paraméterek a kiválasztott paramétertől függenek. A tevékenység által használt paramétereket megváltoztathatja a **set paraméter** kiválasztásával és egy másik készlet kijelölésével. Ebben az esetben a konfigurált paraméterek értékei elvesznek.

A következő példában a Get-AzureRmVM parancsmag három paraméter-készlettel rendelkezik. A paraméterek értékét nem lehet konfigurálni, amíg ki nem választja az egyik paramétert. A ListVirtualMachineInResourceGroupParamSet paraméter úgy van beállítva, hogy egy erőforráscsoport összes virtuális gépét visszaadja, és egyetlen opcionális paraméterrel rendelkezik. A **GetVirtualMachineInResourceGroupParamSet** a visszaadni kívánt virtuális gép megadására szolgál, és két kötelező és egy választható paramétert tartalmaz.

![Paraméter beállítva](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Paraméterértékek

Egy paraméter értékének megadásakor ki kell választania egy adatforrást, amely meghatározza, hogy az érték hogyan legyen megadva. Az adott paraméter számára elérhető adatforrások az adott paraméter érvényes értékeitől függenek. A Null érték például nem érhető el egy olyan paraméter számára, amely nem engedélyezi null értékek használatát.

| Adatforrás | Leírás |
|:--- |:--- |
| Konstans érték |Adja meg a paraméter értékét. Ez csak a következő adattípusokhoz érhető el: Int32, Int64, string, Boolean, DateTime, Switch. |
| Tevékenység kimenete |A munkafolyamat aktuális tevékenységét megelőző tevékenység kimenete. Minden érvényes tevékenység megjelenik. Válassza ki azt a tevékenységet, amelynek a kimenetét a paraméter értékeként szeretné használni. Ha a tevékenység több tulajdonsággal rendelkező objektumot ad eredményül, a tevékenység kiválasztása után beírhatja a tulajdonság nevét. |
| Runbook bemenete |Válasszon egy runbook bemeneti paramétert a tevékenység paraméter bemenetként. |
| Változó eszköz |Válassza ki az Automation változót bemenetként. |
| Hitelesítőadat-eszköz |Válassza ki az Automation hitelesítő adatait bemenetként. |
| Tanúsítvány-eszköz |Válassza ki az Automation-tanúsítványt bemenetként. |
| A kapcsolatfelvételi eszköz |Válassza ki az Automation-kapcsolatokat bemenetként. |
| PowerShell-kifejezés |Adja meg az egyszerű [PowerShell-kifejezést](#powershell-expressions). A kifejezés kiértékelése a tevékenység és a paraméter értékéhez használt eredmény előtt történik. A változók használatával hivatkozhat egy tevékenység kimenetére vagy egy runbook bemeneti paraméterre. |
| Nincs konfigurálva |Törli a korábban konfigurált értékeket. |

#### <a name="optional-additional-parameters"></a>További választható paraméterek

Minden parancsmagnál lehetőség van további paraméterek megadására. Ezek a PowerShell gyakori paramétereinek vagy más egyéni paraméterek. Megjelenik egy olyan szövegmező, amelyen a PowerShell-szintaxis használatával adhat meg paramétereket. A **részletes** Common paraméter használatához például a **"-verbose: $true"** értéket kell megadnia.

### <a name="retry-activity"></a>Újrapróbálkozási tevékenység

Az **újrapróbálkozási viselkedés** lehetővé teszi, hogy egy tevékenység többször is fusson, amíg egy adott feltétel nem teljesül, hasonlóan a hurokhoz. Ezt a funkciót olyan tevékenységekhez használhatja, amelyeknek többször is futniuk kell, a hibák hajlamosak, és több próbálkozásra is szükség lehet a sikeres művelethez, vagy tesztelni kell a tevékenység kimeneti információit az érvényes adatokhoz.

Ha engedélyezi az újrapróbálkozást egy tevékenységhez, beállíthatja a késést és a feltételt. A késleltetés az az idő (másodpercben vagy percben mérve), ameddig a runbook megvárja a tevékenység ismételt futtatása előtt. Ha nincs késleltetés megadva, a tevékenység a befejeződése után azonnal elindul.

![Tevékenység újrapróbálkozási késleltetése](media/automation-graphical-authoring-intro/retry-delay.png)

Az újrapróbálkozási feltétel egy PowerShell-kifejezés, amelyet a rendszer a tevékenység futtatásakor értékel ki. Ha a kifejezés igaz értéket ad vissza, akkor a tevékenység újra lefut. Ha a kifejezés feloldja a hamis értéket, akkor a tevékenység nem fut újra, és a runbook a következő tevékenységre lép.

![Tevékenység újrapróbálkozási késleltetése](media/automation-graphical-authoring-intro/retry-condition.png)

Az újrapróbálkozási feltétel használhat egy $RetryData nevű változót, amely hozzáférést biztosít a tevékenység újrapróbálkozásával kapcsolatos információkhoz. Ez a változó a következő táblázatban található tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| NumberOfAttempts |A tevékenység futási idejének száma. |
| Kimenet |A tevékenység utolsó futtatásának kimenete. |
| TotalDuration |A tevékenység első elindítása óta eltelt idő. |
| StartedAt |A tevékenység első elindításakor UTC formátumban. |

A következő példák a tevékenység újrapróbálkozási feltételeit mutatják be.

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

Egy tevékenység újrapróbálkozási feltételének konfigurálása után a tevékenység két vizuális mutatót tartalmaz, amelyekkel emlékeztetheti Önt. Az egyik a tevékenységben jelenik meg, a másik pedig a tevékenység konfigurációjának áttekintése.

![Tevékenység-újrapróbálkozások vizuális indikátorai](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Munkafolyamat-parancsfájl vezérlő

A kód vezérlőelem olyan speciális tevékenység, amely a létrehozott grafikus runbook függően a PowerShell vagy a PowerShell munkafolyamat-parancsfájlt fogadja el, hogy olyan funkciókat biztosítson, amelyek egyébként nem érhetők el. A paraméterek nem fogadhatók el, de változókat is használhatnak a tevékenységek kimenetére és a runbook bemeneti paramétereinek használatára. A tevékenység bármilyen kimenete hozzáadódik a adatbuszba, kivéve, ha nincs kimenő hivatkozása, amely esetben a rendszer hozzáadja a runbook kimenetéhez.

A következő kód például egy $NumberOfDays nevű runbook bemeneti változó használatával végez dátum-számításokat. Ezután elküld egy számított dátum időpontot, mint kimenetként, amelyet a következő tevékenységek fognak használni a runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Hivatkozások és munkafolyamat

A grafikus runbook található **hivatkozások** két tevékenységet kapcsolnak össze. A vászonon a forrás tevékenységtől a cél tevékenységig mutató nyíl jelenik meg. A tevékenységek a nyíl irányában futnak a célként megadott tevékenységgel a forrás tevékenység befejeződése után.

### <a name="create-a-link"></a>Hivatkozás létrehozása

Hozzon létre egy kapcsolatot két tevékenység között úgy, hogy kiválasztja a forrás tevékenységet, majd rákattint az alakzat alján található körre. Húzza a nyilat a cél tevékenységre, és adja meg a kiadást.

![Hivatkozás létrehozása](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Válassza ki a hivatkozást a tulajdonságainak konfigurálásához a konfiguráció panelen. Ez magában foglalja a hivatkozás típusát is, amelyet a következő táblázat ismertet:

| Hivatkozás típusa | Leírás |
|:--- |:--- |
| Folyamat |A célként megadott tevékenység a forrás tevékenység minden objektumának kimenetén egyszer fut. A célként megadott tevékenység nem fut, ha a forrásoldali tevékenység nem eredményez kimenetet. A forrás tevékenység kimenete objektumként érhető el. |
| Sorozat |A célként megadott tevékenység csak egyszer fut le. A forrás tevékenységből származó objektumok tömbjét kapja meg. A forrás tevékenység kimenete objektumok tömbje érhető el. |

### <a name="starting-activity"></a>Kezdési tevékenység

A grafikus runbook minden olyan tevékenységgel elindul, amely nem rendelkezik bejövő hivatkozással. Ez gyakran csak egy tevékenység, amely a runbook kezdési tevékenységként fog működni. Ha több tevékenységnek nincs bejövő hivatkozása, akkor a runbook párhuzamosan futtatja őket. Az alábbi hivatkozásokat követve más tevékenységek futtatását hajtja végre.

### <a name="conditions"></a>Feltételek

Ha egy hivatkozásnál megad egy feltételt, a célként megadott tevékenység csak akkor fut, ha a feltétel igaz értékre van feloldva. Általában egy $ActivityOutput változót használ a feltételben a kimenet lekéréséhez a forrás tevékenységből

Egy folyamat hivatkozásakor meg kell adnia egy adott objektum feltételeit, és a rendszer kiértékeli a feltételt a forrás tevékenység minden objektum kimenete esetében. A rendszer ezután futtatja a célként megadott tevékenységet minden olyan objektumnál, amely megfelel a feltételnek. Ha például a Get-AzureRmVm forrás tevékenységgel rendelkezik, a következő szintaxis használható egy feltételes folyamatra mutató hivatkozáshoz, hogy csak a *Group1*nevű erőforráscsoport virtuális gépei legyenek lekérdezve.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

A sorozatokra mutató hivatkozás esetén a rendszer csak egyszer értékeli ki a feltételt, mivel a rendszer egyetlen tömböt ad vissza, amely az összes objektum kimenetét tartalmazza a forrás tevékenységből. Emiatt nem használhatók a sorozatokra mutató hivatkozások olyan szűréshez, mint például egy folyamat hivatkozása, de egyszerűen eldöntheti, hogy fut-e a következő tevékenység. Tegyük fel például, hogy a következő tevékenységek jelennek meg a Start VM-runbook.

![Feltételes hivatkozás szakaszokkal](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Három különböző sorozat-hivatkozás található a virtuális gép nevét és az erőforráscsoport nevét képviselő két runbook bemeneti paraméter számára, hogy megállapítsa, melyik a megfelelő művelet egy adott virtuális gép elindításához, majd indítsa el az erőforrásban lévő összes virtuális gépet. Csoport vagy az előfizetésben lévő összes virtuális gép. A csatlakozás az Azure-hoz és egyetlen virtuális gép lekérése között a következő feltétel logikája:

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

Ha feltételes hivatkozást használ, a forrás tevékenységtől az adott ág más tevékenységeire elérhető adatok a feltétel szerint vannak szűrve. Ha egy tevékenység több hivatkozás forrása, akkor az egyes ágakban található tevékenységek számára elérhető adatok az adott ágra mutató hivatkozás állapotától függenek.

Például az alábbi runbook lévő **Start-AzureRmVm** tevékenység elindítja az összes virtuális gépet. Két feltételes hivatkozással rendelkezik. Az első feltételes hivatkozás a következő kifejezést használja: *$ActivityOutput ["Start-AzureRmVM"]. IsSuccessStatusCode – EQ $true* annak szűrésére, hogy a Start-AzureRmVm tevékenység sikeresen befejeződött-e. A második a következő kifejezést használja: *$ActivityOutput ["Start-AzureRmVM"]. IsSuccessStatusCode – ne $true* szűrni, ha a Start-AzureRmVm tevékenység nem tudta elindítani a virtuális gépet.

![Feltételes hivatkozás – példa](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Minden olyan tevékenység, amely az első hivatkozást követi, és a Get-AzureVM tevékenység kimenetét használja, csak azokat a virtuális gépeket fogja kapni, amelyeket a Get-AzureVM futtatásakor indítottak el. A második hivatkozást követő tevékenységek csak azokat a virtuális gépeket kapják meg, amelyek a Get-AzureVM futtatásának időpontjában leálltak. A harmadik hivatkozás utáni bármely tevékenység lekéri az összes virtuális gépet a futó állapottól függetlenül.

### <a name="junctions"></a>Csomópontok

A Junction egy különleges tevékenység, amely addig vár, amíg az összes bejövő ág be nem fejeződik. Ez lehetővé teszi, hogy egyszerre több tevékenységet futtasson, és győződjön meg arról, hogy az összes elkészült, mielőtt továbblép.

Míg a csomópontok korlátlan számú bejövő kapcsolattal rendelkezhetnek, a kapcsolatok közül nem több lehet egy folyamat. A bejövő szekvenciális hivatkozások száma nincs korlátozva. A csomópontot több bejövő folyamattal rendelkező kapcsolattal is létrehozhatja, és mentheti a runbook, de a futtatása meghiúsul.

Az alábbi példa egy olyan runbook részét képezi, amely virtuális gépeket indít el, miközben a javítások letöltését is alkalmazza az adott gépekre. A rendszer egy elágazást használ annak biztosítására, hogy mindkét folyamat befejeződik, mielőtt a runbook folytatja.

![Szinkronizációs pont](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciklusok

A ciklus olyankor történik, amikor egy cél tevékenység visszakapcsolódik a forrás tevékenységéhez, vagy egy másik tevékenységhez, amely végül a forráshoz kapcsolódik. A ciklusok jelenleg nem engedélyezettek grafikus szerzői műveletekben. Ha a runbook ciklust tartalmaz, a rendszer megfelelően menti, de hibaüzenetet kap a futtatásakor.

![Ciklikus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>Az adatmegosztás tevékenységek között

A kimenő kapcsolattal rendelkező tevékenységek által kimeneti adatokat a rendszer a runbook *adatbuszba* írja. A runbook bármely tevékenysége a adatbuszba lévő adatok használatával feltöltheti a paramétereket, vagy belefoglalhatja a szkript kódjába. Egy tevékenység elérheti a munkafolyamatban lévő korábbi tevékenységek kimenetét.

Az adatok adatbuszba való írásának módja a tevékenységre mutató hivatkozás típusától függ. Egy **folyamat**esetében az adatokat a rendszer több objektumként adja eredményként. A **sorozatokra** mutató hivatkozás esetén az adatokat tömbként adja eredményként. Ha csak egy érték van, a kimenet egyetlen elem tömbként történik.

A adatbuszba lévő adatai a következő két módszer egyikével érhetők el. Az első egy **tevékenység-kimeneti** adatforrást használ egy másik tevékenység paramétereinek feltöltéséhez. Ha a kimenet egy objektum, egyetlen tulajdonságot is megadhat.

![Tevékenység kimenete](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Egy tevékenység kimenetét egy **PowerShell-kifejezés** adatforrásában vagy egy ActivityOutput változóval rendelkező **munkafolyamat-parancsfájl** tevékenységből is lekérheti. Ha a kimenet egy objektum, egyetlen tulajdonságot is megadhat. A ActivityOutput változók a következő szintaxist használják.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Ellenőrzőpontok

Az [ellenőrzőpontokat](automation-powershell-workflow.md#checkpoints) grafikus PowerShell-munkafolyamatok runbook is beállíthatja, ha az *ellenőrzőpont runbook* lehetőséget választja bármely tevékenységen. Ez azt eredményezi, hogy a tevékenység futtatása után ellenőrzőpontot kell beállítani.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Az ellenőrzőpontok csak grafikus PowerShell-munkafolyamatok runbookok engedélyezettek, grafikus runbookok nem érhetők el. Ha a runbook Azure-parancsmagokat használ, akkor a runbook felfüggesztése után minden olyan ellenőrzőponttal ellátott tevékenységet el kell végeznie, amely egy másik munkavégző esetében a AzureRmAccount.

## <a name="authenticating-to-azure-resources"></a>Hitelesítés az Azure-erőforrásokkal

Az Azure-erőforrásokat kezelő Azure Automation runbookok az Azure-ba történő hitelesítést igénylik. A [futtató fiók](automation-create-runas-account.md) (más néven szolgáltatásnév) az alapértelmezett módszer az előfizetésben lévő Azure Resource Manager erőforrások elérésére az Automation runbookok. Ezt a funkciót grafikus runbook is hozzáadhatja úgy, hogy hozzáadja a **azurerunasconnection elemet** -kapcsolati objektumot, amely a PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) parancsmagot használja, és [összeköti az AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot a vászonhoz. Ezt a következő példa szemlélteti:

![Futtató hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A lekéréses futtató tevékenység (azaz a Get-AutomationConnection) a Azurerunasconnection elemet nevű állandó értékű adatforrással van konfigurálva.

![Futtató kapcsolatok konfigurációja](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A következő tevékenység, a AzureRmAccount, hozzáadja a hitelesített futtató fiókot a runbook való használathoz.

![A AzureRmAccount paraméter beállítása](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

> [!IMPORTANT]
> A **Add-AzureRmAccount** mostantól egy alias a **kapcsolat-AzureRmAccount**. Ha nem látja a **AzureRMAccount**, használhatja a **AzureRMAccount**, vagy frissítheti a modulokat az Automation-fiókban.

A **APPLICATIONID**, a **CERTIFICATETHUMBPRINT**és a **TENANTID** paramétereknél meg kell adnia a mező elérési útjához tartozó tulajdonság nevét, mivel a tevékenység több tulajdonsággal rendelkező objektumot ad eredményül. Ha a runbook hajtja végre, a hitelesítés sikertelen lesz. Legalább a futtató fiókkal kell hitelesítenie a runbook.

Ha az Azure [ad-felhasználói fiókkal](automation-create-aduser-account.md) rendelkező Automation-fiókot használó előfizetőknek visszamenőleges kompatibilitást szeretne fenntartani az Azure klasszikus üzembe helyezésének vagy Azure Resource Manager erőforrásainak kezeléséhez, a hitelesítési módszer az Add-AzureAccount parancsmag [, amely az](automation-credentials.md) Azure-fiókhoz hozzáféréssel rendelkező Active Directory felhasználót jelöl.

Ezt a funkciót grafikus runbook is hozzáadhatja, ha egy hitelesítőadat-objektumot ad hozzá a vászonhoz, majd egy add-AzureAccount tevékenységet. Az Add-AzureAccount a hitelesítő adatokat használja a beviteléhez. Ezt a következő példa szemlélteti:

![Hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authentication-activities.png)

A runbook elején és minden ellenőrzőpont után hitelesítenie kell magát. Ez azt jelenti, hogy az ellenőrzőpont-munkafolyamatok tevékenysége után hozzáad egy kiegészítő AzureAccount tevékenységet. Nincs szükség további hitelesítő adatokra, mert ugyanazokat a műveleteket használhatja

![Tevékenység kimenete](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook-bemenet és-kimenet

### <a name="runbook-input"></a>Runbook bemenete

Előfordulhat, hogy egy runbook a felhasználótól a Azure Portal vagy egy másik runbook keresztül indítja el a runbook, ha az aktuális alkalmazást gyermekként használják.
Ha például olyan runbook rendelkezik, amely létrehoz egy virtuális gépet, előfordulhat, hogy olyan információkat kell megadnia, mint például a virtuális gép neve és egyéb tulajdonságok, amikor elindítja a runbook.

A runbook bevitelét egy vagy több bemeneti paraméter definiálásával fogadhatja el. Ezeket a paramétereket a runbook minden indításakor megadja. Amikor elindít egy runbook a Azure Portal, megkéri, hogy adja meg az egyes runbook bemeneti paramétereinek értékeit.

A runbook bemeneti paramétereit a runbook eszköztár **bemenet és kimenet** gombjára kattintva érheti el.

Ekkor megnyílik a **bemeneti és kimeneti** vezérlő, ahol szerkesztheti a meglévő bemeneti paramétereket, vagy létrehozhat egy újat a **bemenet hozzáadása**lehetőségre kattintva.

![Bemenet hozzáadása](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Az egyes bemeneti paramétereket az alábbi táblázat tulajdonságai határozzák meg:

| Tulajdonság | Leírás |
|:--- |:--- |
| Név |A paraméter egyedi neve. A művelet csak alfanumerikus karaktereket tartalmazhat, és nem tartalmazhat szóközt. |
| Leírás |A bemeneti paraméter opcionális leírása. |
| Type (Típus) |A paraméter értékének várt adattípus. A Azure Portal minden paraméter esetében megfelelő vezérlést biztosít az adattípushoz, amikor rákérdez a bemenetre. |
| Kötelező |Megadja, hogy meg kell-e adni egy értéket a paraméterhez. A runbook nem indítható el, ha nem ad meg értéket minden olyan kötelező paraméterhez, amelyhez nincs megadva alapértelmezett érték. |
| Alapértelmezett érték |Megadja, hogy a paraméterhez milyen értéket kell használni, ha az egyik nincs megadva. Ez lehet null vagy egy adott érték. |

### <a name="runbook-output"></a>Forgatókönyv kimenete

A kimenő hivatkozással nem rendelkező tevékenységek által létrehozott adatokat a rendszer a [runbook kimenetére](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)menti. A rendszer menti a kimenetet a runbook-feladatokkal, és a szülő runbook számára elérhető, amikor a runbook gyermekként használják.

## <a name="powershell-expressions"></a>PowerShell-kifejezések

A grafikus szerzői műveletek egyik előnye, hogy lehetővé teszi, hogy runbook hozzon létre a PowerShell minimális ismeretével. Jelenleg ismernie kell egy kicsit a PowerShellt, hogy bizonyos [Paraméterek](#activities) kitöltése és a [kapcsolati feltételek beállítása megtörténjen](#links-and-workflow). Ez a szakasz a PowerShell-kifejezések gyors bevezetését ismerteti azon felhasználók számára, akik esetleg nem ismerik. A PowerShell részletes adatai a [Windows PowerShell-lel való parancsfájlkezelésben](https://technet.microsoft.com/library/bb978526.aspx)érhetők el.

### <a name="powershell-expression-data-source"></a>PowerShell-kifejezés adatforrása
A PowerShell-kifejezéseket adatforrásként használva feltöltheti egy [tevékenység-paraméter](#activities) értékét egy PowerShell-kód eredményeivel. Ez lehet egyetlen sornyi kód, amely néhány egyszerű funkciót vagy több olyan sort hajt végre, amelyek valamilyen összetett logikát végeznek. Egy olyan parancs kimenete, amely nincs változóhoz rendelve, kimenetet ad a paraméter értékének.

A következő parancs például az aktuális dátumot adja eredményül.

A következő parancs például az aktuális dátumot adja eredményül.

```powershell-interactive
Get-Date
```

Az alábbi parancsok létrehoznak egy karakterláncot az aktuális dátumból, és hozzárendelik egy változóhoz. A rendszer ezután elküldi a változó tartalmát a kimenetnek.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

A következő parancsok értékelik ki az aktuális dátumot, és egy olyan karakterláncot adnak vissza, amely azt jelzi, hogy az aktuális nap hétvége vagy hétköznap.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Tevékenység kimenete

Ha a runbook egy korábbi tevékenység kimenetét szeretné használni, használja a $ActivityOutput változót a következő szintaxissal.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Előfordulhat például, hogy egy olyan tevékenységgel rendelkezik, amely egy virtuális gép nevét igényli, amely esetben a következő kifejezést használhatja:

```powershell-interactive
$ActivityOutput['Get-AzureVm'].Name
```

Ha a tulajdonság, amely nem csak egy tulajdonság helyett a virtuálisgép-objektumot követelte meg, akkor a teljes objektumot a következő szintaxissal kell visszaadnia.

```powershell-interactive
$ActivityOutput['Get-AzureVm']
```

Egy tevékenység kimenetét egy összetettebb kifejezésben is használhatja, például a következővel, amely összefűzi a szöveget a virtuális gép nevével.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVm'].Name
```

### <a name="conditions"></a>Feltételek

Az [összehasonlító operátorok](https://technet.microsoft.com/library/hh847759.aspx) segítségével hasonlíthatja össze az értékeket, vagy meghatározhatja, hogy egy érték megfelel-e a megadott mintának. Az összehasonlítás $true vagy $false értékét adja vissza.

A következő feltétel például meghatározza, hogy a *Get-AzureVM* nevű tevékenységből származó virtuális gép jelenleg le van-e *állítva*.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A következő feltétel ellenőrzi, hogy ugyanaz a virtuális gép a *leállítotttól*eltérő állapotban van-e.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

Több feltételt is csatlakoztathat egy [logikai operátor](https://technet.microsoft.com/library/hh847789.aspx) , például a **-és** vagy a **-vagy**a használatával. A következő feltétel például ellenőrzi, hogy az előző példában szereplő virtuális gép *leállított* vagy *leállítási*állapotban van-e.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Szórótáblában

A [szórótáblában](https://technet.microsoft.com/library/hh847780.aspx) olyan név/érték párok, amelyek értékek halmazának visszaadására használhatók. Bizonyos tevékenységek tulajdonságai egy egyszerű érték helyett egy szórótábla számíthatnak. A szórótábla néven is látható.

Létre kell hoznia egy szórótábla a következő szintaxissal. A szórótábla tetszőleges számú bejegyzést tartalmazhatnak, de mindegyiket névvel és értékkel határozzák meg.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

A következő kifejezés például létrehoz egy szórótábla az adatforrásban egy olyan tevékenység-paraméterhez, amely egy internetes keresés értékeit várta egy szórótábla.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Az alábbi példa a *Get Twitter-kapcsolatok* nevű tevékenység kimenetét használja egy szórótábla feltöltéséhez.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="next-steps"></a>Következő lépések

* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* Az Automation futtató fiók használatával végzett hitelesítés megismeréséhez lásd az Azure-beli [futtató fiók konfigurálása](automation-sec-configure-azure-runas-account.md) című témakört.
