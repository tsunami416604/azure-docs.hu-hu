---
title: "Az Azure Service Bus-üzenetkezelés áttekintése | Microsoft Docs"
description: "A Service Bus-üzenetkezelés és az Azure Relay ismertetése"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 05/25/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 3a4382979dd6e18c0e94b4a989bb8289882eeb89
ms.contentlocale: hu-hu
ms.lasthandoff: 05/26/2017


---
# <a name="service-bus-messaging-flexible-data-delivery-in-the-cloud"></a>Service Bus üzenetkezelés: rugalmas adattovábbítás a felhőben
A Microsoft Azure Service Bus egy megbízható információkézbesítési szolgáltatás. A szolgáltatás célja a kommunikáció megkönnyítése. Ha két vagy több fél információt szeretne cserélni, valamilyen kommunikációs eszközre van szükségük. A Service Bus egy közvetítő- vagy harmadikfél-alapú kommunikációs módszer. Hasonló a fizikai világ postai szolgáltatásaihoz. A postai szolgáltatások számos kézbesítési garanciával könnyítik meg különböző fajta levelek és csomagok küldését a világ minden táján.

A postai szolgáltatás levélkézbesítéséhez hasonlóan a Service Bus is rugalmas információkézbesítési szolgáltatást biztosít a küldő és a címzett számára. Az üzenetkezelési szolgáltatás biztosítja, hogy az információ akkor is célba érjen, ha a két fél soha nincs egyszerre online, vagy nem érhetők el pont egyszerre. Ebben a tekintetben az üzenetkezelés egy levél küldéséhez, a nem közvetítőalapú kommunikáció pedig egy telefonhíváshoz (vagy legalábbis a hívásvárakoztatás és hívóazonosítás korát megelőző idők telefonhívásaihoz) hasonlít.

Az üzenet küldője számos különböző továbbítási jellemzőt igényelhet (például tranzakciók, kettős észlelés, időalapú lejárat és kötegelés). Ezeknek a mintáknak is megvannak a postai párhuzamaik: ismételt kézbesítés, kötelező aláírás, címmódosítás vagy visszahívás.

A Service Bus két különböző üzenetkezelési mintát támogat: az *Azure Relay-t* és a *Service Bus-üzenetkezelést*.

## <a name="azure-relay"></a>Azure Relay
Az Azure Relay [WCF Relay](../service-bus-relay/relay-what-is-it.md) összetevője egy központosított (de erősen elosztott terhelésű) szolgáltatás, amely számos különböző átviteli protokollt és webszolgáltatási szabványt támogat. Ezek közé tartozik például a SOAP, a WS-* és a REST is. A [továbbítási szolgáltatás](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md) számos különböző továbbítási kapcsolati lehetőséget biztosít, és segít a közvetlen társközi kapcsolatok egyeztetésében, ha lehetséges. A Service Bus a teljesítmény és a használhatóság tekintetében a Windows Communication Foundation (WCF) szolgáltatást használó .NET-fejlesztők számára van optimalizálva, másrészt teljes hozzáférést biztosít a továbbítási szolgáltatáshoz a SOAP és a REST felületeken keresztül. Ez lehetővé teszi bármely SOAP vagy REST programozási környezet integrálását a Service Busba.

A továbbítási szolgáltatás támogatja a hagyományos egyirányú, a kérelem/válasz típusú és a társközi üzenetkezelést. Támogatja az események terjesztését is az internetes tartományban, így a pontok közötti nagyobb hatékonyság érdekében lehetővé válnak a közzétételi és előfizetési forgatókönyvek és a kétirányú szoftvercsatornás kommunikáció. A továbbítón keresztüli üzenetcsere mintában a helyszíni szolgáltatás csatlakozik a továbbítási szolgáltatáshoz egy kimenő porton keresztül, majd létrehoz egy adott szinkronizálási címhez kötött kétirányú szoftvercsatornát a kommunikációhoz. Az ügyfél ezután kommunikálhat a helyszíni szolgáltatással, ha a szinkronizálási címet megcélozva üzeneteket küld a továbbítási szolgáltatásnak. A továbbítási szolgáltatás ezután továbbítja az üzeneteket a helyszíni szolgáltatásra a már létrejött kétirányú szoftvercsatornán keresztül. Az ügyfélnek nincs szüksége közvetlen kapcsolatra a helyszíni szolgáltatással, és azt sem kell tudnia, hol található, a helyszíni szolgáltatásnak pedig nincs szüksége megnyitott bejövő portra a tűzfalon.

A kapcsolatot a helyszíni és a továbbítási szolgáltatás között kezdeményezheti egy WCF továbbító kötéskészlet használatával. A színfalak mögött a továbbítási kötéseket a rendszer a Service Busszal a felhőben integrálódó WCF-csatornaösszetevők létrehozására tervezett átviteli kötőelemekké képezi le.

A WCF Relay használatával történő üzenetcsere számos előnnyel jár, de elengedhetetlen hozzá, hogy a kiszolgáló és az ügyfél egyszerre legyen online állapotban, hogy küldeni és fogadni lehessen az üzeneteket. Ez nem optimális a HTTP-stílusú kommunikáció esetén, ahol a kérelmek jellemzően nem hosszú életűek, illetve az olyan ügyfelek számára sem, akik csak alkalmanként csatlakoznak (például böngészők, mobilalkalmazások stb.). A közvetítőalapú üzenettovábbítás támogatja a leválasztott kommunikációt, és megvannak a saját előnyei; az ügyfelek és a kiszolgálók akkor csatlakozhatnak, amikor szükséges, és a műveleteket aszinkron módon hajthatják végre.

## <a name="brokered-messaging"></a>Közvetítőalapú üzenettovábbítás
A Relay-sémával szemben a Service Bus-üzenetkezelés, más néven a [közvetítőalapú üzenettovábbítás](service-bus-queues-topics-subscriptions.md), aszinkrónnak vagy „átmenetileg leválasztottnak” tekinthető. Az adatalkotóknak (küldőknek) és a fogyasztóknak (fogadóknak) nem kell egyszerre online lenniük. Az üzenetküldési infrastruktúra megbízhatóan tárolja az üzeneteket egy közvetítőben (például várólistán), amíg a fogyasztó fél készen nem áll a fogadásukra. Ez lehetővé teszi az elosztott alkalmazás összetevőinek leválasztását, akár önkéntesen – például karbantartási céllal –, akár egy összetevő összeomlása miatt, anélkül, hogy ez az egész rendszerre hatással lenne. A fogadó alkalmazásnak továbbá csak a nap bizonyos szakaszaiban kell online lennie, például egy olyan készletkezelő rendszer esetén, amelynek csak a munkanapok végén kell futnia.

A Service Bus közvetítő alapú üzenettovábbítás infrastruktúrájának alapvető összetevői a várólisták, témakörök és előfizetések.  Az elsődleges különbség az, hogy a témakörök támogatják a közzétételi/előfizetési képességeket, amelyek segítségével kifinomultabb tartalomalapú útválasztás és kézbesítési logika érhető el, például küldés több címzettnek. Ezek az összetevők új aszinkron üzenetkezelési forgatókönyveket tesznek lehetővé, például az átmeneti leválasztást, a közzétételt/előfizetést és a terheléselosztást. További információk ezekről az üzenetkezelési entitásokról: [Service Bus queues, topics, and subscriptions](service-bus-queues-topics-subscriptions.md) (Service Bus-üzenetsorok, -témakörök és -előfizetések).

Akárcsak a WCF Relay-infrastruktúra esetén, a közvetítőalapú üzenettovábbítás képessége is elérhető a WCF és .NET-keretrendszer programozói számára, valamint REST-en keresztül is.

## <a name="next-steps"></a>Következő lépések
A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [A Service Bus-üzenetsorok használata](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)


