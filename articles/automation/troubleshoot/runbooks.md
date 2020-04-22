---
title: Az Azure Automation runbookhibáinak elhárítása
description: Ismerje meg, hogyan háríthatja el és oldhatja meg az Azure Automation runbookokkal esetleg felmerülő problémákat.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ed25821f606b98bacf2acf3c2c389a8437406fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770903"
---
# <a name="troubleshoot-runbook-errors"></a>Runbook-hibák elhárítása

 Ez a cikk ismerteti a különböző Runbook-hibák, amelyek előfordulhatnak, és hogyan lehet megoldani őket.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="diagnosing-runbook-issues"></a>Runbook-problémák diagnosztizálása

Ha hibaüzenetet kap a Runbook-végrehajtás során az Azure Automationben, az alábbi lépésekkel diagnosztizálhatja a problémákat.

1. **Győződjön meg arról, hogy a runbook-parancsfájl végrehajtása sikeresen a helyi számítógépen.** 

    Tekintse meg a [PowerShell-dokumentumok](/powershell/scripting/overview) vagy [python docs](https://docs.python.org/3/) nyelvi referencia-és tanulási modulok. A parancsfájl helyi végrehajtása felderítheti és megoldhatja a gyakori hibákat, például a következőket:

      * Hiányzó modulok
      * Szintaktikai hibák
      * Logikai hibák

2. **Vizsgálja meg a runbook [hibaadatfolyamait.](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)**

    Tekintse meg ezeket az adatfolyamokat az egyes üzenetekért, és hasonlítsa össze őket a cikkben dokumentált hibákkal.

3. **Győződjön meg arról, hogy a csomópontok és az automatizálásmunkaterület rendelkezik a szükséges modulokkal.** 

    Ha a runbook importál ja a modulokat, ellenőrizze, hogy azok elérhetők-e az Automation-fiók számára a [modulok importálása](../shared-resources/modules.md#importing-modules)című részben felsorolt lépések segítségével. Frissítse a modulokat a legújabb verzióra az [Azure Automation Azure-modulok frissítésével](..//automation-update-azure-modules.md)kapcsolatos utasításokat követve. További hibaelhárítási információt a [Modulok hibaelhárítása című témakörben talál.](shared-resources.md#modules)

4. **Teendő, ha a runbook fel van függesztve, vagy váratlanul meghibásodik.**

    * [Ellenőrizze a feladat állapota](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) határozza meg runbook állapotait és néhány lehetséges okait.
    * [Adjon hozzá további kimenetet](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) a runbookhoz, hogy azonosítsa, mi történik a runbook felfüggesztése előtt.
    * Kezelje a feladatai által okozott [kivételeket.](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions)

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Forgatókönyv: A bejelentkezési adatok futtatása az AzureRM-fiókkal a bejelentkezéshez

### <a name="issue"></a>Probléma

A runbook végrehajtásakor a következő hibaüzenet jelenik meg:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha nem használ Futtatás másként fiókot, vagy a Futtatás másként fiók lejárt. Lásd: [Azure Automation Futtatás másként fiókok kezelése.](https://docs.microsoft.com/azure/automation/manage-runas-account)

Ennek a hibának két fő oka van:

* Az AzureRM vagy az Az modul különböző verziói vannak.
* Az erőforrásokat külön előfizetésben próbálja elérni.

### <a name="resolution"></a>Megoldás:

Ha egy AzureRM vagy Az modul frissítése után ez a hibaüzenet jelenik meg, az összes modult ugyanabba a verzióra kell frissítenie.

Ha egy másik előfizetés erőforrásaihoz próbál hozzáférni, az alábbi lépéseket követve konfigurálhatja az engedélyeket.

1. Nyissa meg az Automation Run As fiókot, és másolja az alkalmazásazonosítót és az ujjlenyomatot.

    ![Azonosító és ujjlenyomat másolása](../media/troubleshoot-runbooks/collect-app-id.png)

1. Nyissa meg az előfizetés hozzáférés-vezérlését, ahol az Automation-fiók NEM található, és adjon hozzá egy új szerepkör-hozzárendelést.

    ![Hozzáférés-vezérlés](../media/troubleshoot-runbooks/access-control.png)

1. Adja hozzá a korábban gyűjtött alkalmazásazonosítót. Válassza a Közreműködői engedélyek lehetőséget.

    ![Szerepkör-hozzárendelés hozzáadása](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Másolja az előfizetés nevére.

1. Most már használhatja a következő Runbook-kódot az Automation-fiók engedélyeinek teszteléséhez a másik előfizetéshez. Cserélje `"\<CertificateThumbprint\>"` le az 1. Cserélje `"\<SubscriptionName\>"` le a 4.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Forgatókönyv: Nem található az Azure-előfizetés

### <a name="issue"></a>Probléma

A , `Select-AzureSubscription` `Select-AzureRMSubscription`vagy `Select-AzSubscription` a parancsmaggal végzett munka során a következő hibaüzenet jelenik meg:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Hiba

Ez a hiba akkor fordulhat elő, ha:

* Az előfizetés neve érvénytelen.
* Az Azure Active Directory-felhasználó, aki megpróbálja beszerezni az előfizetés adatait nincs konfigurálva az előfizetés rendszergazdája.
* A parancsmag nem érhető el.

### <a name="resolution"></a>Megoldás:

Az alábbi lépéseket követve állapítsa meg, hogy hitelesítette-e az Azure-t, és hozzáfér-e a kijelölni kívánt előfizetéshez.

1. Győződjön meg arról, hogy a parancsfájl önállóan működik, tesztelje az Azure Automationen kívül.
2. Győződjön meg arról, hogy a parancsfájl a `Select-*` parancsmag futtatása előtt futtatja a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) parancsmamot.
3. Adja `Disable-AzContextAutosave –Scope Process` hozzá a runbook elejéhez. Ez a parancsmag-hívás biztosítja, hogy a hitelesítő adatok csak az aktuális runbook végrehajtására vonatkozzanak.
4. Ha továbbra is megjelenik ez a hibaüzenet, módosítsa a kódot a `AzContext` paraméter `Connect-AzAccount`hozzáadásával, majd hajtsa végre a kódot.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Forgatókönyv: Az Azure-hitelesítés sikertelen, mert a többtényezős hitelesítés engedélyezve van

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor azure-felhasználónevével és jelszavával hitelesíti az Azure-ba:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Ok

Ha többtényezős hitelesítéssel rendelkezik az Azure-fiókjában, nem használhat Azure Active Directory-felhasználót az Azure-ba való hitelesítéshez. Ehelyett a hitelesítéshez tanúsítványt vagy egyszerű szolgáltatást kell használnia.

### <a name="resolution"></a>Megoldás:

Ha klasszikus Azure-telepítési modellparancsmagokkal szeretne tanúsítványt használni, olvassa el [az Azure-szolgáltatások kezeléséhez használt tanúsítvány létrehozása és hozzáadása című tanúsítványt.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Ha egyszerű szolgáltatást szeretne használni az Azure Resource Manager parancsmagokkal, olvassa el az Egyszerű szolgáltatás létrehozása az [Azure Portal használatával és](../../active-directory/develop/howto-create-service-principal-portal.md) az egyszerű szolgáltatás [hitelesítése az Azure Resource Manager használatával.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Eset: Hibaüzenet jelenik meg a feladatstreamekben a get_SerializationSettings metódussal kapcsolatban.

### <a name="issue"></a>Probléma

A következő hiba jelenik meg a runbook feladatstreamjeiben:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AZAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Ok

Ezt a hibát az Okozza, hogy az AzureRM és az Az modul parancsmagjait egy runbookban használja. Ez akkor fordul elő, amikor importálja az Az modult az AzureRM-modul importálása előtt.

### <a name="resolution"></a>Megoldás:

Az és az AzureRM-parancsmagok nem importálhatók és nem használhatók ugyanabban a runbookban. Ha többet szeretne megtudni az Az-parancsmagokról az Azure Automationben, olvassa [el a Modulok kezelése az Azure Automationben](../shared-resources/modules.md).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Eset: A runbook sikertelen a hibával: Egy feladat meglett szakítva

### <a name="issue"></a>Probléma

A runbook a következő példához hasonló hibával sikertelen:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Ok

Ezt a hibát az elavult Azure-modulok használata okozhatja.

### <a name="resolution"></a>Megoldás:

Ezt a hibát az Azure-modulok legújabb verzióra való frissítésével oldhatja meg. 

1. Az Automation-fiókban kattintson a **Modulok**elemre, majd **az Azure-modulok frissítése parancsra.** 
2. A frissítés körülbelül 15 percet vesz igénybe. Miután befejeződött, futtassa újra a sikertelen runbookot.

Ha többet szeretne tudni a modulok frissítéséről, olvassa [el az Azure-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Eset: Runbooks fail kezelése esetén több előfizetések

### <a name="issue"></a>Probléma

Runbookok végrehajtásakor a runbook nem kezeli az Azure-erőforrásokat.

### <a name="cause"></a>Ok

A runbook nem a megfelelő környezetben fut.

### <a name="resolution"></a>Megoldás:

Az előfizetési környezet elveszhet, ha egy runbook több runbookot hív meg. Annak érdekében, hogy az előfizetési környezet átkerüljön a runbookok, az `AzureRmContext` ügyfél runbook adja át a környezetet a `Start-AzureRmAutomationRunbook` paraméter ben a parancsmag. Használja `Disable-AzureRmContextAutosave` a parancsmag `Scope` a paraméter `Process` készlet, győződjön meg arról, hogy a megadott hitelesítő adatok csak az aktuális runbook. További információ: [Több előfizetés megmunkálata.](../automation-runbook-execution.md#working-with-multiple-subscriptions)

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

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Eset: A kifejezés nem ismeri fel a parancsmag, a függvény, a parancsfájl nevét

### <a name="issue"></a>Probléma

A runbook a következő példához hasonló hibával sikertelen:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A parancsmast tartalmazó modul nem importálva az Automation-fiókba.
* A rendszer importálja a parancsmacst tartalmazó modult, de elavult.

### <a name="resolution"></a>Megoldás:

A hiba megoldásához végezze el az alábbi feladatok egyikét. 

* Egy Azure-modul, [hogyan frissítheti az Azure PowerShell-modulok az Azure Automation-ben,](../automation-update-azure-modules.md) hogyan frissítheti a modulokat az Automation-fiókban.

* Egy nem Azure-modul, győződjön meg arról, hogy a modul importált az Automation-fiókba.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Eset: A runbook-feladat indítása háromszor történt megkísérlésre, de nem sikerült minden alkalommal elindulni

### <a name="issue"></a>Probléma

A runbook a következő hibával sikertelen:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Ok

A hiba az alábbi problémák valamelyike miatt fordul elő:

* Memóriakorlát. Egy feladat sikertelen lehet, ha 400 MB-nál több memóriát használ. A rendszer a sandbox számára lefoglalt memória dokumentált korlátai az [Automation szolgáltatás korlátaiban](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)találhatók. 

* Hálózati szoftvercsatornák. Az Azure sandboxok 1000 egyidejű hálózati szoftvercsatornára korlátozódnak. Lásd: [Automatizálási szolgáltatáskorlátok](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Modul nem kompatibilis. Előfordulhat, hogy a modulfüggőségek nem megfelelőek. Ebben az esetben a runbook `Command not found` általában `Cannot bind parameter` egy vagy üzenetet ad vissza.

* Nincs hitelesítés az Active Directory val a sandbox. A runbook megkísérelt meghívni egy futtatható vagy részfolyamat, amely egy Azure-sandbox ban fut. Runbookok konfigurálása az Azure AD-vel az Azure Active Directory hitelesítési könyvtár (ADAL) használatával hitelesítésre nem támogatott.

* Túl sok kivételadat. A runbook túl sok kivételadatot próbált írni a kimeneti adatfolyamba.

### <a name="resolution"></a>Megoldás:

* Memóriakorlát, hálózati szoftvercsatornák. A memóriakorlátokon belül a munkafeladat felosztása több runbook között, kevesebb adat feldolgozása a memóriában, a runbookok felesleges kimenetének írása, valamint a PowerShell-munkafolyamat-runbookok beírásának mérlegelése javasolt módszer. Használja a tiszta módszert, például `$myVar.clear`a , `[GC]::Collect` a változók törléséhez és a szemétgyűjtés azonnali futtatásához. Ezek a műveletek csökkentik a runbook memóriaigényét futásidőben.

* Modul nem kompatibilis. Az Azure-modulok frissítéséhez kövesse az [Azure PowerShell-modulok frissítése](../automation-update-azure-modules.md)az Azure Automationben című lépéseit.

* Nincs hitelesítés az Active Directory sandboxhoz szolgáltatással. Amikor egy runbookkal hitelesíti az Azure AD-t, győződjön meg arról, hogy az Azure AD-modul elérhető az Automation-fiókban. Ügyeljen arra, hogy adja meg a Futtatás másként fiók nak a szükséges engedélyeket a runbook által automatizálható feladatok végrehajtásához.

  Ha a runbook nem tudja meghívni az Azure-sandboxban futó végrehajtható vagy részfolyamatot, használja a runbookot egy [hibrid Runbook-feldolgozón.](../automation-hrw-run-runbooks.md) A hibrid dolgozókat nem korlátozzák az Azure sandboxok memória- és hálózati korlátai.

* Túl sok kivételadat. Van egy 1 MB-os korláta a feladat kimeneti adatfolyam. Győződjön meg arról, hogy a runbook `try` egy `catch` végrehajtható vagy részfolyamathoz zárja a hívásokat, és blokkolja. Ha a műveletek kivételt okoznak, a kód írja be az üzenetet a kivételből egy Automation-változóba. Ez a módszer megakadályozza, hogy az üzenet a feladat kimeneti adatfolyamba kerüljön.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Forgatókönyv: Nem sikerült bejelentkezni az Azure-fiókba

### <a name="issue"></a>Probléma

A `Connect-AzAccount` parancsmaggal végzett munka során az alábbi hibák egyike jelenik meg:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Ok

Ezek a hibák akkor fordulnak elő, ha a hitelesítő egység neve érvénytelen. Ezek akkor is előfordulhatnak, ha az Automation hitelesítő adatok eszközének beállításához használt felhasználónév és jelszó érvénytelen.

### <a name="resolution"></a>Megoldás:

A hiba meghatározásához tegye a következő lépéseket:

1. Győződjön meg arról, hogy nincsenek különleges karakterek. Ezek a `\@` karakterek tartalmazzák a karaktert az Automation hitelesítő egység neve, amely az Azure-hoz való csatlakozáshoz használ.
2. Ellenőrizze, hogy használhatja-e a helyi PowerShell ISE-szerkesztőben az Azure Automation hitelesítő adatában tárolt felhasználónevet és jelszót. Futtassa a következő parancsmagokat a PowerShell ISE-ben.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. Ha a hitelesítés helyileg sikertelen, nem állította be megfelelően az Azure Active Directory hitelesítő adatait. Tekintse meg a [hitelesítés az Azure-ban az Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogbejegyzést az Azure Active Directory-fiók megfelelő beállítása.

4. Ha a hiba átmenetinek tűnik, próbálja meg újrapróbálkozási logikát hozzáadni a hitelesítési rutinhoz, hogy a hitelesítés hatékonyabbá tegye a hitelesítést.

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

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Eset: Az objektumhivatkozás nincs objektumpéldányra állítva

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor egy `Wait` gyermek runbookot a paraméterrel, és a kimeneti adatfolyam objektumot tartalmaz:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Ok

`Start-AzAutomationRunbook`nem kezeli megfelelően a kimeneti adatfolyamot, ha az adatfolyam objektumokat tartalmaz.

### <a name="resolution"></a>Megoldás:

Ajánlott egy lekérdezési logika megvalósítása, és a [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) parancsmag használatával a kimenet lekéréséhez. Ennek a logikának a mintája az alábbiakban van meghatározva.

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

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Eset: Runbook sikertelen, mert deszerializált objektum

### <a name="issue"></a>Probléma

A runbook a következő hibával sikertelen:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Ok

Ha a runbook egy PowerShell-munkafolyamat, összetett objektumokat tárol deszerializált formátumban, hogy a munkafolyamat felfüggesztése esetén megőrizte a runbook állapotát.

### <a name="resolution"></a>Megoldás:

A probléma megoldásához használja az alábbi megoldások bármelyikét.

* Ha összetett objektumokat csőel az egyik parancsmagból a másikba, `InlineScript` csomagolja ezeket a parancsmagokat egy tevékenységbe.
* Adja át a szükséges nevet vagy értéket az összetett objektumból ahelyett, hogy a teljes objektumot átadná.
* PowerShell-runbook használata a PowerShell-munkafolyamat-runbook helyett.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Eset: A Runbook-feladat sikertelen, mert a lefoglalt kvóta túllépte a kvótát

### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával sikertelen:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladat végrehajtása meghaladja a fiók 500 perces szabad kvótáját. Ez a kvóta a feladat-végrehajtási feladatok minden típusára vonatkozik. Ezek a feladatok egy feladat tesztelése, a feladat indítása a portálról, egy feladat végrehajtása webhookok használatával, vagy egy feladat ütemezése az Azure Portalon vagy az adatközpontban. Ha többet szeretne tudni az Automatizálás díjszabásáról, olvassa el az [Automatizálásdíj.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Megoldás:

Ha havonta több mint 500 perc feldolgozást szeretne használni, módosítsa az előfizetést az ingyenes szintről az alapszintű szintre.

1. Jelentkezzen be Azure-előfizetésbe.
2. Válassza ki a frissíteni kívánt Automation-fiókot.
3. Kattintson a **Beállítások** **gombra, majd az Árképzés parancsra.**
4. Kattintson az **Engedélyezés** gombra a lap alján, ha a fiókot az alapszintre szeretné frissíteni.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Eset: A parancsmag nem ismerhető fel a runbook végrehajtásakor

### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával sikertelen:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Ok

Ez a hiba akkor jön, ha a PowerShell-motor nem találja a runbookban használt parancsmamot. Lehetséges, hogy a parancsmacst tartalmazó modul hiányzik a fiókból, van egy név ütközés egy runbook nevét, vagy a parancsmag is létezik egy másik modulban, és az Automation nem tudja feloldani a nevet.

### <a name="resolution"></a>Megoldás:

A probléma megoldásához használja az alábbi megoldások bármelyikét.

* Ellenőrizze, hogy helyesen adta-e meg a parancsmag nevét.
* Győződjön meg arról, hogy a parancsmag létezik az Automation-fiókban, és hogy nincsenek ütközések. Annak ellenőrzéséhez, hogy a parancsmag jelen van-e, nyisson meg egy runbookot szerkesztési módban, és keresse meg a tárban keresett parancsmagát, vagy futtassa a parancsot. `Get-Command <CommandName>` Miután meggyőződött arról, hogy a parancsmag elérhető a fiók számára, és hogy nincsenek névütközések más parancsmagokkal vagy runbookokkal, adja hozzá a parancsmast a vászonhoz, és győződjön meg arról, hogy érvényes paraméterkészletet használ a runbookban.
* Ha névütközése van, és a parancsmag két különböző modulban érhető el, oldja meg a problémát a parancsmag teljesen minősített nevének használatával. Így használhatja például a következőt: `ModuleName\CmdletName`.
* Ha a runbook ot egy hibrid feldolgozócsoportban hajtja végre, győződjön meg arról, hogy a modul és a parancsmag telepítve van a hibrid feldolgozót tartalmazó gépen.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Eset: Egy hosszú ideig futó runbook nem fejeződik be

### <a name="issue"></a>Probléma

A runbook 3 óra futás után leállított állapotban jelenik meg. A következő hibaüzenet jelenhet meg:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Ez a viselkedés az Azure sandboxes, mert a [méltányos megosztás](../automation-runbook-execution.md#fair-share) nyomon követése folyamatok az Azure Automation-ben. Ha egy folyamat három óránál hosszabb ideig hajt végre, a méltányos megosztás automatikusan leállítja a runbookot. A runbook állapota, amely túllépi a méltányos megosztási időkorlátot, runbook-típusonként eltérő. A PowerShell és a Python runbookok leállított állapotra vannak beállítva. A PowerShell-munkafolyamat runbookjai sikertelenek.

### <a name="cause"></a>Ok

A runbook átfutotta a 3 órás korlátot, amelyet egy Azure-sandboxban való méltányos megosztás engedélyezett.

### <a name="resolution"></a>Megoldás:

Az egyik ajánlott megoldás a runbook futtatása [egy hibrid Runbook-feldolgozón.](../automation-hrw-run-runbooks.md) A hibrid feldolgozók nincsenek korlátozva az Azure sandboxes 3 órás méltányos megosztású runbook-korláttal. Runbookok futtatásához hibrid Runbook feldolgozók kell fejleszteni, hogy támogassa az újraindítási viselkedést, ha nem várt helyi infrastruktúra problémák.

Egy másik megoldás a runbook optimalizálása [gyermek runbookok](../automation-child-runbooks.md)létrehozásával. Ha a runbook hurkok ugyanazon a függvényen keresztül több erőforrás, például egy adatbázis-művelet több adatbázison, áthelyezheti a függvényt egy gyermek runbook. Minden gyermek runbook végrehajtása párhuzamosan egy külön folyamat. Ez a viselkedés csökkenti a szülő runbook befejezéséhez szükséges teljes időt.

A PowerShell-parancsmagok, amelyek lehetővé teszik a gyermek runbook forgatókönyv:

* [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). Ez a parancsmag lehetővé teszi, hogy elindítson egy runbookot, és paramétereket adjon át a számára.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). Ha vannak olyan műveletek, amelyeket a gyermek runbook befejezése után kell végrehajtani, ez a parancsmag lehetővé teszi, hogy ellenőrizze a feladat állapotát minden gyermek.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Forgatókönyv: Állapot: 400 hibás kérelem webhook hívásakor

### <a name="issue"></a>Probléma

Amikor megpróbál meghívni egy webhookot egy Azure Automation-runbookhoz, a következő hibaüzenet jelenik meg:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Ok

A hívni kívánt webhook le van tiltva, vagy lejárt.

### <a name="resolution"></a>Megoldás:

Ha a webhook le van tiltva, újra engedélyezheti a webhook az Azure Portalon keresztül. Ha a webhook lejárt, törölnie kell, majd újra létre kell hoznia. Csak [akkor újíthatja meg a webhookot,](../automation-webhooks.md#renew-webhook) ha még nem járt le.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Forgatókönyv: 429: A kérelem aránya jelenleg túl nagy...

### <a name="issue"></a>Probléma

A parancsmag futtatásakor `Get-AzAutomationJobOutput` a következő hibaüzenet jelenik meg:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, ha a feladatkimenetet olyan runbookról kéri le, amely sok [részletes adatfolyamot rendelkezik.](../automation-runbook-output-and-messages.md#verbose-stream)

### <a name="resolution"></a>Megoldás:

A hiba megoldásához tegye az alábbiak egyikét.

* Szerkesztheti a runbookot, és csökkentheti az általa kibocsátott feladatstreamek számát.

* Csökkentse a parancsmag futtatásakor beolvasandó adatfolyamok számát. Ehhez beállíthatja a `Stream` [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) parancsmag paraméterének értékét, hogy csak a kimeneti adatfolyamokat olvassa be. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Eset: A PowerShell-feladat hiba miatt sikertelen: Metódus nem hívható meg

### <a name="issue"></a>Probléma

A következő hibaüzenet jelenik meg, amikor egy PowerShell-feladatot indít el egy Azure-ban futó runbookban:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Ok

Ez a hiba azt jelezheti, hogy az Azure-sandboxban futó runbookok nem futtathatók [teljes nyelvi módban.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Megoldás:

A hiba kétféleképpen oldható meg.

* A [Start-Job](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)használata helyett használja [a Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) a runbook elindításához.
* Próbálja meg futtatni a runbookot egy hibrid Runbook-feldolgozón.

Ha többet szeretne megtudni erről a viselkedésről és az Azure Automation-runbookok egyéb viselkedéséről, olvassa el a [Runbook-viselkedés című témakört.](../automation-runbook-execution.md#runbook-behavior)

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Eset: A Linux hibrid Runbook-feldolgozó egy jelszót kér a runbook aláírásakor

### <a name="issue"></a>Probléma

A `sudo` linuxos hibrid runbook-feldolgozó parancsfuttatása váratlan jelszót kér.

### <a name="cause"></a>Ok

A Log Analytics ügynök Linux-ügynök **nxautomationuser** fiókja nincs megfelelően konfigurálva a **sudoers** fájlban. A hibrid Runbook-feldolgozónak szüksége van a fiókengedélyek és egyéb adatok megfelelő konfigurációjára, hogy aláírhassa a Runbookokat a Linux Runbook Worker en.

### <a name="resolution"></a>Megoldás:

* Győződjön meg arról, hogy a hibrid Runbook-feldolgozó rendelkezik a GnuPG (GPG) végrehajtható a számítógépen.

* Ellenőrizze az **nxautomationuser** fiók konfigurációját a **sudoers** fájlban. Lásd: [Runbookok futtatása hibrid Runbook-feldolgozón](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Eset: A Parancsmag meghibásodott az Azure Automation PnP PowerShell-runbookjában

### <a name="issue"></a>Probléma

Amikor egy runbook közvetlenül az Azure Automation-kimenetre ír egy PnP PowerShell által létrehozott objektumot, a parancsmag kimenete nem streamelhető vissza az Automation be.

### <a name="cause"></a>Ok

Ez a probléma leggyakrabban akkor fordul elő, amikor az Azure Automation folyamatok runbookok, amelyek meghívja a PnP PowerShell-parancsmag, például anélkül, `add-pnplistitem`hogy a visszatérési objektumok at.

### <a name="resolution"></a>Megoldás:

A parancsfájlok szerkesztésével rendeljen visszaértékeket a változókhoz, hogy a parancsmagok ne próbáljanak egész objektumokat írni a szabványos kimenetre. A parancsfájl átirányíthatja a kimeneti adatfolyamot egy parancsmagba az alábbiak szerint.

```azurecli
  $null = add-pnplistitem
```

Ha a parancsfájl parancsmag kimenetet elemez, a parancsfájlnak egy változóban kell tárolnia a kimenetet, és a kimenet egyszerű streamelése helyett manipulálnia kell a változót.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>Eset: Érvénytelen állapotkód "Tiltott" a Key Vault runbookon belüli használatakor

### <a name="issue"></a>Probléma

Amikor egy Azure Automation-runbookon keresztül próbál hozzáférni a Key Vaulthoz, a következő hibaüzenet jelenik meg:

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>Ok

A probléma lehetséges okai:

* Nem használja a Futtatás másként fiókot.
* Nincs elegendő engedélye.

### <a name="resolution"></a>Megoldás:

#### <a name="not-using-run-as-account"></a>Nem használja a Futtatás másként fiókot

Kövesse az [5.](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) 

#### <a name="insufficient-permissions"></a>Nincs elég engedély

Kövesse az [Engedélyek hozzáadása a Key Vaulthoz](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) című lépéseket, és győződjön meg arról, hogy a Futtatás másként fiók megfelelő engedélyekkel rendelkezik a Key Vault eléréséhez. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>A problémám nem szerepel a fenti felsorolásban

Az alábbi szakaszok felsorolják az egyéb gyakori hibákat, és alátámasztó dokumentációt tartalmaznak a probléma megoldásához.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>A hibrid runbook-feldolgozó nem futtat feladatokat vagy nem válaszol

Ha az Azure Automation helyett hibrid Runbook-feldolgozón futtat feladatokat, előfordulhat, hogy magának a hibrid dolgozónak kell [elhárítania a feladatokat.](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>A Runbook sikertelen, engedély nélkül vagy valamilyen változatban

Futtatás másként fiókok előfordulhat, hogy nem rendelkezik az Azure-erőforrásokkal való azonos engedélyekkel, mint az aktuális fiók. Győződjön meg arról, hogy a Futtatás másként fiók rendelkezik-e a parancsfájlban használt [erőforrások eléréséhez szükséges engedélyekkel.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

### <a name="issues-passing-parameters-into-webhooks"></a>Problémák átadása paraméterek webhooks

A paraméterek webhookokba való átvitelével kapcsolatban a [Runbook indítása webhookból](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook)című témakörben talál segítséget.

### <a name="issues-using-az-modules"></a>Az modulok használatával kapcsolatos problémák

Az modulok és az AzureRM-modulok használata ugyanabban az Automation-fiókban nem támogatott. További részletekért tekintse meg [az Az modulokat a runbookokban.](https://docs.microsoft.com/azure/automation/az-modules)

### <a name="inconsistent-behavior-in-runbooks"></a>Következetlen viselkedés a runbookokban

Kövesse a [Runbook-végrehajtás](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) útmutatóját, hogy elkerülje az egyidejű feladatokkal, az erőforrások többszöri létrehozásával vagy a runbookok más időzítés-érzékeny logikájával kapcsolatos problémákat.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook nem sikerül a hiba Nincs engedély, Tiltott (403), vagy valamilyen változata

Futtatás másként fiókok előfordulhat, hogy nem rendelkezik az Azure-erőforrásokkal való azonos engedélyekkel, mint az aktuális fiók. Győződjön meg arról, hogy a Futtatás másként fiók rendelkezik-e a parancsfájlban használt [erőforrások eléréséhez szükséges engedélyekkel.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

### <a name="runbooks-were-working-but-suddenly-stopped"></a>A runbookok működtek, de váratlanul leálltak

* Győződjön meg arról, hogy a Futtatás másként fiók nem járt le. Lásd: [a tanúsítvány megújítása](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Ha egy [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) ot használ egy runbook indításához, győződjön meg arról, hogy a webhook nem járt le.

### <a name="passing-parameters-into-webhooks"></a>Paraméterek átadása a webhookoknak

A paraméterek webhookokba való átvitelével kapcsolatban a [Runbook indítása webhookból](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook)című témakörben talál segítséget.

### <a name="using-az-modules"></a>Az modulok használata

Az modulok és az AzureRM-modulok használata ugyanabban az Automation-fiókban nem támogatott. Lásd: [Az modulok a runbookokban.](https://docs.microsoft.com/azure/automation/az-modules)

### <a name="using-self-signed-certificates"></a>Önaláírt tanúsítványok használata

Az önaláírt tanúsítványok használatához olvassa el [Az új tanúsítvány létrehozása témakört.](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Hozzáférés megtagadva, ha Az Azure sandbox egy runbook

Az Azure sandbox megakadályozza a hozzáférést az összes folyamaton kívüli COM-kiszolgálók. Egy sandboxos alkalmazás vagy runbook például nem hívhatja be a Windows Management Instrumentation (WMI) szolgáltatást, illetve a Windows Installer szolgáltatást (msiserver.exe). A sandbox használatáról a [Runbook-végrehajtás az Azure Automationben](https://docs.microsoft.com/azure/automation/automation-runbook-execution)című témakörben talál további információt.

## <a name="recommended-documents"></a>Ajánlott dokumentumok

* [Runbook indítása az Azure Automationben](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Runbook végrehajtása az Azure Automationben](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba – a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Ha további segítségre van szüksége, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
