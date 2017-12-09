---
title: "Hozzon létre egy nyilvános terheléselosztó IPv6 - Azure parancssori Felülettel |} Microsoft Docs"
description: "Útmutató: Azure parancssori felület használatával hozzon létre egy nyilvános terheléselosztó IPv6 az Azure Resource Manager."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: "IPv6-alapú, azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot"
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3abd47460999f7b059469a58a59a3e297e88effb
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>Hozzon létre egy nyilvános terheléselosztó IPv6 az Azure Resource Manager Azure parancssori felület használatával

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Sablon](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. Terheléselosztó bejövő forgalmat a felhőszolgáltatások kifogástalan szolgáltatáspéldány vagy a load balancer csoportban lévő virtuális gépek között elosztásával magas rendelkezésre állás biztosításához. A terheléselosztók is jelenthet, ezek a szolgáltatások több portot vagy több IP-cím, vagy mindkettőt.

## <a name="example-deployment-scenario"></a>Központi telepítési példa

A következő ábra szemlélteti a terheléselosztási megoldás, amely a jelen cikkben ismertetett példa sablon használatával történik.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Ebben a forgatókönyvben a következő Azure-erőforrások létrehozása:

* két virtuális gépek (VM)
* az egyes virtuális gépekhez rendelt IPv4 és IPv6-címmel rendelkező virtuális hálózati illesztő
* Egy nyilvános terheléselosztó egy IPv4-és IPv6 nyilvános IP-cím
* A két virtuális gépeket tartalmazó rendelkezésre állási csoportok
* két terheléselosztási szabályok a nyilvános virtuális IP-címek hozzárendelését a saját végpontokhoz való betöltése

## <a name="deploy-the-solution-by-using-azure-cli"></a>A megoldás üzembe helyezéséhez az Azure parancssori felület használatával

A következő lépések bemutatják, hogyan egy nyilvános terheléselosztó létrehozása az Azure CLI Azure Resource Manager használatával. Az Azure Resource Manager létrehozása és beállítása az egyes objektumok külön-külön, majd tegye azokat együtt egy erőforrás létrehozásához.

Terheléselosztó telepítéséhez hozzon létre, és adja meg a következő objektumok:

* **Előtér-IP-konfiguráció**: a bejövő hálózati forgalmat a nyilvános IP-cím szerepel.
* **Háttér-címkészlet**: tartalmazza a hálózati adapterek (NIC) a virtuális gépek hálózati forgalom fogadására a terheléselosztótól.
* **Terheléselosztási szabályok**: egy nyilvános port terheléselosztón van leképezve a háttér-címkészletbeli port szabályokat tartalmaz.
* **Bejövő NAT-szabályok**: hálózati cím címfordítási (NAT) szabályok, amelyek egy nyilvános port terheléselosztón leképezése egy adott virtuális gép a háttér-címkészletbeli port tartalmazza.
* **Vizsgálat**: állapotfigyelő mintavételek menüpontban, amely segítségével a háttér-címkészletben lévő virtuálisgép-példányok rendelkezésre állásának ellenőrzése tartalmazza.

A további információkat az [Azure Resource Manager support for Load Balancer](load-balancer-arm.md) (Azure Resource Manager-támogatás a terheléselosztóhoz) című rész tartalmazza.

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>Állítsa be a Azure CLI környezetet az Azure Resource Manager használatára

Ebben a példában futtassa az Azure CLI-eszközeit egy PowerShell-parancsablakot. Olvashatóság és újbóli javítása érdekében használhatja a PowerShell parancsfájl-kezelési képességei, nem az Azure PowerShell-parancsmagok.

1. Ha még sosem használta az Azure parancssori felület, lásd: [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md) és kövesse az utasításokat a pontig, ahol ki kell választania az Azure-fiókja és -előfizetést.

2. Váltson Resource Manager módra, futtassa a **azure config mód** parancs:

    ```azurecli
    azure config mode arm
    ```

    Várt kimenet:

        info:    New mode is arm

3. Jelentkezzen be az Azure-ba, és az előfizetések listáját:

    ```azurecli
    azure login
    ```

4. A parancssorba írja be Azure hitelesítő adatait:

    ```azurecli
    azure account list
    ```

5. Válassza ki a használni kívánt előfizetést, és jegyezze fel az előfizetés-azonosító a következő lépésben használandó.

6. Az Azure parancssori felület parancsait való használatra PowerShell változók beállítása:

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

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Hozzon létre egy erőforráscsoportot, egy adott terheléselosztóhoz, egy virtuális hálózatot és alhálózatok

1. Hozzon létre egy erőforráscsoportot:

    ```azurecli
    azure group create $rgName $location
    ```

2. Hozzon létre egy terhelés-kiegyenlítő:

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Virtuális hálózat létrehozása:

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. A virtuális hálózatban két alhálózat létrehozása:

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Nyilvános IP-címeket az előtér-készlet létrehozása

1. A PowerShell-változó beállítása:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Hozzon létre egy nyilvános IP-címet az előtér-IP-címkészlet:

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > A terheléselosztó a nyilvános IP-cím a tartomány címkéjének teljesen minősített tartománynév (FQDN) használja. Ez a változás a klasszikus üzembe helyezési, amely használja a felhőalapú szolgáltatás nevezze el a terheléselosztó FQDN.
    >
    > Ebben a példában az FQDN-je *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Hozzon létre az előtér- és készletek

Ebben a szakaszban a következő IP-címkészletek létrehozása:
* Az előtér-IP-címkészlet, amely megkapja a bejövő hálózati forgalmat a terheléselosztón.
* A háttér IP-készlet, amennyiben az előtér-készlet elküldi a hálózati terheléselosztással rendelkező forgalmat.

1. A PowerShell-változó beállítása:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Hozzon létre egy előtér-IP-címkészletet, és rendelje hozzá azt az előző lépésben és a terheléselosztó a nyilvános IP-cím.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>A mintavételi, a NAT-szabályok létrehozása, és terheléselosztói szabály

Ez a példa a következő elemeket hozza létre:

* Ellenőrizze a kapcsolatot a 80-as TCP-port mintavételi szabályra.
* NAT-szabály RDP a 3389-es porton keresztül 3389-es port minden bejövő forgalom lefordítani.\*
* NAT-szabály lefordítani az összes porton érkező forgalom 3391 3389-es port a távoli asztal protokoll (RDP).\*
* olyan terheléselosztó szabályhoz minden bejövő forgalom 80-as porton a címkészletben levő a háttér-80-as portjához egyensúlyba.

\*NAT-szabályok társítva a terheléselosztó mögött egy adott virtuális gép példányt. A 3389-es port érkező hálózati forgalom érkezik, az adott virtuális gép és a portot, amelyet a NAT-szabály van társítva. A NAT-szabályhoz meg kell adnia egy protokollt (UDP vagy TCP). Mindkét protokollt nem lehet hozzárendelni ugyanazt a portot.

1. A PowerShell-változó beállítása:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. A mintavétel létrehozása.

    Az alábbi példakód létrehozza a TCP-vizsgálatot, amely ellenőrzi a kapcsolatot a háttér-TCP 80-as port 15 másodpercenként. Után két egymást követő hibák a háttér-erőforrás nem érhető el, jelöli.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Bejövő NAT-szabályok, amelyek lehetővé teszik a háttér-erőforrások RDP-kapcsolatok létrehozása:

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Hozzon létre terheléselosztási szabály, amely forgalmat küldeni a különböző háttér-portok, attól függően, hogy az előtér, amely a kérelmet kapott.

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Ellenőrizze a beállításokat:

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
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

Hálózati adaptert létrehozni, és rendelje hozzá őket mintavételek menüpontban, a NAT szabályok és a terheléselosztási szabály.

1. A PowerShell-változó beállítása:

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

2. Hozzon létre egy minden háttérbeli hálózati Adapterhez, és adja hozzá az IPv6 konfiguráció:

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>A háttér-Virtuálisgép-erőforrások létrehozása és csatolása az egyes hálózati adapterek

Virtuális gépek létrehozására, rendelkeznie kell egy tárfiókot. A terheléselosztást, a virtuális gépek kell lennie a rendelkezésre állási csoportok tagjai. Virtuális gépek létrehozásával kapcsolatos további információkért lásd: [egy Azure virtuális gép létrehozása a PowerShell használatával](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. A PowerShell-változó beállítása:

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Ez a példa a felhasználónevet és jelszót a virtuális gépek nem titkosított. Megfelelő gondossággal járjanak el, ha használja ezeket a hitelesítő adatokat nem titkosított. Egy biztonságosabb módszer a PowerShell hitelesítő adatok kezelésére, tekintse meg a [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) parancsmag.

2. A tárolási fiók és a rendelkezésre állási készlet létrehozása.

    Meglévő tárfiók a virtuális gépek létrehozásakor használható. A következő paranccsal létrehozhat egy új tárfiókot:

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. A rendelkezésre állási csoport létrehozása:

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. A társított hálózati adaptert a virtuális gépek létrehozására:

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Következő lépések

[Bevezetés a belső terheléselosztók konfigurálásába](load-balancer-get-started-ilb-arm-cli.md)  
[A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)  
[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)
