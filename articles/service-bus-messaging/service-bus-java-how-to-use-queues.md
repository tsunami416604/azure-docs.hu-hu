---
title: Az Azure Service Bus-várólisták használata java-val
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre Java-alkalmazásokat üzenetek küldéséhez és fogadásához egy Azure Service Bus-várólistából.
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
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aschhab
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 184ffd39281ea27d8596bc37a9f89fd22acfb1ba
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732182"
---
# <a name="quickstart-use-azure-service-bus-queues-with-java-to-send-and-receive-messages"></a>Rövid útmutató: Üzenetek küldéséhez és fogadásához használja az Azure Service Bus-várólistákat a Java-val

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre Java-alkalmazásokat üzenetek küldéséhez és fogadásához egy Azure Service Bus-várólistából. 

> [!NOTE]
> Java-mintákat a GitHubon az [azure-service-bus tárházban](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)talál.

## <a name="prerequisites"></a>Előfeltételek
1. Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja [az MSDN előfizetői előnyeit,](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) vagy regisztrálhat egy [ingyenes fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Ha nincs várólistája, amivel dolgozhatna, kövesse az Azure Portal használata című lépéseit [a Service Bus-várólista-cikk létrehozásához](service-bus-quickstart-portal.md) egy várólista létrehozásához.
    1. Olvassa el a Service Bus **várólistáinak**gyors **áttekintését.** 
    2. Hozzon létre egy Service Bus **névteret**. 
    3. A **kapcsolati karakterlánc bekapcsolása**.
    4. Hozzon létre egy Service **Bus-várólistát**.
3. Telepítse [az Azure SDK Java-hoz][Azure SDK for Java]című szolgáltatást. 


## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus használatára
Győződjön meg arról, hogy telepítette az [Azure SDK Java-hoz][Azure SDK for Java] a minta létrehozása előtt. 

Ha az Eclipse,telepítheti az [Azure Toolkit for Eclipse,][Azure Toolkit for Eclipse] amely tartalmazza az Azure SDK Java.If you are using Eclipse, you can install the Azure Toolkit for Eclipse that includes the Azure SDK for Java. Ezután hozzáadhatja a **Microsoft Azure Java-kódtárakat** a projekthez. Ha IntelliJ-t használ, [olvassa el az Azure Toolkit for IntelliJ című témakört.](/azure/developer/java/toolkit-for-intellij/installation) 

![A Java-hoz készült Microsoft Azure-kódtárak hozzáadása az Eclipse projekthez](./media/service-bus-java-how-to-use-queues/eclipse-azure-libraries-java.png)


Adja hozzá `import` a következő állításokat a Java-fájl tetejéhez:

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
Ha üzeneteket szeretne küldeni egy service buszvárólistába, az alkalmazás egy **QueueClient** objektumot hoz végre, és aszinkron módon küld üzeneteket. A következő kód bemutatja, hogyan küldhet üzenetet a portálon keresztül létrehozott várólistához.

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

A Service Bus várólistáiba küldött és onnan fogadott üzenetek az [Üzenet](/java/api/com.microsoft.azure.servicebus.message?view=azure-java-stable) osztály példányai. Az üzenetobjektumok szabványos tulajdonságokkal (például Címke és TimeToLive), egyéni alkalmazásspecifikus tulajdonságok tárolására használt szótárral és tetszőleges alkalmazásadatokkal rendelkeznek. Az alkalmazás beállíthatja az üzenet törzsét úgy, hogy bármilyen szerializálható objektumot átad az üzenet konstruktorának, és a megfelelő szerializáló t használja az objektum szerializálására. Alternatív megoldásként, akkor a **java. Io. InputStream** objektum.


A Service Bus-üzenetsorok a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Az üzenetsorban tárolt üzenetek száma korlátlan, az üzenetsor által tárolt üzenetek teljes mérete azonban korlátozva van. Az üzenetsor ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása várólistából
A várólistából érkező üzenetek fogadásának elsődleges módja egy **ServiceBusContract** objektum használata. A fogadott üzenetek két különböző módban működhetnek: **ReceiveAndDelete** és **PeekLock**.

**A ReceiveAndDelete** mód használataesetén a fogadás egyegylatú művelet , azaz amikor a Service Bus olvasási kérelmet kap egy üzenetre egy várólistában, az üzenetet felhasználtként jelöli meg, és visszaküldi az alkalmazásnak. **ReceiveAndDelete** mód (amely az alapértelmezett mód) a legegyszerűbb modell, és működik a legjobban olyan esetekben, amikor egy alkalmazás tűri nem dolgoz fel egy üzenetet hiba esetén. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt.
Mivel a Service Bus az üzenetet felhasználtként jelölte meg, majd amikor az alkalmazás újraindul, és újra elkezdi az üzenetek felhasználását, az összeomlás előtt felhasznált üzenetet kihagyta.

**PeekLock** módban a fogadás kétlépcsős műveletté válik, amely lehetővé teszi olyan alkalmazások támogatását, amelyek nem tolerálják a hiányzó üzeneteket. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja a jövőbeli feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a **complete()** hívásával a fogadott üzeneten. Amikor a Service Bus látja a **complete()** hívást, az üzenetet felhasználásként jelöli meg, és eltávolítja a várólistából. 

A következő példa bemutatja, hogyan fogadhatók és dolgozhatók fel az üzenetek **peeklock** módban (nem az alapértelmezett módban). Az alábbi példa a visszahívási modellt használja egy regisztrált üzenetkezelővel, és feldolgozza az üzeneteket, amint megérkeznek a rendszerünkbe. `TestQueue` Ez a mód automatikusan meghívja a **complete() függvényt,** ahogy a visszahívás a szokásos módon tér vissza, és a **(hagyd)** függvényt hívja, ha a visszahívás kivételt okoz. 

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor meghívhatja az **ügyfélobjektum abandon()** metódusát a **getLockToken()** keresztül kapott zárolási jogkivonattal. Ennek hatására a Service Bus feloldja az üzenet zárolását az üzenetsoron belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Van is egy időtúltöltés társított egy üzenetet zárva a várólistán belül, és ha az alkalmazás nem dolgozza fel az üzenetet, mielőtt a zárolási időtúltöltés lejár (például, ha az alkalmazás összeomlik), majd a Service Bus automatikusan feloldja az üzenetet, és elérhetővé teszi, hogy újra kell fogadni.

Abban az esetben, ha az alkalmazás összeomlik az üzenet feldolgozása után, de a **complete()** kérelem kiadása előtt, majd az üzenet újrakézbesítése az alkalmazás, amikor újraindul. Ezt gyakran *nevezik legalább egyszer feldolgozás*; ez azt illeti, minden üzenet feldolgozása legalább egyszer, de bizonyos helyzetekben ugyanazt az üzenetet lehet kézbesíteni. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el a **getMessageId** módszer az üzenet, amely állandó marad a kézbesítési kísérletek során.

> [!NOTE]
> A Service Bus erőforrásait a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)segítségével kezelheti. A Service Bus Explorer lehetővé teszi a felhasználók számára, hogy csatlakozzanak a Service Bus névtér és felügyeli az üzenetkezelési entitások egyszerű módon. Az eszköz speciális funkciókat biztosít, például importálási/exportálási funkciókat, vagy a témakör, a várólisták, az előfizetések, a továbbítási szolgáltatások, az értesítési központok és az eseményközpontok tesztelését. 

## <a name="next-steps"></a>Következő lépések
Most, hogy megtanulta a Service Bus-várólisták alapjait, további információért olvassa [el a Várólisták, témakörök és előfizetések című témakört.][Queues, topics, and subscriptions]

További információ: [Java fejlesztői központ](https://azure.microsoft.com/develop/java/).

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
