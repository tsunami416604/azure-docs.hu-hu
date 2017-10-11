---
title: "A virtuálisgép-méretezési csoportban lévő virtuális gépek kezeléséhez |} Microsoft Docs"
description: "A virtuálisgép-méretezési beállítása az Azure PowerShell használatával a virtuális gépek kezeléséhez."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.openlocfilehash: d09a020b903e5f43afe03b86c675bcc1eb536cbc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoportban lévő virtuális gépek kezelése
A virtuálisgép-méretezési csoportban lévő virtuális gépek kezeléséhez használja a cikkben a feladatokat.

A virtuálisgép-méretezési csoportban lévő kezelése a feladatokról a legtöbb szükséges, hogy ismeri-e a Példányazonosító a kezelni kívánt számítógép. Használhat [Azure erőforrás-kezelő](https://resources.azure.com) méretezési csoportban lévő virtuális gép Példányazonosítója kereséséhez. Az erőforrás-kezelő által végzett feladatok állapotának ellenőrzéséhez is használni.

Az Azure PowerShell legfrissebb verziójának telepítésével, a kívánt előfizetés kiválasztásával és a fiókjába való bejelentkezéssel kapcsolatos információkért lásd: [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása).

## <a name="display-information-about-a-scale-set"></a>A méretezési kapcsolatos információk megjelenítése
A méretezési csoport, amely a példányait tartalmazó nézetet is nevezzük kapcsolatos általános információkat kaphat. Másik lehetőségként kaphat konkrétabb információkat, például a méretezési csoportban lévő erőforrások adatait.

Az ajánlatban szereplő értékek cserélje le a nevét vagy az erőforráscsoport és állítsa be, és futtassa a parancsot:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Ehhez hasonló adja vissza:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

Az ajánlatban szereplő értékek cserélje le a erőforrás csoport és a méretezési készlet nevét. Cserélje le  *#*  a virtuális gép, amelyet szeretne információ jelenik meg, és futtassa a példány azonosítója:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Ez a példa hasonlót adja vissza:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## <a name="start-a-virtual-machine-in-a-scale-set"></a>Indítsa el a virtuálisgép-méretezési csoportban lévő
Az ajánlatban szereplő értékek cserélje le a erőforrás csoport és a méretezési készlet nevét. Cserélje le  *#*  a virtuális gép, amelyet szeretne elindítani, és futtassa az azonosítóval:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Az erőforrás-kezelőben, láthatja, hogy a példány állapota **futtató**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

A méretezési készletben - InstanceId paraméter nem használatával is elindítható a virtuális gépeket.

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Méretezési csoportban lévő virtuális gép leállítása
Az ajánlatban szereplő értékek cserélje le a erőforrás csoport és a méretezési készlet nevét. Cserélje le  *#*  a virtuális gép leállítása, és futtassa kívánt azonosítóval:

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Az erőforrás-kezelőben, láthatja, hogy a példány állapota **felszabadítása**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

Állítsa le a virtuális gépet, és nem felszabadítani, használja a - StayProvisioned paramétert. A készlet összes virtuális gépet a - InstanceId paraméter nem segítségével állíthatók le.

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Indítsa újra a virtuálisgép-méretezési csoportban lévő
Az ajánlatban szereplő értékek cserélje le az erőforráscsoport és a méretezési csoport nevét. Cserélje le  *#*  a virtuális gép, amelyet szeretne újraindítani, és futtassa az azonosítóval:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

A készlet összes virtuális gépet a - InstanceId paraméter nem használatával indíthatja újra.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>A virtuális gép eltávolítása egy méretezési csoport
Az ajánlatban szereplő értékek cserélje le az erőforráscsoport és a méretezési csoport nevét. Cserélje le  *#*  a virtuális gép, amelyet szeretne eltávolítani, és futtassa az azonosítóval:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

A virtuális gép méretezési egyszerre is eltávolíthat, ha nem használja a - InstanceId paraméter.

## <a name="change-the-capacity-of-a-scale-set"></a>A kapacitás, a méretezési módosítása
Adja hozzá, vagy távolítsa el a virtuális gépeket a készlet kapacitásának módosításával. A méretezési, kapacitás beállítása a kívánt műveleteket kell lennie, és ezután frissítse a méretezési készletben az új kapacitással rendelkező kívánt beolvasása. Ezek a parancsok cserélje le az ajánlatban szereplő értékeket az erőforráscsoport és a méretezési csoport nevét.

    $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
    $vmss.sku.capacity = 5
    Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

Virtuális gépek a méretezési készlet távolítja el, ha a virtuális gépek legmagasabb azonosítókkal először törlődnek.

