---
title: Az első PowerShell-runbook az Azure Automationben
description: Ez az oktatóanyag bemutatja egy egyszerű PowerShell-forgatókönyv létrehozását, tesztelését és közzétételét.
keywords: azure powershell, powershell-szkript oktatóanyag, powershell automation
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0df2680a9f17fc0af950b0ce744a655348b4cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406066"
---
# <a name="my-first-powershell-runbook"></a>Az első PowerShell-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks). Kezdje egy egyszerű runbook, amely tesztelheti és közzéteheti, miközben megtanulják, hogyan követheti nyomon a Runbook-feladat állapotát. Ezután módosítsa a runbookot az Azure-erőforrások tényleges kezeléséhez, ebben az esetben egy Azure virtuális gép indítása. Végezze el az oktatóanyagot, hogy a runbook robusztusabbá tegye a runbook-paraméterek hozzáadásával.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-quickstart-create-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez. Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Mivel megáll, és indítsa el ezt a gépet, nem kell egy éles virtuális gép.
* Ha szükséges, [importálja az Azure-modulokat](shared-resources/modules.md) vagy [a modulok frissítése a](automation-update-azure-modules.md) használt parancsmagok alapján.

## <a name="differences-from-powershell-workflow-runbooks"></a>Különbségek a PowerShell-munkafolyamat runbookjaitól

A PowerShell-runbookok ugyanazokkal az életciklussal, képességekkel és felügyelettel rendelkeznek, mint a PowerShell-munkafolyamat runbookjai. Vannak azonban különbségek és korlátozások.

| Jellemző  | PowerShell-runbookok | PowerShell-munkafolyamat-runbookok |
| ------ | ----- | ----- |
| Sebesség | Fuss gyorsan, mert nem használ fordítási lépést. | Fuss lassabban. |
| Ellenőrzőpontok | Ne támogassa az ellenőrzőpontokat. A PowerShell-runbookok csak az elejétől folytathatják a működést. | Használjon ellenőrzőpontokat, amelyek lehetővé teszik, hogy a munkafüzet bármely pontról folytassa a műveletet. |
| Parancs végrehajtása | Csak a soros végrehajtás támogatása. | Támogatja mind a soros és párhuzamos végrehajtás.|
| Runspace között | Egyetlen runspace fut mindent egy parancsfájlban. | Egy külön runspace használható egy tevékenységhez, egy parancshoz vagy egy parancsfájlblokkhoz. |

Ezek a különbségek mellett a PowerShell runbookok néhány [szintaktikai különbséget](https://technet.microsoft.com/magazine/dn151046.aspx) a PowerShell-munkafolyamat runbookok.

## <a name="step-1---create-runbook"></a>1. lépés – Runbook létrehozása

Első lépésként hozzon létre egy `Hello World`egyszerű runbookot, amely a szöveget adja ki.

1. Az Azure Portalon nyissa meg az Automation-fiókját.
2. Válassza **a Runbookok** **a Folyamatautomatizálás** csoportban a runbookok listájának megnyitásához.
3. Hozzon létre egy új runbookot **a Runbook létrehozása**lehetőség kiválasztásával.
4. Adja a forgatókönyvnek a **MyFirstRunbook-PowerShell** nevet.
5. Ebben az esetben egy [PowerShell-runbookot](automation-runbook-types.md#powershell-runbooks)fog létrehozni. Válassza a **PowerShell** lehetőséget a **Runbook-típushoz.**
6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz

Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben az oktatóanyagban közvetlenül a runbookba fogja beírni a kódot.

1. A runbook jelenleg üres. Írja `Write-Output "Hello World"` be a parancsfájl törzsét.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> 3. lépés - A runbook tesztelése

Mielőtt közzétenné a runbookot, hogy elérhetővé tegye éles környezetben, tesztelje, hogy megfelelően működik-e. A runbook tesztelése futtatja a Piszkot verziója, és lehetővé teszi, hogy interaktívan tekintse meg a kimenetet.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Vegye figyelembe, hogy létrejön egy [runbook-feladat,](automation-runbook-execution.md) és állapota megjelenik az ablaktáblán.

   A feladat állapota várólistára kerülése, jelezve, hogy a feladat arra vár, hogy a felhőben lévő runbook-dolgozó elérhetővé váljon. Az állapot akkor változik, amikor egy dolgozó igényt tart a feladatra. Végül az állapot fut, amikor a runbook ténylegesen elindul.

4. Amikor a runbook-feladat befejeződik, a Teszt ablaktábla megjeleníti a kimenetét. Ebben az esetben, `Hello World`látod .

   ![Teszt panel kimenete](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása

A létrehozott runbook továbbra is Vázlat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk. Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal. Ebben az esetben még nincs Közzétett verzió, mivel még csak most hozta létre a runbookot.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.
1. Görgessen balra a Runbook oklapon lévő runbook megtekintéséhez, és vegye figyelembe, hogy a **Szerzői állapot** értéke **Közzétéve**érték.
1. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell**oldalának megtekintéséhez.
   
   A beállítások a felső lehetővé teszi, hogy indítsa el a runbook most, ütemezése egy jövőbeli kezdési időpont, vagy hozzon létre egy [webhook,](automation-webhooks.md) hogy a runbook lehet indítani egy HTTP-hívás.
1. Válassza **a Start,** majd **az Igen** lehetőséget, amikor a runbook indítására kéri. 
1. Meg van nyitva egy feladat ablaktábla a létrehozott runbook-feladathoz. Bár bezárhatja ezt az ablaktáblát, hagyja nyitva most, hogy megnézhesse a feladat előrehaladását. A feladat állapota megjelenik a **Feladat összegzése**, és a lehetséges állapotok a runbook teszteléséhez leírt módon jelennek meg.

   ![Feladat összegzése](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Miután a runbook állapota **Output** látható befejeződött, kattintson a `Hello World` Kimenet gombra a Kimenet lap megnyitásához, ahol megjelenik a megjelenítés.

   ![Feladat kimenete](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Zárja be a Kimenet lapot.
1. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. Csak a `Hello World` kimeneti adatfolyamban jelenik meg.

    Vegye figyelembe, hogy a Streams ablaktábla más adatfolyamokat is megjeleníthet egy runbook-feladathoz, például a részletes és a hibaadatfolyamokat, ha a runbook ír nekik.

   ![Minden napló](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Zárja be a Streams és a Feladat ablaktáblát a MyFirstRunbook-PowerShell lapra való visszatéréshez.
1. A **Részletek csoportban**kattintson a **Feladatok** elemre a runbook Feladatok lapjának megnyitásához. Ez a lap a runbook által létrehozott összes feladatot megjeleníti. Csak egy feladat jelenik meg a listában, mivel csak egyszer futtatta a feladatot.

   ![Feladatlista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Kattintson a feladat nevére, ha meg szeretné nyitni ugyanazt a feladat ablaktáblát, amelyet a runbook indításakor megtekintett. Ezen az ablaktáblán megtekintheti a runbookhoz létrehozott feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez

Most már befejeződött a runbook tesztelése és közzététele, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Ehhez a runbook képesnek kell lennie arra, hogy hitelesítse a Futtatás másként fiók használatával, amely automatikusan létre jött az Automation-fiók létrehozásakor.

Az alábbi példában látható módon a Futtatás másként kapcsolat a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) parancsmaggal történik. Ha több előfizetésben kezel erőforrásokat, a `AzContext` paramétert a [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)segítségével kell használnia.

> [!NOTE]
> A PowerShell runbookok, `Add-AzAccount` és `Connect-AzAccount` `Add-AzureRMAccount` aliasok. Használhatja ezeket a parancsmagokat, vagy [frissítheti a modulokat](automation-update-azure-modules.md) az Automation-fiókban a legújabb verziókra. Előfordulhat, hogy frissítenie kell a modulokat, még akkor is, ha nemrég létrehozott egy új Automation-fiókot.

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
1. Nyissa meg a szöveges **szerkesztőt** a Szerkesztés gombra kattintva a MyFirstRunbook-PowerShell lapon.
1. Már nincs szükséged `Write-Output` a vonalra. Csak rajta, töröld ki.
1. Írja be vagy másolja be a következő kódot, amely kezeli a hitelesítést az Automation Run As fiókkal.

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

1. Kattintson **a Teszt ablaktábla** elemre, hogy tesztelhesse a runbookot.
1. Kattintson az **Indítás** gombra a teszt elindításához. Miután befejeződött, a következőhöz hasonló kimenetet kell látnia, amely a fiókjából származó alapvető információkat jeleníti meg. Ez a kimenet megerősíti, hogy a Futtatás partner érvényes.

   ![Hitelesítés](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához

Most, hogy a runbook hitelesíti az Azure-előfizetését, kezelheti az erőforrásokat. Adjunk hozzá egy parancsot egy virtuális gép indításához. Az Azure-előfizetésben bármelyik virtuális gépet kiválaszthatja, és egyelőre csak a nevet kódolja a runbookban.

1. A runbook-parancsfájlhoz adja hozzá a [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) parancsmast a virtuális gép elindításához. Az alábbiakban látható módon a parancsmag egy `VMName` virtuális gépet indít `ResourceGroupName`el a nevével és egy erőforráscsoporttal.

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

1. Mentse a runbookot, majd kattintson a **Teszt ablaktáblára,** hogy tesztelhesse.
1. A teszt megkezdéséhez kattintson a **Start** gombra. Miután befejeződött, győződjön meg arról, hogy a virtuális gép elindult.

## <a name="step-7---add-an-input-parameter"></a>7. lépés - Bemeneti paraméter hozzáadása

A runbook jelenleg elindítja a virtuális gépet, amely a runbookban kódolt. A runbook hasznosabb, ha megadja a virtuális gépet a runbook indításakor. Adjunk hozzá bemeneti paramétereket a runbookhoz, hogy ezt a funkciót biztosítsuk.

1. A szövegszerkesztőben módosítsa `Start-AzVM` a parancsmatot úgy, `VMName` hogy `ResourceGroupName`a paraméterekhez és a hoz. 

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
1. Írja be az elindítani kívánt virtuális gép **VMNAME** és **RESOURCEGROUPNAME** értékeit, majd kattintson az **OK**gombra.<br><br> ![Paraméter átadása](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Amikor a runbook befejeződik, győződjön meg arról, hogy a virtuális gép elindult.

## <a name="next-steps"></a>További lépések

* A PowerShellről további információt, beleértve a nyelvi referencia- és tanulási modulokat, tekintse meg a [PowerShell-dokumentumok című témakört.](/powershell/scripting/overview)
* A PowerShell-parancsmag referencia, lásd: [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
* A grafikus runbookok első lépései [Az Első grafikus runbook című témakörben](automation-first-runbook-graphical.md)látható.
* A PowerShell-munkafolyamat-runbookok első [lépései: Az első PowerShell-munkafolyamat-runbook.](automation-first-runbook-textual.md)
* Ha többet szeretne tudni a runbook-típusokról, azok előnyeiről és korlátairól, olvassa el az [Azure Automation runbook-típusok című témakört.](automation-runbook-types.md)
* A PowerShell-parancsfájltámogatási funkcióról további információt az [Azure Automation natív PowerShell-parancsfájl-támogatása című témakörben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)talál.
