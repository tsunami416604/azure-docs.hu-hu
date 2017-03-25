---
title: "Internetkapcsolattal rendelkező terheléselosztó létrehozása – klasszikus Azure CLI | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó klasszikus üzembehelyezési modellben az Azure parancssori felület használatával"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
tags: azure-service-management
ms.assetid: e433a824-4a8a-44d2-8765-a74f52d4e584
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: da3a908f17ff5c6d3923549a884ecc0a13cb8e9e
ms.lasthandoff: 03/21/2017

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Bevezetés az internetkapcsolattal rendelkező terheléselosztó (klasszikus) Azure parancssori felület használatával történő létrehozásába

> [!div class="op_single_selector"]
> * [klasszikus Azure portál](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Az Azure-erőforrásokkal való munka megkezdése előtt fontos megérteni, hogy az Azure jelenleg két üzembe helyezési modellel rendelkezik, a Resource Managerrel és a klasszikussal. Bizonyosodjon meg arról, hogy megfelelő ismeretekkel rendelkezik az [üzembe helyezési modellekről és eszközökről](../azure-classic-rm.md), mielőtt elkezdene dolgozni az Azure-erőforrásokkal. A különféle eszközök dokumentációit a cikk tetején található fülekre kattintva tekintheti meg. Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Emellett [azt is megismerheti, hogyan lehet internetkapcsolattal rendelkező terheléselosztót létrehozni az Azure Resource Manager használatával](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>Internetkapcsolattal rendelkező terheléselosztó parancssori felület használatával történő létrehozásának lépései

Ez az útmutató bemutatja, hogyan hozhat létre internetkapcsolattal rendelkező terheléselosztót a fenti forgatókönyv alapján.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **azure config mode** parancs futtatásával váltson a klasszikus módra, a lent látható módon.

    ```azurecli
    azure config mode asm
    ```

    Várt kimenet:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Végpont és terheléselosztó készlet létrehozása

A forgatókönyv feltételezi, hogy létrehozták a „web1” és „web2” virtuális gépet.
Ez az útmutató létrehoz egy terheléselosztó készletet: a 80-as portot használja nyilvános portként, és a 80-as portot használja helyi portként. A mintavételi port szintén a 80-as porton van konfigurálva, és a terheléselosztó készlet „lbset” nevet kapta.

### <a name="step-1"></a>1. lépés

Hozza létre az első végpontot és terheléselosztó készletet a következő használatával a „web1” virtuális géphez: `azure network vm endpoint create`.

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

## <a name="step-2"></a>2. lépés

Adjon hozzá egy második virtuális gépet a terheléselosztó készlethez „web2” néven.

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

## <a name="step-3"></a>3. lépés

Ellenőrizze a terheléselosztó konfigurációját a következő használatával: `azure vm show`.

```azurecli
azure vm show web1
```

A kimenet a következő lesz:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Hozzon létre egy távoli asztali végpontot a virtuális géphez

Létrehozhat egy távoli asztali végpontot a hálózati forgalom nyilvános portról helyi portra történő továbbításához egy adott virtuális géphez a `azure vm endpoint create` parancs használatával.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Virtuális gép eltávolítása a terheléselosztóból

Törölnie kell a terheléselosztó készlethez társított végpontot a virtuális gépből. Miután megtörtént a végpont eltávolítása, a virtuális gép többé nem tartozik a terheléselosztó készlethez.

A fenti példa használatával eltávolíthatja a „web1” virtuális géphez létrehozott végpontot az „lbset” terheléselosztóból a következő parancs használatával: `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> A következő paranccsal további, a végpontok kezelésére szolgáló beállítást ismerhet meg: `azure vm endpoint --help`

## <a name="next-steps"></a>Következő lépések

[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-ps.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

