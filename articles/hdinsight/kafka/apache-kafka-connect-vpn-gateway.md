---
title: Csatlakozás a Kafkához virtuális hálózatok használatával – Azure HDInsight
description: Ismerje meg, hogyan csatlakozhat közvetlenül a Kafka a HDInsight egy Azure virtuális hálózaton keresztül. Ismerje meg, hogyan csatlakozhat a Kafka-hoz a fejlesztői ügyfelekvpn-átjáróhasználatával, vagy a helyszíni hálózat ügyfeleitől VPN-átjáróeszköz használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 36ff0d5f1fc96b2013555d37a869ebf629a22be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272122"
---
# <a name="connect-to-apache-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Csatlakozás az Apache Kafkához HDInsight-on egy Azure-beli virtuális hálózaton keresztül

Ismerje meg, hogyan csatlakozhat közvetlenül az Apache Kafkához a HDInsight-on egy Azure virtuális hálózaton keresztül. Ez a dokumentum a Kafkához való csatlakozással kapcsolatos információkat a következő konfigurációk használatával tartalmazza:

* A helyszíni hálózat erőforrásaiból. Ez a kapcsolat a helyi hálózaton lévő VPN-eszköz (szoftver vagy hardver) használatával jön létre.
* VPN-szoftverügyfélt használó fejlesztői környezetből.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="architecture-and-planning"></a>Építészet és tervezés

A HDInsight nem engedélyezi a Kafka-hoz való közvetlen csatlakozást a nyilvános interneten keresztül. Ehelyett a Kafka ügyfeleknek (gyártóknak és fogyasztóknak) az alábbi csatlakozási módszerek egyikét kell használniuk:

* Futtassa az ügyfelet ugyanabban a virtuális hálózatban, mint a Kafka a HDInsight. Ez a konfiguráció a Start az [Apache Kafka a](apache-kafka-get-started.md) HDInsight-dokumentumban. Az ügyfél közvetlenül a HDInsight fürtcsomópontokon vagy ugyanazon a hálózaton lévő másik virtuális gépen fut.

* Csatlakoztasson egy magánhálózatot, például a helyszíni hálózatot a virtuális hálózathoz. Ez a konfiguráció lehetővé teszi, hogy a helyszíni hálózat ügyfelei közvetlenül együttműködjenek a Kafkával. A konfiguráció engedélyezéséhez hajtsa végre a következő feladatokat:

  1. Hozzon létre egy virtuális hálózatot.
  2. Hozzon létre egy VPN-átjárót, amely helyek közötti konfigurációt használ. A dokumentumban használt konfiguráció a helyszíni hálózat VPN-átjáróeszközéhez csatlakozik.
  3. Hozzon létre egy DNS-kiszolgálót a virtuális hálózatban.
  4. Konfigurálja az adattovábbítást az egyes hálózatok DNS-kiszolgálói között.
  5. Hozzon létre egy Kafka a HDInsight-fürt a virtuális hálózatban.

     További információ: Csatlakozás az [Apache Kafkához egy helyszíni hálózati szakaszból.](#on-premises)

* Egyes gépek csatlakoztatása a virtuális hálózathoz VPN-átjáró és VPN-ügyfél használatával. A konfiguráció engedélyezéséhez hajtsa végre a következő feladatokat:

  1. Hozzon létre egy virtuális hálózatot.
  2. Hozzon létre egy VPN-átjárót, amely pont-hely konfigurációt használ. Ez a konfiguráció Windows és MacOS ügyfelekkel egyaránt használható.
  3. Hozzon létre egy Kafka a HDInsight-fürt a virtuális hálózatban.
  4. Konfigurálja a Kafka-t az IP-hirdetésekhez. Ez a konfiguráció lehetővé teszi, hogy az ügyfél tartománynevek helyett közvetítőI IP-címekkel csatlakozzon.
  5. Töltse le és használja a VPN-ügyfelet a fejlesztői rendszeren.

     További információ: Csatlakozás az [Apache Kafkához VPN-ügyféllel](#vpnclient) című szakaszban.

     > [!WARNING]  
     > Ez a konfiguráció csak fejlesztési célokra ajánlott, mert a következő korlátozások:
     >
     > * Minden ügyfélnek VPN-szoftverügyfélen keresztül kell csatlakoznia.
     > * A VPN-ügyfél nem adja át a névfeloldási kérelmeket a virtuális hálózatnak, ezért az IP-címzést a Kafkával való kommunikációhoz kell használnia. Az IP-kommunikáció további konfigurációt igényel a Kafka-fürtön.

A HDInsight virtuális hálózatban való használatáról az [Azure HDInsight-fürtök virtuális hálózatának megtervezése](../hdinsight-plan-virtual-network-deployment.md)című témakörben talál további információt.

## <a name="connect-to-apache-kafka-from-an-on-premises-network"></a><a id="on-premises"></a>Csatlakozás az Apache Kafkához helyszíni hálózatról

Hozzon létre egy Kafka-fürt, amely kommunikál a helyszíni hálózat, kövesse a [hdinsight csatlakoztatása a helyszíni hálózati dokumentum lépéseit.](./../connect-on-premises-network.md)

> [!IMPORTANT]  
> A HDInsight-fürt létrehozásakor válassza ki a __Kafka__ fürttípusát.

Ezek a lépések a következő konfigurációt hozzák létre:

* Azure Virtual Network
* Helyek közötti VPN-átjáró
* Azure Storage-fiók (a HDInsight használja)
* Kafka on HDInsight

Annak ellenőrzéséhez, hogy egy Kafka-ügyfél a helyszíni csatlakozáshoz csatlakozhat-e a fürthöz, kövesse a [Példa: Python-ügyfél](#python-client) szakasz lépéseit.

## <a name="connect-to-apache-kafka-with-a-vpn-client"></a><a id="vpnclient"></a>Csatlakozás az Apache Kafkához VPN-ügyféllel

Az ebben a szakaszban ismertetett lépésekkel hozza létre a következő konfigurációt:

* Azure Virtual Network
* Pont-hely VPN átjáró
* Azure storage-fiók (a HDInsight használja)
* Kafka on HDInsight

1. Kövesse a [Pont-hely kapcsolatok önaláírt tanúsítványainak használata](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) című dokumentum lépéseit. Ez a dokumentum létrehozza az átjáróhoz szükséges tanúsítványokat.

2. Nyisson meg egy PowerShell-kérdést, és a következő kód dal jelentkezzen be az Azure-előfizetésbe:

    ```powershell
    Connect-AzAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzSubscription -SubscriptionName "name of your subscription"
    ```

3. A következő kóddal konfigurációs adatokat tartalmazó változókat hozhat létre:

    ```powershell
    # Prompt for generic information
    $resourceGroupName = Read-Host "What is the resource group name?"
    $baseName = Read-Host "What is the base name? It is used to create names for resources, such as 'net-basename' and 'kafka-basename':"
    $location = Read-Host "What Azure Region do you want to create the resources in?"
    $rootCert = Read-Host "What is the file path to the root certificate? It is used to secure the VPN gateway."

    # Prompt for HDInsight credentials
    $adminCreds = Get-Credential -Message "Enter the HTTPS user name and password for the HDInsight cluster" -UserName "admin"
    $sshCreds = Get-Credential -Message "Enter the SSH user name and password for the HDInsight cluster" -UserName "sshuser"

    # Names for Azure resources
    $networkName = "net-$baseName"
    $clusterName = "kafka-$baseName"
    $storageName = "store$baseName" # Can't use dashes in storage names
    $defaultContainerName = $clusterName
    $defaultSubnetName = "default"
    $gatewaySubnetName = "GatewaySubnet"
    $gatewayPublicIpName = "GatewayIp"
    $gatewayIpConfigName = "GatewayConfig"
    $vpnRootCertName = "rootcert"
    $vpnName = "VPNGateway"

    # Network settings
    $networkAddressPrefix = "10.0.0.0/16"
    $defaultSubnetPrefix = "10.0.0.0/24"
    $gatewaySubnetPrefix = "10.0.1.0/24"
    $vpnClientAddressPool = "172.16.201.0/24"

    # HDInsight settings
    $HdiWorkerNodes = 4
    $hdiVersion = "3.6"
    $hdiType = "Kafka"
    ```

4. Az Azure-erőforráscsoport és a virtuális hálózat létrehozásához használja a következő kódot:

    ```powershell
    # Create the resource group that contains everything
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzVirtualNetworkGateway -Name $vpnName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -IpConfigurations $gatewayIpConfig `
        -GatewayType Vpn `
        -VpnType RouteBased `
        -EnableBgp $false `
        -GatewaySku Standard `
        -VpnClientAddressPool $vpnClientAddressPool `
        -VpnClientRootCertificates $p2sRootCert
    ```

    > [!WARNING]  
    > A folyamat több percig is eltarthat, amíg ez a folyamat befejeződik.

5. Az alábbi kód dalhoz az Azure Storage-fiók és a blob tároló:

    ```powershell
    # Create the storage account
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -SkuName Standard_GRS `
        -Location $location `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. A HDInsight-fürt létrehozásához használja az alábbi kódot:

    ```powershell
    # Create the HDInsight cluster
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $hdiWorkerNodes `
        -ClusterType $hdiType `
        -OSType Linux `
        -Version $hdiVersion `
        -HttpCredential $adminCreds `
        -SshCredential $sshCreds `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageKey `
        -DefaultStorageContainer $defaultContainerName `
        -DisksPerWorkerNode 2 `
        -VirtualNetworkId $network.Id `
        -SubnetName $defaultSubnet.Id
    ```

   > [!WARNING]  
   > Ez a folyamat körülbelül 15 percet vesz igénybe.

### <a name="configure-kafka-for-ip-advertising"></a>A Kafka konfigurálása IP-hirdetésekhez

Alapértelmezés szerint az Apache Zookeeper visszaadja a Kafka brókerek tartománynevét az ügyfeleknek. Ez a konfiguráció nem működik a VPN-szoftverügyféllel, mivel nem használhatja a névfeloldást a virtuális hálózat entitásaihoz. Ehhez a konfigurációhoz az alábbi lépésekkel konfigurálhatja a Kafka-t az IP-címek tartománynevek helyett történő hirdetésére:

1. Egy böngészőben nyissa meg a következőt: `https://CLUSTERNAME.azurehdinsight.net`. Cserélje `CLUSTERNAME` le a HDInsight-fürt Kafka nevét.

    Amikor a rendszer kéri, használja a fürt HTTPS-felhasználónevét és jelszavát. Megjelenik a fürt Ambari webfelhasználói felülete.

2. A Kafka-val kapcsolatos információk megtekintéséhez válassza a __Kafka__ elemet a bal oldali listából.

    ![Szolgáltatási lista kiemelt Kafkával](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. A Kafka-konfiguráció megtekintéséhez válassza a __Konfigurációk__ elemet a felső középről.

    ![Apache Ambari szolgáltatások konfigurációja](./media/apache-kafka-connect-vpn-gateway/select-kafka-config1.png)

4. A __kafka-env__ konfiguráció megkereséséhez írja be `kafka-env` a jobb felső __sarokszűrő__ mezőjét.

    ![Kafka konfiguráció, kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Ha a Kafkát az IP-címek hirdetésére szeretné beállítani, adja hozzá a következő szöveget a __kafka-env-template__ mező aljához:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. A Kafka által figyelt összeköttetés konfigurálásához írja be `listeners` a jobb felső __sarokszűrő__ mezőjébe.

7. Ha azt szeretné, hogy a Kafka az összes hálózati `PLAINTEXT://0.0.0.0:9092`csatolón figyelje a figyelést, módosítsa a __figyelők__ mező értékét a értékre.

8. A konfigurációs módosítások mentéséhez használja a __Mentés__ gombot. Írjon be egy szöveges üzenetet a módosításokról. A módosítások mentése után válassza az __OK__ gombot.

    ![Apache Ambari menteni konfiguráció](./media/apache-kafka-connect-vpn-gateway/save-configuration-button.png)

9. A Kafka újraindításakor előforduló hibák elkerülése érdekében használja a __Szolgáltatási műveletek__ gombot, és válassza a Karbantartási __mód bekapcsolása lehetőséget.__ A művelet végrehajtásához válassza az OK gombot.

    ![Szolgáltatási műveletek, kiemelve a karbantartás bekapcsolva](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. A Kafka újraindításához használja az __Újraindítás__ gombot, és válassza __az Újraindítás minden érintettet.__ Erősítse meg az újraindítást, majd a művelet befejezése után használja az __OK__ gombot.

    ![Újraindítás gomb az összes érintett érintett újraindításával kiemelve](./media/apache-kafka-connect-vpn-gateway/restart-required-button.png)

11. A karbantartási mód letiltásához használja a __Szervizműveletek__ gombot, és válassza __a Karbantartási mód kikapcsolása__lehetőséget. A művelet végrehajtásához válassza az **OK** gombot.

### <a name="connect-to-the-vpn-gateway"></a>Csatlakozás a VPN-átjáróhoz

A VPN-átjáróhoz való csatlakozáshoz használja a Csatlakozás az __Azure-hoz__ szakasza a [Pont-hely kapcsolat dokumentum konfigurálása.](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect)

## <a name="example-python-client"></a><a id="python-client"></a>Példa: Python-ügyfél

A Kafka-val való kapcsolat érvényesítéséhez az alábbi lépésekkel hozzon létre és futtasson egy Python-gyártót és -fogyasztót:

1. A Kafka-fürt csomópontjainak teljesen minősített tartománynevét (FQDN) és IP-címét az alábbi módszerek egyikével szerezheti be:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Ez a parancsfájl `$resourceGroupName` feltételezi, hogy a virtuális hálózatot tartalmazó Azure-erőforráscsoport neve.

    Mentse a visszaadott adatokat a következő lépésekben való használatra.

2. A [kafka-python](https://kafka-python.readthedocs.io/) ügyfél telepítéséhez használja az alábbiakat:

    ```bash
    pip install kafka-python
    ```

3. Ha adatokat szeretne küldeni a Kafkának, használja a következő Python-kódot:

   ```python
   from kafka import KafkaProducer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # NOTE: you don't need the full list of worker nodes, just one or two.
   producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
   for _ in range(50):
      producer.send('testtopic', b'test message')
   ```

    Cserélje `'kafka_broker'` le a bejegyzéseket az 1.

   * Ha __szoftveres VPN-ügyfelet__használ, cserélje le a `kafka_broker` bejegyzéseket a munkavégző csomópontok IP-címére.

   * Ha __egyéni DNS-kiszolgálón keresztül engedélyezte a névfeloldást,__ cserélje le a `kafka_broker` bejegyzéseket a munkavégző csomópontok teljes tartománynevére.

     > [!NOTE]
     > Ez a kód `test message` elküldi `testtopic`a karakterláncot a témakörnek. A Kafka alapértelmezett konfigurációja a HDInsight-on a témakör létrehozása, ha nem létezik.

4. A Kafka üzeneteinek lekéréséhez használja a következő Python-kódot:

   ```python
   from kafka import KafkaConsumer
   # Replace the `ip_address` entries with the IP address of your worker nodes
   # Again, you only need one or two, not the full list.
   # Note: auto_offset_reset='earliest' resets the starting offset to the beginning
   #       of the topic
   consumer = KafkaConsumer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'],auto_offset_reset='earliest')
   consumer.subscribe(['testtopic'])
   for msg in consumer:
     print (msg)
   ```

    Cserélje `'kafka_broker'` le a bejegyzéseket az 1.

    * Ha __szoftveres VPN-ügyfelet__használ, cserélje le a `kafka_broker` bejegyzéseket a munkavégző csomópontok IP-címére.

    * Ha __egyéni DNS-kiszolgálón keresztül engedélyezte a névfeloldást,__ cserélje le a `kafka_broker` bejegyzéseket a munkavégző csomópontok teljes tartománynevére.

## <a name="next-steps"></a>További lépések

A HDInsight virtuális hálózattal való használatáról további információt az [Azure HDInsight-fürtök virtuális hálózati üzembe helyezésének megtervezése](../hdinsight-plan-virtual-network-deployment.md) című dokumentumban talál.

Az Azure virtuális hálózat point-to-site VPN-átjáróval való létrehozásáról az alábbi dokumentumokban talál további információt:

* [Pont-hely közötti kapcsolat konfigurálása az Azure Portal használatával](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Pont-hely kapcsolat konfigurálása az Azure PowerShell használatával](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Az Apache Kafka HDInsight-on végzett együttműködésével kapcsolatos további tudnivalókért tekintse meg az alábbi dokumentumokat:

* [Ismerkedés az Apache Kafkával a HDInsighton](apache-kafka-get-started.md)
* [Tükrözés használata az Apache Kafkával a HDInsighton](apache-kafka-mirroring.md)
