---
title: A virtuális gép (klasszikus) vagy a Cloud Services szerepkör-példány áthelyezése egy másik alhálózat - Azure PowerShell |} Microsoft Docs
description: Megtudhatja, hogyan helyezi át a virtuális gépek (klasszikus) és a Felhőszolgáltatások szerepkörpéldányokat egy másik alhálózat PowerShell használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb1576b7b842537dbdc49e6b9d402ef47ad9fae9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>A virtuális gép (klasszikus) vagy a Cloud Services szerepkör példány áthelyezése egy másik alhálózat PowerShell használatával
PowerShell használatával helyezze át a virtuális gépek (klasszikus) egy alhálózatból másik ugyanazt a virtuális hálózatot (VNet). Szerepkörpéldányokat helyezheti át a szolgáltatáskonfigurációs SÉMA fájl szerkesztése, hanem a PowerShell használatával.

> [!NOTE]
> Ez a cikk ismerteti, hogyan kívánja áthelyezni a virtuális gépek telepítése csak a klasszikus üzembe helyezési modell használatával.
> 
> 

Miért virtuális gépek áthelyezése egy másik alhálózat? Alhálózati áttelepítés akkor hasznos, ha a régebbi alhálózati túl kicsi, és nem bonthatók ki meglévő futó virtuális gépeinek alhálózat miatt. Ebben az esetben hozzon létre egy új, nagyobb alhálózatot, és telepítse át a virtuális gépeket az új alhálózatot, majd áttelepítés befejezése után törölheti a régi üres alhálózat.

## <a name="how-to-move-a-vm-to-another-subnet"></a>A virtuális gép áthelyezése egy másik alhálózat
Helyezze át a virtuális Gépet, futtassa a Set-AzureSubnet PowerShell-parancsmag, az alábbi példában egy sablon használatával. Az alábbi példa azt áthelyezi TestVM a jelen alhálózatból az alhálózat-2. Ne felejtse el szerkeszteni a példa a környezetnek megfelelően. Vegye figyelembe, hogy amikor a frissítés-AzureVM parancsmag egy eljárás részeként futtatja, mindig újra lesz indítva a virtuális Gépet a frissítési folyamat részeként.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Ha megadott egy statikus belső magánhálózati IP-címe a virtuális Gépet, törölje ezt a beállítást, mielőtt a virtuális gép áthelyezése egy új alhálózatot kell. Ebben az esetben az alábbi parancsokat használja:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>A szerepkör példánya áthelyezése egy másik alhálózat
Helyezze át a szerepkör-példányt, szerkessze a CSCFG-fájl. Az alábbi példa azt áthelyezi "Role0"-beli virtuális hálózaton *VNETName* a jelen alhálózatból az *alhálózat-2*. A szerepkör példánya már telepítve lett, mert az alhálózat neve csak módosítani fogjuk alhálózat-2 =. Ne felejtse el szerkeszteni a példa a környezetnek megfelelően.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
