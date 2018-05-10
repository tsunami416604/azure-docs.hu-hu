---
title: Az Azure virtuális gépek ügynök áttekintése |} Microsoft Docs
description: Az Azure virtuális gép ügynök – áttekintés
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: fb29f0f931715b8a6ba5b4528294eb61ef5762c8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-virtual-machine-agent-overview"></a>Az Azure virtuális gép ügynökének áttekintése
A Microsoft Azure virtuális gép ügynökének (Virtuálisgép-ügynök) rendkívül biztonságos, egyszerű folyamat, amely a virtuális gép (VM) interakcióba az Azure Fabric Controller felügyeli. A Virtuálisgép-ügynök engedélyezése és a végrehajtása az Azure virtuálisgép-bővítmények az elsődleges szerepkör tartozik. Virtuálisgép-bővítmények lehetővé teszik a telepítés utáni konfigurációjának telepítése és beállítása a szoftver például a virtuális gépet. Virtuálisgép-bővítmények is engedélyezheti a helyreállítási szolgáltatás például a virtuális gépek a rendszergazdai jelszó alaphelyzetbe állításával. Az Azure Virtuálisgép-ügynök nélküli Virtuálisgép-bővítmények nem futtatható.

Ez a cikk telepítési, észlelését és az Azure virtuális gép ügynök eltávolításának részletes.

## <a name="install-the-vm-agent"></a>A Virtuálisgép-ügynök telepítése

### <a name="azure-marketplace-image"></a>Kép: Azure piactér

Az Azure Virtuálisgép-ügynök telepítve van minden Azure Piactéri lemezképből telepített Windows virtuális gép alapértelmezés szerint. A portálon, a PowerShell, a parancssori felületen vagy a Azure Resource Manager-sablonok az Azure piactér lemezképének központi telepítésekor az Azure Virtuálisgép-ügynök is telepítve van.

A Windows vendég ügynök csomag rendszer darabolja fel két részből áll:

- Kiépítési ügynök (PA)
- Windows-Vendégügynök (WinGA)

Rendelkeznie kell a szolgáltatói a virtuális Gépen telepített virtuális gép rendszerindító, azonban a WinGA nem kell telepíteni. A virtuális gép idő, a rendszer kiválaszthatja, hogy nem telepíti a WinGA. A következő példa bemutatja, hogyan válassza ki a *provisionVmAgent* lehetőséget az Azure Resource Manager-sablonokban:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Ha nincs telepítve ügynök, egy Azure-szolgáltatások, például az Azure biztonsági mentési vagy Azure biztonsági nem használható. Ezek a szolgáltatások szükséges kiterjesztéssel kell telepíteni. Ha egy virtuális Gépet a WinGA nélkül telepített, akkor telepítheti később az ügynök legújabb verzióját.

### <a name="manual-installation"></a>Manuális telepítés
A Windows Virtuálisgép-ügynök manuálisan telepíthető egy Windows installer csomaggal. Manuális telepítés akkor lehet szükség, amikor létrehoz egy egyéni Virtuálisgép-lemezkép, amelyek az Azure rendszerbe. Manuálisan kell telepítenie a Windows Virtuálisgép-ügynök [töltse le az ügynököt telepítő](http://go.microsoft.com/fwlink/?LinkID=394789).

A Virtuálisgép-ügynök a Windows installer fájlra duplán kattintva is telepíthető. A Virtuálisgép-ügynök automatikus vagy felügyelet nélküli telepítésének futtassa a következő parancsot:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>A Virtuálisgép-ügynök észlelése

### <a name="powershell"></a>PowerShell

Az Azure Resource Manager PowerShell modul Azure virtuális gépek adatainak lekérésére használható. Egy virtuális Gépet, például a telepítés állapotát az Azure Virtuálisgép-ügynök, az információt használja [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm):

a(z) "powershell" Get-AzureRmVM
```

The following condensed example output shows the the *ProvisionVMAgent* property nested inside *OSProfile*. This property can be used to determine if the VM agent has been deployed to the VM:

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

A következő parancsfájl segítségével a virtuális gép nevét és a Virtuálisgép-ügynök állapota tömör listáját adja vissza:

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuális észlelés
Ha jelentkezett be a Windows Azure virtuális Gépen, a Feladatkezelő segítségével vizsgálja meg a futó folyamatok. Az Azure virtuális gép ügynököt ellenőrzéséhez nyissa meg a Feladatkezelőt, kattintson a *részletek* lapot, és keresse meg a folyamat nevét **WindowsAzureGuestAgent.exe**. A folyamat jelzi, hogy a Virtuálisgép-ügynök telepítve van-e.


## <a name="upgrade-the-vm-agent"></a>A Virtuálisgép-ügynök frissítése
Az Azure virtuális gép ügynök a Windows a rendszer automatikusan frissíti. Új virtuális gépek vannak telepítve az Azure-ba, mert a legújabb ügynököt kapott virtuális gép kiépítése során. Egyéni Virtuálisgép-rendszerképek tartalmazza az új Virtuálisgép-ügynök kép létrehozáskor manuálisan frissíteni kell.


## <a name="next-steps"></a>További lépések
További információ a Virtuálisgép-bővítmények: [Azure virtuális gép bővítményeket és szolgáltatásokat – áttekintés](overview.md).