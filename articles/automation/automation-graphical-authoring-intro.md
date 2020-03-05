---
title: Grafikus szerzői műveletek Azure Automation
description: A grafikus szerzői műveletek lehetővé teszik, hogy runbookok hozzon létre Azure Automation a kód használata nélkül. Ez a cikk bemutatja a grafikus szerzői műveletek és a grafikus runbook létrehozásának megkezdéséhez szükséges összes részlet bevezetését.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: a50dbe4d1e100032282891ccd15a94330f7fead4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78272974"
---
# <a name="graphical-authoring-in-azure-automation"></a>Grafikus szerzői műveletek Azure Automation

A grafikus készítés lehetővé teszi, hogy az alapul szolgáló Windows PowerShell-vagy PowerShell munkafolyamat-kód bonyolultsága nélkül hozza létre a Azure Automation runbookok. Hozzáadhat tevékenységeket a vászonhoz a parancsmagok és runbookok könyvtára alapján, összekapcsolhatja őket, és konfigurálhatja őket munkafolyamatok létrehozásához. Ha valaha is dolgozott a System Center Orchestrator vagy Service Management Automation (SMA), a grafikus szerzőknek ismerősnek kell lenniük. Ez a cikk bemutatja a grafikus runbook létrehozásának megkezdéséhez szükséges fogalmakat.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="graphical-runbooks"></a>Grafikus runbookok

A Azure Automation összes runbookok Windows PowerShell-munkafolyamatok. A grafikus runbookok és a grafikus PowerShell-munkafolyamat runbookok olyan PowerShell-kódot állít elő, amelyet az Automation-feldolgozó futtat, de nem tudja megtekinteni vagy módosítani. Grafikus runbook grafikus PowerShell-munkafolyamat runbook alakíthat át, és fordítva is. Ezeket a runbookok azonban nem konvertálhatja szöveges runbook. Emellett az Automation grafikus szerkesztőjének nem lehet szöveges runbook importálnia.

## <a name="overview-of-graphical-editor"></a>A grafikus szerkesztő áttekintése

A Azure Portal grafikus szerkesztőjét grafikus runbook létrehozásával vagy szerkesztésével nyithatja meg.

![Grafikus munkaterület](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

A következő szakaszok a grafikus szerkesztőben lévő vezérlőket ismertetik.

### <a name="canvas-control"></a>Vászon vezérlőelem

A vászon vezérlőelem lehetővé teszi a runbook megtervezését. Hozzáadhat tevékenységeket a könyvtár vezérlőelem csomópontjaiból a runbook, és összekapcsolhatja őket a runbook-logika definiálásához használható hivatkozásokkal. A vászon alján olyan vezérlők találhatók, amelyek lehetővé teszik a nagyítást és a kicsinyítést.

### <a name="library-control"></a>Könyvtár-vezérlőelem

A könyvtár vezérlőelem lehetővé teszi, hogy kiválassza a runbook hozzáadni kívánt [tevékenységeket](#activities) . Hozzáadja őket a vászonhoz, ahol más tevékenységekhez is csatlakozhat. A könyvtár vezérlőelem a következő táblázatban definiált szakaszt tartalmazza.

| Section | Leírás |
|:--- |:--- |
| Parancsmagok |Az összes olyan parancsmag, amely használható a runbook. A parancsmagok modul szerint vannak rendszerezve. Az Automation-fiókba telepített összes modul elérhető. |
| Runbookok |Az Automation-fiók runbookok. Ezeket a runbookok felveheti a vászonra, hogy gyermek runbookok lehessen használni. Csak a szerkesztett runbook azonos alaptípusú runbookok jelennek meg. Grafikus runbookok csak a PowerShell-alapú runbookok jelennek meg. A grafikus PowerShell-munkafolyamat runbookok csak a PowerShell munkafolyamat-alapú runbookok jelennek meg. |
| Objektumok |Az Automation-fiókban található [Automation-eszközök](/previous-versions/azure/dn939988(v=azure.100)) , amelyeket a runbook használhat. Egy eszköz runbook való hozzáadása egy munkafolyamat-tevékenységet ad hozzá, amely beolvassa a kijelölt objektumot. Változó eszközök esetén kiválaszthatja, hogy szeretne-e tevékenységet hozzáadni a változóhoz, vagy beállítja a változót. |
| Runbook-vezérlő |Az aktuális runbook használható vezérlési tevékenységek. A csatlakozási tevékenységek több bemenetet is igénybe vesznek, és addig vár, amíg az összes befejezve nem fejeződött be a munkafolyamat folytatása előtt. A kód tevékenység a grafikus runbook függően a PowerShell-vagy a PowerShell-munkafolyamat kódjának egy vagy több sorát futtatja. Ezt a tevékenységet egyéni kódokhoz vagy olyan funkciókhoz használhatja, amelyeket nehéz elérni más tevékenységekkel. |

### <a name="configuration-control"></a>Konfiguráció vezérlő

A konfiguráció vezérlőelem lehetővé teszi a vásznon kiválasztott objektumok részleteinek megadását. Az ebben a vezérlőben elérhető tulajdonságok a kiválasztott objektum típusától függenek. Amikor kiválaszt egy beállítást a konfiguráció vezérlőben, további lapokat nyit meg további információk megadásához.

### <a name="test-control"></a>Tesztelési vezérlő

A tesztelési vezérlő nem jelenik meg a grafikus szerkesztő első elindításakor. Akkor nyílik meg, amikor interaktívan tesztel egy grafikus runbook.

## <a name="activities"></a>Tevékenységek

A tevékenységek a runbook építőelemei. Egy tevékenység lehet PowerShell-parancsmag, gyermek runbook vagy munkafolyamat. Hozzáadhat egy tevékenységet a runbook. ehhez kattintson a jobb gombbal a könyvtár vezérlőelemre, és válassza a **Hozzáadás a vászonhoz**lehetőséget. Ezután rákattinthat a tevékenységre, és húzással bárhová elhelyezheti azt a vászonon, amelyet szeretne. A tevékenység helye a vásznon nem befolyásolja a runbook működését. A runbook úgy is megállapíthatja, hogy a legmegfelelőbb módon jelenítse meg a műveletét.

![Hozzáadás a vászonhoz](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

Válasszon ki egy tevékenységet a vásznon a tulajdonságok és paraméterek konfigurálásához a konfiguráció panelen. Megváltoztathatja a tevékenység címkéjét egy olyan névre, amelyet leíróként talál. A runbook továbbra is az eredeti parancsmagot futtatja. Egyszerűen megváltoztatja a grafikus szerkesztő által használt megjelenítendő nevet. Vegye figyelembe, hogy a címkének egyedinek kell lennie a runbook belül.

### <a name="parameter-sets"></a>Paraméterek készletei

A beállításhalmaz meghatározza azokat a kötelező és választható paramétereket, amelyek egy adott parancsmag értékeit fogadják el. Minden parancsmaghoz legalább egy paraméter van beállítva, néhány pedig több készlettel rendelkezik. Ha egy parancsmagnak több paramétere is van, akkor a paraméterek konfigurálása előtt ki kell választania a használni kívánt elemet. A tevékenység által használt paramétereket megváltoztathatja a **set paraméter** kiválasztásával és egy másik készlet kiválasztásával. Ebben az esetben a már konfigurált paraméterek értékei elvesznek.

A következő példában a [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) parancsmag három paraméter-készlettel rendelkezik. A példa egy **ListVirtualMachineInResourceGroupParamSet**nevű készletet használ egyetlen opcionális paraméterrel, amely egy erőforráscsoport összes virtuális gépének visszaadása. A példa a **GetVirtualMachineInResourceGroupParamSet** paramétert is használja a visszaadni kívánt virtuális gép megadásához. A készlet két kötelező paraméterrel és egy opcionális paraméterrel rendelkezik.

![Paraméter beállítva](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>Paraméterek értékei

Egy paraméter értékének megadásakor ki kell választania egy adatforrást, amely meghatározza, hogy az érték hogyan legyen megadva. Egy adott paraméter számára elérhető adatforrások az adott paraméter érvényes értékeitől függenek. A **Null** érték például nem érhető el egy olyan paraméter számára, amely nem engedélyezi null értékek használatát.

| Adatforrás | Leírás |
|:--- |:--- |
| Konstans érték |Adja meg a paraméter értékét. Ez az adatforrás csak a következő adattípusokhoz érhető el: Int32, Int64, string, Boolean, DateTime, Switch. |
| Tevékenység kimenete |Olyan tevékenység kimenetét használja, amely megelőzi az aktuális tevékenységet a munkafolyamatban. Minden érvényes tevékenység megjelenik. A paraméter értékeként csak a kimenetet előállító tevékenységet használja. Ha a tevékenység több tulajdonsággal rendelkező objektumot ad eredményül, a tevékenység kiválasztása után megadhatja egy adott tulajdonság nevét. |
| Runbook bemenete |Válassza ki a runbook bemenetét bemenetként a tevékenység paraméterhez. |
| Változó eszköz |Válassza ki az Automation változót bemenetként. |
| Hitelesítőadat-eszköz |Válassza ki az Automation hitelesítő adatait bemenetként. |
| Tanúsítvány-eszköz |Válassza ki az Automation-tanúsítványt bemenetként. |
| A kapcsolatfelvételi eszköz |Válassza ki az Automation-kapcsolatokat bemenetként. |
| PowerShell-kifejezés |Adja meg az egyszerű [PowerShell-kifejezést](#powershell-expressions). A kifejezés kiértékelése a tevékenység előtt történik, és a rendszer a paraméter értékének eredményét használja. A változók használatával hivatkozhat egy tevékenység kimenetére vagy egy runbook bemeneti paraméterre. |
| Nincs konfigurálva |Törölje a korábban konfigurált értékeket. |

#### <a name="optional-additional-parameters"></a>További választható paraméterek

Minden parancsmagnál lehetőség van további paraméterek megadására. Ezek a PowerShell-Common paraméterek vagy más egyéni paraméterek. A grafikus szerkesztő egy szövegmezőt jelenít meg, amelyen a PowerShell-szintaxis használatával adhat meg paramétereket. A *részletes* Common paraméter használatához például meg kell adnia a `-Verbose:$True`.

### <a name="retry-activity"></a>Újrapróbálkozási tevékenység

Egy tevékenység újrapróbálkozási funkciója lehetővé teszi, hogy többször is fusson, amíg egy adott feltétel nem teljesül, hasonlóan a hurokhoz. Ezt a funkciót olyan tevékenységekhez használhatja, amelyeknek többször is futniuk kell, a hibák hajlamosak lehetnek, több próbálkozásra van szükség, vagy a tevékenység kimeneti információit tesztelik érvényes adatokra.

Ha engedélyezi az újrapróbálkozást egy tevékenységhez, beállíthatja a késést és a feltételt. A késleltetés az az idő (másodpercben vagy percben mérve), ameddig a runbook megvárja a tevékenység ismételt futtatása előtt. Ha nem ad meg késleltetést, a tevékenység a befejeződése után azonnal elindul.

![Tevékenység újrapróbálkozási késleltetése](media/automation-graphical-authoring-intro/retry-delay.png)

Az újrapróbálkozási feltétel egy PowerShell-kifejezés, amelyet a rendszer a tevékenység futtatásakor értékel ki. Ha a kifejezés True (igaz) értékre oldódik, a tevékenység újra lefut. Ha a kifejezés hamis értéket ad vissza, a tevékenység nem fut újra, és a runbook a következő tevékenységre lép.

![Tevékenység újrapróbálkozási késleltetése](media/automation-graphical-authoring-intro/retry-condition.png)

Az újrapróbálkozási feltétel egy *RetryData* nevű változót használhat, amely hozzáférést biztosít a tevékenység újrapróbálkozásával kapcsolatos információkhoz. Ez a változó a következő táblázatban található tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás |
|:--- |:--- |
| NumberOfAttempts |A tevékenység futási idejének száma. |
| Kimenet |A tevékenység utolsó futtatásának kimenete. |
| TotalDuration |A tevékenység első elindítása óta eltelt idő. |
| StartedAt |A tevékenység első elindításakor bekövetkező idő (UTC formátumban). |

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

Egy tevékenység újrapróbálkozási feltételének konfigurálása után a tevékenység két vizuális mutatót tartalmaz, amelyekkel emlékeztetheti Önt. Az egyik a tevékenységben jelenik meg, a másik pedig a tevékenység konfigurációjának áttekintésekor látható.

![Tevékenység-újrapróbálkozások vizuális indikátorai](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Munkafolyamat-parancsfájl vezérlő

A munkafolyamat-parancsfájlok vezérlőelem egy speciális tevékenység, amely a létrehozott grafikus runbook függően a PowerShell vagy a PowerShell munkafolyamat-parancsfájlt fogadja. Ez a vezérlő olyan funkciókat biztosít, amelyek más módon nem érhetők el. A paraméterek nem fogadhatók el, de változókat is használhatnak a tevékenységek kimenetére és a runbook bemeneti paramétereinek használatára. A tevékenység bármilyen kimenete megjelenik a adatbuszba. Kivételt képez a kimenő hivatkozás nélküli kimenet, amely esetben a kimenet hozzá lesz adva a runbook kimenetéhez.

A következő kód például a *NumberOfDays*nevű runbook bemeneti változó használatával végez dátum-számításokat. Ezután a számított DateTime értéket küldi el kimenetként, amelyet a következő tevékenységek fognak használni a runbook.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>Hivatkozások és munkafolyamat

A grafikus runbook található hivatkozások két tevékenységet kapcsolnak össze. A vászonon a forrás tevékenységtől a cél tevékenységig mutató nyíl jelenik meg. A tevékenységek a nyíl irányában futnak a célként megadott tevékenységgel a forrás tevékenység befejeződése után.

### <a name="link-creation"></a>Hivatkozás létrehozása

A két tevékenység között úgy hozhat létre kapcsolatot, hogy kiválasztja a forrás tevékenységet, majd az alakzat alján található körre kattint. Húzza a nyilat a cél tevékenységre, és adja meg a kiadást.

![Hivatkozás létrehozása](media/automation-graphical-authoring-intro/create-link-revised20165.png)

Válassza ki a hivatkozást a tulajdonságainak konfigurálásához a konfiguráció panelen. A tulajdonságok közé tartozik a hivatkozás típusa, amelyet az alábbi táblázat ismertet.

| Hivatkozás típusa | Leírás |
|:--- |:--- |
| Folyamat |A célként megadott tevékenység a forrás tevékenység minden objektumának kimenetén egyszer fut. A célként megadott tevékenység nem fut, ha a forrásoldali tevékenység nem eredményez kimenetet. A forrás tevékenység kimenete objektumként érhető el. |
| Sorozat |A célként megadott tevékenység csak egyszer fut le, amikor megkapja a kimenetet a forrás tevékenységtől. A forrás tevékenység kimenete objektumok tömbje érhető el. |

### <a name="start-of-activity"></a>Tevékenység kezdete

A grafikus runbook minden olyan tevékenységgel elindul, amely nem rendelkezik bejövő hivatkozással. Gyakran csak egy tevékenység működik, amely a runbook kezdő tevékenysége. Ha több tevékenységnek nincs bejövő kapcsolata, a runbook párhuzamosan futtatva indul el. Az alábbi hivatkozásokat követve más tevékenységek futtatását hajtja végre.

### <a name="link-conditions"></a>Hivatkozás feltételei

Ha egy hivatkozásnál megad egy feltételt, a célként megadott tevékenység csak akkor fut le, ha a feltétel igaz értékre van feloldva. Általában egy *ActivityOutput* változót használ egy feltétellel, hogy lekérje a kimenetet a forrás tevékenységből.

Egy folyamat hivatkozásához meg kell adnia egy feltételt egyetlen objektumhoz. A runbook kiértékeli a forrás tevékenység minden objektum kimenetének feltételeit. Ezután futtatja a cél tevékenységet minden olyan objektumnál, amely megfelel a feltételnek. Ha például a **Get-AzVM**forrás tevékenységgel rendelkezik, a következő szintaxist használhatja egy feltételes folyamat hivatkozásához, hogy csak a Group1 nevű erőforráscsoport virtuális gépei legyenek lekérdezve.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Egy sorozatra mutató hivatkozás esetén a runbook csak egyszer értékeli ki a feltételt, mivel a program a forrás tevékenységből származó összes objektumot tartalmazó egyetlen tömböt ad vissza. Emiatt a runbook nem használhat szekvenciális hivatkozást a szűréshez, például egy folyamat hivatkozásával. A sorozat hivatkozása egyszerűen meghatározhatja, hogy a következő tevékenység fut-e.

Tegyük fel például, hogy a következő tevékenységek jelennek meg a **Start VM** -runbook:

![Feltételes hivatkozás szakaszokkal](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

A runbook három különböző sorozatot használ, amelyek a *VMName* és a *ResourceGroupName* bemeneti paraméterek értékeit ellenőrzik a megfelelő művelet meghatározásához. A lehetséges műveletek egyetlen virtuális gépet indítanak el, elindítják az erőforráscsoport összes virtuális gépét, vagy elindítják az előfizetésben lévő összes virtuális gépet. A csatlakozás az Azure- **hoz** és **egyetlen virtuális gép lekérése**között a következő feltétel logikája:

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

Ha feltételes hivatkozást használ, a forrás tevékenységtől az adott ág más tevékenységeire elérhető adatok a feltétel szerint vannak szűrve. Ha egy tevékenység több hivatkozás forrása, az egyes ágakban található tevékenységek számára elérhető adatok az adott ágra mutató hivatkozás állapotától függenek.

Például az alábbi runbook lévő **Start-AzVM** tevékenység elindítja az összes virtuális gépet. Két feltételes hivatkozással rendelkezik. Az első feltételes hivatkozás a `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` kifejezést használja annak szűrésére, hogy a **Start-AzVM** tevékenység sikeresen befejeződött-e. A második feltételes hivatkozás a `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` kifejezést használja annak szűrésére, hogy a **Start-AzVm** tevékenység nem tudja elindítani a virtuális gépet.

![Feltételes hivatkozás – példa](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Minden olyan tevékenység, amely az első hivatkozást követi, és a **Get-AzureVM** tevékenység kimenetét használja, csak a **Get-AzureVM** futtatásakor elindított virtuális gépeket kérdezi le. A második hivatkozást követő tevékenységek csak a **Get-AzureVM** futtatásakor leállított virtuális gépeket kapják meg. A harmadik hivatkozás utáni bármely tevékenység lekéri az összes virtuális gépet a futó állapottól függetlenül.

### <a name="junctions"></a>Csomópontok

A Junction egy különleges tevékenység, amely addig vár, amíg az összes bejövő ág be nem fejeződik. Ez lehetővé teszi, hogy a runbook párhuzamosan futtasson több tevékenységet, és győződjön meg arról, hogy az összes befejeződött, mielőtt továbblép.

Míg a csomópontok korlátlan számú bejövő hivatkozást tartalmazhatnak, csak az egyik kapcsolat lehet egy folyamat. A bejövő szekvenciális hivatkozások száma nincs korlátozva. A csomópontot több bejövő folyamattal rendelkező kapcsolattal is létrehozhatja, és mentheti a runbook, de a futtatáskor sikertelen lesz.

Az alábbi példa egy olyan runbook részét képezi, amely virtuális gépeket indít el, miközben a javítások letöltését is alkalmazza az adott gépekre. Egy elágazás használatával biztosítja, hogy mindkét folyamat befejeződik, mielőtt a runbook folytatja.

![Szinkronizációs pont](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Ciklusok

Egy ciklus akkor jön létre, amikor egy cél tevékenység a forrás tevékenységre hivatkozik, vagy egy másik tevékenységre, amely végül a forráshoz kapcsolódik. A grafikus szerzői műveletek jelenleg nem támogatják a ciklusokat. Ha a runbook ciklust tartalmaz, a rendszer megfelelően menti, de hibaüzenetet kap a futtatásakor.

![Ciklikus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>Tevékenységek közötti adatmegosztás

A kimenő kapcsolattal rendelkező tevékenységek által a runbook adatbuszba megjelenő adatokat a rendszer a megadott értékre írja. A runbook bármely tevékenysége a adatbuszba lévő adatok használatával feltöltheti a paramétereket, vagy belefoglalhatja a szkript kódjába. Egy tevékenység elérheti a munkafolyamatban lévő korábbi tevékenységek kimenetét.

Az adatok adatbuszba való írásának módja a tevékenységre mutató hivatkozás típusától függ. A feldolgozási kapcsolatok esetében az adatokat több objektumként jeleníti meg a rendszer. A sorozatokra mutató hivatkozás esetén az adatokat tömbként adja eredményként. Ha csak egy érték van, a kimenet egyelemű tömbként történik.

A runbook kétféleképpen férhet hozzá a adatbuszba lévő adataihoz: 
* Tevékenység kimeneti adatforrásának használata.
* Használjon egy PowerShell-kifejezés adatforrását.

Az első mechanizmus egy tevékenység-kimeneti adatforrást használ egy másik tevékenység paramétereinek feltöltéséhez. Ha a kimenet egy objektum, a runbook egyetlen tulajdonságot is megadhat.

![tevékenység kimenete](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

A második adatelérési mechanizmus egy *ActivityOutput* változóval rendelkező tevékenység kimenetét kérdezi le egy PowerShell-kifejezés adatforrásában vagy egy munkafolyamat-parancsfájl tevékenységben az alább látható szintaxis használatával. Ha a kimenet egy objektum, a runbook egyetlen tulajdonságot is megadhat.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>Az ellenőrzőpontok

Az [ellenőrzőpontokat](automation-powershell-workflow.md#checkpoints) grafikus PowerShell-munkafolyamatok runbook is beállíthatja, ha az **ellenőrzőpont runbook** lehetőséget választja bármely tevékenységen. Ez azt eredményezi, hogy a tevékenység futtatása után ellenőrzőpontot kell beállítani.

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

Az ellenőrzőpontok csak grafikus PowerShell-munkafolyamatok runbookok engedélyezettek, és grafikus runbookok nem érhetők el. Ha a runbook Azure-parancsmagokat használ, akkor az összes ellenőrzőponttal ellátott tevékenységet követnie kell egy **AzAccount** tevékenységgel. A csatlakozási művelet abban az esetben használatos, ha a runbook fel van függesztve, és egy másik feldolgozón újra kell indítani az ellenőrzőpontból.

## <a name="runbook-input-and-output"></a>Runbook-bemenet és-kimenet

### Runbook bemenete<a name="runbook-input"></a>

A runbook a runbook a Azure Portal vagy egy másik runbook keresztül indító felhasználótól kell megadnia, ha az aktuális alkalmazást gyermekként használják. Például egy virtuális gépet létrehozó runbook esetében előfordulhat, hogy a felhasználónak meg kell adnia a virtuális gép nevét, valamint a runbook minden egyes indításakor a többi tulajdonságot.

A runbook egy vagy több bemeneti paraméter definiálásával fogadja a bemenetet. A felhasználó a runbook indításakor minden alkalommal megadja a paraméterek értékeit. Amikor a felhasználó a Azure Portal használatával elindítja a runbook, a rendszer a felhasználótól a runbook által támogatott összes bemeneti paraméter értékének megadását kéri.

A runbook készítésekor a runbook eszköztár **bemenet és kimenet** gombjára kattintva elérheti a bemeneti paramétereit. Ekkor megnyílik a bemeneti és kimeneti vezérlő, ahol szerkesztheti a meglévő bemeneti paramétereket, vagy létrehozhat egy újat a **bemenet hozzáadása**lehetőségre kattintva.

![Bemenet hozzáadása](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Az egyes bemeneti paramétereket az alábbi táblázat tulajdonságai határozzák meg:

| Tulajdonság | Leírás |
|:--- |:--- |
| Name (Név) | Kötelező. A paraméter neve. A névnek egyedinek kell lennie a runbook belül. Betűvel kell kezdődnie, és csak betűket, számokat és aláhúzást tartalmazhat. A név nem tartalmazhat szóközt. |
| Leírás |Választható. A bemeneti paraméter céljának leírása. |
| Típus | Választható. A paraméter értékének várt adattípus. A Azure Portal minden paraméter esetében megfelelő vezérlést biztosít az adattípushoz, amikor rákérdez a bemenetre. A támogatott paraméterek a következők: string, Int32, Int64, decimális, Boolean, DateTime és Object. Ha nincs kiválasztva adattípus, a rendszer az alapértelmezett karakterláncot adja meg.|
| Kötelező | Választható. Ez a beállítás határozza meg, hogy meg kell-e adni egy értéket a paraméterhez. Ha az **Igen**lehetőséget választja, a runbook indításakor meg kell adni egy értéket. Ha a **nem**lehetőséget választja, a runbook indításakor nem szükséges érték, és az alapértelmezett érték is használható. A runbook nem indítható el, ha nem ad meg értéket minden olyan kötelező paraméterhez, amelyhez nincs megadva alapértelmezett érték. |
| Alapértelmezett érték | Választható. Egy paraméterhez használt érték, ha az egyiket nem adja át a rendszer a runbook indításakor. Alapértelmezett érték beállításához válassza az **Egyéni**lehetőséget. Válassza a **nincs** lehetőséget, ha nem szeretné megadni az alapértelmezett értéket. |

### <a name="runbook-output"></a>Runbook kimenete

A grafikus szerzői műveletek olyan tevékenységek által létrehozott adatokat mentenek, amelyek nem rendelkeznek kimenő hivatkozással a [runbook kimenetéhez](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages). A rendszer menti a kimenetet a runbook-feladatokkal, és a szülő runbook számára elérhető, amikor a runbook gyermekként használják.

## <a name="powershell-expressions"></a>PowerShell-kifejezések

A grafikus szerzői műveletek egyik előnye, hogy lehetővé teszi, hogy runbook hozzon létre a PowerShell minimális ismeretével. Jelenleg azonban ismernie kell egy kicsit a PowerShellt bizonyos [Paraméterek értékének](#activities) és a [kapcsolati feltételek](#links-and-workflow)beállításának a megadásához. Ez a szakasz a PowerShell-kifejezések gyors bevezetését ismerteti. A PowerShell részletes adatai a [Windows PowerShell-lel való parancsfájlkezelésben](https://technet.microsoft.com/library/bb978526.aspx)érhetők el.

### <a name="powershell-expression-data-source"></a>PowerShell-kifejezés adatforrása

A PowerShell-kifejezéseket adatforrásként használva feltöltheti egy [tevékenység-paraméter](#activities) értékét a PowerShell-kód eredményeivel. A kifejezés lehet egyetlen sornyi kód, amely egy egyszerű függvényt vagy több olyan sort végez, amely valamilyen összetett logikát hajt végre. Egy olyan parancs kimenete, amely nincs változóhoz rendelve, kimenetet ad a paraméter értékének.

A következő parancs például az aktuális dátumot adja eredményül.

```powershell-interactive
Get-Date
```

A következő kódrészlet egy karakterláncot hoz létre az aktuális dátumból, és hozzárendeli azt egy változóhoz. A kód a változó tartalmát elküldi a kimenetnek.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Az alábbi parancsok kiértékelik az aktuális dátumot, és egy olyan karakterláncot adnak vissza, amely azt jelzi, hogy az aktuális nap hétvége vagy hétköznap.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>tevékenység kimenete

Ha a runbook egy korábbi tevékenység kimenetét szeretné használni, használja a *ActivityOutput* változót az alábbi szintaxissal.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Létrehozhat például egy olyan tulajdonságot, amely egy virtuális gép nevét igényli. Ebben az esetben a runbook a következő kifejezést használhatja.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Ha a tulajdonsághoz csak egy név helyett a virtuálisgép-objektum szükséges, a runbook a következő szintaxissal adja vissza a teljes objektumot.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

A runbook egy tevékenység kimenetét egy összetettebb kifejezésben, például a következő módon használhatja. Ez a kifejezés összefűzi a szöveget a virtuális gép nevével.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>Feltételek

Az [összehasonlító operátorok](https://technet.microsoft.com/library/hh847759.aspx) segítségével hasonlíthatja össze az értékeket, vagy meghatározhatja, hogy egy érték megfelel-e a megadott mintának. Az összehasonlítás igaz vagy hamis értéket ad vissza.

A következő feltétel például meghatározza, hogy a **Get-AzureVM** nevű tevékenységből származó virtuális gép jelenleg le van-e állítva.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

A következő feltétel határozza meg, hogy ugyanaz a virtuális gép a leállítotttól eltérő állapotban van-e.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

A runbook több feltételt is csatlakoztathat egy [logikai operátor](https://technet.microsoft.com/library/hh847789.aspx)használatával, például: **-és** vagy **-vagy**. A következő feltétel például ellenőrzi, hogy az előző példában szereplő virtuális gép **leállított** vagy **leállítási**állapotban van-e.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>Szórótáblában

A [szórótáblában](https://technet.microsoft.com/library/hh847780.aspx) olyan név-érték párok, amelyek értékek halmazának visszaadására használhatók. Előfordulhat, hogy a szórótábla egy szótár néven is látható. Bizonyos tevékenységek tulajdonságai egy egyszerű érték helyett egy szórótábla várnak.

Hozzon létre egy szórótábla a következő szintaxis használatával. Tetszőleges számú bejegyzést tartalmazhat, de mindegyiket egy név és egy érték határozza meg.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

A következő kifejezés például létrehoz egy szórótábla, amelyet egy olyan tevékenységi paraméter adatforrásaként használhat, amely egy internetes keresés szórótábla vár.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Az alábbi példa a **Get Twitter-kapcsolatok** nevű tevékenység kimenetét használja egy szórótábla feltöltéséhez.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>Hitelesítés az Azure-erőforrásokkal

Az Azure-erőforrásokat kezelő Azure Automation runbookok az Azure-ba történő hitelesítést igénylik. A [futtató fiók](automation-create-runas-account.md)(más néven egyszerű szolgáltatásnév) az az alapértelmezett mechanizmus, amelyet az Automation runbook az előfizetéshez tartozó Azure Resource Manager erőforrások elérésére használ. Ezt a funkciót grafikus runbook is hozzáadhatja úgy, hogy hozzáadja a **azurerunasconnection elemet** kapcsolódási eszközt, amely a PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) parancsmagot használja a vászonhoz. A [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmagot is hozzáadhatja. Ezt a forgatókönyvet a következő példa szemlélteti.

![Futtató hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

A **Get futtató tevékenység**vagy a **Get-AutomationConnection**a **azurerunasconnection elemet**nevű állandó értékű adatforrással van konfigurálva.

![Futtató kapcsolatok konfigurációja](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

A következő tevékenység, a **AzAccount**, hozzáadja a hitelesített futtató fiókot a runbook való használathoz.

![A AzAccount paraméter beállítása](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>PowerShell-runbookok esetében a **Add-AzAccount** és a **Add-AzureRMAccount** aliasok a **csatlakozási-AzAccount**. Vegye figyelembe, hogy ezek az aliasok nem érhetők el a grafikus runbookok. A grafikus runbook saját maga is használhatják A **AzAccount** .

A **APPLICATIONID**, a **CERTIFICATETHUMBPRINT**és a **TENANTID**paraméternél adja meg a mező elérési útjának tulajdonságának nevét, mivel a tevékenység több tulajdonsággal rendelkező objektumot ad eredményül. Ellenkező esetben a runbook végrehajtása sikertelen lesz a hitelesítés megkísérlése során. Legalább a futtató fiókkal kell hitelesítenie a runbook.

Egyes előfizetők egy [Azure ad-felhasználói fiókkal](automation-create-aduser-account.md) hoznak létre Automation-fiókot az Azure klasszikus üzembe helyezésének vagy Azure Resource Manager erőforrásainak kezeléséhez. Ezen előfizetők visszamenőleges kompatibilitásának fenntartása érdekében a runbook használandó hitelesítési mechanizmus az **Add-AzureAccount** parancsmag, amely egy [hitelesítőadat-eszközzel](automation-credentials.md)rendelkezik. Az eszköz az Azure-fiókhoz hozzáféréssel rendelkező Active Directory felhasználót jelöl.

Ezt a funkciót a grafikus runbook is engedélyezheti, ha egy hitelesítőadat-objektumot ad hozzá a vászonhoz, majd egy **Add-AzureAccount** tevékenységet, amely a bemeneti adatokhoz a hitelesítő eszközt használja. Tekintse meg a következő példát.

![Hitelesítési tevékenységek](media/automation-graphical-authoring-intro/authentication-activities.png)

A runbook az egyes ellenőrzőpontok elején és után kell hitelesítenie. Ezért egy **AzureAccount** tevékenységet kell használnia egy **ellenőrzőpont-munkafolyamat** tevékenység után. Nem kell további hitelesítő adatokat használnia.

![tevékenység kimenete](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>Grafikus runbook exportálása és importálása

Csak grafikus runbook közzétett verzióját exportálhatja. Ha a runbook még nem tették közzé, az **Exportálás** gomb le lesz tiltva. Amikor az **Exportálás** gombra kattint, a runbook letölti a helyi számítógépre. A fájl neve megegyezik a runbook nevével **. graphrunbook** kiterjesztéssel.

Egy grafikus vagy grafikus PowerShell munkafolyamat runbook-fájl importálásához válassza az **Importálás** lehetőséget a runbook hozzáadásakor. Ha kijelöli az importálandó fájlt, megtarthatja ugyanazt a nevet, vagy megadhat egy újat. A **Runbook típusa** mező megjeleníti a Runbook típusát, miután kivizsgálta a kijelölt fájlt. Ha olyan eltérő típust próbál kijelölni, amely nem megfelelő, a grafikus szerkesztő egy üzenetet jelez, hogy lehetséges ütközések vannak, és szintaktikai hibák fordultak elő az átalakítás során.

![Runbook importálása](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

## <a name="testing-a-graphical-runbook"></a>Grafikus runbook tesztelése

A Azure Automation minden grafikus runbook rendelkezik egy Piszkozat verziószámmal és egy közzétett verzióval. Csak a közzétett verziót futtathatja, miközben csak a Piszkozat verziójának szerkesztését végezheti el. A közzétett verzióra nincsenek hatással a piszkozat verzióban végrehajtott módosítások. Ha a Piszkozat verziója készen áll a használatra, a rendszer közzéteszi, amely felülírja a jelenlegi közzétett verziót a Piszkozat verziójával.

A Azure Portal runbook tesztelésével a közzétett verzió változatlanul hagyása mellett is tesztelhető. Azt is megteheti, hogy a közzététel előtt tesztel egy új runbook, hogy ellenőrizni tudja, hogy a runbook megfelelően működik-e a verziók visszahelyezése előtt. A runbook tesztelése végrehajtja a Piszkozat verzióját, és gondoskodik arról, hogy az általa végrehajtott műveletek befejeződik. A rendszer nem hoz létre munkaelőzményeket, de a test output (kimenet) panel megjeleníti a kimenetet.

Nyissa meg a grafikus runbook tartozó tesztelési vezérlőt úgy, hogy megnyitja a runbook a szerkesztéshez, majd rákattint a **teszt panelre**. A teszt vezérlő kéri a bemeneti paramétereket, és a **Start**gombra kattintva elindíthatja a runbook.

## <a name="publishing-a-graphical-runbook"></a>Grafikus runbook közzététele

Tegye közzé a grafikus runbook úgy, hogy megnyitja a runbook a szerkesztéshez, majd a **Közzététel**lehetőségre kattint. A runbook lehetséges állapota a következő:

* Új – a runbook még nem tették közzé. 
* Közzétett – a runbook közzé lett téve.
* A Szerkesztés – a runbook a közzététel után lett szerkesztve, és a Piszkozat és a közzétett verziók eltérőek.

![Runbook-állapotok](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Lehetősége van arra, hogy visszaállítson egy runbook közzétett verzióját. A művelet eldönti, hogy milyen módosításokat hajtottak végre a runbook legutóbbi közzététele óta. Lecseréli a runbook Piszkozat verzióját a közzétett verzióra.

## <a name="next-steps"></a>További lépések

* A PowerShell-alapú munkafolyamat-forgatókönyvekre vonatkozó első lépések leírását [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md) témakör ismerteti.
* A grafikus runbookok megkezdéséhez tekintse meg [az első grafikus runbook](automation-first-runbook-graphical.md).
* Ha többet szeretne megtudni a runbook típusairól és azok előnyeiről és korlátairól, tekintse meg a [Azure Automation runbook-típusok](automation-runbook-types.md)című témakört.
* Az Automation futtató fiók használatával végzett hitelesítés megismeréséhez lásd: az Azure-beli [futtató fiók konfigurálása](automation-sec-configure-azure-runas-account.md).
