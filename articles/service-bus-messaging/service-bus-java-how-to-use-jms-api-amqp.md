---
title: A AMQP használata Java-üzenetküldési szolgáltatás API-& Azure Service Bus
description: A Java Message Service (JMS) használata a Azure Service Bus és a Advanced Message Queueing Protocol (AMQP) 1,0 használatával.
services: service-bus-messaging
documentationcenter: java
author: axisc
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: c0c7e8b6066626966e2a72d474306bae4ead14c2
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027225"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>A Java Message Service (JMS) használata a Azure Service Bus és a AMQP 1,0
Ez a cikk azt ismerteti, hogyan használhatók a Java-alkalmazások Azure Service Bus üzenetkezelési funkciói (Queues and publish/subscribe) a népszerű Java Message Service-(JMS-) API-szabvány használatával. Ez a [cikk](service-bus-amqp-dotnet.md) azt ismerteti, hogyan végezheti el ugyanezt a Azure Service Bus .NET API használatával. A két útmutató együttes használatával megismerheti a AMQP 1,0-et használó platformok közötti üzenetkezelést.

A Advanced Message Queueing Protocol (AMQP) 1,0 egy hatékony, megbízható, vezeték nélküli üzenetkezelési protokoll, amellyel robusztus, platformfüggetlen üzenetkezelési alkalmazások hozhatók létre.

A AMQP 1,0-es verziójának támogatása a Azure Service Bus azt jelenti, hogy a több platformon futó, felügyelt és feliratkozott üzenetkezelési funkciókat egy hatékony bináris protokoll használatával lehet használni. Emellett a különböző nyelvek, keretrendszerek és operációs rendszerek együttes használatával létrehozott összetevőkből álló alkalmazásokat is készíthet.

## <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései
Ez az útmutató feltételezi, hogy már rendelkezik egy **basicqueue**nevű várólistát tartalmazó Service Bus névtérrel. Ha nem, akkor a [névtér és a várólista](service-bus-create-namespace-portal.md) a [Azure Portal](https://portal.azure.com)használatával hozható létre. Service Bus névterek és várólisták létrehozásával kapcsolatos további információkért lásd: [Service Bus Queues – első lépések](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> A particionált várólisták és témakörök szintén támogatják a AMQP. További információ: [particionált üzenetküldési entitások](service-bus-partitioning.md) és [AMQP 1,0 támogatás Service Bus particionált várólistákhoz és témakörökhöz](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>A AMQP 1,0 JMS ügyféloldali kódtár letöltése
Az Apache csontos JMS AMQP 1,0 ügyféloldali kódtár legújabb verziójának letöltésével kapcsolatos információkért látogasson el [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

A következő négy JAR-fájlt hozzá kell adnia az Apache csontos JMS AMQP 1,0 Distribution Archive-ből a Java OSZTÁLYÚTVONAL-hoz a JMS-alkalmazások létrehozásakor és futtatásakor Service Bus használatával:

* Geronimo-JMS\_1,1\_Spec-1.0. jar
* Csontos-JMS-Client-[Version]. jar

> [!NOTE]
> Lehetséges, hogy a JMS JAR-nevek és-verziók megváltoztak. Részletekért lásd: [csontos JMS-AMQP 1,0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Java-alkalmazások kódolása
### <a name="java-naming-and-directory-interface-jndi"></a>Java-elnevezési és könyvtári felület (JNDI)
A JMS a Java elnevezési és könyvtári felületet (JNDI) használja a logikai nevek és a fizikai nevek közötti elkülönítés létrehozásához. A JMS két típusa oldható fel a JNDI: ConnectionFactory és a Destination használatával. A JNDI egy szolgáltatói modellt használ, amelyben különböző címtárszolgáltatás-szolgáltatásokat lehet csatlakoztatni a névfeloldási feladatok kezeléséhez. Az Apache csontos JMS AMQP 1,0 Library egy egyszerű, file-alapú JNDI-szolgáltatót tartalmaz, amely a következő formátumú tulajdonságok használatával van konfigurálva:

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>A JNDI környezet beállítása és a ConnectionFactory konfigurálása

Az [Azure Portalon](https://portal.azure.com) az **elsődleges kapcsolati sztring** alatt elérhető "közös hozzáférési szabályzatok" részében hivatkozott **ConnectionString**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>A gyártó és a fogyasztói cél várólisták konfigurálása
A célhelynek a csontos Properties fájlban JNDI-szolgáltatóban való definiálásához használt bejegyzés formátuma a következő:

A cél üzenetsor létrehozása a termelő számára – 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Cél üzenetsor létrehozása a fogyasztó számára – 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>A JMS alkalmazás írása
Nem szükségesek speciális API-k vagy beállítások a JMS és a Service Bus használata esetén. Van azonban néhány korlátozás, amelyet később fog tárgyalni. Ahogy a JMS-alkalmazásokhoz hasonlóan, az első szükséges a JNDI-környezet konfigurációja, amely lehetővé tenné a **ConnectionFactory** és a célhelyek feloldását.

#### <a name="configure-the-jndi-initialcontext"></a>A JNDI-InitialContext konfigurálása
A JNDI-környezet úgy van konfigurálva, hogy a konfigurációs adatok szórótábla átadja a javax. Naming. InitialContext osztály konstruktorának. A szórótábla két kötelező eleme a kezdeti környezeti gyár neve és a szolgáltató URL-címe. A következő kód azt mutatja be, hogyan konfigurálható a JNDI-környezet a csontos tulajdonságok fájl-alapú JNDI-szolgáltatójának használatára a **servicebus. properties**nevű tulajdonságokkal.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Egy egyszerű JMS alkalmazás Service Bus üzenetsor használatával
A következő példa program JMS TextMessages küld egy Service Bus üzenetsor számára a várólista logikai JNDI, és visszafogadja az üzeneteket.

Az [Azure Service Bus Samples JMS-várólista](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart) összes forráskódját és konfigurációs adatát elérheti gyorskonfigurálás

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Az alkalmazás futtatása
Adja át a **kapcsolati karakterláncot** a megosztott hozzáférési szabályzatokból az alkalmazás futtatásához.
Alább látható az űrlap kimenete az alkalmazás futtatásával:

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP-elrendezés és Service Bus művelet-hozzárendelés
Itt látható, hogy egy AMQP-elrendezés hogyan fordítható le egy Service Bus műveletre:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS témakörök és Service Bus témakörök
A Azure Service Bus témakörök és előfizetések használata a Java Message Service (JMS) API-n keresztül alapvető küldési és fogadási képességeket biztosít. Kényelmes választás, ha más JMS-kompatibilis API-kkal rendelkező alkalmazásokat portolnak, bár a Service Bus témakörök eltérnek a JMS Témaköröktől, és néhány módosítást igényelnek. 

Azure Service Bus témakörök üzeneteket továbbítanak az Azure Resource Management felületen, az Azure parancssori eszközein vagy a Azure Portal keresztül felügyelt, névvel ellátott, megosztott és tartós előfizetésekben. Az egyes előfizetések legfeljebb 2000 kiválasztási szabályt tesznek lehetővé, amelyek mindegyike tartalmaz egy szűrési feltételt, és az SQL-szűrők esetében metaadat-átalakítási műveletet is végezhet. Az egyes Szűrőfeltételek egyeztetése kiválasztja az előfizetésbe másolandó bemeneti üzenetet.  

Az előfizetésből érkező üzenetek fogadása azonos a várólistákból érkező üzenetek fogadásával. Minden előfizetéshez tartozik egy kézbesítetlen levelek várólistája, valamint az üzenetek automatikus továbbításának lehetősége egy másik várólistába vagy témakörbe. 

A JMS témakörök lehetővé teszik, hogy az ügyfelek dinamikusan hozzanak létre nem tartós és tartós előfizetőket, amelyek opcionálisan lehetővé teszik a Message választókkal való szűrést. A Service Bus nem támogatja ezeket a nem megosztott entitásokat. A Service Bus SQL-szűrési szabályának szintaxisa azonban nagyon hasonlít a JMS által támogatott üzenet-választói szintaxisra. 

A JMS-témakör közzétevő oldala kompatibilis a Service Busval, ahogy az ebben a mintában is látható, de a dinamikus előfizetők nem. A következő topológia-alapú JMS API-k nem támogatottak a Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Nem támogatott funkciók és korlátozások
A következő korlátozások érvényesek, ha a JMS-t a AMQP 1,0-nél több mint Service Bus használatával használja, nevezetesen:

* Egy **munkamenetben**csak egy **MessageProducer** vagy **MessageConsumer** engedélyezett. Ha több **MessageProducers** vagy **MessageConsumers** kell létrehoznia egy alkalmazásban, hozzon létre egy dedikált **munkamenetet** mindegyikhez.
* A felejtő témakör-előfizetések jelenleg nem támogatottak.
* A **MessageSelectors** jelenleg nem támogatottak.
* A tranzakciós munkamenetek és az elosztott tranzakciók nem támogatottak.

Emellett a Azure Service Bus a vezérlő síkot az adatsíkon osztja fel, így nem támogatja több JMS dinamikus topológiai funkcióját:

| Nem támogatott metódus          | Csere erre                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Témakör-előfizetés létrehozása az üzenet-választóhoz                                 |
| createDurableConsumer       | Témakör-előfizetés létrehozása az üzenet-választóhoz                                 |
| createSharedConsumer        | Service Bus témakörök mindig megoszthatók, lásd fentebb                                       |
| createSharedDurableConsumer | Service Bus témakörök mindig megoszthatók, lásd fentebb                                       |
| createTemporaryTopic        | témakör létrehozása felügyeleti API/eszközök/portál használatával, a *AutoDeleteOnIdle* pedig lejárati időszakra |
| createTopic                 | témakör létrehozása felügyeleti API/eszközök/portál használatával                                           |
| Leiratkozás                 | a témakör felügyeleti API/eszközök/portál törlése                                             |
| createBrowser               | Támogatott. A Service Bus API betekintés () funkciójának használata                         |
| createQueue                 | üzenetsor létrehozása felügyeleti API/eszközök/portál használatával                                           | 
| createTemporaryQueue        | üzenetsor létrehozása felügyeleti API/eszközök/portál használatával a *AutoDeleteOnIdle* lejárati időszakra állítva |
| receiveNoWait               | használja a Service Bus SDK által biztosított Receive () metódust, és nagyon alacsony vagy nulla időtúllépést ad meg. |

## <a name="summary"></a>Összefoglalás
Ez az útmutató bemutatja, hogyan használhatók Service Bus felügyelt üzenetküldési funkciók (Queues and publish/subscribe) Java-ból a népszerű JMS API és a AMQP 1,0 használatával.

Más nyelvekről is használhat Service Bus AMQP 1,0-et, beleértve a .NET, a C, a Python és a PHP-t is. Az ezekkel a különböző nyelvekkel létrehozott összetevők megbízhatóan és teljes hűséggel cserélhetik az üzeneteket a Service Bus AMQP 1,0-támogatásának használatával.

## <a name="next-steps"></a>Következő lépések
* [AMQP 1,0-támogatás Azure Service Bus](service-bus-amqp-overview.md)
* [A AMQP 1,0 használata a Service Bus .NET API-val](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1,0 fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java fejlesztői központban](https://azure.microsoft.com/develop/java/)

