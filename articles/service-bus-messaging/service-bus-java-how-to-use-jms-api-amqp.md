---
title: Az AMQP 1.0 használata a Java JMS Service Bus API |} A Microsoft Docs
description: Hogyan használható a Java Message Service (JMS) az Azure Service Bus és az Advanced Message Queuing AMQP protokoll 1.0-s.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 03/05/2019
ms.author: aschhab
ms.openlocfilehash: a7e4282a176794fe885049173ba56ce2461cd6fa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885553"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>A Service Bus és az AMQP 1.0-t a Java Message Service (JMS) API használata
A speciális Message Queuing AMQP protokoll 1.0-s egy hatékony, megbízható, alkalmazásszintű üzenetkezelő protokoll, amely nagy teherbírású, többplatformos üzenetkezelési alkalmazások létrehozásához használható.

Támogatás a Service Bus AMQP 1.0-s azt jelenti, hogy az üzenetsor-kezelési használ, és közzététel és feliratkozás közvetítőalapú üzenettovábbítási szolgáltatások listája egy hatékony bináris protokoll használatával platformok esetében. Emellett hozhat létre olyan alkalmazásokat, használja a programozási nyelvek, keretrendszerek és operációs rendszerek-összetevői áll.

Ez a cikk ismerteti a Service Bus üzenetkezelési funkcióit (az üzenetsorok és üzenettémák közzététele vagy előfizetése) Javás alkalmazásokból a népszerű Java Message Service (JMS) szabványos API használatával. Van egy [kiegészítő cikk](service-bus-amqp-dotnet.md) , amely azt ismerteti, hogyan végezze el ugyanezt a Service Bus .NET API használatával. Ezek az útmutatók két együtt használható többplatformos üzenetkezelés az AMQP 1.0-val kapcsolatos.

## <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései
Ez az útmutató feltételezi, hogy már rendelkezik egy Service Bus-névtér nevű üzenetsor tartalmazó **basicqueue**. Ha nem, akkor is [a névtér és üzenetsor létrehozása](service-bus-create-namespace-portal.md) használatával a [az Azure portal](https://portal.azure.com). Service Bus-névterek és az üzenetsorok létrehozásával kapcsolatos további információkért lásd: [Ismerkedés a Service Bus-üzenetsorok](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Particionált üzenetsorok és témakörök is támogatják az AMQP. További információkért lásd: [particionált üzenetküldési entitások](service-bus-partitioning.md) és [a Service Bus AMQP 1.0 támogatása particionált üzenetsorok és témakörök](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Az AMQP 1.0-s JMS ügyféloldali kódtár letöltése
Az Apache Qpid JMS AMQP 1.0-s ügyféloldali kódtár legújabb verzióját töltse le, hogy hol kapcsolatos információkért látogasson el a [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Hozzá kell adnia a következő négy JAR-fájlok az Apache Qpid JMS AMQP 1.0-s terjesztési archívból a Java OSZTÁLYÚTVONAL összeállításakor és a Service Bus JMS alkalmazások futtatásához:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms - client-[version] .jar

> [!NOTE]
> JMS JAR neve és verziója megváltozott. További információkért lásd: [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Kódolási Java-alkalmazások
### <a name="java-naming-and-directory-interface-jndi"></a>Java-kiosztási és a Directory Interface (JNDI)
JMS a Java-kiosztási és a Directory Interface (JNDI) használatával hozzon létre egy logikai és fizikai nevének elkülönítése. Két típusú JMS objektumok feloldása JNDI használatával: ConnectionFactory és a cél. JNDI, amelybe különböző címtárszolgáltatások név feloldása feladatkörök kezelésére is csatlakoztathatja szolgáltató modellt használ. Az Apache Qpid JMS AMQP 1.0 könyvtár tartalmaz egy egyszerű tulajdonságok fájlalapú JNDI szolgáltató, amely konfigurálva van a következő tulajdonságok fájl használatával formázása:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>JNDI környezet telepítése és konfigurálása a ConnectionFactory

A **ConnectionString** érhető el a "megosztott hozzáférési szabályzatokat" egy hivatkozott a [az Azure Portal](https://portal.azure.com) alatt **elsődleges kapcsolati karakterlánc**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Előállítói és fogyasztói célvárólista konfigurálása
A használt határozhat meg a célhelyet a Qpid tulajdonságok JNDI szolgáltató bejegyzés van a következő formátumban:

Az előállítói - a cél várólista létrehozása 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

A fogyasztók - cél várólista létrehozása 
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
Nincsenek speciális API-k vagy a Service Bus JMS használatakor szükséges beállításokat. Azonban néhány korlátozások vonatkoznak, amelyek később tárgyalja. Mivel minden JMS alkalmazással thing először szükség tudják feloldani, a JNDI környezet konfigurációjától egy **ConnectionFactory** forrásaként és céljaként.

#### <a name="configure-the-jndi-initialcontext"></a>A JNDI InitialContext konfigurálása
A JNDI környezet konfigurálva van egy kivonattáblát a konfigurációs adatokat a javax.naming.InitialContext osztály konstruktorának való átadásával. A két szükséges elemeket a kivonattábla kulcsa az osztály nevét a kezdeti környezet Factory és a szolgáltató URL-címe. A következő kód bemutatja, hogyan használja a Tulajdonságok fájlalapú JNDI szolgáltató tulajdonságai nevű fájlt a Qpid a JNDI környezet beállításait **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Egy egyszerű JMS alkalmazást, az Service Bus-üzenetsorba
A következő példa program JMS TextMessages küld egy Service Bus-üzenetsorba VÁRÓLISTA JNDI logikai nevét, és fogadja az üzeneteket vissza.

Az összes elérheti az összes a forrás és konfigurációs információt a a [Azure Service Bus minták JMS várólista gyors üzembe helyezés](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)

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
Adja át a **kapcsolati karakterlánc** , a megosztott hozzáférési házirendek az alkalmazás futtatásához.
Alább van az alkalmazás futtatásával a kimenet a következő formában:

```
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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP törlése és a Service Bus-műveletet leképezés
Itt látható, hogyan egy AMQP törlése a rendszer lefordítja arra a Service Bus-műveletet:

```
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS Topics vs. Service Bus-üzenettémák
Azure Service Bus-üzenettémák és előfizetések révén a Java Message Service (JMS) API-t használó egyszerű küldési biztosít, és szolgáltatásokat. Egy kényelmes választott, akkor, ha a többi üzenetközvetítők JMS megfelelő API-k, az alkalmazások portol, annak ellenére, hogy a Service Bus-témakörök eltérnek a JMS, és néhány stabilitásjavító megkövetelése. 

Az Azure Service Bus-üzenettémakörök üzenetek útvonal nevesített, megosztott és tartós előfizetések az Azure Resource Management felületen, az Azure parancssori eszközök, vagy az Azure Portalon keresztül felügyelt be. Az egyes előfizetésekhez legfeljebb 2000 kijelölés szabályt, minden egyes előfordulhat, hogy rendelkezik, amelyek egy szűrési feltételt, és az SQL-szűrők is a metaadatok átalakítási művelet lehetővé teszi. Minden egyes szűrési feltétel egyezik a bemeneti üzenet tehj előfizetés másolását választja ki.  

Üzenetek fogadása az előfizetések megegyezik az üzenetsorok üzenetek fogadására. Minden előfizetési csomaghoz tartozik egy társított kézbesítetlen levelek várólistájára vonatkozik, valamint automatikusan a várólistához vagy témaköröket üzenetek elküldhetők. 

JMS témakörök engedélyezése az ügyfelek dinamikusan hozhat létre rövid életű és hosszú élettartamú engedélyező előfizetőknél igény szerint az üzenet választók üzenetek szűrése. Service Bus által a fiókirodát ezek az entitások nem támogatottak. SQL szűrési szabály a Service Bus szintaxisa a következő, azonban nagyon hasonlít a üzenet-választó JMS által támogatott szintaxist. 

A témakör JMS közzétevő oldalon kompatibilis Service Bus, az ebben a példában látható módon, de dinamikus előfizetőink nem. A Service Bus a következő topológia kapcsolatos JMS API-k használata nem támogatott. 

## <a name="unsupported-features-and-restrictions"></a>Nem támogatott szolgáltatások és korlátozások
A következő korlátozások érvényesek a JMS nevezetesen keresztül a Service Bus, az AMQP 1.0-s használata esetén:

* Csak egy **MessageProducer** vagy **MessageConsumer** engedélyezett **munkamenet**. Ha szeretne létrehozni több **MessageProducers** vagy **MessageConsumers** egy alkalmazásban, hozzon létre egy dedikált **munkamenet** mindegyikük számára.
* Ideiglenes előfizetések jelenleg nem támogatottak.
* **MessageSelectors** jelenleg nem támogatottak.
* Tranzakciós munkamenetek és az elosztott tranzakciók nem támogatottak.

Emellett Azure Service Bus bontja a vezérlősík az adatsík a, és ezért nem támogatja a több JMS meg topológia dinamikus funkciók:

| A metódus nem támogatott          | Csere erre                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Hozzon létre egy témakör-előfizetésben, az üzenet választó portolása                                 |
| createDurableConsumer       | Hozzon létre egy témakör-előfizetésben, az üzenet választó portolása                                 |
| createSharedConsumer        | Service Bus-üzenettémakörök mindig megosztható, lásd a fenti                                       |
| createSharedDurableConsumer | Service Bus-üzenettémakörök mindig megosztható, lásd a fenti                                       |
| createTemporaryTopic        | létrehoz egy témát, felügyeleti API/tools/portál *AutoDeleteOnIdle* lejárati időt beállítása |
| createTopic                 | felügyeleti API-tools-portál témakör létrehozása                                           |
| előfizetés lemondása                 | a témakör felügyeleti API-tools-portál törlése                                             |
| createBrowser               | nem támogatott. A Service Bus API Peek() funkciójának használatához                         |
| createQueue                 | felügyeleti API-tools-portál üzenetsor létrehozása                                           | 
| createTemporaryQueue        | felügyeleti várólista létrehozása API/tools/portál *AutoDeleteOnIdle* lejárati időt beállítása |

## <a name="summary"></a>Összegzés
Ez az útmutató bemutatta, hogyan használható a Service Bus által felügyelt üzenettovábbítási szolgáltatások (az üzenetsorok és üzenettémák közzététele vagy előfizetése) az Java a népszerű JMS API és az AMQP 1.0 használatával.

Használhatja a Service Bus AMQP 1.0-t a többi nyelvet – például a .NET, C, Python és PHP. Eltérő nyelvek használatával létrehozott összetevők is megbízhatóan váltanak üzeneteket, és teljes visszaadása az AMQP 1.0 használatával támogatja a Service Bus.

## <a name="next-steps"></a>További lépések
* [Az Azure Service Bus AMQP 1.0-támogatás](service-bus-amqp-overview.md)
* [Az AMQP 1.0 használata a Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0-s – fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java fejlesztői központ](https://azure.microsoft.com/develop/java/)

