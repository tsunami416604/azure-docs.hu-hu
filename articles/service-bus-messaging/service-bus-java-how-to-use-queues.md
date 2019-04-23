---
title: Az Azure Service Bus-üzenetsorok használata javával |} A Microsoft Docs
description: Ismerje meg, hogyan használhatók a Service Bus-üzenetsorok az Azure-ban. A Java nyelven írt kódmintákat.
services: service-bus-messaging
documentationcenter: java
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f701439c-553e-402c-94a7-64400f997d59
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 958dc02ecc744f89badee851467f2e78190287cd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786939"
---
# <a name="how-to-use-service-bus-queues-with-java"></a>Service Bus-üzenetsorok használata javával
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre Java-alkalmazások üzeneteket küldeni, illetve üzeneteket fogadhat a Service Bus-üzenetsorba. 

> [!NOTE]
> Java-példák a githubon találja a [azure szolgáltatásbusz-tárház](https://github.com/Azure/azure-service-bus/tree/master/samples/Java).

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja a [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Ha nem rendelkezik egy üzenetsorba való együttműködéshez, kövesse lépéseket a [egy Service Bus-üzenetsor létrehozása az Azure portal](service-bus-quickstart-portal.md) várólista létrehozásához a cikkben.
    1. Olvassa el a gyors **áttekintése** Service Bus **üzenetsorok**. 
    2. Hozzon létre egy Service Bus **névtér**. 
    3. Első a **kapcsolati karakterlánc**.
    4. Hozzon létre egy Service Bus **várólista**.
3. Telepítés [Javához készült Azure SDK][Azure SDK for Java]. 


## <a name="configure-your-application-to-use-service-bus"></a>A Service Bus-alkalmazás konfigurálása
Győződjön meg arról, hogy telepítette a [Javához készült Azure SDK] [ Azure SDK for Java] Ez a minta létrehozása előtt. Ha az Eclipse használja, telepítheti a [Azure Toolkit for Eclipse] [ Azure Toolkit for Eclipse] , amely tartalmazza a Javához készült Azure SDK. Ezután hozzáadhatja a **Javához készült Azure-könyvtárak Microsoft** a projekthez:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Adja hozzá a következő `import` utasításokat a Java-fájl elejéhez:

```java
// Include the following imports to use Service Bus APIs
import com.google.gson.reflect.TypeToken;
import com.microsoft.azure.servicebus.*;
import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import com.google.gson.Gson;

import static java.nio.charset.StandardCharsets.*;

import java.time.Duration;
import java.util.*;
import java.util.concurrent.*;

import org.apache.commons.cli.*;

```

## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Üzeneteket küld egy Service Bus-üzenetsorba, hogy az alkalmazás példányosít egy **QueueClient** objektumra, és aszinkron módon üzeneteket küld. A következő kód bemutatja, hogyan üzenet küldése a portálon keresztül létrehozott üzenetsorhoz.

```java
public void run() throws Exception {
    // Create a QueueClient instance and then asynchronously send messages.
    // Close the sender once the send operation is complete.
    QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
    this.sendMessageAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());

    sendClient.close();
}

    CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
        List<HashMap<String, String>> data =
                GSON.fromJson(
                        "[" +
                                "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                                "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                                "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                                "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                                "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                                "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                                "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                                "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                                "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                                "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                                "]",
                        new TypeToken<List<HashMap<String, String>>>() {}.getType());

        List<CompletableFuture> tasks = new ArrayList<>();
        for (int i = 0; i < data.size(); i++) {
            final String messageId = Integer.toString(i);
            Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
            message.setContentType("application/json");
            message.setLabel("Scientist");
            message.setMessageId(messageId);
            message.setTimeToLive(Duration.ofMinutes(2));
            System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
            tasks.add(
                    sendClient.sendAsync(message).thenRunAsync(() -> {
                        System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
                    }));
        }
        return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
    }

```

Küldi az üzeneteket, és a Service Bus kapott példányai a [üzenet](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) osztály. Üzenet objektumok egy szabványos tulajdonságkészlettel (például a címke és a TimeToLive) rendelkeznek, egyéni alkalmazásspecifikus tulajdonságokat használt, valamint egy tetszőleges alkalmazásadatokból álló törzzsel. Az alkalmazás beállíthatja az üzenet törzse bármilyen szerializálható objektumnak a üzenet konstruktorának való átadásával, és a megfelelő szerializáló ezután a rendszer használja az objektumot. Másik lehetőségként megadhat egy **java. IO. InputStream** objektum.


A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy üzenetsorból
Üzenetek fogadása egy üzenetsorból elsődleges módja egy **ServiceBusContract** objektum. A fogadott üzenetek két különböző módban tudnak működni: **ReceiveAndDelete** és **PeekLock**.

Használatakor a **ReceiveAndDelete** mód, kap egy egylépéses művelet –, amelyek a Service Bus egy üzenetsorban lévő üzenet egy olvasási kérést kap, amikor azt jelöli meg az üzenetet, és visszaadja az alkalmazásnak. **ReceiveAndDelete** módban (amely az alapértelmezett mód) a legegyszerűbb modell, és leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet egy hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt.
Mivel a Service Bus az üzenetet, van megjelölve, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, kimaradt az összeomlás előtt feldolgozott üzenetet.

A **PeekLock** mód, kap egy két szakaszból álló művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), **törlése** a fogadott üzenethez. Amikor a Service Bus látja a **törlése** hívást, feldolgozottként jelöli meg az üzenetet, és távolítsa el az üzenetsorból.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával **PeekLock** módban (nem az alapértelmezett mód). Az alábbi példa egy végtelen ciklust nem, és üzeneteket dolgoz fel, akkor azokat el `TestQueue`:

```java
    public void run() throws Exception {
        // Create a QueueClient instance for receiving using the connection string builder
        // We set the receive mode to "PeekLock", meaning the message is delivered
        // under a lock and must be acknowledged ("completed") to be removed from the queue
        QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
        this.registerReceiver(receiveClient);

        // shut down receiver to close the receive loop
        receiveClient.close();
    }
    void registerReceiver(QueueClient queueClient) throws Exception {
        // register the RegisterMessageHandler callback
        queueClient.registerMessageHandler(new IMessageHandler() {
        // callback invoked when the message handler loop has obtained a message
            public CompletableFuture<Void> onMessageAsync(IMessage message) {
            // receives message is passed to callback
                if (message.getLabel() != null &&
                    message.getContentType() != null &&
                    message.getLabel().contentEquals("Scientist") &&
                    message.getContentType().contentEquals("application/json")) {

                        byte[] body = message.getBody();
                        Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                        System.out.printf(
                            "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                            "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                            message.getMessageId(),
                            message.getSequenceNumber(),
                            message.getEnqueuedTimeUtc(),
                            message.getExpiresAtUtc(),
                            message.getContentType(),
                            scientist != null ? scientist.get("firstName") : "",
                            scientist != null ? scientist.get("name") : "");
                    }
                    return CompletableFuture.completedFuture(null);
                }

                // callback invoked when the message handler has an exception to report
                public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                    System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                }
        },
        // 1 concurrent call, messages are auto-completed, auto-renew duration
        new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));
    }

```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az **unlockMessage** metódust a fogadott üzenethez (helyett a **deleteMessage** metódus). Ennek hatására a Service Bus feloldja az üzenet zárolását az üzenetsoron belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van, az üzenetsorban lévő üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és lehetővé teszi elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a **deleteMessage** kérés kiadása, akkor az üzenet újbóli kézbesítése az alkalmazáshoz, amikor újraindul. Ezt a módszert gyakran *Legalább egyszeri feldolgozásnak* nevezik. Ez azt jelenti, hogy minden üzenet legalább egyszer fel lesz dolgozva, de bizonyos helyzetekben előfordulhat ugyanazon üzenet újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el használatával a **getMessageId** metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, [üzenetsorok, témakörök és előfizetések] [ Queues, topics, and subscriptions] további információt.

További információ: [Java fejlesztői központ](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: https://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
