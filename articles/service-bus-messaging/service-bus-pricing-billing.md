---
title: A Service Bus árak és számlázás |} A Microsoft Docs
description: A Service Bus díjszabásáról struktúra áttekintése.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 7c45b112-e911-45ab-9203-a2e5abccd6e0
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: 4faf37394b8e4f6c4e463acb11aea898a29fef80
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448407"
---
# <a name="service-bus-pricing-and-billing"></a>A Service Bus árak és számlázás

Az Azure Service Bus Standard csomagban érhető el, és [prémium](service-bus-premium-messaging.md) szinten. Kiválaszthatja, hogy minden egyes szolgáltatás Service Bus-névtér, Ön által létrehozott szolgáltatásrétegébe, és a kiválasztott csomag érvényes névtéren belül létrehozott összes entitás között.

> [!NOTE]
> Aktuális Service Bus díjszabásáról kapcsolatos részletes információkért lásd: a [Azure Service Bus díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/service-bus/), és a [Service Bus – gyakori kérdések](service-bus-faq.md#pricing).
>
>

A Service Bus-üzenetsorok és üzenettémák, előfizetések a következő 2 mérőszámok használ:

1. **Üzenetkezelési műveletek**: üzenetsor vagy üzenettémák/előfizetések végpontjai API-hívásokként vannak definiálva. Ez az érték üzeneteket küldött vagy fogadott,-üzenetsorok és üzenettémák, előfizetések számlázható Usage elsődleges egységét váltja fel.
2. **Felügyelt kapcsolat**: definiált egy adott egy órás mintavételi időszakban üzenetsorok, témakörök vagy előfizetések nyílt állandó kapcsolatok maximális száma. Ez az érték csak a Standard szintű, amelyben további kapcsolatok megnyithatja vonatkozik (korábban a kapcsolatok-ra volt korlátozva üzenetsor vagy üzenettéma/előfizetés / 100) kapcsolatonként névleges díj ellenében.

A **Standard** szint vezet be, az üzenetsorok és témakörök/előfizetések, a legmagasabb szintű használati akár 80 %-os kötet-alapú kedvezmények eredményez a végrehajtott műveletek adatmennyiségen díjszabása. Emellett van egy Standard szintű alapdíj 10 USD havonta, amely lehetővé teszi, hogy 12,5 millió művelet / hó további költségek nélkül.

A **prémium** szintű erőforrás-elkülönítést, a Processzor és a memóriarétegben biztosít, így minden ügyfél számítási feladata elkülönítve fut. Ennek az erőforrás-tárolónak a neve *üzenetkezelési egység*. Legalább egy üzenetkezelési egység van lefoglalva minden prémium névtérhez. Az egyes Service Bus prémium névterekhez 1, 2 vagy 4 üzenetkezelési egység vásárolható. Egyetlen számítási feladat vagy entitás több üzenetkezelési egységre is kiterjedhet, az üzenetkezelési egységek száma pedig tetszés szerint módosítható, bár a számlázás 24 órás vagy napi díjszabás szerint történik. Az eredmény a Service Bus-alapú megoldás kiszámítható és ismételhető teljesítménye. Nem csak kiszámíthatóbb és nagyobb rendelkezésre állású a teljesítmény, de gyorsabb is.

> [!NOTE]
> Üzenettémák és előfizetések érhetők el csak a Standard vagy prémium szintű díjcsomagok árából; az alapszintű csomag csak a várólisták támogatja.

A Standard szintű alapdíjat Azure-előfizetésenként havonta egyszer csak díjszabásának. Ez azt jelenti, hogy miután létrehozott egy Standard szint a Service Bus-névteret, hozhat létre tetszőleges számú további standard szintű névteret alapdíjat nélkül szeretné, hogy ugyanazon Azure-előfizetéshez.

A [Service Bus díjszabásáról](https://azure.microsoft.com/pricing/details/service-bus/) tábla a Standard és Premium szintű működési különbségeit foglalja össze.

## <a name="messaging-operations"></a>Üzenetküldési műveletek

Üzenetsorok és üzenettémák, előfizetések számlázása "művelet" nem egy üzenet. Egy művelet bármely API-hívás egy üzenetsor vagy üzenettéma/előfizetés végpontot társzolgáltatásokhoz hivatkozik. Ide tartoznak a kezeléssel, a küldéssel/fogadással és a munkamenet-állapottal kapcsolatos műveletek is.

| Művelettípus | Leírás |
| --- | --- |
| Kezelés |Hozzon létre, olvasási, frissítési, törlési (CRUD) üzenetsorok vagy üzenettémák/előfizetések. |
| Üzenetkezelés |Üzenetek küldése és fogadása az üzenetsorok vagy üzenettémák/előfizetések. |
| Munkamenet állapota |Készítsen, vagy állítsa be a munkamenet-állapot üzenetsor vagy üzenettéma/előfizetés. |

Költség részletekért lásd: a felsorolt díjak a [Service Bus díjszabásáról](https://azure.microsoft.com/pricing/details/service-bus/) lapot.

## <a name="brokered-connections"></a>Felügyelt kapcsolatok

*Felügyelt kapcsolat* "tartósan csatlakoztatott" küldők és fogadók üzenetsorok, témakörök vagy előfizetések nagy számú érintő használati minták befogadásához. Állandó csatlakoztatott küldők és fogadók, azokat, amelyek egy nem nulla értékű AMQP vagy HTTP használatával csatlakoznak kap időkorlátja (például HTTP hosszú lekérdezések). HTTP küldők és fogadók egy azonnali időtúllépéssel nem hoznak létre a felügyelt kapcsolatok.

Kapcsolat kvótákkal és egyéb szolgáltatási korlátai: a [Service Bus-kvóták](service-bus-quotas.md) cikk. A felügyelt kapcsolatok kapcsolatos további információkért lásd: a [– gyakori kérdések](#faq) Ez a cikk későbbi szakaszában talál.

A Standard szintű megszűnik az a névtér kapcsolatonként korlátozás, és a az Azure-előfizetés között kapcsolatonként összesített használati számát. További információkért lásd: a [felügyelt kapcsolat](https://azure.microsoft.com/pricing/details/service-bus/) tábla.

> [!NOTE]
> 1000 felügyelt kapcsolatot foglal magában a Standard szintű üzenetküldés (keresztül az alapszintű díjat), és keresztül valamennyi üzenetsorok, témakörök és előfizetések társított Azure-előfizetésen belül megoszthatók.
>
>

<br />

> [!NOTE]
> A szolgáltatás díjszabását az egyidejű kapcsolatok maximális száma határozza meg. Az elszámolás óraszám alapján, havi 744 óra figyelembe vételével történik.
>
>

### <a name="premium-tier"></a>Prémium szintű csomag

Prémium szinten a felügyelt kapcsolatokért nem számítunk fel díjat.

## <a name="faq"></a>GYIK

### <a name="what-are-brokered-connections-and-how-do-i-get-charged-for-them"></a>Mik a felügyelt kapcsolat, és hogyan fizetnem a számukra?

Felügyelt kapcsolat a következők egyike lehet:

1. Az AMQP-kapcsolaton egy ügyfél egy Service Bus-üzenetsor vagy üzenettéma/előfizetés.
2. Egy HTTP-hívás üzenetet fogad egy Service Bus-üzenettémától vagy -várólistától, amelynek fogadási időkorlátja nagyobb, mint nulla.

A Service Bus díjszabást a szolgáltatásban foglalt mennyiséget (Standard szinten 1000) túllépő, egyidejű felügyelt kapcsolatok maximális száma határozza meg. A maximális szám mérése óránként történik, majd havi 744 órával elosztva elkészül az elszámolás, amely a havi számlázási időszak alatt halmozódik. A szolgáltatásban foglalt mennyiség (1000 felügyelt kapcsolat havonta) a számlázási időszak végén kerül felszámításra az óránként megállapított maximális kapcsolatok összege alapján.

Példa:

1. Minden 10 000 eszköz egyetlen AMQP-kapcsolaton keresztül kapcsolódik, és parancsokat fogad egy Service Bus-témakörbe. Az eszközök telemetrikus eseményeket küld egy eseményközpontnak. Ha az összes eszköz 12 órán keresztül minden nap, a következő csatlakozási díjakkal alkalmazni (más Service Bus témakör vonatkozó díjakon felül): 10 000 kapcsolódás * 12 óra * 31 nap / 744 = 5000 felügyelt kapcsolat. Havi kedvezmény az 1000 felügyelt kapcsolatot számlázunk 4000 felügyelt kapcsolatot, a 0,03 $ $120 összesen, felügyelt kapcsolatonként aránya.
2. 10 000 készülék fogad egy Service Bus-üzenetsorba, HTTP-n keresztül nem nulla értékű időkorlát megadása mellett üzeneteket. Ha az összes eszköz 12 órán keresztül minden nap, látni fogja a következő csatlakozási díjakkal (bármely más Service Bus-díjon felül): 10 000 HTTP-fogadási kapcsolódás * 12 óra naponta * 31 nap / 744 óra = 5000 felügyelt kapcsolat.

### <a name="do-brokered-connection-charges-apply-to-queues-and-topicssubscriptions"></a>A felügyelt kapcsolatok díjai a várólistákra és az üzenettémákra/előfizetésekre is vonatkoznak?

Igen. Az események HTTP-n keresztül történő küldése – a küldő rendszerek és eszközök számától függetlenül – díjmentes. Más néven "hosszú lekérdezések" nullánál nagyobb időkorláttal használatával HTTP-fogadott események hoz létre a felügyelt kapcsolati díjat. Az AMQP-kapcsolatok küldésre és fogadásra használt kapcsolatok esetén is felügyelt kapcsolati díjat generálnak. Az Azure-előfizetés az összes Standard névtérben létrehozott első 1000 felügyelt kapcsolat (az alapdíjon) külön díjfizetés nélkül is. Mivel ezek a kedvezmények vonatkoznak a legtöbb szolgáltatások közötti üzenetküldési forgatókönyvre, felügyelt kapcsolati díjat általában csak akkor válnak megfelelő, ha azt tervezi, hogy a nagy mennyiségű ügyfelet; AMQP vagy HTTP hosszú lekérdezéseket használni Ha például hatékonyabb elérése vagy sok eszköz vagy alkalmazáspéldány kétirányú kommunikációt engedélyezze.

## <a name="next-steps"></a>További lépések

* További információ a Service Bus díjszabásáról teljes körű információkért lásd: a [díjszabását ismertető lapon a Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* Tekintse meg a [Service Bus – gyakori kérdések](service-bus-faq.md#pricing) az egyes Service bus díjszabással és a számlázással kapcsolatos gyakori kérdéseket.

[Azure portal]: https://portal.azure.com
