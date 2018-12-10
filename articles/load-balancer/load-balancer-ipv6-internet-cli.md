---
title: Nyilvános load balancer létrehozása IPv6 - Azure CLI-vel
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozhat létre az Azure CLI-vel nyilvános load balancer konfigurálása IPv6-tal.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6-alapú, az azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 1caa8e7554024c3b2e3d86436d3d494d7995169a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53142019"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Hozzon létre egy nyilvános load balancer konfigurálása IPv6-tal Azure CLI használatával


Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. Terheléselosztók osztja meg a bejövő forgalmat, többek között a kifogástalan állapotú szolgáltatási példányai a felhőszolgáltatások vagy virtuális gépek egy terheléselosztó készletet: a magas rendelkezésre állást biztosít. Terheléselosztók is jelenthet, ezeket a szolgáltatásokat több portra vagy több IP-cím vagy mindkettőt meg.

## <a name="example-deployment-scenario"></a>A példában üzembe helyezési forgatókönyv

A következő ábra szemlélteti a terheléselosztási megoldás, amely a jelen cikkben ismertetett példa sablon segítségével telepítve van.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Ebben a forgatókönyvben a következő Azure-erőforrásokat hoz létre:

* Két virtuális gép (VM)
* Az egyes virtuális Gépekhez rendelt IPv4- és IPv6-címekkel rendelkező virtuális hálózati adapter
* Nyilvános load balancer egy IPv4-és IPv6-alapú nyilvános IP-cím
* A két virtuális gépet tartalmazó rendelkezésre állási csoport
* Két a nyilvános VIP-címek leképezése a privát végpontok terheléselosztási szabályok betöltése

## <a name="deploy-the-solution-by-using-azure-cli"></a>A megoldás üzembe helyezése az Azure CLI használatával

A következő lépésekkel egy nyilvános terheléselosztó létrehozása az Azure parancssori felület használatával. Parancssori felület használatával, létre és konfigurálhatja az egyes objektumok egyenként, és csak utána őket egy erőforrás létrehozása.

A terheléselosztó üzembe helyezéséhez, hozzon létre és konfigurálja a következő objektumokat:

* **Előtérbeli IP-konfigurációhoz**: a bejövő hálózati forgalomhoz nyilvános IP-címeket tartalmazza.
* **Háttér címkészletet**: hálózati adaptereket (NIC) tartalmaz a virtuális gépek a terheléselosztóról érkező hálózati forgalom fogadására.
* **Terheléselosztási szabályok**: olyan szabályokat, amelyek a terheléselosztó nyilvános portját a háttér-címkészletben levő porthoz tartalmaz.
* **Bejövő NAT-szabályok**: tartalmazza a hálózati címfordítás (NAT) szabályait, amelyek a terheléselosztó nyilvános portjait egy adott virtuális gép a háttér-címkészletben levő porthoz.
* **Mintavételek**: a háttér-címkészletet a virtuálisgép-példányok rendelkezésre állásának ellenőrzésére használt állapotfigyelő mintavételezőket tartalmaz.

## <a name="set-up-azure-cli"></a>Az Azure parancssori felület beállítása

Ebben a példában az Azure CLI-eszközöket egy PowerShell-parancsablakban futtassa. A jobb olvashatóság érdekében és újbóli érdekében PowerShell a parancsfájl-kezelési képességei, nem az Azure PowerShell-parancsmagokat kell használnia.

1. [Telepítse és konfigurálja az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a következő a hivatkozott cikk lépéseit, és jelentkezzen be Azure-fiókjába.

2. Állítsa be az Azure CLI-parancsokkal használható PowerShell-változók:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Hozzon létre egy erőforráscsoportot, egy terheléselosztót, virtuális hálózat és alhálózatok

1. Hozzon létre egy erőforráscsoportot:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Load balancer létrehozása:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Virtuális hálózat létrehozása:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. A virtuális hálózat létrehozása két alhálózattal:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Nyilvános IP-címek az előtér-készlet létrehozása

1. Állítsa be a PowerShell-változókat:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Hozzon létre egy nyilvános IP-címet az előtérbeli IP-címkészlet:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > A terheléselosztó a nyilvános IP-cím tartománycímkéjét használja a teljes tartománynév (FQDN). Ez a klasszikus üzemelő példányról, amely a felhőalapú szolgáltatást használ egy módosítást nevezze el a terheléselosztó FQDN.
    >
    > Ebben a példában az FQDN-je *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Előtér- és háttér-címkészletek létrehozása

Ebben a szakaszban a következő IP-címkészletek létrehozása:
* Az előtérbeli IP-címkészlet, amely megkapja a terheléselosztón a bejövő hálózati forgalmat.
* A háttérbeli IP-készlet hová az előtérkészlet küldi az elosztott terhelésű hálózati forgalmat.

1. Állítsa be a PowerShell-változókat:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Hozzon létre egy előtérbeli IP-címkészletet, és társítsa a nyilvános IP-cím, amelyet az előző lépést, és a terheléselosztóhoz.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>A mintavétel, a NAT-szabályok létrehozása és a load balancer-szabályok

Ez a példa a következő elemeket hozza létre:

* Egy mintavételi szabályt, a 80-as porton való csatlakozást.
* Az RDP a 3389-es portra 3389-es porton bejövő összes forgalmat lefordítja a NAT-szabályt.\*
* A távoli asztal protokoll (RDP) a 3389-es portra 3391 porton bejövő összes forgalmat lefordítja a NAT-szabályt.\*
* Egy terheléselosztó-szabályt a 80-as portot a háttér-címkészletben szereplő címek a 80-as porton bejövő összes forgalmat elosztása érdekében.

\* NAT-szabályok társítva a terheléselosztó mögött egy adott virtuálisgép példányhoz. A 3389-es portra érkező hálózati forgalom érkezik az adott virtuális gép és portot, amelyet a NAT-szabályhoz van társítva. A NAT-szabályhoz meg kell adnia egy protokollt (UDP vagy TCP). Mindkét protokollt nem rendelhető hozzá ugyanahhoz a porthoz.

1. Állítsa be a PowerShell-változókat:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. A projekt létrehozásához.

    A következő példában létrehozunk egy TCP-mintavétel, amely ellenőrzi a csatlakozást a háttérbeli 80-as porton 15 másodpercenként. Két egymást követő hibák után, a háttér-erőforrás nem jelöli meg.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Bejövő NAT-szabályok, amelyek lehetővé teszik a háttér-erőforrásokhoz való RDP-kapcsolatok létrehozása:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Hozzon létre a load balancer-szabályok, amelyek forgalmat küldeni, a másik háttér-portok, attól függően, hogy a kérés érkezett a kezelőfelület.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Ellenőrizze a beállításokat:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Várt kimenet:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Hálózati adapterek létrehozása

Hálózati adapterek létrehozása, és rendelje azokat NAT-szabályok, a load balancer-szabályok és a mintavételek.

1. Állítsa be a PowerShell-változókat:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Hozzon létre egy hálózati Adaptert az egyes háttérrendszerének, és a egy IPv6-konfiguráció hozzáadása:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>A háttérbeli Virtuálisgép-erőforrások létrehozása, és minden egyes hálózati adapter csatolása

Virtuális gépek létrehozásához, egy storage-fiókot kell rendelkeznie. A terheléselosztást, a virtuális gépek rendelkezésre állási csoport tagjának lennie kell. Virtuális gépek létrehozásával kapcsolatos további információkért lásd: [egy Azure virtuális gép létrehozása PowerShell használatával](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Állítsa be a PowerShell-változókat:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Ebben a példában a virtuális gépek nem titkosított felhasználónevet és jelszót használja. Megfelelő gondossággal ezeket a hitelesítő adatokat nem titkosított szövegben való használatakor. A PowerShell hitelesítő adatok kezelésére egy biztonságosabb módszert a [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) parancsmagot.

2. A rendelkezésre állási csoport létrehozása:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. A társított hálózati adapterrel rendelkező virtuális gépek létrehozása:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```

## <a name="next-steps"></a>További lépések

[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-cli.md)  
[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)  
[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)
