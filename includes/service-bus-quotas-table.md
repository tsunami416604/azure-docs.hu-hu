---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b0c6d80bfa7a7ae27e5dc6565b0d15b94eb7cdd3
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310599"
---
A következő táblázat a Azure Service Bus üzenetkezelésre vonatkozó kvóta-információkat sorolja fel. További információ a Service Bus díjszabásáról és egyéb kvótáról: [Service Bus díjszabása](https://azure.microsoft.com/pricing/details/service-bus/).

| Kvóta neve | Scope | Megjegyzések | Value |
| --- | --- | --- | --- |
| Egy Azure-előfizetéshez tartozó alapszintű vagy standard névterek maximális száma |Névtér |A Azure Portal a további alapszintű vagy standard szintű névterek további kérelmeit a rendszer elutasítja. |100|
| Prémium szintű névterek maximális száma az Azure-előfizetések esetében |Névtér |A portál a további prémium szintű névterek további kérelmeit is elutasítja. |100 |
| Üzenetsor vagy témakör mérete |Entitás |A várólista vagy a témakör létrehozásakor van meghatározva. <br/><br/> A rendszer elutasítja a következő bejövő üzeneteket, és kivételt kap a hívó kód. |1, 2, 3, 4 GB vagy 5 GB.<br /><br />A prémium SKU-ban és a [particionálást](/azure/service-bus-messaging/service-bus-partitioning) engedélyező standard SKU esetében a maximális várólista vagy a témakör mérete 80 GB. |
| Névtér egyidejű kapcsolatainak száma |Névtér |A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívási kód kivételt kap. A REST-műveletek nem számítanak bele az egyidejű TCP-kapcsolatok irányába. |NetMessaging: 1 000.<br /><br />AMQP 5 000. |
| Egyidejű fogadási kérelmek száma egy üzenetsor, témakör vagy előfizetési entitás esetében |Entitás |A rendszer elutasítja a további fogadási kérelmeket, és a hívási kód kivételt kap. Ez a kvóta az egyidejű fogadási műveletek együttes számára vonatkozik a témakör összes előfizetésében. |5,000 |
| Témakörök vagy várólisták száma a névtérben |Névtér |Az új témakör vagy várólista a névtéren való létrehozására vonatkozó további kérelmeket a rendszer elutasítja. Ennek eredményeképpen, ha a [Azure Portalon][Azure portal]keresztül van konfigurálva, hibaüzenetet generál. Ha a felügyeleti API hívja meg, a hívási kód kivételt kap. |10 000 az alapszintű vagy a standard szinthez. A névtérben lévő témakörök és várólisták teljes száma nem lehet kisebb, mint 10 000. <br/><br/>A prémium szintű, 1 000/Messaging egységenként (MU). A maximális korlát 4 000. |
| [Particionált témakörök vagy várólisták](/azure/service-bus-messaging/service-bus-partitioning) száma névtérben |Névtér |A rendszer elutasítja az új particionált témakör vagy várólista a névtéren való létrehozására vonatkozó további kérelmeket. Ennek eredményeképpen, ha a [Azure Portalon][Azure portal]keresztül van konfigurálva, hibaüzenetet generál. Ha a felügyeleti API-val hívja meg a hívást, a **quotaexceededexception osztályról** kivételt kap. |Alapszintű és standard csomag: 100.<br/><br/>A [prémium](../articles/service-bus-messaging/service-bus-premium-messaging.md) szint nem támogatja a particionált entitásokat.<br/><br />Mindegyik particionált üzenetsor vagy témakör a 1 000 entitások kvótáját adja meg névtérként. |
| Az üzenetküldési entitás elérési útjának maximális mérete: üzenetsor vagy témakör |Entitás |- |260 karakter. |
| Az üzenetküldési entitások nevének maximális mérete: névtér, előfizetés vagy előfizetési szabály |Entitás |- |50 karakter. |
| [Üzenet azonosítójának](/dotnet/api/microsoft.azure.servicebus.message.messageid) maximális mérete | Entitás |- | 128 |
| Egy üzenet-munkamenet- [azonosító](/dotnet/api/microsoft.azure.servicebus.message.sessionid) maximális mérete | Entitás |- | 128 |
| Üzenetsor, témakör vagy előfizetési entitás üzeneteinek mérete |Entitás |Az ezeket a kvótákat meghaladó bejövő üzenetek elutasításra kerülnek, és a hívási kód kivételt kap. |Üzenetek maximális mérete: 256 KB [standard szintű](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB a [prémium szintű](../articles/service-bus-messaging/service-bus-premium-messaging.md)csomaghoz. <br /><br />A rendszer terhelése miatt ez a korlát kevesebb, mint az érték.<br /><br />Fejléc maximális mérete: 64 KB.<br /><br />Fejléc tulajdonságainak maximális száma a (z) Bag: **byte/int tulajdonságban. MaxValue**.<br /><br />Tulajdonság maximális mérete a (z) csomagban: Nincs explicit korlát. A fejlécek maximális mérete korlátozza. |
| Üzenetsor, témakör vagy előfizetési entitás üzenet-tulajdonságának mérete |Entitás | A rendszer létrehozza a kivétel **SerializationException** . |Az üzenetsor maximális mérete az egyes tulajdonságokhoz 32 000. Az összes tulajdonság összesített mérete nem haladhatja meg a 64 000-ot. Ez a korlát a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)teljes fejlécére vonatkozik, amely a felhasználói tulajdonságokat és a rendszer tulajdonságait, például a [sorszám](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), a [label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)és a [MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Előfizetések száma témakör szerint |Entitás |A további előfizetések létrehozására vonatkozó további kérelmeket a rendszer elutasítja. Ennek eredményeképpen, ha a portálon keresztül van konfigurálva, hibaüzenet jelenik meg. Ha a felügyeleti API hívja meg, a hívási kód kivételt kap. |2 000/téma az alapszintű vagy a standard szinthez. |
| SQL-szűrők száma egy témakörben |Entitás |A rendszer elutasítja a további szűrők létrehozására vonatkozó további kérelmeket a témakörben, és kivételt kapott a hívó kód. |2,000 |
| Korrelációs szűrők száma egy témakörben |Entitás |A rendszer elutasítja a további szűrők létrehozására vonatkozó további kérelmeket a témakörben, és kivételt kapott a hívó kód. |100,000 |
| SQL-szűrők vagy-műveletek mérete |Névtér |A rendszer elutasítja a további szűrők létrehozására vonatkozó további kérelmeket, és a hívási kód kivételt kap. |Szűrési feltétel karakterláncának maximális hossza: 1 024 (1 K).<br /><br />Szabály műveleti karakterláncának maximális hossza: 1 024 (1 K).<br /><br />Kifejezések maximális száma szabály szerint művelet: 32. |
| [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -szabályok száma névtér, üzenetsor vagy témakör szerint |Entitás, névtér |A további szabályok létrehozására vonatkozó további kérelmeket a rendszer elutasítja, és a hívó kód kivételt kap. |Szabályok maximális száma: 12. <br /><br /> A Service Bus névterekben konfigurált szabályok az adott névtérben lévő összes várólistára és témakörre érvényesek. |
| Üzenetek száma tranzakció szerint | Tranzakció | A rendszer elutasítja a további bejövő üzeneteket, és kivételt jelent a következő: "nem lehet több mint 100 üzenetet küldeni egyetlen tranzakcióban" a hívó kód fogadja. | 100 <br /><br /> Mind a **Send ()** , mind a **SendAsync ()** művelethez. |

[Azure portal]: https://portal.azure.com
