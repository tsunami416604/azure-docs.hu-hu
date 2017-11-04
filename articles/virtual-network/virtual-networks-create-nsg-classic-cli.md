---
title: "Az NSG-k létrehozása a klasszikus mód az Azure parancssori felülettel |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre és telepíthet az NSG-k klasszikus módban, az Azure parancssori felület használatával"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: 115a1937a4c88ba2b986a40c84b1b759ed5e03b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Az NSG-k (klasszikus) létrehozása az Azure parancssori felület
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [NSG-k létrehozása a Resource Manager üzembe helyezési modellel](virtual-networks-create-nsg-arm-cli.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Az alábbi minta Azure parancssori felület parancsait már a fenti forgatókönyv alapján létre egy egyszerű környezetben várható. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először által a tesztkörnyezet felépítéséhez [létre virtuális hálózatok](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Az NSG az előtér-alhálózat létrehozása
Hozzon létre egy NSG nevű nevű **NSG-előtérbeli** a fenti forgatókönyv alapján, kövesse az alábbi lépéseket.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Futtassa a  **`azure config mode`**  parancs futtatásával váltson klasszikus módban, a lent látható módon.
   
        azure config mode asm
   
    Várt kimenet:
   
        info:    New mode is asm
3. Futtassa a  **`azure network nsg create`**  parancs futtatásával hozzon létre egy NSG.
   
        azure network nsg create -l uswest -n NSG-FrontEnd
   
    Várt kimenet:
   
        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK
   
    Paraméterek:
   
   * **-l (vagy --location)**. Azure-régió, ahol létrejön az új NSG. A mi esetünkben *westus*.
   * **-n (vagy --name)**. Az új NSG neve. A mi esetünkben *NSG-előtérbeli*.
4. Futtassa a  **`azure network nsg rule create`**  parancs futtatásával hozzon létre egy szabályt, amely engedélyezi a hozzáférést a 3389-es (RDP) az internetről.
   
        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   
    Várt kimenet:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK
   
    Paraméterek:
   
   * **-a (vagy--nsg-név)**. Az ebben a szabály jön létre NSG neve. A mi esetünkben *NSG-előtérbeli*.
   * **-n (vagy --name)**. Az új szabály nevét. A mi esetünkben *rdp-szabály*.
   * **-c (vagy--művelet)**. Hozzáférési szint a szabály (Megtagadás vagy engedélyezés).
   * **-p (vagy--protokoll)**. Protocol (Tcp, Udp vagy *) a szabályhoz.
   * **-r (vagy--típus)**. Irányát (bejövő vagy kimenő) kapcsolat.
   * **-y (vagy--prioritás)**. A szabály prioritását.
   * **-f (vagy--forráscímelőtag)**. Forráscímelőtag CIDR vagy az alapértelmezett címkéket használ.
   * **-o (vagy--Forrásporttartomány)**. Forrásport, vagy porttartomány.
   * **-e (vagy--cél címelőtag)**. Célcím-előtag CIDR vagy az alapértelmezett címkéket használ.
   * **-u (vagy--Célporttartomány)**. Célport vagy porttartomány.
5. Futtassa a  **`azure network nsg rule create`**  parancs futtatásával hozzon létre egy szabályt, amely engedélyezi a hozzáférést a port a 80-as (HTTP) az internetről.
   
        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
   
    Várt putput:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK
6. Futtassa a  **`azure network nsg subnet add`**  parancs az NSG csatolása az előtér-alhálózat.
   
        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   
    Várt kimenet:
   
        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Az NSG a háttérbeli alhálózat létrehozása
Hozzon létre egy NSG nevű nevű *NSG-háttérrendszer* a fenti forgatókönyv alapján, kövesse az alábbi lépéseket.

1. Futtassa a  **`azure network nsg create`**  parancs futtatásával hozzon létre egy NSG.
   
        azure network nsg create -l uswest -n NSG-BackEnd
   
    Várt kimenet:
   
        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK
   
    Paraméterek:
   
   * **-l (vagy --location)**. Azure-régió, ahol létrejön az új NSG. A mi esetünkben *westus*.
   * **-n (vagy --name)**. Az új NSG neve. A mi esetünkben *NSG-előtérbeli*.
2. Futtassa a  **`azure network nsg rule create`**  parancs futtatásával hozzon létre egy szabályt, amely engedélyezi a hozzáférést a port a 1433-as port (SQL) az előtér-alhálózatból.
   
        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   
    Várt kimenet:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK
3. Futtassa a  **`azure network nsg rule create`**  parancs futtatásával hozzon létre egy szabályt, amely megtagadja a hozzáférést az internethez.
   
        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   
    Várt putput:
   
        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK
4. Futtassa a  **`azure network nsg subnet add`**  csatolja az NSG a háttérbeli alhálózati parancsot.
   
        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
   
    Várt kimenet:
   
        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK

