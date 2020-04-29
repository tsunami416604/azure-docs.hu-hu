---
title: Runbook hibák elhárítása Azure Automation
description: Ismerje meg, hogy miként lehet elhárítani a Azure Automation runbookok kapcsolatban felmerülő problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ef3f50e79f4458845ec2e3df73e1e87766bfd0f4
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508785"
---
# <a name="troubleshoot-runbook-errors"></a>Runbook hibák elhárítása

 Ez a cikk az esetlegesen előforduló különböző runbook-hibákat és azok megoldásának módját ismerteti.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="diagnosing-runbook-issues"></a>Runbook problémák diagnosztizálása

Ha a runbook végrehajtása során hibákat kap a Azure Automationban, a következő lépésekkel diagnosztizálhatja a problémákat.

1. **Győződjön meg arról, hogy a runbook-parancsfájl sikeresen lefut a helyi gépen.** 

    A nyelvi referenciák és a tanulási modulok a [PowerShell-dokumentumok](/powershell/scripting/overview) vagy a [Python-dokumentumok](https://docs.python.org/3/) című témakörben találhatók. A szkript helyi végrehajtása a gyakori hibák felderítését és megoldását, például a következőket hajtja végre:

      * Hiányzó modulok
      * Szintaktikai hibák
      * Logikai hibák

2. **Vizsgálja meg a runbook- [hibák folyamait](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output).**

    Tekintse meg ezeket a streameket bizonyos üzenetekhez, és hasonlítsa össze azokat a cikkben leírt hibákkal.

3. **Győződjön meg arról, hogy a csomópontok és az Automation-munkaterület rendelkezik a szükséges modulokkal.** 

    Ha a runbook bármely modult importál, ellenőrizze, hogy elérhetők-e az Automation-fiókjához az [importálási modulok](../shared-resources/modules.md#importing-modules)részben felsorolt lépések segítségével. Frissítse a modulokat a legújabb verzióra az [Azure-modulok frissítése Azure Automationban](..//automation-update-azure-modules.md)című témakör útmutatásait követve. További hibaelhárítási információkért lásd: [modulok hibaelhárítása](shared-resources.md#modules).

4. **Ha a runbook fel van függesztve vagy váratlanul leáll.**

    * A [feladatok állapotának megadásával](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) meghatározhatja a runbook állapotát és a lehetséges okokat.
    * [További kimenet hozzáadása](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) a runbook annak azonosításához, hogy mi történjen a runbook felfüggesztése előtt.
    * [Kezelje](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) a feladatokban felmerülő kivételeket.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Forgatókönyv: a login-AzureRMAccount futtatása a bejelentkezéshez

### <a name="issue"></a>Probléma

A runbook végrehajtásakor a következő hibaüzenet jelenik meg:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha nem futtató fiókot használ, vagy lejárt a futtató fiók. Lásd: [Azure Automation futtató fiókok kezelése](https://docs.microsoft.com/azure/automation/manage-runas-account).

Ennek a hibának két elsődleges oka van:

* A AzureRM vagy az a modul különböző verziói vannak.
* Egy külön előfizetésben próbál hozzáférni az erőforrásokhoz.

### <a name="resolution"></a>Megoldás:

Ha ezt a hibaüzenetet egy AzureRM vagy az a modul frissítése után kapja meg, akkor az összes modult ugyanarra a verzióra kell frissítenie.

Ha egy másik előfizetésben lévő erőforrásokhoz próbál hozzáférni, az engedélyek konfigurálásához kövesse az alábbi lépéseket.

1. Nyissa meg az Automation futtató fiókot, és másolja az alkalmazás AZONOSÍTÓját és ujjlenyomatát.

    ![AZONOSÍTÓ és ujjlenyomat másolása](../media/troubleshoot-runbooks/collect-app-id.png)

1. Nyissa meg az előfizetéshez tartozó Access Control, ahol az Automation-fiók nem található, és adjon hozzá egy új szerepkör-hozzárendelést.

    ![Hozzáférés-vezérlés](../media/troubleshoot-runbooks/access-control.png)

1. Adja hozzá a korábban összegyűjtött alkalmazás-azonosítót. Válassza a közreműködői engedélyek lehetőséget.

    ![Szerepkör-hozzárendelés hozzáadása](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Másolja az előfizetés nevét.

1. Mostantól a következő runbook-kóddal tesztelheti az Automation-fiókjából a másik előfizetésre vonatkozó engedélyeket. Cserélje `"\<CertificateThumbprint\>"` le az értéket az 1. lépésben átmásolt értékre. Cserélje `"\<SubscriptionName\>"` le a értéket a 4. lépésben átmásolt értékre.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Forgatókönyv: nem található az Azure-előfizetés

### <a name="issue"></a>Probléma

A (z), `Select-AzureSubscription` `Select-AzureRMSubscription`, vagy `Select-AzSubscription` parancsmag használatakor a következő hibaüzenet jelenik meg:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Hiba

Ez a hiba akkor fordulhat elő, ha:

* Az előfizetés neve érvénytelen.
* Az előfizetést beolvasni próbáló Azure Active Directory felhasználó nem az előfizetés rendszergazdájaként van konfigurálva.
* A parancsmag nem érhető el.

### <a name="resolution"></a>Megoldás:

Az alábbi lépéseket követve megállapíthatja, hogy hitelesített-e az Azure-ban, és hozzáfér-e a kiválasztani kívánt előfizetéshez.

1. Annak ellenőrzéséhez, hogy a parancsfájl önállóan működik-e, tesztelje a Azure Automationon kívül.
2. A `Select-*` parancsmag futtatása előtt győződjön meg arról, hogy a parancsfájl a [AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) parancsmagot futtatja.
3. Hozzáadás `Disable-AzContextAutosave –Scope Process` a runbook elejéhez. Ez a parancsmag-hívás biztosítja, hogy a hitelesítő adatok csak az aktuális runbook végrehajtásához legyenek érvényesek.
4. Ha továbbra is megjelenik ez a hibaüzenet, módosítsa a kódját a `AzContext` paraméter hozzáadásával `Connect-AzAccount`, majd hajtsa végre a kódot.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Forgatókönyv: az Azure-hitelesítés nem sikerült, mert a többtényezős hitelesítés engedélyezve van

### <a name="issue"></a>Probléma

Az Azure-beli felhasználónevével és jelszavával történő hitelesítéskor a következő hibaüzenet jelenik meg:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Ok

Ha az Azure-fiókjában többtényezős hitelesítés van, nem használhat Azure Active Directory felhasználót az Azure-beli hitelesítéshez. Ehelyett tanúsítványt vagy szolgáltatásnevet kell használnia a hitelesítéshez.

### <a name="resolution"></a>Megoldás:

Ha az Azure-beli klasszikus üzemi modell-parancsmagokkal rendelkező tanúsítványt szeretne használni, tekintse meg az [Azure-szolgáltatások kezeléséhez szükséges tanúsítvány létrehozását és hozzáadását](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)ismertető témakört. Ha Azure Resource Manager-parancsmagokkal szeretne szolgáltatásnevet használni, tekintse meg az [egyszerű szolgáltatásnév létrehozása Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) és [a szolgáltatásnév hitelesítése az Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)használatával című témakört.

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Forgatókönyv: a get_SerializationSettings metódussal kapcsolatos feladatokban szereplő hiba jelenik meg

### <a name="issue"></a>Probléma

A runbook a következő hiba jelenik meg a feladatokban:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Ok

Ezt a hibát valószínűleg az okozza, hogy a AzureRM-ből a runbook-ben lévő moduloknak nem teljes áttelepítést használ. Ez azt okozhatja, hogy Azure Automation egy runbook-feladatot csak AzureRM-modulok használatával indíthat el, majd egy másik feladatot csak az az modulok használatával indít el, ami egy sandbox-összeomláshoz vezet. 

### <a name="resolution"></a>Megoldás:

Az az és a AzureRM parancsmagok használata nem ajánlott ugyanazon a runbook. Ha többet szeretne megtudni ezeknek a moduloknak a helyes használatáról, tekintse meg az [áttelepítés az modulokra](../shared-resources/modules.md#migrating-to-az-modules)című témakört.

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Forgatókönyv: a runbook sikertelen a következő hibával: a feladat meg lett szakítva

### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Ok

Ezt a hibát az elavult Azure-modulok használatával lehet okozni.

### <a name="resolution"></a>Megoldás:

A hiba megoldásához frissítse az Azure-modulokat a legújabb verzióra. 

1. Az Automation-fiókban kattintson a **modulok**, majd az **Azure-modulok frissítése**elemre. 
2. A frissítés nagyjából 15 percet vesz igénybe. Ha elkészült, futtassa újra a sikertelen runbook.

A modulok frissítésével kapcsolatos további információkért lásd: [Azure-modulok frissítése Azure Automationban](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Forgatókönyv: a Runbookok sikertelen a több előfizetés kezelésekor

### <a name="issue"></a>Probléma

A runbookok végrehajtásakor a runbook nem tudja kezelni az Azure-erőforrásokat.

### <a name="cause"></a>Ok

A runbook nem a megfelelő környezetet használja a futtatásakor.

### <a name="resolution"></a>Megoldás:

Előfordulhat, hogy az előfizetési környezet elvész, ha egy runbook több runbookok hív meg. Annak biztosítása érdekében, hogy az előfizetési környezet átkerüljön a runbookok, az ügyfélnek runbook kell adnia a `Start-AzureRmAutomationRunbook` kontextust a `AzureRmContext` paraméterben található parancsmagnak. A `Disable-AzureRmContextAutosave` parancsmaggal állítsa `Scope` `Process` be a paramétert annak biztosítására, hogy a megadott hitelesítő adatok csak az aktuális runbook legyenek felhasználva. További információ: [több előfizetés használata](../automation-runbook-execution.md#working-with-multiple-subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Forgatókönyv: a kifejezés nem ismerhető fel parancsmag, függvény, parancsfájl neveként

### <a name="issue"></a>Probléma

A runbook az alábbi példához hasonló hibával meghiúsul:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A parancsmagot tartalmazó modul nem lett importálva az Automation-fiókba.
* A parancsmagot tartalmazó modul importálva van, de elavult.

### <a name="resolution"></a>Megoldás:

A hiba elhárításához hajtsa végre az alábbi műveletek egyikét. 

* Az Azure-modulokkal kapcsolatban lásd: [Azure PowerShell-modulok frissítése Azure Automationban](../automation-update-azure-modules.md) , hogy megtudja, hogyan frissítheti a modulokat az Automation-fiókban.

* Nem Azure-modul esetén győződjön meg arról, hogy a modul az Automation-fiókba lett importálva.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Forgatókönyv: a runbook-feladatok indítását háromszor próbálta meg, de minden alkalommal nem indult el

### <a name="issue"></a>Probléma

A runbook a következő hiba miatt meghiúsul:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Ok

Ez a hiba az alábbi problémák egyike miatt fordul elő:

* Memória korlátja Egy feladat meghiúsulhat, ha több mint 400 MB memóriát használ. A sandbox számára lefoglalt memória dokumentált korlátai az [Automation szolgáltatás korlátain](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)találhatók. 

* Hálózati szoftvercsatornák. Az Azure-beli munkaterületek 1000 egyidejű hálózati szoftvercsatornára korlátozódnak. Lásd: az [Automation szolgáltatás korlátai](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* A modul nem kompatibilis. Előfordulhat, hogy a modul függőségei nem megfelelőek. Ebben az esetben a runbook általában egy vagy `Command not found` `Cannot bind parameter` egy üzenetet ad vissza.

* Nincs hitelesítés Active Directory a homokozóban. A runbook megpróbált meghívni egy Azure-beli homokozóban futó végrehajtható fájlt vagy alfolyamatot. A runbookok konfigurálása az Azure AD-val való hitelesítéshez az Azure Active Directory Authentication Library (ADAL) használatával nem támogatott.

* Túl sok kivételi érték. A runbook túl sok kivételi adatokat próbált meg írni a kimeneti adatfolyamba.

### <a name="resolution"></a>Megoldás:

* Memória korlátja, hálózati szoftvercsatorna. A memória korlátain belüli munkaterhelések használatának javasolt módjai a munkaterhelés több runbookok közötti felosztása, a memóriában kevesebb adat feldolgozása, a felesleges kimenetek írása a runbookok, valamint a PowerShell-munkafolyamatok runbookok való beírásának gyakorisága. A Clear metódussal, például `$myVar.clear`a paranccsal törölheti a változókat, `[GC]::Collect` és azonnal futtathatja a szemetet. Ezek a műveletek csökkentik a runbook memória-lábnyomát futtatókörnyezet közben.

* A modul nem kompatibilis. Frissítse az Azure-modulokat a [Azure Automation Azure PowerShell moduljainak frissítése](../automation-update-azure-modules.md)című témakör lépéseit követve.

* Nincs hitelesítés Active Directory a homokozóban. Az Azure AD-runbook való hitelesítéskor győződjön meg arról, hogy az Azure AD-modul elérhető az Automation-fiókjában. Ügyeljen arra, hogy a futtató fióknak meg kell adnia a szükséges engedélyeket a runbook automatizálható feladatok végrehajtásához.

  Ha a runbook nem hívhat meg egy Azure-beli homokozóban futó végrehajtható fájlt vagy alfolyamatot, használja a runbook [hibrid runbook-feldolgozón](../automation-hrw-run-runbooks.md). A hibrid feldolgozók nem korlátozódnak az Azure-beli munkaterületek által használt memória-és hálózati korlátokra.

* Túl sok kivételi érték. A feladatok kimeneti adatfolyama 1MB-ra van korlátozva. Győződjön meg arról, hogy a runbook egy végrehajtható fájlhoz vagy egy alfolyamathoz tartozó hívásokat csatol a és `try` `catch` a blokk használatával. Ha a műveletek kivételt jeleznek, a kódnak kell megírnia az üzenetet a kivételből egy Automation-változóba. Ezzel a technikával megakadályozható, hogy a rendszer beírja az üzenetet a feladatok kimeneti adatfolyamba.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Forgatókönyv: nem sikerült az Azure-fiókba való bejelentkezés

### <a name="issue"></a>Probléma

A `Connect-AzAccount` parancsmag használatakor a következő hibák valamelyike jelenik meg:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Ok

Ezek a hibák akkor jelentkeznek, ha a hitelesítőadat-eszköz neve érvénytelen. Az is előfordulhat, hogy az Automation hitelesítőadat-eszköz beállításához használt Felhasználónév és jelszó nem érvényes.

### <a name="resolution"></a>Megoldás:

A probléma okának megállapításához hajtsa végre a következő lépéseket:

1. Győződjön meg arról, hogy nincs speciális karaktere. A karakterek közé tartozik `\@` az Automation hitelesítőadat-eszköz neve, amelyet az Azure-hoz való kapcsolódáshoz használ.
2. Ellenőrizze, hogy használhatja-e a Azure Automation hitelesítő adatokban tárolt felhasználónevet és jelszót a helyi PowerShell ISE-szerkesztőben. Futtassa a következő parancsmagokat a PowerShell ISE-ben.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Ha a hitelesítés helyileg meghiúsul, nem állította be megfelelően a Azure Active Directory hitelesítő adatait. Tekintse át a hitelesítés az Azure-ban [Azure Active Directory blogbejegyzés használatával](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) című témakört, hogy helyesen állítsa be a Azure Active Directory fiókot.

4. Ha a hiba úgy tűnik, hogy átmeneti, próbálkozzon újra az újrapróbálkozási logikával a hitelesítési rutinhoz, hogy megbízhatóbb legyen a hitelesítés.

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
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Forgatókönyv: helytelen objektum-hivatkozás az Add-AzAccount hívásához

### <a name="issue"></a>Probléma

Ez a hibaüzenet akkor jelenik meg `Add-AzAccount`, ha a használatakor a `Connect-AzAccount` parancsmag aliasa:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha a runbook nem hajtja `Add-AzAccount` végre a megfelelő lépéseket az Automation-fiók hozzáadásának meghívása előtt. A szükséges lépések egyikének példája egy futtató fiókkal való bejelentkezés. A runbook használt helyes műveletekért lásd: [runbook-végrehajtás a Azure Automation-ben](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Forgatókönyv: az objektum hivatkozása nem az objektum egy példányára van beállítva

### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor egy gyermek runbook meghívása `Wait` a paraméterrel, és a kimeneti adatfolyam tartalmaz egy objektumot:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Ok

`Start-AzAutomationRunbook`a nem kezeli megfelelően a kimeneti adatfolyamot, ha az adatfolyam objektumokat tartalmaz.

### <a name="resolution"></a>Megoldás:

Javasoljuk, hogy hozzon létre egy lekérdezési logikát, és használja a [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) parancsmagot a kimenet lekéréséhez. Az alábbi logika mintája alább látható.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Forgatókönyv: a Runbook nem sikerül deszerializált objektum miatt

### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Ok

Ha a runbook egy PowerShell-munkafolyamat, akkor az összetett objektumokat egy deszerializált formátumban tárolja, hogy a runbook állapot maradjon, ha a munkafolyamat fel van függesztve.

### <a name="resolution"></a>Megoldás:

A probléma megoldásához használja az alábbi megoldások bármelyikét.

* Ha a komplex objektumokat az egyik parancsmagból egy másikba végzi, a parancsmagokat egy `InlineScript` tevékenységbe csomagolja.
* Adja meg a szükséges nevet vagy értéket a komplex objektumból a teljes objektum átadása helyett.
* PowerShell-munkafolyamatok runbook helyett használjon PowerShell-runbook.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Forgatókönyv: a Runbook feladata meghiúsult, mert a lefoglalt kvóta túllépte a korlátot

### <a name="issue"></a>Probléma

A runbook-feladata a következő hibával meghiúsul:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladatok végrehajtása meghaladja a fiókhoz tartozó 500 perces ingyenes kvótát. Ez a kvóta a feladat-végrehajtási feladatok összes típusára vonatkozik. Ezek közül néhány feladat teszteli a feladatot, elindítja a feladatot a portálról, webhookok használatával hajt végre feladatot, vagy a Azure Portal vagy az adatközpont használatával végrehajtja a feladat ütemezését. További információ az automatizálás díjszabásáról: az [Automation díjszabása](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Megoldás:

Ha havonta több mint 500 perces feldolgozást szeretne használni, módosítsa az előfizetést az ingyenes szintről az alapszintű csomagra.

1. Jelentkezzen be az Azure-előfizetésbe.
2. Válassza ki a frissítendő Automation-fiókot.
3. Kattintson a **Beállítások**, majd a **díjszabás**elemre.
4. Kattintson az **Engedélyezés** elemre az oldal alján a fiók alapszintű szintre való frissítéséhez.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Forgatókönyv: a parancsmag nem ismerhető fel egy runbook végrehajtásakor

### <a name="issue"></a>Probléma

A runbook-feladata a következő hibával meghiúsul:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Ok

Ezt a hibát akkor okozza a rendszer, ha a PowerShell-motor nem találja a runbook használt parancsmagot. Lehetséges, hogy a parancsmagot tartalmazó modul hiányzik a fiókból, a név ütközik a runbook nevével, vagy a parancsmag egy másik modulban is létezik, és az Automation nem tudja feloldani a nevet.

### <a name="resolution"></a>Megoldás:

A probléma megoldásához használja az alábbi megoldások bármelyikét.

* Győződjön meg arról, hogy helyesen adta meg a parancsmag nevét.
* Győződjön meg arról, hogy a parancsmag létezik az Automation-fiókban, és nincsenek ütközések. Annak ellenőrzéséhez, hogy a parancsmag megtalálható-e, nyisson meg egy runbook szerkesztési módban, és keresse meg a könyvtárban megkeresni kívánt parancsmagot `Get-Command <CommandName>`, vagy futtassa a parancsot. Miután ellenőrizte, hogy a parancsmag elérhető-e a fiók számára, és hogy a név nem ütközik más parancsmagokkal vagy runbookok, adja hozzá a parancsmagot a vászonhoz, és győződjön meg arról, hogy érvényes paramétert használ a runbook.
* Ha a név ütközik, és a parancsmag két különböző modulban érhető el, oldja meg a problémát a parancsmag teljes nevének használatával. Így használhatja például a következőt: `ModuleName\CmdletName`.
* Ha a helyszíni runbook egy hibrid feldolgozói csoportban hajtja végre, győződjön meg arról, hogy a modul és a parancsmag telepítve van a hibrid feldolgozót futtató számítógépen.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Forgatókönyv: A hosszan futó runbook nem fejeződik be

### <a name="issue"></a>Probléma

A runbook 3 órán belül leállított állapotban jelenik meg. Előfordulhat, hogy a következő hibaüzenet is megjelenik:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Ez a viselkedés az Azure-beli munkafolyamatok tervezésekor, a Azure Automationon belüli folyamatok [valós megosztásának](../automation-runbook-execution.md#fair-share) figyelése miatt. Ha egy folyamat három óránál hosszabb ideig fut, a valós megosztás automatikusan leállítja a runbook. A valós megosztási időkorláttal meghaladó runbook állapota eltér a runbook típustól. A PowerShell és a Python runbookok leállított állapotra vannak állítva. A PowerShell-munkafolyamat runbookok beállítása sikertelen.

### <a name="cause"></a>Ok

A runbook egy Azure-beli homokozóban a méltányos megosztás által engedélyezett 3 órás korlátot futtatott.

### <a name="resolution"></a>Megoldás:

Egy javasolt megoldás, ha a runbook egy [hibrid runbook-feldolgozón](../automation-hrw-run-runbooks.md)futtatja. A hibrid feldolgozók nem korlátozódnak az Azure-beli munkaterületek 3 órás, méltányos megosztásra vonatkozó runbook-korlátozására. A hibrid Runbook-feldolgozókon futó runbookok úgy kell kialakítani, hogy támogassa az újraindítási viselkedést, ha váratlan helyi infrastruktúra-problémák merülnek fel.

Egy másik megoldás a runbook optimalizálása [gyermek runbookok](../automation-child-runbooks.md)létrehozásával. Ha a runbook több erőforráson ugyanazt a függvényt futtatja, például egy adatbázis-műveletben több adatbázison, áthelyezheti a függvényt egy alárendelt runbook. Minden gyermek runbook párhuzamosan, külön folyamatban hajtja végre. Ez a viselkedés csökkenti a szülő runbook befejezésének teljes időtartamát.

A gyermek runbook-forgatókönyvet engedélyező PowerShell-parancsmagok a következők:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Ez a parancsmag lehetővé teszi, hogy elindítson egy runbookot, és paramétereket adjon át a számára.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Ha vannak olyan műveletek, amelyeket a gyermek runbook befejezését követően kell végrehajtani, ez a parancsmag lehetővé teszi, hogy minden gyermeknél ellenőrizze a feladatok állapotát.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Forgatókönyv: Status: 400 hibás kérelem webhook meghívásakor

### <a name="issue"></a>Probléma

Amikor egy Azure Automation runbook próbál meg meghívni egy webhookot, a következő hibaüzenet jelenik meg:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Ok

A meghívni próbált webhook le van tiltva vagy lejárt.

### <a name="resolution"></a>Megoldás:

Ha a webhook le van tiltva, újra engedélyezheti a webhookot a Azure Portalon keresztül. Ha a webhook lejárt, akkor törölnie kell, majd újra létre kell hoznia. Csak akkor [újíthat meg egy webhookot](../automation-webhooks.md#renew-webhook) , ha még nem járt le.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Forgatókönyv: 429: a kérelmek aránya jelenleg túl nagy...

### <a name="issue"></a>Probléma

A `Get-AzAutomationJobOutput` parancsmag futtatásakor a következő hibaüzenet jelenik meg:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha a feladatok kimenetét olyan runbook kérdezi le, amely sok [részletes adatfolyammal](../automation-runbook-output-and-messages.md#verbose-stream)rendelkezik.

### <a name="resolution"></a>Megoldás:

A hiba elhárításához hajtsa végre az alábbi műveletek egyikét.

* Szerkessze a runbook, és csökkentse az általa kibocsátott feladatokhoz tartozó adatfolyamok számát.

* Csökkentse a lekérdezni kívánt adatfolyamok számát a parancsmag futtatásakor. Ehhez a `Stream` [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) parancsmag paraméterének értékét állíthatja be csak kimeneti adatfolyamok lekéréséhez. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Forgatókönyv: a PowerShell-feladatok sikertelenek, hiba: a metódus nem hívható meg

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor elindítja a PowerShell-feladatot egy Azure-ban futó runbook:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Ok

Ez a hiba arra utalhat, hogy az Azure-beli homokozóban végrehajtott runbookok nem futtatható [teljes nyelvi módban](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Megoldás:

Ez a hiba kétféleképpen oldható meg.

* A [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)használata helyett a [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) használatával indítsa el a runbook.
* Próbálja meg futtatni a runbook egy hibrid Runbook-feldolgozón.

Ha többet szeretne megtudni erről a viselkedésről és a Azure Automation runbookok egyéb viselkedéséről, tekintse meg a következő témakört: [Runbook-végrehajtás Azure Automation](../automation-runbook-execution.md).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Forgatókönyv: a Linux Hybrid Runbook Worker a Runbook aláírásakor kéri a jelszót

### <a name="issue"></a>Probléma

A `sudo` Linux Hybrid Runbook Worker parancs futtatása nem várt jelszót kér le.

### <a name="cause"></a>Ok

A Linux rendszerhez készült Log Analytics-ügynök **nxautomationuser** -fiókja helytelenül van konfigurálva a **sudoers** fájlban. A hibrid Runbook-feldolgozónak szüksége van a fiók engedélyeinek és egyéb adatmennyiségének megfelelő konfigurálására, hogy a runbookok a Linux Runbook-feldolgozó számára is aláírható legyen.

### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a hibrid Runbook Worker rendelkezik a GnuPG (GPG) végrehajtható fájllal a gépen.

* Ellenőrizze a **nxautomationuser** -fiók konfigurációját a **sudoers** fájlban. Lásd: [Runbookok futtatása hibrid Runbook-feldolgozón](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Forgatókönyv: a parancsmag sikertelen a PnP PowerShell runbook Azure Automation

### <a name="issue"></a>Probléma

Amikor egy runbook egy PnP PowerShell által generált objektumot ír közvetlenül a Azure Automation kimenetre, a parancsmag kimenete nem tud visszaküldeni az Automation szolgáltatásba.

### <a name="cause"></a>Ok

Ez a probléma leggyakrabban akkor fordul elő, amikor a Azure Automation feldolgozza a PnP PowerShell-parancsmagokat meghívó runbookok, például `add-pnplistitem`a visszatérési objektumok kifogása nélkül.

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

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Forgatókönyv: Érvénytelen "tiltott" állapotkód, ha Key Vaultt használ egy runbook belül

### <a name="issue"></a>Probléma

Ha Azure Automation runbook keresztül próbál meg hozzáférni Key Vault, a következő hibaüzenet jelenik meg:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Ok

A probléma lehetséges okai:

* Nem használ futtató fiókot.
* Nem megfelelő engedélyek.

### <a name="resolution"></a>Megoldás:

#### <a name="not-using-run-as-account"></a>Nem használja a futtató fiókot

Kövesse az [5. lépés – hitelesítés hozzáadása az Azure-erőforrások kezeléséhez című témakört](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) , amely biztosítja, hogy a futtató fiókot használja a Key Vault eléréséhez. 

#### <a name="insufficient-permissions"></a>Nem megfelelő engedélyek

[Adja hozzá az Key Vaulthoz szükséges engedélyeket](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) annak biztosításához, hogy a futtató fiók megfelelő engedélyekkel rendelkezik a Key Vault eléréséhez. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>A probléma nem szerepel a fenti felsorolásban

Az alábbi szakaszokban más gyakori hibák is szerepelnek, és a probléma megoldásához segítséget nyújtó dokumentációt nyújtanak.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>A hibrid runbook-feldolgozó nem futtat feladatokat vagy nem válaszol

Ha Azure Automation helyett hibrid Runbook-feldolgozón futtat feladatokat, lehetséges, hogy [a hibrid feldolgozót is el kell hárítania](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>A Runbook nincs engedélye vagy valamilyen változat

Előfordulhat, hogy a futtató fiókok nem rendelkeznek ugyanazokkal az engedélyekkel az Azure-erőforrásokhoz, mint az aktuális fiók. Győződjön meg arról, hogy a futtató fiók rendelkezik a parancsfájlban használt [erőforrások eléréséhez szükséges engedélyekkel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) .

### <a name="issues-passing-parameters-into-webhooks"></a>Paraméterek átadása webhookokra

A paraméterek webhookok való átadásával kapcsolatos segítségért lásd: [runbook indítása webhookból](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Az az modulok használatával kapcsolatos problémák

A runbook-modulok AzureRM-ből az az-ba történő hiányos áttelepítésének használatával a sandbox összeomlik és runbook hibákat okozhat. Lásd: [modulok használata a runbookok](../automation-runbook-execution.md#using-modules-in-your-runbooks).

### <a name="inconsistent-behavior-in-runbooks"></a>Következetlen viselkedés a runbookokban

Kövesse a [Runbook végrehajtásának](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) utasításait, hogy elkerülje az egyidejű feladatokkal, a több alkalommal létrehozott erőforrásokkal vagy más, az runbookok-ben használt időérzékeny logikával kapcsolatos problémákat.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>A Runbook sikertelen a következő hiba miatt: nem engedélyezett, tiltott (403), vagy valamilyen variáció

Előfordulhat, hogy a futtató fiókok nem rendelkeznek ugyanazokkal az engedélyekkel az Azure-erőforrásokhoz, mint az aktuális fiók. Győződjön meg arról, hogy a futtató fiók rendelkezik a parancsfájlban használt [erőforrások eléréséhez szükséges engedélyekkel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) .

### <a name="runbooks-were-working-but-suddenly-stopped"></a>A runbookok működtek, de váratlanul leálltak

* Győződjön meg arról, hogy a futtató fiók nem járt le. Lásd: a [tanúsítvány megújítása](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Ha [webhookot](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) használ egy runbook elindításához, győződjön meg arról, hogy a webhook nem járt le.

### <a name="passing-parameters-into-webhooks"></a>Paraméterek átadása a webhookoknak

A paraméterek webhookok való átadásával kapcsolatos segítségért lásd: [runbook indítása webhookból](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-self-signed-certificates"></a>Önaláírt tanúsítványok használata

Önaláírt tanúsítványok használatához tekintse meg az [új tanúsítvány létrehozása](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)című témakört.

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Hozzáférés megtagadva az Azure-beli homokozó runbook való használatakor

Az Azure-beli homokozó megakadályozza az összes folyamaton kívüli COM-kiszolgáló elérését. Például egy munkapéldányos alkalmazás vagy runbook nem hívható meg Windows Management Instrumentation (WMI) vagy a Windows Installer szolgáltatásba (MSIServer. exe). A sandbox használatával kapcsolatos részletekért lásd: [Runbook végrehajtása Azure Automationban](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Ajánlott dokumentumok

* [Runbook végrehajtása az Azure Automationben](../automation-runbook-execution.md)
* [Runbook indítása Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Runbook végrehajtása az Azure Automationben](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Az Azure [@AzureSupport](https://twitter.com/azuresupport) -Közösség a megfelelő erőforrásokhoz való csatlakoztatásával, a hivatalos Microsoft Azure fiókkal csatlakozhat a felhasználói élmény fokozásához: válaszok, támogatás és szakértők.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
