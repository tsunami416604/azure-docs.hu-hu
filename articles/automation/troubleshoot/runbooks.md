---
title: Az Azure Automation-Runbookokkal kapcsolatos hibák elhárítása
description: Ismerje meg, az Azure Automation-runbookokkal kapcsolatos problémák elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b34a1716d077aeead572c60d0c6b9bcad60a5b1e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005420"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbookokkal kapcsolatos hibák elhárítása

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Az Azure Automation-runbookok használatakor hitelesítési hibák

### <a name="sign-in-failed"></a>Forgatókönyv: Jelentkezzen be Azure-fiókot nem sikerült

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor dolgozik a `Add-AzureAccount` vagy `Connect-AzureRmAccount` parancsmagok.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha az eszköz hitelesítő adat neve nem érvényes. Ez a hiba akkor is előfordulhat, ha a felhasználónevet és jelszót, amellyel az Automation-hitelesítőeszközt beállítása nem érvényes.

#### <a name="resolution"></a>Megoldás:

Annak megállapításához, hogy mi okozza, tegye a következőket:  

1. Győződjön meg arról, hogy nem kell semmilyen speciális karaktereket. Ezek a karakterek a következők a **\@** Automation nevű hitelesítő adat eszköz csatlakozhat az Azure-ban használt karakter.  
2. Ellenőrizze, hogy használhatja-e a felhasználónevet és jelszót, amely tárolja az Azure Automation hitelesítő adat a helyi PowerShell ISE-szerkesztőben. Teheti meg, ellenőrizze a felhasználónév és jelszó helyességét a PowerShell ISE-ben a következő parancsmag futtatásával:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Ha a hitelesítés helyben nem sikerül, az azt jelenti, hogy Ön még nem állította be az Azure Active Directory hitelesítő adatai megfelelően. Tekintse meg [hitelesítése az Azure-ban az Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) blogbejegyzés megfelelően állítsa be az Azure Active Directory-fiók létrehozása.  

4. Ha úgy tűnik, például átmeneti hiba, próbálja meg újrapróbálkozási logika hozzáadása a hitelesítési rutin, hogy hitelesítése robusztusabb.

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

### <a name="unable-to-find-subscription"></a>Forgatókönyv: Nem található az Azure-előfizetés

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor dolgozik a `Select-AzureSubscription` vagy `Select-AzureRmSubscription` parancsmagok:

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Hiba

Ez a hiba akkor fordulhat elő, ha:

* Az előfizetés neve nem érvényes

* Az Azure Active Directory felhasználó megpróbálja beolvasni az előfizetés részleteinek nincs beállítva az előfizetés-rendszergazdaként.

#### <a name="resolution"></a>Megoldás:

A következő lépésekkel határozza meg, ha már hitelesített, Azure-ba, és rendelkezik hozzáféréssel az előfizetéshez, válasszon kívánt:  

1. Győződjön meg arról, hogy önálló működik, az Azure Automation-en kívül parancsprogram teszteléséhez.
2. Győződjön meg arról, hogy futtassa a `Add-AzureAccount` parancsmag futtatása előtt a `Select-AzureSubscription` parancsmagot. 
3. Adjon hozzá `Disable-AzureRmContextAutosave –Scope Process` a runbook elejére. Ez a parancsmag biztosítja, hogy a hitelesítő adatokat csak az aktuális runbook végrehajtása vonatkoznak.
4. Ha továbbra is látja ezt a hibaüzenetet, módosítsa a kódot adja hozzá a **AzureRmContext** paraméter következő a `Add-AzureAccount` parancsmag majd futtassa a kódot.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Forgatókönyv: A hitelesítés sikertelen volt, mert engedélyezve van a multi-factor authentication szolgáltatás Azure-bA

#### <a name="issue"></a>Probléma

Az Azure-bA az Azure felhasználónevével és jelszavával való hitelesítéskor a következő hibaüzenetet kapja:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Ok

A multi-factor authentication szolgáltatás az Azure-fiókkal rendelkezik, ha egy Azure Active Directory-felhasználó nem használható az Azure-beli hitelesítésre. Ehelyett kell egy tanúsítványt, vagy egy egyszerű szolgáltatás használatával Azure-beli hitelesítésre.

#### <a name="resolution"></a>Megoldás:

A klasszikus Azure üzemi modell parancsmagokban tanúsítványt használ, tekintse meg [létrehozása és kezelése az Azure-szolgáltatások egy tanúsítvány hozzáadása.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Egy egyszerű szolgáltatást az Azure Resource Manager parancsmagjainak használatához tekintse meg [az egyszerű szolgáltatásnév létrehozása az Azure portal használatával](../../active-directory/develop/howto-create-service-principal-portal.md) és [hitelesítése egy egyszerű szolgáltatást az Azure Resource Managerrel.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>A forgatókönyvek használata során előforduló gyakori hibák

### <a name="child-runbook-object"></a>Gyermek runbook hibát ad vissza, ha a kimeneti adatfolyamba tartalmaz objektumokat, hanem az egyszerű adattípusok

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor egy childrunbook való meghívása a `-Wait` kapcsoló és a kimeneti adatfolyamba és objektum:

```
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Ok

Egy ismert probléma, a [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) nem megfelelően kezeli a a kimeneti adatfolyamba Ha objektumokat tartalmaz.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához, javasoljuk, hogy inkább egy lekérdezési a logikát alkalmazzák, és használjon a [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) parancsmag kimenetének lekéréséhez. A logikai mintát a következő példában definiálva van.

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

### <a name="get-serializationsettings"></a>Forgatókönyv: A feladatstreamek a get_SerializationSettings metódus kapcsolatos hibaüzenet jelenik meg

#### <a name="issue"></a>Probléma

Az a feladatstreamek egy runbook a következő üzenet jelenik meg a hiba:

```
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

Ez a hiba okozza a parancsmagok AzureRM- és Az használata a runbookokban. Ez akkor fordul elő, amikor importálja `Az` importálása előtt `AzureRM`.

#### <a name="resolution"></a>Megoldás:

Nem importálható az és AzureRM-parancsmagok és a talál további információt Az támogatása az Azure Automationben, ugyanaz a runbook használt [házirendmodul-támogatás Az Azure Automation](../az-modules.md).

### <a name="task-was-cancelled"></a>Forgatókönyv: A runbook a következő hibával meghiúsul: A feladatot törölték

#### <a name="issue"></a>Probléma

A runbook egy a következőhöz hasonló hibaüzenettel meghiúsul:

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Ok

Ezt a hibát okozhatja az elavult Azure-modulokat.

#### <a name="resolution"></a>Megoldás:

Ez a hiba megoldhatók az Azure-modulok frissítése a legújabb verzióra.

Az Automation-fiókban kattintson **modulok**, és kattintson a **frissítés az Azure-modulok**. A frissítés beolvassa körülbelül 15 perc, a folyamat végén futtassa újra a runbook sikertelen volt. A modulok frissítésével kapcsolatos további tudnivalókért lásd: [frissítés az Azure-modulokat az Azure Automationben](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Forgatókönyv: Runbookok esetén több előfizetés sikertelen

#### <a name="issue"></a>Probléma

A runbookok végrehajtásakor `Start-AzureRmAutomationRunbook`, a runbook nem Azure-erőforrások kezeléséhez.

#### <a name="cause"></a>Ok

A runbook nem a megfelelő környezetet használja, futtatásakor.

#### <a name="resolution"></a>Megoldás:

Több előfizetés használata, ha az előfizetési környezet elveszhetnek, amikor a runbookok meghívása. Győződjön meg arról, hogy a runbookok átadott az előfizetési környezetet, adja hozzá a `AzureRmContext` a parancsmagot, és azt a környezetet pass paramétert. Emellett javasoljuk, hogy használja a `Disable-AzureRmContextAutosave` parancsmagot a **folyamat** biztosítják, hogy csak a használt hitelesítő adatokat használják az aktuális runbook hatókör.

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

### <a name="not-recognized-as-cmdlet"></a>Forgatókönyv: A runbook végrehajtása sikertelen, mert hiányzik a parancsmag

#### <a name="issue"></a>Probléma

A runbook egy a következőhöz hasonló hibaüzenettel meghiúsul:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő egy alapján:

1. A modul, amely tartalmazza a parancsmag nem importálhatók az automation-fiók
2. A modul, amely tartalmazza a parancsmag importálja, de nem naprakész

#### <a name="resolution"></a>Megoldás:

Ez a hiba a következő feladatok végrehajtásával oldható meg:

Ha a modul egy Azure-modul, olvassa el [frissítése az Azure PowerShell-modulok az Azure Automationben](../automation-update-azure-modules.md) megtudhatja, hogyan frissíthető a modulokat az automation-fiók.

Ha egy különálló modul, ellenőrizze, hogy a modul importálása az Automation-fiókban.

### <a name="job-attempted-3-times"></a>Forgatókönyv: A runbook-feladat indítása háromszor történt kísérlet, de nem indult el, hogy minden alkalommal, amikor

#### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, az alábbi problémák egyike miatt:

1. Memória felső korlátja. A következő címen található egy tesztkörnyezet számára lefoglalt memória dokumentált korlátait [Automation Szolgáltatáskorlátok](../../azure-subscription-service-limits.md#automation-limits). Egy feladat sikertelen lehet, ha 400 MB-nál több memóriát használ.

2. Hálózati szoftvercsatorna. Azure próbakörnyezetbe lefordítja a rendszer legfeljebb 1000 egyidejű hálózati szoftvercsatorna ismertetett módon [Automation Szolgáltatáskorlátok](../../azure-subscription-service-limits.md#automation-limits).

3. A modul inkompatibilis. Ez a hiba akkor fordulhat elő, ha a modul függőségek nem megfelelőek, és ha nem, a runbook általában adja vissza egy "parancs nem található" vagy "Nem köthető a paraméter" üzenet.

4. A runbook meghívása egy végrehajtható fájl vagy egy runbook, amely egy Azure védőfal subprocess történt kísérlet. Ez a forgatókönyv nem támogatott az Azure próbakörnyezetbe lefordítja a.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:

* A memória felső korlátja belül működik javasolt módszerek a következők felosztása több runbook között, nem processzormagonkénti memória, nem a szükségtelen kimeneti írási a runbookok az adatok feldolgozása vagy fontolja meg, hány ellenőrzőpontot ír be a PowerShell-munkafolyamat runbookok. Használhatja például a Törlés metódust `$myVar.clear()` tisztítsa meg a változót és a `[GC]::Collect()` szemétgyűjtés azonnal futtatni. Ezek a műveletek a runbook memóriaigénye csökkentheti a Futtatás ideje alatt.

* A lépéseket követve az Azure-modulok frissítése [frissítése az Azure PowerShell-modulok az Azure Automationben](../automation-update-azure-modules.md).  

* Egy másik megoldás, ha a runbook futtatása egy [hibrid Runbook-feldolgozó](../automation-hrw-run-runbooks.md). Hibrid feldolgozók nem korlátozza, amelyek az Azure próbakörnyezetbe lefordítja a memória és a hálózati korlátok.

* A runbook egy folyamat (például .exe vagy subprocess.call) meghívásához szükséges, ha szüksége lesz a runbook futtatását egy [hibrid Runbook-feldolgozó](../automation-hrw-run-runbooks.md).

### <a name="fails-deserialized-object"></a>Forgatókönyv: Runbook deszerializált objektum miatt meghiúsul

#### <a name="issue"></a>Probléma

A runbook a következő hibával meghiúsul:

```
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Ok

Ha a forgatókönyv egy PowerShell-munkafolyamat, összetett objektumok tárol deszerializált formátumát a runbook állapota továbbra is fennáll, ha a munkafolyamat fel van függesztve.

#### <a name="resolution"></a>Megoldás:

A probléma megoldásához a következő három megoldások valamelyikét:

1. Ha egy másikra egy parancsmag összetett objektumokat használ átirányításával, tegye ezeket a parancsmagokat egy InlineScript.
2. A nevét vagy a szükséges érték átadása helyett a teljes objektum átadását a komplex objektumot.
3. Használjon PowerShell-runbook helyett egy PowerShell-munkafolyamati forgatókönyv.

### <a name="runbook-fails"></a>Forgatókönyv: A Runbook sikertelen lesz, de működik, ha helyileg futtatta

#### <a name="issue"></a>Probléma

A parancsfájl futása sikertelen, amikor egy runbook lefutott, de működik, amikor helyileg futtatta.

#### <a name="cause"></a>Ok

A parancsfájl meghiúsulhat egy runbook számítógépként futtatva sem a következő okok valamelyike:

1. Hitelesítési problémák
2. Nem állnak szükséges modulokat, importált vagy elavult.
3. Előfordulhat, hogy a felhasználói beavatkozás során arra kéri a a szkriptet.
4. Egyes modulok győződjön meg arról, feltételezéseket kódtárakat, amelyek a Windows-számítógépeken találhatók. Ezek a könyvtárak nem lehet egy tesztkörnyezet.
5. Egyes modulok támaszkodik, amely eltér a védőfal elérhető egy verzióját.

#### <a name="resolution"></a>Megoldás:

A következő megoldások valamelyikét megoldhatja a problémát:

1. Győződjön meg arról, hogy megfelelően vannak [hitelesítés az Azure-](../manage-runas-account.md).
2. Győződjön meg, hogy a [Azure-modulokat: importált és naprakész](../automation-update-azure-modules.md).
3. Győződjön meg arról, hogy a parancsmagok egyik információkat kéri. Ez a viselkedés nem támogatott a runbookokban.
4. Ellenőrizze, hogy semmit, a modul részét képező rendelkezik-e egy függőségi hiba, amely a modul nem szerepel a.
5. Azure próbakörnyezetbe lefordítja a .NET-keretrendszer 4.7.2, használja, ha a modul használja egy újabb verziója, nem fog működni. Ebben az esetben használjon egy [hibrid Runbook-feldolgozó](../automation-hybrid-runbook-worker.md)

Ha ezek a megoldások egyike sem oldja meg a problemReview a [naplók feladat](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) miért az adott részletekért a runbook nem sikerült.

### <a name="quota-exceeded"></a>Forgatókönyv: Runbook-feladat sikertelen volt, mert túllépte a számára lefoglalt kvótát

#### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával meghiúsul:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladat-végrehajtási meghaladja az 500-as perces ingyenes kvótát a fiókjához. Ez a kvóta feladatok végrehajtása minden típusú vonatkozik. Előfordulhat, hogy ezek a feladatok egy részének tesztelését egy feladatot, a portálról, webhookok segítségével, vagy ütemezés egy feladatot, amely az Azure portal használatával, vagy hajtsa végre az adatközpontban egy feladat végrehajtása a feladat indítása folyamatban van. Az Automation szolgáltatás díjszabása kapcsolatos további információkért lásd: [díjszabásról](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Megoldás:

Ha azt szeretné, 500 percnél több feldolgozási havonta használni, módosítania az előfizetés az ingyenes szint az alapszintű csomag. Az alapszintű csomag az alábbi lépések megtételével frissíthet:  

1. Jelentkezzen be az Azure-előfizetésébe  
2. Válassza ki a frissíteni kívánt Automation-előfizetést  
3. Kattintson a **beállítások** > **díjszabás**.
4. Kattintson a **engedélyezése** lap alján, a fiók frissítését a **alapszintű** szint.

### <a name="cmdlet-not-recognized"></a>Forgatókönyv: A parancsmag nem ismerhető fel, amikor egy runbook futtatását

#### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával meghiúsul:

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Ok

Ezt a hibát az okozza, ha a PowerShell motor használata a runbookban a parancsmag nem található. Ez a hiba oka az lehet, a modul, amely tartalmazza a parancsmag nem található a fiókból, egy neve ütközik, a runbook nevét, vagy a parancsmag is létezik egy másik modul és automatizálási nem tudja feloldani a nevet.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:  

* Ellenőrizze, hogy a parancsmag neve helyes adta-e.  
* Győződjön meg arról, hogy a parancsmag az Automation-fiók létezik, és, hogy nem lesznek ütközések. Annak ellenőrzéséhez, hogy a parancsmag hogy található-e, nyissa meg a runbook szerkesztési módban, és keresse meg a keresi a tárban, vagy futtassa a parancsmagot a `Get-Command <CommandName>`. Miután ellenőrizte, hogy a parancsmag érhető el a fiókot, és, ha nincs más parancsmagok vagy a runbookok neve ütközik, adja hozzá a vászonhoz, és győződjön meg arról, hogy a runbookban egy érvényes paramétert használ.  
* Névütközés rendelkezik, és a parancsmag két különböző modulok érhető el, ha a probléma megoldható a teljes nevet, a parancsmag használatával. Használhatja például **ModuleName\CmdletName**.  
* Ha még végrehajtja a runbook a helyszíni hibrid feldolgozói csoportban, majd ellenőrizze, hogy a modul és parancsmag telepítve van a gépen, amelyen a hibrid feldolgozó.

### <a name="long-running-runbook"></a>Forgatókönyv: Egy hosszú ideig futó runbook nem lehetett végrehajtani

#### <a name="issue"></a>Probléma

Megjeleníti a runbook egy **leállítva** állapot 3 óráig futtatása után. Emellett a hibaüzenet jelenhet meg:

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Ez szándékosan van az Azure próbakörnyezetbe lefordítja a "Igazságos elosztás" figyelése az Azure Automation folyamatok miatt. Végrehajtása hosszabb három óra, ha az igazságos elosztás automatikusan leáll egy runbookot. A runbook típusa eltérő egy runbookot, amely a valós-share határidőn túli állapotát. PowerShell és Python runbookok vannak beállítva, hogy egy **leállítva** állapotát. PowerShell-munkafolyamati runbookok vannak beállítva, hogy **sikertelen**.

#### <a name="cause"></a>Ok

A runbook futott egy Azure tesztkörnyezetben igazságos elosztás által engedélyezett 3 óra túllépi.

#### <a name="resolution"></a>Megoldás:

Az egyik ajánlott megoldás, ha a runbook futtatása egy [hibrid Runbook-feldolgozó](../automation-hrw-run-runbooks.md).

Hibrid feldolgozók nem korlátozza a [igazságos elosztás](../automation-runbook-execution.md#fair-share) 3 óra runbook korlát, amely az Azure próbakörnyezetbe lefordítja a rendszer. Runbookok futtatunk hibrid Runbook-feldolgozók újraindítási viselkedés támogatása váratlan helyi infrastruktúra merül fel kell kidolgozni.

Egy másik lehetőség az, hogy optimalizálja a runbook létrehozásával [gyermek runbookok](../automation-child-runbooks.md). Ha a runbook végighalad a több erőforrás, például az adatbázis-művelet, a számos adatbázis ugyanannak a függvénynek a függvény áthelyezheti egy gyermek runbookot. Gyermek runbookok mindegyike külön folyamatokban párhuzamosan hajtja végre. Ez a viselkedés csökkenti a teljes ideje a szülő runbook befejezéséhez.

A PowerShell-parancsmagok, amelyek lehetővé teszik a gyermek runbook forgatókönyvet a következők:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – Ez a parancsmag lehetővé teszi elindít egy runbookot, és át a paramétereket a forgatókönyvhöz

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – Ha műveleteket kell elvégezni, a gyermek runbook befejezése után, ez a parancsmag lehetővé teszi a feladat állapotának minden gyermek.

### <a name="expired webhook"></a>Forgatókönyv: Állapot: 400 Hibás kérés egy webhook hívása során

#### <a name="issue"></a>Probléma

Ha megpróbál egy webhookot, az Azure Automation-runbook meghívása, a következő hibaüzenetet kapja:

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Ok

A webhookot, amely a meghívni kívánt vagy le van tiltva, vagy lejárt.

#### <a name="resolution"></a>Megoldás:

A webhook le van tiltva, ha újból engedélyezheti a webhookot, az Azure Portalon keresztül. Amikor egy webhook lejárt, a webhook kell törölni kell, majd újra létre kell hozni. Csak [újítsa meg a webhook](../automation-webhooks.md#renew-webhook) Ha még nem már lejárt.

### <a name="429"></a>Forgatókönyv: 429: A kérések aránya jelenleg túl nagy. Próbálkozzon újra

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, futtatásakor a `Get-AzureRmAutomationJobOutput` parancsmagot:

```
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordulhat elő, sok runbook-feladat kimeneti lekérésekor [részletes Streamek](../automation-runbook-output-and-messages.md#verbose-stream).

#### <a name="resolution"></a>Megoldás:

Ez a hiba megoldásához két módja van:

* A runbook szerkesztése és a feladatstreamek, amely azt bocsát ki számának csökkentése.
* Kevesebb Streamek lekérni a parancsmag futtatásakor. Kövesse ezt a viselkedést, megadhatja a `-Stream Output` paramétert a `Get-AzureRmAutomationJobOutput` csak a kimeneti steamjeihez beolvasásához. 

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.
