---
title: Kiszolgálók célállapotba történő konfigurálása és eltéréskezelés az Azure Automation használatával
description: Oktatóanyag – Kiszolgálókonfigurációk kezelése az Azure Automation állapotkonfigurációjával
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: a02c664ddf0802ad5ac306f98de14b7c0d5d7271
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678712"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Kiszolgálók konfigurálása a kívánt állapotra és a drift kezelése

Az Azure Automation állapotkonfiguráció lehetővé teszi, hogy adja meg a konfigurációk a kiszolgálók, és győződjön meg arról, hogy ezek a kiszolgálók a megadott állapotban idővel.

> [!div class="checklist"]
> - Az Azure Automation DSC által kezelendő virtuális gép alaplapja
> - Konfiguráció feltöltése az Azure Automationbe
> - Konfiguráció fordítása csomópontkonfigurációba
> - Csomópont-konfiguráció hozzárendelése felügyelt csomóponthoz
> - Felügyelt csomópont megfelelőségi állapotának ellenőrzése

Ebben az oktatóanyagban egy egyszerű [DSC-konfigurációt](/powershell/scripting/dsc/configurations/configurations) használunk, amely biztosítja, hogy az IIS telepítve van a virtuális gépre.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
- Windows Server 2008 R2 vagy újabb rendszert futtató Azure Resource Manager virtuális gép.An Azure Resource Manager VM (not classic) running Windows Server 2008 R2 or later. A virtuális gépek létrehozásáról az [Első Windows virtuális gép létrehozása az Azure Portalon](../virtual-machines/virtual-machines-windows-hero-tutorial.md)című témakörben talál.
- Az Azure PowerShell-modul 3.6-os vagy újabb verziója. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.
- A kívánt állapotkonfiguráció (DSC) ismerete. A DSC-ről a [Windows PowerShell kívánt állapotkonfiguráció – áttekintés című témakörében olvashat.](/powershell/scripting/dsc/overview/overview)

## <a name="support-for-partial-configurations"></a>Részleges konfigurációk támogatása

Az Azure Automation state configuration támogatja a [részleges konfigurációk](/powershell/scripting/dsc/pull-server/partialconfigs)használatát. Ebben a forgatókönyvben a DSC több konfiguráció egymástól függetlenül történő kezelésére van konfigurálva, és minden konfiguráció lekérésre kerül az Azure Automationből. Azonban automatizálási fiókonként csak egy konfiguráció rendelhető hozzá egy csomóponthoz. Ez azt jelenti, hogy ha egy csomóponthoz két konfigurációt használ, két Automation-fiókra lesz szüksége.

A részleges konfiguráció késedelmi szolgáltatásból történő regisztrálásáról a [részleges konfigurációk dokumentációjában](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)olvashat.

Ha többet szeretne tudni arról, hogy a csapatok hogyan dolgozhatnak együtt a kiszolgálók kódként imázsát használó kiszolgálók közös kezelésében, [olvassa el a DSC CI/CD-folyamatban betöltött szerepének ismertetése című témakört.](/powershell/scripting/dsc/overview/authoringadvanced)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be Azure-előfizetésébe a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) parancsmaggal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Konfiguráció létrehozása és feltöltése az Azure Automationbe


A szövegszerkesztőbe írja be a következőket, és mentse helyileg **TestConfig.ps1 néven.**

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
> A fejlettebb esetekben, ahol több modul importálására van szükség, amelyek DSC-erőforrásokat biztosítanak, győződjön meg arról, hogy minden modul egyedi `Import-DscResource` vonallal rendelkezik a konfigurációban.

Hívja meg az [Import-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration?view=azps-3.7.0) parancsmagfeltöltésa a konfigurációt az Automation-fiókba.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Konfiguráció fordítása csomópontkonfigurációba

A DSC-konfigurációt le kell fordítani egy csomópontkonfigurációba, mielőtt hozzárendelhető lenne egy csomóponthoz. Lásd: [DSC-konfigurációk](/powershell/scripting/dsc/configurations/configurations).

Hívja meg a [Start-AzAutomationDscCompilationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob?view=azps-3.7.0) parancsmag fordításához a `TestConfig` `TestConfig.WebServer` konfiguráció egy csomópont konfiguráció neve az Automation-fiókban.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Az állapotkonfiguráció által kezelendő virtuális gép regisztrálása

Az Azure Automation State Configuration használatával kezelheti az Azure virtuális gépeket (klasszikus és erőforrás-kezelőt is), a helyszíni virtuális gépeket, a Linux-gépeket, az AWS virtuális gépeket és a helyszíni fizikai gépeket. Ebben a témakörben azt ismerteti, hogyan regisztrálhatja csak az Azure Resource Manager virtuális gépek. A más típusú gépek regisztrálásáról a [Bevezetési gépek az Azure Automation állapotkonfigurációáltali kezeléscímű témakörben](automation-dsc-onboarding.md)talál.

Hívja meg a [Register-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationDscNode?view=azps-3.7.0) parancsmag regisztrálni a virtuális gépet az Azure Automation állapotkonfiguráció felügyelt csomópontként. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Konfigurációs mód beállításainak megadása

A [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) parancsmag használatával regisztráljon egy virtuális gépet felügyelt csomópontként, és adja meg a konfigurációs tulajdonságokat. Megadhatja például, hogy a gép állapotát csak egyszer kell alkalmazni, ha a tulajdonság értékeként adja `ApplyOnly` meg. `ConfigurationMode` Az állapotkonfiguráció nem próbálja meg alkalmazni a konfigurációt az első ellenőrzés után.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Azt is megadhatja, hogy a DSC `ConfigurationModeFrequencyMins` milyen gyakran ellenőrizze a konfigurációs állapotot a tulajdonság használatával. A DSC konfigurációs beállításairól a [Helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig)című témakörben olvashat bővebben.

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Csomópont-konfiguráció hozzárendelése felügyelt csomóponthoz

Most már hozzárendelheti a lefordított csomópont konfigurációját a beállítani kívánt virtuális géphez.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Ez hozzárendeli a `TestConfig.WebServer` regisztrált DSC-csomóponthoz `DscVm`elnevezett csomópontkonfigurációt. Alapértelmezés szerint a DSC-csomópont 30 percenként ellenőrzi, hogy megfelel-e a csomópont konfigurációjának. A megfelelőség-ellenőrzési időköz módosításáról a [Helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig)című témakörben talál további információt.

## <a name="check-the-compliance-status-of-a-managed-node"></a>Felügyelt csomópont megfelelőségi állapotának ellenőrzése

A [Get-AzAutomationDscNodeReport](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport?view=azps-3.7.0) parancsmag használatával jelentéseket kaphat egy felügyelt csomópont megfelelőségi állapotáról.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Csomópontok eltávolítása a szolgáltatásból

Amikor hozzáad egy csomópontot az Azure Automation State Configuration, a beállítások at Local Configuration Manager vannak beállítva, hogy regisztráljon a szolgáltatásés a lekéréses konfigurációk és a szükséges modulokat a számítógép konfigurálásához.
Ha úgy dönt, hogy eltávolítja a csomópontot a szolgáltatásból, ezt megteheti az Azure Portalon vagy az Az parancsmagok használatával.

> [!NOTE]
> Egy csomópont szolgáltatásból való törlésének törlése csak a Helyi konfigurációkezelő beállításait állítja be, így a csomópont már nem csatlakozik a szolgáltatáshoz.
> Ez nem befolyásolja a csomópontra jelenleg alkalmazott konfigurációt.
> Az aktuális konfiguráció eltávolításához használja a [PowerShellt,](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) vagy törölje a helyi konfigurációs fájlt (ez az egyetlen linuxos csomópont.

### <a name="azure-portal"></a>Azure Portal

Az Azure Automation, kattintson **az állapotkonfiguráció (DSC)** a tartalomjegyzékben.
Ezután kattintson a **Csomópontok gombra** a szolgáltatásban regisztrált csomópontok listájának megtekintéséhez.
Kattintson az eltávolítani kívánt csomópont nevére.
A megnyíló csomópontnézetben kattintson a **Regisztráció megszüntetése gombra.**

### <a name="powershell"></a>PowerShell

Ha törölni szeretne egy csomópontot az Azure Automation állapotkonfigurációs szolgáltatásból a PowerShell használatával, kövesse az [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0)parancsmag dokumentációját.

## <a name="next-steps"></a>További lépések

- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései.](automation-dsc-getting-started.md)
- A csomópontok alaplapi állapotáról a [Bevezetési gépek az Azure Automation állapotkonfigurációáltali kezelésről](automation-dsc-onboarding.md)olvashat.
- A DSC-konfigurációk összeállításáról a célcsomópontokhoz való hozzárendelésről a [Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)témakörben olvashat.
- A PowerShell-parancsmagok hivatkozásáról az [Azure Automation állapotkonfigurációs parancsmagjai.](/powershell/module/azurerm.automation/#automation)
- Díjszabási információkért lásd: [Azure Automation state configuration fortaring.](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation állapotkonfigurációjának folyamatos üzembe helyezési folyamatban való használatára példa: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és csokoládés használatával](automation-dsc-cd-chocolatey.md)
