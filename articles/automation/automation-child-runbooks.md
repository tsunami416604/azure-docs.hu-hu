---
title: Gyermekrunbookok az Azure Automationben
description: Bemutatja a különböző módszereket egy runbook indítása az Azure Automation egy másik runbook és az információk megosztása között.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367363"
---
# <a name="child-runbooks-in-azure-automation"></a>Gyermekrunbookok az Azure Automationben

Az Azure Automation ajánlott újrafelhasználható, moduláris runbookok írása egy különálló funkcióval, amely más runbookok által hívott. A szülő runbookok gyakran meghívja egy vagy több gyermek runbookok a szükséges funkciók végrehajtásához. A gyermek runbook kétféleképpen hívhatja meg a gyermek runbookot, és különböző különbségeket kell megértenie, hogy meg tudja határozni, melyik a legjobb a forgatókönyvek számára.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Gyermek runbook meghívása szövegközi végrehajtással

Egy runbook inline meghívásához egy másik runbook, használja a runbook nevét, és adja meg a paramétereket, mint ha egy tevékenység vagy egy parancsmag használata. Az azonos Automation-fiókban lévő összes runbook az összes többi számára elérhető az ilyen módon használható. A szülő runbook megvárja, amíg a gyermek runbook befejezéséhez, mielőtt a következő sorba, és a kimeneti visszatér közvetlenül a szülő.

A beágyazottan indított runbook ugyanazt a feladatot futtatja, mint a szülőrunbook. Nincs jelzés a gyermek runbook feladatelőzményei. A gyermek runbook kivételei és streamkimenetei a szülőhöz vannak társítva. Ez a viselkedés kevesebb feladatot eredményez, és megkönnyíti a nyomon követhető és a hibaelhárítást.

Runbook közzétételekor a meghívott gyermek runbookokat már közzé kell tenni. Ennek az az oka, hogy az Azure Automation létrehoz egy társítást bármely gyermek runbookok, amikor egy runbook fordítása. Ha a gyermek runbookok még nem tették közzé, a szülő runbook úgy tűnik, hogy megfelelően közzé, de létrehoz egy kivételt, amikor elindul. Ha ez történik, újra közzéteheti a szülő runbook megfelelően hivatkozhat a gyermek runbookok. Nem kell újra közzétennie a szülő runbookot, ha bármelyik gyermek runbook módosult, mert a társítás már létrejött.

A szövegközi nevű gyermekrunbook paraméterei bármilyen típusúak lehetnek, beleértve az összetett objektumokat is. Nincs [JSON-szerializálás,](start-runbooks.md#runbook-parameters)ahogy az Azure Portalon vagy a [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) parancsmaggal, amikor elindítja a runbookot.

### <a name="runbook-types"></a>Runbook-típusok

Mely runbook-típusok hívhatják egymást?

* A [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) és a [grafikus runbook](automation-runbook-types.md#graphical-runbooks) hívhatja egymást inline, mivel mindkettő PowerShell-alapú.
* A [PowerShell-munkafolyamat-runbook](automation-runbook-types.md#powershell-workflow-runbooks) és a grafikus PowerShell-munkafolyamat-runbook oka lehet egymást inline, mivel mindkettő PowerShell-munkafolyamat-alapú.
* A PowerShell-típusok és a PowerShell-munkafolyamat-típusok nem hívhatják `Start-AzAutomationRunbook`meg egymást inline, és használnia kell.

Mikor számít a rendelés közzététele?

A runbookok közzétételi sorrendje csak a PowerShell-munkafolyamat és a grafikus PowerShell-munkafolyamat runbookok esetében fontos.

Amikor a runbook hív egy grafikus vagy A PowerShell-munkafolyamat gyermek runbook segítségével inline végrehajtás, a runbook nevét használja. A névnek `.\\` azzal kell kezdődnie, hogy a parancsfájl a helyi könyvtárban található-e.

### <a name="example"></a>Példa

A következő példa egy összetett objektumot, egy egész értéket és egy logikai értéket elfogadó tesztgyermek-runbookot indít el. A rendszer egy változóhoz társítja a gyermekrunbook kimenetét. Ebben az esetben a gyermek runbook egy PowerShell-munkafolyamat runbook.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Íme, a PowerShell runbook használatával a PowerShell runbook, mint a gyermek.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Gyermekrunbook indítása parancsmag használatával

> [!IMPORTANT]
> Ha a runbook meghívja a `Start-AzAutomationRunbook` gyermek runbook `Wait` a parancsmag a paraméterrel, és a gyermek runbook létrehoz egy objektum eredménye, a művelet hibát észlelhet. A hiba kerülő használata, olvassa el [az objektumkimenettel rendelkező gyermek runbookok című témakört,](troubleshoot/runbooks.md#child-runbook-object) amely ből megtudhatja, hogyan valósíthatja meg az eredmények lekérdezéséhez szükséges logikát a [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) rekordparancsmag használatával.

A `Start-AzAutomationRunbook` runbook okaaaaaaaaaa a [Windows PowerShell használatával való futtatási könyv indítása.](start-runbooks.md#start-a-runbook-with-powershell) Ehhez a parancsmaghoz két használati mód létezik. Az egyik módban a parancsmag visszaadja a feladat azonosítóját, amikor a feladat jön létre a gyermek runbook. A másik módban, amelyet a parancsfájl lehetővé tesz a *Várakozás* paraméter megadásával, a parancsmag megvárja, amíg a gyermekfeladat befejeződik, és visszaadja a gyermek runbook kimenetét.

A feladat egy gyermek runbook egy parancsmaggal indított fut külön a szülő runbook feladat. Ez a viselkedés több feladatot eredményez, mint a runbook inline indítása, és megnehezíti a feladatok nyomon követését. A szülő aszinkron módon több gyermek runbookot is elindíthat anélkül, hogy megvárnák az egyes futtatási adatok befejezését. Ehhez a párhuzamos végrehajtáshoz a gyermek runbookok inline, a szülő runbook kell használnia a [párhuzamos kulcsszót.](automation-powershell-workflow.md#parallel-processing)

A gyermek runbook kimenete az időzítés miatt nem tér vissza megbízhatóan a szülő runbookba. Ezenkívül előfordulhat, hogy `$VerbosePreference` `$WarningPreference`a változók, például a , és mások nem lesznek propagálva a gyermek runbookokszámára. Ezek elkerülése érdekében ezeket a problémákat, elindíthatja a gyermek runbookok külön Automation-feladatok használatával `Start-AzAutomationRunbook` a `Wait` paraméter. Ez a módszer blokkolja a szülő runbook, amíg a gyermek runbook befejeződött.

Ha nem szeretné, hogy a szülő runbook le legyen tiltva a `Start-AzAutomationRunbook` várakozás, elindíthatja a gyermek runbook használatával a `Wait` paraméter nélkül. Ebben az esetben a runbook kell [használnia Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) a feladat befejezéséhez. Az eredmények lekéréséhez [a Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) és a [Get-AzAutomationJobOutputRecord is](/powershell/module/az.automation/get-azautomationjoboutputrecord) használnia kell.

A parancsmaggal indított gyermekrunbook paraméterei kivonatként vannak megadva, a [Runbook paramétereiben leírtak szerint.](start-runbooks.md#runbook-parameters) Csak egyszerű adattípusok használhatók. Ha a runbook rendelkezik összetett adattípusú paraméterrel, beágyazottan kell meghívni.

Az előfizetési környezet elveszhet, amikor a gyermek runbookok külön feladatokként. Ahhoz, hogy a gyermek runbook az Az modul parancsmagokat hajtson végre egy adott Azure-előfizetéssel, a gyermeknek a szülő runbooktól függetlenül hitelesítenie kell magát ehhez az előfizetéshez.

Ha az ugyanazon Automation-fiókon belüli feladatok egynél több előfizetéssel működnek, az egyik feladatelőfizetés kiválasztása módosíthatja a többi feladat aktuálisan kiválasztott előfizetési környezetét. A helyzet elkerülése `Disable-AzContextAutosave –Scope Process` érdekében használja az egyes runbookok elején. Ez a művelet csak menti a környezetet, hogy a runbook végrehajtása.

### <a name="example"></a>Példa

A következő példa elindítja a gyermek runbook paraméterekkel, majd `Start-AzAutomationRunbook` megvárja, `Wait` amíg befejeződik a parancsmag használatával a paraméterrel. Miután elkészült, a példa összegyűjti a parancsmag kimenetét a gyermek runbook. A `Start-AzAutomationRunbook`használata érdekében a parancsfájlnak hitelesítenie kell magát az Azure-előfizetéshez.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Gyermek runbook hívási módszereinek összehasonlítása

Az alábbi táblázat összefoglalja a különbségeket a két módja, hogy egy runbook egy másik runbook.

|  | Beágyazott | Parancsmag |
|:--- |:--- |:--- |
| Feladat |A gyermekrunbookok a szülővel azonos feladatban futnak. |A rendszer külön feladatot hoz létre a gyermekrunbookhoz. |
| Futtatási |A folytatás előtt a szülőrunbook megvárja, hogy a gyermekrunbook befejeződjön. |A szülő runbook azonnal folytatódik, miután a gyermek runbook *elindult, vagy* a szülő runbook várja, amíg a gyermek feladat befejeződik. |
| Kimenet |A szülőrunbook közvetlenül lekérheti a gyermekrunbook kimenetét. |A szülő runbooknak le kell kérnie a kimenetet a gyermek runbook-feladatból, *vagy* a szülő runbook közvetlenül lekérheti a kimenetet a gyermek runbookból. |
| Paraméterek |A gyermekrunbook paramétereinek értékeit külön kell meghatározni, és bármilyen adattípus használható. |A gyermek runbook-paraméterek értékeit egyetlen kivonattá kell egyesíteni. Ez a kivonatcsak olyan egyszerű, tömb- és objektumadat-típusokat tartalmazhat, amelyek JSON-szerializálást használnak. |
| Automation-fiók |A szülő runbook csak ugyanabban az Automation-fiókban használhatja a gyermek runbookot. |A szülő runbookok bármely Automation-fiókból, ugyanabból az Azure-előfizetésből, sőt egy másik előfizetésből is használhatják a gyermekrunbookokat, amelyhez kapcsolat kapcsolódik. |
| Közzététel |A gyermekrunbookot a szülőrunbook közzététele előtt kell közzétenni. |A gyermek runbook bármikor közzé lesz téve a szülő runbook indítása előtt. |

## <a name="next-steps"></a>További lépések

* [Runbook indítása az Azure Automationben](start-runbooks.md)
* [Runbook-kimenet és üzenetek az Azure Automationben](automation-runbook-output-and-messages.md)