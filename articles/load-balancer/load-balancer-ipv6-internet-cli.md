---
title: Nyilvános terheléselosztó létrehozása az IPv6-tal – Azure CLI
titleSuffix: Azure Load Balancer
description: Ezzel a tanulási útvonallal első lépések hozhat létre nyilvános terheléselosztót az IPv6-tal az Azure CLI használatával.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azúrkék terheléselosztó, kettős verem, nyilvános ip, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: bff6a7ca6eb1a6859ec25d488f564c66946a780b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045411"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Nyilvános terheléselosztó létrehozása az IPv6-tal az Azure CLI használatával

>[!NOTE] 
>Ez a cikk egy bevezető IPv6-szolgáltatást ismertet, amely lehetővé teszi, hogy az egyszerű terheléselosztók iPv4- és IPv6-kapcsolatot is biztosítsanak. Az IPv6-kapcsolat már elérhető az [IPv6 for Azure VNET-k](../virtual-network/ipv6-overview.md) esetében, amely integrálja az IPv6-kapcsolatot a virtuális hálózatokkal, és olyan kulcsfontosságú funkciókat tartalmaz, mint az IPv6 hálózati biztonsági csoport szabályai, az IPv6-felhasználó által definiált útválasztás, az IPv6 alapszintű és a szabványos terheléselosztás stb.  Az IPv6 azure-beli VNET-k az Azure-beli IPv6-alkalmazások ajánlott szabványai. Lásd: [IPv6 az Azure VNET Powershell üzembe helyezéséhez](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztók magas rendelkezésre állást biztosítanak azáltal, hogy a bejövő forgalmat a felhőszolgáltatások ban vagy a terheléselosztó készletben lévő virtuális gépeken lévő kifogástalan szolgáltatáspéldányok között osztják el. A terheléselosztók ezeket a szolgáltatásokat több porton vagy több IP-címen vagy mindkettőn is bemutathatják.

## <a name="example-deployment-scenario"></a>Példa telepítési forgatókönyvre

Az alábbi ábra a jelen cikkben ismertetett példasablon használatával üzembe helyezett terheléselosztási megoldást mutatja be.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Ebben a forgatókönyvben a következő Azure-erőforrásokat hozza létre:

* Két virtuális gép (VM)
* Virtuális hálózati adapter minden virtuális géphez, amelyhez IPv4- és IPv6-címek is vannak hozzárendelve
* Nyilvános terheléselosztó IPv4-vel és IPv6 nyilvános IP-címmel
* A két virtuális gépet tartalmazó rendelkezésre állási csoport
* Két terheléselosztási szabály a nyilvános VIP-k hozzárendeléséhez a privát végpontokhoz

## <a name="deploy-the-solution-by-using-azure-cli"></a>A megoldás üzembe helyezése az Azure CLI használatával

A következő lépések bemutatják, hogyan hozhat létre egy nyilvános terheléselosztó t az Azure CLI használatával. A CLI használatával minden objektumot külön-külön hozhat létre és konfigurálhat, majd összeállíthatja őket egy erőforrás létrehozásához.

Terheléselosztó üzembe helyezéséhez hozza létre és konfigurálja a következő objektumokat:

* **Előtér-IP-konfiguráció**: Nyilvános IP-címeket tartalmaz a bejövő hálózati forgalomhoz.
* **Háttércímkészlet**: Hálózati adaptereket (NIC) tartalmaz, amelyek en a virtuális gépek számára a terheléselosztótól érkező hálózati forgalmat fogadhatják.
* **Terheléselosztási szabályok**: Olyan szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját a háttércímkészlet egyik portjához kötik.
* **Bejövő NAT-szabályok**: Hálózati címfordítási (NAT) szabályokat tartalmaz, amelyek a terheléselosztó nyilvános portját a háttércímkészletben lévő adott virtuális gép portjához kötik.
* **Mintavételek**: Állapotminta-mintavételeket tartalmaz, amelyek a virtuálisgép-példányok rendelkezésre állásának ellenőrzésére szolgálnak a háttércímkészletben.

## <a name="set-up-azure-cli"></a>Az Azure CLI beállítása

Ebben a példában az Azure CLI-eszközöket egy PowerShell-parancsablakban futtatja. Az olvashatóság és az újrafelhasználás javítása érdekében a PowerShell parancsfájlkezelési képességeit használja, nem pedig az Azure PowerShell-parancsmagokat.

1. [Telepítse és konfigurálja az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a csatolt cikkben leírt lépések végrehajtásával, és jelentkezzen be az Azure-fiókjába.

2. Állítsa be a PowerShell-változókat az Azure CLI-parancsokkal való használatra:

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

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Erőforráscsoport, terheléselosztó, virtuális hálózat és alhálózatok létrehozása

1. Hozzon létre egy erőforráscsoportot:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Hozzon létre egy terheléselosztót:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Virtuális hálózat létrehozása:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Ebben a virtuális hálózatban hozzon létre két alhálózatot:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Nyilvános IP-címek létrehozása az előtér-készlethez

1. Állítsa be a PowerShell-változókat:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Hozzon létre egy nyilvános IP-címet az előtér-IP-készlethez:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > A terheléselosztó a nyilvános IP tartománycímkéjét használja teljes körűen minősített tartománynévként (FQDN). Ez a változás a klasszikus üzembe helyezéshez, amely a felhőszolgáltatás nevét használja a terheléselosztó fqdn-jeként.
    >
    > Ebben a példában a teljes tartománynév *contoso09152016.southcentralus.cloudapp.azure.com.*

## <a name="create-front-end-and-back-end-pools"></a>Előtér- és háttérkészletek létrehozása

Ebben a szakaszban a következő IP-készleteket hozza létre:
* Az előtér-IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
* A háttér-IP-készlet, ahol az előtér-készlet elküldi a terheléselosztásos hálózati forgalmat.

1. Állítsa be a PowerShell-változókat:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Hozzon létre egy előtér-IP-készletet, és társítsa azt az előző lépésben létrehozott nyilvános IP-címhez és a terheléselosztóhoz.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>A mintavétel, a NAT-szabályok és a terheléselosztó szabályok létrehozása

Ez a példa a következő elemeket hozza létre:

* Mintavételi szabály a 80-as TCP-porthoz való kapcsolódás ellenőrzésére.
* NAT-szabály a 3389-es porton érkező összes bejövő forgalom és az RDP 3389-es portjára való fordítására.\*
* NAT-szabály, amely a 3391-es porton lévő összes bejövő forgalmat a távoli asztali protokoll (RDP) 3389-es portjára fordítja le.\*
* A terheléselosztó szabály kiegyenlítésére az összes bejövő forgalom a 80-as port port 80 a címek a háttér-készlet.

\*A NAT-szabályok egy adott virtuálisgép-példányhoz vannak társítva a terheléselosztó mögött. A 3389-es portra érkező hálózati forgalmat a rendszer a NAT-szabályhoz társított virtuális gépnek és portnak küldi. A NAT-szabályhoz meg kell adnia egy protokollt (UDP vagy TCP). Nem rendelheti mindkét protokollt ugyanahhoz a porthoz.

1. Állítsa be a PowerShell-változókat:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Hozza létre a szondát.

    A következő példa létrehoz egy TCP-mintavételt, amely 15 másodpercenként ellenőrzi a 80-as háttér-TCP-porthoz való kapcsolódást. Két egymást követő hiba után a háttérerőforrás elérhetetlenné válna.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Bejövő hálózati házirendek létrehozása, amelyek lehetővé teszik az RDP-kapcsolatokat a háttérerőforrásokhoz:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Hozzon létre terheléselosztási szabályokat, amelyek a kérést eljuttató előtér-kódtól függően különböző háttérportokra küldik a forgalmat.

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

Hozzon létre hálózati adaptereket, és társítsa őket a NAT-szabályokkal, a terheléselosztó szabályokkal és a mintavételekkel.

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

2. Hozzon létre egy hálózati adaptert minden háttérrendszerhez, és adjon hozzá egy IPv6-konfigurációt:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Hozza létre a háttérvirtuális gép erőforrásait, és csatolja az egyes hálózati adaptereket

Virtuális gépek létrehozásához rendelkeznie kell egy tárfiókkal. A terheléselosztáshoz a virtuális gépeknek egy rendelkezésre állási csoport tagjainak kell lenniük. A virtuális gépek létrehozásáról további információt az [Azure-beli virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)a PowerShell használatával című témakörben talál.

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
    > Ez a példa a virtuális gépek felhasználónevét és jelszavát használja a tiszta szövegben. Legyen megfelelő gondossággal, ha ezeket a hitelesítő adatokat tiszta szövegként használja. A PowerShell ben a hitelesítő adatok kezelésének [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx) biztonságosabb módja a parancsmagban található.

2. Hozza létre a rendelkezésre állási csoportot:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Hozza létre a virtuális gépeket a kapcsolódó hálózati adapterekkel:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```


