---
title: "A Java Service Bus API AMQP 1.0 használata |} Microsoft Docs"
description: "Megtudhatja, hogyan használható a Java üzenet szolgáltatás (JMS) Azure Service Bus és speciális Message Queuing Protodol (AMQP) 1.0-s."
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: 
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 0848facd764c4fb0d7f95c1ae89ecb02a32257e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>A Java üzenet szolgáltatás (JMS) API használata a Service Bus és AMQP 1.0
A speciális Message Queuing protokoll (AMQP) 1.0 egy egy hatékony, megbízható, vezetékes szintű üzenetkezelési protokoll, amely segítségével hatékony, platformok közötti üzenetküldő alkalmazások létrehozását.

Támogatás az AMQP 1.0 a Service Bus azt jelenti, hogy a queuing használ, és közzétételi/előfizetési közvetítőalapú üzenettovábbítás szolgáltatások egy hatékony bináris protokollal platformok közötti. Ezenkívül összetevőket használja a nyelveket, keretrendszerek és operációs rendszerek áll alkalmazásokat hozhat létre.

Ez a cikk ismerteti, hogyan Service Bus messaging szolgáltatást (üzenetsorok és témakörök közzétételi/előfizetési) a Java-alkalmazások a népszerű Java üzenet szolgáltatás (JMS) szabványos API használatával. Van egy [kiegészítő cikk](service-bus-amqp-dotnet.md) , amely azt ismerteti, hogyan ehhez a Service Bus .NET API használatával. Ezek az útmutatók két együtt segítségével platformok közötti AMQP 1.0 használatával megismerése.

## <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései
Ez az útmutató feltételezi, hogy már rendelkezik egy Szolgáltatásbusz-névtér nevű várólista tartalmazó **1**. Ha nem, akkor is [a névtér és a várólista létrehozása](service-bus-create-namespace-portal.md) használatával a [Azure-portálon](https://portal.azure.com). A Service Bus-névteret és a várólisták létrehozásával kapcsolatos további információkért lásd: [Ismerkedés a Service Bus-üzenetsorok](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> A particionált üzenetsorok és témakörök is támogatja az AMQP. További információkért lásd: [particionált üzenetküldési entitások](service-bus-partitioning.md) és [AMQP 1.0 támogatása a Service Bus üzenetsorok és témakörök particionálva](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Az AMQP 1.0 JMS ügyféloldali kódtár letöltése
Az Apache Qpid JMS AMQP 1.0 ügyféloldali kódtár legújabb verziójának letöltéséhez kapcsolatos információkért látogasson el a [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Hozzá kell adnia a következő négy JAR-fájlok az Apache Qpid JMS AMQP 1.0 terjesztési archívumból a Java CLASSPATH összeállításakor és a Service busszal JMS alkalmazások futtatásához:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-amqp-1-0-Client-[Version].JAR
* qpid-amqp-1-0-Client-jms-[Version].JAR
* qpid-amqp-1-0-Common-[Version].JAR

## <a name="coding-java-applications"></a>Java-alkalmazások kódolása
### <a name="java-naming-and-directory-interface-jndi"></a>Java-kiosztási és Directory Interface (JNDI)
JMS a Java elnevezésekor és a Directory Interface (JNDI) segítségével hozza létre a logikai és fizikai nevét elkülönítése. Kétféle JMS objektumok pedig használatával oldja fel JNDI: ConnectionFactory és a cél. JNDI amelybe különböző címtárszolgáltatások név feloldása feladatok kezelésére is csatlakoztathatja szolgáltató modellt használ. Az Apache Qpid JMS AMQP 1.0 könyvtár tartalmaz egy egyszerű tulajdonságok fájlalapú JNDI szolgáltató a következő tulajdonságok fájl használatával konfigurált formázása:

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

#### <a name="configure-the-connectionfactory"></a>A ConnectionFactory konfigurálása
A bejegyzés definiáló egy **ConnectionFactory** a Qpid tulajdonságok fájlban JNDI szolgáltató verziója a következő formátumban:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Ha **[jndi_name]** és **[ConnectionURL]** jelentése a következő:

* **[jndi_name]** : A ConnectionFactory logikai nevét. Ez az a név, amely a Java-alkalmazást a JNDI IntialContext.lookup() metódussal megszűnik.
* **[ConnectionURL]** : A JMS könyvtárban a szükséges információt biztosít az AMQP broker URL-címet.

A formátum a **ConnectionURL** a következőképpen történik:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Ha **[névtér]**, **[SASPolicyName]** és **[SASPolicyKey]** jelentése a következő:

* **[névtér]** : A Service Bus-névtér.
* **[SASPolicyName]** : A várólista közös hozzáférésű Jogosultságkód házirend nevét.
* **[SASPolicyKey]** : A várólista közös hozzáférésű Jogosultságkód házirend kulcs.

> [!NOTE]
> URL-kódolja a jelszót manuálisan kell. Hasznos URL-kódolást segédprogram érhető el: [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Célok konfigurálása
Adjon meg egy cél a Qpid tulajdonságok JNDI fájlszolgáltató használt bejegyzés nem a következő formátumban:

```
queue.[jndi_name] = [physical_name]
```

vagy

```
topic.[jndi_name] = [physical_name]
```

Ha **[jndi\_neve]** és **[fizikai\_neve]** jelentése a következő:

* **[jndi_name]** : A cél logikai nevét. Ez az a név, amely a Java-alkalmazást a JNDI IntialContext.lookup() metódussal megszűnik.
* **[physical_name]** :, Amelyhez az alkalmazás küld vagy fogad üzeneteket a Szolgáltatásbusz-entitás nevét.

> [!NOTE]
> Amikor egy Service Bus üzenettémakör-előfizetésben fogad, a fizikai JNDI megadott nevét a nevét, a következő témakörben kell lennie. Az előfizetés név van megadva, ha a tartós előfizetés létrehozása a JMS alkalmazáskód. A [Service Bus AMQP 1.0 fejlesztői útmutató](service-bus-amqp-dotnet.md) JMS a Service Bus-üzenettémakörök használata további részleteket tartalmaz.
> 
> 

### <a name="write-the-jms-application"></a>A JMS-alkalmazás
Nincsenek speciális API-k vagy a Service busszal JMS használatakor szükséges beállításokat. Van azonban néhány olyan korlátozásokat, amelyeket később szerepelnek. Bármilyen JMS alkalmazással először thing szükséges a konfiguráció a JNDI környezet kell tudnia oldani a **ConnectionFactory** és a célhelyre.

#### <a name="configure-the-jndi-initialcontext"></a>A JNDI InitialContext konfigurálása
A JNDI környezet konfigurálva van egy kivonattáblát a konfigurációs adatokat a javax.naming.InitialContext osztály konstruktorának való átadásával. A két szükséges a kivonattábla elemeinek osztály nevét a kezdeti környezet gyári és a szolgáltató URL-CÍMÉT. A következő kód bemutatja, hogyan konfigurálja a JNDI környezetet a Tulajdonságok fájlalapú JNDI szolgáltató nevű tulajdonságok fájlt tartalmazó Qpid használandó **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Egy egyszerű JMS-alkalmazást a Service Bus-üzenetsorba
A következő példa program JMS TextMessages küld egy Service Bus-üzenetsorba VÁRÓLISTA JNDI logikai nevét, és vissza az üzeneteket fogad.

```java
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}    
```

### <a name="run-the-application"></a>Az alkalmazás futtatása
Az alkalmazás futtatása hoz létre a kimenete a következő formátumban:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Platformok közötti JMS és .NET között
Ez az útmutató bemutatta, hogyan küldeni és fogadni az üzeneteket a Service Bus JMS használatával. Azonban a legfontosabb előnyei AMQP 1.0 egyik, hogy engedélyezi-e alkalmazások kialakítani, megbízható és teljes visszaadása által küldött üzenetek a különböző nyelveken írt összetevőkből.

A fent leírt JMS alkalmazást és egy kiegészítő cikk vett hasonló .NET-alkalmazás [Service Bus használatával a .NET-az AMQP 1.0](service-bus-amqp-dotnet.md), .NET és Java közötti üzenetek tudjon cserélni. Ez a cikk platformok közötti Service Bus és AMQP 1.0 használatával további információt a részletekért olvassa el.

### <a name="jms-to-net"></a>A .NET JMS
.NET üzenetkezelés bemutatása a JMS:

* A .NET mintaalkalmazás parancssori argumentum nélkül indítsa el.
* A Java mintaalkalmazás kezdje a "sendonly" parancssori argumentum. Ebben a módban az alkalmazás nem kap meg az üzenetet az üzenetsorból, csak akkor küld.
* Nyomja le az **Enter** néhány alkalommal a Java-alkalmazás konzolon, amelynek hatására üzenetek küldését.
* Ezek az üzenetek a .NET-alkalmazás által fogadott.

#### <a name="output-from-jms-application"></a>JMS alkalmazás kimenete
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>.NET-alkalmazás
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>A JMS .NET
.NET bemutatásához JMS üzenetkezelés:

* .NET mintaalkalmazás kezdje a "sendonly" parancssori argumentum. Ebben a módban az alkalmazás nem kap meg az üzenetet az üzenetsorból, csak akkor küld.
* A Java mintaalkalmazás parancssori argumentum nélkül indítsa el.
* Nyomja le az **Enter** néhány alkalommal a .NET alkalmazás-konzolon, amelynek hatására üzenetek küldését.
* Ezek az üzenetek a Java-alkalmazás által fogadott.

#### <a name="output-from-net-application"></a>.NET-alkalmazás
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>JMS alkalmazás kimenete
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Nem támogatott funkciókat és korlátozások
A következő korlátozások érvényesek, nevezetesen az AMQP 1.0-s, Service Bus keresztül JMS használata esetén:

* Csak egy **MessageProducer** vagy **MessageConsumer** engedélyezett-e **munkamenet**. Ha több létrehozásához szükséges **MessageProducers** vagy **MessageConsumers** alkalmazás, hozzon létre egy dedikált **munkamenet** esetében.
* "Volatile" témakör előfizetések jelenleg nem támogatottak.
* **MessageSelectors** jelenleg nem támogatottak.
* Ideiglenes célok; például **TemporaryQueue**, **TemporaryTopic** jelenleg nem támogatottak, jelszavat rendelkező a **QueueRequestor** és **TopicRequestor**API-t használhatja őket.
* A tranzakción alapuló munkamenetek és az elosztott tranzakciók nem támogatottak.

## <a name="summary"></a>Összefoglalás
Ez az útmutató útmutató bemutatta, hogyan Service Bus közvetítőalapú üzenettovábbítás szolgáltatás használatára (üzenetsorok és témakörök közzétételi/előfizetési) Java a népszerű JMS API és az AMQP 1.0 használatával.

Használhatja a Service Bus AMQP 1.0 más nyelven, beleértve a .NET, a C, a Python és a PHP. Ezek különböző nyelveken használó összetevőket üzenetváltásra megbízhatóan, és a teljes visszaadása az AMQP 1.0 használatával támogatja a Service Bus.

## <a name="next-steps"></a>Következő lépések
* [Az Azure Service Bus AMQP 1.0-támogatás](service-bus-amqp-overview.md)
* [A Service Bus .NET API AMQP 1.0 használata](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0 fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java fejlesztői központban](https://azure.microsoft.com/develop/java/)

