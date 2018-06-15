---
title: Az Azure Service Fabric események |} Microsoft Docs
description: Ismerje meg a Service Fabric események megadott kívül a jelölőnégyzetet, hogy az Azure Service Fabric-fürt figyeléséhez nyújt segítséget.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: b9372c806eab1b0ca69ba078d972b076c8a7d6f6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212719"
---
# <a name="service-fabric-events"></a>Service Fabric-események 

A Service Fabric-platformról kulcs működési tevékenységek történik a fürtön belül több strukturált eseményt ír. Ezek között fürt frissítések replika elhelyezésének döntésekhez. Minden esemény, hogy a Service Fabric képeződik le az alábbi entitásokat a fürt egyik mutatja:
* Fürt
* Alkalmazás
* Szolgáltatás
* Partíció
* Replika 
* Tároló

Események jelennek meg, ha a platform - teljes listájának megtekintéséhez [lista a Service Fabric események](service-fabric-diagnostics-event-generation-operational.md).

Az alábbiakban néhány olyan fontos forgatókönyveit, hogy eseményeket megjelennie a fürtön. 
1. Csomópont életciklus-események: csomópontok elérni, leáll, aktiválása vagy inaktiválása vagy újraindul, eseményeket jeleníti meg, mi történt megjelenik, és azonosíthatja, ha egy hiba lépett fel a géppel magát, vagy ha egy API-t, amely a ú keresztül lett meghívva a csomópont állapotának módosítása.
1. A fürt frissítésének:, a fürt frissítése (KB verziója vagy a konfigurációs módosítást), látni fogja kezdeményezzen, végig a UDs visszaállítani, és végezze el a frissítés (vagy visszaállítási). 
1. Alkalmazásfrissítések: hasonlóan a fürt frissíti, a frissítés áthalad nincs események széles választékát. Lehet, hogy ezek az események amikor frissítésre lett ütemezve, a frissítés az aktuális állapotát, és a teljes eseménysorozat működésének megértése. Ez akkor hasznos, például hogy vissza mi frissítések rendelkezik már megkezdődött sikeresen.
1. Alkalmazás/szolgáltatás telepítése / törlése: nincsenek események, a minden alkalmazás, szolgáltatás és -tároló létrehozása vagy törlése.
1. Partíció helyezi (Konfigurálás): eseményt naplózott, amikor az állapot-nyilvántartó partíció egy újrakonfigurálás (a replikakészlethez változása) kerül,. Ez akkor hasznos, ha tudni, hogy milyen gyakran próbálja a partíció replikakészlethez változik, vagy nyomon, hogy melyik csomópont futott az elsődleges replika bármikor időben.
1. Chaos események: a Service Fabric használatakor [Chaos](service-fabric-controlled-chaos.md) szolgáltatástól, látni fogja események minden alkalommal, amikor a szolgáltatás elindult, vagy leállították, vagy ha azt a hibát a rendszer esetében.
1. Állapotával kapcsolatos események: A Service Fabric közzététele állapotával kapcsolatos események figyelmeztetés vagy a rendszerállapot hibajelentés jön létre, vagy egy entitás visszatér egy OK állapotát, vagy egy jelentés lejár. Ezek az események nagyon hasznosak entitás korábbi állapotát statisztikáinak nyomon követéséhez. 

## <a name="how-to-access-events"></a>Hogyan férhet hozzá események

Néhány, amelyen keresztül a Service Fabric események elérhető különböző módja van:
* a működési csatornán keresztül. Ezek az Azure Diagnostics-bővítmény segítségével gyűjtött és egy tárolási tábla vagy egy eszköz, például az OMS szolgáltatáshoz történő adatfeldolgozást küldött. Ha "diagnosztika" fürt engedélyezve van, az Azure diagnosztikai ügynök telepítve van a fürt, és úgy konfigurálva, hogy olvassa a műveleti csatornát a naplók alapértelmezés szerint ki van. További információk konfigurálása a [Azure diagnosztikai ügynök](service-fabric-diagnostics-event-aggregation-wad.md) a fürt további naplóit vagy teljesítményszámlálóit átvételéhez diagnosztika konfigurációjának módosítása. 
* a EventStore szolgáltatás Rest API-kon keresztül, amelyek lehetővé teszik, hogy a fürt lekérdezése közvetlenül, vagy a Service Fabric ügyféloldali kódtár keresztül. Lásd: [lekérdezés EventStore API-khoz, fürthöz kapcsolódó események](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>További lépések
* További információ a figyelést a fürt - [figyelési a fürt és a platform](service-fabric-diagnostics-event-generation-infra.md).
* További információ a EventStore szolgáltatás - [EventStore szolgáltatás áttekintése](service-fabric-diagnostics-eventstore.md)
