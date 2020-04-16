---
title: NVIDIA GPU illesztőprogram-bővítmény – Azure Linux os virtuális gépek
description: Microsoft Azure Extension az NVIDIA GPU-illesztőprogramok Linuxot futtató N sorozatú számítási virtuális gépeken történő telepítéséhez.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: e7f6653043d46925d6a4c35eedaf81224ea6c36d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415790"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU illesztőprogram-bővítmény Linuxra

## <a name="overview"></a>Áttekintés

Ez a bővítmény telepíti az NVIDIA GPU-illesztőprogramokat Linux N sorozatú virtuális gépekre. A virtuálisgép-családtól függően a bővítmény cuda- vagy GRID-illesztőprogramokat telepít. Ha ezzel a kiterjesztéssel telepíti az NVIDIA illesztőprogramokat, elfogadja és elfogadja az [NVIDIA végfelhasználói licencszerződés](https://go.microsoft.com/fwlink/?linkid=874330)feltételeit. A telepítési folyamat során a virtuális gép újraindulhat az illesztőprogram telepítésének befejezéséhez.

Az illesztőprogramok kézi telepítésére és a jelenlegi támogatott verziókra vonatkozó utasítások [itt](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)találhatók.
Az NVIDIA [GPU-illesztőprogramok Windows N sorozatú virtuális gépekre](hpccompute-gpu-windows.md)történő telepítéséhez is elérhető egy bővítmény.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő operációsrendszer-distózisokat támogatja, attól függően, hogy az illesztőprogram támogatja-e az adott operációsrendszer-verziót.

| Disztribúció | Verzió |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

### <a name="internet-connectivity"></a>Internetkapcsolat

A Microsoft Azure-bővítmény NVIDIA GPU-illesztőprogramok megköveteli, hogy a cél virtuális gép csatlakozik az internethez, és hozzáféréssel rendelkezik.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a bővítmény sémáját mutatja.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Tulajdonságok

| Name (Név) | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| közzétevő | Microsoft.HpcCompute | sztring |
| type | NvidiaGpuDriverLinux | sztring |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Beállítások

Minden beállítás nem kötelező. Az alapértelmezett viselkedés az, hogy nem frissíti a kernelt, ha nem szükséges az illesztőprogram telepítéséhez, telepítse a legújabb támogatott illesztőprogramot és a CUDA eszközkészletet (adott esetben).

| Name (Név) | Leírás | Alapértelmezett érték | Érvényes értékek | Adattípus |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Frissítse a rendszermagot, még akkor is, ha nem szükséges az illesztőprogram telepítéséhez | hamis | igaz, hamis | logikai |
| driverVersion | NV: GRID illesztőprogram verziója<br> NC/ND: CUDA eszközkészlet verzió. A kiválasztott CUDA legújabb illesztőprogramjai automatikusan települnek. | legújabb | RÁCS: "430,30", "418,70", "410,92", "410,71", "390,75", "390,57", "390,42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | sztring |
| installCUDA telepítése | Telepítse a CUDA eszközkészletet. Csak nc/ND sorozatú virtuális gépek esetén releváns. | igaz | igaz, hamis | logikai |


## <a name="deployment"></a>Üzembe helyezés


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon 

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. A sablonok ideálisak egy vagy több olyan virtuális gép üzembe helyezéséhez, amelyek üzembe helyezés után konfigurációt igényelnek.

A virtuálisgép-bővítmény JSON-konfigurációja beágyazható a virtuálisgép-erőforrásba, vagy elhelyezhető egy Erőforrás-kezelő JSON-sablon gyökér- vagy legfelső szintjén. A JSON-konfiguráció elhelyezése hatással van az erőforrás nevének és típusának értékére. További információt a [Név és a gyermekerőforrások típusának beállítása](../../azure-resource-manager/resource-manager-template-child-resource.md)című témakörben talál. 

A következő példa feltételezi, hogy a bővítmény a virtuális gép erőforrásba van ágyazva. A bővítmény erőforrás beágyazásakor a `"resources": []` JSON a virtuális gép objektumába kerül.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

A következő példa tükrözi a fenti Azure Resource Manager és a PowerShell példákat, és egyéni beállításokat is hozzáad, például a nem alapértelmezett illesztőprogram-telepítéshez. Pontosabban, frissíti az operációs rendszer kernel és telepíti egy adott CUDA toolkit verziódriver.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.2 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "9.1.85", \
  }'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról, valamint az Azure PowerShell és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény-végrehajtási kimenet a következő fájlba kerül:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kilépési kódok

| Kilépési kód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 0 | A művelet sikeres |
| 1 | A kiterjesztés helytelen használata | Végrehajtási kimeneti napló ellenőrzése |
| 10 | A Linux-integrációs szolgáltatások a Hyper-V-hez és az Azure-hoz nem érhetők el vagy nem telepíthetők | Az lspci kimenetének ellenőrzése |
| 11 | Az NVIDIA GPU nem található ezen a virtuális gépméreten | Támogatott [virtuális gépméret és operációs rendszer használata](../linux/n-series-driver-setup.md) |
| 12 | A képajánlat nem támogatott |
| 13 | A virtuális gép mérete nem támogatott | N sorozatú virtuális gép használata az üzembe helyezéshez |
| 14 | A művelet nem sikerült | Végrehajtási kimeneti napló ellenőrzése |


### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/community/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

## <a name="next-steps"></a>További lépések
A bővítményekről további információt a [Virtuálisgép-bővítmények és -szolgáltatások Linuxhoz](features-linux.md)című témakörben talál.

Az N sorozatú virtuális gépekről a [GPU-ra optimalizált virtuális gépméretek](../linux/sizes-gpu.md)című témakörben talál további információt.
