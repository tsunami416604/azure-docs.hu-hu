---
title: Alkalmazás üzembe helyezése egy Azure virtuálisgép-méretezési csoportba
description: Útmutató az alkalmazások Linux- és Windows-alapú virtuálisgép-példányokra történő központi telepítéséről méretezési csoportban
author: ju-shim
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: e157db79f1835a8ca891dd360a99e3319565d1d4
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011463"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Alkalmazások üzembe helyezése virtuálisgép-méretezési csoportokon

Ha alkalmazásokat szeretne futtatni egy méretezési csoport virtuálisgép-példányán, először telepítenie kell az alkalmazás összetevőit és szükséges fájljait. Ez a cikk bemutatja, hogyan hozhat létre egy egyéni virtuálisgép-lemezképet egy méretezési csoportban, vagy automatikusan futtathatja a telepítési parancsfájlokat a meglévő virtuálisgép-példányokon. Azt is megtudhatja, hogyan kezelheti az alkalmazások vagy az operációs rendszer frissítéseit egy méretezési készletben.


## <a name="build-a-custom-vm-image"></a>Egyéni virtuálisgép-lemezkép létrehozása
Ha az Azure platform lemezképeinek egyikét használja a méretezési csoport példányainak létrehozásához, nincs további szoftver telepítve vagy konfigurálva. Automatizálhatja ezeknek az összetevőknek a telepítését, azonban ez növeli a virtuálisgép-példányok a méretezési csoportok kiépítése szükséges időt. Ha sok konfigurációs módosítást alkalmaz a virtuálisgép-példányokra, a konfigurációs parancsfájlok és feladatok felügyeleti többletterheléssel.

A konfigurációs felügyelet csökkentése és a virtuális gép kiépítéséhez szükséges idő csökkentése érdekében létrehozhat egy egyéni virtuálisgép-lemezképet, amely készen áll az alkalmazás futtatására, amint egy példány ki van építve a méretezési csoportban. Az egyéni virtuálisgép-lemezkép méretezési készlettel történő létrehozásáról és használatáról az alábbi oktatóanyagokban talál további információt:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Alkalmazás telepítése az egyéni parancsfájl-bővítménylel
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében. Az egyéni parancsfájl-bővítményt kiterjesztő alkalmazások telepítéséről az alábbi útmutatókban talál további információt:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-sablon](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Alkalmazás telepítése PowerShell-dsc-vel rendelkező Windows virtuális gépre
[PowerShell kívánt állapotkonfiguráció (DSC)](/powershell/scripting/dsc/overview/overview) egy felügyeleti platform a célgépek konfigurációjának meghatározásához. A DSC-konfigurációk határozzák meg, hogy mit telepítsenek a számítógépre, és hogyan konfigurálják az állomást. A helyi configuration manager (LCM) motor fut minden célcsomóponton, amely a kért műveleteket a leadott konfigurációk alapján dolgozza fel.

A PowerShell DSC-bővítmény lehetővé teszi a virtuálisgép-példányok testreszabását a PowerShell méretezési csoportban. A következő példa:

- Arra utasítja a virtuálisgép-példányokat, hogy töltsenek le egy DSC-csomagot a GitHubról –*https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Beállítja a bővítményt egy telepítési parancsfájl futtatásához -`configure-http.ps1`
- Információ beszerez egy méretezési csoportról a [Get-AzVmss segítségével](/powershell/module/az.compute/get-azvmss)
- A bővítmény alkalmazása a [virtuálisgép-példányokra az Update-AzVmss](/powershell/module/az.compute/update-azvmss) segítségével

A DSC-bővítmény a *myResourceGroup*nevű erőforráscsoport *myScaleSet* virtuálisgép-példányaira lesz alkalmazva. Írja be saját nevét az alábbiak szerint:

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
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Ha a méretezési csoport frissítési házirendje *manuális,* frissítse a [virtuálisgép-példányokat az Update-AzVmssInstance segítségével.](/powershell/module/az.compute/update-azvmssinstance) Ez a parancsmag alkalmazza a frissített méretezési csoport konfigurációját a virtuális gép példányaira, és telepíti az alkalmazást.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Alkalmazás telepítése linuxos virtuális gépre felhőalapú telepítéssel
A [cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a cloud-init a kezdeti rendszerindítás során fut, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

További információért, beleértve egy példa *felhő-init.txt* fájlt, [lásd: A felhő-init testreszabásához Azure virtuális gépek.](../virtual-machines/linux/using-cloud-init.md)

Méretezési csoport létrehozásához és egy felhő-init `--custom-data` fájl használatához adja hozzá a paramétert az [az vmss create](/cli/azure/vmss) parancshoz, és adja meg egy felhőalapú init fájl nevét. A következő példa létrehoz egy *myScaleSet* nevű méretezési csoportot a *myResourceGroup* ban, és a virtuálisgép-példányokat *egy cloud-init.txt*nevű fájllal konfigurálja. Írja be saját nevét az alábbiak szerint:

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


### <a name="install-applications-with-os-updates"></a>Alkalmazások telepítése operációsrendszer-frissítésekkel
Amikor új operációsrendszer-kiadások érhetők el, használhat vagy hozhat létre egy új egyéni lemezképet, és [telepítheti az operációs rendszer frissítéseit](virtual-machine-scale-sets-upgrade-scale-set.md) egy méretezési készletre. Minden virtuálisgép-példány frissül a legújabb, megadott lemezképre. Egyéni lemezképet használhat az előre telepített alkalmazással, az egyéni parancsfájl-bővítménysel vagy a PowerShell DSC-vel, hogy az alkalmazás automatikusan elérhető legyen a frissítés végrehajtásakor. Előfordulhat, hogy a folyamat végrehajtása során meg kell terveznie az alkalmazások karbantartását, hogy megbizonyosodjon arról, hogy nincsenek verziókompatibilitási problémák.

Ha egy egyéni virtuálisgép-lemezképet használ az előre telepített alkalmazással, integrálhatja az alkalmazásfrissítéseket egy központi telepítési folyamattal az új lemezképek létrehozásához és az operációs rendszer frissítéseinek központi telepítéséhez a méretezési készletben. Ez a megközelítés lehetővé teszi, hogy a folyamat vegye fel a legújabb alkalmazás-buildek, hozzon létre és érvényesítse a virtuálisgép-lemezképet, majd frissítse a virtuálisgép-példányok a méretezési csoportban. Ha olyan telepítési folyamatot szeretne futtatni, amely egyéni virtuálisgép-lemezképekközött építi fel és telepíti az alkalmazásfrissítéseket, [létrehozhat egy Packer-lemezképet, és üzembe helyezheti az Azure DevOps-szolgáltatásokkal,](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)vagy használhat egy másik platformot, például [a Spinnakert](https://www.spinnaker.io/) vagy [a Jenkinst.](https://jenkins.io/)


## <a name="next-steps"></a>További lépések
Amikor alkalmazásokat hoz létre és telepít a méretezési csoportokra, áttekintheti a [Méretezési készlet tervének áttekintését.](virtual-machine-scale-sets-design-overview.md) A méretezési csoport kezeléséről a [PowerShell használata a méretezési csoport kezeléséhez című](virtual-machine-scale-sets-windows-manage.md)témakörben talál további információt.
