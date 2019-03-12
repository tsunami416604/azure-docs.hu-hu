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
ms.openlocfilehash: bb5dc4e01a872178af1bc7d5d57fc189424e7e9d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553663"
---
Az alábbi táblázat az Azure Service Bus-üzenetkezelés adott kvótákra vonatkozó információk. Díjszabással kapcsolatos információk és a többi Service Bus kvóták: [Service Bus díjszabásáról](https://azure.microsoft.com/pricing/details/service-bus/).

| Kvóta neve | Hatókör | Megjegyzések | Érték |
| --- | --- | --- | --- | --- |
| Azure-előfizetésenként alapszintű vagy standard szintű névterek maximális száma |Névtér |Az Azure portal által elutasított további alapszintű vagy standard szintű névterekhez későbbi kérelmeket. |100|
| Prémium szintű névterek Azure-előfizetésenként maximális száma |Névtér |A további prémium szintű névterek későbbi kérelmeket a rendszer elutasítja a portál által. |25 |
| Üzenetsor vagy témakör mérete |Entitás |Az üzenetsor vagy témakör létrehozásakor meghatározott. <br/><br/> A rendszer elutasítja a további bejövő üzenetek, és a hívó kód által fogadott kivétel. |1, 2, 3, 4 GB-os vagy 5 GB.<br /><br />A prémium szintű Termékváltozat és a Standard Termékváltozat- [particionálás](/azure/service-bus-messaging/service-bus-partitioning) engedélyezve van, a maximális üzenetsor vagy témakör mérete 80 GB. |
| Egy névtér az egyidejű kapcsolatok száma |Névtér |További kapcsolatok későbbi kérelmeket a rendszer elutasítja, és a egy kivételt a hívó kód által fogadott. REST-műveleteinek egyidejű TCP-kapcsolatok nem beleszámítanak. |NetMessaging: 1,000.<br /><br />AMQP: 5,000. |
| Párhuzamosan futó száma egy üzenetsor, témakör vagy előfizetés entitás kérelmek fogadásához |Entitás |Ezt követő kap kérelmek azért lettek elutasítva, és a egy kivételt a hívó kód által fogadott. Ez a kvóta vonatkozik, a kombinált számú párhuzamosan futó fogadási műveletek az összes előfizetés témakörökre. |5000 |
| Témakörök és -névterenként várólisták száma |Névtér |Új témakör vagy a névtér a várólista létrehozása a későbbi kérelmeket a rendszer elutasítja. Eredményként, ha keresztül lehet konfigurálni a [az Azure portal][Azure portal], akkor jön létre, egy hibaüzenet. A felügyeleti API-t hív, ha kivétel a hívó kód érkezik. |az alap vagy Standard szinten 1000. Üzenettémák és -névtérben található üzenetsorok száma legfeljebb 1000 kell lennie. <br/><br/>A prémium szint, 1000 / üzenetkezelési egység (MU). Maximális korlátját 4 000. |
| Hány [témakörök vagy az üzenetsorok particionálása](/azure/service-bus-messaging/service-bus-partitioning) névterenként |Névtér |Későbbi kérelmeket egy új particionált témakör vagy sor a névtér létrehozásakor a rendszer elutasítja. Eredményként, ha keresztül lehet konfigurálni a [az Azure portal][Azure portal], akkor jön létre, egy hibaüzenet. Ha a felügyeleti API-t, a kivétel nevű **QuotaExceededException** a hívó kód érkezik. |Alapszintű és Standard szint: 100.<br/><br/>Particionált entitások nem támogatottak a [prémium](../articles/service-bus-messaging/service-bus-premium-messaging.md) szint.<br/><br />Minden egyes particionált üzenetsorra vagy témakörbe megszámolja a kvóta névterenként 1000 entitások felé. |
| Minden üzenetküldési entitás elérési útja maximális mérete: üzenetsor vagy témakör |Entitás |- |260 karakter hosszúságú lehet. |
| Minden üzenetküldési entitás neve maximális mérete: névteret, az előfizetés vagy az előfizetés szabály |Entitás |- |50 karakter hosszú lehet. |
| Maximális mérete egy [üzenet azonosítója](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entitás |- | 128 |
| Egy üzenet maximális mérete [munkamenet-azonosító](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entitás |- | 128 |
| Egy üzenetsor, témakör vagy előfizetés egység mérete |Entitás |Ezek a kvóták túllépéséből bejövő üzenetek a rendszer elutasítja, és a hívó kód által fogadott kivétel. |Maximális mérete: 256 KB- [Standard szintű](../articles/service-bus-messaging/service-bus-premium-messaging.md), az 1 MB [prémium szintű](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Rendszer terhelés miatt a Ez a korlátozás nem éri el ezeket az értékeket.<br /><br />A fejléc maximális mérete: 64 KB-OS.<br /><br />Fejléc tulajdonságai a tulajdonságcsomag maximális számát: **bájt /: egész szám. MaxValue**.<br /><br />A tulajdonságcsomag tulajdonság maximális mérete: Nincs explicit korlát. Attól függ, a fejléc maximális méretét. |
| Egy üzenetsor, témakör vagy előfizetés entitás tulajdonság mérete |Entitás | A kivétel **SerializationException** jön létre. |Minden egyes tulajdonság tulajdonság maximális mérete 32 000. Az összes tulajdonság összesített mérete nem lehet hosszabb 64 000. Ez a korlátozás vonatkozik teljes fejlécében a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), amelynek felhasználói tulajdonságok és a rendszer tulajdonságai, például [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [címke](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), és [ Üzenetazonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Témánként előfizetéseinek száma. |Entitás |A témakör a további előfizetések létrehozására szolgáló későbbi kérelmeket a rendszer elutasítja. Ennek eredményeképpen ha a portálon keresztül konfigurálható, egy hibaüzenet jelenik meg. A felügyeleti API-t hív, ha kivétel a hívó kód érkezik. |Standard szint: Az egyes előfizetésekhez, beleszámít 1000 entitásokat, a beállított kvótát, üzenetsorok, témakörök és előfizetések névterenként. <br/> <br/> A prémium szintű: 2,000. |
| Az SQL-szűrők témánként száma |Entitás |A témakör a további szűrők létrehozása a későbbi kérelmeket a rendszer elutasítja, és a hívó kód által fogadott kivétel. |2,000 |
| A korrelációs szűrők témánként száma |Entitás |A témakör a további szűrők létrehozása a későbbi kérelmeket a rendszer elutasítja, és a hívó kód által fogadott kivétel. |100 000 |
| Az SQL-szűrők vagy műveletek mérete |Névtér |A rendszer elutasítja a további szűrők létrehozása a későbbi kérelmeket, és a hívó kód által fogadott kivétel. |Szűrési feltétel karakterlánc maximális hossza: 1024 (1-K).<br /><br />A szabály művelet karakterlánc maximális hossza: 1024 (1-K).<br /><br />Egy szabály hatására végrehajtott művelet a kifejezések maximális száma: 32. |
| Hány [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) szabályok száma a névtérben, üzenetsor vagy témakör |Entitás, névtér |A rendszer elutasítja a további kérések, a további szabályok létrehozásához, és a hívó kód által fogadott kivétel. |Szabályok maximális száma: 12. <br /><br /> A Service Bus-névtér konfigurált szabályok valamennyi üzenetsorok és témakörök a névtéren vonatkoznak. |
| Tranzakciós üzenetek száma | Tranzakció | A rendszer elutasítja a további bejövő üzenetek, és a egy kivétel szerint "nem lehet több mint 100 üzenetek küldése egy tranzakción belül" megkapta a hívó kód. | 100 <br /><br /> Mindkét **Send()** és **SendAsync()** műveleteket. |

[Azure portal]: https://portal.azure.com