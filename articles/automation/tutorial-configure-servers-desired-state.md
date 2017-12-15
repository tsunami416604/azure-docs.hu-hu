---
title: "A kívánt állapot-kiszolgálók konfigurálása és kezelése az Azure Automation szolgáltatásban, mivel |} Microsoft Docs"
description: "Az oktatóanyag - kiszolgáló kezelését az Azure Automation DSC Szolgáltatásban"
services: automation
documentationcenter: automation
author: georgewallace
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: gwallace
ms.custom: 
ms.openlocfilehash: c510b2244dfa85b12ed08cad9dbab75067ebe41a
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>A kívánt állapot-kiszolgálók konfigurálása és kezelése eltolódás

Azure Automation kívánt állapot konfigurációs szolgáltatása (DSC) lehetővé teszi a kiszolgálók konfigurációk, és győződjön meg arról, hogy ezek a kiszolgálók állapotban van a megadott adott idő alatt.



> [!div class="checklist"]
> * A bevezetni egy Azure Automation DSC által kezelt virtuális Gépet
> * Egy konfigurációs feltöltése az Azure Automation
> * A konfiguráció a csomópont-konfiguráció-fordítási
> * A csomópont-konfiguráció hozzárendelése egy felügyelt csomópont
> * A felügyelt csomóponthoz megfelelőségi állapotának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez szüksége lesz:

* Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
* Az Azure Resource Manager virtuális gép (klasszikus) Windows Server 2008 R2 rendszerű vagy újabb. A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.
* Az Azure PowerShell 3,6 vagy újabb verziója. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.
* A DSC ismeretét. A DSC kapcsolatos információkért lásd: [Windows PowerShell kívánt állapot beállítása – áttekintés](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Login-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Login-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Hozzon létre, és egy konfigurációs feltöltése az Azure Automation

Ebben az oktatóanyagban egy egyszerű DSC-konfiguráció, amely biztosítja, hogy a virtuális gép telepítve van az IIS használjuk.

A DSC-konfigurációkkal kapcsolatos információkért lásd: [a DSC-konfigurációk](https://docs.microsoft.com/powershell/dsc/configurations).

Egy szövegszerkesztőben, írja be a következőt, és mentse a fájlt helyileg `TestConfig.ps1`.

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

Hívja a `Import-AzureRmAutomationDscConfiguration` parancsmaggal töltse fel a konfigurációs adatok az Automation-fiók:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>A konfiguráció a csomópont-konfiguráció-fordítási

A DSC-konfiguráció kell állítható össze a csomópont-konfiguráció előtt rendelhető hozzá egy csomópont.

További információ a konfiguráció fordítása: [a DSC-konfigurációk](https://docs.microsoft.com/powershell/dsc/configurations).

Hívja a `Start-AzureRmAutomationDscCompilationJob` parancsmag segítségével lefordítani a `TestConfig` konfigurációs adatok a csomópont-konfiguráció:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Ezzel létrehoz egy csomópont-konfiguráció `TestConfig.WebServer` az Automation-fiókban.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Regisztrálja a virtuális gépek DSC kell kezelnie

Azure Automation DSC segítségével kezelheti az Azure virtuális gépeken (klasszikus és Resource Manager), a helyszíni virtuális gépek, Linux rendszerű gépek, AWS virtuális gépek és a helyszíni fizikai gépeket. Ebben a témakörben azt csak a Azure Resource Manager virtuális gépek regisztrálása fedik le.
Más típusú gépek nyilvántartására vonatkozó további információkért lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md).

Hívja a `Register-AzureRmAutomationDscNode` parancsmag futtatásával regisztrálja a virtuális Gépet az Azure Automation DSC Szolgáltatásban.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

A megadott virtuális gép ezzel regisztrálja a DSC-csomópont, az Azure Automation-fiókban.

### <a name="specify-configuration-mode-settings"></a>Adja meg a konfigurációs mód beállításai

Amikor regisztrál egy virtuális Gépet felügyelt csomópontként, adja meg a konfiguráció tulajdonságainak.
Például megadhatja, hogy a gép állapota csak egyszer alkalmazandó (DSC nem kísérli meg a kezdeti ellenőrzés után a konfiguráció alkalmazásához) megadásával `ApplyOnly` értékeként a **ConfigurationMode** tulajdonság :

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

Azt is megadhatja, milyen gyakran DSC konfigurációs állapotát ellenőrzi a a **ConfigurationModeFrequencyMins** tulajdonság:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

A felügyelt csomóponthoz tartozó konfigurációs tulajdonságok beállításával kapcsolatos további információkért lásd: [Register-AzureRmAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

DSC konfigurációs beállításaival kapcsolatos további információkért lásd: [konfigurálása a helyi Configuration Manager](https://docs.microsoft.com/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>A csomópont-konfiguráció hozzárendelése egy felügyelt csomópont

Most azt is a lefordított csomópont-konfiguráció hozzárendelése a virtuális gép szeretnénk konfigurálni.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

Ez a csomópont-konfiguráció hozzárendel `TestConfig.WebServer` nevű regisztrált DSC-csomóponthoz `DscVm`.
Alapértelmezés szerint a DSC-csomópont van a csomópont-konfiguráció 30 percenként megfelelőségét ellenőrzi.
A megfelelőség-ellenőrzési időköz módosításával kapcsolatos információkért lásd: [a helyi Configuration Manager konfigurálása](https://docs.microsoft.com/PowerShell/DSC/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>A felügyelt csomóponthoz megfelelőségi állapotának ellenőrzése

Meghívásával kaphat a jelentéseket a DSC-csomópont megfelelőségi állapotát az `Get-AzureRmAutomationDscNodeReport` parancsmagot:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$reports[0]
```

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan előkészítésére csomópontokat a felügyelethez az Azure Automation DSC Szolgáltatásban, lásd: [bevezetési gépeket Azure Automation DSC általi kezelésre](automation-dsc-onboarding.md)
* Automation DSC használata az Azure-portál használatával kapcsolatban lásd: [Ismerkedés az Azure Automation DSC](automation-dsc-getting-started.md)
* A DSC-konfigurációk fordítása, így hozzárendelheti azokat a célcsomópontokat, lásd: [fordítása Azure Automation DSC-konfigurációja](automation-dsc-compile.md)
* PowerShell parancsmag-referencia az Azure Automation DSC Szolgáltatásban, lásd: [Azure Automation DSC-parancsmagok](/powershell/module/azurerm.automation/#automation)
* Díjszabási információkért lásd: [Azure Automation DSC díjszabása](https://azure.microsoft.com/pricing/details/automation/)
* Egy Azure Automation DSC használata a folyamatos üzembe helyezési folyamat példát, olvassa el [IaaS virtuális gépek használata Azure Automation DSC és Chocolatey folyamatos üzembe helyezés](automation-dsc-cd-chocolatey.md)
