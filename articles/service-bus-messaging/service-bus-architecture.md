---
title: "Az Azure Service Bus üzenetfeldolgozási architektúrájának áttekintése | Microsoft Docs"
description: "A cikk ismerteti az Azure Service Bus üzenetfeldolgozási architektúráját."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: b810618b485b631e1d72b24c2a9587017d635cc4
ms.contentlocale: hu-hu
ms.lasthandoff: 09/06/2017

---
# <a name="service-bus-architecture"></a>Service Bus-architektúra
Ez a cikk ismerteti az Azure Service Bus üzenetfeldolgozási architektúrát.

## <a name="service-bus-scale-units"></a>A Service Bus skálázási egységei
A Service Bus rendszerezése *skálázási egységek* szerint történik. A skálázási egység egy telepítési egység, amely a szolgáltatás futtatásához szükséges összes összetevőt tartalmazza. Minden régió telepít egy vagy több Service Bus skálázási egységet.

A skálázási egységre egy Service Bus-névtér lesz leképezve. A skálázási egység kezeli a Service Bus-entitások minden típusát (üzenetsorok, témakörök, előfizetések). A Service Bus skálázási egység az alábbi összetevőkből áll:

* **Átjárócsomópontok készlete.** Az átjárócsomópontok hitelesítik a bejövő kérelmeket. Minden átjáró nyilvános IP-címmel rendelkezik.
* **Üzenetközvetítő csomópontok készlete.** Az üzenetközvetítő csomópontok az üzenetküldési entitásokkal kapcsolatos kérelmeket dolgozzák fel.
* **Egy átjárótároló.** Az átjárótároló a skálázási egységben definiált összes entitás adatait tartalmazza. Az átjáró tároló az SQL Azure-adatbázisban jön létre.
* **Több üzenetküldési tároló.** Az üzenetküldési tárolók a skálázási egységben definiált összes üzenetsor, témakör és előfizetés üzeneteit tartalmazzák. Emellett az előfizetések összes adata is megtalálható bennük. Ha a [particionált üzenetküldési entitások](service-bus-partitioning.md) engedélyezve vannak, az üzenetsor vagy témakör egyetlen üzenetküldési tárolóra lesz leképezve. Az előfizetések ugyanabban az üzenetküldési tárolóban vannak, mint a szülő témakörük. A Service Bus [Prémium szintű üzenetkezelés](service-bus-premium-messaging.md) kivételével az üzenetküldési tárolók az SQL Azure-adatbázisokban vannak.

## <a name="containers"></a>Tárolók
Minden üzenetküldési entitás egy adott tárolóhoz van társítva. A tároló egy logikai szerkezet, amely egy üzenetküldési tárolót használ a tároló szempontjából releváns összes adat tárolására. Az egyes tárolók egy üzenetközvetítő csomóponthoz vannak társítva. Általában több tároló van, mint üzenetközvetítő csomópont. Ezért az egyes üzenetközvetítő csomópontok több tárolót is betöltenek. A tárolók elosztása az üzenetközvetítő csomópontok között úgy történik, hogy a csomópontok terhelése egyenlő legyen. Ha a terhelési mintázatok megváltoznak (pl. az egyik tároló terhelése túlzottan megnő), vagy ha egy üzenetküldési csomópont átmenetileg nem érhető el, akkor a rendszer újra szétosztja a tárolókat az üzenetközvetítő csomópontok között.

## <a name="processing-of-incoming-messaging-requests"></a>Bejövő üzenetküldési kérelmek feldolgozása
Amikor egy ügyfél kérelmet küld a Service Busnak, az Azure Load Balancer továbbítja azt valamelyik átjáró csomópontnak. Az átjárócsomópont engedélyezi a kérelmet. Ha a kérelem egy üzenetküldési entitásra vonatkozik (üzenetsor, témakör, előfizetés), akkor az átjáró kikeresi az entitást az átjáró tárolójából, és meghatározza, hogy az entitás melyik üzenetküldési tárolóban található. Ezután megkeresi, hogy melyik üzenetközvetítő csomópont szolgálja ki a tárolót, és elküldi a kérelmet ennek az üzenetközvetítő csomópontnak. Az üzenetközvetítő csomópont feldolgozza a kérelmet, és frissíti az entitás állapotát a tárolóban. Az üzenetközvetítő csomópont ezután visszaküldi a választ az átjárócsomópontnak, az pedig visszaküld egy megfelelő választ az eredeti kérelmet kibocsátó ügyfélnek.

![Bejövő üzenetküldési kérelmek feldolgozása](./media/service-bus-architecture/ic690644.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy elolvasta a Service Bus architektúrájának áttekintését, további információkért kövesse az alábbi hivatkozásokat:

* [Service Bus messaging overview](service-bus-messaging-overview.md) (A Service Bus üzenetkezelésének áttekintése)
* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Üzenetsor-kezelési megoldás a Service Bus által kezelt üzenetsorok használatával](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



