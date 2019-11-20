---
title: Hálózati biztonsági csoport (klasszikus) létrehozása a klasszikus Azure CLI használatával
description: Megtudhatja, hogyan hozhat létre és helyezhet üzembe egy hálózati biztonsági csoportot (klasszikus) a klasszikus Azure CLI használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: c17e75849d1127c9c1001fd87af64e3ffe0eba14
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196715"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Hálózati biztonsági csoport (klasszikus) létrehozása a klasszikus Azure CLI használatával
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. [NSG is létrehozhat a Resource Manager-alapú üzemi modellben](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

A következő minta Azure CLI-parancsok a forgatókönyv alapján már létrehozott egyszerű környezetet várnak. Ha a jelen dokumentumban megjelenő parancsokat szeretné futtatni, először hozzon [létre egy VNet a tesztkörnyezet létrehozásával](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>NSG létrehozása az előtér-alhálózathoz

1. Ha még soha nem használta az Azure CLI-t, olvassa el [Az Azure CLI telepítése és konfigurálása](/cli/azure/install-cli-version-1.0)című témakört.
2. Váltás klasszikus módra:

    ```azurecli
    azure config mode asm
    ```   

3. Hozzon létre egy NSG::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Hozzon létre egy biztonsági szabályt, amely engedélyezi az internetről a 3389-es (RDP) port elérését:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Hozzon létre egy szabályt, amely engedélyezi a 80-as (HTTP) port elérését az internetről:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. A NSG hozzárendelése az előtér-alhálózathoz:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>A NSG létrehozása a háttérbeli alhálózathoz

1. Hozza létre a NSG:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Hozzon létre egy szabályt, amely engedélyezi az 1433 (SQL) port elérését az előtér-alhálózatból:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Hozzon létre egy szabályt, amely megtagadja az internet elérését:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. A NSG hozzárendelése a háttérbeli alhálózathoz:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```