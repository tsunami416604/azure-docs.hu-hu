---
title: Virtuális gép (klasszikus) vagy Cloud Services szerepkör-példány áthelyezése másik alhálózatra – Azure PowerShell | Microsoft Docs
description: Megtudhatja, hogyan helyezhet át virtuális gépeket (klasszikus) és Cloud Services szerepkör-példányokat egy másik alhálózatra a PowerShell használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 275d59a7bddd8b2b609169218afcd15e9a0ce913
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058375"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Virtuális gép (klasszikus) vagy Cloud Services szerepkör-példány áthelyezése másik alhálózatra a PowerShell használatával
A PowerShell használatával áthelyezheti a virtuális gépeket (klasszikus) az egyik alhálózatból egy másikba ugyanazon a virtuális hálózaton (VNet). A szerepkör-példányok a PowerShell használata helyett a CSCFG fájl szerkesztésével helyezhetők át.

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan helyezhetők át a klasszikus üzembe helyezési modellen keresztül üzembe helyezett virtuális gépek.
> 
> 

Miért helyezze át a virtuális gépeket egy másik alhálózatra? Az alhálózatok áttelepítése akkor lehet hasznos, ha a régebbi alhálózat túl kicsi, és az adott alhálózat meglévő futó virtuális gépei miatt nem lehet kibontani. Ebben az esetben létrehozhat egy új, nagyobb alhálózatot, és áttelepítheti a virtuális gépeket az új alhálózatra, majd az áttelepítés befejeződése után törölheti a régi üres alhálózatot.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Virtuális gép áthelyezése másik alhálózatra
Egy virtuális gép áthelyezéséhez futtassa a set-AzureSubnet PowerShell-parancsmagot az alábbi példában szereplő sablon alapján. Az alábbi példában a TestVM a jelen alhálózatról a 2. alhálózatra helyezzük át. Ügyeljen arra, hogy a példát a környezetének megfelelően szerkessze. Vegye figyelembe, hogy amikor egy eljárás részeként futtatja az Update-AzureVM parancsmagot, a rendszer újraindítja a virtuális gépet a frissítési folyamat részeként.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Ha a virtuális gép számára statikus belső magánhálózati IP-címet adott meg, akkor törölnie kell ezt a beállítást, mielőtt át tudja helyezni a virtuális gépet egy új alhálózatra. Ebben az esetben használja a következőt:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Szerepkör-példány áthelyezése másik alhálózatra
Egy szerepkör-példány áthelyezéséhez szerkessze a CSCFG fájlt. Az alábbi példában a "Role0" a virtuális hálózati *VNETName* a meglévő alhálózatról a *2*. alhálózatra kerül. Mivel a szerepkör-példány már telepítve van, csak az alhálózat neve = alhálózat-2 lesz módosítva. Ügyeljen arra, hogy a példát a környezetének megfelelően szerkessze.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
