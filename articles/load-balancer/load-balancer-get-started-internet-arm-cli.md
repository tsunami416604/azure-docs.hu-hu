---
title: "Internetkapcsolattal rendelkező terheléselosztó létrehozása a Resource Managerben az Azure parancssori felület használatával | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó a Resource Managerben az Azure parancssori felület használatával"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: c8f29176c8566c94efeecadbc804f459d8f2a6c3

---
# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Belső terheléselosztó létrehozása az Azure parancssori felület használatával

> [!div class="op_single_selector"]
> * [Portál](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Sablon](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [azt is megismerheti, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó a klasszikus üzemelő példány használatával](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>A megoldás üzembe helyezése az Azure parancssori felület használatával

A következő lépések bemutatják, hogyan hozható létre internetkapcsolattal rendelkező terheléselosztó az Azure Resource Manager parancssori felületének használatával. Az Azure Resource Manager lehetővé teszi, hogy az egyes erőforrások konfigurálása egyenként történjen, majd az összerakásukkal jöjjön létre egy erőforrás.

A terheléselosztó üzembe helyezéséhez a következő objektumokat kell létrehozni és konfigurálni:

* Előtér-IP-konfiguráció – a nyilvános IP-címeket tartalmazza a bejövő hálózati forgalomhoz.
* Háttércímkészlet – hálózati adaptereket (NIC) tartalmaz, amelyek segítségével a virtuális gépek fogadhatják a terheléselosztóról érkező hálózati forgalmat.
* Terheléselosztási szabályok – olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá háttércímkészlet portjaihoz.
* Bejövő NAT-szabályok – olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portjait rendelik hozzá egy adott virtuális gép portjához a háttércímkészletben.
* Mintavételezők – állapotfigyelő mintavételezőket tartalmaz, amelyek a virtuálisgép-példányok rendelkezésre állását ellenőrzik a háttércímkészletben.

A további információkat [Az Azure Resource Manager támogatása a terheléselosztó számára](load-balancer-arm.md) című rész tartalmazza.

## <a name="set-up-cli-to-use-resource-manager"></a>A parancssori felület beállítása a Resource Manager használatához

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../xplat-cli-install.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **azure config mode** parancs futtatásával váltson az Erőforrás-kezelő módra, a lent látható módon.

    ```azurecli
        azure config mode arm
    ```

    Várt kimenet:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Virtuális hálózat és nyilvános IP-cím létrehozása az előtér-IP-címkészlethez

1. Hozzon létre egy *NRPVnet* nevű virtuális hálózatot (VNet) az USA keleti régiója helyen az *NRPRG* nevű erőforráscsoporttal.

    ```azurecli
        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16
    ```

    Hozzon létre egy 10.0.0.0/24 CIDR-blokkot tartalmazó *NRPVnetSubnet* nevű alhálózatot az *NRPVnet* virtuális hálózatban.

    ```azurecli
        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24
    ```

2. Hozzon létre egy előtér-IP-címkészlet által használandó nyilvános IP-címet *NRPPublicIP* névvel és *loadbalancernrp.eastus.cloudapp.azure.com* DNS-névvel. Az alábbi parancs a statikus felosztástípust és 4 perces üresjárati időkorlátot használ.

    ```azurecli
        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4
    ```

   > [!IMPORTANT]
   > A terheléselosztó a nyilvános IP-cím tartománycímkéjét fogja használni FQDN-ként. Ez áttérést jelent a klasszikus üzemelő példányról, amely a felhőszolgáltatást használja a terheléselosztó teljes tartományneveként (FQDN).
   > Ebben a példában a *loadbalancernrp.eastus.cloudapp.azure.com* az FQDN.

## <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása

A következő parancs létrehoz egy *NRPlb* nevű terheléselosztót az *NRPRG* erőforráscsoportban, az *USA keleti régiója* Azure-helyen.

    ```azurecli
    azure network lb create NRPRG NRPlb eastus
    ```

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Előtér-IP-címkészlet és háttércímkészlet létrehozása
Ez a példa bemutatja, hogyan kell létrehozni azt az előtér-IP-címkészletet, amely a bejövő hálózati forgalmat fogadja a terheléselosztón, illetve azt a háttér-IP-címkészletet, ahová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat.

1. Hozzon létre egy előtér-IP-címkészletet az előző lépésben létrehozott nyilvános IP-címet társítva a terheléselosztóhoz.

    ```azurecli
        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip
    ```

2. Állítson be egy háttér-címkészletet az előtér-IP-címkészletből bejövő forgalom fogadásához.

    ```azurecli
        azure network lb address-pool create NRPRG NRPlb NRPbackendpool
    ```

## <a name="create-lb-rules-nat-rules-and-probe"></a>LB-szabályok, NAT-szabályok és mintavétel létrehozása

Ez a példa a következő elemeket hozza létre.

* egy NAT-szabályt, amely a 21-es porton bejövő összes forgalmat lefordítja a 22<sup>1</sup>-es portra
* egy NAT-szabályt, amely a 23-as porton bejövő összes forgalmat lefordítja a 22-es portra
* egy terheléselosztó-szabályt, amely elosztja a 80-as porton bejövő összes forgalmat a háttér-címkészletben szereplő címekhez tartozó 80-as porton.
* egy mintavételi szabályt, amely az állapotot ellenőrzi a *HealthProbe.aspx* nevű oldalon.

<sup>1</sup> A NAT-szabályok a terheléselosztó mögött található adott virtuálisgép-példányhoz vannak társítva. A 21-es portra érkező hálózati forgalmat a rendszer elküldi a 22-es porton keresztül egy adott virtuális gépre, amely ehhez a NAT-szabályhoz van társítva. A NAT-szabályhoz meg kell adnia egy protokollt (UDP vagy TCP). Mindkét protokollt nem lehet hozzárendelni ugyanahhoz a porthoz.

1. Hozza létre a NAT-szabályokat.

    ```azurecli
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22
    ```

2. Hozzon létre egy terheléselosztó-szabályt.

    ```azurecli
        azure network lb rule create --resource-group nrprg nrplb --lb-name lbrule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name NRPfrontendpool --backend-address-pool-name NRPbackendpool
    ```

3. Hozzon létre egy állapotmintát.

    ```azurecli
        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4
    ```

4. Ellenőrizze a beállításokat.

    ```azurecli
        azure network lb show nrprg nrplb
    ```

    Várt kimenet:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Hálózati adapterek létrehozása

Hálózati adaptereket kell létrehoznia (vagy a meglévőket is módosíthatja), és hozzá kell rendelnie őket a NAT-szabályokhoz, a terheléselosztási szabályokhoz és a mintavételezőkhöz.

1. Hozzon létre egy hálózati adaptert *lb-nic1-be* néven, majd társítsa az *rdp1* NAT-szabállyal és az *NRPbackendpool* háttércímkészlettel.

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus
    ```

    Várt kimenet:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Hozzon létre egy hálózati adaptert *lb-nic2-be* néven, majd társítsa az *rdp2* NAT-szabállyal és az *NRPbackendpool* háttércímkészlettel.

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus
    ```

3. Hozzon létre egy virtuális gépet (VM) *web1* néven, és társítsa az *lb-nic1-be* nevű hálózati adapterhez. Az alábbi parancs futtatása előtt létrejött egy *web1nrp* nevű tárfiók.

    ```azurecli
        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

    > [!IMPORTANT]
    > A terheléselosztó virtuális gépeinek ugyanabban a rendelkezésre állási készletben kell lenniük. Használja az `azure availset create` parancsot a rendelkezésre állási készlet létrehozásához.

    A kimenet az alábbihoz hasonló lesz:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    > [!NOTE]
    > A következő tájékoztató üzenetnek kell megjelennie: **Ez egy nyilvános IP-cím nélkül konfigurált hálózati adapter**, mivel a terheléselosztóhoz létrehozott hálózati adapter a terheléselosztó nyilvános IP-címével csatlakozik az internethez.

    Mivel az *lb-nic1-be* hálózati adapter az *rdp1* NAT-szabályhoz van társítva, az RDP-vel a terheléselosztó 3441-es portján keresztül csatlakozhat a *web1* virtuális géphez.

4. Hozzon létre egy virtuális gépet (VM) *web2* néven, és társítsa az *lb-nic2-be* nevű hálózati adapterhez. Az alábbi parancs futtatása előtt létrejött egy *web1nrp* nevű tárfiók.

    ```azurecli
        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="update-an-existing-load-balancer"></a>Meglévő terheléselosztó frissítése
Hozzáadhat egy meglévő terheléselosztóra hivatkozó szabályokat. A következő példában egy új terheléselosztó-szabályt adunk hozzá egy **NRPlb** nevű meglévő terheléselosztóhoz

```azurecli
azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool
```

## <a name="delete-a-load-balancer"></a>Terheléselosztó törlése
Terheléselosztó eltávolításához használja a következő parancsot:

```azurecli
azure network lb delete --resource-group nrprg --name nrplb
```

## <a name="next-steps"></a>Következő lépések
[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-cli.md)

[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO5-->


