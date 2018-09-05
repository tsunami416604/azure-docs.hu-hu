---
title: Az Azure Service Bus-üzenetsorok használata javával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. A Java nyelven írt kódmintákat.
services: service-bus-messaging
documentationcenter: java
author: spelluru
manager: timlt
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: e4099c8228e9434276242a3c49ebcb4fc2e995b2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696465"
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Service Bus-üzenetsorok használata javával
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk a Service Bus-üzenetsorok használatát ismerteti. Java- és használati minták íródnak a [Javához készült Azure SDK][Azure SDK for Java]. Az ismertetett forgatókönyvek között megtalálható **üzenetsorok létrehozása**, **üzenetek küldése és fogadása**, és **üzenetsorok törlése**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>A Service Bus-alkalmazás konfigurálása
Győződjön meg arról, hogy telepítette a [Javához készült Azure SDK] [ Azure SDK for Java] Ez a minta létrehozása előtt. Ha az Eclipse használja, telepítheti a [Azure Toolkit for Eclipse] [ Azure Toolkit for Eclipse] , amely tartalmazza a Javához készült Azure SDK. Ezután hozzáadhatja a **Javához készült Azure-könyvtárak Microsoft** a projekthez:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adja hozzá a következő `import` utasításokat a Java-fájl elejéhez:

```java
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása
Felügyeleti műveletek a Service Bus-üzenetsorok használatával is elvégezhető a **ServiceBusContract** osztály. A **ServiceBusContract** objektum, amely magában foglalja a SAS-jogkivonat engedélyek kezeléséhez, a megfelelő konfiguráció úgy van felépítve, és a **ServiceBusContract** osztály egyetlen pont kommunikáció az Azure-ral.

A **ServiceBusService** osztály metódusokat létrehozásához, enumerálásához és törléséhez az üzenetsorokat biztosít. Az alábbi példában hogyan egy **ServiceBusService** objektum segítségével nevű üzenetsor létrehozása `TestQueue`, nevű névtér `HowToSample`:

```java
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

A módszer van `QueueInfo` , amelyek engedélyezik a beállítani, hogy a várólista tulajdonságainak (például: alapértelmezett time-to-live (Élettartam TTL) értéket a alkalmazni lehessen az üzenetsorba küldött üzenetek). Az alábbi példa bemutatja, hogyan hozhat létre nevű üzenetsor `TestQueue` 5 GB maximális mérettel:

````java
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Vegye figyelembe, hogy használhatja a `listQueues` metódust **ServiceBusContract** objektumok ellenőrizheti, ha egy adott nevű üzenetsor egy szolgáltatásnévtérben már létezik.

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Service Bus-üzenetsorba egy üzenet küldéséhez, hogy az alkalmazás lekéri a **ServiceBusContract** objektum. A következő kód bemutatja, hogyan lehet elküldeni egy üzenetet a `TestQueue` korábban létrehozott üzenetsorba a `HowToSample` névteret:

```java
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Küldi az üzeneteket, és a Service Bus kapott példányai a [BrokeredMessage] [ BrokeredMessage] osztály. [BrokeredMessage] [ BrokeredMessage] objektumok rendelkeznek egy szabványos tulajdonságkészlettel (például [címke](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) és [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.timetolive#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), egy szótár, amely egyéni tárolására szolgál alkalmazásspecifikus tulajdonságokat, és a egy tetszőleges alkalmazásadatokból álló törzzsel törzse. Az alkalmazás beállíthatja az üzenet törzse bármilyen szerializálható objektumnak konstruktorának való átadásával a [BrokeredMessage][BrokeredMessage], és a megfelelő szerializáló ezután a rendszer használja az objektumot. Másik lehetőségként megadhat egy **java. IO. InputStream** objektum.

Az alábbi példa bemutatja, hogyan küldhető öt tesztüzenet az a `TestQueue` **MessageSender** azt az előző kódrészletben kapott:

```java
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Üzenetek fogadása egy üzenetsorból elsődleges módja egy **ServiceBusContract** objektum. A fogadott üzenetek használható két különböző módban: **ReceiveAndDelete** és **PeekLock**.

Használatakor a **ReceiveAndDelete** mód, kap egy egylépéses művelet –, amelyek a Service Bus egy üzenetsorban lévő üzenet egy olvasási kérést kap, amikor azt jelöli meg az üzenetet, és visszaadja az alkalmazásnak. **ReceiveAndDelete** módban (amely az alapértelmezett mód) a legegyszerűbb modell, és leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet egy hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt.
Mivel a Service Bus az üzenetet, jelölte, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

A **PeekLock** mód, kap egy két szakaszból álló művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), **törlése** a fogadott üzenethez. Amikor a Service Bus látja a **törlése** hívást, fog jelölje meg az üzenetet, és távolítsa el az üzenetsorból.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával **PeekLock** módban (nem az alapértelmezett mód). Az alábbi példa egy végtelen ciklust nem, és üzeneteket dolgoz fel, akkor azokat el `TestQueue`:

```java
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                 service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az **unlockMessage** metódust a fogadott üzenethez (helyett a **deleteMessage** metódus). Ennek hatására a Service Bus feloldja az üzenet zárolását az üzenetsoron belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van, az üzenetsorban lévő üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és lehetővé teszi elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a **deleteMessage** kérés kiadása, akkor az üzenet újbóli kézbesítése az alkalmazáshoz, amikor újraindul. Ezt gyakran nevezik *legalább egyszeri feldolgozásnak*; azaz minden üzenetet legalább egyszer dolgozza fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el használatával a **getMessageId** metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, [üzenetsorok, témakörök és előfizetések] [ Queues, topics, and subscriptions] további információt.

További információ: [Java fejlesztői központ](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
