---
title: Hozzon létre egy hálózati biztonsági csoportot (klasszikus) használata az Azure CLI 1.0 |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és telepíthet a hálózati biztonsági csoport (klasszikus) használata az Azure CLI 1.0.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: 67b959378521e5a456a2e464d6dd04bbdcab4f30
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-network-security-group-classic-using-the-azure-cli-10"></a>Hálózati biztonsági csoport létrehozása (klasszikus) használata az Azure CLI 1.0
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [NSG-k létrehozása a Resource Manager üzembe helyezési modellel](tutorial-filter-network-traffic-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Az alábbi Példaparancsok az Azure parancssori felület már a a forgatókönyv alapján létre egy egyszerű környezetben várható. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először által a tesztkörnyezet felépítéséhez [létre virtuális hálózatok](virtual-networks-create-vnet-classic-cli.md).

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Hozzon létre egy NSG-t az előtér-alhálózat

1. Ha még sosem használta az Azure parancssori felület, lásd: [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md).
2. Átválthat a klasszikus mód:

    ```azurecli
    azure config mode asm
    ```   

3. Hozzon létre egy NSG::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Hozzon létre egy biztonsági szabályt, amely engedélyezi a hozzáférést a 3389-es (RDP) az internetről:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Hozzon létre egy szabályt, amely engedélyezi a hozzáférést a port a 80-as (HTTP) az internetről:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. Az előtér-alhálózathoz NSG társítása:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Az NSG-t, a háttér-alhálózat létrehozása

1. Az NSG létrehozása:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Hozzon létre egy szabályt, amely engedélyezi a hozzáférést a port a 1433-as port (SQL) az előtér-alhálózatból:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Hozzon létre egy szabályt, amely megtagadja a hozzáférést az internethez:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. A háttér-alhálózathoz NSG társítása:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```