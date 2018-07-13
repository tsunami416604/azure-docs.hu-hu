---
title: Hozzon létre egy hálózati biztonsági csoport (klasszikus) PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és telepíthet egy hálózati biztonsági csoport (klasszikus) PowerShell-lel
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: ecb977660ed99a3cea2a71a867f50822b23e568c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38634196"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>Hozzon létre egy hálózati biztonsági csoport (klasszikus) PowerShell-lel
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [NSG-k létrehozása a Resource Manager-alapú üzemi modellben](tutorial-filter-network-traffic.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

A minta PowerShell-parancsokat az alábbi hatással vannak a már létrehozott egy egyszerű környezetet a fenti forgatókönyv alapján. Ha azt szeretné, akkor jelennek meg ebben a dokumentumban a parancsok futtatásához először hozhat létre a tesztkörnyezetben által [létre virtuális hálózat](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Hozzon létre egy NSG-t az előtér-alhálózat

1. Ha még nem használta az Azure Powershellt, tekintse meg [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

2. Hozzon létre egy hálózati biztonsági csoport nevű *NSG-FrontEnd*:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Hozzon létre egy biztonsági szabály engedélyezi a hozzáférést az internetről 3389-es port:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Hozzon létre egy biztonsági szabály engedélyezi a hozzáférést az internetről a 80-as port:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>Az NSG a háttérbeli alhálózat létrehozása

1. Hozzon létre egy hálózati biztonsági csoport nevű *NSG-háttérrendszer*:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Hozzon létre egy biztonsági szabály engedélyezi a hozzáférést az előtérbeli alhálózatból az 1433-as port (SQL Server által használt alapértelmezett port):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Hozzon létre egy biztonsági szabály blokkolja a hozzáférést az alhálózatról, az internethez:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```