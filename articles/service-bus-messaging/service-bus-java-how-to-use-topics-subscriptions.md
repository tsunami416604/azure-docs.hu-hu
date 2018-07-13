---
title: Azure Service Bus-üzenettémakörök használata javával |} A Microsoft Docs
description: Service Bus-üzenettémák és előfizetések használata az Azure-ban.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 63d6c8bd-8a22-4292-befc-545ffb52e8eb
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/17/2017
ms.author: sethm
ms.openlocfilehash: 9c2501840b3c00a63b0344d48e3225fd2c9d1620
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38701991"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Service Bus-üzenettémák és előfizetések használata javával

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez az útmutató azt ismerteti, hogyan használható a Service Bus-üzenettémák és előfizetések. Java- és használati minták íródnak a [Javához készült Azure SDK][Azure SDK for Java]. Az ismertetett forgatókönyvek között megtalálható **üzenettémák és előfizetések létrehozása**, **előfizetés-szűrők létrehozása**, **üzenetek küldése egy üzenettémakörbe**, **fogadása előfizetés üzeneteit**, és **üzenettémák és előfizetések törlése**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

A Service Bus-üzenetsorokkal ellentétben, amely esetében minden üzenetet egy-egy fogyasztó dolgoz fel, az üzenettémák és előfizetések a közzététel/előfizetés minta használatával „egy a többhöz” típusú kommunikációt biztosítanak. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet.

Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. A témakör, amely lehetővé teszi, hogy a szűrő/korlátozása melyik előfizetések által fogadott üzeneteket a témakörökbe melyik előfizetésenként alapon szűrési szabályokat lehet regisztrálni.

Service Bus-üzenettémák és előfizetések lehetővé teszi nagy mennyiségű üzenetet feldolgozni a felhasználók és alkalmazások nagy számú skálázhatja.

## <a name="create-a-service-namespace"></a>Szolgáltatásnévtér létrehozása
A kezdéshez a Service Bus-üzenettémák és előfizetések használata az Azure-ban, először létre kell hoznia egy *névtér*, amely egy hatókörkezelési tárolót biztosít a Service Bus erőforrásainak címzéséhez az alkalmazáson belül.

Névtér létrehozása:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>A Service Bus-alkalmazás konfigurálása
Győződjön meg arról, hogy telepítette a [Javához készült Azure SDK] [ Azure SDK for Java] Ez a minta létrehozása előtt. Ha az Eclipse használja, telepítheti a [Azure Toolkit for Eclipse] [ Azure Toolkit for Eclipse] , amely tartalmazza a Javához készült Azure SDK. Ezután hozzáadhatja a **Javához készült Azure-könyvtárak Microsoft** a projekthez:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Adja hozzá a következő `import` utasításokat a Java-fájl elejéhez:

```java
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

A Javához készült Azure-könyvtárak hozzáadása a fordítási utat, és adja hozzá a projekt üzembe helyezési sestavení.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
Felügyeleti műveletek a Service Bus-üzenettémakörök végezhető a **ServiceBusContract** osztály. A **ServiceBusContract** objektum, amely magában foglalja a SAS-jogkivonat engedélyek kezeléséhez, a megfelelő konfiguráció úgy van felépítve, és a **ServiceBusContract** osztály egyetlen pont kommunikáció az Azure-ral.

A **ServiceBusService** osztály létrehozásához, enumerálásához és törléséhez témakörök metódusokat biztosít. A következő példa bemutatja hogyan egy **ServiceBusService** objektum nevű témakör létrehozásához használható `TestTopic`, nevű névtér `HowToSample`:

```java
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
      "HowToSample",
      "RootManageSharedAccessKey",
      "SAS_key_value",
      ".servicebus.windows.net"
      );

ServiceBusContract service = ServiceBusService.create(config);
TopicInfo topicInfo = new TopicInfo("TestTopic");
try  
{
    CreateTopicResult result = service.createTopic(topicInfo);
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

A módszer van **TopicInfo** tulajdonságai, a következő témakörben kell beállítani, amelyek lehetővé teszik (például: alapértelmezett time-to-live (Élettartam TTL) értéket a alkalmazni lehessen a témakörbe küldött üzenetek). Az alábbi példa bemutatja, hogyan hozhat létre a témakörbe `TestTopic` 5 GB maximális mérettel:

```java
long maxSizeInMegabytes = 5120;  
TopicInfo topicInfo = new TopicInfo("TestTopic");  
topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateTopicResult result = service.createTopic(topicInfo);
```

Használhatja a **listTopics** metódust **ServiceBusContract** objektumok ellenőrizheti, ha a témakör a megadott néven már létezik egy szolgáltatásnévtérben.

## <a name="create-subscriptions"></a>Előfizetések létrehozása
Témakörök, előfizetések is jönnek létre az a **ServiceBusService** osztály. Az előfizetések el vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek korlátozzák az előfizetés virtuális üzenetsorának átadott üzenetek készletét.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha nincs meghatározva szűrő egy új előfizetés létrehozásakor, akkor a **MatchAll** szűrő az alapértelmezett használandó szűrő. Ha a **MatchAll** szűrőt használ, a témakörbe közzétett összes üzenetet az előfizetés virtuális üzenetsorának vannak elhelyezve. A következő példa egy „AllMessages” nevű előfizetést hoz létre, és az alapértelmezett **MatchAll** szűrőt használja.

```java
SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
CreateSubscriptionResult result =
    service.createSubscription("TestTopic", subInfo);
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Lehetővé teszi, hogy a hatókör, amely egy témakörbe küldött üzenetek egy adott témakör-előfizetésben kell megjelenítése szűrőket is létrehozhat.

A legrugalmasabb típusú szűrő előfizetések által támogatott a [SqlFilter][SqlFilter], amely megvalósítja az SQL92 egy részhalmazát. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további részletekért tekintse át a [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] szintaxist.

Az alábbi példa egy nevű előfizetést hoz létre `HighMessages` együtt egy [SqlFilter] [ SqlFilter] objektum, amely csak azokat a választja ki, amelyek egyéni üzenetek **MessageNumber** nagyobb, mint 3 tulajdonság:

```java
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

Hasonlóképpen, az alábbi példa egy nevű előfizetést hoz létre `LowMessages` együtt egy [SqlFilter] [ SqlFilter] objektum, amely csak azokat a rendelkező üzeneteket választja ki egy **MessageNumber** tulajdonság kisebb vagy egyenlő, mint 3:

```java
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Amikor egy üzenet most már a érkezik `TestTopic`, azt rendszer mindig kézbesíti az üzenetet az előfizetett a `AllMessages` -előfizetéssel, és szelektív módon kézbesíti a `HighMessages` és `LowMessages` előfizetések (attól függően, hogy a üzenet tartalma).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A Service Bus-témakörbe való üzenetküldéshez, az alkalmazás lekéri a **ServiceBusContract** objektum. A következő kód bemutatja, hogyan lehet elküldeni egy üzenetet a `TestTopic` a témakörben korábban létrehozott belül a `HowToSample` névteret:

```java
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Service Bus-témakörökbe küldött üzenetek olyan példányai a [BrokeredMessage] [ BrokeredMessage] osztály. [BrokeredMessage][BrokeredMessage]* objektumok rendelkeznek szabványos módszer (például **setLabel** és **TimeToLive**), egy egyéni tárolására használt szótár alkalmazásspecifikus tulajdonságokat, és a egy tetszőleges alkalmazásadatokból álló törzzsel törzse. Az alkalmazás beállíthatja az üzenet törzse bármilyen szerializálható objektumnak konstruktorának való átadásával a [BrokeredMessage][BrokeredMessage], és a megfelelő **DataContractSerializer** majd használja az objektumot. Másik lehetőségként egy **java.io.InputStream** adható meg.

Az alábbi példa bemutatja, hogyan küldhető öt tesztüzenet az a `TestTopic` **MessageSender** azt az előző kódrészletben kapott.
Megjegyzés: a **MessageNumber** egyes üzenetek tulajdonság értéke a ciklus ismétléseinek a változik (Ez az érték azt határozza meg, melyik előfizetések megkapni):

```java
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. Nem található a témakörökben tárolt üzenetek száma korlátozott, de, a témakörök által tárolt üzenetek teljes mérete korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Üzenetek fogadása egy előfizetésből, használja a **ServiceBusContract** objektum. A fogadott üzenetek használható két különböző módban: **ReceiveAndDelete** és **PeekLock** (alapértelmezett).

Használatakor a **ReceiveAndDelete** mód, kap egy egylépéses művelet –, amelyek a Service Bus egy olvasási kérést üzenetet kap, amikor azt jelöli meg az üzenetet, és visszaadja az alkalmazásnak. **ReceiveAndDelete** mód a legegyszerűbb modell, és leginkább forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet, ha hiba történik. Például vegyünk egy forgatókönyvet, amelyben a fogyasztó a fogadási kérést, és majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, van megjelölve, majd az alkalmazás újraindításakor és megkezdésekor üzeneteket, kimaradt az összeomlás előtt feldolgozott üzenetet.

A **PeekLock** mód, kap egy két szakaszból álló művelet lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. A fogadási folyamat második fázisa meghívásával befejezése után az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), **törlése** a fogadott üzenethez. Amikor a Service Bus látja a **törlése** hívást, feldolgozottként jelöli meg az üzenetet, és eltávolítja azt a témakört.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával **PeekLock** (az alapértelmezett mód). A példa hajt végre egy hurkot, és feldolgozza az üzeneteket a `HighMessages` előfizetést, majd kilép, ha nincsenek további üzenetek (másik lehetőségként beállítható az új üzenetek várakozási).

```java
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
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
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból az üzenet feldolgozása nem sikerült, akkor meghívhatja az **unlockMessage** metódust a fogadott üzenethez (helyett a **deleteMessage** metódus). Ennek hatására a Service Bus feloldja az üzenet a témakörön belül zárolását, és tegye elérhetővé számára az azonos fogyasztó alkalmazás általi vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van egy zárolva van a témakörön belül üzenethez társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időkorlát lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és lehetővé teszi elérhető az újbóli fogadását.

Abban az esetben, ha az alkalmazás összeomlik, mielőtt azonban az üzenet feldolgozása után a **deleteMessage** kérés kiadása, akkor az üzenet újbóli kézbesítése az alkalmazáshoz, amikor újraindul. Ez a folyamat gyakran nevezik **legalább egyszeri feldolgozásnak**; azaz minden üzenetet legalább egyszer dolgozza fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el használatával a **getMessageId** metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Az elsődleges legegyszerűbb módja, üzenettémák és előfizetések törlése egy **ServiceBusContract** objektum. Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők.

```java
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, [Service Bus-üzenetsorok, témakörök és előfizetések] [ Service Bus queues, topics, and subscriptions] további információt.

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
