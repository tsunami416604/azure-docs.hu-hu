---
title: "Alkalmazás üzembe helyezése az Azure virtuálisgép-méretezési csoport |} Microsoft Docs"
description: "Megtudhatja, hogyan telepíthetők az alkalmazások a Linux és Windows-méretezési csoportban lévő virtuálisgép-példányok"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 288bcdf6628f60d0b08fe151e630784d665db56f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>A virtuálisgép-méretezési csoportok az alkalmazás központi telepítése
A virtuális gép (VM)-példányok méretezési csoportban lévő alkalmazások futtatásához, először telepítse az alkalmazás-összetevők és a szükséges fájlokat. Ez a cikk egy egyéni Virtuálisgép-lemezkép összeállítása a terjedő skálán-példány beállítása, vagy automatikusan az install-parancsfájlok futtathatók a meglévő Virtuálisgép-példányok módjai be. Azt is megtudhatja, hogyan alkalmazást vagy az operációs rendszer frissítése érdekében kezelése érdekében egy méretezési.


## <a name="build-a-custom-vm-image"></a>Egyéni VM-lemezkép elkészítése
Használatakor egy Azure platformon lemezképet a méretezési csoportban lévő példányainak létrehozásához további szoftver van telepítve vagy konfigurálva. Automatizálható az ezek az összetevők telepítését azonban, hogy a Virtuálisgép-példányokat a méretezési készlet létrehozásához szükséges idő ad hozzá. A Virtuálisgép-példányok számos konfigurációs módosításokat alkalmazza, ha nincs terhelés az ezekkel a konfigurációs parancsfájlokat és feladatok kezelése.

A konfigurációs felügyeleti és egy virtuális gép kiépítése idő csökkentése érdekében létrehozhat egy egyéni készen áll a futtatásra az alkalmazás, amint egy példány ki van építve a méretezési csoportban lévő Virtuálisgép-lemezkép. A teljes folyamat set példányok bővített egyéni Virtuálisgép-lemezkép létrehozásához a következők:

1. Hozhat létre egy egyéni Virtuálisgép-lemezkép a bővített set példányok, akkor hozzon létre és jelentkezzen be egy virtuális Gépet, majd telepítse és konfigurálja az alkalmazás. Csomagoló segítségével határozza meg, és hozhat létre egy [Linux](../virtual-machines/linux/build-image-with-packer.md) vagy [Windows](../virtual-machines/windows/build-image-with-packer.md) Virtuálisgép-lemezkép. Vagy manuálisan hozhat létre, és a virtuális gép konfigurálása:

    - A Linux virtuális gép létrehozása a [Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md), [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md), vagy a [portal](../virtual-machines/linux/quick-create-portal.md).
    - A Windows virtuális gép létrehozása a [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md), a [Azure CLI 2.0](../virtual-machines/windows/quick-create-cli.md), vagy a [portal](../virtual-machines/windows/quick-create-portal.md).
    - Jelentkezzen be egy [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) vagy [Windows](../virtual-machines/windows/connect-logon.md) virtuális gép.
    - Telepítse és konfigurálja az alkalmazások és eszközök szükséges. Szalagtár vagy futásidejű verzióról van szüksége, ha egy egyéni Virtuálisgép-lemezkép megadhatja egy verziót és 

2. Az a virtuális gép rögzítése a [Azure CLI 2.0](../virtual-machines/linux/capture-image.md) vagy [Azure PowerShell](../virtual-machines/windows/capture-image.md). Ebben a lépésben létrehozza a egyéni Virtuálisgép-lemezkép, amellyel majd a méretezési csoportban lévő példányok telepítése.

3. [Hozzon létre egy méretezési](virtual-machine-scale-sets-create.md) és adja meg az előző lépésekben létrehozott egyéni VM lemezképfájlt.


## <a name="already-provisioned"></a>Alkalmazások telepítése és az egyéni parancsprogramok futtatására szolgáló bővítmény
Az egyéni parancsprogramok futtatására szolgáló bővítmény és hajtanak végre a parancsfájlok Azure virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében.

Az egyéni parancsprogramok futtatására szolgáló bővítmény integrálódik az Azure Resource Manager-sablonok, és is futtathat az Azure parancssori felület, PowerShell, Azure-portálon vagy az Azure virtuális gép REST API használatával. 

További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/windows/extensions-customscript.md).


### <a name="use-azure-powershell"></a>Azure PowerShell használatával
PowerShell használ egy kivonattáblát letölteni a fájlt, és a végrehajtandó parancs. Az alábbi példa:

- Arra utasítja a Virtuálisgép-példányok parancsfájl letölthető a GitHub - *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- A bővítmény - telepítő parancsfájl futtatásának beállítása`powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- A skála állítható be információ lekérése [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- A bővítmény vonatkozik a Virtuálisgép-példányok [frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

Az egyéni parancsprogramok futtatására szolgáló bővítmény alkalmazott a *myScaleSet* nevű erőforráscsoport Virtuálisgép-példány *myResourceGroup*. Adja meg a saját nevek a következők szerint:

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Ha a házirend a méretezési csoportban lévő *manuális*, frissítse a Virtuálisgép-példányok a [frissítés-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Ez a parancsmag a Virtuálisgép-példányok a frissített méretezési készlet konfigurációja vonatkozik, és telepíti az alkalmazást.


### <a name="use-azure-cli-20"></a>Use Azure CLI 2.0
Az Azure parancssori felület segítségével az egyéni parancsprogramok futtatására szolgáló bővítmény használatához hozzon létre egy JSON-fájl, amely meghatározza, milyen fájlok beszerzése és végrehajtandó, parancsokat. Ezek a JSON-definíciók ismételten használhatók méretű készlet telepítések alkalmazni a konzisztens alkalmazás telepítését.

Hozzon létre egy fájlt az aktuális rendszerhéjban *customConfig.json* , majd illessze be a következő konfigurációt. Például hozza létre a fájlt a Cloud Shellben, és ne a helyi gépén. A szerkesztő kívánja használata. Írja be a `sensible-editor cloudConfig.json` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Az egyéni parancsprogramok futtatására szolgáló bővítmény konfiguráció alkalmazása a skála beállítani a Virtuálisgép-példány [az vmss bővítmény set](/cli/azure/vmss/extension#az_vmss_extension_set). A következő példa érvényes a *customConfig.json* konfigurációját, és a *myScaleSet* nevű erőforráscsoport Virtuálisgép-példány *myResourceGroup*. Adja meg a saját nevek a következők szerint:

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

Ha a házirend a méretezési csoportban lévő *manuális*, frissítse a Virtuálisgép-példányok a [az vmss frissítés-példányok](/cli/azure/vmss#update-instances). Ez a parancsmag a Virtuálisgép-példányok a frissített méretezési készlet konfigurációja vonatkozik, és telepíti az alkalmazást.


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Alkalmazások telepítése a Windows PowerShell DSC rendelkező virtuális gépet
[PowerShell kívánt állapot konfigurációs szolgáltatása (DSC)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) olyan felügyeleti platform célszámítógépekre konfigurációjának meghatározása. A DSC-konfigurációk határozza meg, mi egy számítógépre telepítéséhez és a gazdagép konfigurálása. A helyi Configuration Manager (LCM) motor dolgozza fel a kért műveletek megnyomott konfigurációk alapján minden cél csomóponton fut.

A PowerShell DSC-bővítmény lehetővé teszi testre szabhatja a PowerShell segítségével állítsa be a skálázási Virtuálisgép-példány. Az alábbi példa:

- Arra utasítja a Virtuálisgép-példányok DSC csomag letölthető a GitHub - *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- A bővítmény - telepítő parancsfájl futtatásának beállítása`configure-http.ps1`
- A skála állítható be információ lekérése [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- A bővítmény vonatkozik a Virtuálisgép-példányok [frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

A DSC-bővítményt alkalmazott a *myScaleSet* nevű erőforráscsoport Virtuálisgép-példány *myResourceGroup*. Adja meg a saját nevek a következők szerint:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
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

Felhő inicializálás terjesztéseket is használható. Például, hogy ne használjon **apt-get-telepítés** vagy **yum telepítése** csomag telepítéséhez. Helyette megadhatja a telepítendő csomagok listája. Felhő inicializálás automatikusan használja a natív csomag felügyeleti eszköz a distro választja.

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


## <a name="install-applications-as-a-set-scales-out"></a>Alkalmazások telepítéséhez, egy kimenő méretezi
Méretezési csoportok lehetővé teszik az alkalmazást futtató Virtuálisgép-példányok számának növeléséhez. A folyamat kibővítési manuálisan elindítani, vagy automatikusan például CPU és memória-használati metrikák alapján.

A méretezési alkalmazott egy egyéni parancsprogramok futtatására szolgáló bővítmény, az alkalmazás telepítésének új Virtuálisgép-példányokhoz csatlakoznak. Ha a méretezési előre telepített alkalmazásokkal rendelkező egyéni lemezkép alapul, minden új Virtuálisgép-példány használható állapotban van telepítve. 

Tároló állomások a méretezési készlet Virtuálisgép-példányok esetén az egyéni parancsprogramok futtatására szolgáló bővítmény segítségével lekéréses és futtatásához szükséges tároló képek. Az egyéni parancsprogramok futtatására szolgáló bővítmény az új Virtuálisgép-példány is regisztrálása az orchestrator, például az Azure Tárolószolgáltatás.


## <a name="deploy-application-updates"></a>Alkalmazás-frissítések központi telepítéséhez
Az alkalmazás kódja, a szalagtárak, vagy a csomagok frissítésekor a Virtuálisgép-példányok méretezési csoportban lévő tolható ki a legfrissebb alkalmazás állapotát. Ha az egyéni parancsprogramok futtatására szolgáló bővítmény, az alkalmazás frissítései használja, és nem automatikusan telepíti. Módosítsa a egyéni parancsprogram konfigurációját, többek között úgy, hogy egy telepítési parancsfájlt, amely a frissített verzió neve mutasson. Az előző példában az egyéni parancsprogramok futtatására szolgáló bővítmény parancsfájlokra nevű *automate_nginx.sh* az alábbiak szerint:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Minden olyan frissítés, megteheti, hogy az alkalmazás nem érhetők az egyéni parancsprogramok futtatására szolgáló bővítmény, amely telepíti a parancsfájl módosításokat. Egy megoldás, az alkalmazással lépésekben kiadott verziószámát tartalmazza. Az egyéni parancsprogramok futtatására szolgáló bővítmény most hivatkozhat *automate_nginx_v2.sh* az alábbiak szerint:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Az egyéni parancsprogramok futtatására szolgáló bővítmény már fut a Virtuálisgép-példányok a legfrissebb alkalmazás-frissítések alkalmazásához.


### <a name="install-applications-with-os-updates"></a>Alkalmazások telepítése az operációs rendszer frissítése érdekében
Ha új operációsrendszer-verziókban áll rendelkezésre, használhatja, vagy új egyéni lemezkép elkészítése és [központi telepítése az operációs rendszer frissítései](virtual-machine-scale-sets-upgrade-scale-set.md) terjedő skálán állítsa be. Minden egyes Virtuálisgép-példány frissítése a legújabb lemezképet történik. Az alkalmazással előre telepített, az egyéni parancsprogramok futtatására szolgáló bővítmény, vagy a PowerShell DSC egyéni lemezkép használatával még az alkalmazás automatikusan elérhető, mert a frissítés végrehajtása. Ez a folyamat győződjön meg arról, hogy nincsenek megszűnik kompatibilitási problémák hajt végre alkalmazás karbantartása tervezése szeretne.

Ha egyéni Virtuálisgép-lemezképet használhat előre telepített alkalmazásokkal rendelkező, az alkalmazás frissítései sikerült integrálja egy központi telepítési folyamatot, az új lemezképeket és központi telepítése az operációs rendszer frissítései között a méretezési szolgáltatást a. Ez a megközelítés lehetővé teszi, hogy a kimenetátirányítási a legfrissebb alkalmazás buildek átvételéhez, hozzon létre, ellenőrizze a Virtuálisgép-lemezképet, majd a méretezési csoportban lévő Virtuálisgép-példányok frissítéséhez. Alapszik, és telepíti az alkalmazás frissítései között egyéni Virtuálisgép-lemezképek központi telepítési folyamat futtatásához használhatja [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Spinnaker](https://www.spinnaker.io/), vagy [Jenkins](https://jenkins.io/) .


## <a name="next-steps"></a>További lépések
Build, és telepítsen alkalmazásokat olyan a méretezési csoportok, tekintse át a [méretezési beállítása kialakítás áttekintése](virtual-machine-scale-sets-design-overview.md). A méretezési kezeléséről további információkért lásd: [a PowerShell szolgáltatás használatával kezelheti a méretezési](virtual-machine-scale-sets-windows-manage.md).
