---
title: Csatlakozás a Kafkához virtuális hálózatok – Azure HDInsight használata
description: Ismerje meg, hogyan Kafka on HDInsight az Azure virtuális hálózaton keresztül közvetlenül csatlakozhat. Ismerje meg, hogyan csatlakozhat a Kafka fejlesztési VPN-átjáró használatával, vagy a helyi hálózatban lévő ügyfelek egy VPN-átjáróeszközt.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: da98873b133d69d78271494b991b67caea1d5a11
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283070"
---
# <a name="connect-to-kafka-on-hdinsight-through-an-azure-virtual-network"></a>Kafka on HDInsight az Azure virtuális hálózaton keresztül kapcsolódni

Ismerje meg, hogyan Kafka on HDInsight az Azure virtuális hálózaton keresztül közvetlenül csatlakozhat. Ez a dokumentum csatlakozásról ad információt, a Kafka a következő konfigurációk használatával:

* Egy a helyszíni hálózaton lévő erőforrásokból. Ez a kapcsolat jön létre a helyi hálózaton a VPN-eszközt (szoftveres vagy hardveres) használatával.
* A fejlesztési környezet egy VPN-szoftverügyfél használatával.

## <a name="architecture-and-planning"></a>Architektúra és tervezés

HDInsight nem engedélyezi a közvetlen kapcsolat, a Kafka, a nyilvános interneten keresztül. Ehelyett azt a Kafka-ügyfelek (előállítók és fogyasztók) kell használnia, a következő kapcsolat módszerek egyikét:

* Futtassa az ügyfél a HDInsight Kafka azonos virtuális hálózatban. Ez a konfiguráció használatban van a [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md) dokumentumot. Az ügyfél közvetlenül a HDInsight-fürt csomópontjainak vagy ugyanazon a hálózaton egy másik virtuális gépen futtatja.

* Egy magánhálózaton, például a helyszíni hálózat csatlakoztatása a virtuális hálózathoz. Ez a konfiguráció lehetővé teszi az ügyfelek a helyszíni hálózat közvetlenül együttműködni a Kafka. Ez a konfiguráció engedélyezi, hajtsa végre a következő feladatokat:

    1. Hozzon létre egy virtuális hálózatot.
    2. Hozzon létre egy VPN-átjáró, amely a site-to-site-konfigurációt használ. Az itt bemutatott konfiguráció kapcsolódik a helyszíni hálózat VPN-átjáróeszközt.
    3. Hozzon létre egy DNS-kiszolgáló a virtuális hálózatban.
    4. Konfigurálja a DNS-kiszolgáló, az egyes hálózatok közötti továbbítás.
    5. Hozzon létre egy Kafka HDInsight-fürtön a virtuális hálózatban.

    További információkért lásd: a [Kafka csatlakozhat egy helyszíni hálózatról](#on-premises) szakaszban. 

* Egyes gépek csatlakozhat a virtuális hálózathoz egy VPN-átjáró és a VPN-ügyfél. Ez a konfiguráció engedélyezi, hajtsa végre a következő feladatokat:

    1. Hozzon létre egy virtuális hálózatot.
    2. Hozzon létre egy VPN-átjáró, amely a pont – hely konfigurációt használ. Ez a konfiguráció Windows- és MacOS-ügyfelek is használható.
    3. Hozzon létre egy Kafka HDInsight-fürtön a virtuális hálózatban.
    4. Konfigurálja a Kafka IP hirdetési célból. Ez a konfiguráció lehetővé teszi az ügyfél broker segítségével szeretne csatlakozni, IP-címek tartománynevek helyett.
    5. Töltse le és használja a VPN-ügyfél a fejlesztői rendszeren.

    További információkért lásd: a [Kafka az egy VPN-ügyfél csatlakozhat](#vpnclient) szakaszban.

    > [!WARNING]
    > Ez a konfiguráció csak a következő korlátozások miatt ajánlott fejlesztési célokra:
    >
    > * Minden egyes ügyfélnek csatlakoznia kell egy VPN-szoftverügyfél használatával.
    > * A VPN-ügyfél nem továbbítja névfeloldási a virtuális hálózathoz, így az IP-címek Kafka kommunikálni kell használnia. Integrációs csomaggal folytatott kommunikációhoz a Kafka-fürt a további konfigurálást igényel.

A HDInsight segítségével a virtuális hálózat további információkért lásd: [HDInsight kiterjesztése az Azure-beli virtuális hálózatok használatával](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Csatlakozás a Kafkához egy a helyszíni hálózatból

Kafka-fürt, amely kommunikál a helyszíni hálózattal létrehozásához kövesse a [HDInsight csatlakoztatása a helyszíni hálózathoz](./../connect-on-premises-network.md) dokumentumot.

> [!IMPORTANT]
> A HDInsight-fürt létrehozásakor válassza ki a __Kafka__ fürt típusa.

Ezeket a lépéseket hozza létre a következő konfigurációt:

* Azure Virtual Network
* Site-to-site VPN gateway
* Az Azure Storage-fiók (a HDInsight által használt)
* Kafka on HDInsight

Annak ellenőrzéséhez, hogy a Kafka-ügyfél csatlakozhat a fürthöz a helyszíni, kövesse a [példa: Python-kliens](#python-client) szakaszban.

## <a id="vpnclient"></a> Csatlakozás a Kafka-VPN-ügyfél

Ebben a szakaszban a lépések segítségével hozzon létre a következő konfigurációt:

* Azure Virtual Network
* Pont – hely VPN-átjáró
* Az Azure Storage-fiók (a HDInsight által használt)
* Kafka on HDInsight

1. Kövesse a [önaláírt tanúsítványok használata pont – hely kapcsolatok használata](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) dokumentumot. Ez a dokumentum az átjáró szükséges tanúsítványokat hoz létre.

2. Nyisson meg egy PowerShell-parancssort, és jelentkezzen be az Azure-előfizetéshez az alábbi kód használatával:

    ```powershell
    Connect-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. A következő kód használatával hozzon létre a konfigurációs adatokat tartalmazó változókat:

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

4. A következő kód használatával az Azure-erőforráscsoportot és a virtuális hálózat létrehozása:

    ```powershell
    # Create the resource group that contains everything
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create the subnet configuration
    $defaultSubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -AddressPrefix $defaultSubnetPrefix
    $gatewaySubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -AddressPrefix $gatewaySubnetPrefix

    # Create the subnet
    New-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AddressPrefix $networkAddressPrefix `
        -Subnet $defaultSubnetConfig, $gatewaySubnetConfig

    # Get the network & subnet that were created
    $network = Get-AzureRmVirtualNetwork -Name $networkName `
        -ResourceGroupName $resourceGroupName
    $gatewaySubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $gatewaySubnetName `
        -VirtualNetwork $network
    $defaultSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $defaultSubnetName `
        -VirtualNetwork $network

    # Set a dynamic public IP address for the gateway subnet
    $gatewayPublicIp = New-AzureRmPublicIpAddress -Name $gatewayPublicIpName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -AllocationMethod Dynamic
    $gatewayIpConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $gatewayIpConfigName `
        -Subnet $gatewaySubnet `
        -PublicIpAddress $gatewayPublicIp

    # Get the certificate info
    # Get the full path in case a relative path was passed
    $rootCertFile = Get-ChildItem $rootCert
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($rootCertFile)
    $certBase64 = [System.Convert]::ToBase64String($cert.RawData)
    $p2sRootCert = New-AzureRmVpnClientRootCertificate -Name $vpnRootCertName `
        -PublicCertData $certBase64

    # Create the VPN gateway
    New-AzureRmVirtualNetworkGateway -Name $vpnName `
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
    > A folyamat befejezése több percet is igénybe vehet.

5. Az Azure Storage-fiókot és a blob-tároló létrehozásához használja a következő kódot:

    ```powershell
    # Create the storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageName `
        -Type Standard_GRS `
        -Location $location

    # Get the storage account keys and create a context
    $defaultStorageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName `
        -Name $storageName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageName `
        -StorageAccountKey $defaultStorageKey

    # Create the default storage container
    New-AzureStorageContainer -Name $defaultContainerName `
        -Context $storageContext
    ```

6. A HDInsight-fürt létrehozásához használja a következő kódot:

    ```powershell
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
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
  > Ez a folyamat körülbelül 15 percet vesz igénybe befejezéséhez.

### <a name="configure-kafka-for-ip-advertising"></a>Kafka IP hirdetés konfigurálása

Alapértelmezés szerint Zookeeper-ügyfelek a Kafka-közvetítőkhöz tartomány nevét adja vissza. Ez a konfiguráció nem működik a VPN szoftver-ügyfél, a névfeloldást a virtuális hálózatban található entitások esetében nem használható. Ehhez a konfigurációhoz a következő lépések segítségével Kafka meghirdetése tartománynevek helyett IP-címek konfigurálása:

1. Egy böngészőben nyissa meg https://CLUSTERNAME.azurehdinsight.net. Cserélje le __CLUSTERNAME__ a Kafka on HDInsight-fürt nevére.

    Amikor a rendszer kéri, használja a HTTPS-felhasználónevet és jelszót a fürthöz. Az Ambari webes felhasználói Felületet, a fürt akkor jelenik meg.

2. A Kafka információ megtekintéséhez jelölje ki __Kafka__ a bal oldali listából.

    ![A Kafka lista kiemelésével](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Kafka-konfigurációt megtekintéséhez jelölje ki __Configs__ felső közepén.

    ![A Kafka Configs hivatkozások](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Keresése a __kafka-env__ konfigurációs, adja meg `kafka-env` a a __szűrő__ a jobb felső sarokban található.

    ![Kafka-konfigurációt, a kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Kafka meghirdetése IP-címek konfigurálásához adja hozzá a következő szöveget a alján a __kafka-boríték-template__ mező:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. A Kafka figyel-kapcsolat konfigurálásához írja be a `listeners` a a __szűrő__ a jobb felső sarokban található.

7. Figyeljen az összes hálózati adapteren Kafka konfigurálásához módosítsa az értéket a __figyelői__ mezőt `PLAINTEXT://0.0.0.0:9092`.

8. A konfigurációs módosítások mentéséhez használja a __mentése__ gombra. Adja meg a módosításokat leíró szöveges üzenetet. Válassza ki __OK__ után a rendszer mentette a módosításokat.

    ![Mentés gomb konfigurálása](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Hibák a Kafka újraindításához használja a __szolgáltatás műveletek__ gombra, majd __karbantartási mód bekapcsolása__. Kattintson az OK gombra a művelet végrehajtásához.

    ![Szolgáltatási művelet, amely a kiemelt karbantartási bekapcsolása](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Indítsa újra a Kafka, használja a __indítsa újra a__ gombra, majd __indítsa újra az összes érintett__. Erősítse meg az újraindítást, és használja a __OK__ gombra a művelet befejeződése után.

    ![Indítsa újra az összes érintett újraindítás gomb kiemelésével](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. Tiltsa le a karbantartási mód, használja a __szolgáltatás műveletek__ gombra, majd __kapcsolja ki karbantartási módba__. Válassza ki **OK** végrehajtani a műveletet.

### <a name="connect-to-the-vpn-gateway"></a>A VPN-átjárón

A VPN-átjáró csatlakozni, használja a __csatlakozás az Azure__ szakaszában a [pont – hely kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) dokumentumot.

## <a id="python-client"></a> Példa: Python-ügyféllel

A Kafka, a kapcsolat ellenőrzéséhez használja a következő lépések létrehozása és futtatása a Python producer és consumer:

1. Használja a következő módszerek egyikét a teljesen minősített tartománynevét (FQDN) és IP-címek, a Kafka-fürt csomópontjainak beolvasásához:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

    Ez a parancsfájl feltételezi, hogy `$resourceGroupName` az Azure-erőforráscsoportot, amely tartalmazza a virtuális hálózat neve.

    Mentse a visszakapott információk használható a következő lépésben.

2. A következők használatával telepítheti a [kafka-python](http://kafka-python.readthedocs.io/) ügyfél:

        pip install kafka-python

3. Adatokat küldeni a Kafka, használja a következő Python-kódban:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  # NOTE: you don't need the full list of worker nodes, just one or two.
  producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Cserélje le a `'kafka_broker'` -címekről visszakapott bejegyzések ebben a szakaszban az 1. lépés:

    * Ha használ egy __szoftver VPN-ügyfél__, cserélje le a `kafka_broker` bejegyzéseket a feldolgozó csomópontok IP-címét.

    * Ha rendelkezik __engedélyezve van egy egyéni DNS-kiszolgálón keresztül névfeloldás__, cserélje le a `kafka_broker` bejegyzéseket a feldolgozó csomópontok teljes Tartománynevét.

    > [!NOTE]
    > Ez a kód elküldi a karakterlánc `test message` témakörbe `testtopic`. A HDInsight alatt futó Kafka használható alapértelmezett konfigurációt, hogy a témakör létrehozása, ha még nem létezik.

4. Az üzenetek beolvasásához a Kafka, a következő Python-kód használatával:

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

    Cserélje le a `'kafka_broker'` -címekről visszakapott bejegyzések ebben a szakaszban az 1. lépés:

    * Ha használ egy __szoftver VPN-ügyfél__, cserélje le a `kafka_broker` bejegyzéseket a feldolgozó csomópontok IP-címét.

    * Ha rendelkezik __engedélyezve van egy egyéni DNS-kiszolgálón keresztül névfeloldás__, cserélje le a `kafka_broker` bejegyzéseket a feldolgozó csomópontok teljes Tartománynevét.

## <a name="next-steps"></a>További lépések

A HDInsight használatával és a virtuális hálózat további információkért lásd: a [kiterjesztése az Azure HDInsight használata az Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md) dokumentumot.

További információ az Azure Virtual Networkhöz pont – hely VPN-átjáró való létrehozását a következő dokumentumokban talál:

* [Az Azure portal használatával pont – hely kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Azure PowerShell-lel pont – hely kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

A HDInsight-beli Kafka használatával kapcsolatos további információk a következő dokumentumokban találhatók:

* [A HDInsighton futó Kafka használatának első lépései](apache-kafka-get-started.md)
* [A HDInsight alatt futó Kafka-tükrözés használata](apache-kafka-mirroring.md)
