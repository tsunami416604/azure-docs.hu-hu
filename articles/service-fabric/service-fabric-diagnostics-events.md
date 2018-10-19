---
title: Az Azure Service Fabric-események |} A Microsoft Docs
description: További információ a Service Fabric-események azonnal használatra kész segítséget nyújtanak az Azure Service Fabric-fürt monitorozására.
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
ms.openlocfilehash: ca63d67f6d7c19b4ca6928c4cc0f9ccb06eace2b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402981"
---
# <a name="service-fabric-events"></a>Service Fabric-események 

A Service Fabric platformot a főbb üzemeltetési tevékenységek történik a fürtön belül több strukturált eseményt ír. Ezek köre a fürtfrissítések replika elhelyezésének döntéseket hozhat. Minden egyes esemény, hogy a Service Fabric vannak leképezve a következő entitásokat a fürt egyik tesz elérhetővé:
* Fürt
* Alkalmazás
* Szolgáltatás
* Partíció
* Replika 
* Tároló

A platform - által közzétett események teljes listájának megtekintéséhez [lista a Service Fabric-események](service-fabric-diagnostics-event-generation-operational.md).

Az alábbiakban néhány példa fontos forgatókönyvet lehetővé tesz, hogy megjelenik a fürt eseményeket. 
1. Csomópont-életciklusesemények: csomópontok merülnek fel, leáll, aktiválása/inaktiválása, vagy újraindul, eseményeket jeleníti meg, mi történt lesz közzétéve, és segít azonosítani, ha valamilyen hiba történt a gépet, vagy hiba történt egy API, amely a SF keresztül lett meghívva csomópont állapotának módosítása.
1. Fürt frissítése: mivel a fürt frissítése (SF verziója vagy a konfigurációs módosítás), látni fogja kezdeményez, a frissítési tartománnyal egyes állítja, és hajtsa végre a frissítést (vagy visszaállítással). 
1. Alkalmazásfrissítések: ehhez hasonlóan a fürt frissíti, a frissítés áthalad nincs események átfogó készletét. Ezek az események akkor lehet hasznos, amikor ütemezett frissítést, a frissítés aktuális állapotát, és az események teljes sorozatát megértéséhez. Ez akkor hasznos, való vissza a tekintse meg, milyen frissítéseket kell lett egyik tagján jelennek meg sikeresen.
1. Alkalmazás/szolgáltatás telepítése / törlése: nincsenek események, az egyes alkalmazás-, szolgáltatás és tárolót, létrehozása vagy törlése.
1. Partíció áthelyezi (Konfigurálás): egy állapotalapú partíció egy újrakonfigurálás (a módosítás a replika) lép, amikor egy eseményt a rendszer naplózza. Ez akkor hasznos, ha tudni, hogy milyen gyakran szeretne a partíció replikakészlethez változnak, vagy nyomon, hogy melyik csomópont futott az elsődleges replika tetszőleges időpontban.
1. A Chaos események: Service Fabric használatakor [káosz](service-fabric-controlled-chaos.md) szolgáltatástól, látni fogja események minden alkalommal, amikor a szolgáltatás elindult vagy leállt, vagy amikor a rendszer egy tartalék azt kódtárba.
1. A Szolgáltatásállapot-események: Service Fabric hálózatállapot-események minden alkalommal, amikor figyelmeztetés vagy egészségügyi hibajelentés jön létre, vagy egy jelentés lejár, illetve egy entitás visszatér egy OK állapot arra tesz elérhetővé. Ezek az események nagyon hasznosak egy entitás korábbi egészségügyi statisztikák nyomon követéséhez. 

## <a name="how-to-access-events"></a>Események elérése

Van néhány eltérő módot, amelyekkel Service Fabric-események érhető el:
* keresztül a műveleti csatorna. Ezek az Azure Diagnostics bővítmény gyűjtött és egy tárolótáblában használatalapú vagy egy eszköz, például az Azure Log Analytics-eseményközpontba küldi. "Diagnosztika" engedélyezve van a fürt számára, amikor az Azure Diagnostics-ügynök telepítve van a fürt, és úgy konfigurálva, hogy olvassa el a műveleti csatornát a naplók alapértelmezés szerint. További információk a konfigurálása a [Azure Diagnostics-ügynök](service-fabric-diagnostics-event-aggregation-wad.md) a fürt további naplóit vagy teljesítményszámlálóit csomópontmetrikák diagnostics konfigurációjának módosítása. 
* az EventStore szolgáltatás Rest API-kon keresztül, amelyek lehetővé teszik, hogy a fürt lekérdezése közvetlenül, vagy a Service Fabric ügyféloldali kódtár használatával. Lásd: [lekérdezés EventStore API-k a fürthöz kapcsolódó események](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>További lépések
* További információ a fürtben – a figyelés [figyelése a fürt és a platform](service-fabric-diagnostics-event-generation-infra.md).
* További információ az EventStore service – [EventStore szolgáltatás áttekintése](service-fabric-diagnostics-eventstore.md)
