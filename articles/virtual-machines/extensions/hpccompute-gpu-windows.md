---
title: NVIDIA GPU illesztőprogram-bővítmény – Azure Windows virtuális gépek
description: Microsoft Azure-bővítmény az NVIDIA GPU-illesztőprogramok Telepítéséhez Windows rendszerű N sorozatú számítási virtuális gépeken.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: c388f433327b5328483f10fbef637a6fdfd08832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250542"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>NVIDIA GPU illesztőprogram-bővítmény Windowshoz

## <a name="overview"></a>Áttekintés

Ez a bővítmény az NVIDIA GPU-illesztőprogramokat windows N sorozatú virtuális gépekre telepíti. A virtuálisgép-családtól függően a bővítmény cuda- vagy GRID-illesztőprogramokat telepít. Ha ezzel a kiterjesztéssel telepíti az NVIDIA illesztőprogramokat, elfogadja és elfogadja az [NVIDIA végfelhasználói licencszerződés](https://go.microsoft.com/fwlink/?linkid=874330)feltételeit. A telepítési folyamat során a virtuális gép újraindulhat az illesztőprogram telepítésének befejezéséhez.

Az illesztőprogramok kézi telepítésére és a jelenlegi támogatott verziókra vonatkozó utasítások [itt](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup)találhatók.
Az NVIDIA [GPU-illesztőprogramok Linux N sorozatú virtuális gépekre](hpccompute-gpu-linux.md)történő telepítéséhez is elérhető egy kiterjesztés.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Ez a bővítmény a következő ok-műveleteket támogatja:

| Disztribúció | Verzió |
|---|---|
| Windows 10 | Mag |
| Windows Server 2016 | Mag |
| Windows Server 2012 R2 | Mag |

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Tulajdonságok

| Név | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| közzétevő | Microsoft.HpcCompute | sztring |
| type | NvidiaGpuDriverWindows | sztring |
| typeHandlerVersion | 1.2 | int |


## <a name="deployment"></a>Környezet

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
    "type": "NvidiaGpuDriverWindows",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
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

A bővítmény-végrehajtási kimenet a következő könyvtárba kerül:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Hibakódok

| Hibakód | Jelentés | Lehetséges művelet |
| :---: | --- | --- |
| 0 | A művelet sikeres |
| 1 | A művelet sikeres. Újraindítás szükséges. |
| 100 | A művelet nem támogatott vagy nem hajtható végre. | Lehetséges okok: PowerShell-verzió nem támogatott, a virtuális gép mérete nem N-sorozatú virtuális gép, az adatok letöltésének sikertelen. A hiba okának megállapításához ellenőrizze a naplófájlokat. |
| 240, 840 | A művelet időeltelése. | Próbálkozzon újra a művelettel. |
| -1 | Kivétel történt. | A kivétel okának meghatározásához ellenőrizze a naplófájlokat. |
| -5x | A művelet egy folyamatban lévő újraindítás miatt megszakadt. | Indítsa újra a virtuális gépet. A telepítés újraindítás után folytatódik. Az eltávolítást manuálisan kell meghívni. |


### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/community/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

## <a name="next-steps"></a>További lépések
A bővítményekről a [Virtuálisgép-bővítmények és -szolgáltatások windowsos](features-windows.md)című témakörben talál további információt.

Az N sorozatú virtuális gépekről a [GPU-ra optimalizált virtuális gépméretek](../windows/sizes-gpu.md)című témakörben talál további információt.
