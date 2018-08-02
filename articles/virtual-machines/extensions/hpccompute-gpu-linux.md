---
title: Illesztőprogram NVIDIA GPU - bővítmény Azure-beli Linuxos virtuális gépek |} A Microsoft Docs
description: A Microsoft Azure-bővítmény telepítse az NVIDIA GPU-illesztőprogramokat N-sorozatú linuxos virtuális gépek számítási.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/03/2018
ms.author: roiyz
ms.openlocfilehash: d95a1b510411f913a05762494dd48d6a5b6f84fd
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413671"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>NVIDIA GPU illesztőprogramjának-bővítmény linuxhoz

## <a name="overview"></a>Áttekintés

Ez a bővítmény Linux N-sorozat virtuális gépei NVIDIA GPU-illesztők telepítése. A Virtuálisgép-család függően a bővítmény telepítését CUDA vagy GRID illesztőprogramok. NVIDIA telepítésekor illesztőprogramokat a bővítmény használatával Ön elfogadja, és elfogadja a NVIDIA végfelhasználói licencszerződés feltételeit. A telepítés során a virtuális gép újraindulhat az illesztőprogramot a telepítés végrehajtásához.

Egy bővítmény is érhető el az NVIDIA GPU-illesztők telepítése [Windows N-sorozat virtuális gépei](hpccompute-gpu-windows.md).

NVIDIA végfelhasználói licencszerződés feltételeit itt találhatók – https://go.microsoft.com/fwlink/?linkid=874330

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő nyílt forráskódú támogatja:

| Disztribúció | Verzió |
|---|---|
| Linux: Ubuntu | 16.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4 |
| Linux: CentOS | 7.3, 7.4 |

### <a name="internet-connectivity"></a>Internetkapcsolat

A Microsoft Azure-bővítmény esetében az NVIDIA GPU-illesztők szükséges, hogy a céloldali virtuális gép csatlakozik az internethez, és rendelkezik hozzáféréssel.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a bővítmény sémáját jeleníti meg.

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
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.HpcCompute | sztring |
| type | NvidiaGpuDriverLinux | sztring |
| typeHandlerVersion | 1.0 | int |


## <a name="deployment"></a>Környezet


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon 

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Sablonok ideálisak, egy vagy több üzembe helyezés utáni konfigurálásra igénylő virtuális gépek üzembe helyezésekor.

Virtuálisgép-bővítmények JSON konfigurációjának a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON konfigurációs elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-manager-template-child-resource.md). 

Az alábbi példa azt feltételezi, hogy a bővítményt a virtuális gép típusú erőforrást van beágyazva. A bővítmény erőforrás beágyazása, ha a JSON az kerül a `"resources": []` objektum a virtuális gép.

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
    "typeHandlerVersion": "1.0",
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-lel és az Azure CLI használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Kilépési kódok

| Kilépési kód | Jelentés | A művelet lehetséges |
| :---: | --- | --- |
| 0 | A művelet sikeres |
| 1 | Helytelen-bővítmény használatát. | Forduljon az ügyfélszolgálathoz a végrehajtási napló kimenetét. |
| 10 | Linux Integration Services, a Hyper-V és az Azure nem érhető el, illetve telepített. | Ellenőrizze a kimenetét lspci. |
| 11 | NVIDIA GPU nem található a Virtuálisgép-méretet. | Használja a [virtuális gép mérete és az operációs rendszer támogatott](../linux/n-series-driver-setup.md). |
| 14 | A művelet sikertelen | |
| 21 | Ubuntu rendszeren nem sikerült frissíteni a | "Sudo apt-get frissítése" négyzet kimeneti |


### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
Bővítmények kapcsolatos további információkért lásd: [virtuális gépi bővítmények és szolgáltatások Linux](features-linux.md).

N-sorozatú virtuális gépekkel kapcsolatos további információkért lásd: [GPU-optimalizált virtuális gépek méretei](../linux/sizes-gpu.md).