---
title: Hozzon létre egy hálózati biztonsági csoport (klasszikus) használatával a klasszikus Azure CLI |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és telepíthet egy hálózati biztonsági csoport (klasszikus) az Azure klasszikus parancssori felület használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: 5e81fa709589e5fa4a109cdf7709b87bc446ac3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947234"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Hozzon létre egy hálózati biztonsági csoport (klasszikus) az Azure klasszikus parancssori felület használatával
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [NSG-k létrehozása a Resource Manager-alapú üzemi modellben](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Azure CLI az alábbi Példaparancsok egy egyszerű környezetet állítunk már létrehozta a forgatókönyvet alapján várt. Ha azt szeretné, akkor jelennek meg ebben a dokumentumban a parancsok futtatásához először hozhat létre a tesztkörnyezetben által [létre virtuális hálózat](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Hozzon létre egy NSG-t az előtér-alhálózat

1. Ha még soha nem használta az Azure CLI-vel, tekintse meg [telepítése és konfigurálása az Azure CLI](/cli/azure/install-cli-version-1.0).
2. Váltson a klasszikus módra:

    ```azurecli
    azure config mode asm
    ```   

3. Hozzon létre egy NSG-t::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Hozzon létre egy biztonsági szabályt, amely lehetővé teszi a hozzáférést a 3389-es (RDP) port az internetről:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Hozzon létre egy szabályt, amely lehetővé teszi, hogy hozzáférési port a 80-as (HTTP) az internetről:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Az előtérbeli alhálózat NSG hozzárendelése:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Az NSG a háttérbeli alhálózat létrehozása

1. Az NSG létrehozásához:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Hozzon létre egy szabályt, amely lehetővé teszi a hozzáférést az 1433-as (SQL) portot az előtérbeli alhálózatból:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Hozzon létre egy szabályt, amely megtagadja a hozzáférést az internethez:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. NSG a háttérbeli alhálózat hozzárendelése:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```