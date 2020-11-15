---
title: Apache Kafka az Azure HDInsight-ben az Fluent séma beállításjegyzékével
description: Helyezzen üzembe egy HDInsight által felügyelt Kafka-fürtöt egy Virtual Network belül egy peremhálózati csomóponttal, majd telepítse az Fluent séma beállításjegyzékét a peremhálózati csomóponton.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 7e17cdca508db81551d988c795bd1235fa729e82
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636860"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka az Azure HDInsight-ben az Fluent séma beállításjegyzékével

A Kafka-séma beállításjegyzékében olyan szerializálók találhatók, amelyek olyan Apache Kafka-ügyfelekhez csatlakoztathatók, amelyek az üzenet-séma tárolását és a Avro formátumban küldött Kafka-üzenetek lekérését kezelik. A szolgáltatás az OSS-projekthez is kapcsolódott, de most már az [Fluent közösségi licenc](https://www.confluent.io/blog/license-changes-confluent-platform/)alatt áll. A séma beállításjegyzéke emellett az alábbi célokat szolgálja:

* A termelők és a fogyasztók sémáinak tárolása és beolvasása.
* A visszamenőleges/továbbítási/Full kompatibilitásának érvényesítése a témakörökben.
* A Kafka számára továbbított adattartalom méretének csökkentése.

Egy HDInsight által felügyelt Kafka-fürtben a séma beállításjegyzékét általában egy peremhálózati csomóponton helyezik üzembe, hogy lehetővé váljon a fő csomópontokból való számítási elkülönítés.

Az alábbiakban látható a séma beállításjegyzékének egy HDInsight-fürtön való üzembe helyezésének egy jellemző architektúrája. A séma beállításjegyzéke natív módon elérhetővé tesz egy REST API a műveletekhez.  A gyártók és a fogyasztók a VNet vagy a [KAFKA Rest proxy](rest-proxy.md)használatával kezelhetik a séma beállításjegyzékét.

![HDInsight Kafka-séma beállításjegyzéke](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>HDInsight által felügyelt Kafka üzembe helyezése az Fluent séma beállításjegyzékével

Ebben a szakaszban egy HDInsight által felügyelt Kafka-fürtöt helyezünk üzembe egy peremhálózati csomóponttal egy virtuális hálózaton belül, majd a peremhálózati csomóponton telepítjük a Fluent séma beállításjegyzékét.  

1. Az Azure-ba való bejelentkezéshez és a Resource Manager-sablon megnyitásához válassza az alábbi **üzembe helyezés az Azure** -ban gombot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json" target="_blank">:::image type="icon" source="media/schema-registry/hdi-deploy-to-azure1.png":::</a>

1. Az egyéni központi telepítési sablonban töltse fel a mezőket az alább leírtak szerint:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|A legördülő listában válassza ki a fürthöz használt Azure-előfizetést.|
    |Erőforráscsoport|A legördülő listából válassza ki a meglévő erőforráscsoportot, vagy válassza az **új létrehozása** lehetőséget.|
    |Régió|A legördülő listából válassza ki azt a régiót, ahol a fürtöt létrehozták.|
    |Fürt neve|Adjon meg egy globálisan egyedi nevet. Vagy hagyja a következőt: az alapértelmezett név használata.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett érték a **rendszergazda**.|
    |Fürt bejelentkezési jelszava|Adja meg a jelszót.|
    |SSH-Felhasználónév|Adja meg a felhasználónevet, az alapértelmezett érték a **sshuser**.|
    |SSH-jelszó|Adja meg a jelszót.|

    Hagyja meg a többi mezőt. A folytatáshoz válassza a **felülvizsgálat + létrehozás** lehetőséget.

1. Tekintse át a központi telepítés részleteit, majd válassza a **Létrehozás** lehetőséget az üzembe helyezés inicializálásához. Az üzembe helyezés 45 percet is igénybe vehet.

## <a name="configure-the-confluent-schema-registry"></a>Az Fluent séma beállításjegyzékének konfigurálása

Ebben a szakaszban a (z) rendszerű, a Microsoft Edge-csomóponton telepített, a-ben a Kafka-séma beállításjegyzékét konfiguráljuk. Az Fluent séma beállításjegyzéke a (z) helyen található  `/etc/schema-registry/schema-registry.properties` , és a szolgáltatási végrehajtható fájlok elindítására és leállítására szolgáló mechanizmusok a  `/usr/bin/` mappában találhatók.

A séma regisztrálásának tudnia kell, hogy a Zookeeper szolgáltatás képes kommunikálni a HDInsight Kafka-fürttel. Kövesse az alábbi lépéseket a Zookeeper kvórum részleteinek beszerzéséhez.

1. Az [SSH-parancs](../hdinsight-hadoop-linux-use-ssh-unix.md) használatával csatlakozzon a fürt peremhálózati csomópontjához. Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Jelszó-változó beállítása. Cserélje le a jelszót a fürt bejelentkezési jelszavára, majd írja be a parancsot:

    ```bash
    export password='PASSWORD'
    ```

1. Bontsa ki a megfelelő tokozású fürt nevét. Futtassa a következő parancsot:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. A Kafka Zookeeper-gazdagépek kibontása. Futtassa a következő parancsot:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Jegyezze fel ezt az értéket, mert később használni fogja.

1. Bontsa ki a Kafka Broker gazdagépeit. Futtassa a következő parancsot:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Nyissa meg szerkesztési módban a séma beállításjegyzékének tulajdonságait tartalmazó fájlokat. Futtassa a következő parancsot:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Frissítse a értékét `kafkastore.connection.url` a korábban azonosított Zookeeper-karakterlánccal.
    1. A értékének frissítése a következőre: `debug` `true` .

    A tulajdonságok fájl most a következőhöz hasonlóan néz ki:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. A fájl mentéséhez használja a **CTRL + X billentyűkombinációt** **, majd** **írja be** a következőt:.

1. Indítsa el a séma beállításjegyzékét, és mutasson rá a frissített séma beállításjegyzék-tulajdonságok fájljának használatára. Hajtsa végre a következő parancsokat:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. Ha egy SSH-munkamenetben fut a séma-beállításjegyzék, indítson el egy másik SSH-ablakot.  Regisztráljon egy séma új verzióját a "Kafka-Key" tárgy alatt, és jegyezze fel a kimenetet:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Regisztráljon egy séma új verzióját a "Kafka-Value" tárgy alatt, és jegyezze fel a kimenetet:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Sorolja fel az összes témát, és keresse meg a kimenetet:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Az [itt felsorolt speciális parancsokat](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart)érdemes kipróbálnia.

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Avro-adatok küldése és felhasználása a Kafka használatával a séma beállításjegyzékével

Ebben a szakaszban beolvasjuk az adatokat a standard bemenetből, és egy Kafka-témakörbe írjuk azt formátumban. Ezután beolvasjuk a Kafka-témakörök adatait egy olyan fogyasztóval, aki egy Avro formázta az adatok olvasható formátumba alakítását.

1. Hozzon létre egy Kafka-témakört `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. Hozzon létre egy sémát a **Kafka Avro-konzol gyártójának** használatával, rendeljen hozzá egy sémát a témakörhöz, és kezdje el az adatok küldését a témakörbe Avro formátumban. Győződjön meg arról, hogy az előző lépésben a Kafka-témakör létrehozása sikeres volt, és hogy **$KAFKABROKERS** tartalmaz egy értéket.

    A küldendő séma kulcs: érték pár

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Használja az alábbi parancsot a **Kafka Avro-konzol gyártójának** elindításához:

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Ha a gyártó készen áll az üzenetek fogadására, az előre definiált Avro-séma formátumban küldje el az üzeneteket. A TAB billentyűvel hozza létre a kulcs és az érték közötti térközt.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Próbáljon véletlenszerű, nem sémabeli adatokat beírni a konzol gyártójának, hogy megtudja, hogyan teszi lehetővé a gyártó az előre definiált Avro-sémának nem megfelelő adatokat.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. Egy másik képernyőn indítsa el a Kafka Avro-konzol fogyasztóját

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Az alábbi kimenetnek kell megjelennie:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>További lépések

* [A Apache Kafka producer és a fogyasztói API-k használata](apache-kafka-producer-consumer-api.md)