---
title: Azure Service Bus-üzenettémakörök használata Java |} Microsoft Docs
description: Használja a Service Bus-üzenettémák és előfizetések az Azure-ban.
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
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30927669"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-java"></a>Service Bus-üzenettémák és előfizetések használata Java

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez az útmutató ismerteti a Service Bus-üzenettémák és előfizetések használata. A mintákat a Java és -felhasználási nyelven íródtak a [Javához készült Azure SDK][Azure SDK for Java]. Az ismertetett forgatókönyvek **üzenettémák és előfizetések létrehozása**, **előfizetés-szűrők létrehozása**, **üzeneteket küld egy témakör**, **üzenetek fogadása egy előfizetésből**, és **üzenettémák és előfizetések törlése**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

A Service Bus-üzenetsorokkal ellentétben, amely esetében minden üzenetet egy-egy fogyasztó dolgoz fel, az üzenettémák és előfizetések a közzététel/előfizetés minta használatával „egy a többhöz” típusú kommunikációt biztosítanak. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet.

Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Akkor is szűrőszabályok előfizetésenként történő beállítására, amely lehetővé teszi a szűrő/korlátozása mely üzeneteket a témakörökbe fogadásának melyik előfizetések kaphatják meg témakör.

Service Bus-üzenettémák és előfizetések lehetővé teszik méretezhető, nagy számú üzenetet feldolgozni nagyszámú felhasználók és az alkalmazások között.

## <a name="create-a-service-namespace"></a>Szolgáltatásnévtér létrehozása
A Service Bus-üzenettémák és előfizetések az Azure-ban használatának megkezdéséhez létre kell hoznia egy *névtér*, amely egy hatókörkezelési tárolót biztosít az alkalmazáson belül a Service Bus erőforrásainak kezeléséhez.

Névtér létrehozása:

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Állítsa be az alkalmazását, Service Bus használatára
Győződjön meg arról, hogy telepítette a [Javához készült Azure SDK] [ Azure SDK for Java] Ez a minta létrehozása előtt. Ha az Eclipse használja, telepítheti a [Eclipse Azure eszköztára] [ Azure Toolkit for Eclipse] , amely tartalmazza az Azure SDK for Java. Ezután a **Microsoft Azure-könyvtárakban Java** a projekthez:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Adja hozzá a következő `import` utasítást, hogy a Java-fájl elejéhez:

```java
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Adja hozzá az Azure-könyvtárakban Java build elérési úthoz, és adja hozzá a projekt központi telepítési szerelvényben.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
Felügyeleti műveletek a Service Bus-témakörök használatával is kell végezni a **ServiceBusContract** osztály. A **ServiceBusContract** objektum van kialakítani, amely magában foglalja a SAS-jogkivonat való kezelése, a megfelelő konfiguráció és a **ServiceBusContract** osztálya: Azure kommunikáció egyetlen pont.

A **ServiceBusService** osztály létrehozásához, enumerálásához és törléséhez témakörök metódusokat biztosít. Az alábbi példában látható hogyan egy **ServiceBusService** objektum segítségével létrehoz egy témát nevű `TestTopic`, úgynevezett névtérrel `HowToSample`:

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

Módszer áll rendelkezésre a **TopicInfo** , engedélyezni kell beállítani a témakör tulajdonságainak (például: alapértelmezett--élettartama (TTL) értékének beállítására a témakörbe küldött üzenetek alkalmazandó). A következő példa bemutatja, hogyan hozzon létre egy elnevezett téma `TestTopic` 5 GB maximális mérettel:

```java
long maxSizeInMegabytes = 5120;  
TopicInfo topicInfo = new TopicInfo("TestTopic");  
topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateTopicResult result = service.createTopic(topicInfo);
```

Használhatja a **listTopics** metódusa **ServiceBusContract** objektumok kereséséhez, ha a témakör a megadott névvel már létezik egy szolgáltatásnévtérben.

## <a name="create-subscriptions"></a>Előfizetések létrehozása
Előfizetések témakörök is jönnek létre a **ServiceBusService** osztály. Az előfizetések el vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek korlátozzák az előfizetés virtuális üzenetsorának átadott üzenetek készletét.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha nincs meghatározva szűrő egy új előfizetés létrehozásakor, akkor a **MatchAll** szűrő az alapértelmezett használandó szűrő. Ha a **MatchAll** szűrővel, a témakörbe közzétett összes üzenetet kerülnek, az előfizetés virtuális üzenetsorában. A következő példa egy „AllMessages” nevű előfizetést hoz létre, és az alapértelmezett **MatchAll** szűrőt használja.

```java
SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
CreateSubscriptionResult result =
    service.createSubscription("TestTopic", subInfo);
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Lehetővé teszi, hogy a hatókör, amely egy témakörbe küldött üzenetek belül egy adott üzenettémakör-előfizetésben kell megjelenítése szűrők is létrehozhat.

A szűrő előfizetések által támogatott legrugalmasabb típusú a [SqlFilter][SqlFilter], amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkért tekintse át a [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] szintaxist.

A következő példa egy nevű előfizetést hoz létre `HighMessages` rendelkező egy [SqlFilter] [ SqlFilter] objektum, amely csak kiválasztja az üzeneteket, amelyek egyéni **MessageNumber** tulajdonságának értéke nagyobb, mint 3:

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

Hasonlóképpen, a következő példa egy nevű előfizetést hoz létre `LowMessages` rendelkező egy [SqlFilter] [ SqlFilter] objektum, amely csak kiválasztja az üzeneteket, amelyek rendelkeznek egy **MessageNumber** tulajdonságának értéke kisebb vagy egyenlő, mint 3:

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

Ha egy most üzenettel `TestTopic`, mindig biztosítását előfizetett fogadó számára a `AllMessages` előfizetés, és szelektív módon kézbesíti a a `HighMessages` és `LowMessages` előfizetések (attól függően, a üzenet tartalma).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
A Service Bus-témakörbe való üzenetküldéshez, az alkalmazás beolvassa a **ServiceBusContract** objektum. A következő kód bemutatja, hogyan küldhető a `TestTopic` korábban létrehozott belül a témakör a `HowToSample` névtér:

```java
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Service Bus-témakörökbe küldött üzenetek példánya a [BrokeredMessage] [ BrokeredMessage] osztály. [BrokeredMessage][BrokeredMessage]* objektumok rendelkeznek egy szabványos módszerek (mint például **setLabel** és **TimeToLive**), egyéni tárolására használt dictionary az alkalmazás-specifikus tulajdonságait, és egy tetszőleges alkalmazásadatokból álló törzzsel. Az alkalmazás beállíthatja az üzenet törzsét bármilyen szerializálható objektumnak konstruktorának való átadásával a [BrokeredMessage][BrokeredMessage], és a megfelelő **DataContractSerializer** szolgál majd szerializálja az objektumot. Másik megoldásként egy **java.io.InputStream** tartalmazhat.

A következő példa bemutatja, hogyan küldhető öt tesztüzenet az a `TestTopic` **MessageSender** azt a korábbi kódrészletben kapott.
Megjegyzés: az **MessageNumber** minden üzenetet tulajdonság értékének meg az a ciklus ismétléseinek számától függően változik (Ez az érték azt határozza meg, melyik előfizetések fogják megkapni):

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

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma nincs korlátozva, de korlátozás van, a témakörök által tárolt üzenetek teljes mérete. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Üzenetek fogadása egy előfizetést, használja a **ServiceBusContract** objektum. A fogadott üzenetek a két különböző módban tudnak működni: **ReceiveAndDelete** és **PeekLock** (alapértelmezett).

Használatakor a **ReceiveAndDelete** mód, kap egy egylépéses művelet – Ez azt jelenti, hogy a Service Bus egy olvasási kérést üzenetet kap, ha azt az üzenetet, feldolgozottként jelöli meg, és visszaadja az alkalmazásnak. **ReceiveAndDelete** mód a legegyszerűbb modell, és leginkább megfelelő forgatókönyvek, amelyben az alkalmazás működését nem dolgoz fel üzenetet, ha hiba történik. Példaként vegyünk egy forgatókönyvet, amelyben a fogyasztó a fogadási kérést, és majd összeomlik a feldolgozása előtt. Mivel a Service Bus az üzenetet, feldolgozottként jelölte meg, majd az alkalmazás újraindításakor és megkezdése az üzenetek fel újra, amikor kimaradt a az összeomlás előtt feldolgozott üzenetet.

A **PeekLock** mód, kap egy két szakaszból álló művelet, amely lehetővé teszi az alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek lesz. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a második a fogadási folyamat meghívásával **törlése** metódus a fogadott üzenethez. Amikor a Service Bus látja a **törlése** hívás, azt az üzenetet, feldolgozottként jelöli meg, és eltávolítja azt a témakörben.

A következő példa bemutatja, hogyan lehet üzeneteket fogadni, és a feldolgozott használatával **PeekLock** (az alapértelmezett mód). Példa hurkot hajt végre, és feldolgozza az üzenetet a `HighMessages` előfizetés, majd kilép, ha nincsenek további üzenetek (másik lehetőségként beállítható új üzenetek várakozási).

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
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás nem tudja feldolgozni az üzenetet valamilyen okból kifolyólag, majd akkor meghívhatja a **unlockMessage** metódust a fogadott üzenethez (ahelyett, hogy a **deleteMessage** metódus). Ennek hatására a Service Bus feloldja a témakör az üzenet zárolását, és lehetővé teszi a felhasználó az alkalmazás vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett van a következő témakörben lévő társított időtúllépés, és ha az alkalmazás nem tudja feldolgozni az üzenetet, mielőtt a zárolás időtúllépését lejárta (például, ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet, és lehetővé teszi az elérhető ismételt fogadását.

Abban az esetben, ha az alkalmazás összeomlik, de előtte az üzenet feldolgozása után a **deleteMessage** kérés kiadása, akkor az üzenet újból kézbesítve az alkalmazásnak, amikor újraindul. Ez a folyamat gyakran nevezik **legalább egyszeri feldolgozásnak**, ez azt jelenti, hogy minden üzenet legalább egyszer fel, de bizonyos helyzetekben előfordulhat ugyanazon üzenet előfordulhat, hogy újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran érhető el, használja a **getMessageId** metódus az üzenet, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
Az elsődleges témakörök és előfizetések törlése módja használja egy **ServiceBusContract** objektum. Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők.

```java
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a Service Bus-üzenetsorok alapjait, lásd: [Service Bus-üzenetsorok, témakörök és előfizetések] [ Service Bus queues, topics, and subscriptions] további információt.

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Azure Toolkit for Eclipse]: ../azure-toolkit-for-eclipse.md
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage

[0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
[2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
[3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
