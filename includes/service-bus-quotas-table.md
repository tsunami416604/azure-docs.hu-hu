---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 08/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1116d6037a149b379bd96d6b208ca306a38c7a03
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52288688"
---
Az alábbi táblázat a Service Bus-üzenetkezelés adott kvótákra vonatkozó információk. A díjszabással kapcsolatos információk és a többi Service Bus kvóták, tekintse meg a [Service Bus díjszabása](https://azure.microsoft.com/pricing/details/service-bus/) áttekintése.

| Kvóta neve | Hatókör | Megjegyzések | Érték |
| --- | --- | --- | --- | --- |
| Azure-előfizetésenként alapszintű / standard szintű névterek maximális száma |Névtér |További alapszintű / standard szintű névterekhez későbbi kérelmeket a rendszer elutasítja a portál által. |100|
| Prémium szintű névterek Azure-előfizetésenként maximális száma |Névtér |A további prémium szintű névterek későbbi kérelmeket a rendszer elutasítja a portál által. |10 |
| Üzenetsor vagy témakör mérete |Entitás |Határozza meg, az üzenetsor vagy-témakör létrehozása után. <br/><br/> A rendszer elutasítja a további bejövő üzenetek, és a hívó kód által fogadott kivétel. |1, 2, 3, 4 GB-os vagy 5 GB.<br /><br />A prémium szintű Termékváltozat, valamint a Standard és a [particionálás](/azure/service-bus-messaging/service-bus-partitioning) engedélyezve van, a maximális üzenetsor vagy témakör mérete 80 GB. |
| Egy névtér az egyidejű kapcsolatok száma |Névtér |További kapcsolatok későbbi kérelmeket a rendszer elutasítja, és a hívó kód által fogadott kivétel. REST-műveleteket nem számítanak fel az egyidejű TCP-kapcsolatok. |NetMessaging: 1000<br /><br />AZ AMQP: 5 000 |
| Párhuzamosan futó száma egy üzenetsor vagy üzenettéma/előfizetés entitáson kérelmek fogadásához |Entitás |Ezt követő kap kérelmek azért lettek elutasítva, és a egy kivételt a hívó kód által fogadott. Ez a kvóta vonatkozik, a kombinált számú párhuzamosan futó fogadási műveletek az összes előfizetés témakörökre. |5000 |
| Témakörök és üzenetsorok száma névterenként |Névtér |Új témakör vagy a névtér a várólista létrehozása a későbbi kérelmeket a rendszer elutasítja. Eredményként, ha keresztül lehet konfigurálni a [az Azure portal][Azure portal], akkor jön létre, egy hibaüzenet. A felügyeleti API-t hív, ha kivétel a hívó kód érkezik. |1000 (alapszintű/Standard szintű). Üzenettémák és -névtérben található üzenetsorok száma legfeljebb 1000 kell lennie. <br/><br/>A prémium szintű üzenetkezelési unit(MU) 1000. Maximális 4000-es érték. |
| Hány [témakörök/üzenetsorok particionálása](/azure/service-bus-messaging/service-bus-partitioning) névterenként |Névtér |Későbbi kérelmeket egy új particionált témakör vagy sor a névtér létrehozásakor a rendszer elutasítja. Eredményként, ha keresztül lehet konfigurálni a [az Azure portal][Azure portal], akkor jön létre, egy hibaüzenet. Ha a felügyeleti API-t, nevű egy **QuotaExceededException** a hívó kód által fogadott kivétel. |Alapszintű és Standard csomagokról – 100<br/><br/>A particionált entitások nem támogatottak a [prémium](../articles/service-bus-messaging/service-bus-premium-messaging.md) szint.<br/><br />Minden egyes particionált üzenetsor vagy témakör felé számolnak 1000 entitások névterenként kvótáját. |
| Minden üzenetküldési entitás elérési útja maximális mérete: üzenetsor vagy témakör |Entitás |- |260 karakter hosszúságú lehet |
| Minden üzenetküldési entitás neve maximális mérete: névteret, az előfizetés vagy az előfizetés szabály |Entitás |- |50 karakter hosszú lehet |
| Egy üzenet maximális mérete [munkamenet-azonosító](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entitás |- | 128 |
| Egy üzenetsor vagy üzenettéma/előfizetés egység mérete |Entitás |Ezek a kvóták túllépéséből bejövő üzenetek a rendszer elutasítja, és a hívó kód által fogadott kivétel. |Maximális mérete: 256 KB-os ([Standard szintű](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([prémium szintű](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />Rendszer terhelés miatt a Ez a korlátozás nem éri el ezeket az értékeket.<br /><br />A fejléc maximális mérete: 64 KB-os<br /><br />Fejléc tulajdonságai a tulajdonságcsomag maximális számát: **bájt /: egész szám. MaxValue**<br /><br />A tulajdonságcsomag tulajdonság maximális mérete: nincs explicit korlát. Attól függ, a fejléc maximális méretét. |
| Egy üzenetsor vagy üzenettéma/előfizetés entitás tulajdonság mérete |Entitás |A **SerializationException** kivétel akkor jön létre. |Minden egyes tulajdonság tulajdonság maximális mérete az összesített mérete 32 K. az összes tulajdonság nem lehet hosszabb 64 k Ez a korlátozás vonatkozik teljes fejlécében a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), mindkettő szerepel felhasználói tulajdonságok, valamint a rendszer tulajdonságai (például [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [címke](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ Üzenetazonosító](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid), és így tovább). |
| Témánként előfizetéseinek száma. |Entitás |A témakör a további előfizetések létrehozására szolgáló későbbi kérelmeket a rendszer elutasítja. Ennek eredményeképpen ha a portálon keresztül konfigurálható, egy hibaüzenet jelenik meg. Ha a felügyeleti API-tól nevű kivételt a hívó kód érkezik. |Standard szintű csomag – az egyes előfizetésekhez beleszámít a kvótát, az 1000 entitások (üzenetsorok, témakörök és előfizetések) névterenként. <br/> <br/> A prémium szintű – 2000 |
| Az SQL-szűrők témánként száma |Entitás |A témakör a további szűrők létrehozása a későbbi kérelmeket a rendszer elutasítja, és a hívó kód által fogadott kivétel. |2,000 |
| A korrelációs szűrők témánként száma |Entitás |A témakör a további szűrők létrehozása a későbbi kérelmeket a rendszer elutasítja, és a hívó kód által fogadott kivétel. |100 000 |
| SQL-szűrők/művelet mérete |Névtér |További szűrők létrehozása a későbbi kérelmeket a rendszer elutasítja, és a hívó kód által fogadott kivétel. |Szűrési feltétel karakterlánc maximális hossza: 1024 (1-K).<br /><br />A szabály művelet karakterlánc maximális hossza: 1024 (1-K).<br /><br />Egy szabály hatására végrehajtott művelet a kifejezések maximális számát: 32. |
| Hány [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) szabályok száma a névtérben, üzenetsor vagy témakör |Entitás, névtér |A további szabályok létrehozásához későbbi kérelmeket visszautasítja, és a hívó kód által fogadott kivétel. |Szabályok maximális száma: 12. <br /><br /> A Service Bus-névtér konfigurált szabályok valamennyi üzenetsorok és témakörök a névtéren vonatkoznak. |
| Tranzakciós üzenetek száma | Tranzakció | A rendszer elutasítja a további bejövő üzenetek, és a egy kivétel szerint "nem lehet több mint 100 üzenetek küldése egy tranzakción belül" megkapta a hívó kód. | 100 <br /><br /> Mindkét **Send()** és **SendAsync()** műveleteket. |

[Azure portal]: https://portal.azure.com