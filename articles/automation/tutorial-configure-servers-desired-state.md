---
title: Kiszolgálók célállapotba történő konfigurálása és eltéréskezelés az Azure Automation használatával
description: Oktatóanyag – az Azure Automation konfigurációs kiszolgáló-konfigurációk kezelése
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 582533d23757de748b9cc7d40e45acc00240d384
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570317"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Kiszolgálók célállapotba történő konfigurálása és eltéréskezelés

Azure Automation állapot konfiguráció lehetővé teszi, hogy a kiszolgálók konfigurációk meghatározását, és győződjön meg arról, hogy ezeken a kiszolgálókon állapotban van a megadott időszakban.

> [!div class="checklist"]
> - Előkészítése az Azure Automation DSC által kezelt virtuális gépek
> - A konfiguráció feltöltése az Azure Automationhöz
> - Csomópont-konfiguráció be konfiguráció fordítása
> - Csomópont-konfiguráció hozzárendelése egy felügyelt csomópont
> - A kezelt csomópontok megfelelőségi állapotának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
- Egy Azure Resource Manager virtuális gép (nem klasszikus) futó Windows Server 2008 R2 vagy újabb. A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.
- Az Azure PowerShell modul 3.6-os vagy újabb verziójára. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.
- Desired State Configuration (DSC) való ismerkedés során bizonyulhat. DSC kapcsolatos információkért lásd: [Windows PowerShell Desired State Configuration áttekintése](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Létrehozása és a konfiguráció feltöltése az Azure Automation

Ebben az oktatóanyagban egy egyszerű DSC-konfiguráció, amely biztosítja, hogy a virtuális gép telepítve van az IIS használjuk.

A DSC-konfigurációkról információért lásd: [DCS-konfigurációk](/powershell/dsc/configurations).

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

Hívja a `Import-AzureRmAutomationDscConfiguration` parancsmaggal töltse fel a konfiguráció az Automation-fiókba:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Csomópont-konfiguráció be konfiguráció fordítása

A DSC-konfiguráció kell lefordítva csomópont-konfiguráció ahhoz a csomóponthoz rendelhető.

További információ a konfiguráció fordítása: [DSC-konfigurációk](/powershell/dsc/configurations).

Hívja a `Start-AzureRmAutomationDscCompilationJob` összeállítása a parancsmag a `TestConfig` -konfigurációnak csomópont-konfiguráció:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Ez létrehoz egy nevű csomópont-konfiguráció `TestConfig.WebServer` az Automation-fiókban.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Állapotkonfiguráció által felügyelendő virtuális gép regisztrálása

Használhatja az Azure Automation Állapotkonfiguráció kezelése az Azure virtuális gépek (klasszikus és Resource Manager), a helyszíni virtuális gépek, Linux rendszerű gépek, az AWS-beli virtuális gépek és a helyszíni fizikai gépek. Ebben a témakörben bemutatjuk, hogyan regisztrálása csak az Azure Resource Manager virtuális gépeket. További információ a más típusú gépek regisztrálása: [gépek előkészítése kezelésre által az Azure Automation Állapotkonfiguráció](automation-dsc-onboarding.md).

Hívja a `Register-AzureRmAutomationDscNode` parancsmagot, hogy a virtuális gép regisztrálása az Azure Automation Állapotkonfiguráció.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Ez regisztrálja a megadott virtuális gép konfigurációs felügyelt csomópontként.

### <a name="specify-configuration-mode-settings"></a>Adja meg a konfigurációs mód beállításai

Amikor regisztrál egy virtuális gép felügyelt csomópontot, a konfiguráció tulajdonságainak is megadhatja. Például megadhatja, hogy a gép állapota csak egyszer alkalmazható (DSC nem kísérli meg a kezdeti ellenőrzés után a konfiguráció alkalmazásához) megadásával `ApplyOnly` értékeként a **ConfigurationMode** tulajdonság :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Azt is megadhatja, hogy milyen gyakran DSC ellenőrzi a konfigurációs állapotát az **ConfigurationModeFrequencyMins** tulajdonság:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Egy felügyelt csomópont konfigurációs tulajdonságainak beállításával kapcsolatos további információkért lásd: [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

DSC-konfigurációs beállításaival kapcsolatos további információkért lásd: [a Local Configuration Manager](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Csomópont-konfiguráció hozzárendelése egy felügyelt csomópont

Most már azt is hozzárendelhet a lefordított csomópont-konfigurációt a konfigurálni kívánt virtuális Gépet.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Ez hozzárendeli a nevű csomópont-konfiguráció `TestConfig.WebServer` a regisztrált DSC-csomóponthoz nevű `DscVm`.
A DSC-csomópont megfelel-e a csomópont-konfiguráció 30 percenként alapértelmezés szerint be van jelölve.
A megfelelőség-ellenőrzési időköz módosításával kapcsolatos további információkért lásd: [a Local Configuration Manager](/PowerShell/DSC/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>A kezelt csomópontok megfelelőségi állapotának ellenőrzése

Megtekintheti a jelentések a kezelt csomópontok megfelelőségi állapota meghívásával a `Get-AzureRmAutomationDscNodeReport` parancsmagot:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>További lépések

- Első lépésként lásd [Ismerkedés az Azure Automation Állapotkonfiguráció](automation-dsc-getting-started.md)
- Megtudhatja, hogyan előkészítheti a csomópontokat, hogy meg [gépek előkészítése kezelésre, az Azure Automation állapot konfigurációja](automation-dsc-onboarding.md)
- DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat kapcsolatos további információkért lásd: [összeállítása az Azure Automation Állapotkonfiguráció konfigurációk](automation-dsc-compile.md)
- PowerShell-parancsmagok leírása, lásd: [konfiguráló Azure Automation-parancsmagok](/powershell/module/azurerm.automation/#automation)
- Díjszabási információkért tekintse meg a [Azure Automation State Configuration díjszabása](https://azure.microsoft.com/pricing/details/automation/)
- Folyamatos üzembe helyezés folyamatban lévő Azure Automation Állapotkonfiguráció használatának példájáért lásd: [folyamatos üzembe helyezés használatával az Azure Automation Állapotkonfiguráció és a chocolatey-t](automation-dsc-cd-chocolatey.md)
