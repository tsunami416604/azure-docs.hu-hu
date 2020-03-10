---
title: Azure Automation Runbookok kapcsolatos hibák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a Azure Automation runbookok kapcsolatban felmerülő problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b7d876c7f865b8368451ea1b6cc96ade89a59aa8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373432"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbookok kapcsolatos hibák elhárítása

Ha a runbookok végrehajtása során hibák léptek fel Azure Automationban, a következő lépésekkel diagnosztizálhatja a problémákat.

1. **Győződjön meg arról, hogy a runbook-parancsfájl sikeresen lefut a helyi gépen:**  A nyelvi referenciák és a tanulási modulok a [PowerShell-dokumentumok](/powershell/scripting/overview) vagy a [Python-dokumentumok](https://docs.python.org/3/) című témakörben találhatók.

   A szkript helyi végrehajtása a gyakori hibák felderítését és megoldását, például a következőket hajtja végre:

   - **Hiányzó modulok**
   - **Szintaktikai hibák**
   - **Logikai hibák**

2. **Vizsgálja** meg az adott üzenetekhez tartozó runbook- [adatfolyamokat](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) , és hasonlítsa össze azokat az alábbi hibákkal.

3. **Győződjön meg arról, hogy a csomópontok és az Automation-munkaterület rendelkezik a szükséges modulokkal:** Ha a runbook bármely modult importál, győződjön meg arról, hogy elérhetők-e az Automation-fiókja számára az [importálási modulok](../shared-resources/modules.md#import-modules)részben ismertetett lépések segítségével. Frissítse a modulokat a legújabb verzióra az [Azure-modulok frissítése Azure Automationban](..//automation-update-azure-modules.md)című témakör útmutatásait követve. További hibaelhárítási információkért lásd: [modulok hibaelhárítása](shared-resources.md#modules).

Ha a Runbook felfüggesztve vagy váratlanul meghiúsult:

* A [feladatok állapotának megadásával](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) meghatározhatja a runbook állapotát és a lehetséges okokat.
* [További kimenet hozzáadása](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) a runbook annak azonosításához, hogy mi történjen a runbook felfüggesztése előtt.
* [Kezelje](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) a feladatokban felmerülő kivételeket.

## <a name="login-azurerm"></a>Forgatókönyv: a login-AzureRMAccount futtatása a bejelentkezéshez

### <a name="issue"></a>Probléma

A runbook végrehajtásakor a következő hibaüzenet jelenik meg:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha nem futtató fiókot használ, vagy lejárt a futtató fiók. Lásd: [Azure Automation futtató fiókok kezelése](https://docs.microsoft.com/azure/automation/manage-runas-account).

Ennek a hibának két elsődleges oka van:

* A AzureRM modulok különböző verziói.
* Egy külön előfizetésben próbál hozzáférni az erőforrásokhoz.

### <a name="resolution"></a>Megoldás:

Ha ezt a hibaüzenetet az egyik AzureRM-modul frissítése után kapja meg, akkor az összes AzureRM-modult ugyanarra a verzióra kell frissítenie.

Ha egy másik előfizetésben lévő erőforrásokhoz próbál hozzáférni, az engedélyek konfigurálásához kövesse az alábbi lépéseket.

1. Nyissa meg az Automation futtató fiókot, és másolja az alkalmazás AZONOSÍTÓját és ujjlenyomatát.
  ![az alkalmazás AZONOSÍTÓjának és ujjlenyomatának másolása](../media/troubleshoot-runbooks/collect-app-id.png)
1. Nyissa meg az előfizetéshez tartozó Access Control, ahol az Automation-fiók nem található, és adjon hozzá egy új szerepkör-hozzárendelést.
  ![Hozzáférés-vezérlés](../media/troubleshoot-runbooks/access-control.png)
1. Adja hozzá az előző lépésben összegyűjtött alkalmazás-azonosítót. Válassza a közreműködői engedélyek lehetőséget.
   ![szerepkör-hozzárendelés hozzáadása](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Másolja ki az előfizetés nevét a következő lépéshez.
1. Mostantól a következő runbook-kóddal tesztelheti az Automation-fiókjából a másik előfizetésre vonatkozó engedélyeket.

    Cserélje le a "\<CertificateThumbprint\>" értéket az #1 lépésben másolt értékre, és a "\<SubscriptionName\>" értéket, amelyet a #4 lépésben másolt.

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

## <a name="unable-to-find-subscription"></a>Forgatókönyv: nem található az Azure-előfizetés

### <a name="issue"></a>Probléma

A `Select-AzureSubscription`-vagy `Select-AzureRmSubscription`-parancsmagok használatakor a következő hibaüzenetet kapja:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Hiba

Ez a hiba akkor fordulhat elő, ha:

* Az előfizetés neve érvénytelen.

* Az előfizetést beolvasni próbáló Azure Active Directory felhasználó nem rendszergazdaként van konfigurálva az előfizetéshez.

### <a name="resolution"></a>Megoldás:

Az alábbi lépéseket követve megállapíthatja, hogy hitelesített-e az Azure-ban, és hozzáfér-e a kiválasztani kívánt előfizetéshez:

1. Annak biztosítása érdekében, hogy a működés önálló legyen, tesztelje a parancsfájlt a Azure Automationon kívül.
2. A `Select-AzureSubscription` parancsmag futtatása előtt futtassa a `Add-AzureAccount` parancsmagot.
3. `Disable-AzureRmContextAutosave –Scope Process` hozzáadása a runbook elejéhez. Ez a parancsmag biztosítja, hogy a hitelesítő adatok csak az aktuális runbook végrehajtásához legyenek érvényesek.
4. Ha továbbra is ezt a hibaüzenetet látja, módosítsa a kódot úgy, hogy hozzáadja a **AzureRmContext** paramétert a `Add-AzureAccount` parancsmag után, majd végrehajtja a kódot.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>Forgatókönyv: az Azure-hitelesítés nem sikerült, mert a többtényezős hitelesítés engedélyezve van

### <a name="issue"></a>Probléma

Az Azure-beli felhasználónevével és jelszavával történő hitelesítéskor a következő hibaüzenet jelenik meg:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Ok

Ha az Azure-fiókjában többtényezős hitelesítés van, nem használhat Azure Active Directory felhasználót az Azure-beli hitelesítéshez. Ehelyett tanúsítványt vagy szolgáltatásnevet kell használnia az Azure-beli hitelesítéshez.

### <a name="resolution"></a>Megoldás:

Ha a klasszikus Azure üzemi modell parancsmagokkal rendelkező tanúsítványt szeretne használni, tekintse meg a [tanúsítvány létrehozása és hozzáadása az Azure-szolgáltatások kezeléséhez](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)című témakört. Ha Azure Resource Manager-parancsmagokkal szeretne szolgáltatásnevet használni, tekintse meg az [egyszerű szolgáltatásnév létrehozása a Azure Portal használatával](../../active-directory/develop/howto-create-service-principal-portal.md) és az [egyszerű szolgáltatásnév hitelesítése a Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)segítségével című témakört.

## <a name="get-serializationsettings"></a>Forgatókönyv: a get_SerializationSettings metódussal kapcsolatos feladatokban szereplő hiba jelenik meg

### <a name="issue"></a>Probléma

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

### <a name="cause"></a>Ok

Ezt a hibát a AzureRM és az az parancsmagok egy runbook való használata okozza. Akkor következik be, amikor `Az` importál `AzureRM`importálása előtt.

### <a name="resolution"></a>Megoldás:

Az az és a AzureRM parancsmag nem importálható és nem használható ugyanabban a runbook. Ha többet szeretne megtudni az az parancsmagokról a Azure Automationban, tekintse meg az [az modul támogatása a Azure Automationban](../az-modules.md)című témakört.

## <a name="task-was-cancelled"></a>Forgatókönyv: a runbook sikertelen a következő hibával: a feladat meg lett szakítva

### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Ok

Ezt a hibát az elavult Azure-modulok használatával lehet okozni.

### <a name="resolution"></a>Megoldás:

Ez a hiba az Azure-modulok legújabb verzióra való frissítésével oldható fel.

Az Automation-fiókban kattintson a **modulok**elemre, majd az **Azure-modulok frissítése**elemre. A frissítés nagyjából 15 percet vesz igénybe, és a művelet végrehajtása után futtassa újra a hibát okozó runbook. A modulok frissítésével kapcsolatos további információkért lásd: [Azure-modulok frissítése Azure Automationban](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Forgatókönyv: a Runbookok sikertelen a több előfizetés kezelésekor

### <a name="issue"></a>Probléma

A runbookok végrehajtásakor a runbook nem tudja kezelni az Azure-erőforrásokat.

### <a name="cause"></a>Ok

A runbook nem a megfelelő környezetet használja a futtatásakor.

### <a name="resolution"></a>Megoldás:

Több előfizetés használata esetén előfordulhat, hogy az előfizetési környezet elvész a runbookok meghívásakor. Annak érdekében, hogy az előfizetési környezet a runbookok legyen átadva, adja hozzá a `AzureRmContext` paramétert a parancsmaghoz, és adja át a környezetet. Javasoljuk továbbá, hogy az `Disable-AzureRmContextAutosave` parancsmagot használja a **folyamat** hatókörével annak biztosítása érdekében, hogy a használt hitelesítő adatok csak az aktuális runbook legyenek használatban.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
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

További információ: [több előfizetés használata](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Forgatókönyv: a kifejezés nem ismerhető fel parancsmag, függvény, parancsfájl neveként

### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A parancsmagot tartalmazó modul nem lett importálva az Automation-fiókba.
* A parancsmagot tartalmazó modul importálva van, de elavult.

### <a name="resolution"></a>Megoldás:

Ez a hiba a következő feladatok egyikének végrehajtásával oldható fel:

Ha a modul egy Azure-modul, tekintse meg a [Azure Automation Azure PowerShell moduljainak frissítése](../automation-update-azure-modules.md) című témakört, amelyből megtudhatja, hogyan frissítheti a modulokat az Automation-fiókban.

Ha ez egy különálló modul, győződjön meg arról, hogy a modul az Automation-fiókjában lett importálva.

## <a name="job-attempted-3-times"></a>Forgatókönyv: a runbook-feladatok indítását háromszor próbálta meg, de minden alkalommal nem indult el

### <a name="issue"></a>Probléma

A runbook a következő hiba miatt meghiúsul.

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Ok

Ez a hiba az alábbi problémák egyike miatt fordul elő.

* Memória korlátja Egy feladat meghiúsulhat, ha több mint 400 MB memóriát használ. A sandbox számára lefoglalt memória dokumentált korlátai az [Automation szolgáltatás korlátain](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)találhatók. 

* Hálózati szoftvercsatornák. Az Azure-beli munkaterületek 1000 egyidejű hálózati szoftvercsatornára korlátozódnak. Lásd: az [Automation szolgáltatás korlátai](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* A modul nem kompatibilis. Előfordulhat, hogy a modul függőségei nem megfelelőek. Ebben az esetben a runbook jellemzően "parancs nem található" vagy "nem köthető paraméter" üzenetet ad vissza.

* Nincs hitelesítés Active Directory a homokozóban. A runbook megpróbált meghívni egy Azure-beli homokozóban futó végrehajtható fájlt vagy alfolyamatot. A runbookok konfigurálása az Azure AD-val való hitelesítéshez az Azure Active Directory Authentication Library (ADAL) használatával nem támogatott.

* Túl sok kivételi érték. A runbook túl sok kivételi adatokat próbált meg írni a kimeneti adatfolyamba.

### <a name="resolution"></a>Megoldás:

* Memória korlátja, hálózati szoftvercsatorna. A memória korlátain belüli munkaterhelések használatának javasolt módjai több runbookok között a számítási feladatok felosztása, a memóriában kevesebb adat feldolgozása, a felesleges kimenetek írása a runbookok, valamint a PowerShell-munkafolyamatba való írások számának megvizsgálása. runbookok. A Clear metódust (például `$myVar.clear`) törölheti a változók törléséhez, és a `[GC]::Collect` használatával azonnal futtathatja a szemét-gyűjteményt. Ezek a műveletek csökkentik a runbook memória-lábnyomát futtatókörnyezet közben.

* A modul nem kompatibilis. Frissítse az Azure-modulokat a [Azure Automation Azure PowerShell moduljainak frissítése](../automation-update-azure-modules.md)című témakör lépéseit követve.

* Nincs hitelesítés a ADAL a homokozóban. Az Azure AD-runbook való hitelesítéskor győződjön meg arról, hogy az Azure AD-modul elérhető az Automation-fiókjában. Ügyeljen arra, hogy a futtató fióknak meg kell adnia a szükséges engedélyeket a runbook automatizálható feladatok végrehajtásához.

  Ha a runbook nem hívhat meg egy Azure-beli homokozóban futó végrehajtható fájlt vagy alfolyamatot, használja a runbook [hibrid runbook-feldolgozón](../automation-hrw-run-runbooks.md). A hibrid feldolgozók nem korlátozódnak az Azure-beli munkaterületek által használt memória-és hálózati korlátokra.

* Túl sok kivételi érték. A feladatok kimeneti adatfolyama 1MB-ra van korlátozva. Győződjön meg arról, hogy a runbook egy try/catch blokkban lévő végrehajtható fájl vagy alfolyamat hívásait fedi le. Ha a műveletek kivételt jeleznek, a kódnak kell megírnia az üzenetet a kivételből egy Automation-változóba. Ezzel a technikával megakadályozható, hogy a rendszer beírja az üzenetet a feladatok kimeneti adatfolyamba.

## <a name="sign-in-failed"></a>Forgatókönyv: nem sikerült az Azure-fiókba való bejelentkezés

### <a name="issue"></a>Probléma

A `Add-AzureAccount`-vagy `Connect-AzureRmAccount`-parancsmagok használatakor a következő hibák valamelyike jelenik meg:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a hitelesítőadat-eszköz neve érvénytelen. Ez a hiba akkor is előfordulhat, ha az Automation hitelesítőadat-eszköz beállításához használt Felhasználónév és jelszó nem érvényes.

### <a name="resolution"></a>Megoldás:

A probléma okának megállapításához hajtsa végre a következő lépéseket:

1. Győződjön meg arról, hogy nincs speciális karaktere. Ezeknek a karaktereknek a **\@** karaktere az Automation hitelesítőadat-eszköz neve, amelyet az Azure-hoz való kapcsolódáshoz használ.
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
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="child-runbook-object"></a>Forgatókönyv: az objektum hivatkozása nem az objektum egy példányára van beállítva

### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor egy gyermek runbook az `-Wait` kapcsolóval, a kimeneti adatfolyam pedig egy objektumot tartalmaz:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Ok

Létezik egy ismert probléma, amelyben a Start-AzureRmAutomationRunbook nem kezeli megfelelően a kimeneti adatfolyamot, ha objektumokat tartalmaz.

### <a name="resolution"></a>Megoldás:

A probléma megoldásához javasoljuk, hogy hozzon létre egy lekérdezési logikát, és használja a [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) parancsmagot a kimenet lekéréséhez. A következő példában a logikának egy mintája van meghatározva.

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

## <a name="fails-deserialized-object"></a>Forgatókönyv: a Runbook nem sikerül deszerializált objektum miatt

### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Ok

Ha a runbook egy PowerShell-munkafolyamat, akkor az összetett objektumokat egy deszerializált formátumban tárolja, hogy a runbook állapot maradjon, ha a munkafolyamat fel van függesztve.

### <a name="resolution"></a>Megoldás:

A probléma megoldásához a következő három megoldás egyike szükséges:

* Ha a komplex objektumokat az egyik parancsmagból egy másikba kívánja kiépíteni, csomagolja be ezeket a parancsmagokat egy InlineScript.
* Adja meg a szükséges nevet vagy értéket a komplex objektumból a teljes objektum átadása helyett.
* PowerShell-munkafolyamatok runbook helyett használjon PowerShell-runbook.

## <a name="quota-exceeded"></a>Forgatókönyv: a Runbook feladata meghiúsult, mert a lefoglalt kvóta túllépte a korlátot

### <a name="issue"></a>Probléma

A runbook-feladata a következő hibával meghiúsul:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladatok végrehajtása meghaladja a fiókhoz tartozó 500 perces ingyenes kvótát. Ez a kvóta a feladat-végrehajtási feladatok összes típusára vonatkozik. Ezen feladatok némelyike lehet egy feladat tesztelése, egy feladat elindítása a portálról, egy feladat végrehajtása webhookok használatával, vagy a Azure Portal vagy az adatközponton keresztül végrehajtandó feladat ütemezése. További információ az automatizálás díjszabásáról: az [Automation díjszabása](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Megoldás:

Ha havonta több mint 500 perces feldolgozást szeretne használni, módosítania kell az előfizetést az ingyenes szintről az alapszintű csomagra. Az alapszintű csomagra az alábbi lépések végrehajtásával frissítheti:

1. Jelentkezzen be az Azure-előfizetésbe.
2. Válassza ki a frissítendő Automation-fiókot.
3. Kattintson a **Beállítások**, majd a **díjszabás**elemre.
4. Kattintson az **Engedélyezés** elemre az oldal alján a fiók **alapszintű** szintre való frissítéséhez.

## <a name="cmdlet-not-recognized"></a>Forgatókönyv: a parancsmag nem ismerhető fel egy runbook végrehajtásakor

### <a name="issue"></a>Probléma

A runbook-feladata a következő hibával meghiúsul:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Ok

Ezt a hibát akkor okozza a rendszer, ha a PowerShell-motor nem találja a runbook használt parancsmagot. Ennek a hibának az lehet az oka, hogy a parancsmagot tartalmazó modul hiányzik a fiókból, a név ütközik egy runbook nevével, vagy a parancsmag egy másik modulban is létezik, és az Automation nem tudja feloldani a nevet.

### <a name="resolution"></a>Megoldás:

A következő megoldások bármelyike elháríthatja a problémát:

* Győződjön meg arról, hogy helyesen adta meg a parancsmag nevét.
* Győződjön meg arról, hogy a parancsmag létezik az Automation-fiókban, és nincsenek ütközések. Annak ellenőrzéséhez, hogy a parancsmag megtalálható-e, nyisson meg egy runbook szerkesztési módban, és keresse meg a könyvtárban megkeresni kívánt parancsmagot, vagy futtassa `Get-Command <CommandName>`. Miután ellenőrizte, hogy a parancsmag elérhető-e a fiók számára, és hogy a nevek nem ütköznek más parancsmagokkal vagy runbookok, adja hozzá a vászonhoz, és győződjön meg arról, hogy érvényes paramétert használ a runbook.
* Ha a név ütközik, és a parancsmag két különböző modulban érhető el, a probléma megoldásához használja a parancsmag teljesen minősített nevét. Használhatja például a **ModuleName\CmdletName**.
* Ha a helyszíni runbook egy hibrid feldolgozói csoportban hajtja végre, akkor győződjön meg arról, hogy a modul és a parancsmag telepítve van a hibrid feldolgozót futtató számítógépen.

## <a name="long-running-runbook"></a>Forgatókönyv: A hosszan futó runbook nem fejeződik be

### <a name="issue"></a>Probléma

A runbook 3 órán belül **leállított** állapotban jelenik meg. Az is előfordulhat, hogy a következő hibaüzenetet kapja:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Ez a viselkedés az Azure-beli munkafolyamatok tervezésekor, a Azure Automationon belüli folyamatok [valós megosztásának](../automation-runbook-execution.md#fair-share) figyelése miatt. Ha három óránál hosszabb ideig hajtja végre, a méltányos megosztás automatikusan leállítja a runbook. A valós megosztási időkorláttal meghaladó runbook állapota eltér a runbook típustól. A PowerShell és a Python runbookok **leállított** állapotra vannak állítva. A PowerShell-munkafolyamat runbookok beállítása **sikertelen**.

### <a name="cause"></a>Ok

A runbook egy Azure-beli homokozóban a méltányos megosztás által engedélyezett 3 órás korlátot futtatott.

### <a name="resolution"></a>Megoldás:

Egy javasolt megoldás, ha a runbook egy [hibrid runbook-feldolgozón](../automation-hrw-run-runbooks.md)futtatja.

A hibrid feldolgozók nem korlátozódnak az Azure-beli munkaterületek 3 órás, méltányos megosztásra vonatkozó runbook-korlátozására. A hibrid Runbook-feldolgozókon futó runbookok úgy kell kialakítani, hogy támogassa az újraindítási viselkedést, ha váratlan helyi infrastruktúra-problémák merülnek fel.

Egy másik lehetőség, hogy optimalizálja a runbook a [gyermek runbookok](../automation-child-runbooks.md)létrehozásával. Ha a runbook több erőforráson ugyanazt a függvényt futtatja, például egy adatbázis-művelet több adatbázison, akkor áthelyezheti a függvényt egy alárendelt runbook. Ezek az alárendelt runbookok külön folyamatokban párhuzamosan hajthatók végre. Ez a viselkedés csökkenti a szülő runbook befejezésének teljes időtartamát.

A gyermek runbook-forgatókönyvet engedélyező PowerShell-parancsmagok a következők:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – ez a parancsmag lehetővé teszi, hogy elindítson egy runbook, és adja át a paramétereket a runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – ha vannak olyan műveletek, amelyeket a gyermek runbook befejezése után kell végrehajtani, ez a parancsmag lehetővé teszi, hogy minden gyermeknél ellenőrizze a feladatok állapotát.

## <a name="expired webhook"></a>Forgatókönyv: Status: 400 hibás kérelem webhook meghívásakor

### <a name="issue"></a>Probléma

Amikor megpróbál meghívja egy webhookot egy Azure Automation runbook, a következő hibaüzenet jelenik meg:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Ok

A meghívni próbált webhook le van tiltva vagy lejárt.

### <a name="resolution"></a>Megoldás:

Ha a webhook le van tiltva, újra engedélyezheti a webhookot a Azure Portalon keresztül. Ha egy webhook lejárt, a webhookot törölni kell, majd újra létre kell hozni. Csak akkor [újíthat meg egy webhookot](../automation-webhooks.md#renew-webhook) , ha még nem járt le.

## <a name="429"></a>Forgatókönyv: 429: a kérelmek aránya jelenleg túl nagy...

### <a name="issue"></a>Probléma

A `Get-AzureRmAutomationJobOutput`-parancsmag futtatásakor a következő hibaüzenet jelenik meg:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, amikor a feladatok kimenetét olyan runbook kérdezi le, amely sok [részletes adatfolyammal](../automation-runbook-output-and-messages.md#verbose-stream)rendelkezik.

### <a name="resolution"></a>Megoldás:

A hiba kétféleképpen oldható fel:

* Szerkessze a runbook, és csökkentse az általa kibocsátott feladatokhoz tartozó adatfolyamok számát.
* Csökkentse a lekérdezni kívánt adatfolyamok számát a parancsmag futtatásakor. Ennek a viselkedésnek a követéséhez megadhatja a `-Stream Output` paramétert a `Get-AzureRmAutomationJobOutput` parancsmaghoz, így csak a kimeneti adatfolyamok olvashatók be. 

## <a name="cannot-invoke-method"></a>Forgatókönyv: a PowerShell-feladatok sikertelenek, hiba: a metódus nem hívható meg

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor elindítja a PowerShell-feladatot egy Azure-ban futó runbook:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha egy Azure-ban futó runbook indít el egy PowerShell-feladatot. Ez a viselkedés azért fordulhat elő, mert az Azure-beli homokozóban futó runbookok nem [teljes nyelvi módban](/powershell/module/microsoft.powershell.core/about/about_language_modes)fut.

### <a name="resolution"></a>Megoldás:

A hiba kétféleképpen oldható fel:

* A `Start-Job`használata helyett használja a `Start-AzureRmAutomationRunbook`t a runbook elindításához.
* Ha a runbook ez a hibaüzenet jelenik meg, futtassa azt egy hibrid Runbook-feldolgozón

Ha többet szeretne megtudni erről a viselkedésről és a Azure Automation Runbookok egyéb viselkedéséről, tekintse meg a [Runbook viselkedését](../automation-runbook-execution.md#runbook-behavior)ismertető témakört.

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Forgatókönyv: a Linux Hybrid Runbook Worker a Runbook aláírásakor kéri a jelszót

### <a name="issue"></a>Probléma

A Linux Hybrid Runbook Worker egy **sudo** parancsának futtatásakor a rendszer váratlan jelszót kér le.

### <a name="cause"></a>Ok

A Linux rendszerhez készült Log Analytics-ügynök nxautomationuser-fiókja helytelenül van konfigurálva a sudoers fájlban. A hibrid Runbook-feldolgozónak szüksége van a fiók engedélyeinek és egyéb adatmennyiségének megfelelő konfigurálására, hogy a runbookok a Linux Runbook-feldolgozó számára is aláírható legyen.

### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a hibrid Runbook Worker rendelkezik a GnuPG (GPG) végrehajtható fájllal a gépen.

* Ellenőrizze a nxautomationuser-fiók konfigurációját a sudoers fájlban. Lásd: [Runbookok futtatása hibrid Runbook-feldolgozón](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Forgatókönyv: a parancsmag sikertelen a PnP PowerShell runbook Azure Automation

### <a name="issue"></a>Probléma

Amikor egy runbook egy PnP PowerShell által generált objektumot ír közvetlenül a Azure Automation kimenetre, a parancsmag kimenete nem tud visszaküldeni az Automation szolgáltatásba.

### <a name="cause"></a>Ok

Ezt a problémát általában az okozza, ha Azure Automation feldolgozza a PnP PowerShell-parancsmagokat meghívó runbookok, például az **Add-pnplistitem**, anélkül, hogy a visszaadott objektumokat kellene befognia.

### <a name="resolution"></a>Megoldás:

Szerkessze a parancsfájlokat úgy, hogy bármilyen visszatérési értéket rendeljen a változókhoz, hogy a parancsmagok ne próbáljanak teljes objektumokat írni a standard kimenetre. Egy parancsfájl átirányíthatja a kimeneti adatfolyamot egy parancsmagra az alább látható módon.

```azurecli
  $null = add-pnplistitem
```
Ha a parancsfájl a parancsmag kimenetét elemzi, a szkriptnek egy változóban kell tárolnia a kimenetet, és a változót csak a kimenet továbbítása helyett kell módosítania.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="other"></a>A probléma nem szerepel a fenti felsorolásban

Az alábbi részekben a probléma megoldásához segítséget nyújtó dokumentáción kívül más gyakori hibák is szerepelnek.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>A hibrid runbook-feldolgozó nem futtat feladatokat vagy nem válaszol

Ha Azure Automation helyett hibrid feldolgozót használó feladatokat futtat, előfordulhat, hogy [a hibrid feldolgozót is](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)le kell állítania.

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>A runbook „Nincs engedély” hibaüzenettel vagy annak valamelyik variációjával meghiúsul

Előfordulhat, hogy a futtató fiókok nem rendelkeznek ugyanazokkal az engedélyekkel az Azure-erőforrásokhoz, mint az aktuális fiók. Győződjön meg arról, hogy a futtató fiók rendelkezik a parancsfájlban használt [erőforrások eléréséhez szükséges engedélyekkel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) .

### <a name="runbooks-were-working-but-suddenly-stopped"></a>A runbookok működtek, de váratlanul leálltak

* Ha a runbookok korábban már elvégezték, de leállították, győződjön meg arról, hogy a [futtató fiók](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal) nem járt le.
* Ha webhookokat használ a runbookok elindításához, győződjön meg arról, hogy a [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) nem járt le.

### <a name="issues-passing-parameters-into-webhooks"></a>Paraméterek átadása webhookokra

A paraméterek webhookok való átadásával kapcsolatos segítségért lásd: [runbook indítása webhookból](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

### <a name="issues-using-az-modules"></a>Az az modulok használatával kapcsolatos problémák

Ugyanazon Automation-fiókban az az modulok és a AzureRM modulok használata nem támogatott. További információt az [az modulok a runbookok](https://docs.microsoft.com/azure/automation/az-modules) című témakörben talál.

### <a name="inconsistent-behavior-in-runbooks"></a>Következetlen viselkedés a runbookokban

Kövesse a [Runbook végrehajtásának](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) utasításait, hogy elkerülje az egyidejű feladatokkal, a több alkalommal létrehozott erőforrásokkal vagy más, az runbookok-ben lévő, időzítésre érzékeny logikával kapcsolatos problémákat.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>A Runbook sikertelen a következő hiba miatt: nem engedélyezett, tiltott (403), vagy valamilyen variáció

Előfordulhat, hogy a futtató fiókok nem rendelkeznek ugyanazokkal az engedélyekkel az Azure-erőforrásokhoz, mint az aktuális fiók. Győződjön meg arról, hogy a futtató fiók rendelkezik a parancsfájlban használt [erőforrások eléréséhez szükséges engedélyekkel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) .

### <a name="runbooks-were-working-but-suddenly-stopped"></a>A runbookok működtek, de váratlanul leálltak

* Ha a runbookok korábban már elvégezték, de leállították, győződjön meg arról, hogy a futtató fiók nem járt le. Lásd: a [tanúsítvány megújítása](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Ha webhookokat használ a runbookok elindításához, győződjön meg arról, hogy a webhook [nem járt le](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

### <a name="passing-parameters-into-webhooks"></a>Paraméterek átadása a webhookoknak

A paraméterek webhookok való átadásával kapcsolatos segítségért lásd: [runbook indítása webhookból](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

### <a name="using-az-modules"></a>Az modulok használata

Ugyanazon Automation-fiókban az az modulok és a AzureRM modulok használata nem támogatott. Lásd [az az modulok a runbookok](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Önaláírt tanúsítványok használata

Önaláírt tanúsítványok használatához tekintse meg az [új tanúsítvány létrehozása](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)című témakört.

## <a name="recommended-documents"></a>Ajánlott dokumentumok

* [Runbook indítása Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Runbook végrehajtás a Azure Automationban](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
