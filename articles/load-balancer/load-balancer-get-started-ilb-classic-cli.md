---
title: "Belső terheléselosztó létrehozása az Azure parancssori felület használatával a klasszikus üzembehelyezési modellben | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre belső terheléselosztó az Azure parancssori felület használatával a klasszikus üzembehelyezési modellben"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: becbbbde-a118-4269-9444-d3153f00bf34
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4364d3bcdffd278bef35b224a8e22062814ca490


---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Bevezetés a belső terheléselosztó (klasszikus) létrehozásába az Azure parancssori felület használatával
[!INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

[ Ismerje meg, hogyan ](load-balancer-get-started-ilb-arm-cli.md)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Belső terheléselosztó készlet létrehozása a virtuális gépekhez
Belső terheléselosztó készlet, illetve a forgalmukat a készletnek küldő kiszolgálók konfigurálásához tegye a következőket:

1. Hozzon létre egy belső terheléselosztási példányt, amely annak a bejövő forgalomnak a végpontja lesz, amelynek a terhelését el kell osztani az elosztott terhelésű készlet kiszolgálóin.
2. Adja hozzá a virtuális gépekhez rendelt végpontokat, amelyek a bejövő forgalmat fogják kapni.
3. Konfigurálja úgy a kiszolgálókat, amelyek a terheléselosztóra fogják küldeni a forgalmat, hogy azt a belső terheléselosztási példány virtuális IP-címére (VIP) küldjék.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Belső terheléselosztó parancssori felület használatával történő létrehozásának lépései
Ez az útmutató bemutatja, hogyan hozhat létre belső terheléselosztót a fenti forgatókönyv alapján.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../xplat-cli-install.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **azure config mode** parancs futtatásával váltson a klasszikus módra, a lent látható módon.
   
        azure config mode asm
   
    Várt kimenet:
   
        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Végpont és terheléselosztó készlet létrehozása
A forgatókönyv azt feltételezi, hogy a „mytestcloud” nevű felhőszolgáltatásban létre van hozva két virtuális gép, a „DB1” és a „DB2”. Mindkét virtuális gép a „testvnet” nevű virtuális hálózatot használja, a „alhalozat-1” nevű alhálózattal.

Ez az útmutató létrehoz egy belső terheléselosztó készletet: az 1433-as portot használja nyilvános portként, és az 1433-as portot használja helyi portként.

Ez egy gyakori forgatókönyv, amelyben a háttér SQL virtuális gépei terheléselosztót használnak annak a biztosítására, hogy az adatbázis-kiszolgálók ne legyenek közvetlenül elérhetők a használt nyilvános IP-cím miatt.

### <a name="step-1"></a>1. lépés
Belső terheléselosztó készlet létrehozása a következő használatával: `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Használt paraméterek:

**-r** – felhőszolgáltatás neve<BR>
**-n** – belső terheléselosztó neve<BR>
**-t** -alhálózat neve (azonos alhálózat a belső terheléselosztóhoz hozzáadott virtuális gépekével)<BR>
**-a** – (opcionális) statikus privát IP-cím hozzáadása<BR>

További információ: `azure service internal-load-balancer --help`.

A belső terheléselosztó tulajdonságait a következő paranccsal ellenőrizheti: `azure service internal-load-balancer list` *felhőszolgáltatás neve*.

A kimenet például a következő lehet:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>2. lépés
A belső terheléselosztó készlet konfigurálását az első végpont hozzáadásakor kell elvégezni. Ebben a lépésben a végpontot, a virtuális gépet és a mintavételi portot társítja a belső terheléselosztó készlethez.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Használt paraméterek:

**-k** – helyi virtuális gép portja<BR>
**-t** – mintavételi port<BR>
**-r** – mintavételi protokoll<BR>
**-e** – mintavételi időköz (másodperc)<BR>
**-f** – időtúllépési időköz (másodperc) <BR>
**-i** – belső terheléselosztó neve <BR>

## <a name="step-3"></a>3. lépés
Ellenőrizze a terheléselosztó konfigurációját a következő használatával: `azure vm show` *virtuális gép neve*

    azure vm show DB1

A kimenet a következő lesz:

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Hozzon létre egy távoli asztali végpontot a virtuális géphez
Létrehozhat egy távoli asztali végpontot a hálózati forgalom nyilvános portról helyi portra történő továbbításához egy adott virtuális géphez a `azure vm endpoint create` parancs használatával.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Virtuális gép eltávolítása a terheléselosztóból
A virtuális gépet a hozzárendelt végpont törlésével lehet eltávolítani a belső terheléselosztó készletből. Miután megtörtént a végpont eltávolítása, a virtuális gép többé nem tartozik a terheléselosztó készlethez.

 A fenti példa használatával eltávolíthatja a „DB1” virtuális géphez létrehozott végpontot az „lbset” belső terheléselosztóból a következő parancs használatával: `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


További információ: `azure vm endpoint --help`.

## <a name="next-steps"></a>Következő lépések
[A terheléselosztó elosztási módjának konfigurálása forrás IP-affinitás használatával](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


