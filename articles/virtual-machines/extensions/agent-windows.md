---
title: Azure-beli virtuálisgép-ügynök – áttekintés |} A Microsoft Docs
description: Azure-beli virtuálisgép-ügynök – áttekintés
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: d17d7c9d7b57e6ca040e4f81c9665789c8bc26e2
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483250"
---
# <a name="azure-virtual-machine-agent-overview"></a>Az Azure virtuálisgép-ügynök – áttekintés
A Microsoft Azure virtuálisgép-ügynök (VM-ügynök) egy olyan biztonságos, egyszerűsített folyamat, amely felügyeli a virtuális gép (VM) az Azure Fabric Controller-szal. A Virtuálisgép-ügynök engedélyezése és az Azure virtuálisgép-bővítmények végrehajtása az elsődleges szerepe van. A Virtuálisgép-bővítmények telepítése és beállítása a szoftver például a virtuális gép üzembe helyezés utáni konfigurációs engedélyezése. A Virtuálisgép-bővítmények rendszergazdai jelszavának visszaállítása egy virtuális gép helyreállítási funkciókat is engedélyezheti. Az Azure Virtuálisgép-ügynök nélkül nem lehet futtatni a Virtuálisgép-bővítmények.

Ez a cikk részletesen, telepítési, észlelési és az Azure virtuálisgép-ügynök eltávolítását.

## <a name="install-the-vm-agent"></a>Virtuálisgép-ügynök telepítése

### <a name="azure-marketplace-image"></a>Az Azure Marketplace-lemezkép

Az Azure Virtuálisgép-ügynök bármely Windows virtuális gépen az Azure Marketplace-lemezképből üzembe helyezett alapértelmezés szerint telepítve van. A portal, PowerShell, parancssori felület vagy az Azure Resource Manager-sablonok az Azure Marketplace-rendszerképpel történő telepítésekor az Azure Virtuálisgép-ügynök is telepítve van.

A Windows vendég ügynök csomag van osztva a két részből áll:

- A kiépítési ügynök (PA)
- Windows-Vendégügynök (WinGA)

Rendelkeznie kell a szolgáltatói cím a virtuális Gépre telepített virtuális gép rendszerindító, azonban a WinGA nem kell telepíteni. A virtuális gép üzembe helyezése, kiválaszthatja, hogy nem telepíti a WinGA. Az alábbi példa bemutatja, hogyan válassza ki a *provisionVmAgent* beállítást az Azure Resource Manager-sablon:

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

Ha nem rendelkezik telepített ügynökkel, nem használhat bizonyos Azure-szolgáltatások, például az Azure Backup vagy az Azure Security. Ezek a szolgáltatások telepítendő bővítmény szükséges. Ha egy virtuális Gépet a WinGA nélkül telepítette, telepítheti később az ügynök legújabb verzióját.

### <a name="manual-installation"></a>Manuális telepítés
A Windows Virtuálisgép-ügynök manuálisan is telepíthető a Windows installer-csomag. Manuális telepítés szükséges lehet az Azure-ban üzembe helyezett egyéni Virtuálisgép-rendszerkép létrehozásakor. A Windows Virtuálisgép-ügynök manuális telepítése [a Virtuálisgép-ügynök telepítőjének letöltéséhez](https://go.microsoft.com/fwlink/?LinkID=394789).

A Virtuálisgép-ügynök a Windows installer-fájl duplán kattintva is telepíthető. A Virtuálisgép-ügynök automatikus vagy felügyelet nélküli telepítéséhez futtassa a következő parancsot:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>A Virtuálisgép-ügynök észlelése

### <a name="powershell"></a>PowerShell

Az Azure Resource Manager PowerShell-modul az Azure virtuális gépek adatainak beolvasásához használható. Egy virtuális Gépet, például az Azure Virtuálisgép-ügynök kiépítési állapota kapcsolatos információk megtekintéséhez használja a [Get-azvm parancsmag](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

A következő sűrített példához kimenetet mutat be a *ProvisionVMAgent* tulajdonság ágyazhatók egymásba *OSProfile*. Ez a tulajdonság segítségével határozza meg, ha a Virtuálisgép-ügynök telepítve van a virtuális géphez:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

A következő parancsfájl segítségével egy virtuális gép neve és a Virtuálisgép-ügynök állapotát az tömör listáját adja vissza:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuális észlelés

Amikor bejelentkezett egy Windows virtuális Gépre, a Feladatkezelő segítségével vizsgálja meg a futó folyamatok. Az Azure Virtuálisgép-ügynök ellenőrzéséhez nyissa meg a Feladatkezelőt, kattintson a *részletek* lapra, és keresse meg a folyamat neve **WindowsAzureGuestAgent.exe**. Ezt a folyamatot jelzi, hogy a Virtuálisgép-ügynök telepítve van-e.


## <a name="upgrade-the-vm-agent"></a>A Virtuálisgép-ügynök frissítése
Az Azure VM Agent a Windows automatikusan frissül. Új virtuális gépeket az Azure-ban van telepítve, mint a legújabb Virtuálisgép-ügynök kapnak, virtuális gép kiépítése időpontban. Egyéni Virtuálisgép-rendszerképek manuálisan frissíteni kell az új Virtuálisgép-ügynök tartalmazza a rendszerkép létrehozásának időpontjában.


## <a name="next-steps"></a>További lépések
Virtuálisgép-bővítményekkel kapcsolatos további információkért lásd: [Azure-beli virtuálisgép-bővítmények és szolgáltatások áttekintése](overview.md).
