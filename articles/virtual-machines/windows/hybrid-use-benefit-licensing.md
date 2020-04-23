---
title: Azure Hybrid Benefit Windows Serverhez
description: Megtudhatja, hogy miként maximalizálhatja a Windows frissítési garanciával kapcsolatos előnyeit, hogy helyszíni licenceket hozhasson az Azure-ba
author: xujing-ms
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: f84d4fcd85f1e718f414e63bbe76fd29fa32427d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869568"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit Windows Serverhez
A Frissítési Garanciával rendelkező ügyfelek számára az Azure Hybrid Benefit for Windows Server lehetővé teszi a helyszíni Windows Server-licencek használatát és a Windows virtuális gépek futtatását az Azure-ban csökkentett költséggel. Az Azure Hybrid Benefit for Windows Server segítségével új virtuális gépeket telepíthet a Windows operációs rendszerrel. Ez a cikk bemutatja az új virtuális gépek üzembe helyezésének lépéseit az Azure Hybrid Benefit for Windows Server használatával, és hogyan frissítheti a meglévő futó virtuális gépeket. Az Azure Hybrid Benefit for Windows Server licenceléséről és költségmegtakarításáról az [Azure Hybrid Benefit for Windows Server licencelési lapján talál](https://azure.microsoft.com/pricing/hybrid-use-benefit/)további információt.

Minden kétprocesszoros licenc vagy 16 magos licenckészlet két, legfeljebb 8 magból álló példányra vagy egy legfeljebb 16 magból álló példányra jogosult. Az Azure Hybrid Benefit standard kiadású licencek csak akkor használható, ha a helyszínen vagy az Azure-ban. A Datacenter Edition előnyei lehetővé teszik a helyszíni és az Azure-beli egyidejű használatot.

Az Azure Hybrid Benefit for Windows Server használata a Windows Server operációs rendszert futtató virtuális gépeken már minden régióban támogatott, beleértve a további szoftverekkel, például az SQL Server vagy a külső piactéri szoftverrel rendelkező virtuális gépeket is. 


## <a name="classic-vms"></a>A klasszikus virtuális gépeket

A klasszikus virtuális gépek, csak üzembe helyezése új virtuális gép a helyszíni egyéni rendszerképek támogatott. A cikkben támogatott képességek kihasználásához először át kell telepítenie a klasszikus virtuális gépeket az Erőforrás-kezelő modellbe.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Az Azure Hybrid Benefit for Windows Server használatának módjai
Az Azure Hybrid Benefit használatával kevés lehetőség van a Windows virtuális gépek használatára:

1. Virtuális gépeket telepíthet a megadott Windows Server-lemezképek egyikéről az Azure Piactéren
2. Feltölthet egy egyéni virtuális gép, és üzembe helyezheti egy Resource Manager sablon vagy az Azure PowerShell
3. Válthat és konvertálhat meglévő virtuális gépeket az Azure Hybrid Benefit használatával való futtatás vagy a Windows Server igény szerinti költsége között
4. Az Azure Hybrid Benefit for Windows Server szolgáltatást virtuálisgép-méretezési csoporton is alkalmazhatja


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Virtuális gép létrehozása az Azure Hybrid Benefit for Windows Server alkalmazással
Az Azure Hybrid Benefit for Windows Server minden Windows Server operációs rendszeralapú lemezkép támogatott. Használhatja az Azure platform támogatási lemezképeit, vagy feltöltheti saját egyéni Windows Server-lemezképeit. 

### <a name="portal"></a>Portál
Ha virtuális gép létrehozása az Azure Hybrid Benefit for Windows Server, használja a kapcsoló t "Pénzmegtakarítás" szakaszban.

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Sablon
Az Erőforrás-kezelő sablonokon belül `licenseType` meg kell adni egy további paramétert. Az [Azure Resource Manager-sablonok szerzőiről](../../resource-group-authoring-templates.md) további információ
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Meglévő virtuális gép konvertálása az Azure Hybrid Benefit for Windows Server használatával
Ha rendelkezik egy meglévő virtuális gép, amely szeretné átalakítani, hogy kihasználják az Azure Hybrid Benefit for Windows Server, frissítheti a virtuális gép licenctípusát az alábbi utasításokat követve.

> [!NOTE]
> A virtuális gép licenctípusának módosítása nem okozza a rendszer újraindítását vagy szolgáltatás-interuption-et.  Ez egyszerűen egy metaadat-jelző frissítése.
> 

### <a name="portal"></a>Portál
A portálos virtuálisgép-panelről frissítheti a virtuális gép használatát az Azure Hybrid Benefit használatához a "Konfiguráció" lehetőség kiválasztásával, és válthat az "Azure hibrid juttatás" lehetőségközött.

### <a name="powershell"></a>PowerShell
- Meglévő Windows Server-virtuális gépek átalakítása Azure Hybrid Benefit for Windows Server alkalmazásba

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- A juttatással rendelkező Windows Server virtuális gépek konvertálása felosztó-kirendelt) fizetésre

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>parancssori felület
- Meglévő Windows Server-virtuális gépek átalakítása Azure Hybrid Benefit for Windows Server alkalmazásba

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Hogyan ellenőrizheti, hogy a virtuális gép a licencelési előny felhasználásával
Miután üzembe helyezte a virtuális gép vagy PowerShell, Resource Manager sablon vagy portál, ellenőrizheti a beállítást a következő módszerekben.

### <a name="portal"></a>Portál
A portális virtuális gép panelen megtekintheti az Azure Hybrid Benefit for Windows Server kapcsolóját a "Konfiguráció" fül kiválasztásával.

### <a name="powershell"></a>PowerShell
A következő példa egyetlen virtuális gép licenctípusát mutatja be
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Kimenet:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ez a kimenet ellentétben áll az Azure Hybrid Benefit for Windows Server licencelés e nélkül telepített következő virtuális gépekkel:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> A virtuális gép licenctípusának módosítása nem okozza a rendszer újraindítását vagy szolgáltatás-interuption-et. Csak metaadat-licencelési jelző.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Az összes virtuális gép listázása az Azure Hybrid Benefit for Windows Server alkalmazással előfizetésben
Az Azure Hybrid Benefit for Windows Server alkalmazással telepített összes virtuális gép megtekintéséhez és megszámlálásához futtassa a következő parancsot az előfizetéséből:

### <a name="portal"></a>Portál
A virtuális gép vagy a virtuális gép méretezési készletek erőforrás panel, megtekintheti az összes virtuális gép és a licencelési típus a tábla oszlop konfigurálásával az "Azure Hybrid Benefit". A virtuális gép beállítása lehet "Engedélyezve", "Nem engedélyezve" vagy "Nem támogatott" állapotban.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Virtuálisgép-méretezési készlet üzembe helyezése az Azure Hybrid Benefit for Windows Server alkalmazással
A virtuális gép méretezési készletében Erőforrás-kezelő sablonok, egy további paramétert `licenseType` kell megadni a VirtualMachineProfile tulajdonság. Ezt a méretezési csoport létrehozása vagy frissítése során arm sablon, PowerShell, Azure CLI vagy REST keresztül.

A következő példa ARM-sablont használ Windows Server 2016 Datacenter-lemezképpel:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
A rról is többet is megtudhat, hogyan [módosíthatja a virtuálisgép-méretezési készletet](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) a méretezési csoport frissítésének további módjairól.

## <a name="next-steps"></a>További lépések
- További információ [arról, hogyan takaríthat meg pénzt az Azure Hybrid Benefit használatával](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- További információ az [Azure Hybrid Benefit kel kapcsolatos gyakori kérdésekről](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- További információ az [Azure Hybrid Benefit for Windows Server licenceléséről részletes útmutatás](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- További információ az [Azure Hybrid Benefit for Windows Server és az Azure Site Recovery alkalmazásokkal kapcsolatban, amelyek még költséghatékonyabbá teszik az alkalmazások Azure-ba való áttelepítését](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- További információ az [Azure-beli Windows 10-ről a több-bérlős üzemeltetési jog](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- További információ az [Erőforrás-kezelő sablonok használatáról](../../azure-resource-manager/management/overview.md)
