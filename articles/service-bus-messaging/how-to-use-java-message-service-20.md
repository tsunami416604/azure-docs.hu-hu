---
title: A Java Message Service 2,0 API és a Azure Service Bus Premium használata
description: A Java Message Service (JMS) használata a Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8363011187a4c2ef77681ece4bb8b1de73ec7a63
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801489"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium-preview"></a>A Java Message Service 2,0 API és a Azure Service Bus Premium (előzetes verzió) használata

Ez a cikk azt ismerteti, hogyan használhatja a népszerű **Java Message Service (JMS) 2,0** API-t a speciális üzenetsor-kezelési protokoll (AMQP 1,0) protokollal való Azure Service Bus interakcióhoz.

> [!NOTE]
> A Java Message Service (JMS) 2,0 API támogatása csak a **prémium szintű Azure Service Bus** érhető el, és jelenleg **előzetes**verzióban érhető el.
>

## <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései

Ez az útmutató azt feltételezi, hogy már rendelkezik Service Bus névtérrel. Ha nem, akkor a [névtér és a várólista](service-bus-create-namespace-portal.md) a [Azure Portal](https://portal.azure.com)használatával hozható létre. 

Service Bus névterek és várólisták létrehozásával kapcsolatos további információkért tekintse meg [a Service Bus queues Azure Portal használatával történő ismerkedést](service-bus-quickstart-portal.md)ismertető témakört.

## <a name="what-jms-features-are-supported"></a>Milyen JMS funkciók támogatottak?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>A Java Message Service (JMS) ügyféloldali kódtár letöltése

Azure Service Bus Premium szinten elérhető összes funkció kihasználásához az alábbi könyvtárat hozzá kell adni a projekt Build elérési útjához.

[Azure-servicebus – JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Ha hozzá szeretné adni az [Azure-servicebus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) a Build elérési úthoz, használja az előnyben részesített függőségi kezelési eszközt a projekthez, például a [Maven](https://maven.apache.org/) vagy a [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Java-alkalmazások kódolása

A függőségek importálása után a Java-alkalmazások JMS-szolgáltatótól független módon is megírhatók.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Csatlakozás a Azure Service Bushoz a JMS használatával

Ha a JMS-ügyfelekkel szeretne csatlakozni a Azure Service Bushoz, akkor az **elsődleges kapcsolati karakterlánc**alatt lévő [Azure Portal](https://portal.azure.com) "megosztott elérési szabályzatok" részében elérhető **kapcsolati sztringre** van szükség.

1. A`ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Hozza létre a `ServiceBusJmsConnectionFactory` megfelelő példányát `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. A `ConnectionFactory` vagy a létrehozásához használja `Connection` a`Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    vagy a `JMSContext` (JMS 2,0-ügyfelek esetében)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

### <a name="write-the-jms-application"></a>A JMS alkalmazás írása

A vagy a létrejötte után az `Session` `JMSContext` alkalmazás az ismerős JMS API-k használatával is végrehajthatja a felügyeleti és az adatműveleteket.

Tekintse át a [támogatott JMS-funkciók](how-to-use-java-message-service-20.md#what-jms-features-are-supported) listáját, és tekintse meg, hogy mely API-k támogatottak az előzetes verzió részeként.

Az alábbiakban néhány mintakód-kódrészletet talál a JMS használatába

#### <a name="sending-messages-to-a-queue-and-topic"></a>Üzenetek küldése egy várólistára és témakörbe

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Üzenetek fogadása egy megosztott tartós előfizetésből egy témakörből

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Összefoglalás

Ez az útmutató bemutatja, hogyan használhatók a Java-üzenetküldést (JMS) használó Java-ügyfélalkalmazások a AMQP 1,0-mel a Azure Service Bus használatával.

Más nyelvekről is használhat Service Bus AMQP 1,0-et, beleértve a .NET, a C, a Python és a PHP-t is. Az ezekkel a különböző nyelvekkel létrehozott összetevők megbízhatóan és teljes hűséggel cserélhetik az üzeneteket a Service Bus AMQP 1,0-támogatásának használatával.

## <a name="next-steps"></a>További lépések

A Azure Service Bus és a Java Message Service-(JMS-) entitásokkal kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat – 
* [Service Bus – várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)
* [Service Bus – Java Message Service-entitások](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [AMQP 1,0-támogatás Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java Message Service API (külső Oracle doc)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Ismerje meg, hogyan migrálhat a ActiveMQ-ről Service Bus](migrate-jms-activemq-to-servicebus.md)
