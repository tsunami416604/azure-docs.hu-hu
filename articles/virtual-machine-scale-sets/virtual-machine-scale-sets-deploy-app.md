---
title: Alkalmazás üzembe helyezése az Azure virtuálisgép-méretezési csoport |} Microsoft Docs
description: Megtudhatja, hogyan telepíthetők az alkalmazások a Linux és Windows-méretezési csoportban lévő virtuálisgép-példányok
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: bbbe677b0a0d47147ace41ff5a229282f80bbf1b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839515"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>A virtuálisgép-méretezési csoportok az alkalmazás központi telepítése
Ha alkalmazásokat szeretne futtatni egy méretezési csoport virtuálisgép-példányán, először telepítenie kell az alkalmazás összetevőit és szükséges fájljait. Ez a cikk egy egyéni Virtuálisgép-lemezkép összeállítása a terjedő skálán-példány beállítása, vagy automatikusan az install-parancsfájlok futtathatók a meglévő Virtuálisgép-példányok módjai be. Azt is megtudhatja, hogyan alkalmazást vagy az operációs rendszer frissítése érdekében kezelése érdekében egy méretezési.


## <a name="build-a-custom-vm-image"></a>Egyéni VM-lemezkép elkészítése
Használatakor egy Azure platformon lemezképet a méretezési csoportban lévő példányainak létrehozásához további szoftver van telepítve vagy konfigurálva. Automatizálható az ezek az összetevők telepítését azonban, hogy a Virtuálisgép-példányokat a méretezési készlet létrehozásához szükséges idő ad hozzá. A Virtuálisgép-példányok számos konfigurációs módosításokat alkalmazza, ha nincs terhelés az ezekkel a konfigurációs parancsfájlokat és feladatok kezelése.

A konfigurációs felügyeleti és egy virtuális gép kiépítése idő csökkentése érdekében létrehozhat egy egyéni készen áll a futtatásra az alkalmazás, amint egy példány ki van építve a méretezési csoportban lévő Virtuálisgép-lemezkép. További információt a létrehozása, és egy egyéni Virtuálisgép-lemezkép használata a skálával, olvassa el az alábbi oktatóanyagok:

- [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Alkalmazások telepítése és az egyéni parancsprogramok futtatására szolgáló bővítmény
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. További információt a létrehozása, és egy egyéni Virtuálisgép-lemezkép használata a skálával, olvassa el az alábbi oktatóanyagok:

- [Azure CLI 2.0](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-sablon](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Alkalmazások telepítése a Windows PowerShell DSC rendelkező virtuális gépet
[PowerShell kívánt állapot konfigurációs szolgáltatása (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) olyan felügyeleti platform célszámítógépekre konfigurációjának meghatározása. A DSC-konfigurációk határozza meg, mi egy számítógépre telepítéséhez és a gazdagép konfigurálása. A helyi Configuration Manager (LCM) motor dolgozza fel a kért műveletek megnyomott konfigurációk alapján minden cél csomóponton fut.

A PowerShell DSC-bővítmény lehetővé teszi testre szabhatja a PowerShell segítségével állítsa be a skálázási Virtuálisgép-példány. Az alábbi példa:

- Arra utasítja a DSC-csomag letölthető a GitHub - Virtuálisgép-példányok *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- A bővítmény - telepítő parancsfájl futtatásának beállítása `configure-http.ps1`
- A skála állítható be információ lekérése [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- A bővítmény vonatkozik a Virtuálisgép-példányok [frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

A DSC-bővítményt alkalmazott a *myScaleSet* nevű erőforráscsoport Virtuálisgép-példány *myResourceGroup*. Adja meg a saját nevek a következők szerint:

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

Ha a házirend a méretezési csoportban lévő *manuális*, frissítse a Virtuálisgép-példányok a [frissítés-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Ez a parancsmag a Virtuálisgép-példányok a frissített méretezési készlet konfigurációja vonatkozik, és telepíti az alkalmazást.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Alkalmazások telepítése a Linux virtuális gép és felhő inicializálás
A [cloud-init](https://cloudinit.readthedocs.io/latest/) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a cloud-init a kezdeti rendszerindítás során fut, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

További információt, beleértve például *felhő-init.txt* fájl című [felhő inicializálás használni ahhoz, hogy Azure virtuális gépek](../virtual-machines/linux/using-cloud-init.md).

Hozzon létre egy méretezési készletet, és a felhő inicializálás fájl használatát, adja hozzá a `--custom-data` paramétert a [az vmss létrehozása](/cli/azure/vmss#az_vmss_create) parancsot, és adja meg a felhő-inicializációs fájl neve. Az alábbi példakód létrehozza a méretezési készletben elnevezett *myScaleSet* a *myResourceGroup* , és konfigurálja a Virtuálisgép-példányok nevű fájlt tartalmazó *felhő-init.txt*. Adja meg a saját nevek a következők szerint:

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


### <a name="install-applications-with-os-updates"></a>Alkalmazások telepítése az operációs rendszer frissítése érdekében
Ha új operációsrendszer-verziókban áll rendelkezésre, használhatja, vagy új egyéni lemezkép elkészítése és [központi telepítése az operációs rendszer frissítései](virtual-machine-scale-sets-upgrade-scale-set.md) terjedő skálán állítsa be. Minden egyes Virtuálisgép-példány frissítése a legújabb lemezképet történik. Az alkalmazással előre telepített, az egyéni parancsprogramok futtatására szolgáló bővítmény, vagy a PowerShell DSC egyéni lemezkép használatával még az alkalmazás automatikusan elérhető, mert a frissítés végrehajtása. Ez a folyamat győződjön meg arról, hogy nincsenek megszűnik kompatibilitási problémák hajt végre alkalmazás karbantartása tervezése szeretne.

Ha egyéni Virtuálisgép-lemezképet használhat előre telepített alkalmazásokkal rendelkező, az alkalmazás frissítései sikerült integrálja egy központi telepítési folyamatot, az új lemezképeket és központi telepítése az operációs rendszer frissítései között a méretezési szolgáltatást a. Ez a megközelítés lehetővé teszi, hogy a kimenetátirányítási a legfrissebb alkalmazás buildek átvételéhez, hozzon létre, ellenőrizze a Virtuálisgép-lemezképet, majd a méretezési csoportban lévő Virtuálisgép-példányok frissítéséhez. Alapszik, és telepíti az alkalmazás frissítései között egyéni Virtuálisgép-lemezképek központi telepítési folyamat elindításához sikerült [a csomagoló lemezkép létrehozása és telepítése a Visual Studio Team Services](/vsts/pipelines/apps/cd/azure/deploy-azure-scaleset), vagy használjon egy másik platform például [Spinnaker ](https://www.spinnaker.io/) vagy [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>További lépések
Build, és telepítsen alkalmazásokat olyan a méretezési csoportok, tekintse át a [méretezési beállítása kialakítás áttekintése](virtual-machine-scale-sets-design-overview.md). A méretezési kezeléséről további információkért lásd: [a PowerShell szolgáltatás használatával kezelheti a méretezési](virtual-machine-scale-sets-windows-manage.md).
