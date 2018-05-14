---
title: Windows Server az Azure hibrid juttatása |} Microsoft Docs
description: Útmutató a Windows Software Assurance előnyeit, hogy helyszíni licencek Azure maximalizálása
services: virtual-machines-windows
documentationcenter: ''
author: xujing
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing-ms
ms.openlocfilehash: a4b0baefc8c3c839a06d6540e57b34657138c8ff
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit Windows Serverhez
Software Assurance rendelkező ügyfelek Azure hibrid juttatás for Windows Server lehetővé teszi a helyszíni Windows Server licencek használja, és futtassa a Windows virtuális gépek Azure költséghatékony. Azure hibrid juttatás for Windows Server segítségével új virtuális gépek telepítése a Windows operációs rendszer. Ez a cikk halad keresztül lépéseket, a új virtuális gépek Azure hibrid juttatás for Windows Server telepítése, és hogyan frissíthet meglévő futtató virtuális gépek. További információ az Azure hibrid juttatás for Windows Server licencelési és pluszköltséggel kevésbé kell kihasználni, tekintse meg a [Azure hibrid juttatás for Windows Server licencelési oldal](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Minden kétprocesszoros licenc vagy 16 magos licenckészlet két, legfeljebb 8 magos példányra, vagy egy, legfeljebb 16 magos példányra jogosít. A Standard kiadásra szóló licencek esetében az Azure Hybrid Benefit értékelem csak egyszer használható fel: vagy a helyszínen, vagy az Azure-ban, a Datacenter kiadásra szóló licencek esetében viszont párhuzamosan használható a helyszínen és az Azure-ban.
>

> [!Important]
> A Windows Server operációs rendszereket futtató virtuális gépek Azure hibrid juttatás a Windows Server használatával mostantól támogatja a minden egyes, beleértve az virtuális gépeket vagy külső piactér további szoftvereket, például az SQL Server. 
>

> [!NOTE]
> Klasszikus virtuális gépekhez csak az új virtuális gép helyszíni egyéni lemezképek telepítése esetén támogatott. Az ebben a cikkben támogatott képességek kihasználásához, először át kell klasszikus virtuális gépek erőforrás-kezelő modellhez.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Azure hibrid juttatás for Windows Server használatának módjai
Windows virtuális gépek használata az Azure hibrid juttatás néhány módja van:

1. Telepíthet egy, a megadott virtuális gépek [Windows Server lemezképeket az Azure piactéren](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Töltse fel egy egyéni virtuális Gépet, és a Resource Manager-sablon vagy az Azure PowerShell telepítése
3. Váltás és konvertálja a meglévő virtuális gép közötti Azure hibrid juttatás fut vagy igény szerinti költségek ellenében Windows Server
4. A virtuálisgép-méretezési készletben, valamint is alkalmazhat Azure hibrid juttatás for Windows Server


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Virtuális gép létrehozása az Azure hibrid juttatásra, a Windows Server
Azure hibrid juttatás for Windows Server összes Windows Server operációs rendszer alapján lemezképek támogatottak. Az Azure platform támogatása a lemezképeket, vagy a saját egyéni Windows Server-lemezképek feltöltése. 

### <a name="portal"></a>Portál
Virtuális gép létrehozása Azure hibrid előnye a Windows Server, használja a váltógomb "Költségtakarékosabb munkavégzésben" szakaszban.

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmVm `
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
A Resource Manager-sablonok, egy további paraméter belül `licenseType` meg kell adni. További tudnivalók [Azure Resource Manager sablonok készítése](../../resource-group-authoring-templates.md)
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Azure hibrid juttatás for Windows Server egy meglévő virtuális gép átalakítása
Ha egy meglévő virtuális Gépet, amely szeretné átalakítása Azure hibrid juttatás for Windows Server előnyeit, frissítheti a virtuális gép licenc típusa az alábbiak szerint:

### <a name="portal"></a>Portál
Portálról VM panelen frissítse a virtuális gép használata a Azure hibrid kiszolgálóterhelések használják ki a "Konfiguráció" lehetőség kiválasztásával és az "Azure hibrid előnyeit" beállítás bekapcsolására

### <a name="powershell"></a>PowerShell
- Meglévő Windows Server virtuális gépek átalakítása Azure hibrid juttatás for Windows Server

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Alakítsa át a Windows Server virtuális gépen a juttatás vissza a használatalapú fizetés

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>parancssori felület
- Meglévő Windows Server virtuális gépek átalakítása Azure hibrid juttatás for Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```
    
### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Hogyan állapítható meg a virtuális gép van használata a licencelési juttatás
Miután a virtuális Gépet, vagy PowerShell, a Resource Manager-sablon vagy a portálon keresztül telepített ellenőrizheti a beállítás a következő módszerek.

### <a name="portal"></a>Portál
Portálról VM panelen megtekintheti a váltógomb Azure hibrid előnye a Windows Server "Konfiguráció" fülre.

### <a name="powershell"></a>PowerShell
A következő példa bemutatja a licenctípus egyetlen virtuális gépeknél
```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Kimenet:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ezzel szemben a hagyományosabb kimeneti a a következő virtuális Géphez nem Azure hibrid juttatás for Windows Server licencelési:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query '[?licenseType==Windows_Server]' -o table
```

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Előfizetés az összes virtuális gépek Azure hibrid juttatás for Windows Server listájában
Telepített Azure hibrid előnye a Windows Server összes virtuális gépek száma és megtekintéséhez az előfizetésből futtathatja a következő parancsot:

### <a name="portal"></a>Portál
A virtuális gépet vagy virtuális gép méretezési készletek erőforráspanelen megtekintheti az összes virtuális gép van és licencelési típus listáját a tartalmazza az "Azure hibrid juttatás" táblaoszlop konfigurálásával. A virtuális gépekre vonatkozó beállításával lehet az "Enabled", "Nem engedélyezett" vagy "Nem támogatott" állapotban.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzureRMVM 
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm list --query '[?licenseType==Windows_Server]' -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>A virtuálisgép-méretezési csoport az Azure hibrid juttatásra, a Windows Server telepítése
A virtuális gépen belüli virtuális gépek méretezési csoportjának Resource Manager-sablonok, egy további paraméter `licenseType` belül a VirtualMachineProfile tulajdonságot meg kell adni. Ehhez létrehozásakor, vagy frissítse a bővített ARM-sablon Powershell, az Azure parancssori felület vagy a REST segítségével állíthatók be.

Az alábbi példa ARM-sablon a Windows Server 2016 Datacenter rendszerképet használ:
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
Is további kapcsolatos [módosítsa a virtuálisgép-méretezési csoport](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) további részleteket a skála frissítése a beállítása.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet az [Azure hibrid előnyökkel pénz mentése](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Tudjon meg többet az [Azure hibrid juttatásra gyakori kérdések](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- További információ [Azure hibrid juttatás a Windows Server licencelési részletes útmutatást](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- További információ [Azure hibrid juttatás for Windows Server és az Azure Site Recovery ellenőrizze áttelepítése alkalmazások az Azure-bA még több költséghatékony](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- További információ [Azure több-Bérlős üzemeltető jogosultsággal rendelkező Windows 10-re](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- További információ [használatával Resource Manager-sablonok](../../azure-resource-manager/resource-group-overview.md)
