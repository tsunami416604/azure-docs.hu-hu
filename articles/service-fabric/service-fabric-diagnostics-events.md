---
title: Azure Service Fabric-események
description: Ismerje meg a Service Fabric-események a dobozból, hogy segítsen az Azure Service Fabric-fürt figyelése.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 638b650e485ad3e83bd6021639a7e55b540d9cdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451731"
---
# <a name="service-fabric-events"></a>Service Fabric-események 

A Service Fabric platform több strukturált eseményt ír a fürtön belül zajló kulcsfontosságú operatív tevékenységekhez. Ezek a fürtfrissítésektől a replikaelhelyezési döntésekig terjednek. Minden esemény, amelyet a Service Fabric elérhetővé tesz, leképezi a fürt alábbi entitásainak egyikét:
* Fürt
* Alkalmazás
* Szolgáltatás
* Partíció
* Replika 
* Tároló

A platform által elérhetővé tett események teljes listájának megtekintéséhez – [A Service Fabric-események listája](service-fabric-diagnostics-event-generation-operational.md).

Íme néhány példa olyan forgatókönyvek, amelyek a fürtben eseményeket kell látnia. 
* Csomópont életciklus-események: a csomópontok jönnek fel, menj le, skála be / ki, újraindítás, és aktivált/inaktivált, ezek az események lesznek kitéve, amely megmutatja, mi történt, és segít azonosítani, ha valami baj van a gép maga, vagy ha volt egy API-t, amely az SF-en keresztül, hogy módosítsa a csomópont állapotát.
* Fürtfrissítés: a fürt frissítésekor (SF-verzió vagy konfigurációváltozás) megjelenik a frissítés kezdeményezése, az egyes frissítési tartományok végiggörgetése és a teljes (vagy visszaállítás). 
* Alkalmazásfrissítések: csakúgy, mint a fürt frissítések, van egy átfogó események, mint a frissítés gördül át. Ezek az események hasznosak lehetnek a frissítés ütemezésének, a frissítés aktuális állapotának és az események teljes sorrendjének megértéséhez. Ez akkor hasznos, ha visszatekint, hogy milyen frissítéseket vezettek be sikeresen, vagy hogy a visszaállítás elindult-e.
* Alkalmazás/szolgáltatás telepítése/törlése: minden egyes alkalmazáshoz, szolgáltatáshoz és tárolóhoz vannak események, létrehozva vagy törölve, és hasznos a replikák be- és kiméretezésekor, pl. a replikák számának növelése
* Partíció áthelyezése (újrakonfigurálás): amikor egy állapotalapú partíció átmegy egy újrakonfiguráláson (a replikakészlet módosítása), egy eseményt naplóz a rendszer. Ez akkor hasznos, ha megpróbálja megérteni, hogy a partíció replikakészlete milyen gyakran változik vagy nem adja fel a feladatát, vagy nyomon követi, hogy melyik csomópont futtatta az elsődleges replikát bármikor.
* Káosz események: a Service Fabric [Chaos](service-fabric-controlled-chaos.md) szolgáltatás használatakor események jelennek meg minden alkalommal, amikor a szolgáltatás elindul, vagy leáll, vagy ha befecskendezegy hibát a rendszerben.
* Állapotesemények: A Service Fabric állapoteseményeket tesz elérhetővé minden alkalommal, amikor egy figyelmeztetés vagy egy hibaállapot-jelentés jön létre, vagy egy entitás egy OK állapotba kerül, vagy egy állapotjelentés lejár. Ezek az események nagyon hasznos a korábbi állapotstatisztikák nyomon követéséhez egy entitás. 

## <a name="how-to-access-events"></a>Az események elérése

A Service Fabric-események néhány különböző módon érhetők el:
* Az eseményeket a szabványos csatornákon keresztül, például etw/Windows eseménynaplók, és láthatóvá bármely figyelési eszköz, amely támogatja ezeket, például az Azure Monitor naplók. Alapértelmezés szerint a portálon létrehozott fürtök diagnosztika be van kapcsolva, és a Windows Azure diagnosztikai ügynök elküldi az eseményeket az Azure table storage, de továbbra is integrálni kell ezt a log analytics erőforrás. További információ az [Azure Diagnostics ügynök](service-fabric-diagnostics-event-aggregation-wad.md) konfigurálásáról a fürt diagnosztikai konfigurációjának módosítására további naplók vagy teljesítményszámlálók felvételéhez, valamint az [Azure Monitor naplók integrációjának](service-fabric-diagnostics-event-analysis-oms.md)
* EventStore szolgáltatás Rest API-k, amelyek lehetővé teszik a fürt közvetlen lekérdezése, vagy a Service Fabric ügyfélkönyvtáron keresztül. A [fürtesemények lekérdezési eventstore API-i.](service-fabric-diagnostics-eventstore-query.md)

## <a name="next-steps"></a>További lépések
* További információ a fürt figyelése - [A fürt és a platform figyelése](service-fabric-diagnostics-event-generation-infra.md).
* További információ az EventStore szolgáltatásról – [Az EventStore szolgáltatás áttekintése](service-fabric-diagnostics-eventstore.md)
