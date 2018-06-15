---
title: Azure Service Bus-üzenetsorok használata Java |} Microsoft Docs
description: Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. A Kódminták Java nyelven.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 170f431525ffdc93a01fc085e48e69c3a774968e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868430"
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Service Bus-üzenetsorok használata Java
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Ez a cikk a Service Bus-üzenetsorok használatát ismerteti. A mintákat a Java és -felhasználási nyelven íródtak a [Javához készült Azure SDK][Azure SDK for Java]. Az ismertetett forgatókönyvek **üzenetsorok létrehozása**, **üzenetek küldése és fogadása**, és **várólisták törlése**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Állítsa be az alkalmazását, Service Bus használatára
Győződjön meg arról, hogy telepítette a [Javához készült Azure SDK] [ Azure SDK for Java] Ez a minta létrehozása előtt. Ha az Eclipse használja, telepítheti a [Eclipse Azure eszköztára] [ Azure Toolkit for Eclipse] , amely tartalmazza az Azure SDK for Java. Ezután a **Microsoft Azure-könyvtárakban Java** a projekthez:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adja hozzá a következő `import` utasítást, hogy a Java-fájl elejéhez:

```java
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása
Felügyeleti műveletek a Service Bus-üzenetsorok használatával is kell végezni a **ServiceBusContract** osztály. A **ServiceBusContract** objektum van kialakítani, amely magában foglalja a SAS-jogkivonat való kezelése, a megfelelő konfiguráció és a **ServiceBusContract** osztálya: Azure kommunikáció egyetlen pont.

A **ServiceBusService** osztály létrehozásához, enumerálásához és törléséhez várólisták metódusokat biztosít. Az alábbi példa hogyan egy **ServiceBusService** objektum segítségével létrehoz egy sort nevű `TestQueue`, nevű névtérrel `HowToSample`:

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

Módszer áll rendelkezésre a `QueueInfo` , amelyek lehetővé teszik a kell beállítani a várólista tulajdonságainak (például: alapértelmezett--élettartama (TTL) értékének beállítására a várólistára küldött üzenetek alkalmazandó). A következő példa bemutatja, hogyan nevű várólista létrehozása `TestQueue` 5 GB maximális mérettel:

````java
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Vegye figyelembe, hogy használhatja a `listQueues` metódusa **ServiceBusContract** objektumok kereséséhez, ha a megadott névvel már létezik sor egy szolgáltatásnévtérben.

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
A Service Bus-üzenetsorba való üzenetküldéshez, az alkalmazás beolvassa a **ServiceBusContract** objektum. A következő kód bemutatja, hogyan küldhető a `TestQueue` a korábban létrehozott várólista a `HowToSample` névtér:

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

Küldött üzeneteket, és kapott a Service Bus üzenetsorok példánya a [BrokeredMessage] [ BrokeredMessage] osztály. [BrokeredMessage] [ BrokeredMessage] objektumok rendelkeznek egy szabványos tulajdonságkészlettel (például [címke](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) és [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.timetolive#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), egyéni tárolására használt dictionary az alkalmazás-specifikus tulajdonságait, és egy tetszőleges alkalmazásadatokból álló törzzsel. Az alkalmazás beállíthatja az üzenet törzsét bármilyen szerializálható objektumnak konstruktorának való átadásával a [BrokeredMessage][BrokeredMessage], és a megfelelő szerializáló majd használható szerializálja az objektumot. Másik lehetőségként megadhat egy **java. IO. InputStream** objektum.

A következő példa bemutatja, hogyan küldhető öt tesztüzenet az a `TestQueue` **MessageSender** azt a korábbi kódrészletben kapott:

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
Az elsődleges üzenetek fogadása egy üzenetsorból módja használja egy **ServiceBusContract** objektum. A fogadott üzenetek a két különböző módban tudnak működni: **ReceiveAndDelete** és **PeekLock**.

Használatakor a **ReceiveAndDelete** mód, kap egy egylépéses művelet – Ez azt jelenti, hogy amikor a Service Bus egy olvasási kérést kap a sorhoz, azt az üzenetet, feldolgozottként jelöli meg, és visszaadja az alkalmazásnak. **ReceiveAndDelete** módban (amely az alapértelmezett mód) a legegyszerűbb modell, és működik a legjobban forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt.
Mivel a Service Bus csak fel az üzenetet, feldolgozottként, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor ki fogja hagyni a az összeomlás előtt feldolgozott üzenetet.

A **PeekLock** mód, kap egy két szakaszból álló művelet, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával **törlése** metódus a fogadott üzenethez. Amikor a Service Bus látja a **törlése** hívás, azt az üzenetet használnak, és távolítsa el a sorból.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával **PeekLock** mode (nem az alapértelmezett mód). Az alábbi példában nem végtelen hurkot, és feldolgozza a üzenetek megérkeznek a `TestQueue`:

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a **unlockMessage** metódust a fogadott üzenethez (ahelyett, hogy a **deleteMessage** metódus). Ennek hatására a Service Bus feloldja az üzenet zárolását az üzenetsoron belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Még nincs hozzárendelve az üzenetsorban lévő időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időtúllépését lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és lehetővé teszi az elérhető ismételt fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a **deleteMessage** kérés kiadása, akkor az üzenet újból kézbesítve az alkalmazásnak, amikor újraindul. Ezt gyakran nevezik *legalább egyszeri feldolgozásnak*, ez azt jelenti, hogy minden üzenet legalább egyszer fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el, használja a **getMessageId** metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, lásd: [üzenetsorok, témakörök és előfizetések] [ Queues, topics, and subscriptions] további információt.

További információ: [Java fejlesztői központ](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
