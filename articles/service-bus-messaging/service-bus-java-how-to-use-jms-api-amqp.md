---
title: Az AMQP 1.0 használata a Java Service Bus API |} A Microsoft Docs
description: Hogyan használható a Java Message Service (JMS) az Azure Service Bus és az Advanced Message Queuing Protodol AMQP 1.0.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2018
ms.author: spelluru
ms.openlocfilehash: a3274053e772cbdf120be15a385c84d5ae37d610
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392652"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>A Service Bus és az AMQP 1.0-t a Java Message Service (JMS) API használata
A speciális Message Queuing AMQP protokoll 1.0-s egy hatékony, megbízható, alkalmazásszintű üzenetkezelő protokoll, amely nagy teherbírású, többplatformos üzenetkezelési alkalmazások létrehozásához használható.

Támogatás a Service Bus AMQP 1.0-s azt jelenti, hogy az üzenetsor-kezelési használ, és közzététel és feliratkozás közvetítőalapú üzenettovábbítási szolgáltatások listája egy hatékony bináris protokoll használatával platformok esetében. Emellett hozhat létre olyan alkalmazásokat, használja a programozási nyelvek, keretrendszerek és operációs rendszerek-összetevői áll.

Ez a cikk ismerteti a Service Bus üzenetkezelési funkcióit (az üzenetsorok és üzenettémák közzététele vagy előfizetése) Javás alkalmazásokból a népszerű Java Message Service (JMS) szabványos API használatával. Van egy [kiegészítő cikk](service-bus-amqp-dotnet.md) , amely azt ismerteti, hogyan végezze el ugyanezt a Service Bus .NET API használatával. Ezek az útmutatók két együtt használható többplatformos üzenetkezelés az AMQP 1.0-val kapcsolatos.

## <a name="get-started-with-service-bus"></a>A Service Bus használatának első lépései
Ez az útmutató feltételezi, hogy már rendelkezik egy Service Bus-névtér nevű üzenetsor tartalmazó **1**. Ha nem, akkor is [a névtér és üzenetsor létrehozása](service-bus-create-namespace-portal.md) használatával a [az Azure portal](https://portal.azure.com). Service Bus-névterek és az üzenetsorok létrehozásával kapcsolatos további információkért lásd: [Ismerkedés a Service Bus-üzenetsorok](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Particionált üzenetsorok és témakörök is támogatják az AMQP. További információkért lásd: [particionált üzenetküldési entitások](service-bus-partitioning.md) és [a Service Bus AMQP 1.0 támogatása particionált üzenetsorok és témakörök](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Az AMQP 1.0-s JMS ügyféloldali kódtár letöltése
Az Apache Qpid JMS AMQP 1.0-s ügyféloldali kódtár legújabb verzióját töltse le, hogy hol kapcsolatos információkért látogasson el a [ https://qpid.apache.org/download.html ](https://qpid.apache.org/download.html).

Hozzá kell adnia a következő négy JAR-fájlok az Apache Qpid JMS AMQP 1.0-s terjesztési archívból a Java OSZTÁLYÚTVONAL összeállításakor és a Service Bus JMS alkalmazások futtatásához:

* geronimo-jms\_1.1\_specifikációja-1.0.jar
* qpid-amqp-1-0-Client-[Version].JAR
* qpid-amqp-1-0-Client-jms-[Version].JAR
* qpid-amqp-1-0-Common-[Version].JAR

## <a name="coding-java-applications"></a>Kódolási Java-alkalmazások
### <a name="java-naming-and-directory-interface-jndi"></a>Java-kiosztási és a Directory Interface (JNDI)
JMS a Java-kiosztási és a Directory Interface (JNDI) használatával hozzon létre egy logikai és fizikai nevének elkülönítése. Két típusú JMS objektumok használatával JNDI feloldása: ConnectionFactory és a célkiszolgálón. JNDI, amelybe különböző címtárszolgáltatások név feloldása feladatkörök kezelésére is csatlakoztathatja szolgáltató modellt használ. Az Apache Qpid JMS AMQP 1.0 könyvtár tartalmaz egy egyszerű tulajdonságok fájlalapú JNDI szolgáltató, amely konfigurálva van a következő tulajdonságok fájl használatával formázása:

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
A bejegyzés definiáló egy **ConnectionFactory** a Qpid tulajdonságok fájlban JNDI szolgáltató a következő formátumban van:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Ahol **[jndi_name]** és **[ConnectionURL]** jelentése a következő:

* **[jndi_name]** : A ConnectionFactory annak a logikai neve. Ez az a név, amely a Java-alkalmazás, a JNDI IntialContext.lookup() metódussal megszűnik.
* **[ConnectionURL]** : Egy URL-címet, amely a JMS kódtár biztosít szükséges adatokat az AMQP közvetítőnek.

Formátumát a **ConnectionURL** a következő:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Ahol **[névtér]**, **[SASPolicyName]** és **[SASPolicyKey]** jelentése a következő:

* **[névtér]** : A Service Bus-névteret.
* **[SASPolicyName]** : A várólista közös hozzáférésű Jogosultságkód szabályzat neve.
* **[SASPolicyKey]** : A várólista közös hozzáférésű Jogosultságkód házirendjének kulcsa.

> [!NOTE]
> URL-kódolása jelszavát manuálisan kell. Elérhető legyen a hasznos URL-Címének kódolása segédprogram [ http://www.w3schools.com/tags/ref_urlencode.asp ](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Célhelyek konfigurálása
A használt határozhat meg a célhelyet a Qpid tulajdonságok JNDI szolgáltató bejegyzés van a következő formátumban:

```
queue.[jndi_name] = [physical_name]
```

vagy

```
topic.[jndi_name] = [physical_name]
```

Ahol **[jndi\_neve]** és **[fizikai\_neve]** jelentése a következő:

* **[jndi_name]** : A logikai neve, a cél. Ez az a név, amely a Java-alkalmazás, a JNDI IntialContext.lookup() metódussal megszűnik.
* **[physical_name]** : A Service Bus entitásban, amelyhez az alkalmazás fogad vagy küld üzeneteket nevét.

> [!NOTE]
> Ha fogad egy Service Bus témakör-előfizetésből, a JNDI fizikai nevet az üzenettéma nevére kell lennie. Az előfizetés neve van megadva, a tartós előfizetés létrehozásakor az JMS alkalmazáskód. A [Service Bus AMQP 1.0-s fejlesztői útmutató](service-bus-amqp-dotnet.md) JMS a Service Bus-üzenettémakörök használata további részleteket.
> 
> 

### <a name="write-the-jms-application"></a>A JMS alkalmazás írása
Nincsenek speciális API-k vagy a Service Bus JMS használatakor szükséges beállításokat. Azonban néhány korlátozások vonatkoznak, amelyek később tárgyalja. Mivel minden JMS alkalmazással thing először szükség tudják feloldani, a JNDI környezet konfigurációjától egy **ConnectionFactory** forrásaként és céljaként.

#### <a name="configure-the-jndi-initialcontext"></a>A JNDI InitialContext konfigurálása
A JNDI környezet konfigurálva van egy kivonattáblát a konfigurációs adatokat a javax.naming.InitialContext osztály konstruktorának való átadásával. A két szükséges elemeket a kivonattábla kulcsa az osztály nevét a kezdeti környezet Factory és a szolgáltató URL-címe. A következő kód bemutatja, hogyan használja a Tulajdonságok fájlalapú JNDI szolgáltató tulajdonságai nevű fájlt a Qpid a JNDI környezet beállításait **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Egy egyszerű JMS alkalmazást, az Service Bus-üzenetsorba
A következő példa program JMS TextMessages küld egy Service Bus-üzenetsorba VÁRÓLISTA JNDI logikai nevét, és fogadja az üzeneteket vissza.

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
Az alkalmazás futtatása hoz létre a kimenet a következő formában:

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

## <a name="cross-platform-messaging-between-jms-and-net"></a>Többplatformos üzenetkezelés közötti JMS és .NET
Ez az útmutató bemutatta, hogyan küldhet és fogadhat üzeneteket, és a Service Bus JMS használatával. Egyik fő előnye az AMQP 1.0-s, azonban, hogy engedélyezi-e a megbízható és teljes visszaadása által küldött üzenetek a különböző nyelveken írt összetevőket kell létrehozni alkalmazásokat.

Használatával a fent ismertetett mintaalkalmazás JMS és a egy kiegészítő cikk származó hasonló .NET-alkalmazás [a Service Bus használata a .NET és az AMQP 1.0-s](service-bus-amqp-dotnet.md), .NET, a Java közötti üzenetek is cserél. Ez a cikk további információt a részletek a többplatformos üzenetkezelés a Service Bus és az AMQP 1.0 használatával.

### <a name="jms-to-net"></a>.NET-re JMS
Bemutatásához JMS .NET üzenetkezelési:

* Indítsa el a .NET mintaalkalmazás nélkül parancssori argumentumokat.
* A Java-mintaalkalmazás kezdje a "sendonly" parancssori argumentum. Ebben a módban az alkalmazás nem fogad üzeneteket az üzenetsorból, csak akkor küld.
* Nyomja meg **Enter** néhány alkalommal a Java-alkalmazás konzolon amely hatására a rendszer üzenetek küldését.
* Ezeket az üzeneteket a .NET-alkalmazás által fogadott.

#### <a name="output-from-jms-application"></a>JMS alkalmazás kimenete
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>.NET-alkalmazás kimenete
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>.NET-JMS
.NET bemutatásához JMS üzenetkezelési:

* Indítsa el a .NET-mintaalkalmazást a "sendonly" parancssori argumentummal. Ebben a módban az alkalmazás nem fogad üzeneteket az üzenetsorból, csak akkor küld.
* Indítsa el a Java-mintaalkalmazás nélkül parancssori argumentumokat.
* Nyomja meg **Enter** néhány alkalommal a .NET alkalmazás-konzolon, amely hatására a rendszer üzenetek küldését.
* Ezeket az üzeneteket a Java-alkalmazás által fogadott.

#### <a name="output-from-net-application"></a>.NET-alkalmazás kimenete
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

## <a name="unsupported-features-and-restrictions"></a>Nem támogatott szolgáltatások és korlátozások
A következő korlátozások érvényesek a JMS nevezetesen keresztül a Service Bus, az AMQP 1.0-s használata esetén:

* Csak egy **MessageProducer** vagy **MessageConsumer** engedélyezett **munkamenet**. Ha szeretne létrehozni több **MessageProducers** vagy **MessageConsumers** egy alkalmazásban, hozzon létre egy dedikált **munkamenet** mindegyikük számára.
* Ideiglenes előfizetések jelenleg nem támogatottak.
* **MessageSelectors** jelenleg nem támogatottak.
* Ideiglenes destinations; Ha például **TemporaryQueue**, **TemporaryTopic** jelenleg nem támogatottak, valamint az a **QueueRequestor** és **TopicRequestor**API-kat használja őket.
* Tranzakciós munkamenetek és az elosztott tranzakciók nem támogatottak.

## <a name="summary"></a>Összegzés
Ez az útmutató bemutatta, hogyan használható a Service Bus által felügyelt üzenettovábbítási szolgáltatások (az üzenetsorok és üzenettémák közzététele vagy előfizetése) az Java a népszerű JMS API és az AMQP 1.0 használatával.

Használhatja a Service Bus AMQP 1.0-t a többi nyelvet – például a .NET, C, Python és PHP. Eltérő nyelvek használatával létrehozott összetevők is megbízhatóan váltanak üzeneteket, és teljes visszaadása az AMQP 1.0 használatával támogatja a Service Bus.

## <a name="next-steps"></a>További lépések
* [Az Azure Service Bus AMQP 1.0-támogatás](service-bus-amqp-overview.md)
* [Az AMQP 1.0 használata a Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)
* [Service Bus AMQP 1.0-s – fejlesztői útmutató](service-bus-amqp-dotnet.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [Java fejlesztői központ](https://azure.microsoft.com/develop/java/)

