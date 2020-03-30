---
title: Az AMQP használata a Java Message Service API-val & Az Azure Service Bus
description: A Java Message Service (JMS) használata az Azure Service Bus és a Advanced Message Queuing Protocol (AMQP) 1.0 protokollal.
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
ms.openlocfilehash: cd06838abbb69af5684fdea18c42f6a8f95ffe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371254"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>A Java Message Service (JMS) használata az Azure Service Bus és az AMQP 1.0 szolgáltatással
Ez a cikk bemutatja, hogyan használhatja az Azure Service Bus üzenetkezelési funkcióit (várólisták és közzétételi/előfizetési témakörök) a Java-alkalmazásokból a népszerű Java Message Service (JMS) API-szabvány használatával. Van egy [társcikk,](service-bus-amqp-dotnet.md) amely bemutatja, hogyan kell ugyanezt az Azure Service Bus .NET API használatával. Az AMQP 1.0 használatával a két útmutató együttes használatával megismerheti a platformok közötti üzenetküldést.

Az Advanced Message Queuing Protocol (AMQP) 1.0 egy hatékony, megbízható, vezetékes szintű üzenetkezelési protokoll, amelynek segítségével robusztus, platformfüggetlen üzenetküldő alkalmazásokat hozhat létre.

Az AMQP 1.0 támogatása az Azure Service Busban azt jelenti, hogy számos platformról használhatja a sorban állásés közzététel/előfizetés felügyelt üzenetkezelési funkciókat egy hatékony bináris protokoll használatával. Ezenkívül olyan alkalmazásokat is létrehozhat, amelyek nyelvek, keretrendszerek és operációs rendszerek kombinációjával készült összetevőkből állnak.

## <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései
Ez az útmutató feltételezi, hogy már rendelkezik egy `basicqueue`Service Bus-névtérrel, amely egy .. Ha nem, akkor [létrehozhatja a névteret és a várólistát](service-bus-create-namespace-portal.md) az [Azure Portal](https://portal.azure.com)használatával. A Service Bus névterei és várólistái létrehozásáról további információt a [Service Bus-várólisták – Első lépések című témakörben talál.](service-bus-dotnet-get-started-with-queues.md)

> [!NOTE]
> Particionált várólisták és témakörök is támogatják az AMQP. További információ: [Particionált üzenetküldési entitások](service-bus-partitioning.md) és [AMQP 1.0 támogatása Service Bus particionált várólisták és témakörök.](service-bus-partitioned-queues-and-topics-amqp-overview.md)
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Az AMQP 1.0 JMS ügyfélkönyvtár letöltése
Ha tudni szeretné, hogy hol töltheti le az Apache Qpid JMS AMQP 1.0 ügyfélkönyvtárlegújabb verzióját, látogasson el a alkalmazásba. [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html)

A Következő négy JAR-fájlt kell hozzáadnia az Apache Qpid JMS AMQP 1.0 terjesztési archívumból a Java CLASSPATH-hoz, amikor JMS alkalmazásokat hoz létre és futtat a Service Bus segítségével:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[verzió].jar

> [!NOTE]
> Lehet, hogy a JMS JAR nevek és verziók megváltoztak. További részletek: [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Java alkalmazások kódolása
### <a name="java-naming-and-directory-interface-jndi"></a>Java-név- és címtár-illesztő (JNDI)
A JMS a Java naming and Directory Interface (JNDI) segítségével hozza létre a logikai nevek és a fizikai nevek elkülönítését. A JNDI-objektumokkal kétféle JMS-objektum van feloldva: A ConnectionFactory és a Destination. A JNDI olyan szolgáltatói modellt használ, amelybe különböző címtárszolgáltatásokat csatlakoztathat a névfeloldási feladatok kezeléséhez. Az Apache Qpid JMS AMQP 1.0 könyvtár egy egyszerű tulajdonságfájl-alapú JNDI-szolgáltatóval rendelkezik, amely a következő tulajdonságokkal rendelkezik:

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

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>JNDI-környezet beállítása és a ConnectionFactory konfigurálása

A **ConnectionString** hivatkozott az egyik elérhető a "Megosztott hozzáférési szabályzatok" az [Azure Portalon](https://portal.azure.com) **az elsődleges kapcsolati karakterlánc**
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

#### <a name="configure-producer-and-consumer-destination-queues"></a>Gyártói és fogyasztói célvárólisták konfigurálása
A Qpid tulajdonságfájl JNDI-szolgáltatójában a cél definiálására használt bejegyzés formátuma a következő:

A gyártó célvárólistájának létrehozása - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Célvárólista létrehozása a fogyasztó számára - 
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
Nincsszükség speciális API-kra vagy beállításokra a JMS Service Bus használatával. Van azonban néhány korlátozás, amelyre később vonatkozik. Mint minden JMS alkalmazás, az első dolog szükséges a JNDI környezet konfigurációja, hogy képes legyen megoldani a **ConnectionFactory** és a célok.

#### <a name="configure-the-jndi-initialcontext"></a>A JNDI InitialContext konfigurálása
A JNDI-környezet úgy van konfigurálva, hogy a konfigurációs adatok kivonatát átadja a javax.naming.InitialContext osztály konstruktorának. A kivonattáblázat két szükséges eleme a Kezdeti környezetgyár és a Szolgáltató URL-címe. A következő kód bemutatja, hogyan konfigurálható a JNDI-környezet a Qpid tulajdonságfájl alapú JNDI-szolgáltató **servicebus.properties**nevű tulajdonságfájllal.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Egyszerű JMS-alkalmazás a Service Bus-várólistát használva
A következő példaprogram jms textüzeneteket küld egy Service Bus várólistába a QUEUE JNDI logikai nevével, és visszakapja az üzeneteket.

Az [Azure Service Bus Samples JMS-várólista rövid útmutatójából](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart) az összes forráskód- és konfigurációs információ elérhető.

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
Adja át a **kapcsolati karakterláncot** a megosztott hozzáférési házirendekből az alkalmazás futtatásához.
Az alábbiakban az űrlap kimenete az alkalmazás futtatásával:

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

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP-intézkedési és szolgáltatásbusz-művelet leképezése
Az AMQP-intézkedés szolgáltatásszolgáltatás-műveletre fordítása a következőképpen történik:

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS-témakörök és a Service Bus-témakörök
Az Azure Service Bus témaköreinek és előfizetéseinek használata a Java Message Service (JMS) API-n keresztül alapvető küldési és fogadási lehetőségeket biztosít. Kényelmes választás, ha más üzenetbrókerektől származó alkalmazásokat jms-kompatibilis API-kkal portol, annak ellenére, hogy a Service Bus-témakörök eltérnek a JMS-témaköröktől, és néhány módosítást igényelnek. 

Az Azure Service Bus témakörei az üzeneteket az Azure Resource Management felületén, az Azure parancssori eszközein vagy az Azure Portalon keresztül kezelt elnevezett, megosztott, tartós előfizetésekbe irányítják. Minden előfizetés legfeljebb 2000 kijelölési szabályt tesz lehetővé, amelyek mindegyike rendelkezhet szűrőfeltétellel, és SQL-szűrők esetén metaadat-átalakítási műveletet is. Minden szűrőfeltétel-egyezés kiválasztja az előfizetésbe másolandó bemeneti üzenetet.  

Az előfizetésektől érkező üzenetek fogadása azonos a várólistákból érkező üzenetek fogadása. Minden előfizetéshez tartozik egy kézbesítetlen levelek várólistája, és képes automatikusan továbbítani az üzeneteket egy másik várólistába vagy témakörökbe. 

A JMS-témakörök lehetővé teszik az ügyfelek számára, hogy dinamikusan hozzanak létre nem tartós és tartós előfizetőket, amelyek opcionálisan lehetővé teszik az üzenetek üzenetválasztóval történő szűrését. Ezeket a nem megosztott entitásokat a Service Bus nem támogatja. A Service Bus SQL-szűrőszabályszintaxisa azonban hasonló a JMS által támogatott üzenetválasztó szintaxishoz. 

A JMS-témakör közzétevői oldala kompatibilis a Service Bus szolgáltatással, amint az ebben a példában látható, de a dinamikus előfizetők nem. A következő topológia kapcsolatos JMS API-k nem támogatottak a Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Nem támogatott szolgáltatások és korlátozások
A következő korlátozások vannak érvényben, ha jms-t használ az AMQP 1.0-n keresztül a Service Bus szolgáltatással, nevezetesen:

* **Munkamenetenként**csak egy **MessageProducer** vagy **MessageConsumer** engedélyezett. Ha több **MessageProducers** vagy **MessageConsumers** kell létrehoznia egy alkalmazásban, hozzon létre egy dedikált **munkamenetet** mindegyikhez.
* Az felejtő témakör-előfizetések jelenleg nem támogatottak.
* **A MessageSelectors** jelenleg nem támogatott.
* Az elosztott tranzakciók nem támogatottak (de a lebonyolított munkamenetek támogatottak).

Emellett az Azure Service Bus felosztja a vezérlősíkot az adatsíkról, ezért nem támogatja a JMS számos dinamikus topológiai funkcióját:

| Nem támogatott metódus          | Csere erre                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | Témakör-előfizetés létrehozása az üzenetválasztó portolása                                 |
| createDurableConsumer       | Témakör-előfizetés létrehozása az üzenetválasztó portolása                                 |
| createSharedConsumer        | A Service Bus témakörei mindig megoszthatóak, lásd fent                                       |
| createSharedDurableConsumer | A Service Bus témakörei mindig megoszthatóak, lásd fent                                       |
| createTemporaryTopic        | témakör létrehozása felügyeleti API/tools/portal segítségével, ha az *AutoDeleteOnIdle* lejárati időszakra van állítva |
| createTopic                 | téma létrehozása a felügyeleti API/tools/portal segítségével                                           |
| Leiratkozás                 | a témakörkezelés API/tools/portal törlése                                             |
| createBrowser (böngésző) létrehozása               | Támogatott. A Service Bus API Betekintés() funkciójának használata                         |
| létrehozásVárió                 | várólista létrehozása felügyeleti API/eszközök/portál segítségével                                           | 
| createTemporaryQueue        | várólista létrehozása felügyeleti API/tools/portal segítségével, ha az *AutoDeleteOnIdle* lejárati időszakra van állítva |
| receiveNoWait               | használja a Service Bus SDK által biztosított receive() metódust, és adjon meg nagyon alacsony vagy nulla időtúlértéket |

## <a name="summary"></a>Összefoglalás
Ez az útmutató bemutatja, hogyan használhatja a Service Bus felügyelt üzenetkezelési funkcióit (várólisták és közzétételi/előfizetési témakörök) a Java-ból a népszerű JMS API és Az AMQP 1.0 használatával.

A Service Bus AMQP 1.0-s szolgáltatása más nyelvekről is használható, például a .NET, a C, a Python és a PHP. Az ezekkel a különböző nyelvekkel készült összetevők megbízhatóan és teljes hűséggel válthatnak üzeneteket a Service Bus AMQP 1.0 támogatásával.

## <a name="next-steps"></a>További lépések
* [Az AMQP 1.0 támogatása az Azure Service Busban](service-bus-amqp-overview.md)
* [Az AMQP 1.0 használata a Service Bus .NET API-val](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 Fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java fejlesztői központ](https://azure.microsoft.com/develop/java/)

