---
title: Kiszolgálók célállapotba történő konfigurálása és eltéréskezelés az Azure Automation használatával
description: Oktatóanyag – kiszolgálói konfigurációk kezelése Azure Automation állapot-konfigurációval
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 72e5018dc1212e57dc190c05cc54158d37ca7fe1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231499"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Kiszolgálók konfigurálása a kívánt állapotra és a drift kezelése

Azure Automation állapot-konfiguráció lehetővé teszi a kiszolgálók konfigurációjának megadását, és biztosítja, hogy ezek a kiszolgálók a megadott állapotban legyenek az idő múlásával.

> [!div class="checklist"]
> - Azure Automation DSC által felügyelni kívánt virtuális gép előkészítése
> - Konfiguráció feltöltése Azure Automationre
> - Konfiguráció fordítása csomópont-konfigurációban
> - Csomópont-konfiguráció társítása felügyelt csomóponthoz
> - Felügyelt csomópont megfelelőségi állapotának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
- Egy Azure Resource Manager Windows Server 2008 R2 vagy újabb rendszert futtató virtuális gép (klasszikus). A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.
- Azure PowerShell modul 3,6-es vagy újabb verziója. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.
- A kívánt állapot-konfiguráció (DSC) ismerete. A DSC-vel kapcsolatos információkért lásd: a [Windows PowerShell kívánt állapotának konfigurálása – áttekintés](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Konfiguráció létrehozása és feltöltése Azure Automation

Ebben az oktatóanyagban egy egyszerű DSC-konfigurációt fogunk használni, amely biztosítja, hogy az IIS telepítve legyen a virtuális gépen.

A DSC-konfigurációkról információért lásd: [DCS-konfigurációk](/powershell/scripting/dsc/configurations/configurations).

Egy szövegszerkesztőben írja be a következőt, és mentse helyileg `TestConfig.ps1` névvel.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> A fejlettebb forgatókönyvek esetében, ahol több, a DSC-erőforrásokat biztosító modult kell importálnia, győződjön meg arról, hogy minden modul rendelkezik egyedi `Import-DscResource`-sorral a konfigurációban.

Az `Import-AzureRmAutomationDscConfiguration` parancsmag meghívásával töltse fel a konfigurációt az Automation-fiókjába:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Konfiguráció fordítása csomópont-konfigurációban

A DSC-konfigurációt egy csomópont-konfigurációba kell lefordítani, mielőtt hozzá lehetne rendelni egy csomóponthoz.

A konfigurációk fordításával kapcsolatos információkért lásd: [DSC-konfigurációk](/powershell/scripting/dsc/configurations/configurations).

Hívja meg az `Start-AzureRmAutomationDscCompilationJob` parancsmagot a `TestConfig` konfiguráció egy csomópont-konfigurációba való fordításához:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Ezzel létrehoz egy `TestConfig.WebServer` nevű csomópont-konfigurációt az Automation-fiókjában.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>A virtuális gép regisztrálása az állapot-konfigurációval felügyelhető

Azure Automation állapot-konfigurációval felügyelheti az Azure-beli virtuális gépeket (a klasszikus és a Resource Managert is), a helyszíni virtuális gépeket, a linuxos gépeket, az AWS-alapú virtuális gépeket és a helyszíni fizikai gépeket. Ebben a témakörben bemutatjuk, hogyan regisztrálhat csak Azure Resource Manager virtuális gépeket. További információ az egyéb típusú gépek regisztrálásáról: [bevezetési gépek felügyeletre Azure Automation állapot-konfiguráció alapján](automation-dsc-onboarding.md).

Hívja meg a `Register-AzureRmAutomationDscNode` parancsmagot a virtuális gép Azure Automation állapot-konfigurációval való regisztrálásához.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Ezzel a beállítással a megadott virtuális gépet felügyelt csomópontként regisztrálja az állapot-konfigurációban.

### <a name="specify-configuration-mode-settings"></a>Konfigurációs mód beállításainak megadása

Ha felügyelt csomópontként regisztrálja a virtuális gépet, megadhatja a konfiguráció tulajdonságait is. Megadhatja például, hogy a gép állapota csak egyszer legyen alkalmazva (a DSC a kezdeti ellenőrzés után nem kísérli meg a konfiguráció alkalmazását) a **ConfigurationMode** tulajdonság értékeként `ApplyOnly` megadásával:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Azt is megadhatja, hogy a DSC milyen gyakran ellenőrizze a konfigurációs állapotot a **ConfigurationModeFrequencyMins** tulajdonság használatával:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

A felügyelt csomópontok konfigurációs tulajdonságainak beállításával kapcsolatos további információkért lásd: [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

A DSC konfigurációs beállításaival kapcsolatos további információkért lásd: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Csomópont-konfiguráció társítása felügyelt csomóponthoz

Most hozzárendelheti a lefordított csomópont-konfigurációt a konfigurálni kívánt virtuális géphez.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Ez hozzárendeli a `TestConfig.WebServer` nevű csomópont-konfigurációt a `DscVm`nevű regisztrált DSC-csomóponthoz.
Alapértelmezés szerint a DSC-csomópont 30 percenként be van jelölve a csomópont-konfigurációnak való megfeleléshez.
További információ a megfelelőségi ellenőrzési időköz módosításáról: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Részleges konfigurációk használata

Azure Automation állapot-konfiguráció támogatja a [részleges konfigurációk](/powershell/scripting/dsc/pull-server/partialconfigs)használatát.
Ebben az esetben a DSC-t úgy konfigurálták, hogy egymástól függetlenül kezelhesse a beállításokat, és minden egyes konfigurációt beolvas a Azure Automationból.
Az Automation-fiókok esetében azonban csak egy konfigurációt lehet hozzárendelni.
Ez azt jelenti, hogy ha két konfigurációt használ egy csomóponthoz, két Automation-fiókra lesz szüksége.

A részleges konfiguráció lekéréses szolgáltatásból való regisztrálásával kapcsolatos részletekért tekintse meg a [részleges konfigurációk](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)dokumentációját.

További információ arról, hogy a csapatok hogyan működhetnek együtt a kiszolgálók a konfiguráció kód használatával történő kezelésével: [a DSC szerepkörének ismertetése CI/CD-folyamatokban](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Felügyelt csomópont megfelelőségi állapotának ellenőrzése

A felügyelt csomópontok megfelelőségi állapotáról a jelentések a `Get-AzureRmAutomationDscNodeReport` parancsmag meghívásával szerezhetők be:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Csomópontok eltávolítása a szolgáltatásból

Ha csomópontot ad hozzá Azure Automation állapot-konfigurációhoz, a helyi Configuration Manager beállításai a szolgáltatásba való regisztrálásra, valamint a gép konfigurálásához szükséges modulok és a szükséges modulok beállítására vannak beállítva.
Ha úgy dönt, hogy eltávolítja a csomópontot a szolgáltatásból, ezt a Azure Portal vagy az az parancsmagok használatával teheti meg.

> [!NOTE]
> A csomópontok szolgáltatásból való regisztrációjának törlése csak a helyi Configuration Manager beállításokat állítja be, így a csomópont már nem csatlakozik a szolgáltatáshoz.
> Ez nem befolyásolja a csomóponton jelenleg alkalmazott konfigurációt.
> Az aktuális konfiguráció eltávolításához használja a [PowerShellt](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) , vagy törölje a helyi konfigurációs fájlt (ez az egyetlen lehetőség a Linux-csomópontok számára).

### <a name="azure-portal"></a>Azure Portal

Azure Automation a tartalomjegyzékben kattintson az **állapot konfigurálása (DSC)** elemre.
Ezután kattintson a **csomópontokra** a szolgáltatásban regisztrált csomópontok listájának megtekintéséhez.
Kattintson az eltávolítani kívánt csomópont nevére.
A megnyíló csomópont nézetben kattintson a **Regisztráció törlése**elemre.

### <a name="powershell"></a>PowerShell

Ha meg szeretné szüntetni a csomópont regisztrációját Azure Automation állapot-konfigurációs szolgáltatásból a PowerShell használatával, kövesse a következő parancsmag dokumentációját: [unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Következő lépések

- Első lépésként tekintse meg [az Azure Automation állapot konfigurációjának megismerése](automation-dsc-getting-started.md) című témakört.
- A csomópontok előkészítésének megismeréséhez lásd: [bevezetési gépek Azure Automation állapot-konfiguráció általi felügyelethez](automation-dsc-onboarding.md)
- A DSC-konfigurációk fordításának megismeréséhez, hogy hozzá lehessen rendelni azokat a célcsoportokhoz, tekintse meg a [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md) című témakört.
- A PowerShell-parancsmagok ismertetése: [Azure Automation állapot-konfigurációs parancsmagok](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Ha szeretné megtekinteni a Azure Automation állapot konfigurációjának folyamatos üzembe helyezési folyamatban való használatát, tekintse meg a [folyamatos üzembe helyezést a Azure Automation állapot-konfiguráció és a csokoládés használatával](automation-dsc-cd-chocolatey.md) .
