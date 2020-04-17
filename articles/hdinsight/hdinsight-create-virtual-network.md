---
title: Virtuális hálózatok létrehozása Az Azure HDInsight-fürtökhöz
description: Ismerje meg, hogyan hozhat létre egy Azure virtuális hálózatot a HDInsight más felhőbeli erőforrásokhoz vagy az adatközpontban lévő erőforrásokhoz való csatlakoztatásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 0c7791d43ffbbc13ab151362c5c3026ebbdb0d34
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531016"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Virtuális hálózatok létrehozása Az Azure HDInsight-fürtökhöz

Ez a cikk példákat és kódmintákat tartalmaz az [Azure virtuális hálózatok](../virtual-network/virtual-networks-overview.md)létrehozásához és konfigurálásához. Az Azure HDInsight-fürtökkel való használat. Részletes példák at mutatnak be a hálózati biztonsági csoportok (NSG-k) létrehozására és a DNS konfigurálására.

A virtuális hálózatok Azure HDInsight-mal való használatáról az [Azure HDInsight virtuális hálózatának megtervezése](hdinsight-plan-virtual-network-deployment.md)című témakörben talál háttér-információkat.

## <a name="prerequisites-for-code-samples-and-examples"></a>A kódminták és példák előfeltételei

A cikkben szereplő kódminták végrehajtása előtt ismernie kell a TCP/IP-hálózatokat. Ha nem ismeri a TCP/IP-hálózatot, konzultáljon valakivel, mielőtt módosítaná az éles hálózatokat.

A cikkben szereplő minták további előfeltételei a következő elemek:

* Ha PowerShellt használ, telepítenie kell az [AZ modult.](https://docs.microsoft.com/powershell/azure/overview)
* Ha az Azure CLI-t szeretné használni, és még nem telepítette, olvassa el [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

> [!IMPORTANT]  
> Ha részletes útmutatást keres a HDInsight helyszíni hálózathoz való csatlakoztatásához egy Azure virtuális hálózaton keresztül, tekintse meg a [HDInsight csatlakoztatása a helyszíni hálózati dokumentumhoz című témakört.](connect-on-premises-network.md)

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Példa: hálózati biztonsági csoportok a HDInsight segítségével

Ebben a szakaszban látható példák bemutatják, hogyan hozhat létre hálózati biztonsági csoportszabályokat. A szabályok lehetővé teszik a HDInsight számára, hogy kommunikáljon az Azure felügyeleti szolgáltatásokkal. A példák használata előtt módosítsa az IP-címeket, hogy azok megfeleljenek a használt Azure-régióhoz. Ezeket az információkat a [HDInsight felügyeleti IP-címében](hdinsight-management-ip-addresses.md)találja.

### <a name="azure-resource-management-template"></a>Azure erőforrás-kezelési sablon

A következő Erőforrás-kezelési sablon létrehoz egy virtuális hálózatot, amely korlátozza a bejövő forgalmat, de lehetővé teszi a forgalmat a HDInsight által megkövetelt IP-címekről. Ez a sablon egy HDInsight-fürtöt is létrehoz a virtuális hálózatban.

* [Biztonságos Azure virtuális hálózat és HDInsight Hadoop-fürt üzembe helyezése](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

A következő PowerShell-parancsfájl segítségével hozzon létre egy virtuális hálózatot, amely korlátozza a bejövő forgalmat, és lehetővé teszi a forgalmat az ÉSZAK-európai régió IP-címeiből.

> [!IMPORTANT]  
> Módosítsa az `hdirule1` IP-címeket, és `hdirule2` ebben a példában, hogy megfeleljen az Azure-régióban használt. Ezt az információt [HDInsight felügyeleti IP-címek](hdinsight-management-ip-addresses.md)találhatók.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

Ez a példa bemutatja, hogyan adhat hozzá szabályokat a szükséges IP-címek bejövő forgalom engedélyezéséhez. Nem tartalmaz olyan szabályt, amely korlátozza a más forrásokból érkező bejövő hozzáférést. A következő kód bemutatja, hogyan lehet engedélyezni az SSH-hozzáférést az internetről:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

A következő lépésekkel hozzon létre egy virtuális hálózatot, amely korlátozza a bejövő forgalmat, de lehetővé teszi a forgalmat az IP-címek hdinsight által megkövetelt.

1. A következő paranccsal hozzon létre `hdisecure`egy új hálózati biztonsági csoportot, melynek neve . Cserélje `RESOURCEGROUP` le az Azure virtuális hálózatot tartalmazó erőforráscsoportra. Cserélje `LOCATION` le arra a helyre (régióra), amelyben a csoportot létrehozták.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    A csoport létrehozása után információkat kap az új csoportról.

2. Az alábbi használatával hozzáadhat szabályokat az új hálózati biztonsági csoporthoz, amely lehetővé teszi az Azure HDInsight állapot- és felügyeleti szolgáltatás 443-as portján érkező kommunikációt. Cserélje `RESOURCEGROUP` le az Azure virtuális hálózatot tartalmazó erőforráscsoport nevére.

    > [!IMPORTANT]  
    > Módosítsa az `hdirule1` IP-címeket, és `hdirule2` ebben a példában, hogy megfeleljen az Azure-régióban használt. Ezeket az információkat a [HDInsight felügyeleti IP-címében](hdinsight-management-ip-addresses.md)találja.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. A hálózati biztonsági csoport egyedi azonosítójának beolvasásához használja a következő parancsot:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Ez a parancs a következő höz hasonló értéket ad vissza:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. A következő paranccsal alkalmazhatja a hálózati biztonsági csoportot egy alhálózatra. Cserélje `GUID` le `RESOURCEGROUP` a és az értékeket az előző lépésből visszaadott értékekre. Cserélje `VNETNAME` `SUBNETNAME` le a létrehozni kívánt virtuális hálózat és alhálózat nevét.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Miután ez a parancs befejeződött, telepítheti a HDInsight-ot a virtuális hálózatba.

Ezek a lépések csak nyílt hozzáférést biztosít a HDInsight állapot- és felügyeleti szolgáltatáshoz az Azure-felhőben. A HDInsight-fürthöz a virtuális hálózaton kívülről történő bármely más hozzáférés le van tiltva. A virtuális hálózaton kívülről történő hozzáférés engedélyezéséhez további hálózati biztonsági csoportszabályokat kell hozzáadnia.

A következő kód bemutatja, hogyan lehet engedélyezni az SSH-hozzáférést az internetről:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a>Példa: DNS-konfiguráció

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Névfeloldás a virtuális hálózat és a csatlakoztatott helyszíni hálózat között

Ez a példa a következő feltételezéseket teszi:

* Rendelkezik egy Azure virtuális hálózat, amely csatlakozik egy helyszíni hálózat vpn-átjáró használatával.

* A virtuális hálózat egyéni DNS-kiszolgálója Linux ot vagy Unix-ot futtat operációs rendszerként.

* [A Bind](https://www.isc.org/downloads/bind/) telepítve van az egyéni DNS-kiszolgálón.

A virtuális hálózat egyéni DNS-kiszolgálóján:

1. Az Azure PowerShell vagy az Azure CLI használatával keresse meg a virtuális hálózat DNS-utótagot:

    Cserélje `RESOURCEGROUP` le a virtuális hálózatot tartalmazó erőforráscsoport nevére, majd írja be a parancsot:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. A virtuális hálózat egyéni DNS-kiszolgálóján használja a következő `/etc/bind/named.conf.local` szöveget a fájl tartalmaként:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Cserélje `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` le az értéket a virtuális hálózat DNS-utótagjára.

    Ez a konfiguráció a virtuális hálózat DNS-utótagjára vonatkozó összes DNS-kérelmet az Azure rekurzív feloldójához irányítja.

1. A virtuális hálózat egyéni DNS-kiszolgálóján használja a következő `/etc/bind/named.conf.options` szöveget a fájl tartalmaként:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Cserélje `10.0.0.0/16` le az értéket a virtuális hálózat IP-címtartományára. Ez a bejegyzés lehetővé teszi a névfeloldási kérelmek címeit ezen a tartományon belül.

    * Adja hozzá a helyszíni hálózat IP-címtartományát a `acl goodclients { ... }` szakaszhoz.  a bejegyzés lehetővé teszi a helyszíni hálózat erőforrásaitól érkező névfeloldási kérelmeket.
    
    * Cserélje le `192.168.0.1` az értéket a helyszíni DNS-kiszolgáló IP-címére. Ez a bejegyzés az összes többi DNS-kérelmet a helyszíni DNS-kiszolgálóra irányítja.

1. A konfiguráció használatához indítsa újra a Bind programot. Például: `sudo service bind9 restart`.

1. Feltételes továbbító hozzáadása a helyszíni DNS-kiszolgálóhoz. Konfigurálja úgy a feltételes továbbítót, hogy az 1.

    > [!NOTE]  
    > A DNS-szoftver dokumentációjában tájékozódhat a feltételes továbbító hozzáadásának konkrétumairól.

A lépések végrehajtása után bármelyik hálózat erőforrásaihoz teljesen minősített tartománynevek (FQDN) használatával csatlakozhat. Most már telepítheti a HDInsight-ot a virtuális hálózatra.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Névfeloldás két csatlakoztatott virtuális hálózat között

Ez a példa a következő feltételezéseket teszi:

* Két Azure virtuális hálózattal rendelkezik, amelyek VPN-átjáróval vagy társviszony-létesítéssel kapcsolódnak.

* Az egyéni DNS-kiszolgáló mindkét hálózaton Linux ot vagy Unix-ot futtat operációs rendszerként.

* [A Bind](https://www.isc.org/downloads/bind/) telepítve van az egyéni DNS-kiszolgálókon.

1. Az Azure PowerShell vagy az Azure CLI használatával mindkét virtuális hálózat DNS-utótagot keresheti meg:

    Cserélje `RESOURCEGROUP` le a virtuális hálózatot tartalmazó erőforráscsoport nevére, majd írja be a parancsot:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Használja a következő szöveget `/etc/bind/named.config.local` a fájl tartalmaként az egyéni DNS-kiszolgálón. Mindkét virtuális hálózat egyéni DNS-kiszolgálóján elkell készíteni ezt a módosítást.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Cserélje `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` le az értéket a __másik__ virtuális hálózat DNS-utótagjára. Ez a bejegyzés a távoli hálózat DNS-utótagjára vonatkozó kérelmeket a hálózat egyéni DNS-éhez irányítja.

3. Mindkét virtuális hálózat egyéni DNS-kiszolgálóin használja a következő `/etc/bind/named.conf.options` szöveget a fájl tartalmaként:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Cserélje `10.0.0.0/16` le `10.1.0.0/16` a és az értékeket a virtuális hálózatok IP-címtartományaira. Ez a bejegyzés lehetővé teszi, hogy az egyes hálózatok erőforrásai kéréseket intézjenek a DNS-kiszolgálóktól.

    Minden olyan kérelmet, amely nem a virtuális hálózatok DNS-utótagok (például microsoft.com) kezeli az Azure rekurzív feloldó.

4. A konfiguráció használatához indítsa újra a Bind programot. Például `sudo service bind9 restart` mindkét DNS-kiszolgálón.

A lépések végrehajtása után a teljesen minősített tartománynevek (FQDN) használatával csatlakozhat a virtuális hálózat erőforrásaihoz. Most már telepítheti a HDInsight-ot a virtuális hálózatra.

## <a name="next-steps"></a>További lépések

* A HDInsight helyszíni hálózathoz való csatlakozásra való konfigurálásának teljes példáját a [HDInsight csatlakoztatása helyszíni hálózathoz](./connect-on-premises-network.md)című témakörben található.
* Az Apache HBase-fürtök azure-beli virtuális hálózatokban történő konfigurálása az [Apache HBase-fürtök létrehozása a HDInsight szolgáltatásban az Azure Virtual Network ben című témakörben található.](hbase/apache-hbase-provision-vnet.md)
* Az Apache HBase georeplikáció konfigurálása az [Apache HBase fürtreplikáció beállítása az Azure virtuális hálózatokban](hbase/apache-hbase-replication.md)című témakörben található.
* Az Azure virtuális hálózatokkal kapcsolatos további információkért tekintse meg az [Azure virtuális hálózat áttekintését.](../virtual-network/virtual-networks-overview.md)

* A hálózati biztonsági csoportokról további információt a Hálózati biztonsági csoportok című [témakörben](../virtual-network/security-overview.md)talál.

* A felhasználó által definiált útvonalakról a [Felhasználó által definiált útvonalakról és az IP-továbbításról](../virtual-network/virtual-networks-udr-overview.md)talál további információt.
