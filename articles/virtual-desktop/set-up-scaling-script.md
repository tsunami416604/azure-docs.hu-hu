---
title: A munkamenetek méretezése üzemelteti az Azure Automationt - Azure
description: A Windows virtuális asztali munkamenet-gazdagépeinek automatikus méretezése az Azure Automation segítségével.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3a853dc32f8716f3f2ba32896a7a4a239efcc5bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349869"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Munkamenet-állomások méretezése az Azure Automation használatával

A virtuális gépek (VM-ek) méretezésével csökkentheti a Windows virtuális asztal teljes központi telepítési költségét. Ez azt jelenti, leállítása és deallocating munkamenet gazdagép virtuális gépek csúcsidőn kívüli használati órákban, majd kapcsolja be őket, és átcsoportosítása csúcsidőben.

Ebben a cikkben megismerheti az Azure Automation és az Azure Logic Apps használatával készült skálázási eszközt, amely automatikusan skálázhatja a munkamenetgazda virtuális gépeket a Windows virtuális asztali környezetben. A méretezési eszköz használatának megismeréséhez ugorjon az [Előfeltételek](#prerequisites)elemre.

## <a name="how-the-scaling-tool-works"></a>A méretezési eszköz működése

A méretezési eszköz alacsony költségű automatizálási lehetőséget biztosít azoknak az ügyfeleknek, akik optimalizálni szeretnék a munkamenetgazda virtuális gépének költségeit.

A méretezési eszközzel a következőkre tehető:
 
- Ütemezze a virtuális gépekindítását és leállítását a csúcs- és csúcsidőn kívüli munkaidő alapján.
- A virtuális gépeket a processzormagonkénti munkamenetek száma alapján kell kibővíteni.
- A virtuális gépeken csúcsidőn kívül skálázható, így a munkamenetgazda virtuális gépeinek minimális száma fut.

A skálázási eszköz az Azure Automation PowerShell runbookok, webhookok és Az Azure Logic Apps a működéshez. Amikor az eszköz fut, az Azure Logic Apps meghívja a webhook az Azure Automation runbook elindításához. A runbook ezután létrehoz egy feladatot.

A maximális használati idő alatt a feladat ellenőrzi a munkamenetek aktuális számát és az aktuális futó munkamenet-gazdagép virtuálisgép-kapacitását az egyes gazdakészletekhez. Ezeket az információkat használja annak kiszámításához, hogy a futó munkamenetgazda virtuális gépek támogathatják-e a meglévő munkameneteket a **createazurelogicapp.ps1** fájlhoz definiált *SessionThresholdPerCPU* paraméter alapján. Ha a munkamenet gazdagépei nem támogatják a meglévő munkameneteket, a feladat további munkamenetgazda virtuális gépeket indít el a gazdakészletben.

>[!NOTE]
>*A SessionThresholdPerCPU* nem korlátozza a virtuális gép munkameneteinek számát. Ez a paraméter csak azt határozza meg, hogy mikor kell új virtuális gépeket elindítani a kapcsolatok terheléselosztása. A munkamenetek számának korlátozásához kövesse a [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) utasításokat a *MaxSessionLimit* paraméter megfelelő konfigurálásához.

A csúcsidőn kívüli használati idő alatt a feladat meghatározza, hogy mely munkamenetgazda-virtuális gépeket kell leállítani a *MinimumNumberOfRDSH* paraméter alapján. A feladat a munkamenet gazdagépei virtuális gépeit kiürítési módba állítja, hogy megakadályozza az új munkamenetek csatlakoztatását az állomásokhoz. Ha a *LimitSecondsToForceLogOffUser* paramétert nem nulla pozitív értékre állítja be, a feladat értesíti a jelenleg bejelentkezett felhasználókat, hogy mentse a munkájukat, várjon a beállított időmennyiségre, majd kényszerítse a felhasználókat a kijelentkezésre. Miután a munkamenet gazdagép virtuális gépének összes felhasználói munkamenete ki lett jelentkezve, a feladat leállítja a virtuális gép leállítását.

Ha a *LimitSecondsToForceLogOffUser* paramétert nullára állítja, a feladat lehetővé teszi, hogy a megadott csoportházirendek munkamenet-konfigurációs beállítása kezelje a felhasználói munkamenetek kijelentkezését. A csoportházirendek megtekintéséhez olvassa el a **Számítógép-konfigurációs** > **házirendek** > **Administrative Templates** > felügyeleti sablonok**A Windows-összetevők** > **terminálszolgáltatások** > **terminálkiszolgálójának** > **munkamenet-időkorlátait.** Ha vannak aktív munkamenetek egy munkamenet gazdagép virtuális gép, a feladat elhagyja a munkamenet gazdagép virtuális gép fut. Ha nincsenek aktív munkamenetek, a feladat leállítja a munkamenet gazdagép virtuális gép.

A feladat rendszeres időközönként fut egy beállított ismétlődési időköz alapján. Ezt az időközt a Windows virtuális asztal környezetének mérete alapján módosíthatja, de ne feledje, hogy a virtuális gépek indítása és leállítása eltarthat egy ideig, ezért ne felejtse el figyelembe venni a késleltetést. Azt javasoljuk, hogy az ismétlődési időközt 15 percenként határozza meg.

Az eszköznek azonban a következő korlátai is vannak:

- Ez a megoldás csak a készletezett munkamenetgazda virtuális gépekre vonatkozik.
- Ez a megoldás kezeli a virtuális gépek et bármely régióban, de csak ugyanabban az előfizetésben használható, mint az Azure Automation-fiók és az Azure Logic Apps.

>[!NOTE]
>A skálázási eszköz szabályozza a méretezés alatt álló gazdakészlet terheléselosztási módját. Ez határozza meg, hogy szélesség-első terheléselosztás mind a csúcs-és csúcsidőn kívül.

## <a name="prerequisites"></a>Előfeltételek

A méretezési eszköz beállítása előtt győződjön meg arról, hogy a következő dolgok készen állnak:

- [Windows virtuális asztal bérlője és gazdagépkészlete](create-host-pools-arm-template.md)
- A Windows virtuális asztali szolgáltatással konfigurált és regisztrált munkamenetgazda-készlet virtuális gépei
- [Közreműködői hozzáféréssel](../role-based-access-control/role-assignments-portal.md) rendelkező felhasználó az Azure-előfizetésben

Az eszköz üzembe helyezéséhez használt gépnek a következőkkel kell rendelkeznie: 

- Windows PowerShell 5.1-es vagy újabb verzió
- A Microsoft Az PowerShell modul

Ha minden készen áll, akkor kezdjük.

## <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

Először is szüksége lesz egy Azure Automation-fiók a PowerShell runbook futtatásához. A következőképpen állíthatja be fiókját:

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
2. Futtassa a következő parancsmast az Azure-fiókba való bejelentkezéshez.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >A fióknak közreműködői jogokkal kell rendelkeznie az Azure-előfizetésben, amelyen telepíteni szeretné a méretezési eszközt.

3. Futtassa a következő parancsmast az Azure Automation-fiók létrehozásához a parancsfájl letöltéséhez:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Futtassa a következő parancsmast a parancsfájl végrehajtásához és hozza létre az Azure Automation-fiókot:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. A parancsmag kimenete tartalmazni fogja a webhook URI.The cmdlet's output will include a webhook URI. Győződjön meg arról, hogy az URI-rekord, mert akkor használja paraméterként, amikor beállítja az Azure Logic-alkalmazások végrehajtási ütemezését.

6. Miután beállította az Azure Automation-fiókot, jelentkezzen be az Azure-előfizetésbe, és ellenőrizze, hogy az Azure Automation-fiók és a megfelelő runbook megjelent a megadott erőforráscsoportban, ahogy az az alábbi képen látható:

   ![Az Azure áttekintő lapjának képe, amely az újonnan létrehozott automatizálási fiókot és runbookot mutatja be.](media/automation-account.png)

  Annak ellenőrzéséhez, hogy a webhook, ahol lennie kell, válassza ki a runbook nevét. Ezután nyissa meg a runbook Erőforrások szakaszát, és válassza a **Webhooks**lehetőséget.

## <a name="create-an-azure-automation-run-as-account"></a>Azure Automation-futtatás létrehozása fiókként

Most, hogy rendelkezik egy Azure Automation-fiókkal, létre kell hoznia egy Azure Automation Run As fiókot az Azure-erőforrások eléréséhez.

Az [Azure Automation Run As fiók](../automation/manage-runas-account.md) hitelesítést biztosít az Azure-beli erőforrások azure-parancsmagokkal való kezeléséhez. Amikor létrehoz egy Futtatás másként fiókot, az létrehoz egy új, egyszerű szolgáltatású felhasználót az Azure Active Directoryban, és az előfizetés szintjén hozzárendeli a közreműködői szerepkört az egyszerű szolgáltatásfelhasználóhoz, az Azure Run As Account nagyszerű módja a biztonságos hitelesítésnek tanúsítványok és egyszerű szolgáltatásnév anélkül, hogy egy hitelesítő adatobjektumban kellene tárolnia a felhasználónevet és a jelszót. A Futtatás hitelesítésként című témakörben olvashat bővebben: [Futtatás mint fiók engedélyek.](../automation/manage-runas-account.md#limiting-run-as-account-permissions)

Bármely felhasználó, aki tagja az Előfizetés-rendszergazdák szerepkörnek és az előfizetés társadminisztrátorának, létrehozhat egy Futtatás másként fiókot a következő szakasz utasításait követve.

Futtatási fiók létrehozása az Azure-fiókjában:

1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. Az erőforrások listájában adja meg és válassza az **Automatizálási fiókok lehetőséget.**

2. Az **Automation-fiókok** lapon válassza ki az Automation-fiók nevét.

3. Az ablak bal oldalán lévő ablaktáblán válassza a **Futtatás fiókként** lehetőséget a Fiókbeállítások szakaszban.

4. Válassza az **Azure Futtatás fiókként lehetőséget.** Amikor megjelenik az **Azure-futtatás fiókként** jelölőnégyzet, tekintse át az áttekintő információkat, majd válassza a **Létrehozás** lehetőséget a fiók létrehozásához.

5. Várjon néhány percet, amíg az Azure létrehozza a Futtatás másként fiókot. A létrehozás előrehaladását az Értesítések menümenüben követheti nyomon.

6. Amikor a folyamat befejeződik, létrehoz egy AzureRunAsConnection nevű eszközt a megadott Automation-fiókban. A kapcsolati eszköz rendelkezik az alkalmazásazonosítóval, a bérlőazonosítóval, az előfizetés-azonosítóval és a tanúsítvány ujjlenyomatával. Ne feledje az alkalmazásazonosítót, mert később használni fogja.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Szerepkör-hozzárendelés létrehozása a Windows virtuális asztalon

Ezután létre kell hoznia egy szerepkör-hozzárendelést, hogy az AzureRunAsConnection kommunikálhassa a Windows virtuális asztalt. Győződjön meg arról, hogy a PowerShell használatával jelentkezzen be egy olyan fiókkal, amely rendelkezik a szerepkör-hozzárendelések létrehozásához szükséges jogosultságokkal.

Először töltse le és importálja a [Windows Virtual Desktop PowerShell modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg. Futtassa a következő PowerShell-parancsmagokat a Windows virtuális asztalhoz való csatlakozáshoz és a bérlők megjelenítéséhez.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Ha megtalálja a bérlőt a méretezéshez használni kívánt gazdakészletekkel, kövesse az [Azure Automation-fiók létrehozása című](#create-an-azure-automation-account) szakasz utasításait, és használja a következő parancsmagból kapott bérlői nevet a szerepkör-hozzárendelés létrehozásához:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Az Azure Logic alkalmazás és a végrehajtási ütemezés létrehozása

Végül létre kell hoznia az Azure Logic alkalmazást, és be kell állítania egy végrehajtási ütemezést az új skálázási eszközhöz.

1.  A Windows PowerShell megnyitása rendszergazdaként

2.  Futtassa a következő parancsmast az Azure-fiókba való bejelentkezéshez.

     ```powershell
     Login-AzAccount
     ```

3. Futtassa a következő parancsmast a createazurelogicapp.ps1 parancsfájl helyi gépen való letöltéséhez.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Futtassa a következő parancsmast a Windows virtuális asztalra való bejelentkezéshez egy RDS-tulajdonosi vagy RDS-közreműködői engedélyekkel rendelkező fiókkal.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Futtassa a következő PowerShell-parancsfájlt az Azure Logic alkalmazás és a végrehajtási ütemezés létrehozásához.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you'd like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     A parancsfájl futtatása után a logikai alkalmazás nak meg kell jelennie egy erőforráscsoportban, ahogy az az alábbi képen látható.

     ![Egy példa Azure Logic App áttekintő lap képe.](media/logic-app.png)

A végrehajtási ütemezés módosításához, például az ismétlődési időköz vagy az időzóna módosításához lépjen az automatikus skálázásütemező elemre, és válassza a **Szerkesztés** lehetőséget a Logic Apps Designer megugrásához.

![A Logic Apps Designer képe. Az Ismétlődés és a Webhook menük, amelyek lehetővé teszik a felhasználó számára az ismétlődési idők és a webhook-fájl szerkesztését.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>A méretezési eszköz kezelése

Most, hogy létrehozta a skálázási eszközt, hozzáférhet a kimenethez. Ez a szakasz néhány hasznos funkcióval ismertet.

### <a name="view-job-status"></a>Feladat állapotának megtekintése

Megtekintheti az összes runbook-feladat összesített állapotát, vagy megtekintheti egy adott runbook-feladat részletesebb állapotát az Azure Portalon.

A kiválasztott Automation-fiók jobb oldalán, a "Feladatstatisztika" területen megtekintheti az összes runbook-feladat összegzésének listáját. Az ablak bal oldalán lévő **Feladatok** lap megnyitása az aktuális feladatállapotokat, kezdési időpontokat és befejezési időket mutatja.

![Képernyőkép a feladat állapotoldaláról.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Naplók és méretezési eszköz kimenetének megtekintése

Megtekintheti a kibővített és a horizontális felskálázási műveletek naplóit a runbook megnyitásával és a feladat nevének kiválasztásával.

Keresse meg a runbookot (az alapértelmezett neve WVDAutoScaleRunbook) az Azure Automation-fiókot üzemeltető erőforráscsoportban, és válassza az **Áttekintés**lehetőséget. Az áttekintő lapon válasszon ki egy feladatot a Legutóbbi feladatok csoportban a méretezési eszköz kimenetének megtekintéséhez, ahogy az az alábbi képen látható.

![A méretezési eszköz kimeneti ablakának képe.](media/tool-output.png)

## <a name="report-issues"></a>Problémák bejelentése

Ha bármilyen problémát tapasztal a méretezési eszközzel, jelentheti őket az [RDS GitHub-lapon.](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)
