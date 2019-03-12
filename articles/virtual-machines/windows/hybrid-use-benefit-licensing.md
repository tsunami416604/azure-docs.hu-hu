---
title: Az Azure Hybrid Benefit Windows Serverhez |} A Microsoft Docs
description: Ismerje meg, hogyan maximalizálhatja a Windows-frissítési garancia arra, hogy a helyszíni-licenceiket áthozzák az Azure-bA
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
ms.openlocfilehash: 64e9350606748116d2eef247790e88ed0d576c3f
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570368"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit Windows Serverhez
A frissítési garanciával rendelkező ügyfelek az Azure Hybrid Benefit a Windows Server lehetővé teszi a helyszíni Windows Server-licenceivel és a Windows virtuális gépek futtatásához az Azure-ban költséghatékony. Az Azure Hybrid Benefit a Windows Server használatával a Windows operációs rendszer új virtuális gépek üzembe helyezése. Ez a cikk halad keresztül lépéseket, hogyan helyezhet üzembe új virtuális gépeket az Azure Hybrid Benefit a Windows Server, és hogy miként frissítheti meglévő a futó virtuális gépek. További információ az Azure Hybrid Benefit a Windows Server licencelési és költséghatékonyan takaríthat meg, tekintse meg a [Azure Hybrid Benefit a Windows Server licencelési oldal](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!Important]
> Minden kétprocesszoros licenc vagy 16 magos licenckészlet két, legfeljebb 8 magos példányra, vagy egy, legfeljebb 16 magos példányra jogosít. A Standard kiadásra szóló licencek esetében az Azure Hybrid Benefit értékelem csak egyszer használható fel: vagy a helyszínen, vagy az Azure-ban, a Datacenter kiadásra szóló licencek esetében viszont párhuzamosan használható a helyszínen és az Azure-ban.
>

> [!Important]
> Az Azure Hybrid Benefit a Windows Server használatával olyan Windows Server operációs rendszert futtató virtuális gépek mostantól használható az összes régióban, beleértve a virtuális gépek további szoftvereket, például az SQL Server-vagy harmadik felek marketplace-szoftver. 
>

> [!NOTE]
> A klasszikus virtuális gépek esetében egyetlen üzembe új virtuális gép, a helyi egyéni rendszerképek használata támogatott. Ez a cikk a támogatott képességek kihasználásához, először át kell klasszikus virtuális gépeket Resource Manager modellre.
>

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Az Azure Hybrid Benefit a Windows Server használatával
Windows virtuális gépek használata az Azure Hybrid Benefit néhány módja van:

1. Telepíthet virtuális gépeket a megadott Windows Server-rendszerképeket az Azure Marketplace-en
2. Egyéni VM feltöltése, és üzembe helyezése a Resource Manager-sablon vagy az Azure PowerShell használatával
3. Váltsa át és átalakíthatja a meglévő virtuális gép futtatása az Azure Hybrid Benefittel között vagy igény szerinti költségek kell fizetnie a Windows Server
4. Virtuálisgép-méretezési csoportban, valamint is alkalmazhat az Azure Hybrid Benefit a Windows Server


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Virtuális gép létrehozása az Azure Hybrid Benefit Windows Serverhez
Azure Hybrid Benefit a Windows Server összes Windows Server operációs rendszer alapú lemezképek támogatottak. Az Azure platform támogatási lemezképeket, vagy saját egyéni Windows Server-rendszerképek feltöltése. 

### <a name="portal"></a>Portál
Virtuális gép létrehozása az Azure Hybrid Benefit a Windows Server, a váltógomb használja a "Pénzt takaríthat meg" szakaszában.

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
A Resource Manager-sablonok, egy további paraméter belül `licenseType` meg kell adni. További információ [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Az Azure Hybrid Benefit a Windows Server egy meglévő virtuális gép átalakítása
Ha rendelkezik egy meglévő virtuális Gépet, amelyet szeretne átalakítása kihasználhatja az Azure Hybrid Benefit a Windows Server, az alábbi utasítások alapján frissítheti a virtuális gép licenc típusa.

> [!NOTE]
> A virtuális gépen a licenctípus módosítása nem okoz a rendszer újraindítása, vagy egy szolgáltatás interuption okozhat.  Egyszerűen egy frissítést, a metaadatok jelző.
> 

### <a name="portal"></a>Portál
Portálról virtuális gép paneljén frissítse a virtuális Gépet az Azure Hybrid Benefit használatához a "Beállítások" lehetőség kiválasztásával és az "Azure hybrid benefit" lehetőség átváltása

### <a name="powershell"></a>PowerShell
- Az Azure Hybrid Benefit a Windows Server meglévő Windows Server rendszerű virtuális gépek átalakítása

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Windows Server rendszerű virtuális gépek konvertálása benefittel vissza a használatalapú fizetés

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>parancssori felület
- Az Azure Hybrid Benefit a Windows Server meglévő Windows Server rendszerű virtuális gépek átalakítása

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>A virtuális gép ellenőrzése a licencek előnyeit okból rendszerbetöltést végrehajtani
Miután telepítette a virtuális Gépeket a PowerShell-lel, Resource Manager-sablon vagy a portálon, a beállítás a következő módszerek ellenőrizheti.

### <a name="portal"></a>Portál
Portálról virtuális gép paneljén a váltógomb megtekintheti az Azure Hybrid Benefit a Windows Server "Konfiguráció" lapot választva.

### <a name="powershell"></a>PowerShell
Az alábbi példa bemutatja a licenc típusa egyetlen virtuális gép
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Kimenet:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ez a kimeneti hagyományosabb és a következő virtuális gép nem az Azure Hybrid Benefit a Windows Server licencelési:
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
> A virtuális gépen a licenctípus módosítása nem okoz a rendszer újraindítása, vagy egy szolgáltatás interuption okozhat. A metaadatok jelző csak licencelési.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Egy adott előfizetés összes virtuális gépet az Azure Hybrid Benefit a Windows Server listázása
Tekintse meg, és az Azure Hybrid Benefit a Windows Server telepített összes virtuális gép száma, az előfizetésből a következő parancsot futtathatja:

### <a name="portal"></a>Portál
A virtuális gépet vagy virtuális gép méretezési csoportok erőforrás panelen megtekintheti a virtuális gép és a licencelési típus listáját tartalmazza az "Azure Hybrid Benefit" táblázatban levő konfigurálásával. A virtuális gép beállítás csak a "Engedélyezve", "Nem engedélyezett" vagy "Nem támogatott" állapot.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>parancssori felület
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Egy virtuálisgép-méretezési csoportot az Azure Hybrid Benefit a Windows Server üzembe helyezése
Belül a virtuálisgép-méretezési csoportot, Resource Manager-sablonok, egy további paraméter `licenseType` belül a VirtualMachineProfile tulajdonságot meg kell adni. Ehhez a létrehozás során vagy a méretezési csoportban az ARM-sablon, PowerShell, az Azure CLI vagy a REST segítségével frissítheti is.

Az alábbi példa egy Windows Server 2016 Datacenter rendszerképet az ARM-sablont használ:
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
Azt is megtudhatja, hogyan [módosítása egy virtuálisgép-méretezési csoportot](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) további lehet frissíteni a méretezési csoport beállítása.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [hogyan lehet pénzt takaríthat meg az Azure Hybrid benefittel](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Tudjon meg többet [gyakran ismételt kérdések az Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Tudjon meg többet [Azure Hybrid Benefit a Windows Server licencelési részletes útmutató](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Tudjon meg többet [Azure Hybrid Benefit a Windows Server és az Azure Site Recovery, hogy áttelepítése alkalmazásokat az Azure-bA még költséghatékonyabban](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Tudjon meg többet [több-Bérlős üzemeltetése jobb az Azure-ban Windows 10-es](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Tudjon meg többet [használatával a Resource Manager-sablonok](../../azure-resource-manager/resource-group-overview.md)
