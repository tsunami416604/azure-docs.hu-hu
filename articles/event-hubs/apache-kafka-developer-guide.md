---
title: Az Apache Kafka fejlesztői útmutatója az Event Hubs-hoz
description: Ez a cikk a Kafka-alkalmazások Azure Event Hubs-szal való integrálását leíró cikkekre mutató hivatkozásokat tartalmaz.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 8a72d20101aacaf59b4be5c4a231b132237113f3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633917"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Az Apache Kafka fejlesztői útmutatója az Azure Event Hubs-hoz
Ez a cikk az Apache Kafka-alkalmazások Azure Event Hubs-szal való integrálását leíró cikkekre mutató hivatkozásokat tartalmaz. 

## <a name="overview"></a>Áttekintés
Az Event Hubs egy Kafka-végpontot biztosít, amelyet a meglévő Kafka alapú alkalmazások a saját Kafka-fürt futtatása alternatívájaként használhatnak. Az Event Hubs támogatja az Apache Kafka 1.0-s és újabb protokollját, és együttműködik a meglévő Kafka alkalmazásokkal, beleértve a MirrorMakert is. További információ: [Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Rövid útmutatók
A GitHubon és ebben a tartalomkészletben rövid útmutatókat találhat, amelyek segítségével gyorsan felturbózhatja a Kafka eseményközpontjait.

### <a name="quickstarts-in-github"></a>Rövid útmutatók a GitHubon
Tekintse meg az alábbi rövid **útmutatókat az azure-event-hubs-for-kafka** tárházban: 

| Ügyfél nyelve/keretrendszere | Leírás | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Ez a rövid útmutató bemutatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy c# nyelven írt példatermelő és fogyasztó használatával a .NET Core 2.0 használatával.</p><p>Ez a minta a [Confluent Apache Kafka .NET kliensén](https://github.com/confluentinc/confluent-kafka-dotnet)alapul, amelyet a Kafka eseményelosztóival való használatra módosítottak.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Ez a rövid útmutató bemutatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy Java-ban írt példagyártó és -fogyasztó használatával. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Ez a rövid útmutató bemutatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy példa gyártó és fogyasztó node-ban írt használatával.</p><p>Ez a minta a [csomópont-rdkafka](https://github.com/Blizzard/node-rdkafka) könyvtárat használja. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Ez a rövid útmutató bemutatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy példatermelő és python-ban írt fogyasztó használatával.</p><p>Ez a minta a [Confluent Apache Kafka Python-ügyfél,](https://github.com/confluentinc/confluent-kafka-python)kafka eseményközpontokkal való használatra módosított.</p>|
| [Ugrás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Ez a rövid útmutató bemutatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy példagyártó és egy felhasználó go írásában írt használatával.</p><p>Ez a minta a [Confluent Apache Kafka Golang kliensén](https://github.com/confluentinc/confluent-kafka-go)alapul, amelyet a Kafka eseményelosztóival való használatra módosítottak.</p>| 
| [Sarama kafka Menj](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Ez a rövid útmutató bemutatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy példaproducer és fogyasztó használatával, amelyet a [Sarama Kafka ügyfélkódtár](https://github.com/Shopify/sarama) használatával írt a Go alkalmazásban. |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Ez a rövid útmutató bemutatja, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz az Apache Kafka disztribúcióhoz mellékelt CLI használatával.| 
| [Kafkacat között](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | Kafkacat egy nem JVM parancssori fogyasztó és termelő alapuló librdkafka, népszerű miatt a sebesség és a kis helyigény. Ez a rövid útmutató mintakonfigurációt és számos egyszerű minta kafkacat parancsot tartalmaz. | 
 
### <a name="quickstarts-in-docs"></a>Rövid útmutatók a DOCS-ban
Tekintse meg a rövid útmutató: [Adatfolyam-továbbítás az Event Hubs használatával a Kafka protokoll](event-hubs-quickstart-kafka-enabled-event-hubs.md) ebben a tartalomkészletben, amely lépésről lépésre bemutatja, hogyan streamelheti az Event Hubs. Megtudhatja, hogyan használhatja a gyártók és a fogyasztók beszélni Event Hubs csak egy konfigurációs változás az alkalmazásokban. 


## <a name="tutorials"></a>Oktatóanyagok 

### <a name="tutorials-in-github"></a>Oktatóanyagok a GitHubon
Tekintse meg az alábbi útmutatókat a GitHubon:

| Oktatóanyag | Leírás | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Ez az oktatóanyag bemutatja, hogyan csatlakoztathatja az Akka-streameket a Kafka-kompatibilis eseményközpontokhoz a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. Két külön oktató anyagok **Java** és **Scala** programozási nyelvek. | 
| [Kapcsolódás](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Ez a dokumentum végigvezeti a Kafka Connect és az Azure Event Hubs integrálásán, valamint az alapvető FileStreamSource és FileStreamSink-összekötők üzembe helyezésén. Bár ezek az összekötők nem éles használatra szántak, bemutatják a végpontok között kafka connect forgatókönyvet, ahol az Azure Event Hubs kafka-brókernek álcázza magát.| 
| [Filebeat között](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Ez a dokumentum végigvezeti a Filebeat és az Event Hubs integrálásán a Filebeat Kafka kimenetén keresztül. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Ez az oktatóanyag bemutatja, hogyan csatlakozhat az Apache Flink-hez a Kafka-kompatibilis Event Hubs-hoz a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. | 
| [Fluentd](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Ez a dokumentum végigvezeti önt a Fluentd `out_kafka` és az Event Hubs integrálásán a Fluentd kimeneti beépülő moduljával. |
| [Együttműködési](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Ez az oktatóanyag bemutatja, hogyan cserélheti ki az eseményeket a fogyasztók és a különböző protokollok használatával a gyártók között. |
| [LogStash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Ez az oktatóanyag végigvezeti a Logstash és a Kafka-kompatibilis eseményközpontok integrálásán a Logstash Kafka bemeneti/kimeneti bővítmények használatával. | 
| [MirrorMaker (Tükörkészítő)](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Ez az oktatóanyag bemutatja, hogy egy eseményközpont és a Kafka MirrorMaker hogyan integrálhat egy meglévő Kafka-folyamatot az Azure-ba a Kafka bemeneti adatfolyam tükrözésével az Event Hubs szolgáltatásban. |
| [NiFi között](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Ez az oktatóanyag bemutatja, hogyan lehet csatlakoztatni az Apache NiFi-t egy Eseményközpont-névtérhez. | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | A rövid útmutatók bemutatják, hogyan hozhat létre és csatlakozhat egy Event Hubs Kafka-végponthoz egy go- és Java-programozási nyelveken írt példagyártó és -fogyasztó használatával. |
| [Confluent séma-nyilvántartója](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Ez az oktatóanyag végigvezeti a Kafka séma-rendszerleíró adatbázisának és eseményközpontjainak integrálásán. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Ez az oktatóanyag bemutatja, hogyan csatlakoztathatja a Spark-alkalmazást egy eseményközponthoz a protokollügyfelek módosítása vagy a saját Kafka-fürtök futtatása nélkül. | 

### <a name="tutorials-in-docs"></a>Oktatóanyagok a DOCS-ban
Tekintse meg az oktatóanyagot is: [Az Apache Kafka az Event Hubs-események feldolgozását](event-hubs-kafka-stream-analytics.md) a tartalomkészlet Stream analytics használatával, amely bemutatja, hogyan továbbítsa az adatokat az Event Hubs-ba, és hogyan dolgozza fel azokat az Azure Stream Analytics szolgáltatással.

## <a name="how-to-guides"></a>Útmutatók
Dokumentációnkban az alábbi útmutatók ban olvashat:

| Cikk | Leírás | 
| ------- | ----------- | 
| [Kafka-közvetítő tükrözése egy eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md) | Bemutatja, hogyan tükrözegy Kafka bróker egy eseményközpontban a Kafka MirrorMaker használatával. |
| [Apache Spark csatlakoztatása egy eseményközponthoz](event-hubs-kafka-spark-tutorial.md) | Végigvezeti a Spark-alkalmazás az Event Hubs valós idejű streameléshez való csatlakoztatásán. |
| [Apache Flink csatlakoztatása egy eseményközponthoz](event-hubs-kafka-flink-tutorial.md) | Bemutatja, hogyan csatlakoztathatja az Apache Flinket egy eseményközponthoz a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. |
| [Az Apache Kafka Connect integrálása eseményközponttal (előzetes verzió)](event-hubs-kafka-connect-tutorial.md) | Végigvezeti a Kafka Connect egy eseményközponttal való integrálásán, valamint az alapvető FileStreamSource és FileStreamSink-összekötők üzembe helyezésén. |
| [Akka-adatfolyamok csatlakoztatása eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md) | Bemutatja, hogyan csatlakoztathatja az Akka Streameket egy eseményközponthoz a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. |
| [Az Apache Kafka tavaszi rendszerindítási kezdőcsapatának használata az Azure Event Hubs szolgáltatással](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Bemutatja, hogyan konfigurálhat egy Java-alapú tavaszi felhőalapú streamiratgyűjtőt, amelyet a tavaszi rendszerindítási inicionátorsal hoztak létre az Apache Kafka azure-beli Event Hubs használatával. |

## <a name="next-steps"></a>További lépések
Tekintse át a mintákat a GitHub-tárházban [az azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) rövid útmutató és oktatóanyagok mappák alatt.

