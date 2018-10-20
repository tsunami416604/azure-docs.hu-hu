---
title: Alkalmazás üzembe helyezése egy Azure-beli virtuálisgép-méretezési csoportban |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthet alkalmazásokat a Linux és Windows méretezési csoportban lévő virtuálisgép-példányok
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: zarhoads
ms.openlocfilehash: 22e035be27f16e7b73e545d75eb9cd108a919114
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471203"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>A virtual machine scale sets az alkalmazás üzembe helyezése
Ha alkalmazásokat szeretne futtatni egy méretezési csoport virtuálisgép-példányán, először telepítenie kell az alkalmazás összetevőit és szükséges fájljait. Ez a cikk bemutatja a módon hozhat létre egyéni Virtuálisgép-rendszerképet a méretezési csoportban lévő példányok állítsa be, vagy már meglévő Virtuálisgép-példányok automatikus futtatásra telepítési szkriptek. Azt is megtudhatja, hogyan kezelhetők alkalmazás vagy az operációs rendszer frissítése egy méretezési csoportot.


## <a name="build-a-custom-vm-image"></a>Egyéni Virtuálisgép-rendszerkép létrehozása
Használatakor az Azure-platform-rendszerképeket a példányok létrehozásához a méretezési csoportban lévő, a további szoftver van telepítve vagy konfigurálva. Automatizálhatja az összetevők telepítésének azonban, amely hozzáadja a méretezési csoportok Virtuálisgép-példányok létrehozásához szükséges időt. Számos konfigurációs módosítások alkalmaz a Virtuálisgép-példányok, van-e felügyeleti többletterhelést az ezekkel a konfigurációs szkripteket és feladatokat.

A konfigurációkezelés és egy virtuális gép idő csökkentése érdekében létrehozhat egy egyéni Virtuálisgép-rendszerképet, amely készen áll az alkalmazás futtatásához, amint egy példány üzembe van helyezve a méretezési csoportban. További információ a létrehozása és a egy egyéni Virtuálisgép-rendszerkép használata egy méretezési csoportban, olvassa el a következő oktatóanyagokat:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Az egyéni Szkriptbővítménnyel és alkalmazások telepítése
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. További információ a létrehozása és a egy egyéni Virtuálisgép-rendszerkép használata egy méretezési csoportban, olvassa el a következő oktatóanyagokat:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-sablon](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Az egy virtuális Gépet a PowerShell DSC Windows telepíthet egy alkalmazást
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) egy felügyeleti platform cél gépek konfigurációjának meghatározása. DSC-konfigurációk határozza meg, mi a telepíti egy számítógépre, és a gazdagép konfigurálása. A helyi Configuration Manager (LCM) Konfigurálása motor dolgozza fel a kért műveletek leküldött beállításai minden célként megadott csomóponton fut.

A PowerShell DSC bővítmény lehetővé teszi, hogy a PowerShell-lel méretezési csoportban lévő Virtuálisgép-példányok testreszabását. Az alábbi példában:

- Arra utasítja a DSC-csomag letöltése a Githubról – a Virtuálisgép-példányok *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Beállítja a futtatásához egy telepítési parancsfájl - bővítmény `configure-http.ps1`
- Egy méretezési csoportot az információ lekérése [a Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Vonatkozik a bővítményt a Virtuálisgép-példányok [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

A DSC-bővítmény alkalmazott a *myScaleSet* nevű erőforráscsoportot a Virtuálisgép-példányok *myResourceGroup*. Adja meg a saját nevek a következők szerint:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Ha a házirend a méretezési *manuális*, a Virtuálisgép-példányok a frissítés [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Ez a parancsmag a Virtuálisgép-példányok a frissített méretezésicsoport-konfigurációt vonatkozik, és telepíti az alkalmazást.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Alkalmazások telepítése egy Linux rendszerű virtuális gépre, a cloud-Init használatával
A [cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a cloud-init a kezdeti rendszerindítás során fut, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

További információkat, többek között például *cloud-init.txt* fájlt [cloud-init használata Azure virtuális gépek testreszabása](../virtual-machines/linux/using-cloud-init.md).

Hozzon létre egy méretezési csoportot, és a egy cloud-init-fájlt használ, adja hozzá a `--custom-data` paramétert a [az vmss létrehozásához](/cli/azure/vmss#az_vmss_create) parancsot, majd adja meg a cloud-init fájl nevét. A következő példában létrehozunk egy méretezési csoportot elnevezett *myScaleSet* a *myResourceGroup* konfigurálja a Virtuálisgép-példányok nevű fájlt, és *cloud-init.txt*. Adja meg a saját nevek a következők szerint:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Alkalmazások telepítése az operációs rendszer frissítése
Új operációsrendszer-verziók érhetők el, amikor használja, vagy egy új egyéni rendszerkép összeállítása és [üzembe helyezése operációs rendszer verziófrissítései](virtual-machine-scale-sets-upgrade-scale-set.md) beállítani egy méretezési csoport. Minden egyes Virtuálisgép-példány frissítése a legújabb lemezképet történik. Az alkalmazással előre telepítve van, az egyéni szkriptek futtatására szolgáló bővítmény, vagy a PowerShell DSC egyéni rendszerkép használatával rendelkezik az alkalmazás automatikusan elérhető, a frissítés végrehajtása közben. Szükség lehet tervezése alkalmazás karbantartás végrehajtása közben, győződjön meg arról, hogy nincs kompatibilitási problémák a folyamat.

Az előre telepített alkalmazás egy egyéni Virtuálisgép-rendszerképet használja, ha meg az alkalmazás-frissítések egy központi telepítési folyamat az új rendszerképek létrehozása és üzembe helyezése operációs rendszer verziófrissítései a teljes méretezési és integrálhatja. Ez a megközelítés lehetővé teszi, hogy a folyamat folytattuk a munkát a legfrissebb alkalmazás-buildekről, hozzon létre, ellenőrizze a Virtuálisgép-rendszerkép, majd frissítse a méretezési csoportban lévő Virtuálisgép-példányok. Egy központi telepítési folyamatot, amely felépítése és üzembe helyezése az alkalmazás frissítései között egyéni Virtuálisgép-rendszerképek futtatásához sikerült [Packer lemezkép létrehozása és üzembe helyezése az Azure DevOps-szolgáltatásokkal](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), vagy használjon egy másik platformot például [Spinnaker](https://www.spinnaker.io/) vagy [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>További lépések
Hozhat létre és telepíthet központilag alkalmazásokat a méretezési csoportok, áttekintheti a [méretezési beállítása tervezési áttekintése](virtual-machine-scale-sets-design-overview.md). A méretezési kezeléséről további információkért lásd: [PowerShell-lel történő kezelése a méretezési](virtual-machine-scale-sets-windows-manage.md).
