---
title: A AMQP használata Java-üzenetküldési szolgáltatás API-& Azure Service Bus
description: A Java Message Service (JMS) használata a Azure Service Bus és a Advanced Message Queueing Protocol (AMQP) 1,0 használatával.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119157"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>A Java Message Service (JMS) használata a Azure Service Bus és a AMQP 1,0

A **Advanced Message Queueing Protocol (AMQP) 1,0** -es protokolljának támogatása Azure Service Bus azt jelenti, hogy egy hatékony bináris protokoll használatával a Queuing és a közzététel/előfizetés felügyelt üzenetkezelési funkcióit számos platformról lehet használni. Emellett a különböző nyelvek, keretrendszerek és operációs rendszerek együttes használatával létrehozott összetevőkből álló alkalmazásokat is készíthet.

Ez a cikk azt ismerteti, hogyan használhatók a Java-alkalmazások Azure Service Bus üzenetkezelési funkciói (Queues and publish/subscribe) a népszerű **Java Message Service (JMS)** API használatával a AMQP protokollon keresztül.

## <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései
Ez az útmutató feltételezi, hogy már rendelkezik egy nevű várólistát tartalmazó Service Bus névtérrel `basicqueue` . Ha nem, akkor a [névtér és a várólista](service-bus-create-namespace-portal.md) a [Azure Portal](https://portal.azure.com)használatával hozható létre. Service Bus névterek és várólisták létrehozásával kapcsolatos további információkért lásd: [Service Bus Queues – első lépések](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> A particionált várólisták és témakörök szintén támogatják a AMQP. További információ: [particionált üzenetküldési entitások](service-bus-partitioning.md) és [AMQP 1,0 támogatás Service Bus particionált várólistákhoz és témakörökhöz](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
>

## <a name="what-jms-features-are-supported"></a>Milyen JMS funkciók támogatottak?

A Azure Service Bus által támogatott JMS-funkciók.

| Szolgáltatások | Standard szintű Azure Service Bus – JMS 1,1 | Prémium szintű Azure Service Bus – JMS 2,0 (előzetes verzió) |
|---|---|---|
| Entitások automatikus létrehozása a AMQP-en keresztül | Nem támogatott | **Támogatott** |
| Üzenetsorok | **Támogatott** | **Támogatott** |
| Témakörök | **Támogatott** | **Támogatott** |
| Ideiglenes várólisták | Nem támogatott <br/> (Hozzon létre egy rendszeres várólistát a *AutoDeleteOnIdle* set helyett) | **Támogatott** |
| Ideiglenes témakörök | Nem támogatott | **Támogatott** |
| Üzenetek választói | Nem támogatott | **Támogatott** |
| Üzenetsor-tallózók | Nem támogatott <br/> (Használja a Service Bus API *betekintés* funkcióját) | **Támogatott** |
| Közös tartós előfizetések | **Támogatott** | **Támogatott**|
| Nem megosztott tartós előfizetések | Nem támogatott | **Támogatott** |
| Megosztott nem tartós előfizetések | Nem támogatott | **Támogatott** |
| Nem megosztva nem tartós előfizetések | Nem támogatott | **Támogatott** |
| Leiratkozás tartós előfizetésekhez | Nem támogatott | **Támogatott** |
| ReceiveNoWait | Nem támogatott | **Támogatott** |
| Elosztott tranzakciók | Nem támogatott | Nem támogatott |
| Tartós Terminus | Nem támogatott | Nem támogatott |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>További kikötések Service Bus standard szinthez
Egy **munkamenetben**csak egy **MessageProducer** vagy **MessageConsumer** engedélyezett. Ha több **MessageProducers** vagy **MessageConsumers** kell létrehoznia egy alkalmazásban, hozzon létre egy dedikált **munkamenetet** mindegyikhez.

## <a name="downloading-the-java-message-service-jms-client-library"></a>A Java Message Service (JMS) ügyféloldali kódtár letöltése

Ahhoz, hogy csatlakozni tudjanak a Azure Service Bushoz, és a Java Message Service (JMS) API-t használja a AMQP-en keresztül, ki kell használni az alábbi kódtárakat. Ezeket fel kell venni a létrehozási útvonalra a projekt előnyben részesített függőség-kezelési eszközének használatával.

Az ügyfél-függvénytár szükséges, attól függ, hogy melyik árképzési szintet használja.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Prémium szint – JMS 2,0 over AMQP (előzetes verzió)

Az Azure Service Bus Premium szinten elérhető összes előzetes funkció kihasználásához használja az [Azure-servicebus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) könyvtárat.

### <a name="standard-tier---jms-11-over-amqp"></a>Standard szint – JMS 1,1 over AMQP

A Service Bus standard csomag által támogatott JMS-szolgáltatások kihasználása (lásd a [támogatott JMS-szolgáltatásokat?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)) használja az alábbi könyvtárakat:

* [Geronimo JMS 1,1 specifikáció](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Csontos JMS-ügyfél](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> Lehetséges, hogy a JMS JAR-nevek és-verziók megváltoztak. Részletekért lásd: [csontos JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Java-alkalmazások kódolása

A függőségek importálása után a Java-alkalmazások JMS-szolgáltatótól független módon is megírhatók.

Mivel Azure Service Bus standard és a prémium különbözik a függőségek és az általuk támogatott JMS-funkciók számától, a programozási modell némileg eltér a kettőnél.

> [!IMPORTANT]
> Az alábbi útmutató bemutatja, hogyan csatlakozhat a Azure Service Bus egy egyszerű alkalmazáshoz.
>
> Mivel a legtöbb nagyvállalati alkalmazás architektúrája rendelkezhet a függőségek és konfigurációk kezelésének egyéni módjával, az alábbi útmutatás segítségével megismerheti, hogy mire van szükség, és megfelelően alkalmazkodik az alkalmazáshoz.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Csatlakozás a Azure Service Bushoz a JMS használatával

Ha a JMS-ügyfelekkel szeretne csatlakozni a Azure Service Bushoz, akkor az **elsődleges kapcsolati karakterláncban**található [Azure Portal](https://portal.azure.com) "megosztott elérési szabályzatok" részében elérhető **ConnectionString** kell lennie.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Csatlakozás a Azure Service Bus Premiumhoz a JMS 2,0 (előzetes verzió) használatával

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

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Csatlakozás a Azure Service Bus standard szintű JMS 1,1

1. Szúrja be Azure Service Bus konfigurációt a JNDI Properties fájlba **servicebus. properties**néven.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. A JNDI környezet beállítása és a ConnectionFactory konfigurálása
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. A használatával `ConnectionFactory` hozzon létre egy `Connection` , majd egy `Session` .
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>A JMS alkalmazás írása

A vagy a létrejötte után az `Session` `JMSContext` alkalmazás kihasználhatja az ismerős JMS API-kat a felügyeleti és az adatműveletek végrehajtásához.

A standard és a prémium szintű [támogatott JMS-funkciók](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) listáját az egyes szinteken támogatott API-k listájában találja.

## <a name="summary"></a>Összefoglalás
Ez az útmutató bemutatja, hogyan használhatók a Java-üzenetküldést (JMS) használó Java-ügyfélalkalmazások a AMQP 1,0-mel a Azure Service Bus használatával.

Más nyelvekről is használhat Service Bus AMQP 1,0-et, beleértve a .NET, a C, a Python és a PHP-t is. Az ezekkel a különböző nyelvekkel létrehozott összetevők megbízhatóan és teljes hűséggel cserélhetik az üzeneteket a Service Bus AMQP 1,0-támogatásának használatával.

## <a name="next-steps"></a>Következő lépések

A Azure Service Bus és a Java Message Service-(JMS-) entitásokkal kapcsolatos részletes információkért tekintse meg az alábbi hivatkozásokat: 
* [Service Bus – várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md)
* [Service Bus – Java Message Service-entitások](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [AMQP 1,0-támogatás Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)

