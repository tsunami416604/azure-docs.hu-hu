---
title: Az első PowerShell-runbook Azure Automation
description: Ez az oktatóanyag bemutatja egy egyszerű PowerShell-forgatókönyv létrehozását, tesztelését és közzétételét.
keywords: azure powershell, powershell-szkript oktatóanyag, powershell automation
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: e0c48137f5eecc96b6e7b1cbce5f0c683b2a976a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367312"
---
# <a name="my-first-powershell-runbook"></a>Az első PowerShell-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks). Kezdje egy egyszerű runbook, amelyet tesztelni és közzétenni, miközben megtudhatja, hogyan követheti nyomon a runbook-feladatok állapotát. Ezután módosítsa a runbook, hogy ténylegesen kezelhesse az Azure-erőforrásokat, ebben az esetben egy Azure-beli virtuális gépet indítson el. Fejezze be az oktatóanyagot, hogy a runbook robusztusabb legyen a runbook paraméterek hozzáadásával.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-quickstart-create-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. A gép leállítása és elindítása óta nem lehet üzemi virtuális gép.
* Szükség esetén [importálja az Azure-modulokat](shared-resources/modules.md) vagy a [frissítési modulokat](automation-update-azure-modules.md) a használt parancsmagok alapján.

## <a name="differences-from-powershell-workflow-runbooks"></a>A PowerShell-munkafolyamat runbookok származó különbségek

A PowerShell-runbookok a PowerShell-munkafolyamat runbookok azonos életciklussal, képességekkel és felügyelettel rendelkeznek. Van azonban néhány eltérés és korlátozás.

| Jellemző  | PowerShell-Runbookok | PowerShell-munkafolyamat Runbookok |
| ------ | ----- | ----- |
| Sebesség | Gyors Futtatás, ha nem használ fordítási lépést. | Lassabban fusson. |
| Az ellenőrzőpontok | Az ellenőrzőpontok nem támogatottak. A PowerShell-runbook csak a kezdetektől folytathatja a műveletet. | Használjon ellenőrzőpontokat, amelyek lehetővé teszik, hogy a munkafüzetek bármely pontról folytassanak műveletet. |
| Parancs végrehajtása | Csak a soros végrehajtás támogatása. | Támogatja mind a soros, mind a párhuzamos végrehajtást.|
| RunSpace | Egyetlen RunSpace mindent futtat egy parancsfájlban. | Egy tevékenységhez, parancshoz vagy parancsfájl-blokkhoz külön RunSpace lehet használni. |

Ezen eltérések mellett a PowerShell-runbookok a PowerShell-munkafolyamatok runbookok származó [szintaktikai különbségeket](https://technet.microsoft.com/magazine/dn151046.aspx) is tartalmaz.

## <a name="step-1---create-runbook"></a>1\. lépés – Runbook létrehozása

Először hozzon létre egy egyszerű runbook, amely kiírja a `Hello World`szöveget.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. A runbookok listájának megnyitásához válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
3. Hozzon létre egy új runbook a **Runbook létrehozása**lehetőség kiválasztásával.
4. Adja a forgatókönyvnek a **MyFirstRunbook-PowerShell** nevet.
5. Ebben az esetben létre kell hoznia egy PowerShell- [runbook](automation-runbook-types.md#powershell-runbooks). Válassza a **PowerShell** lehetőséget a **Runbook típushoz**.
6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2\. lépés – Kód hozzáadása a forgatókönyvhöz

Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben az oktatóanyagban közvetlenül a runbook írja be a kódot.

1. A runbook jelenleg üres. Írja be a `Write-Output "Hello World"` értéket a parancsfájl törzsében.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a> 3. lépés – a runbook tesztelése

Mielőtt közzéteszi a runbook, hogy az éles környezetben elérhető legyen, tesztelje, hogy megfelelően működik-e. A runbook tesztelése a Piszkozat verzióját futtatja, és lehetővé teszi a kimenet interaktív megjelenítését.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Vegye figyelembe, hogy létrejön egy [runbook-feladatok](automation-runbook-execution.md) , és az állapota megjelenik a panelen.

   A feladatok állapota `Queued`ként indul el, ami azt jelzi, hogy a feladatot a felhőben lévő runbook-feldolgozó elérhetővé válására vár. Az állapot úgy változik, hogy `Starting`, amikor egy feldolgozó a feladatot kéri. Végül az állapot `Running` válik, amikor a runbook ténylegesen elindul.

4. Ha a runbook-feladatok befejeződik, a teszt ablaktábla megjeleníti a kimenetet. Ebben az esetben a `Hello World`jelenik meg.

   ![Teszt panel kimenete](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4\. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook még mindig Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Görgessen balra a runbook megtekintéséhez a Runbookok lapon, és vegye figyelembe, hogy a **szerzői állapot** értéke **közzétételre**van állítva.
1. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell** panel megtekintéséhez.
   
   A felső beállítások lehetővé teszik a runbook elindítását, a jövőbeli kezdési idő beírását, vagy [webhook](automation-webhooks.md) létrehozását, hogy a runbook http-hívással is elindítható legyen.
1. Kattintson a **Start** gombra, majd az **Igen** gombra, amikor a rendszer felszólítja a runbook elindítására. 
1. Megnyílik a létrehozott runbook-feladatokhoz tartozó feladatok ablaktábla. Bár bezárhatja ezt a panelt, hagyja megnyitva most, hogy megtekintse a feladatok előrehaladását. A feladatok állapota a **feladatok összegzése**területen látható, a lehetséges állapotok pedig a runbook teszteléséhez szükségesek.

   ![Feladat összegzése](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Ha a runbook állapota `Completed`látható, kattintson a **kimenet** elemre a kimenet lap megnyitásához, ahol megtekintheti `Hello World` megjeleníthető.

   ![Feladat kimenete](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. A kimeneti oldal bezárásához.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti adatfolyamban csak `Hello World` jelenik meg.

    Vegye figyelembe, hogy a streamek panel más streameket jeleníthet meg egy runbook-feladatokhoz, például a részletes és a hiba-adatfolyamokhoz, ha a runbook ezeket írja.

   ![Minden napló](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. A **MyFirstRunbook-PowerShell** lapra való visszatéréshez zárjuk be a streamek panelt és a feladatok panelt.
1. A **részletek**területen kattintson a **feladatok** elemre a runbook feladatok lapjának megnyitásához. Ezen az oldalon a runbook által létrehozott összes feladat szerepel. Csak egy feladatot kell látnia, mert csak egyszer futtatta a feladatot.

   ![Feladatlista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Kattintson a feladatokra, hogy megnyissa a runbook elindításakor megtekintett feladatok ablaktábláját. Ezen ablaktábla használatával megtekintheti a runbook létrehozott feladatok részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5\. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ehhez a runbook képesnek kell lennie hitelesíteni az Automation-fiók létrehozásakor automatikusan létrehozott futtató fiók használatával.

Ahogy az alábbi példában is látható, a futtató kapcsolat a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) parancsmaggal történik. Ha több előfizetésen keresztül kezeli az erőforrásokat, akkor a [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)`AzContext` paramétert kell használnia.

> [!NOTE]
> A PowerShell-runbookok esetében a `Add-AzAccount` és az `Add-AzureRMAccount` a `Connect-AzAccount`aliasa. Ezeket a parancsmagokat használhatja, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verzióra. Előfordulhat, hogy frissítenie kell a modulokat akkor is, ha nemrég létrehozott egy új Automation-fiókot.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. A **MyFirstRunbook-PowerShell** oldalon a **Szerkesztés** gombra kattintva nyissa meg a szöveges szerkesztőt.
1. Nincs szükség a `Write-Output` sorra. Csak ugorjon, és törölje.
1. Írja be vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókkal.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Kattintson a **teszt panelre** , hogy tesztelni tudja a runbook.
1. Kattintson az **Indítás** gombra a teszt elindításához. Ha befejeződik, a következőhöz hasonló kimenetnek kell megjelennie, amely a fiók alapvető információit jeleníti meg. Ez a kimenet megerősíti, hogy a futtató fiók érvényes.

   ![Hitelesítés](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6\. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesítést végez az Azure-előfizetésében, kezelheti az erőforrásokat. Hozzunk létre egy parancsot egy virtuális gép indításához. Bármelyik virtuális gépet kiválaszthatja az Azure-előfizetésében, és most parancsmagba ezt a nevet a runbook.

1. A runbook-szkripthez adja hozzá a [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) parancsmagot a virtuális gép elindításához. Ahogy az alábbi ábrán is látható, a parancsmag elindítja a `VMName` nevű virtuális gépet és egy `ResourceGroupName`nevű erőforráscsoportot.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Mentse a runbook, majd kattintson a **teszt panelre** , hogy tesztelni tudja.
1. A teszt **elindításához** kattintson a Start gombra. A művelet befejezését követően győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-7---add-an-input-parameter"></a>7\. lépés – bemeneti paraméter hozzáadása

A runbook jelenleg elindítja a virtuális gépet, amelyet a runbook hardcoded. A runbook akkor hasznos, ha a runbook indításakor megadja a virtuális gépet. Adja hozzá a runbook bemeneti paramétereit a funkció megadásához.

1. A szöveges szerkesztőben módosítsa a `Start-AzVM` parancsmagot úgy, hogy az `VMName` és `ResourceGroupName`paraméterekhez változókat használjon. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
1. Zárja be a Teszt panelt.
1. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
1. Állítsa le a korábban elindított virtuális gépet.
1. Kattintson az **Indítás** gombra a forgatókönyv elindításához. 
1. Írja be a **VMNAME** és a **RESOURCEGROUPNAME** értékeket az elindítani kívánt virtuális géphez, majd kattintson **az OK**gombra.<br><br> ![Paraméter átadása](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. A runbook befejezésekor ellenőrizze, hogy elindult-e a virtuális gép.

## <a name="next-steps"></a>Következő lépések

* A PowerShell-lel kapcsolatos további információk, beleértve a nyelvi referenciákat és a tanulási modulokat, lásd: [PowerShell-dokumentumok](/powershell/scripting/overview).
* A grafikus runbookok megkezdéséhez tekintse meg [az első grafikus runbook](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekre vonatkozó első lépések leírását [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md) témakör ismerteti.
* Ha többet szeretne megtudni a runbook típusairól és azok előnyeiről és korlátairól, tekintse meg a [Azure Automation runbook-típusok](automation-runbook-types.md)című témakört.
* További információ a PowerShell-parancsfájl támogatásáról: [natív PowerShell-parancsfájl-támogatás Azure Automationban](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
