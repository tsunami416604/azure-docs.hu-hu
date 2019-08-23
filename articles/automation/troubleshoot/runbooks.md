---
title: Azure Automation Runbookok kapcsolatos hibák elhárítása
description: Tudnivalók a Azure Automation runbookok kapcsolatos hibák elhárításáról
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c6b526cdd317e8b075d28e0fb9018501148c731c
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971296"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbookok kapcsolatos hibák elhárítása

Ez a cikk a runbookok kapcsolatos gyakori problémákat és azok megoldását ismerteti.

## <a name="steps-to-troubleshoot-runbooks"></a>A runbookok hibaelhárításának lépései

Ha a runbookok végrehajtása során hibák léptek fel Azure Automationban, a probléma diagnosztizálásához kövesse az alábbi lépéseket.

1. **Győződjön meg arról, hogy a runbook-parancsfájl sikeresen lefut a helyi gépen:**  A nyelvi referenciák és a tanulási modulok a [PowerShell-dokumentumok](/powershell/scripting/overview) vagy a [Python-dokumentumok](https://docs.python.org/3/) című témakörben találhatók.

   A szkript helyi végrehajtása a gyakori hibák felderítését és megoldását, például a következőket hajtja végre:

   - **Hiányzó modulok**
   - **Szintaktikai hibák**
   - **Logikai hibák**

2. Vizsgálja meg a runbook [hibastreamjeiben](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) az adott üzeneteket, és hasonlítsa össze őket az alábbi hibákkal.

3. **Győződjön meg arról, hogy a csomópontok és az Automation-munkaterület rendelkezik a szükséges modulokkal:** Ha a runbook bármely modult importál, győződjön meg arról, hogy elérhetők-e az Automation-fiókjába az [importálási modulok](../shared-resources/modules.md#import-modules)részben ismertetett lépések segítségével. Frissítse a modulokat a legújabb verzióra a [Azure Automation Azure-modulok frissítése](..//automation-update-azure-modules.md)című részben található utasításokat követve. További hibaelhárítási információkért lásd: [modulok hibaelhárítása](shared-resources.md#modules).

### <a name="if-the-runbook-is-suspended-or-unexpectedly-failed"></a>Ha a Runbook fel van függesztve vagy váratlanul meghiúsult

A runbook felfüggesztésének vagy megszakításának több oka is lehet:

* A [feladatok állapota](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) határozza meg a runbook állapotát és a lehetséges okokat.
* [További kimenet hozzáadása](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) a runbook annak azonosításához, hogy mi történjen a runbook felfüggesztése előtt.
* [Kezelje](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) a feladatokban felmerülő kivételeket.

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Hitelesítési hibák Azure Automation runbookok használatakor

### <a name="login-azurerm"></a>Forgatókönyv A login-AzureRMAccount futtatása a bejelentkezéshez

Ez a hiba akkor fordulhat elő, ha nem futtató fiókot használ, vagy a futtató fiók lejárt. Lásd: [Azure Automation futtató fiókok kezelése](https://docs.microsoft.com/azure/automation/manage-runas-account).

#### <a name="issue"></a>Probléma

A runbook végrehajtásakor a következő hibaüzenet jelenik meg:

```error
Run Login-AzureRMAccount to login.
```

#### <a name="cause"></a>Ok

Ennek a hibának két elsődleges oka van:

* A AzureRM modulok különböző verziói.
* Egy külön előfizetésben próbál hozzáférni az erőforrásokhoz.

#### <a name="resolution"></a>Megoldás:

Ha ezt a hibaüzenetet az egyik AzureRM-modul frissítése után kapja meg, akkor az összes AzureRM-modult ugyanarra a verzióra kell frissítenie.

Ha egy másik előfizetésben lévő erőforrásokhoz próbál hozzáférni, az engedélyek konfigurálásához kövesse az alábbi lépéseket.

1. Nyissa meg az Automation-fiók futtató fiókját, és másolja az alkalmazás AZONOSÍTÓját és ujjlenyomatát.
  ![Alkalmazás AZONOSÍTÓjának és ujjlenyomatának másolása](../media/troubleshoot-runbooks/collect-app-id.png)
1. Nyissa meg az előfizetéshez tartozó Access Control, ahol az Automation-fiók nem található, és adjon hozzá egy új szerepkör-hozzárendelést.
  ![Hozzáférés-vezérlés](../media/troubleshoot-runbooks/access-control.png)
1. Adja hozzá az előző lépésben összegyűjtött alkalmazás-azonosítót. Válassza a közreműködői engedélyek lehetőséget.
   ![Szerepkör-hozzárendelés hozzáadása](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Másolja ki az előfizetés nevét a következő lépéshez.
1. Mostantól a következő runbook-kóddal tesztelheti az Automation-fiókjából a másik előfizetésre vonatkozó engedélyeket.

    Cserélje le a\<"\>CertificateThumbprint" értéket a #1 lépésben másolt értékre, és\<a\>"SubscriptionName" értéket, amelyet a #4 lépésben másolt.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

### <a name="sign-in-failed"></a>Forgatókönyv Nem sikerült bejelentkezni az Azure-fiókba

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor a vagy `Add-AzureAccount` `Connect-AzureRmAccount` a parancsmaggal dolgozik:

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a hitelesítőadat-eszköz neve érvénytelen. Ez a hiba akkor is előfordulhat, ha az Automation hitelesítőadat-eszköz beállításához használt Felhasználónév és jelszó nem érvényes.

#### <a name="resolution"></a>Megoldás:

A probléma okának megállapításához hajtsa végre a következő lépéseket:

1. Győződjön meg arról, hogy nincs speciális karaktere. A karakterek közé tartozik **\@** az Automation hitelesítőadat-eszköz neve, amelyet az Azure-hoz való kapcsolódáshoz használ.
2. Győződjön meg arról, hogy a helyi PowerShell ISE-szerkesztőben a Azure Automation hitelesítő adatok között tárolt felhasználónevet és jelszót is használja. A Felhasználónév és a jelszó helyességét a következő parancsmagok futtatásával végezheti el a PowerShell ISE-ben:

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Ha a hitelesítés helyileg meghiúsul, az azt jelenti, hogy nem állította be megfelelően a Azure Active Directory hitelesítő adatait. Tekintse át az [Azure-beli hitelesítés Azure Active Directory blogbejegyzés használatával](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) című témakört, hogy helyesen állítsa be a Azure Active Directory fiókot.

4. Ha úgy tűnik, hogy átmeneti hiba történt, próbálkozzon újra az újrapróbálkozási logikával a hitelesítési rutin hozzáadásával, hogy megbízhatóbb legyen a hitelesítés.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Forgatókönyv Nem található az Azure-előfizetés

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor a vagy `Select-AzureSubscription` `Select-AzureRmSubscription` a parancsmaggal dolgozik:

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Hiba

Ez a hiba akkor fordulhat elő, ha:

* Az előfizetés neve érvénytelen.

* Az előfizetést beolvasni próbáló Azure Active Directory felhasználó nem rendszergazdaként van konfigurálva az előfizetéshez.

#### <a name="resolution"></a>Megoldás:

Az alábbi lépéseket követve megállapíthatja, hogy hitelesített-e az Azure-ban, és hozzáfér-e a kiválasztani kívánt előfizetéshez:

1. Annak biztosítása érdekében, hogy a működés önálló legyen, tesztelje a parancsfájlt a Azure Automationon kívül.
2. Győződjön meg arról, hogy a `Add-AzureAccount` parancsmag futtatása előtt `Select-AzureSubscription` futtatja a parancsmagot.
3. Hozzáadás `Disable-AzureRmContextAutosave –Scope Process` a runbook elejéhez. Ez a parancsmag biztosítja, hogy a hitelesítő adatok csak az aktuális runbook végrehajtásához legyenek érvényesek.
4. Ha továbbra is ezt a hibaüzenetet látja, módosítsa a kódot úgy , hogy hozzáadja a `Add-AzureAccount` AzureRmContext paramétert a parancsmag után, majd végrehajtja a kódot.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Forgatókönyv Az Azure-hitelesítés nem sikerült, mert a többtényezős hitelesítés engedélyezve van

#### <a name="issue"></a>Probléma

Az Azure-beli felhasználónevével és jelszavával történő hitelesítéskor a következő hibaüzenet jelenik meg:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Ok

Ha az Azure-fiókjában többtényezős hitelesítés van, nem használhat Azure Active Directory felhasználót az Azure-beli hitelesítéshez. Ehelyett tanúsítványt vagy szolgáltatásnevet kell használnia az Azure-beli hitelesítéshez.

#### <a name="resolution"></a>Megoldás:

Ha a klasszikus Azure üzemi modell parancsmagokkal rendelkező tanúsítványt szeretne használni, tekintse meg a [tanúsítvány létrehozása és hozzáadása az Azure-szolgáltatások kezeléséhez](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) című témakört. Ha Azure Resource Manager-parancsmagokkal szeretne szolgáltatásnevet használni, tekintse meg az [egyszerű szolgáltatásnév létrehozása a Azure Portal használatával](../../active-directory/develop/howto-create-service-principal-portal.md) és az [egyszerű szolgáltatásnév hitelesítése a Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) segítségével című témakört.

## <a name="common-errors-when-working-with-runbooks"></a>Gyakori hibák a runbookok használatakor

### <a name="child-runbook-object"></a>A gyermek runbook hibát ad vissza, ha a kimeneti adatfolyam egyszerű adattípusok helyett objektumokat tartalmaz

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor egy gyermek runbook meghívása `-Wait` a kapcsolóval, és a kimeneti adatfolyam tartalmaz egy objektumot:

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Ok

Létezik egy ismert probléma, amelyben a [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) nem kezeli megfelelően a kimeneti adatfolyamot, ha objektumokat tartalmaz.

#### <a name="resolution"></a>Megoldás:

Ennek megoldásához javasolt a lekérdezési logikát megvalósítani, majd a [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) parancsmag használatával lekérni a kimenetet. A következő példában a logikának egy mintája van meghatározva.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>Forgatókönyv Hibaüzenet jelenik meg a get_SerializationSettings metódussal kapcsolatos feladatokban

#### <a name="issue"></a>Probléma

A következő üzenettel láthatja, hogy a runbook a feladatokban szereplő hibákat:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Ok

Ezt a hibát a AzureRM és az az parancsmagok egy runbook való használata okozza. Az importálás `Az` `AzureRM`előtt történik.

#### <a name="resolution"></a>Megoldás:

Az az és a AzureRM parancsmagok nem importálhatók és nem használhatók ugyanabban a runbook, ha többet szeretne megtudni az az Azure Automation által nyújtott támogatásról, tekintse meg az az [modul-támogatás a Azure Automationban](../az-modules.md)című témakört.

### <a name="task-was-cancelled"></a>Forgatókönyv A runbook sikertelen a következő hibával: Egy feladat meg lett szakítva

#### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>Ok

Ezt a hibát az elavult Azure-modulok használatával lehet okozni.

#### <a name="resolution"></a>Megoldás:

Ez a hiba az Azure-modulok legújabb verzióra való frissítésével oldható fel.

Az Automation-fiókban kattintson a **modulok**elemre, majd az **Azure-modulok frissítése**elemre. A frissítés nagyjából 15 percet vesz igénybe, és a művelet végrehajtása után futtassa újra a hibát okozó runbook. A modulok frissítésével kapcsolatos további információkért lásd: [Azure-modulok frissítése Azure Automationban](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Forgatókönyv A runbookok meghiúsul, ha több előfizetést is kezel

#### <a name="issue"></a>Probléma

A runbookok `Start-AzureRmAutomationRunbook`a használatával történő végrehajtásakor a runbook nem tudja kezelni az Azure-erőforrásokat.

#### <a name="cause"></a>Ok

A runbook nem a megfelelő környezetet használja a futtatásakor.

#### <a name="resolution"></a>Megoldás:

Több előfizetés használata esetén előfordulhat, hogy az előfizetési környezet elvész a runbookok meghívásakor. Annak érdekében, hogy az előfizetési környezet átkerüljön a runbookok, adja `AzureRmContext` hozzá a paramétert a parancsmaghoz, és adja át a környezetet. Azt is javasoljuk, hogy a `Disable-AzureRmContextAutosave` parancsmagot használja a **folyamat** hatókörével annak biztosítására, hogy a használt hitelesítő adatok csak az aktuális runbook legyenek használatban.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Forgatókönyv A runbook egy hiányzó parancsmag miatt meghiúsul

#### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A parancsmagot tartalmazó modul nem lett importálva az Automation-fiókba.
* A parancsmagot tartalmazó modul importálva van, de elavult

#### <a name="resolution"></a>Megoldás:

Ez a hiba a következő feladatok egyikének végrehajtásával oldható fel:

Ha a modul egy Azure-modul, tekintse meg a [Azure Automation Azure PowerShell moduljainak frissítése](../automation-update-azure-modules.md) című témakört, amelyből megtudhatja, hogyan frissítheti a modulokat az Automation-fiókban.

Ha ez egy különálló modul, győződjön meg arról, hogy a modul az Automation-fiókjában lett importálva.

### <a name="job-attempted-3-times"></a>Forgatókönyv A runbook-feladatok indítását háromszor próbálta meg, de minden alkalommal nem indult el

#### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>Ok

Ez a hiba az alábbi problémák egyike miatt fordul elő:

* Memória korlátja Az [Automation szolgáltatás korlátozásai](../../azure-subscription-service-limits.md#automation-limits)alapján a rendszer megtalálta a rendelkezésre álló memória mennyiségét a Sandboxhoz. Ha a feladat 400 MB-nál több memóriát használ, A feladatok sikertelenek lehetnek.

* Hálózati szoftvercsatornák. Az Azure-beli munkaterületek 1000 egyidejű hálózati szoftvercsatornára korlátozódnak, az [Automation szolgáltatás korlátainál](../../azure-subscription-service-limits.md#automation-limits)leírtak szerint.

* A modul nem kompatibilis. Ez a hiba akkor fordulhat elő, ha a modul függőségei nem megfelelőek, és ha nem, a runbook jellemzően "parancs nem található" vagy "nem köthető paraméter" üzenetet ad vissza.

* A runbook egy végrehajtható fájlt vagy alfolyamatot próbált meg hívni egy Azure-beli homokozóban futó runbook. Ez a forgatókönyv nem támogatott az Azure-beli sandboxokban.

* A runbook túl sok kivételi adatokat próbált meg írni a kimeneti adatfolyamba.

#### <a name="resolution"></a>Megoldás:

A következő megoldások bármelyike elháríthatja a problémát:

* A javasolt módszerek a memória korlátján belül a számítási feladatok felosztása több runbookok között, a memóriában tárolt adatok mennyiségének csökkentése nélkül, nem kell felesleges kimenetet írni a runbookok, vagy meg kell fontolnia, hogy hány ellenőrzőpontot ír a PowerShell-munkafolyamatba runbookok. Használhatja a Clear metódust, például `$myVar.clear()` törölje a változót, és a használatával `[GC]::Collect()` azonnal futtathatja a szemetet. Ezek a műveletek csökkentik a runbook memória-lábnyomát futtatókörnyezet közben.

* Frissítse az Azure-modulokat a [Azure Automation Azure PowerShell moduljainak frissítése](../automation-update-azure-modules.md)című témakör lépéseit követve.

* Egy másik megoldás, ha a runbook egy [hibrid runbook](../automation-hrw-run-runbooks.md)-feldolgozón futtatja. A hibrid feldolgozók nem korlátozódnak az Azure-beli munkaterületek által védett memória-és hálózati korlátokra.

* Ha meg kell hívnia egy folyamatot (például. exe vagy alfolyamat. call) egy runbook, a runbook egy [hibrid runbook](../automation-hrw-run-runbooks.md)-feldolgozón kell futtatnia.

* A feladatok kimeneti adatfolyama 1MB-ra van korlátozva. Győződjön meg arról, hogy egy try/catch blokkban lévő végrehajtható fájl vagy alfolyamathoz tartozó hívásokat csatol. Ha kivételt váltott ki, írja az adott kivételből származó üzenetet egy Automation-változóba. Ez megakadályozza, hogy a rendszer beírja a feladatot a kimeneti adatfolyamba.

### <a name="fails-deserialized-object"></a>Forgatókönyv A Runbook deszerializált objektum miatt sikertelen

#### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Ok

Ha a runbook egy PowerShell-munkafolyamat, akkor az összetett objektumokat egy deszerializált formátumban tárolja, hogy a runbook állapot maradjon, ha a munkafolyamat fel van függesztve.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához a következő három megoldás egyike szükséges:

* Ha a komplex objektumokat az egyik parancsmagból egy másikba kívánja kiépíteni, csomagolja be ezeket a parancsmagokat egy InlineScript.
* Adja meg a szükséges nevet vagy értéket a komplex objektumból a teljes objektum átadása helyett.
* PowerShell-munkafolyamatok runbook helyett használjon PowerShell-runbook.

### <a name="runbook-fails"></a>Forgatókönyv A Runbook meghiúsul, de a helyileg futtatva működik

#### <a name="issue"></a>Probléma

A szkript meghiúsul, ha runbook fut, de helyileg futtatva működik.

#### <a name="cause"></a>Ok

A szkript sikertelen lehet, ha runbook fut a következő okok valamelyike miatt:

* Hitelesítési problémák
* A szükséges modulok nincsenek importálva vagy elavultak.
* Előfordulhat, hogy a parancsfájl felhasználói beavatkozást kér.
* Egyes modulok feltételezik a Windows rendszerű számítógépeken lévő könyvtárakat. Előfordulhat, hogy ezek a kódtárak nem találhatók meg a homokozóban.
* Egyes modulok olyan .NET-verzióra támaszkodnak, amely eltér a homokozóban elérhetőtől.

#### <a name="resolution"></a>Megoldás:

A következő megoldások bármelyike megoldhatja ezt a problémát:

* Ellenőrizze, hogy az [Azure-](../manage-runas-account.md)ban megfelelően van-e hitelesítve.
* Győződjön meg róla [, hogy az Azure-modulok importálhatók és](../automation-update-azure-modules.md)naprakészek.
* Győződjön meg arról, hogy a parancsmagok egyike sem kér információt. Ez a viselkedés nem támogatott a runbookok.
* Győződjön meg arról, hogy a modul részét képező elemek olyan függőséggel rendelkeznek-e, amely nem szerepel a modulban.
* Az Azure-beli homokozók a .NET-keretrendszer 4.7.2 használják, ha egy modul magasabb verziót használ, nem fog működni. Ebben az esetben a [hibrid Runbook](../automation-hybrid-runbook-worker.md) -feldolgozót kell használnia

Ha ezeknek a megoldásoknak egyike sem oldja [](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) meg a problemReview, hogy a runbook miért sikertelenek voltak, a feladathoz tartozó naplókban talál részletes információkat.

### <a name="quota-exceeded"></a>Forgatókönyv A Runbook feladata sikertelen volt, mert túllépte a lefoglalt kvótát

#### <a name="issue"></a>Probléma

A runbook-feladata a következő hibával meghiúsul:

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladatok végrehajtása meghaladja a fiókhoz tartozó 500 perces ingyenes kvótát. Ez a kvóta a feladat-végrehajtási feladatok összes típusára vonatkozik. Ezen feladatok némelyike lehet egy feladat tesztelése, egy feladat elindítása a portálról, egy feladat végrehajtása webhookok használatával, vagy a Azure Portal vagy az adatközponton keresztül végrehajtandó feladat ütemezése. További információ az automatizálás díjszabásáról: az [Automation díjszabása](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Megoldás:

Ha havonta több mint 500 perces feldolgozást szeretne használni, módosítania kell az előfizetést az ingyenes szintről az alapszintű csomagra. Az alapszintű csomagra az alábbi lépések végrehajtásával frissítheti:

1. Jelentkezzen be az Azure-előfizetésébe
2. Válassza ki a frissíteni kívánt Automation-előfizetést
3. Kattintson a **Beállítások** > **díjszabása**lehetőségre.
4. Kattintson az **Engedélyezés** elemre az oldal alján a fiók alapszintű szintre való frissítéséhez.

### <a name="cmdlet-not-recognized"></a>Forgatókönyv A parancsmag nem ismerhető fel egy runbook végrehajtásakor

#### <a name="issue"></a>Probléma

A runbook-feladata a következő hibával meghiúsul:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Ok

Ezt a hibát akkor okozza a rendszer, ha a PowerShell-motor nem találja a runbook használt parancsmagot. Ennek a hibának az lehet az oka, hogy a parancsmagot tartalmazó modul hiányzik a fiókból, a név ütközik egy runbook nevével, vagy a parancsmag egy másik modulban is létezik, és az Automation nem tudja feloldani a nevet.

#### <a name="resolution"></a>Megoldás:

A következő megoldások bármelyike elháríthatja a problémát:

* Győződjön meg arról, hogy helyesen adta meg a parancsmag nevét.
* Győződjön meg arról, hogy a parancsmag létezik az Automation-fiókban, és nincsenek ütközések. Annak ellenőrzéséhez, hogy a parancsmag megtalálható-e, nyisson meg egy runbook szerkesztési módban, és keresse meg azt a parancsmagot, amelyet meg `Get-Command <CommandName>`szeretne keresni a könyvtárban, vagy futtassa a parancsot. Miután ellenőrizte, hogy a parancsmag elérhető-e a fiók számára, és hogy a nevek nem ütköznek más parancsmagokkal vagy runbookok, adja hozzá a vászonhoz, és győződjön meg arról, hogy érvényes paramétert használ a runbook.
* Ha a név ütközik, és a parancsmag két különböző modulban érhető el, a probléma megoldásához használja a parancsmag teljesen minősített nevét. Használhatja például a **ModuleName\CmdletName**.
* Ha a helyszíni runbook egy hibrid feldolgozói csoportban hajtja végre, akkor győződjön meg arról, hogy a modul és a parancsmag telepítve van a hibrid feldolgozót futtató számítógépen.

### <a name="long-running-runbook"></a>Forgatókönyv A hosszú ideig futó runbook nem sikerül befejezni

#### <a name="issue"></a>Probléma

A runbook 3 órán belül **leállított** állapotban jelenik meg. Az is előfordulhat, hogy a következő hibaüzenetet kapja:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Ez a viselkedés az Azure-beli munkaterületek "méltányos megosztás" általi, Azure Automation belüli folyamatainak figyelése miatt. Ha három óránál hosszabb ideig hajtja végre, a méltányos megosztás automatikusan leállítja a runbook. A valós idejű megosztás időkorlátja alá tartozó runbook állapota eltér a runbook típustól. A PowerShell és a Python runbookok leállított állapotra vannak állítva. A PowerShell-munkafolyamat runbookok beállítása **sikertelen**.

#### <a name="cause"></a>Ok

A runbook egy Azure-beli homokozóban a méltányos megosztás által engedélyezett 3 órás korlátot futtatta.

#### <a name="resolution"></a>Megoldás:

Egy javasolt megoldás, ha a runbook egy [hibrid runbook](../automation-hrw-run-runbooks.md)-feldolgozón futtatja.

A hibrid feldolgozók nem [](../automation-runbook-execution.md#fair-share) korlátozódnak az Azure-beli munkaterületek esetében 3 órás runbook-korlátra. A hibrid Runbook-feldolgozókon futó runbookok úgy kell kialakítani, hogy támogassa az újraindítási viselkedést, ha váratlan helyi infrastruktúra-problémák merülnek fel.

Egy másik lehetőség, hogy optimalizálja a runbook a [gyermek runbookok](../automation-child-runbooks.md)létrehozásával. Ha a runbook több erőforráson ugyanazt a függvényt futtatja, például egy adatbázis-művelet több adatbázison, akkor áthelyezheti a függvényt egy alárendelt runbook. Ezek az alárendelt runbookok külön folyamatokban párhuzamosan hajthatók végre. Ez a viselkedés csökkenti a szülő runbook befejezésének teljes időtartamát.

A gyermek runbook-forgatókönyvet engedélyező PowerShell-parancsmagok a következők:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – ez a parancsmag lehetővé teszi, hogy elindítson egy runbook, és adja át a paramétereket a runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – ha vannak olyan műveletek, amelyeket a gyermek runbook befejezése után kell végrehajtani, ez a parancsmag lehetővé teszi, hogy minden gyermeknél ellenőrizze a feladatok állapotát.

### <a name="expired webhook"></a>Forgatókönyv Állapota 400 hibás kérelem webhook meghívásakor

#### <a name="issue"></a>Probléma

Amikor megpróbál meghívja egy webhookot egy Azure Automation runbook, a következő hibaüzenet jelenik meg:

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Ok

A meghívni próbált webhook le van tiltva vagy lejárt.

#### <a name="resolution"></a>Megoldás:

Ha a webhook le van tiltva, újra engedélyezheti a webhookot a Azure Portalon keresztül. Ha egy webhook lejárt, a webhookot törölni kell, majd újra létre kell hozni. Csak akkor újíthat meg [egy](../automation-webhooks.md#renew-webhook) webhookot, ha még nem járt le.

### <a name="429"></a>Forgatókönyv 429: A kérelmek száma jelenleg túl nagy. Próbálkozzon újra

#### <a name="issue"></a>Probléma

A `Get-AzureRmAutomationJobOutput` parancsmag futtatásakor a következő hibaüzenet jelenik meg:

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, amikor a feladatok kimenetét olyan runbook kérdezi le, amely sok [részletes adatfolyammal](../automation-runbook-output-and-messages.md#verbose-stream)rendelkezik.

#### <a name="resolution"></a>Megoldás:

A hiba kétféleképpen oldható fel:

* Szerkessze a runbook, és csökkentse az általa kibocsátott feladatokhoz tartozó adatfolyamok számát.
* Csökkentse a lekérdezni kívánt adatfolyamok számát a parancsmag futtatásakor. Ennek a viselkedésnek a követéséhez megadhatja a `-Stream Output` (z) `Get-AzureRmAutomationJobOutput` parancsmag paraméterét a csak kimeneti streamek lekéréséhez. 

### <a name="cannot-invoke-method"></a>Forgatókönyv A PowerShell-feladatok sikertelenek a következő hibával: A metódus nem hívható meg

#### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor elindítja a PowerShell-feladatot egy Azure-ban futó runbook:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha az Azure-ban futtatott runbook indít egy PowerShell-feladatot. Ez a viselkedés azért fordulhat elő, mert egy Azure-beli homokozóban futtatott runbookok nem [teljes nyelvi módban](/powershell/module/microsoft.powershell.core/about/about_language_modes)fut.

#### <a name="resolution"></a>Megoldás:

A hiba kétféleképpen oldható fel:

* A használata helyett használja `Start-AzureRmAutomationRunbook` a parancsot a runbook indításához `Start-Job`
* Ha a runbook ez a hibaüzenet jelenik meg, futtassa azt egy hibrid Runbook-feldolgozón

Ha többet szeretne megtudni erről a viselkedésről és a Azure Automation Runbookok egyéb viselkedéséről, tekintse meg a [Runbook viselkedését](../automation-runbook-execution.md#runbook-behavior)ismertető témakört.

## <a name="other"></a>: A probléma nem szerepel a fenti felsorolásban

Az alábbi részekben a probléma megoldásához segítséget nyújtó dokumentáción kívül más gyakori hibák is szerepelnek.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>A hibrid runbook-feldolgozó nem futtat feladatokat vagy nem válaszol

Ha Azure Automation helyett hibrid feldolgozót használó feladatokat futtat, előfordulhat, hogy [a hibrid feldolgozót is](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)le kell állítania.

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>A runbook „Nincs engedély” hibaüzenettel vagy annak valamelyik variációjával meghiúsul

Előfordulhat, hogy a futtató fiókok nem a jelenlegi fiókjával megegyező engedélyekkel rendelkeznek az Azure-erőforrásokon. Győződjön meg arról, hogy a futtató fiók a szkriptben használt [bármilyen erőforrás elérésére jogosult](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

### <a name="runbooks-were-working-but-suddenly-stopped"></a>A runbookok működtek, de váratlanul leálltak

* Ha a runbookok korábban már elvégezték, de leállították, [Győződjön meg arról, hogy a futtató fiók nem járt le](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Ha webhookokat használ a runbook elindítására, akkor ellenőrizze, hogy [a webhook nem járt-e le](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

### <a name="issues-passing-parameters-into-webhooks"></a>Paraméterek átadása webhookokra

A paraméterek webhookokra való átadásával kapcsolatban lásd: [Runbook indítása](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)webhookból.

### <a name="issues-using-az-modules"></a>Az az modulok használatával kapcsolatos problémák

Az Az modulok és az AzureRM modulok használata ugyanabban az Automation-fiókban nem támogatott. További részletekért tekintse meg az [az modulok a runbookok](https://docs.microsoft.com/azure/automation/az-modules) című témakört.

### <a name="runbook-job-completed-but-with-unexpected-results-or-errors"></a>A Runbook-feladatok befejeződtek, de váratlan eredményekkel vagy hibákkal

Lent felsoroljuk az egyes problémákat és a hozzájuk tartozó megoldásokat, de nyomatékosan javasoljuk, hogy először próbálkozzon a következő két hibaelhárítási lépéssel:

* Próbálja meg [helyileg futtatni a runbook](https://docs.microsoft.com/azure/automation/troubleshoot/runbooks#runbook-fails) , mielőtt futtatja Azure Automation. Ez segít tisztázni, hogy a runbookban van hiba, vagy az Azure Automationnel kapcsolatos a probléma.
* Vizsgálja meg a runbook [hibastreamjeiben](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) az adott üzeneteket, és hasonlítsa össze őket az alábbi hibákkal.
* Adjon hozzá [további kimenetet](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) a runbook a hiba előfordulási helyének azonosításához.

### <a name="inconsistent-behavior-in-runbooks"></a>Következetlen viselkedés a runbookokban

A párhuzamos feladatokkal, erőforrások többszöri létrehozásával vagy a runbookok egyéb, az időzítésre érzékeny logikáival kapcsolatos problémák elkerüléséhez kövesse a [Runbook végrehajtása](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) szakasz utasításait.

### <a name="switching-between-multiple-subscriptions-in-a-runbook"></a>Váltás több előfizetés között egy runbookban

Kövesse a [több előfizetés használata](https://docs.microsoft.com/azure/automation/automation-runbook-execution#working-with-multiple-subscriptions)című témakör útmutatását.

### <a name="runbook-fails-with-error-the-subscription-cannot-be-found"></a>A Runbook a következő hibával meghiúsul: Az előfizetés nem található

Ez a probléma akkor jelentkezhet, ha a runbook nem RunAs-fiókot használ az Azure-erőforrásokhoz való hozzáféréshez. A megoldáshoz kövesse a [nem található Azure-előfizetés forgatókönyvével foglalkozó](https://docs.microsoft.com/azure/automation/troubleshoot/runbooks#unable-to-find-subscription) szakasz lépéseit.

### <a name="error-your-azure-credentials-have-not-been-set-up-or-have-expired-please-run-connect-azurermaccount-to-set-up-your-azure-credentials"></a>Hiba: Az Azure-beli hitelesítő adatai nem lettek beállítva vagy lejártak, futtassa a azureRmAccount-t az Azure-beli hitelesítő adatok beállításához.

Ez a hiba akkor fordulhat elő, ha nem futtató fiókot használ, vagy a futtató fiók lejárt. Lásd: [Azure Automation futtató fiókok kezelése](https://docs.microsoft.com/azure/automation/manage-runas-account).

### <a name="error-run-login-azurermaccount-to-login"></a>Hiba: A login-AzureRmAccount futtatása a bejelentkezéshez

Ez a hiba akkor fordulhat elő, ha nem futtató fiókot használ, vagy a futtató fiók lejárt. Lásd: [Azure Automation futtató fiókok kezelése](https://docs.microsoft.com/azure/automation/manage-runas-account).

### <a name="runbook-fails-with-error-strong-authentication-enrollment-is-required"></a>A Runbook a következő hibával meghiúsul: Erős hitelesítési regisztráció szükséges

Lásd: az [Azure-hitelesítés sikertelen, mert a többtényezős hitelesítés engedélyezve van](https://docs.microsoft.com/azure/automation/troubleshoot/runbooks#auth-failed-mfa) a Runbook hibaelhárítási útmutatójában.

### <a name="runbook-fails-with-the-errors-no-permission-forbidden-403-or-some-variation"></a>A Runbook a hibákkal meghiúsul: Nincs engedélye, tiltott, 403 vagy valamilyen variáció

Előfordulhat, hogy a futtató fiókok nem a jelenlegi fiókjával megegyező engedélyekkel rendelkeznek az Azure-erőforrásokon. Győződjön meg arról, hogy a futtató fiók rendelkezik a parancsfájlban használt [erőforrások eléréséhez szükséges engedélyekkel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) .

### <a name="runbooks-were-working-but-suddenly-stopped"></a>A runbookok működtek, de váratlanul leálltak

* Ha a runbookok korábban már elvégezték, de leállították, győződjön meg arról, hogy a futtató fiók [nem járt le](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Ha webhookokat használ a runbookok elindításához, győződjön meg arról, hogy a webhook [nem járt le](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

### <a name="passing-parameters-into-webhooks"></a>Paraméterek átadása a webhookoknak

A paraméterek webhookokra való átadásával kapcsolatban lásd: [Runbook indítása](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)webhookból.

### <a name="error-the-term-is-not-recognized"></a>Hiba: A kifejezés nem ismerhető fel

Kövesse a parancsmagban [](https://docs.microsoft.com/azure/automation/troubleshoot/runbooks#cmdlet-not-recognized) ismertetett lépéseket a runbook hibaelhárítási útmutatójában

### <a name="errors-about-typedata"></a>TypeData kapcsolatos hibák

Ha TypeData típusú hibaüzeneteket kap, olyan modulokkal futtat egy PowerShell-munkafolyamatot, amelyek nem támogatják azt. Módosítania kell a runbook típusát PowerShellre. További részletekért lásd: [Runbook-típusok](https://docs.microsoft.com/azure/automation/automation-runbook-types#powershell-runbooks) .

### <a name="using-az-modules"></a>Az modulok használata

Az Az modulok és az AzureRM modulok használata ugyanabban az Automation-fiókban nem támogatott. További részletekért tekintse meg az [az modulok a runbookok](https://docs.microsoft.com/azure/automation/az-modules) című témakört.

### <a name="using-self-signed-certificates"></a>Önaláírt tanúsítványok használata

Önaláírt tanúsítványok használatához kövesse az útmutatót az [új tanúsítvány létrehozásához](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

## <a name="recommended-documents"></a>Ajánlott dokumentumok

* [Runbook indítása az Azure Automationben](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Runbook végrehajtása az Azure Automationben](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási](https://azure.microsoft.com/support/options/) webhelyét, és válassza a **támogatás kérése**lehetőséget.
