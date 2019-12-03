---
title: Hálózati biztonsági csoport (klasszikus) létrehozása a PowerShell használatával | Microsoft Docs
description: Útmutató hálózati biztonsági csoport (klasszikus) létrehozásához és üzembe helyezéséhez a PowerShell használatával
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 2/02/2019
ms.author: genli
ms.openlocfilehash: 0f957c5d59dec06057841a95ec48a54462778a69
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672483"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Hálózati biztonsági csoport (klasszikus) létrehozása a PowerShell használatával
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. [NSG is létrehozhat a Resource Manager-alapú üzemi modellben](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Az alábbi PowerShell-parancsok a fenti forgatókönyv alapján már létrehozott egyszerű környezetet várnak. Ha a jelen dokumentumban megjelenő parancsokat szeretné futtatni, először hozzon [létre egy VNet a tesztkörnyezet létrehozásával](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>NSG létrehozása az előtér-alhálózathoz

1. Ha még soha nem használta a Azure PowerShellt, olvassa el [a Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)című témakört.

2. Hozzon létre egy *NSG-FrontEnd*nevű hálózati biztonsági csoportot:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Hozzon létre egy biztonsági szabályt, amely engedélyezi az internetről a 3389-es port elérését:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Hozzon létre egy biztonsági szabályt, amely engedélyezi az internetről a 80-es port elérését:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```
5. A hálózati biztonsági csoport hozzárendelése egy alhálózathoz:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-Frontend" `
    | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName "TestVNet" `
    -Subnet "FrontEnd"
    ```
## <a name="create-an-nsg-for-the-back-end-subnet"></a>NSG létrehozása a háttérbeli alhálózathoz

1. Hozzon létre egy *NSG-backend*nevű hálózati biztonsági csoportot:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Hozzon létre egy biztonsági szabályt, amely engedélyezi a hozzáférést az előtér-alhálózatról a 1433-es portra (az SQL Server által használt alapértelmezett port):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Biztonsági szabály létrehozása az alhálózatról az internetre való hozzáférés blokkolásával:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```
4. A hálózati biztonsági csoport hozzárendelése egy alhálózathoz:
    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-Backend" `
    | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName "TestVNet" `
    -Subnet "BackEnd"
    ```