---
title: Munkamenet-gazdagépek méretezése Azure Automation – Azure
description: A Windows rendszerű virtuális asztali munkamenet-gazdagépek automatikus méretezése Azure Automation használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12b56519ab3fe9319f00841bd8a0cc795d99e950
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615460"
---
# <a name="scale-session-hosts-using-azure-automation"></a>A munkamenet-gazdagépek méretezése Azure Automation használatával

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager.

A virtuális gépek (VM-EK) skálázásával csökkentheti a Windows rendszerű virtuális asztali környezet teljes üzembe helyezési költségeit. Ez azt jelenti, hogy leállítja és felszabadítja a munkamenet-gazda virtuális gépeket a használaton kívüli órákban, majd visszakapcsolja őket a csúcsidőben, majd újra kiosztja őket.

Ebből a cikkből megtudhatja, hogy a Azure Automation és Azure Logic Apps rendszerbe épített skálázási eszköz hogyan fogja automatikusan méretezni a munkamenet-gazdagép virtuális gépeket a Windows rendszerű virtuális asztali környezetben. Ha szeretné megtudni, hogyan használhatja a skálázási eszközt, ugorjon előre az [Előfeltételek](#prerequisites)elemre.

## <a name="how-the-scaling-tool-works"></a>A skálázási eszköz működése

A skálázási eszköz alacsony költségű automatizálási lehetőséget biztosít azon ügyfelek számára, akik szeretnék optimalizálni a munkamenet-gazda virtuális gépek költségeit.

A skálázási eszköz használatával a következőket végezheti el:
 
- A virtuális gépeket a csúcson és a maximum munkaidőn alapuló kezdési és leállítási időszakokra ütemezhet.
- Virtuális gépek vertikális felskálázása a munkamenetek száma alapján a CPU Core-ban.
- Méretezés a virtuális gépeken a munkaidőn kívüli időszakban, így a munkamenet-gazdagépek minimális száma nem fut le.

A skálázási eszköz Azure Automation PowerShell-runbookok, webhookok és Azure Logic Apps függvény kombinációját használja. Az eszköz futtatásakor Azure Logic Apps meghívja a webhookot a Azure Automation runbook elindításához. A runbook Ezután létrehoz egy feladatot.

A maximális kihasználtság ideje alatt a feladattípus ellenőrzi a munkamenetek aktuális számát és az aktuálisan futó munkamenet-gazdagép virtuálisgép-kapacitását az egyes gazdagépek esetében. Ezt az információt használja annak kiszámításához, hogy a futó munkamenet-gazda virtuális gépek támogatni tudják-e a meglévő munkameneteket a **createazurelogicapp. ps1** fájlhoz definiált *SessionThresholdPerCPU* paraméter alapján. Ha a munkamenet-gazdagép virtuális gépei nem támogatják a meglévő munkameneteket, a feladatokban további munkamenet-gazda virtuális gépek indulnak a gazdagép-készletben.

>[!NOTE]
>A *SessionThresholdPerCPU* nem korlátozza a virtuális gépen futó munkamenetek számát. Ez a paraméter csak azt határozza meg, hogy mikor kell elindítani az új virtuális gépeket a kapcsolatok terheléselosztásához. A munkamenetek számának korlátozásához kövesse a [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) utasítást, hogy ennek megfelelően konfigurálja a *MaxSessionLimit* paramétert.

A maximális kihasználtsági idő alatt a feladattípus határozza meg, hogy a *MinimumNumberOfRDSH* paraméter alapján melyik munkamenet-gazda virtuális gépek legyenek leállítva. A feladattípus beállítja a munkamenet-gazda virtuális gépek kiürítési módját, hogy megakadályozza, hogy az új munkamenetek csatlakozzanak a gazdagépekhez. Ha a *LimitSecondsToForceLogOffUser* paramétert nem nulla értékű pozitív értékre állítja be, akkor a feladatokkal a jelenleg bejelentkezett felhasználók megmenthetik a munkájukat, megvárhatják a beállított időtartamot, majd kényszerítik a felhasználókat a kijelentkezésre. Ha a munkamenet-gazda virtuális gépen lévő összes felhasználói munkamenet ki lett jelentkezve, a rendszer leállítja a virtuális gépet.

Ha a *LimitSecondsToForceLogOffUser* paramétert nulla értékre állítja, a művelet engedélyezi a megadott csoportházirendek munkamenet-konfigurációs beállítását a felhasználói munkamenetek kijelentkezésének kezeléséhez. A csoportházirendek megjelenítéséhez nyissa meg a **számítógép-konfigurációs** > **házirendek** > **Felügyeleti sablonok** > a**Windows-összetevők** > **Terminálszolgáltatások** > **terminálkiszolgáló** > **munkamenet-korlátait**. Ha egy munkamenet-gazda virtuális gépen vannak aktív munkamenetek, a feladatokból a munkamenet-gazda virtuális gép fut. Ha nincsenek aktív munkamenetek, a rendszer leállítja a munkamenet-gazda virtuális gépet.

A feladatot rendszeresen futtatja egy beállított Ismétlődési intervallum alapján. Ezt az időközt a Windows rendszerű virtuális asztali környezet méretétől függően módosíthatja, de ne feledje, hogy a virtuális gépek indítása és leállítása hosszabb időt is igénybe vehet, ezért ne felejtse el a késleltetést figyelembe venni. Ajánlott 15 percenként beállítani az ismétlődési időközt.

Az eszköz azonban a következő korlátozásokkal is rendelkezik:

- Ez a megoldás csak a készletezett munkamenet-gazda virtuális gépekre vonatkozik.
- Ez a megoldás bármely régióban felügyeli a virtuális gépeket, de csak abban az előfizetésben használható, mint a Azure Automation fiókja és Azure Logic Apps.

>[!NOTE]
>A skálázási eszköz szabályozza a skálázási készlet terheléselosztási módját. Ez a beállítás az első terheléselosztást adja meg a csúcs és a maximum óra között.

## <a name="prerequisites"></a>Előfeltételek

A skálázási eszköz beállításának megkezdése előtt győződjön meg arról, hogy a következők állnak készen:

- Egy [Windows rendszerű virtuális asztali bérlő és egy gazdagép](create-host-pools-arm-template.md)
- A Windows rendszerű virtuális asztali szolgáltatásban konfigurált és regisztrált munkamenet-gazdagépek készletei
- Az Azure-előfizetés [közreműködői hozzáféréssel](../../role-based-access-control/role-assignments-portal.md) rendelkező felhasználója

Az eszköz telepítéséhez használt gépnek rendelkeznie kell a következővel: 

- Windows PowerShell 5,1 vagy újabb
- A Microsoft az PowerShell-modul

Ha minden készen áll, kezdjük a kezdéssel.

## <a name="create-an-azure-automation-account"></a>Azure Automation-fiók létrehozása

Először is szüksége lesz egy Azure Automation fiókra a PowerShell-runbook futtatásához. A következőképpen állíthatja be a fiókját:

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
2. A következő parancsmag futtatásával jelentkezzen be az Azure-fiókjába.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >A fióknak közreműködői jogosultságokkal kell rendelkeznie azon az Azure-előfizetésen, amelyre a skálázási eszközt telepíteni szeretné.

3. Futtassa a következő parancsmagot a Azure Automation fiók létrehozásához szükséges parancsfájl letöltéséhez:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Futtassa a következő parancsmagot a parancsfájl végrehajtásához és a Azure Automation fiók létrehozásához:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. A parancsmag kimenete tartalmaz egy webhook URI-t. Ügyeljen arra, hogy az URI rekordját tárolja, mert az Azure Logic apps végrehajtási ütemtervének beállításakor paraméterként fogja használni.

6. Miután beállította Azure Automation-fiókját, jelentkezzen be az Azure-előfizetésbe, és ellenőrizze, hogy a Azure Automation-fiókja és a kapcsolódó runbook szerepeltek-e a megadott erőforráscsoporthoz, ahogy az alábbi képen is látható:

![Az újonnan létrehozott Automation-fiókot és runbook bemutató Azure áttekintés oldal képe.](../media/automation-account.png)

  Ha szeretné megnézni, hogy a webhook hol kell legyen, válassza ki a runbook nevét. Ezután nyissa meg a runbook erőforrásai szakaszt, és válassza a **webhookok**lehetőséget.

## <a name="create-an-azure-automation-run-as-account"></a>Azure Automation futtató fiók létrehozása

Most, hogy rendelkezik egy Azure Automation fiókkal, létre kell hoznia egy Azure Automation futtató fiókot is az Azure-erőforrások eléréséhez.

Egy [Azure Automation futtató fiók](../../automation/manage-runas-account.md) hitelesítést biztosít az Azure-ban található erőforrások kezeléséhez az Azure-parancsmagokkal. Amikor létrehoz egy futtató fiókot, létrehoz egy új egyszerű szolgáltatásnevet a Azure Active Directory, és hozzárendeli a közreműködői szerepkört az egyszerű szolgáltatásnév felhasználóhoz az előfizetési szinten, az Azure-beli futtató fiók kiváló módszer a tanúsítványokkal és egyszerű szolgáltatásokkal való biztonságos hitelesítésre, anélkül, hogy a Felhasználónév és a Jelszó tárolása a hitelesítőadat-objektumban megtörténjen. További információ a futtató hitelesítésről: a [futtató fiók engedélyeinek korlátozása](../../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Minden olyan felhasználó, aki tagja az előfizetés-adminisztrátorok szerepkörnek, és az előfizetés rendszergazdája, a következő szakasz utasításait követve hozhat létre futtató fiókot.

Futtató fiók létrehozása az Azure-fiókban:

1. A Azure Portal válassza a **minden szolgáltatás**lehetőséget. Az erőforrások listájában adja meg és válassza az **Automation-fiókok**elemet.

2. Az **Automation-fiókok** lapon válassza ki az Automation-fiók nevét.

3. Az ablak bal oldalán lévő ablaktáblán válassza a **futtató fiókok** lehetőséget a Fiókbeállítások szakaszban.

4. Válassza az Azure-beli **futtató fiók**lehetőséget. Amikor megjelenik az Azure-beli **futtató fiók hozzáadása** panel, tekintse át az áttekintő információkat, majd kattintson a **Létrehozás** elemre a fiók létrehozási folyamatának elindításához.

5. Várjon néhány percet, amíg az Azure létrehozza a futtató fiókot. A létrehozási folyamat nyomon követhető a menüben az értesítések területen.

6. A folyamat befejeződése után létrejön egy Azurerunasconnection elemet nevű eszköz a megadott Automation-fiókban. A szolgáltatás tartalmazza az alkalmazás AZONOSÍTÓját, a bérlő AZONOSÍTÓját, az előfizetés AZONOSÍTÓját és a tanúsítvány ujjlenyomatát. Jegyezze fel az alkalmazás AZONOSÍTÓját, mert később használni fogja.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Szerepkör-hozzárendelés létrehozása a Windows rendszerű virtuális asztalon

Ezután létre kell hoznia egy szerepkör-hozzárendelést, hogy a Azurerunasconnection elemet kommunikálni tudjon a Windows virtuális asztallal. Ügyeljen arra, hogy a PowerShell használatával jelentkezzen be egy olyan fiókkal, amely jogosultságokkal rendelkezik a szerepkör-hozzárendelések létrehozásához.

Először töltse le és importálja a PowerShell-munkamenetben használni kívánt [Windows virtuális asztali PowerShell-modult](/powershell/windows-virtual-desktop/overview/) , ha még nem tette meg. Futtassa a következő PowerShell-parancsmagokat a Windows rendszerű virtuális asztalhoz való kapcsolódáshoz és a bérlők megjelenítéséhez.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Ha a bérlőt a méretezni kívánt gazdagép-készletekkel keresi, kövesse az [Azure Automation fiók létrehozása](#create-an-azure-automation-account) című témakör utasításait, és használja a következő parancsmag előző parancsmagjának a bérlő nevét a szerepkör-hozzárendelés létrehozásához:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Az Azure Logic app és a végrehajtási ütemterv létrehozása

Végezetül létre kell hoznia az Azure logikai alkalmazást, és be kell állítania egy végrehajtási ütemtervet az új méretezési eszközhöz.

1.  Nyissa meg a Windows PowerShellt rendszergazdaként

2.  A következő parancsmag futtatásával jelentkezzen be az Azure-fiókjába.

     ```powershell
     Login-AzAccount
     ```

3. Futtassa a következő parancsmagot a createazurelogicapp. ps1 parancsfájl helyi számítógépen való letöltéséhez.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. A következő parancsmag futtatásával jelentkezzen be a Windows rendszerű virtuális asztalra egy olyan fiókkal, amely az RDS-tulajdonos vagy az RDS közreműködői engedélyekkel rendelkezik.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Futtassa az alábbi PowerShell-szkriptet az Azure Logic app és a végrehajtási ütemterv létrehozásához.

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

     A parancsfájl futtatása után a logikai alkalmazásnak egy erőforráscsoporthoz kell megjelennie, ahogy az alábbi képen is látható.

     ![Egy példa az Azure logikai alkalmazás áttekintés lapjára.](../media/logic-app.png)

Ha módosítani szeretné a végrehajtás ütemezését, például az ismétlődési intervallumot vagy az időzónát, lépjen az autoskálázás ütemező elemre, és válassza a **Szerkesztés** lehetőséget a Logic apps Designer megkereséséhez.

![A Logic Apps Designer képe. A felhasználó ismétlődési idejének módosítására és a webhook fájljának megnyitására szolgáló ismétlődési és webhook-menük.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Méretezési eszköz kezelése

Most, hogy létrehoztuk a skálázási eszközt, elérheti a kimenetét. Ez a szakasz néhány olyan funkciót ismertet, amelyek hasznosak lehetnek.

### <a name="view-job-status"></a>Feladat állapotának megtekintése

Megtekintheti az összes runbook-feladat összegzett állapotát, vagy megtekintheti egy adott runbook-feladat alaposabb állapotát a Azure Portal.

A kiválasztott Automation-fiók jobb oldalán, a "feladat statisztikái" alatt megtekintheti az összes runbook-feladat összefoglalóit tartalmazó listát. Az ablak bal oldalán a **feladatok** lap megnyitása megjeleníti a feladat aktuális állapotát, a kezdési időpontokat és a befejezési időpontokat.

![A feladatok állapota lap képernyőképe.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Naplók és méretezési eszköz kimenetének megtekintése

A kibővíthető és a skálázási műveletek naplóit megtekintheti a runbook megnyitásával és a feladatokhoz tartozó nevek kiválasztásával.

Navigáljon a runbook (az alapértelmezett név WVDAutoScaleRunbook) az Azure Automation fiókot futtató erőforráscsoporthoz, és válassza az **Áttekintés**lehetőséget. Az Áttekintés lapon válasszon ki egy feladatot a legutóbbi feladatok területen a méretezési eszköz kimenetének megtekintéséhez, ahogy az alábbi képen is látható.

![A skálázási eszköz kimeneti ablakának képe.](../media/tool-output.png)

## <a name="report-issues"></a>Problémák bejelentése

Ha problémák merülnek fel a skálázási eszközzel kapcsolatban, az [RDS GitHub oldalán](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps)is bejelentheti azokat.
