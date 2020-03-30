---
title: Azure Service Bus üzenetküldés – várólisták, témakörök és előfizetések
description: Ez a cikk áttekintést nyújt az Azure Service Bus üzenetkezelési entitások (várólista, témakörök és előfizetések).
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 3dc78a22e0e596d812d90fec63475a0b21e9164f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259512"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)

A Microsoft Azure Service Bus felhőalapú, üzenetorientált köztes szoftvertechnológiákat támogat, beleértve a megbízható üzenetsor-kezelésés a tartós közzétételi/előfizetéses üzenetküldést. Ezek a "felügyelt" üzenetkezelési képességek a Service Bus üzenetkezelési számítási feladatok használatával a közzététel-előfizetést, az időbeli leválasztást és a terheléselosztási forgatókönyveket támogató leválasztott üzenetkezelési funkciókként is felfoghatók. A leválasztott kommunikációnak számos előnye van: az ügyfelek és a kiszolgálók például szükség szerint kapcsolódhatnak, és aszinkron módon hajthatják végre a műveleteiket.

A Service Bus üzenetkezelési képességeinek magját alkotó üzenetküldési entitások várólisták, témakörök és előfizetések, valamint szabályok/műveletek.

## <a name="queues"></a>Üzenetsorok

A várólisták *első be, első ki* (FIFO) üzenetkézbesítést kínálnak egy vagy több versenytárs fogyasztónak. Ez azt jelenti, hogy a fogadók általában abban a sorrendben fogadják és dolgozzák fel az üzeneteket, ahogyan a várólistába kerültek, és csak egy üzenet fogadja és dolgozza fel az egyes üzeneteket. A várólisták használatának egyik fő előnye az alkalmazás-összetevők "időbeli függetlenítésének" elérése. Más szóval a gyártóknak (feladóknak) és a fogyasztóknak (fogadóknak) nem kell egyszerre üzeneteket küldeniük és fogadniuk, mivel az üzenetek tárolása tartósan a várólistában van. Ezenkívül a gyártónak nem kell megvárnia a fogyasztó válaszát ahhoz, hogy folytassa az üzenetek feldolgozását és küldését.

A kapcsolódó előny a "terheléskiegyenlítés", amely lehetővé teszi a gyártók és a fogyasztók számára, hogy különböző ütemben küldjenek és fogadjanak üzeneteket. Számos alkalmazásban a rendszer terhelése idővel változik; azonban az egyes munkaegységekhez szükséges feldolgozási idő általában állandó. Az üzenetgyártók és a várólistával rendelkező fogyasztók közvetítése azt jelenti, hogy a fogyasztó alkalmazást csak ki kell építeni ahhoz, hogy a csúcsterhelés helyett az átlagos terhelést kezelni lehessen. A bejövő terhelés változásával az üzenetsor hossza nő vagy csökken. Ez a funkció közvetlenül pénzt takarít meg az alkalmazásterhelés kiszolgálásához szükséges infrastruktúra mennyisége tekintetében. A terhelés növekedésével további munkavégző folyamatok adhatók hozzá a várólistából való olvasáshoz. Az egyes üzeneteket a feldolgozó folyamatoknak csak az egyike dolgozza fel. Továbbá ez a lekéréses alapú terheléselosztás lehetővé teszi a dolgozó számítógépek optimális használatát, még akkor is, ha a feldolgozó számítógépek különböznek a feldolgozási teljesítmény tekintetében, mivel az üzeneteket a saját maximális sebességükön kérik le. Ezt a mintát gyakran nevezik a "versengő fogyasztó" minta.

Az üzenetgyártók és a fogyasztók közötti közbenső sorok használata magában esve laza kapcsolatot biztosít az alkatrészek között. Mivel a termelők és a fogyasztók nincsenek tisztában egymással, a fogyasztó továbbfejlesztett éhen lehet lépni anélkül, hogy ez bármilyen hatással lenne a gyártóra.

### <a name="create-queues"></a>Várólisták létrehozása

Várólistákat az [Azure Portal,](service-bus-quickstart-portal.md)a [PowerShell,](service-bus-quickstart-powershell.md) [a CLI](service-bus-quickstart-cli.md)vagy az [Erőforrás-kezelő sablonjai](service-bus-resource-manager-namespace-queue.md)segítségével hozhat létre. Ezután üzeneteket küldhet és fogadhat egy [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektum mal.

Ha gyorsan meg szeretné tudni, hogyan hozhat létre várólistát, majd hogyan küldhet és fogadhat üzeneteket a várólistába és onnan, tekintse meg az egyes módszerek [rövid útmutatóit.](service-bus-quickstart-portal.md) A várólisták használatáról a [Szolgáltatásbusz-várólisták használatának megkezdéséről](service-bus-dotnet-get-started-with-queues.md)szóló részletes oktatóanyagról az Első lépések a Service Bus-várólisták használatával kapcsolatban ( Első lépések a Szolgáltatásbusz-várólisták használatával ) kapcsolatban.

A működő minta: [BasicSendReceiveUsingQueueClient minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) a GitHubon.

### <a name="receive-modes"></a>Fogadási módok

Két különböző módot adhat meg, amelyben a Service Bus üzeneteket fogad: *ReceiveAndDelete* vagy *PeekLock*. A [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) módban a fogadási művelet egylövéses; azaz amikor a Service Bus megkapja a fogyasztótól a kérést, az üzenetet felhasználásként jelöli meg, és visszaküldi azt a fogyasztói alkalmazásnak. **A ReceiveAndDelete** mód a legegyszerűbb modell, és olyan esetekben működik a legjobban, amikor az alkalmazás hiba esetén nem képes feldolgozni az üzenetet. Ennek a forgatókönyvnek a megértéséhez fontolja meg egy olyan forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Mivel a Service Bus az üzenetet felhasználásként jelöli meg, amikor az alkalmazás újraindul, és újra elkezdi az üzenetek felhasználását, az összeomlás előtt felhasznált üzenet hiányozni fog.

[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) módban a fogadási művelet kétlépcsőssé válik, ami lehetővé teszi olyan alkalmazások támogatását, amelyek nem tolerálják a hiányzó üzeneteket. Amikor a Service Bus megkapja a kérést, megkeresi a következő használni hozandó üzenetet, zárolja, hogy megakadályozza, hogy más fogyasztók megkapják, majd visszaküldi azt az alkalmazásnak. Miután az alkalmazás befejezte az üzenet feldolgozását (vagy megbízhatóan tárolja a jövőbeli feldolgozáshoz), befejezi a fogadási folyamat második szakaszát a [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) hívásával a fogadott üzeneten. Amikor a Service Bus látja a **CompleteAsync** hívást, az üzenetet felhasználtként jelöli meg.

Ha az alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, akkor a fogadott üzenet [(CompleteAsync)](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)helyett meghívhatabandona az [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metódust. Ez a módszer lehetővé teszi a Service Bus számára, hogy feloldja az üzenet zárolását, és elérhetővé tegye, hogy ugyanaz a fogyasztó vagy egy másik versenytárs fogyasztó újra megkapja. Másodszor, van egy időtúltöltés a zároláshoz társítva, és ha az alkalmazás nem dolgozza fel az üzenetet, mielőtt a zárolási időtúllépésre lejár (például ha az alkalmazás összeomlik), majd a Service Bus feloldja az üzenetet, és elérhetővé teszi, hogy újra megkapja (lényegében egy [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) művelet et hajt végre alapértelmezés szerint).

Abban az esetben, ha az alkalmazás összeomlik az üzenet feldolgozása után, de a **CompleteAsync** kérelem kiadása előtt, az üzenet újrakézbesítése az alkalmazáshoz, amikor újraindul. Ezt a folyamatot gyakran *nevezik legalább egyszer* feldolgozás; ez azt illeti, minden üzenet feldolgozása legalább egyszer. Bizonyos helyzetekben azonban ugyanaz az üzenet is kézbesíthető. Ha a forgatókönyv nem tűri a duplikált feldolgozást, akkor további logikára van szükség az alkalmazásban az ismétlődések észleléséhez, amely az üzenet [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonsága alapján érhető el, amely a kézbesítési kísérletek során állandó marad. Ezt a funkciót *pontosan egyszer* dolgozom fel.

## <a name="topics-and-subscriptions"></a>Témakörök és előfizetések

Ellentétben a várólistákkal, amelyekben minden üzenetet egyetlen fogyasztó dolgoz fel, *a témakörök* és *az előfizetések* egy-a-többhöz kommunikációs formát biztosítanak egy *közzétételi/előfizetési* mintában. Hasznos a nagy számú címzettre való méretezéshez, minden közzétett üzenet elérhetővé válik a témakörrel regisztrált minden egyes előfizetés számára. Az üzeneteket a rendszer egy témakörbe küldi, és egy vagy több társított előfizetésnek kézbesíti, az előfizetésenként beállítható szűrőszabályoktól függően. Az előfizetések további szűrőkkel korlátozhatják a fogadni kívánt üzeneteket. Az üzenetek ugyanúgy kerülnek a témakörbe, mint a várólistába, de közvetlenül a témakörből nem érkeznek üzenetek. Ehelyett az előfizetésekből érkeznek. A témakör-előfizetés hasonlít egy virtuális várólistára, amely a témakörbe küldött üzenetek másolatait fogadja. Az üzenetek ugyanúgy érkeznek az előfizetésből, mint a várólistából való beérkezésük.

Összehasonlításképpen, a várólista üzenetküldési funkciója közvetlenül egy témakörhöz és az üzenetfogadó funkcióegy előfizetéshez van rendelve. Ez a funkció többek között azt jelenti, hogy az előfizetések támogatják ugyanazokat a mintákat, amelyeket ebben a szakaszban a várólisták tekintetében ismertetett: versengő fogyasztó, időbeli leválasztás, terheléskiegyenlítés és terheléselosztás.

### <a name="create-topics-and-subscriptions"></a>Témakörök és előfizetések létrehozása

A témakör létrehozása hasonló a várólista létrehozásához, az előző szakaszban leírtak szerint. Ezután üzeneteket küldhet a [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) osztály használatával. Az üzenetek fogadásához hozzon létre egy vagy több előfizetést a témakörhöz. A várólistákhoz hasonlóan az üzenetek is [egy SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) objektumot használva, hanem egy [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) objektumhasználatával fogadnak üzeneteket. Hozza létre az előfizetési ügyfelet, adja meg a témakör nevét, az előfizetés nevét, és (opcionálisan) a fogadási módot paraméterekként.

A teljes működő példa: [BasicSendReceiveUsingASubscriptionClient minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) a GitHubon.

### <a name="rules-and-actions"></a>Szabályok és műveletek

Számos esetben a speciális jellemzőkkel rendelkező üzeneteket különböző módon kell feldolgozni. A feldolgozás engedélyezéséhez konfigurálhatja az előfizetéseket a kívánt tulajdonságokkal rendelkező üzenetek keresésére, majd bizonyos módosításokat hajthat végre ezeken a tulajdonságokon. Míg a Service Bus-előfizetések a témakörbe küldött összes üzenetet látják, ezeknek az üzeneteknek csak egy részhalmazát másolhatja a virtuális előfizetési várólistába. Ez a szűrés előfizetési szűrők használatával történik. Az ilyen módosításokat *szűrőműveleteknek*nevezzük. Előfizetés létrehozásakor megadhat egy szűrőkifejezést, amely az üzenet tulajdonságaialapján működik, mind a rendszertulajdonságokon (például **a Címke)** és az egyéni alkalmazástulajdonságokon (például **a StoreName**.) Ebben az esetben az SQL-szűrőkifejezés megadása nem kötelező; SQL-szűrőkifejezés nélkül az előfizetésen definiált szűrőműveletek az adott előfizetés összes üzenetén végrehajtva lesznek.

A teljes működő példa: [A TopicSubscriptionWithRuleOperationsSample minta](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) a GitHubon.

A lehetséges szűrőértékekről további információt az [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) és az [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) osztályok dokumentációjában talál.

## <a name="next-steps"></a>További lépések

A Service Bus üzenetküldési szolgáltatással kapcsolatos további tudnivalókat és példákat az alábbi témakörökben talál:

* [A Service Bus üzenetküldése – áttekintés](service-bus-messaging-overview.md)
* [Rövid útmutató: Üzenetek küldése és fogadása az Azure Portallal és a .NET-tel](service-bus-quickstart-portal.md)
* [Oktatóanyag: Leltár frissítése az Azure Portal és témakörök/előfizetések használatával](service-bus-tutorial-topics-subscriptions-portal.md)


