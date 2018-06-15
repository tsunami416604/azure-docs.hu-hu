---
title: Az Azure Functions segítségével adatokat küldeni a HDInsight Kafka |} Microsoft Docs
description: Ismerje meg, egy Azure-függvény használata a HDInsight Kafka adatokat írni.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/08/2018
ms.author: larryfr
ms.openlocfilehash: c657cda7dce0611cb357a0a2063a154f2f6f25f2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939804"
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Kafka használata a HDInsight az Azure-függvény alkalmazásokból

Ismerje meg, hogyan hozzon létre egy Azure-függvény alkalmazást, amely adatokat küld a HDInsight Kafka.

[Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/) egy kiszolgáló nélküli számítási szolgáltatás. Lehetővé teszi igény kód futtatása anélkül, hogy explicit módon kiosztania vagy infrastruktúra kezelését.

Az [Apache Kafka](https://kafka.apache.org) egy nyílt forráskódú elosztott streamelési platform streamadatfolyamatok és -alkalmazások létrehozásához. A Kafka az üzenetsorokhoz hasonló üzenetközvetítő funkciót is biztosít, amellyel adatstreameket tehet közzé, illetve feliratkozhat rájuk. A HDInsight alatt futó Kafka felügyelt, rugalmasan méretezhető és magas rendelkezésre állású szolgáltatást biztosít önnek a Microsoft Azure-felhőben.

A HDInsight Kafka nem biztosít az API-k a nyilvános interneten. Közzétételére, vagy Kafka származó adatok felhasználásához, csatlakoznia kell a Kafka fürt Azure virtuális hálózat használatával. Az Azure Functions egy nyilvános végpontot, amely a leküldéses értesítések adatok Kafka be a HDInsight-on keresztül a virtuális hálózati átjáró létrehozásához kényelmes megoldást kínál.

> [!NOTE]
> A jelen dokumentum elsősorban a HDInsight Kafka kommunikálni az Azure Functions engedélyezéséhez szükséges lépéseket. A saját magát példája Kafka készítő annak bemutatásához, hogy működik-e a konfiguráció csak egy alapszintű.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure függvény alkalmazást. További információkért lásd: a [hozzon létre az első függvényét](../../azure-functions/functions-create-first-azure-function.md) dokumentációját.

* Egy Azure virtuális hálózatra. Az Azure Functions használatát a virtuális hálózati kell használnia a következő IP-címtartományok egyikét:

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    További információkért lásd: a [az alkalmazás integrálja az Azure virtuális hálózat](../../app-service/web-sites-integrate-with-vnet.md) dokumentum.

* A HDInsight-fürt Kafka. Létrehozásával kapcsolatos információkat a Kafka a HDInsight-fürtöt, tekintse meg a [Kafka fürt létrehozása](apache-kafka-get-started.md) dokumentum.

    > [!IMPORTANT]
    > Fürt konfigurálása során kell használnia a __speciális beállítások__ lépésben jelölje be a Azure-beli virtuális hálózatra és az alhálózatot, amely a HDInsight-általi. Válassza ki a virtuális hálózat és az előző lépésben létrehozott alhálózati.

    Kafka és virtuális hálózatok további információkért lásd: a [Kafka virtuális hálózaton keresztül csatlakozhat](apache-kafka-connect-vpn-gateway.md) dokumentum.

## <a name="architecture"></a>Architektúra

A HDInsight Kafka egy Azure virtuális hálózat található. Az Azure Functions egy pont – hely átjáró használatával kommunikálhat a virtuális hálózat. Az alábbi képen egy a hálózati topológia diagramja:

![Kapcsolódás a HDInsight az Azure Functions architektúrája](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Kafka konfigurálása

Ebben a szakaszban található információk előkészíti a Kafka fürtöt, az Azure-függvény adatokat fogadhat. Magában foglalja az alábbi konfigurációs műveletek:

* IP-közzététel
* Gyűjtése Kafka Broker IP-címek
* A Kafka témakör létrehozása

### <a name="configure-kafka-for-ip-advertising"></a>IP-hirdetési Kafka konfigurálása

Alapértelmezés szerint Zookeeper az ügyfelek számára a Kafka brókerek tartomány nevét adja vissza. Ez a konfiguráció nem működik nélkül egy DNS-kiszolgáló, az ügyfél (az Azure Functions) nem oldható fel a virtuális hálózat nevét. Ehhez a konfigurációhoz az alábbi lépések segítségével Kafka hivatkozik tartománynevek helyett IP-címek konfigurálása:

1. A webböngészőben nyissa https://CLUSTERNAME.azurehdinsight.net. Cserélje le __CLUSTERNAME__ HDInsight-fürt Kafka nevével.

    Amikor a rendszer kéri, használja a HTTPS-felhasználónevet és jelszót a fürthöz. Az Ambari webes felhasználói felületén, a fürt akkor jelenik meg.

2. Kafka adatok megtekintéséhez válassza ki a __Kafka__ a bal oldali listában.

    ![A kijelölt Kafka szolgáltatás lista](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Válassza ki, ha Kafka konfigurációs __Configs__ felső közepén.

    ![Kafka Configs hivatkozások](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Található a __kafka-env__ konfigurációs, adja meg `kafka-env` a a __szűrő__ jobb felső részén található.

    ![Kafka konfigurációját, kafka-env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

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

    ![Mentse a konfigurációs gomb](./media/apache-kafka-azure-functions/save-button.png)

9. Megakadályozhatja, hogy hibák Kafka újraindításakor a __szolgáltatás műveletek__ gombra, majd az __kapcsolja be a karbantartási mód__. Kattintson az OK gombra a művelet végrehajtásához.

    ![Szolgáltatási művelet, amely a kijelölt karbantartási bekapcsolása](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Indítsa újra a Kafka, használja a __indítsa újra a__ gombra, majd az __indítsa újra az összes érintett__. Erősítse meg az újraindítás, és használja a __OK__ gombra kattint, a művelet befejeződése után.

    ![Indítsa újra a gomb, újraindítással minden érintett](./media/apache-kafka-azure-functions/restart-button.png)

11. A karbantartási mód letiltásához használja a __szolgáltatás műveletek__ gombra, majd az __kapcsolja ki a karbantartási mód__. Válassza ki **OK** a művelet végrehajtásához.

### <a name="get-the-kafka-broker-ip-address"></a>A Kafka broker IP-cím beszerzése

A következő módszerek egyikét használja a teljesen minősített tartománynevét (FQDN) és az IP-címek a fürtben található csomópontok a Kafka beolvasásához:

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

Ez a parancs feltételezi, hogy `<resourcegroupname>` az Azure erőforráscsoport, amely tartalmazza a virtuális hálózat neve.

A visszaadott nevek listájából válassza ki a workernode IP-címét. A csomópont a belső teljesen minősített tartománynevét betűkkel kezdődik `wn`. Az IP-címet használják a függvényt Kafka kommunikálni.

### <a name="create-a-kafka-topic"></a>Hozzon létre egy Kafka témakör

Kafka tárol adatokat __témakörök__. Kafka adatok küldése az Azure-függvény, előtt kell létrehozni a függvényt.

Létrehoz egy témát, kövesse a lépéseket a a [Kafka fürt létrehozása](apache-kafka-get-started.md) dokumentum.

## <a name="create-a-function-that-sends-to-kafka"></a>Hozzon létre egy függvényt, amely Kafka küld

> [!NOTE]
> A jelen szakaszban szereplő lépéseket használó JavaScript függvény létrehozása a [kafka-csomópont](https://www.npmjs.com/package/kafka-node) adatok közzétételére Kafka csomag:

1. Hozzon létre egy új __WebHook + API__ működik, és válassza a __JavaScript__ nyelve. Új funkciók létrehozásával kapcsolatos további információkért lásd: a [hozzon létre az első függvényét](../../azure-functions/functions-create-first-azure-function.md#create-function) dokumentum.

2. A függvény törzséhez tartozó használata a következő kódot:

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Cserélje le `'test'` nevű, a HDInsight-fürt létrehozott Kafka üzenettémakörhöz.

    Cserélje le `10.1.0.7` korábban kapott IP-címmel. Hagyja a `:9092` érték. 9092 a portot, amelyet figyeli Kafka.

    Használja a __mentése__ gombra a módosítások mentéséhez.

    ![A szerkesztő mentési gomb](./media/apache-kafka-azure-functions/function-editor.png)

3. A függvény szerkesztő jobbra, válassza ki __-fájlokat tekinthetnek meg__. Válassza ki __+ Hozzáadás__ , és adja hozzá egy új fájlt `package.json`. Ezt a fájlt határozza meg a függőség használja a `kafka-node` csomag.

    ![Adjon hozzá egy fájlt](./media/apache-kafka-azure-functions/add-files.png)

4. Az új fájl szerkesztéséhez jelölje ki `package.json` a a __-fájlokat tekinthetnek meg__ listája. A fájl tartalmát a következő szöveg használata:

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Használja a __mentése__ gombra a módosítások mentéséhez.

5. Telepítendő a `kafka-node` csomag, használja a _csomópont verziója és a csomag felügyeleti_ szakasza a [Azure függvények JavaScript – útmutató fejlesztőknek](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > Több hiba jelenhet meg a kafka-csomópont csomag telepítve van. Ezeket a hibákat nyugodtan figyelmen kívül hagyhatja.

## <a name="run-the-function"></a>A funkció futtatásához

A függvény szerkesztő jobbra, válassza ki __teszt__. Hagyja meg az alapértelmezett beállításokat a teszthez, majd válassza ki __futtatása__. A tesztet futtatja, mivel a rendszer továbbküld a `name` a függvény paramétere. Ez a paraméter értéke `Azure`, amely a függvény szúr be Kafka.

![Teszt párbeszédpanel képernyőképe](./media/apache-kafka-azure-functions/function-test-dialog.png)

Válassza ki, ha a teszt futtatása közben a függvény által naplózott információ __naplók__ az oldal alján. Futtassa ismét a naplózási adatok létrehozásához a tesztet.

![A függvény kimenet – példa](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Ellenőrizze, hogy az adatok olyan Kafka

Ha ellenőrizni szeretné, hogy az adatok érkeztek a Kafka témakör, olvassa el a a _előállításához és felhasználhatják a rekordok_ szakasza a [Kafka fürt létrehozása](apache-kafka-get-started.md#produce-and-consume-records) dokumentum. A `kafka-console-consumer` olvassa be az adatokat a témakört, és a következő témakör tárolt üzenetek listáját jeleníti meg.

## <a name="next-steps"></a>További lépések

A HDInsighton futó Apache Kafka használatának megismeréséhez tekintse meg a következő hivatkozásokat:

* [A HDInsighton futó Kafka használatának első lépései](apache-kafka-get-started.md)

* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](apache-kafka-mirroring.md)

* [Az Apache Storm használata a HDInsighton futó Kafkával](../hdinsight-apache-storm-with-kafka.md)

* [Az Apache Spark használata a Kafkával a HDInsighton](../hdinsight-apache-spark-with-kafka.md)

* [Csatlakozás a Kafkához Azure Virtual Networkön keresztül](apache-kafka-connect-vpn-gateway.md)
