---
title: Grafikus szerzői jog az Azure Automationben
description: A grafikus szerzői lehetővé teszi, hogy runbookokat hozzon létre az Azure Automation számára anélkül, hogy kódokkal dolgozna. Ez a cikk bemutatja a grafikus szerzői és a grafikus runbook létrehozásának megkezdéséhez szükséges összes részletet.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: cf8ced05066923c94e80628651d8983560601d69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406054"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafikus szerzői jog az Azure Automationben

A grafikus szerzői lehetővé teszi, hogy runbookokat hozzon létre az Azure Automation számára az alapul szolgáló Windows PowerShell- vagy PowerShell-munkafolyamat-kód bonyolultsága nélkül. Tevékenységeket adhat a vászonhoz parancsmagok és runbookok könyvtárából, összekapcsolhatja őket, és munkafolyamat-létrehozásra konfigurálhatja őket. Ha valaha is dolgozott a System Center Orchestrator vagy service Management Automation (SMA), grafikus authoring ismerősnek kell lennie. Ez a cikk bemutatja a grafikus runbook létrehozásának megkezdéséhez szükséges fogalmakat.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="graphical-runbooks"></a>Grafikus runbookok

Az Azure Automation összes runbookja Windows PowerShell-munkafolyamat. Grafikus runbookok és grafikus PowerShell-munkafolyamat runbookok powershell-kódot hoz létre, amelyet az Automation-dolgozók futtatnak, de amelyeket nem tekinthet meg vagy módosíthat. A grafikus runbookok grafikus PowerShell-munkafolyamat-runbookká alakíthatók, és fordítva. Azonban ezek a runbookok nem konvertálhatók szöveges runbookokká. Emellett az Automation grafikus szerkesztő nem tud szöveges runbookot importálni.

## <a name="overview-of-graphical-editor"></a>A grafikus szerkesztő áttekintése

A grafikus szerkesztőt az Azure Portalon egy grafikus runbook létrehozásával vagy szerkesztésével nyithatja meg.

![Grafikus munkaterület](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

A következő szakaszok a grafikus szerkesztő vezérlőit ismertetik.

### <a name="canvas-control"></a>Vászon vezérlése

A Vászon vezérlő lehetővé teszi a runbook megtervezését. A könyvtárvezérlő csomópontjaiból tevékenységeket adhat hozzá a runbookhoz, és összekapcsolhatja őket a runbook logikájának meghatározásához. A vászon alján olyan vezérlők találhatók, amelyek lehetővé teszik a nagyítást és kicsinyítést.

### <a name="library-control"></a>Könyvtár vezérlése

A Könyvtár vezérlő lehetővé teszi, hogy válassza ki a runbookhoz hozzáadni kívánt [tevékenységeket.](#activities) Hozzáadhatja őket a vászonhoz, ahol más tevékenységekhez kapcsolhatja őket. A Könyvtár vezérlő az alábbi táblázatban definiált szakaszokat tartalmazza.

| Section | Leírás |
|:--- |:--- |
| Parancsmagok |A runbookban használható összes parancsmag. A parancsmagok modul szerint vannak rendezve. Az Automation-fiókban telepített összes modul elérhető. |
| Runbookok |Az Automation-fiók runbookjai. Ezeket a runbookokat hozzáadhatja a vászonhoz, hogy gyermek runbookokként legyenek használva. Csak a szerkesztett runbookokkal azonos típusú runbookok jelennek meg. Grafikus runbookok esetén csak a PowerShell-alapú runbookok jelennek meg. A grafikus PowerShell-munkafolyamat-runbookok esetében csak a PowerShell-munkafolyamat-alapú runbookok jelennek meg. |
| Objektumok |Az Automation-fiók [automatizálási eszközei,](/previous-versions/azure/dn939988(v=azure.100)) amelyek et a runbookban használhat. Egy eszköz hozzáadása a runbookhoz egy munkafolyamat-tevékenységet ad hozzá, amely leadja a kijelölt eszközt. Változó eszközök esetén kiválaszthatja, hogy hozzáad-e egy tevékenységet a változó lekérésevagy a változó beállítása. |
| Runbook-vezérlés |Az aktuális runbookban használható tevékenységek vezérlése. A Csomópont tevékenység több bemenetet vesz igénybe, és megvárja, amíg az összes befejeződött, mielőtt folytatná a munkafolyamatot. A kódtevékenység a grafikus runbook típusától függően a PowerShell- vagy PowerShell-munkafolyamat-kód egy vagy több sorát futtatja. Ezt a tevékenységet egyéni kódhoz vagy más tevékenységekkel nehezen elérhető funkciókhoz használhatja. |

### <a name="configuration-control"></a>Konfigurációvezérlés

A Konfiguráció vezérlő lehetővé teszi, hogy a vásznon kijelölt objektum adatait adja meg. A vezérlőben elérhető tulajdonságok a kijelölt objektum típusától függenek. Ha a Konfiguráció vezérlőben kiválaszt egy beállítást, további paneleket nyit meg, hogy további információkat nyújtson.

### <a name="test-control"></a>Vizsgálati vezérlő

A Teszt vezérlő nem jelenik meg a grafikus szerkesztő első indításakor. Akkor nyílik meg, amikor interaktívan tesztel egy grafikus runbookot.

## <a name="activities"></a>Tevékenységek

A tevékenységek a runbook okai. Egy tevékenység lehet PowerShell-parancsmag, gyermek runbook vagy munkafolyamat. A runbookhoz úgy vehet fel tevékenységet, hogy a jobb gombbal a Könyvtár vezérlőben a **Hozzáadás a vászonhoz**parancsra kattint. Ezután a tevékenységre kattintva és húzással elhelyezheti a kívánt helyre a vásznon. A tevékenység helye a vásznon nincs hatással a runbook működésére. A runbookot bármilyen módon eltudja végezni, hogy a működésének megjelenítésére a legmegfelelőbbnek találja.

![Hozzáadás a vászonhoz](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Válasszon ki egy tevékenységet a vásznon a tulajdonságok és paraméterek konfigurálásához a Konfiguráció panelen. A tevékenység címkéjét olyan névre módosíthatja, amelyet leírónak talál. A runbook továbbra is futtatja az eredeti parancsmacs. Egyszerűen megváltoztatja a grafikus szerkesztő által használt megjelenítendő nevet. Vegye figyelembe, hogy a címkének egyedinek kell lennie a runbookon belül.

### <a name="parameter-sets"></a>Paraméterkészletek

A paraméterkészlet azokat a kötelező és választható paramétereket határozza meg, amelyek egy adott parancsmag értékeit fogadják el. Minden parancsmaghoz legalább egy paraméter készlet tartozik, és néhányhoz több készlet tartozik. Ha egy parancsmag több paraméterkészlettel rendelkezik, a paraméterek konfigurálása előtt ki kell jelölnie a használni kívánt paramétert. A tevékenység által használt paraméterkészletet a Paraméterkészlet és egy másik készlet kiválasztásával **módosíthatja.** Ebben az esetben a már konfigurált paraméterértékek elvesznek.

A következő példában a [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) parancsmag három paraméterkészlettel rendelkezik. A példa egy **ListVirtualMachineInResourceGroupParamSet**nevű készletet használ, egyetlen választható paraméterrel, az erőforráscsoport összes virtuális gépének visszaadásához. A példa a **GetVirtualMachineInResourceGroupParamSet** paraméterkészletet is használja a visszaadandó virtuális gép megadásához. Ez a készlet két kötelező paraméterrel és egy választható paraméterrel rendelkezik.

![Paraméterkészlet](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Paraméterértékek

Amikor értéket ad meg egy paraméterhez, egy adatforrást jelöl ki az érték megadásának meghatározásához. Az adott paraméterhez rendelkezésre álló adatforrások az adott paraméter érvényes értékeitől függenek. A Null érték például nem érhető el olyan paraméterhez, amely nem engedélyezi a null értékeket.

| Adatforrás | Leírás |
|:--- |:--- |
| Állandó érték |Írjon be egy értéket a paraméterhez. Ez az adatforrás csak a következő adatforrások esetén érhető el: Int32, Int64, String, Boolean, DateTime, Switch. |
| Tevékenység kimenete |A munkafolyamat aktuális tevékenységét megelőző tevékenység kimenetének használata. Minden érvényes tevékenység fel van sorolva. A paraméter értékéhez csak a kimenetet előállító tevékenységet használja. Ha a tevékenység több tulajdonsággal rendelkező objektumot ad ki, a tevékenység kiválasztása után beírhatja egy adott tulajdonság nevét. |
| Runbook bemenet |Válasszon ki egy runbook-bemenetet a tevékenységparaméter bemeneteként. |
| Változó eszköz |Válasszon egy Automation változót bemenetként. |
| Hitelesítő adatok |Válasszon egy automatizálási hitelesítő adatot bemenetként. |
| Tanúsítvány-eszköz |Válasszon egy automation-tanúsítványt bemenetként. |
| Csatlakozási eszköz |Válasszon egy Automation-kapcsolatot bemenetként. |
| PowerShell-kifejezés |Adjon meg egy egyszerű [PowerShell-kifejezést.](#powershell-expressions) A kifejezés kiértékelése a tevékenység előtt történik, és az eredményt a paraméterértékhez használja. A változók segítségével hivatkozhat egy tevékenység vagy egy runbook bemeneti paraméter kimenetére. |
| Nincs konfigurálva |Törölje a korábban konfigurált értékeket. |

#### <a name="optional-additional-parameters"></a>Választható további paraméterek

Minden parancsmag további paramétereket adhat meg. Ezek a PowerShell-közös paraméterek vagy más egyéni paraméterek. A grafikus szerkesztő egy szövegmezőt jelenít meg, ahol a PowerShell szintaxisa segítségével paramétereket adhat meg. A `Verbose` közös paraméter használatához például meg `-Verbose:$True`kell adnia a értéket.

### <a name="retry-activity"></a>Újrapróbálkozási tevékenység

Egy tevékenység újrapróbálkozási funkciója lehetővé teszi, hogy egy adott feltétel teljesítéséig többször is fusson, hasonlóan a hurokhoz. Ezt a funkciót olyan tevékenységekhez használhatja, amelyeknek többször kell futniuk, hibalehetőségeket okozhatnak, egynél több sikeres kísérletre lehet szükségük, vagy tesztelheti a tevékenység kimeneti adatait érvényes adatokért.

Ha engedélyezi az újrapróbálkozást egy tevékenységhez, beállíthat egy késleltetést és egy feltételt. A késleltetés az az idő (másodpercben vagy percben mérve), amelyet a runbook vár, mielőtt újra futtatja a tevékenységet. Ha nem ad meg késleltetést, a tevékenység a befejezés után azonnal újra fut.

![Tevékenység újrapróbálkozási késleltetése](media/automation-graphical-authoring-intro/retry-delay.png)

Az újrapróbálkozási feltétel egy PowerShell-kifejezés, amely a tevékenység minden egyes futtatása után kiértékelésre kerül. Ha a kifejezés igaz lesz, a tevékenység újra lefut. Ha a kifejezés hamis lesz, a tevékenység nem fut újra, és a runbook továbblép a következő tevékenységre.

![Tevékenység újrapróbálkozási késleltetése](media/automation-graphical-authoring-intro/retry-condition.png)

Az újrapróbálkozási feltétel használhat `RetryData` egy nevű változót, amely hozzáférést biztosít a tevékenység újrapróbálkozásokkal kapcsolatos információkhoz. Ez a változó a következő táblázatban található tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| `NumberOfAttempts` |A tevékenység futtatásának száma. |
| `Output` |A tevékenység utolsó futásának kimenete. |
| `TotalDuration` |A tevékenység első indítása óta eltelt idő eltelt. |
| `StartedAt` |A tevékenység első indításakori idő (UTC formátumban). |

Az alábbiakban példákat mutatunk be a tevékenység újrapróbálkozási feltételeire.

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

Miután konfiguráltegy újrapróbálkozási feltételt egy tevékenységhez, a tevékenység két vizuális jelzést tartalmaz, amelyek emlékeztetik Önt. Az egyik megjelenik a tevékenységben, a másik pedig a tevékenység konfigurációjának áttekintésekénél.

![Tevékenység újrapróbálkozási vizuális kijelzők](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Munkafolyamat-parancsfájl vezérlő

A munkafolyamat-parancsfájl-vezérlő egy speciális tevékenység, amely elfogadja a PowerShell vagy a PowerShell-munkafolyamat-parancsfájl, típusától függően a grafikus runbook a szerzői. Ez a vezérlő olyan funkciókat biztosít, amelyek más módon esetleg nem érhetők el. Nem tudja elfogadni a paramétereket, de a tevékenység kimeneti és runbook bemeneti paraméterek változókat használhat. A tevékenység bármely kimenete hozzáadódik az adatbuszhoz. Kivételt képez a kimenő kapcsolat nélküli kimenet, amely esetben a kimenet hozzáadódik a runbook kimenetéhez.

A következő kód például dátumszámításokat hajt végre egy `NumberOfDays`runbook bemeneti változó neve alapján. Ezután egy számított DateTime értéket küld kimenetként a runbook későbbi tevékenységei hez.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Hivatkozások és munkafolyamat

A grafikus runbookban lévő hivatkozás két tevékenységet kapcsol össze. A vásznon a forrástevékenységről a céltevékenységre mutató nyílként jelenik meg. A tevékenységek a nyíl irányába nének irányába futnak, és a céltevékenység a forrástevékenység befejezése után kezdődik.

### <a name="link-creation"></a>Hivatkozás létrehozása

Két tevékenység közötti kapcsolatot úgy hozhat létre, hogy kiválasztja a forrástevékenységet, és az alakzat alján lévő körre kattint. Húzza a nyilat a céltevékenységre, és engedje fel.

![Hivatkozás létrehozása](media/automation-graphical-authoring-intro/create-link-options.png)

Válassza ki a kapcsolatot a konfigurációpanelen a tulajdonságainak konfigurálásához. A tulajdonságok közé tartozik a hivatkozás típusa, amelyet az alábbi táblázat ismertet.

| Hivatkozás típusa | Leírás |
|:--- |:--- |
| Folyamat |A céltevékenység a forrástevékenység minden egyes objektumkimenetéhez egyszer fut. A céltevékenység nem fut, ha a forrástevékenység nem eredményez kimenetet. A forrástevékenység kimenete objektumként érhető el. |
| Sequence |A céltevékenység csak egyszer fut, amikor a forrástevékenységből kap kimenetet. A forrástevékenység kimenete objektumok tömbjeként érhető el. |

### <a name="start-of-activity"></a>A tevékenység kezdete

A grafikus runbook minden olyan tevékenységgel kezdődik, amely nem rendelkezik bejövő hivatkozással. Gyakran csak egy tevékenység, amely a runbook kezdő tevékenységként működik. Ha több tevékenység nem rendelkezik bejövő kapcsolattal, a runbook elindítja azokat párhuzamosan futtatva. Ez követi a linkeket futtatni más tevékenységek, mint minden befejeződött.

### <a name="link-conditions"></a>Kapcsolódási feltételek

Ha egy hivatkozáson feltételt ad meg, a céltevékenység csak akkor fut, ha a feltétel igaz lesz. Általában egy feltételben lévő `ActivityOutput` változót használ a kimenet lekéréséhez a forrástevékenységből.

Csővezeték-kapcsolat esetén meg kell adnia egy feltételt egyetlen objektumhoz. A runbook kiértékeli a forrástevékenység által az egyes objektumkimenetek állapotát. Ezután futtatja a céltevékenységet minden olyan objektumhoz, amely megfelel a feltételnek. A program forrástevékenységével `Get-AzVM`például a következő szintaxissal egy feltételes folyamatkapcsolathoz csak a Csoport1 nevű erőforráscsoportban lévő virtuális gépeket kérheti le.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Szekvenciakapcsolat esetén a runbook csak egyszer értékeli ki a feltételt, mivel a forrástevékenységből származó összes objektumot tartalmazó egyetlen tömbet adja vissza. Emiatt a runbook nem tud szekvenciakapcsolatot használni a szűréshez, mint egy csővezeték-kapcsolatesetében. A szekvencia-kapcsolat egyszerűen meghatározhatja, hogy a következő tevékenység fut-e.

Például vegye figyelembe a következő tevékenységek et a **Virtuálisgép-indítási** runbookban:

![Feltételes csatolás szekvenciákkal](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

A runbook három különböző szekvenciakapcsolatot használ, amelyek ellenőrzik a bemeneti paraméterek értékeit, `VMName` és `ResourceGroupName` meghatározzák a megfelelő lépéseket. Lehetséges műveletek egy virtuális gép indítása, az erőforráscsoportban lévő összes virtuális gép indítása vagy az előfizetésben lévő összes virtuális gép indítása. A szekvencia `Get single VM`kapcsolat és a között, `Connect to Azure` itt van a feltétel logikája:

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

Ha feltételes hivatkozást használ, a forrástevékenységből az adott ág más tevékenységeihez elérhető adatokat a feltétel szűri. Ha egy tevékenység több hivatkozás forrása, az egyes ágak tevékenységei számára elérhető adatok az adott ághoz kapcsolódó hivatkozás állapotától függnek.

Például az `Start-AzVM` alábbi runbookban lévő tevékenység elindítja az összes virtuális gépet. Két feltételes kapcsolata van. Az első feltételes hivatkozás `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` a kifejezés `Start-AzVM` segítségével szűri a kifejezést, ha a tevékenység sikeresen befejeződött. A második feltételes hivatkozás `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` a kifejezést `Start-AzVm` használja a szűrőre, ha a tevékenység nem tudja elindítani a virtuális gépet.

![Példa feltételes hivatkozásra](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Minden olyan tevékenység, amely követi az első `Get-AzureVM` kapcsolatot, és csak a tevékenység kimenetét használja, lekéri azokat a virtuális gépeket, amelyek a futtatás időpontjában `Get-AzureVM` kezdődtek. Minden olyan tevékenység, amely követi a második kapcsolat csak leveszi a virtuális gépek, amelyek leállították az adott időpontban, amikor `Get-AzureVM` futtatták. A harmadik hivatkozást követő bármely tevékenység az összes virtuális gépet megkapja a futó állapotuktól függetlenül.

### <a name="junctions"></a>Csomópontok

A csomópont egy speciális tevékenység, amely megvárja, amíg az összes bejövő ág befejeződik. Ez lehetővé teszi, hogy a runbook több tevékenységet párhuzamosan futtasson, és győződjön meg arról, hogy minden befejeződött, mielőtt továbblépne.

Bár egy csomópont korlátlan számú bejövő kapcsolattal rendelkezhet, csak az egyik ilyen kapcsolat lehet folyamat. A bejövő szekvenciakapcsolatok száma nincs korlátozva. A csomópont több bejövő folyamatkapcsolattal is létrehozható, és mentheti a runbookot, de futtatásakor sikertelen lesz.

Az alábbi példa egy runbook része, amely elindítja a virtuális gépek készletét, miközben letölti az ezekre a gépekre alkalmazandó javításokat. Csomópontot használ annak biztosítására, hogy mindkét folyamat befejeződjön, mielőtt a runbook folytatódik.

![Szinkronizációs pont](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciklus

Ciklus akkor jön létre, amikor a céltevékenység visszakapcsolódik a forrástevékenységéhez vagy egy másik tevékenységhez, amely végül a forrásához kapcsolódik. A grafikus szerzői szöveg jelenleg nem támogatja a ciklusokat. Ha a runbook rendelkezik egy ciklus, akkor menti megfelelően, de hibaüzenetet kap, amikor fut.

![Ciklikus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>A tevékenységek közötti adatmegosztás

Minden olyan adat, amely egy tevékenység kimenő hivatkozással kimenetelét adja ki, a runbook adatbuszára kerül. A runbook bármely tevékenysége használhatja a databus-on lévő adatokat a paraméterértékek feltöltéséhez, vagy a parancsfájlkódban való belefoglaláshoz. Egy tevékenység hozzáférhet a munkafolyamat bármely korábbi tevékenységének kimenetéhez.

Az adatok adatbuszba írásának módja a tevékenységre mutató hivatkozás típusától függ. Egy folyamatkapcsolat esetén az adatok több objektumként lesznek kikényszerítve. Szekvenciakapcsolat esetén az adatok tömbként lesznek kivéve. Ha csak egy érték van, akkor a kimenet egyelemű tömbként lesz.

A runbook kétféleképpen érheti el az adatbuszon lévő adatokat: 
* Tevékenységkimeneti adatforrás használata.
* Használjon PowerShell-kifejezés adatforrást.

Az első mechanizmus egy tevékenység kimeneti adatforrást használ egy másik tevékenység paraméterének feltöltéséhez. Ha a kimenet objektum, a runbook egyetlen tulajdonságot adhat meg.

![tevékenység kimenete](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

A második adatelérési mechanizmus lekéri egy tevékenység kimenetét egy PowerShell-kifejezés `ActivityOutput` adatforrásában vagy egy munkafolyamat-parancsfájl tevékenység egy változó, az alábbi szintaxis használatával. Ha a kimenet objektum, a runbook egyetlen tulajdonságot adhat meg.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Ellenőrzőpontok

A grafikus PowerShell-munkafolyamat-runbookban állíthatja be [az ellenőrzőpontokat,](automation-powershell-workflow.md#checkpoints) ha bármely **tevékenységnél az Ellenőrzőpont-runbookot** választja. Ez azt eredményezi, hogy a tevékenység futtatása után egy ellenőrzőpont ot kell beállítani.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Ellenőrzőpontok csak engedélyezett grafikus PowerShell-munkafolyamat runbookok, és nem érhetők el a grafikus runbookok. Ha a runbook Azure-parancsmagokat használ, kövesse a `Connect-AzAccount` tevékenységet ellenőrző pontokon végzett tevékenységeket. A connect művelet abban az esetben használatos, ha a runbook fel van függesztve, és újra kell indítani erről az ellenőrzőpontról egy másik dolgozó.

## <a name="runbook-input-and-output"></a>Runbook bemenet és kimenet

### <a name="runbook-input"></a>Runbook bemenet<a name="runbook-input"></a>

A runbook bemenetet igényel, vagy egy felhasználó, aki elindítja a runbook az Azure Portalon keresztül, vagy egy másik runbook, ha az aktuális gyermekként használják. Például egy virtuális gépet létrehozó runbook esetében előfordulhat, hogy a felhasználónak meg kell adnia az olyan információkat, mint a virtuális gép neve és más tulajdonságok minden alkalommal, amikor a runbook elindul.

A runbook egy vagy több bemeneti paraméter definiálásával fogadja el a bemeneti adatokat. A felhasználó a runbook indításakor értékeket ad meg ezekhez a paraméterekhez. Amikor a felhasználó elindítja a runbookot az Azure Portalon, a rendszer felszólítja a felhasználót, hogy adja meg a runbook által támogatott minden egyes bemeneti paraméter értékeit.

A runbook készítésekénél a bemeneti paraméterek eléréséhez kattintson a **Bemenet és kimenet** a Runbook eszköztáron. Ezzel megnyitja a Bemeneti és kimenetvezérlőt, ahol szerkesztheti a meglévő bemeneti paramétert, vagy újat hozhat létre a **Bevitel hozzáadása**gombra kattintva.

![Bemenet hozzáadása](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Az egyes bemeneti paramétereket a következő táblázatban szereplő tulajdonságok határozzák meg:

| Tulajdonság | Leírás |
|:--- |:--- |
| Name (Név) | Kötelező. A paraméter neve. A névnek egyedinek kell lennie a runbookon belül. Betűvel kell kezdődnie, és csak betűket, számokat és aláhúzásjeleket tartalmazhat. A név nem tartalmazhat szóközt. |
| Leírás |Választható. A bemeneti paraméter céljának leírása. |
| Típus | Választható. A paraméterértékhez adattípusra van felszámítva. Az Azure Portal biztosítja a megfelelő vezérlőt az adattípus minden paraméter, amikor a bemeneti kér. A támogatott paramétertípusok a következők: Karakterlánc, Int32, Int64, Decimális, Logikai, DateTime és Objektum. Ha egy adattípus nincs kijelölve, akkor az alapértelmezett érték a Karakterlánc.|
| Kötelező | Választható. Beállítás, amely megadja, hogy meg kell-e adni egy értéket a paraméterhez. Ha úgy `yes`dönt, meg kell adni egy értéket a runbook indításakor. Ha úgy `no`dönt, a runbook indításakor nincs szükség értékre, és alapértelmezett érték is használható. A runbook nem indítható el, ha nem ad meg értéket minden olyan kötelező paraméterhez, amelyhez nincs megadva alapértelmezett érték. |
| Alapértelmezett érték | Választható. A paraméterhez használt érték, ha a runbook indításakor nem adják át. Az alapértelmezett érték beállításához válassza a lehetőséget. `Custom` Jelölje `None` be, ha nem szeretne alapértelmezett értéket megadni. |

### <a name="runbook-output"></a>Runbook kimenete

A grafikus szerzői szöveg olyan tevékenységek által létrehozott adatokat ment, amelyek nem rendelkeznek kimenő hivatkozással [a runbook kimenetéhez.](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages) A kimenet a runbook-feladattal együtt kerül mentésre, és a szülő runbook számára elérhető, ha a runbookot gyermekként használják.

## <a name="powershell-expressions"></a>PowerShell-kifejezések

A grafikus szerzői jog egyik előnye, hogy lehetővé teszi a PowerShell minimális ismeretet tartalmazó runbook okozását. Jelenleg azonban ismernie kell egy kicsit a PowerShellt bizonyos [paraméterértékek](#activities) feltöltéséhez és a [kapcsolati feltételek](#links-and-workflow)beállításához. Ez a szakasz rövid en bemutatja a PowerShell-kifejezéseket. A PowerShell teljes részletei a [Windows PowerShell-alapú Parancsfájlok szolgáltatásban](https://technet.microsoft.com/library/bb978526.aspx)érhetők el.

### <a name="powershell-expression-data-source"></a>PowerShell-kifejezés adatforrása

A PowerShell-kifejezések adatforrásként használhatja egy [tevékenységparaméter](#activities) értékének feltöltéséhez a PowerShell-kód eredményeivel. A kifejezés lehet egy kódsor, amely egy egyszerű függvényt vagy több sort hajt végre, amelyek valamilyen összetett logikát hajtanak végre. A változóhoz nem rendelt parancs bármely kimenete a paraméter értékéhez lesz kiadva.

A következő parancs például az aktuális dátumot adja ki.

```powershell-interactive
Get-Date
```

A következő kódrészlet létrehoz egy karakterláncot az aktuális dátumból, és hozzárendeli egy változóhoz. A kód elküldi a változó tartalmát a kimenetre.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

A következő parancsok kiértékelik az aktuális dátumot, és egy karakterláncot adnak vissza, amely jelzi, hogy az aktuális nap hétvége vagy hétköznap.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>Tevékenység kimenete

A runbook egy korábbi tevékenységkimenetének használatához `ActivityOutput` használja a változót az alábbi szintaxissal.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Például lehet egy tevékenység egy tulajdonság, amely megköveteli a virtuális gép nevét. Ebben az esetben a runbook használhatja a következő kifejezést.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Ha a tulajdonság hoz a virtuális gép objektum helyett csak egy nevet, a runbook a teljes objektumot adja vissza a következő szintaxis sal.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

A runbook egy tevékenység kimenetét egy összetettebb kifejezésben használhatja, például a következőkben. Ez a kifejezés összefűzi a szöveget a virtuális gép nevével.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Feltételek

[Az összehasonlító operátorok](https://technet.microsoft.com/library/hh847759.aspx) segítségével összehasonlíthatja az értékeket, vagy meghatározhatja, hogy egy érték megfelel-e egy megadott mintának. Az összehasonlítás igaz vagy hamis értéket ad eredményül.

A következő feltétel például azt határozza meg, `Get-AzureVM` hogy a virtuális gép egy megnevezett tevékenységből jelenleg le van-e állítva.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A következő feltétel határozza meg, hogy ugyanaz a virtuális gép a leállítotttól eltérő állapotban van-e.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

A runbookban több feltételt is csatlakoztathat `-and` egy `-or` [logikai operátor](https://technet.microsoft.com/library/hh847789.aspx)használatával, például vagy . A következő feltétel például ellenőrzi, hogy az előző példában a virtuális gép leállítva vagy leállításállapotban van-e.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Hashtables

[A kivonatolók](https://technet.microsoft.com/library/hh847780.aspx) olyan név-érték párok, amelyek hasznosak egy értékhalmaz visszaadásához. A szótárnak nevezett kivonattáblázat is megjelenhet. Bizonyos tevékenységek tulajdonságai egyszerű érték helyett kivonatolást várnak.

Hozzon létre egy kivonattáblát az alábbi szintaxissal. Tetszőleges számú bejegyzést tartalmazhat, de mindegyiket egy név és érték határozza meg.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

A következő kifejezés például létrehoz egy kivonattáblát, amely egy olyan tevékenységparaméter adatforrásaként használható, amely egy internetes keresés értékeinek kivonatát várja.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

A következő példa egy kivonattáblázat feltöltéséhez egy nevű `Get Twitter Connection` tevékenység kimenetét használja.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Hitelesítés Azure-erőforrásokhoz

Az Azure-erőforrásokat kezelő Runbookok az Azure-erőforrásokat kezelő Runbookokat az Azure-ban való hitelesítésre van szükség. A [Futtatás másként fiók](automation-create-runas-account.md), más néven egyszerű szolgáltatás, az alapértelmezett mechanizmus, amely egy Automation runbook az Azure Resource Manager-erőforrások eléréséhez az előfizetésben. Ezt a funkciót hozzáadhatja egy grafikus runbookhoz, ha hozzáadja a `AzureRunAsConnection` kapcsolati eszközt, amely a PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) parancsmagját használja a vászonhoz. A [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmamot is hozzáadhatja. Ezt a forgatókönyvet a következő példa szemlélteti.

![Futtatás hitelesítési tevékenységként](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A `Get Run As Connection` tevékenység `Get-AutomationConnection`vagy a , állandó értékű `AzureRunAsConnection`adatforrással van konfigurálva.

![Futtatás kapcsolatkonfigurációként](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A következő `Connect-AzAccount`tevékenység , hozzáadja a hitelesített Futtatás mint fiókot a runbookban való használatra.

![Connect-AzAccount paraméterkészlet](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>A PowerShell runbookok, `Add-AzAccount` és `Connect-AzAccount` `Add-AzureRMAccount` aliasok. Vegye figyelembe, hogy ezek az aliasok nem érhetők el a grafikus runbookok. A grafikus runbookcsak `Connect-AzAccount` önmagát használhatja.

Az **APPLICATIONID**, **CERTIFICATETHUMBPRINT**és **TENANTID**paramétermezőkben adja meg a mezőelérési út tulajdonságának nevét, mivel a tevékenység több tulajdonsággal rendelkező objektumot ad ki. Ellenkező esetben a runbook végrehajtásakor sikertelen, miközben hitelesítést próbál végrehajtani. Ez az, amire legalább a Run As fiókkal való hitelesítéséhez szüksége van a runbook hitelesítéséhez.

Egyes előfizetők egy Azure [AD felhasználói fiók](automation-create-aduser-account.md) használatával hoznak létre egy Automation-fiókot az Azure klasszikus üzembe helyezésének vagy az Azure Resource Manager-erőforrások kezeléséhez. Ezen előfizetők visszamenőleges kompatibilitásának fenntartása érdekében a runbookban `Add-AzureAccount` használandó hitelesítési mechanizmus a [hitelesítő adatokkal](automation-credentials.md)rendelkező parancsmag. Az eszköz egy Azure-fiókhoz hozzáféréssel rendelkező Active Directory-felhasználót jelöl.

Ezt a funkciót a grafikus runbookhoz úgy engedélyezheti, hogy hitelesítő `Add-AzureAccount` adatokat ad hozzá a vászonhoz, majd egy tevékenységet, amely a hitelesítő adatokat használja a hitelesítő adatokhoz. Tekintse meg a következő példát.

![Hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authentication-activities.png)

A runbooknak hitelesítenie kell magát az indításkor és minden ellenőrzőpont után. Így minden `Checkpoint-Workflow` tevékenység `Add-AzureAccount` után tevékenységet kell használnia. Nem kell további hitelesítő adatokat használnia.

![Tevékenység kimenete](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Grafikus runbook exportálása és importálása

Csak a grafikus runbook közzétett verzióját exportálhatja. Ha a runbook még nem lett közzétéve, az **Exportálás** gomb le van tiltva. Amikor az **Exportálás** gombra kattint, a runbook letöltődik a helyi számítógépre. A fájl neve megegyezik a **.graphrunbook** kiterjesztéssel rendelkező runbook nevével.

Grafikus vagy grafikus PowerShell-munkafolyamat-runbook-fájlt importálhat, ha runbook hozzáadásakor az Importálás lehetőséget **választja.** Amikor kijelöli az importálni kívánt fájlt, megtarthatja ugyanazt a nevet, vagy újat adhat meg. A **Runbook Type** mező a runbook típusát jeleníti meg a kiválasztott fájl felmolása után. Ha nem megfelelő más típust próbál kijelölni, a grafikus szerkesztő egy üzenetet jelenít meg, amely szerint lehetséges ütközések léphetnek fel, és a konvertálás során szintaktikai hibák léphetnek fel.

![Runbook importálása](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>Grafikus runbook tesztelése

Az Azure Automation minden egyes grafikus runbookja rendelkezik egy draft verzióval és egy közzétett verzióval. Csak a Közzétett verzió futtatható, míg a Piszkozat verzió csak szerkeszthető. A Közzétett verzióra nincsenek hatással a Piszkozat verzióban végrehajtott módosítások. Amikor a Piszkozat verzió készen áll a használatra, közzéteszi azt, amely felülírja az aktuális közzétett verziót a Piszkozat verzióval.

Tesztelheti a Runbook piszkot az Azure Portalon, miközben a közzétett verzió változatlan marad. Másik lehetőségként tesztelheti az új runbookot a közzététel előtt, így ellenőrizheti, hogy a runbook megfelelően működik-e, mielőtt bármilyen verziócsere lenne. A runbook tesztelése végrehajtja a Piszkot verziót, és biztosítja, hogy az általa végrehajtott műveletek befejeződtek. Nem jön létre feladatelőzmények, de a Teszt kimenet ablaktábla megjeleníti a kimenetet.

A grafikus runbook Teszt vezérlőelemének megnyitásához nyissa meg a futtatási könyvet szerkesztésre, majd kattintson a **Teszt ablaktáblára**. A Teszt vezérlő kéri a bemeneti paramétereket, és a **Start**gombra kattintva indíthatja el a runbookot.

## <a name="publishing-a-graphical-runbook"></a>Grafikus runbook közzététele

Grafikus runbook közzététele a runbook szerkesztésre való megnyitásával, majd a **Közzététel**gombra kattintva. A runbook lehetséges állapotai a következők:

* Új - a runbook még nem lett közzétéve. 
* Közzétéve - a runbook már megjelent.
* A szerkesztésben – a runbook ot a közzététel után szerkesztették, és a Vázlat és a Közzétett verziók eltérőek.

![Runbook-állapotok](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Lehetősége van arra, hogy visszatérjen a runbook közzétett verziójához. Ez a művelet eldobja a runbook legutóbbi közzététele óta végrehajtott módosításokat. A runbook piszkozatverzióját a Közzétett verzióval helyettesíti.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-munkafolyamat-runbookok első [lépései: Az első PowerShell-munkafolyamat-runbook.](automation-first-runbook-textual.md)
* A grafikus runbookok első lépései [Az Első grafikus runbook című témakörben](automation-first-runbook-graphical.md)látható.
* Ha többet szeretne tudni a runbook-típusokról, azok előnyeiről és korlátairól, olvassa el az [Azure Automation runbook-típusok című témakört.](automation-runbook-types.md)
* Az Automation Run As fiók használatával történő hitelesítésről az [Azure Run as account konfigurálása című témakörben](automation-sec-configure-azure-runas-account.md)tekinthető meg.
* A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
