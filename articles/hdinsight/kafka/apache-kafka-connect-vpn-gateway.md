---
title: Virtuális hálózatok - Azure HDInsight használatával Kafka csatlakozni |} Microsoft Docs
description: Útmutató a HDInsight az Azure virtuális hálózaton keresztül Kafka közvetlenül kapcsolódni. Megtudhatja, hogyan Kafka fejlesztési VPN-átjáró használatával, vagy a helyi hálózatban lévő ügyfelek használatával kapcsolódnak a VPN-átjáróeszközt.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/05/2018
ms.author: larryfr
ms.openlocfilehash: 1ddfbbb5efb73f968c9ef42396be45fce12ff47a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="connect-to-kafka-on-hdinsight-through-an-azure-virtual-network"></a>A HDInsight az Azure virtuális hálózaton keresztül Kafka kapcsolódni

Útmutató a HDInsight az Azure virtuális hálózaton keresztül Kafka közvetlenül kapcsolódni. Ez a dokumentum információkat biztosít Kafka csatlakozni a következő konfigurációk használatával:

* Az egy helyi hálózatán lévő erőforrásokat. Ezt a kapcsolatot a helyi hálózaton a VPN-eszköz (szoftveres vagy hardveres) használatával.
* A környezet a VPN szoftver ügyfél használatával.

## <a name="architecture-and-planning"></a>Architektúra és tervezése

A HDInsight nem engedélyezi a Kafka közvetlen kapcsolatra a nyilvános interneten keresztül. Ehelyett azt Kafka ügyfelek (létrehozói és felhasználói) kell használnia, a következő kapcsolat módszerek egyikét:

* Futtassa az ügyfelet a HDInsight a Kafka azonos virtuális hálózaton. Ez a konfiguráció szerepel a [indítsa el a HDInsight Apache Kafka](apache-kafka-get-started.md) dokumentum. Az ügyfél futtatja a közvetlenül a HDInsight-fürtcsomóponton, vagy egy másik virtuális gép ugyanazon a hálózaton.

* A magánhálózaton, például a helyszíni hálózathoz csatlakozni a virtuális hálózat. Ez a konfiguráció lehetővé teszi az ügyfelek a helyszíni hálózat Kafka közvetlenül együttműködni. Ahhoz, hogy ez a konfiguráció, a következő feladatokat:

    1. Hozzon létre egy virtuális hálózatot.
    2. Hozzon létre egy VPN-átjáró, amely webhelyek konfigurációt használ. Az ebben a dokumentumban használt konfigurációs csatlakozik a helyszíni hálózaton a VPN-átjáróeszközt.
    3. Hozzon létre egy DNS-kiszolgáló a virtuális hálózat.
    4. Konfigurálja a DNS-kiszolgáló minden hálózat között.
    5. A HDInsight Kafka telepítse a virtuális hálózat.

    További információkért lásd: a [Kafka csatlakozás egy helyszíni hálózatból](#on-premises) szakasz. 

* Az egyes gépek csatlakozni a virtuális hálózat VPN-átjáró és a VPN-ügyfél segítségével. Ahhoz, hogy ez a konfiguráció, a következő feladatokat:

    1. Hozzon létre egy virtuális hálózatot.
    2. Hozzon létre egy pont – hely konfigurációt használó VPN-átjáró. Ez a konfiguráció a Windows és a MacOS ügyfeleit használható.
    3. A HDInsight Kafka telepítse a virtuális hálózat.
    4. IP-hirdetési Kafka konfigurálása. Ez a konfiguráció lehetővé teszi, hogy az ügyfél történő csatlakozáshoz az IP-címzés tartománynevek helyett.
    5. Töltse le, és a fejlesztői rendszeren a VPN-ügyfél használja.

    További információkért lásd: a [Kafka egy VPN-ügyfél a kapcsolódás](#vpnclient) szakasz.

    > [!WARNING]
    > Ez a konfiguráció csak ajánlott fejlesztési célra miatt a következő korlátozások vonatkoznak:
    >
    > * Minden ügyfél szoftver VPN-ügyfél használatával kell csatlakoztatni.
    > * A VPN-ügyfél nem felel meg névfeloldási a virtuális hálózathoz, így IP-címzési Kafka kommunikálni kell használni. Integrációs csomaggal folytatott kommunikációhoz az Kafka fürtön további konfigurációt igényel.

A HDInsight eszközzel virtuális hálózatban további információkért lásd: [kiterjesztése HDInsight Azure virtuális hálózatok használatával](../hdinsight-extend-hadoop-virtual-network.md).

## <a id="on-premises"></a> Csatlakozás egy helyszíni hálózatból Kafka

A helyszíni hálózattal, melyekkel Kafka fürt létrehozásához kövesse a [a helyszíni hálózathoz való csatlakozás HDInsight](./../connect-on-premises-network.md) dokumentum.

> [!IMPORTANT]
> A HDInsight-fürt létrehozása esetén a __Kafka__ fürt típusa.

Ezeket a lépéseket hozza létre az alábbi konfigurációt:

* Azure Virtual Network
* Webhelyek közötti VPN átjáró
* Az Azure Storage-fiók (a HDInsight által használt)
* Kafka on HDInsight

Győződjön meg arról, hogy egy Kafka kapcsolódni tud a fürt a helyszíni, kövesse a lépéseket a a [példa: ügyfél Python](#python-client) szakasz.

## <a id="vpnclient"></a> A VPN-ügyfél Kafka csatlakozni

Ebben a szakaszban a lépések segítségével hozza létre a következő konfigurációt:

* Azure Virtual Network
* Pont-pont VPN-átjáró
* Az Azure Storage-fiók (a HDInsight által használt)
* Kafka on HDInsight

1. Kövesse a [pont – hely kapcsolatok önaláírt tanúsítványok használata](../../vpn-gateway/vpn-gateway-certificates-point-to-site.md) dokumentum. Ez a dokumentum az átjáró szükséges tanúsítványokat hoz létre.

2. Nyisson meg egy PowerShell-parancssorba, és jelentkezzen be az Azure-előfizetéshez az alábbi kód használatával:

    ```powershell
    Connect-AzureRmAccount
    # If you have multiple subscriptions, uncomment to set the subscription
    #Select-AzureRmSubscription -SubscriptionName "name of your subscription"
    ```

3. Konfigurációs adatokat tartalmazó változók létrehozásához használja a következő kódot:

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

4. Az Azure-erőforráscsoportot és a virtuális hálózat létrehozásához használja a következő kódot:

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

5. Az Azure-Tárfiókot és blob tároló létrehozásához használja a következő kódot:

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
  > Ez a folyamat mintegy 15 perc szükséges teljes.

### <a name="configure-kafka-for-ip-advertising"></a>IP-hirdetési Kafka konfigurálása

Alapértelmezés szerint Zookeeper az ügyfelek számára a Kafka brókerek tartomány nevét adja vissza. Ez a konfiguráció nem működik a VPN-, szoftver-ügyfél, a virtuális hálózat entitások névfeloldás nem használható. Ehhez a konfigurációhoz az alábbi lépések segítségével Kafka hivatkozik tartománynevek helyett IP-címek konfigurálása:

1. A webböngészőben nyissa https://CLUSTERNAME.azurehdinsight.net. Cserélje le __CLUSTERNAME__ HDInsight-fürt Kafka nevével.

    Amikor a rendszer kéri, használja a HTTPS-felhasználónevet és jelszót a fürthöz. Az Ambari webes felhasználói felületén, a fürt akkor jelenik meg.

2. Kafka adatok megtekintéséhez válassza ki a __Kafka__ a bal oldali listában.

    ![A kijelölt Kafka szolgáltatás lista](./media/apache-kafka-connect-vpn-gateway/select-kafka-service.png)

3. Válassza ki, ha Kafka konfigurációs __Configs__ felső közepén.

    ![Kafka Configs hivatkozások](./media/apache-kafka-connect-vpn-gateway/select-kafka-config.png)

4. Található a __kafka-env__ konfigurációs, adja meg `kafka-env` a a __szűrő__ jobb felső részén található.

    ![Kafka konfigurációját, kafka-env](./media/apache-kafka-connect-vpn-gateway/search-for-kafka-env.png)

5. Konfigurálja az IP-címek hivatkozik Kafka, vegye fel a következő szöveg alsó részén található a __kafka-env-sablon__ mező:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. A felület, amely figyeli Kafka konfigurálásához írja be a következőt `listeners` a a __szűrő__ jobb felső részén található.

7. Minden hálózati interfészen figyelésére Kafka konfigurálásához módosítsa a __figyelői__ mezőről `PLAINTEXT://0.0.0.0:9092`.

8. A konfigurációs módosítások mentéséhez használja a __mentése__ gombra. Adja meg a módosítások leíró üzenet. Válassza ki __OK__ a módosítások mentése után.

    ![Mentse a konfigurációs gomb](./media/apache-kafka-connect-vpn-gateway/save-button.png)

9. Megakadályozhatja, hogy hibák Kafka újraindításakor a __szolgáltatás műveletek__ gombra, majd az __kapcsolja be a karbantartási mód__. Kattintson az OK gombra a művelet végrehajtásához.

    ![Szolgáltatási művelet, amely a kijelölt karbantartási bekapcsolása](./media/apache-kafka-connect-vpn-gateway/turn-on-maintenance-mode.png)

10. Indítsa újra a Kafka, használja a __indítsa újra a__ gombra, majd az __indítsa újra az összes érintett__. Erősítse meg az újraindítás, és használja a __OK__ gombra kattint, a művelet befejeződése után.

    ![Indítsa újra a gomb, újraindítással minden érintett](./media/apache-kafka-connect-vpn-gateway/restart-button.png)

11. A karbantartási mód letiltásához használja a __szolgáltatás műveletek__ gombra, majd az __kapcsolja ki a karbantartási mód__. Válassza ki **OK** a művelet végrehajtásához.

### <a name="connect-to-the-vpn-gateway"></a>A VPN-átjárón

Ha csatlakozni szeretne a VPN-átjáró, használja a __csatlakozás az Azure-bA__ szakasza a [egy pont – hely kapcsolat beállítása](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md#connect) dokumentum.

## <a id="python-client"></a> Példa: Python ügyfél

Kafka kapcsolat ellenőrzéséhez tegye a következőket hozhat létre és futtathat a Python-készítő és a fogyasztói:

1. A következő módszerek egyikét használja a teljesen minősített tartománynevét (FQDN) és az IP-címek a fürtben található csomópontok a Kafka beolvasásához:

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

    Ez a parancsfájl feltételezi, hogy `$resourceGroupName` az Azure erőforráscsoport, amely tartalmazza a virtuális hálózat neve.

    Mentse a visszakapott információk használatra a következő lépéseket.

2. A következő segítségével telepítse a [kafka-python](http://kafka-python.readthedocs.io/) ügyfél:

        pip install kafka-python

3. Kafka adatokat küldeni, használja a következő Python kódot:

  ```python
  from kafka import KafkaProducer
  # Replace the `ip_address` entries with the IP address of your worker nodes
  # NOTE: you don't need the full list of worker nodes, just one or two.
  producer = KafkaProducer(bootstrap_servers=['kafka_broker_1','kafka_broker_2'])
  for _ in range(50):
      producer.send('testtopic', b'test message')
  ```

    Cserélje le a `'kafka_broker'` bejegyzést, amelyben a címek által visszaadott ebben a szakaszban 1. lépés:

    * Ha használ egy __szoftver VPN-ügyfél__, cserélje le a `kafka_broker` bejegyzést, amelyben a feldolgozó csomópontok IP-címét.

    * Ha rendelkezik __engedélyezve van egy egyéni DNS-kiszolgálón keresztül névfeloldás__, cserélje le a `kafka_broker` bejegyzést, amelyben a feldolgozó csomópontok teljes Tartománynevét.

    > [!NOTE]
    > Ez a kód elküldi a karakterlánc `test message` a témakör `testtopic`. A HDInsight Kafka alapértelmezett konfigurálása nem hozhat létre a témakört, ha még nem létezik.

4. Az üzenetek beolvasásához Kafka, a következő Python kódot használja:

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

    Cserélje le a `'kafka_broker'` bejegyzést, amelyben a címek által visszaadott ebben a szakaszban 1. lépés:

    * Ha használ egy __szoftver VPN-ügyfél__, cserélje le a `kafka_broker` bejegyzést, amelyben a feldolgozó csomópontok IP-címét.

    * Ha rendelkezik __engedélyezve van egy egyéni DNS-kiszolgálón keresztül névfeloldás__, cserélje le a `kafka_broker` bejegyzést, amelyben a feldolgozó csomópontok teljes Tartománynevét.

## <a name="next-steps"></a>További lépések

A HDInsight használata a virtuális hálózaton további információkért lásd: a [kiterjesztése Azure HDInsight Azure virtuális hálózat használatával](../hdinsight-extend-hadoop-virtual-network.md) dokumentum.

Az Azure virtuális hálózat létrehozásával pont – hely típusú VPN-átjáróval további információkért lásd a következő dokumentumokat:

* [Az Azure portál használatával pont – hely kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)

* [Az Azure PowerShell pont – hely kapcsolat konfigurálása](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

A HDInsight-beli Kafka használatával kapcsolatos további információk a következő dokumentumokban találhatók:

* [A HDInsighton futó Kafka használatának első lépései](apache-kafka-get-started.md)
* [A HDInsight Kafka a tükrözés használata](apache-kafka-mirroring.md)
