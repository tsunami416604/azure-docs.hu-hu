---
title: Kiszolgálók célállapotba történő konfigurálása és eltéréskezelés az Azure Automation használatával
description: Oktatóanyag – Kiszolgálókonfigurációk kezelése az Azure Automation állapotkonfigurációjával
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 9e2f04f59a56be6c516eb90de45fdf7327673086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416590"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Kiszolgálók konfigurálása a kívánt állapotra és a drift kezelése

Az Azure Automation állapotkonfiguráció lehetővé teszi, hogy adja meg a konfigurációk a kiszolgálók, és győződjön meg arról, hogy ezek a kiszolgálók a megadott állapotban idővel.

> [!div class="checklist"]
> - Az Azure Automation DSC által kezelendő virtuális gép alaplapja
> - Konfiguráció feltöltése az Azure Automationbe
> - Konfiguráció fordítása csomópontkonfigurációba
> - Csomópont-konfiguráció hozzárendelése felügyelt csomóponthoz
> - Felügyelt csomópont megfelelőségi állapotának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](automation-sec-configure-azure-runas-account.md) kapcsolatos részt.
- Windows Server 2008 R2 vagy újabb rendszert futtató Azure Resource Manager virtuális gép(nem klasszikus). A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/virtual-machines-windows-hero-tutorial.md) című cikket.
- Az Azure PowerShell-modul 3.6-os vagy újabb verziója. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket.
- A kívánt állapotkonfiguráció (DSC) ismerete. A DSC-ről a [Windows PowerShell kívánt állapotkonfiguráció – áttekintéscímű témakörében olvashat.](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzureRmAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Konfiguráció létrehozása és feltöltése az Azure Automationbe

Ebben az oktatóanyagban egy egyszerű DSC-konfigurációt fogunk használni, amely biztosítja, hogy az IIS telepítve van a virtuális gépre.

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
> A fejlettebb esetekben, ahol több modul importálására van szükség, amelyek DSC-erőforrásokat biztosítanak, győződjön meg arról, hogy minden modul egyedi `Import-DscResource` vonallal rendelkezik a konfigurációban.

Hívja `Import-AzureRmAutomationDscConfiguration` fel a parancsmast a konfiguráció Automation-fiókba való feltöltéséhez:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Konfiguráció fordítása csomópontkonfigurációba

A DSC-konfigurációt le kell fordítani egy csomópontkonfigurációba, mielőtt hozzárendelhető lenne egy csomóponthoz.

A konfigurációk összeállításáról a [DSC-konfigurációk](/powershell/scripting/dsc/configurations/configurations)című témakörben talál további információt.

Hívja `Start-AzureRmAutomationDscCompilationJob` meg a parancsmast a `TestConfig` konfiguráció csomópont-konfigurációba történő lefordításához:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Ez létrehoz egy csomópont `TestConfig.WebServer` konfiguráció neve az Automation-fiókban.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Az állapotkonfiguráció által kezelendő virtuális gép regisztrálása

Az Azure Automation State Configuration használatával kezelheti az Azure virtuális gépeket (klasszikus és erőforrás-kezelőt is), a helyszíni virtuális gépeket, a Linux-gépeket, az AWS virtuális gépeket és a helyszíni fizikai gépeket. Ebben a témakörben azt ismerteti, hogyan regisztrálhatja csak az Azure Resource Manager virtuális gépek. A más típusú gépek regisztrálásáról a [Bevezetési gépek az Azure Automation állapotkonfigurációáltali kezeléscímű témakörben](automation-dsc-onboarding.md)talál.

Hívja `Register-AzureRmAutomationDscNode` meg a parancsmacst a virtuális gép regisztrálásához az Azure Automation állapotkonfigurációval.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Ez regisztrálja a megadott virtuális gépet felügyelt csomópontként az állapotkonfigurációban.

### <a name="specify-configuration-mode-settings"></a>Konfigurációs mód beállításainak megadása

Ha egy virtuális gépet felügyelt csomópontként regisztrál, megadhatja a konfiguráció tulajdonságait is. Megadhatja például, hogy a számítógép állapotát csak egyszer kell alkalmazni (a DSC nem próbálja meg alkalmazni `ApplyOnly` a konfigurációt az első ellenőrzés után) a **ConfigurationMode** tulajdonság értékének megadásával:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Azt is megadhatja, hogy a DSC milyen gyakran ellenőrizze a konfigurációs állapotot a **ConfigurationModeFrequencyMins** tulajdonság használatával:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

A felügyelt csomópontok konfigurációs tulajdonságainak beállításáról a [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)című témakörben talál további információt.

A DSC konfigurációs beállításairól a [Helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig)című témakörben olvashat bővebben.

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Csomópont-konfiguráció hozzárendelése felügyelt csomóponthoz

Most már hozzárendelheti a lefordított csomópont konfigurációját a beállítani kívánt virtuális géphez.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Ez hozzárendeli a `TestConfig.WebServer` regisztrált DSC-csomópontnak `DscVm`nevezett csomópontkonfigurációt.
Alapértelmezés szerint a DSC-csomópont 30 percenként ellenőrzi, hogy megfelel-e a csomópont konfigurációjának.
A megfelelőség-ellenőrzési időköz módosításáról a [Helyi konfigurációkezelő konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig)című témakörben talál további információt.

## <a name="working-with-partial-configurations"></a>Részleges konfigurációk használata

Az Azure Automation state configuration támogatja a [részleges konfigurációk](/powershell/scripting/dsc/pull-server/partialconfigs)használatát.
Ebben a forgatókönyvben a DSC több konfiguráció egymástól függetlenül történő kezelésére van konfigurálva, és minden konfiguráció lekérésre kerül az Azure Automationből.
Azonban automatizálási fiókonként csak egy konfiguráció rendelhető hozzá egy csomóponthoz.
Ez azt jelenti, hogy ha egy csomóponthoz két konfigurációt használ, két automatizálási fiókra lesz szükség.

A részleges konfiguráció késési szolgáltatásból történő regisztrálásáról a [részleges konfigurációk dokumentációjában](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)olvashat.

Ha többet szeretne tudni arról, hogy a csapatok hogyan dolgozhatnak együtt a kiszolgálók kódként történő közös [kezelésében, olvassa el a DSC CI/CD-folyamatban betöltött szerepének ismertetése című témakört.](/powershell/scripting/dsc/overview/authoringadvanced)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Felügyelt csomópont megfelelőségi állapotának ellenőrzése

A felügyelt csomópontok megfelelőségi állapotáról a `Get-AzureRmAutomationDscNodeReport` parancsmag hívásával kaphat jelentéseket:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Csomópontok eltávolítása a szolgáltatásból

Amikor hozzáad egy csomópontot az Azure Automation State Configuration, a beállítások at Local Configuration Manager vannak beállítva, hogy regisztráljon a szolgáltatásés a lekéréses konfigurációk és a szükséges modulokat a számítógép konfigurálásához.
Ha úgy dönt, hogy eltávolítja a csomópontot a szolgáltatásból, ezt megteheti az Azure Portalon vagy az Az parancsmagok használatával.

> [!NOTE]
> Egy csomópont szolgáltatásból való törlésének törlése csak a Helyi konfigurációkezelő beállításait állítja be, így a csomópont már nem csatlakozik a szolgáltatáshoz.
> Ez nem befolyásolja a csomópontra jelenleg alkalmazott konfigurációt.
> Az aktuális konfiguráció eltávolításához használja a [PowerShellt,](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) vagy törölje a helyi konfigurációs fájlt (ez az egyetlen linuxos csomópont.

### <a name="azure-portal"></a>Azure portál

Az Azure Automation, kattintson **az állapotkonfiguráció (DSC)** a tartalomjegyzékben.
Ezután kattintson a **Csomópontok gombra** a szolgáltatásban regisztrált csomópontok listájának megtekintéséhez.
Kattintson az eltávolítani kívánt csomópont nevére.
A megnyíló csomópontnézetben kattintson a **Regisztráció megszüntetése gombra.**

### <a name="powershell"></a>PowerShell

Ha törölni szeretne egy csomópontot az Azure Automation állapotkonfigurációs szolgáltatásból a PowerShell használatával, kövesse az [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0)parancsmag dokumentációját.

## <a name="next-steps"></a>További lépések

- Első lépések: [Az Azure Automation állapotkonfigurációjának első lépései](automation-dsc-getting-started.md)
- A fedélzeti csomópontok ról a [Bevezetési gépek az Azure Automation állapotkonfigurációja általi kezelésről olvashat.](automation-dsc-onboarding.md)
- A DSC-konfigurációk összeállításáról, hogy hozzárendelhesse őket a célcsomópontokhoz, olvassa el [a Konfigurációk összeállítása az Azure Automation állapotkonfigurációjában](automation-dsc-compile.md)
- A PowerShell-parancsmagok hivatkozásáról az [Azure Automation állapotkonfigurációs parancsmagjai](/powershell/module/azurerm.automation/#automation)
- A díjszabással kapcsolatos információkért lásd: [Azure Automation State Configuration pricing](https://azure.microsoft.com/pricing/details/automation/)
- Az Azure Automation állapotkonfigurációjának folyamatos üzembe helyezési folyamatban való használatára példa: [Folyamatos üzembe helyezés az Azure Automation állapotkonfiguráció és csokoládés használatával](automation-dsc-cd-chocolatey.md)
