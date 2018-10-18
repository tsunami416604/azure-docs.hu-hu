---
title: Az Azure Automation-Runbookokkal kapcsolatos hibák elhárítása
description: Ismerje meg, az Azure Automation-runbookokkal kapcsolatos problémák elhárítása
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/17/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b8c6b82af1a71f5e2df7dd555c7ceb91b8ccd292
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394546"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbookokkal kapcsolatos hibák elhárítása

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Az Azure Automation-runbookok használatakor hitelesítési hibák

### <a name="sign-in-failed"></a>. Forgatókönyv: Azure-fiókot nem sikerült bejelentkezni

#### <a name="issue"></a>Probléma

A következő hibaüzenetet kapja, amikor dolgozik a `Add-AzureAccount` vagy `Connect-AzureRmAccount` parancsmagok.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha az eszköz hitelesítő adat neve nem érvényes, vagy ha a felhasználónevet és jelszót, amellyel az Automation-hitelesítőeszközt beállítása nem érvényes.

#### <a name="resolution"></a>Megoldás:

Annak megállapításához, hogy mi okozza, tegye a következőket:  

1. Győződjön meg arról, hogy nincs-e különleges karaktereket, beleértve a **@** Automation nevű hitelesítő adat eszköz csatlakozhat az Azure-ban használt karakter.  
2. Ellenőrizze, hogy használhatja-e a felhasználónevet és jelszót, amelyet az Azure Automation hitelesítő adat a helyi PowerShell ISE-szerkesztőben van tárolva. Teheti meg, ellenőrizze a felhasználónév és jelszó helyességét a PowerShell ISE-ben a következő parancsmag futtatásával:  

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

Ez a hiba akkor fordul elő, ha az előfizetés neve nem érvényes, vagy ha az Azure Active Directory felhasználó megpróbálja beolvasni az előfizetés részleteinek nincs beállítva az előfizetés-rendszergazdaként.

#### <a name="resolution"></a>Megoldás:

Ha már megfelelő hitelesítés az Azure-ba, és rendelkezik hozzáféréssel az előfizetéshez, válassza ki szeretne meghatározni, tegye a következőket:  

1. Győződjön meg arról, hogy futtassa a **Add-AzureAccount** parancsmag futtatása előtt a **Select-AzureSubscription** parancsmagot.  
2. Ha továbbra is látja ezt a hibaüzenetet, módosítsa a kódot adja hozzá a **- AzureRmContext** paraméter következő a **Add-AzureAccount** parancsmag majd futtassa a kódot.

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
   ```

### <a name="auth-failed-mfa"></a>Forgatókönyv: Hitelesítés nem sikerült, mert engedélyezve van a multi-factor authentication szolgáltatás Azure-bA

#### <a name="issue"></a>Probléma

Az Azure-bA az Azure felhasználónevével és jelszavával való hitelesítéskor a következő hibaüzenetet kapja:

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Ok

A multi-factor authentication szolgáltatás az Azure-fiókkal rendelkezik, ha egy Azure Active Directory-felhasználó nem használható az Azure-beli hitelesítésre. Ehelyett kell egy tanúsítványt, vagy egy egyszerű szolgáltatás használatával Azure-beli hitelesítésre.

#### <a name="resolution"></a>Megoldás:

A klasszikus Azure üzemi modell parancsmagokban tanúsítványt használ, tekintse meg [létrehozása és kezelése az Azure-szolgáltatások egy tanúsítvány hozzáadása.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Egy egyszerű szolgáltatást az Azure Resource Manager parancsmagjainak használatához tekintse meg [az egyszerű szolgáltatásnév létrehozása az Azure portal használatával](../../azure-resource-manager/resource-group-create-service-principal-portal.md) és [hitelesítése egy egyszerű szolgáltatást az Azure Resource Managerrel.](../../azure-resource-manager/resource-group-authenticate-service-principal.md)

## <a name="common-errors-when-working-with-runbooks"></a>A forgatókönyvek használata során előforduló gyakori hibák

### <a name="task-was-cancelled"></a>Forgatókönyv: A runbook futtatása meghiúsul a következő hibával: A feladat megszakítása

#### <a name="issue"></a>Probléma

A runbook egy a következőhöz hasonló hibaüzenettel meghiúsul:

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Ok

Ezt a hibát okozhatja az elavult Azure-modulokat.

#### <a name="resolution"></a>Megoldás:

Ez a hiba megoldhatók az Azure-modulok frissítése a legújabb verzióra.

Az Automation-fiókban kattintson **modulok**, és kattintson a **frissítés az Azure-modulok**. A frissítés körülbelül 15 percet vesz igénybe, befejezése után futtassa újból a runbookot, amely sikertelen volt. A modulok frissítésével kapcsolatos további tudnivalókért lásd: [frissítés az Azure-modulokat az Azure Automationben](../automation-update-azure-modules.md).

### <a name="child-runbook-auth-failure"></a>Forgatókönyv: Gyermek runbook nem több előfizetés esetén

#### <a name="issue"></a>Probléma

Gyermek runbookok végrehajtásakor `Start-AzureRmRunbook`, a gyermek runbook nem Azure-erőforrások kezeléséhez.

#### <a name="cause"></a>Ok

A gyermekrunbook nem használja a megfelelő környezet futtatásakor.

#### <a name="resolution"></a>Megoldás:

Ha több előfizetés használata az előfizetési környezet elveszhetnek gyermek runbookok meghívásakor. Győződjön meg arról, hogy a runbookok átadott az előfizetési környezetet, adja hozzá a `AzureRmContext` a parancsmagot, és azt a környezetet pass paramétert.

```azurepowershell-interactive
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

### <a name="not-recognized-as-cmdlet"></a>Forgatókönyv: A runbook nem hiányzik a parancsmag miatt

#### <a name="issue"></a>Probléma

A runbook egy a következőhöz hasonló hibaüzenettel meghiúsul:

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Ok

Ez a hiba oka lehet a következő okok miatt:

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

Ez a hiba oka lehet a következő okok miatt:

1. Memória felső korlátja. A védőfal számára lefoglalt memória mennyiségét a dokumentált korlátozva van [Automation Szolgáltatáskorlátok](../../azure-subscription-service-limits.md#automation-limits) , egy feladat sikertelen lehet, ha több mint 400 MB memóriát használ.

1. Hálózati szoftvercsatorna. Azure próbakörnyezetbe lefordítja a rendszer legfeljebb 1000 egyidejű hálózati szoftvercsatorna ismertetett módon [Automation Szolgáltatáskorlátok](../../azure-subscription-service-limits.md#automation-limits).

1. A modul inkompatibilis. Ez a hiba akkor fordulhat elő, ha a modul függőségek nem megfelelőek, és ha nem, a runbook általában adja vissza egy "parancs nem található" vagy "Nem köthető a paraméter" üzenet.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:

* A memória felső korlátja belül működik javasolt módszerek a következők felosztása több runbook között, nem processzormagonkénti memória, nem a szükségtelen kimeneti írási a runbookok az adatok feldolgozása vagy fontolja meg, hány ellenőrzőpontot ír be a PowerShell-munkafolyamat runbookok. Használhatja például a Törlés metódust `$myVar.clear()` tisztítsa meg a változót és a `[GC]::Collect()` szemétgyűjtés futtatása azonnal, ez csökkenti a runbook memóriaigénye futásidőben.

* A lépéseket követve az Azure-modulok frissítése [frissítése az Azure PowerShell-modulok az Azure Automationben](../automation-update-azure-modules.md).  

* Egy másik megoldás, ha a runbook futtatása egy [hibrid Runbook-feldolgozó](../automation-hrw-run-runbooks.md). Hibrid feldolgozók nem korlátozza, amelyek az Azure próbakörnyezetbe lefordítja a memória és a hálózati korlátok.

### <a name="fails-deserialized-object"></a>Forgatókönyv: A Runbook nem deszerializált objektum miatt

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

### <a name="quota-exceeded"></a>Forgatókönyv: Forgatókönyv-feladat sikertelen volt, mert túllépte a számára lefoglalt kvótát

#### <a name="issue"></a>Probléma

A runbook-feladat a következő hibával meghiúsul:

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Ok

Ez a hiba akkor fordul elő, ha a feladat-végrehajtási meghaladja az 500-as perces ingyenes kvótát a fiókjához. Ez a kvóta vonatkozik minden típusú feladatok végrehajtása például egy feladat tesztelése, egy feladat indítása a portálról, webhookok használatával, és a egy feladatot, amely az Azure portal használatával, vagy ütemezés szerint a feladat végrehajtása a helyi adatközpontban. Az Automation szolgáltatás díjszabása kapcsolatos további információkért lásd: [díjszabásról](https://azure.microsoft.com/pricing/details/automation/).

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

Ezt a hibát az okozza, ha a PowerShell motor használata a runbookban a parancsmag nem található. Ez lehet, mert a modul, amely tartalmazza a parancsmag nem található a fiókból, egy neve ütközik, a runbook nevét, vagy a parancsmag is létezik egy másik modul és automatizálási nem tudja feloldani a nevet.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:  

* Ellenőrizze, hogy a parancsmag neve helyes adta-e.  
* Győződjön meg arról, hogy a parancsmag az Automation-fiók létezik, és, hogy nem lesznek ütközések. Annak ellenőrzéséhez, hogy a parancsmag hogy található-e, nyissa meg a runbook szerkesztési módban, és keresse meg a keresi a tárban, vagy futtassa a parancsmagot a `Get-Command <CommandName>`. Miután ellenőrizte, hogy a parancsmag érhető el a fiókot, és, ha nincs más parancsmagok vagy a runbookok neve ütközik, adja hozzá a vászonhoz, és győződjön meg arról, hogy a runbookban egy érvényes paramétert használ.  
* Ha a parancsmag érhető el két különböző modulok névütközés rendelkezik, akkor ezt megoldhatja a teljes nevet, a parancsmag használatával. Használhatja például **ModuleName\CmdletName**.  
* Ha még végrehajtja a runbook a helyszíni hibrid feldolgozói csoportban, majd ellenőrizze, hogy a modul és parancsmag telepítve van a gépen, amelyen a hibrid feldolgozó.

### <a name="long-running-runbook"></a>Forgatókönyv: Egy hosszú ideig futó runbook nem lehetett végrehajtani

#### <a name="issue"></a>Probléma

Megjeleníti a runbook egy **leállítva** állapot 3 óráig futtatása után. Emellett a hibaüzenet jelenhet meg:

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Ez szándékosan van az Azure próbakörnyezetbe lefordítja a folyamatok Azure Automationön belül, amely egy runbook automatikusan leáll, ha már három órán keresztül hajtja végre a "Igazságos elosztás" figyelési miatt. A runbook típusa eltérő egy runbookot, amely a valós-share határidőn túli állapotát. PowerShell és Python runbookok vannak beállítva, hogy egy **leállítva** állapotát. PowerShell-munkafolyamati runbookok vannak beállítva, hogy **sikertelen**.

#### <a name="cause"></a>Ok

A runbook futott egy Azure tesztkörnyezetben igazságos elosztás által engedélyezett 3 óra túllépi.

#### <a name="resolution"></a>Megoldás:

Az egyik ajánlott megoldás, ha a runbook futtatása egy [hibrid Runbook-feldolgozó](../automation-hrw-run-runbooks.md).

Hibrid feldolgozók nem korlátozza a [igazságos elosztás](../automation-runbook-execution.md#fair-share) 3 óra runbook korlát, amely az Azure próbakörnyezetbe lefordítja a rendszer. Hibrid Runbook-feldolgozók nem korlátozza a 3 óra igazságos elosztás korlátot, míg a runbookok futtatunk továbbra is hibrid Runbook-feldolgozók kell kidolgozni az újraindítási viselkedés váratlan helyi infrastruktúra felmerülő problémák támogatásához.

Egy másik lehetőség az, hogy optimalizálja a runbook létrehozásával [gyermek runbookok](../automation-child-runbooks.md). Ha a runbook végighalad az erőforrások, például a több adatbázis, adatbázis-művelet számos ugyanannak a függvénynek a függvény áthelyezheti egy gyermek runbookot. Ezen gyermek runbookok mindegyike különálló folyamatban, egymással párhuzamosan fut, így csökkentik a szülő runbook futtatásának befejezéséhez szükséges teljes időt.

A PowerShell-parancsmagok, amelyek lehetővé teszik a gyermek runbook forgatókönyvet a következők:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) – Ez a parancsmag lehetővé teszi elindít egy runbookot, és át a paramétereket a forgatókönyvhöz

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) – Ez a parancsmag lehetővé teszi, hogy a feladat állapotának ellenőrzése minden gyermek, ha a művelet, amely a gyermekrunbook befejezése után kell elvégezni.

## <a name="common-errors-when-importing-modules"></a>A modulok importálása során előforduló gyakori hibák

### <a name="module-fails-to-import"></a>Forgatókönyv: A modul importálása sikertelen, vagy parancsmagok nem hajtható végre, az importálás után

#### <a name="issue"></a>Probléma

Modul importálása sikertelen lesz, vagy sikeresen importálja, de nincs parancsmagok ki kell olvasni.

#### <a name="cause"></a>Ok

Néhány általános oka, hogy egy modul előfordulhat, hogy nem sikerült importálni az Azure Automationhöz a következők:

* A struktúra nem felel meg az Automation kell a struktúra.
* A modul szolgáltatás, amely még nem lett telepítve az Automation-fiók egy másik modul függ.
* A modul hiányzó függőségeit a mappában.
* A `New-AzureRmAutomationModule` parancsmag a modult, és have't, megadva a teljes elérési útja feltöltéséhez használja, vagy a modul még nincs betöltve egy nyilvánosan elérhető URL-cím használatával.

#### <a name="resolution"></a>Megoldás:

A probléma elhárításához a következő megoldások valamelyikét:

* Győződjön meg arról, hogy a modul követi a következő formátumban: ModuleName.Zip **->** ModuleName vagy a verziószám **->** (ModuleName.psm1, ModuleName.psd1)
* Nyissa meg a .psd1 fájlban, és tekintse meg, ha a modul rendelkezik-e függőségek. Ha igen, töltse fel ezeket a modulokat az Automation-fiókot.
* Győződjön meg arról, hogy minden hivatkozott .dll modul mappában találhatók.

## <a name="next-steps"></a>További lépések

Ha nem jelenik meg a problémát, vagy nem lehet megoldani a problémát, látogasson el a következő csatornák további támogatás:

* Az [Azure fórumain](https://azure.microsoft.com/support/forums/) Azure-szakértőktől kaphat válaszokat.
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, akkor is fájl egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatja az első**.