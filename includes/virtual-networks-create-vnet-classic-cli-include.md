---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 749cc5a5e5b8417abe602b7e37c103a26cc4dc03
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Egy Azure parancssori felület használatával klasszikus virtuális hálózat létrehozása
Az Azure CLI segítségével a parancssorból felügyelheti az erőforrásokat bármilyen Windows, Linux vagy OSX rendszert futtató számítógépről.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../articles/cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Egy VNet és egy alhálózat létrehozásához futtassa a **azure-hálózat virtuális hálózat létrehozása** parancs:
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    Várt kimenet:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**. A létrehozni kívánt VNet neve. A forgatókönyvhöz *TestVNet*
   * **-e (vagy--címtartomány)**. Virtuális hálózat címterület. A forgatókönyvhöz *192.168.0.0*
   * **-i (vagy - cidr)**. Hálózati maszk CIDR formátumban. A forgatókönyvhöz *16*.
   * **-n (vagy--alhálózatneve**). Az első alhálózat neve. A forgatókönyvhöz *előtér*.
   * **-p (vagy--ip-alhálózat-start)**. Kezdő IP-cím alhálózati vagy alhálózati címtartományt. A forgatókönyvhöz *192.168.1.0*.
   * **-r (vagy--alhálózat-cidr)**. Hálózati maszk alhálózat CIDR formátumban. A forgatókönyvhöz *24*.
   * **-l (vagy --location)**. Azure-régió, ahol a VNet létrejön. A forgatókönyvhöz *USA középső RÉGIÓJA*.
3. Egy alhálózat létrehozásához futtassa a **létrehozása az azure vnet alhálózaton** parancs:
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    Az előző parancs várható kimenete:
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (vagy--vnet-name**. A VNet neve, ahol létrejön az alhálózat. A forgatókönyvhöz *TestVNet*.
   * **-n (vagy --name)**. Az új alhálózat neve. A forgatókönyvhöz *háttér*.
   * **-a (vagy --address-prefix)**. Az alhálózat CIDR-blokkja. A forgatókönyvhöz *192.168.2.0/24*.
4. Az új vnet tulajdonságainak megtekintéséhez futtassa a **azure hálózati vnet show** parancs:
   
            azure network vnet show
   
    Az előző parancs várható kimenete:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

