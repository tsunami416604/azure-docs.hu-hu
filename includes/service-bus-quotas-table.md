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
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651727"
---
Az alábbi táblázat az Azure Service Bus-üzenetküldésre vonatkozó kvótainformációkat sorolja fel. A Service Bus díjszabásáról és egyéb kvótáiról a [Service Bus díjszabása](https://azure.microsoft.com/pricing/details/service-bus/)című témakörben talál.

| Kvóta neve | Hatókör | Megjegyzések | Érték |
| --- | --- | --- | --- |
| Alapszintű vagy standard névterek maximális száma Azure-előfizetésenként |Névtér |További alapszintű vagy standard névterek további kérelmeket az Azure Portal elutasítja. |100|
| Prémium szintű névterek maximális száma Azure-előfizetésenként |Névtér |A portál elutasítja a további prémium szintű névterekre vonatkozó további kérelmeket. |100 |
| Várólista vagy témakör mérete |Entitás |A várólista vagy a témakör létrehozásakor definiálva. <br/><br/> A rendszer elutasítja a további bejövő üzeneteket, és a hívókód kivételt fogad. |1, 2, 3, 4 GB vagy 5 GB.<br /><br />A prémium szintű termékváltozatban és a szabványos termékváltozatban engedélyezve van a [particionálás,](/azure/service-bus-messaging/service-bus-partitioning) a maximális várólista vagy a témakör mérete 80 GB. |
| A névtéregyidejű kapcsolatainak száma |Névtér |A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívókód kivételt fogad. A REST-műveletek nem számítanak bele az egyidejű TCP-kapcsolatokba. |NetMessaging: 1000.<br /><br />AMQP: 5000. |
| Várólistára, témakörre vagy előfizetési entitásra vonatkozó egyidejű fogadási kérelmek száma |Entitás |A későbbi fogadási kérelmeket a rendszer elutasítja, és a hívókód kivételt fogad. Ez a kvóta a témakör összes előfizetésében lévő egyidejű fogadási műveletek összesített számára vonatkozik. |5000 |
| Témakörök vagy várólisták száma névtérenként |Névtér |A névtérben egy új témakör vagy várólista létrehozására irányuló további kérelmeket a rendszer elutasítja. Ennek eredményeképpen, ha az [Azure Portalon][Azure portal]keresztül konfigurálva van, hibaüzenet jön létre. Ha a felügyeleti API-ból hívja meg, a hívó kód kivételt kap. |10 000 az alapszintű vagy standard szintű. A névtérben lévő témakörök és várólisták teljes számának 10 000-nél kisebbnek vagy azzal egyenlőnek kell lennie. <br/><br/>A prémium szint esetén 1000 üzenetegységenként (MU). A maximális határ 4000. |
| [Particionált témakörök vagy várólisták](/azure/service-bus-messaging/service-bus-partitioning) száma névtérenként |Névtér |A rendszer elutasítja az új particionált témakör vagy várólista létrehozására irányuló további kérelmeket. Ennek eredményeképpen, ha az [Azure Portalon][Azure portal]keresztül konfigurálva van, hibaüzenet jön létre. Ha a felügyeleti API-ból hívja meg, a **kivételkvótaQuotededException** a hívó kód. |Alap- és standard szintek: 100.<br/><br/>Particionált entitások nem támogatottak a [prémium szintű.](../articles/service-bus-messaging/service-bus-premium-messaging.md)<br/><br />Minden particionált várólista vagy témakör névtérenként 1000 entitás kvótájába számít bele. |
| Bármely üzenetküldő entitás elérési útjának maximális mérete: várólista vagy témakör |Entitás |- |260 karakter. |
| Az üzenetküldő entitás nevének maximális mérete: névtér, előfizetés vagy előfizetési szabály |Entitás |- |50 karakter. |
| [Az üzenetazonosító](/dotnet/api/microsoft.azure.servicebus.message.messageid) maximális mérete | Entitás |- | 128 |
| Az [üzenet-munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid) maximális mérete | Entitás |- | 128 |
| Üzenetméret egy várólistához, témakörhöz vagy előfizetési entitáshoz |Entitás |A rendszer elutasítja az ezeket a kvótákat meghaladó bejövő üzeneteket, és a hívókód kivételt fogad. |Maximális üzenetméret: 256 KB [standard szint](../articles/service-bus-messaging/service-bus-premium-messaging.md)esetén , 1 MB a [prémium szinthez](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />A rendszer terhelése miatt ez a korlát kisebb, mint ezek az értékek.<br /><br />Maximális fejlécméret: 64 KB.<br /><br />A fejléctulajdonságainak maximális száma a **tulajdonságtáskában: bájt/int. MaxValue**.<br /><br />Az ingatlan maximális mérete az ingatlantáskában: Nincs kifejezett korlátozás. Maximális fejlécméret korlátozza. |
| Üzenettulajdonság-méret egy várólista, témakör vagy előfizetési entitás esetén |Entitás | A kivétel **Szerializációkivétel** jön létre. |Az egyes tulajdonok üzenettulajdonságának maximális mérete 32 000. Az összes tulajdonság összesített mérete nem haladhatja meg a 64 000-et. Ez a korlát a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)teljes fejlécére vonatkozik, amely felhasználói és rendszertulajdonságokkal is rendelkezik, például [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)és [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Előfizetések száma témánként |Entitás |A témakörhöz további előfizetések létrehozására vonatkozó további kérelmeket a rendszer elutasítja. Ennek eredményeképpen, ha a portálon keresztül konfigurálva van, hibaüzenet jelenik meg. Ha a felügyeleti API-ból hívja meg, a hívó kód kivételt kap. |Témakörenként 2000 a standard szinthez. |
| SQL-szűrők száma témakörenként |Entitás |A további szűrők létrehozására irányuló további kérelmeket a rendszer elutasítja, és a hívókód kivételt fogad. |2000 |
| Korrelációs szűrők száma témakörenként |Entitás |A további szűrők létrehozására irányuló további kérelmeket a rendszer elutasítja, és a hívókód kivételt fogad. |100 000 |
| SQL-szűrők vagy műveletek mérete |Névtér |A további szűrők létrehozására vonatkozó további kérelmeket a rendszer elutasítja, és a hívókód kivételt fogad. |A szűrőfeltétel-karakterlánc maximális hossza: 1024 (1 K).<br /><br />A szabályművelet karakterláncának maximális hossza: 1024 (1 K).<br /><br />A kifejezések maximális száma szabályműveletenként: 32. |
| A [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) szabályok száma névtérben, várólistánként vagy témakörben |Entitás, névtér |A további szabályok létrehozására irányuló további kérelmeket a rendszer elutasítja, és a hívókód kivételt kap. |A szabályok maximális száma entitástípusonként: 12. <br /><br /> A Service Bus névterén konfigurált szabályok minden típusra vonatkoznak: várólistákra, témakörökre. |
| Üzenetek száma tranzakciónként | Tranzakció | A rendszer további bejövő üzeneteket utasít el, és a hívókód kivételt fogad, amely szerint "Nem küldhet 100-nál több üzenetet egyetlen tranzakcióban". | 100 <br /><br /> A **Küldés()** és a **SendAsync()** műveletekhez is. |
| Virtuális hálózati és IP-szűrőszabályok száma | Névtér | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
