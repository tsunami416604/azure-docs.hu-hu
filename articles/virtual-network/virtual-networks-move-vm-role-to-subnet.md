---
title: Egy virtuális gép (klasszikus) vagy a Felhőszolgáltatások szerepkörpéldányok áthelyezése másik alhálózatra – Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan helyezi át a virtuális gépek (klasszikus) és a Felhőszolgáltatások szerepkörpéldányok egy másik alhálózatot a PowerShell használatával.
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
ms.openlocfilehash: 787a50a0cbf16089cd15f922b494cd12d680cb43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640395"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Egy virtuális gép (klasszikus) vagy a Felhőszolgáltatások szerepkörpéldányok áthelyezése másik alhálózatra PowerShell-lel
Helyezze át a virtuális gépek (klasszikus) egyik alhálózatról a másikra, az azonos virtuális hálózaton (VNet) a PowerShell használatával is. Szerepkörpéldányok áthelyezhetők a CSCFG-fájl szerkesztése, hanem a PowerShell használatával.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan kívánja áthelyezni a virtuális gépek csak a klasszikus üzemi modellel üzembe helyezett.
> 
> 

Miért áthelyezi egy másik alhálózatot a virtuális gépeket? Alhálózati áttelepítés akkor hasznos, ha a régebbi alhálózat túl kicsi, és nem bonthatók ki meglévő futó virtuális gépek az alhálózat miatt. Ebben az esetben létrehozhat egy új, nagyobb alhálózat és a virtuális gépek migrálása az új alhálózat, majd az áttelepítés után törölheti a régi üres alhálózatot.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Virtuális gép áthelyezése egy másik alhálózatot
Virtuális gép áthelyezése, futtassa a Set-AzureSubnet PowerShell-parancsmagot, használja az alábbi példában egy sablont. Az alábbi példában azt költöznek TestVM a jelen alhálózatról alhálózati – 2. Győződjön meg arról, szerkesztheti a példában a környezetnek megfelelően. Vegye figyelembe, hogy minden alkalommal, amikor a frissítés-AzureVM parancsmag egy eljárás részeként futtatja, újra fog a virtuális gép a frissítési folyamat részeként.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Ha a virtuális géphez megadott statikus belső magánhálózati IP-cím, kell törölje a jelet a beállítás előtt egy új alhálózatot a virtuális gép áthelyezhető. Ebben az esetben használja a következőt:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Egy szerepkörpéldány áthelyezése egy másik alhálózatot
Egy szerepkörpéldány áthelyezéséhez a CSCFG-fájl szerkesztése. Az alábbi példában azt helyez át "Role0" virtuális hálózat *VNETName* való a jelenlegi alhálózatról *alhálózat-2*. A szerepkörpéldány már telepítve volt, mert az alhálózat neve egyszerűen módosítani fogja alhálózat-2 =. Győződjön meg arról, szerkesztheti a példában a környezetnek megfelelően.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
