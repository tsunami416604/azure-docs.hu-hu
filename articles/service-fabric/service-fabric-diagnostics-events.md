---
title: Az Azure Service Fabric-események |} A Microsoft Docs
description: További információ a Service Fabric-események azonnal használatra kész segítséget nyújtanak az Azure Service Fabric-fürt monitorozására.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 936a47593b9db6e4989c30b2df37dfd82c286c59
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290518"
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

Az alábbiakban néhány példa forgatókönyvet, hogy megjelenik a fürt eseményeire a PowerShell. 
* Csomópont-életciklusesemények: csomópontok merülnek fel, leáll, és horizontális skálázása, indítsa újra, és aktiválása/inaktiválása, ezeket az eseményeket jeleníti meg, mi történt lesz közzétéve, és segít azonosítani, ha valamilyen hiba történt a gépet, vagy ha egy API-t korábban volt csomópont állapotának módosítása SF keresztül meghívott.
* Fürt frissítése: mivel a fürt frissítése (SF verziója vagy a konfigurációs módosítás), látni fogja kezdeményezni, állítsa át a frissítési tartományok mindegyike, és hajtsa végre a frissítést (vagy visszaállítással). 
* Alkalmazásfrissítések: csakúgy, mint a fürtfrissítések, van egy átfogó események, a frissítés áthalad. Ezek az események akkor lehet hasznos, amikor ütemezett frissítést, a frissítés aktuális állapotát, és az események teljes sorozatát megértéséhez. Ez hasznos vissza szeretne milyen frissítéseket kell lett egyik tagján jelennek meg sikeresen, vagy hogy a visszaállítás lett elindítva.
* Alkalmazás/szolgáltatás telepítése / törlése: minden alkalmazás, szolgáltatás és a tárolóban, az éppen létrehozott vagy törölt, és hasznos be vagy ki például a replikák számának növelésével vertikális esemény
* Partíció áthelyezi (Konfigurálás): egy állapotalapú partíció egy újrakonfigurálás (a módosítás a replika) lép, amikor egy eseményt a rendszer naplózza. Ez akkor hasznos, ha meg szeretné ismerni, hogy milyen gyakran a partíció replikakészlethez módosítása vagy feladatátvétele, illetve nyomon követheti, melyik csomópont futott az elsődleges replika tetszőleges időpontban.
* A Chaos események: Service Fabric használatakor [káosz](service-fabric-controlled-chaos.md) szolgáltatástól, látni fogja események minden alkalommal, amikor a szolgáltatás elindult vagy leállt, vagy amikor a rendszer egy tartalék azt kódtárba.
* A Szolgáltatásállapot-események: Service Fabric hálózatállapot-események minden alkalommal, amikor figyelmeztetés vagy egészségügyi hibajelentés jön létre, vagy egy jelentés lejár, illetve egy entitás visszatér egy OK állapot arra tesz elérhetővé. Ezek az események nagyon hasznosak egy entitás korábbi egészségügyi statisztikák nyomon követéséhez. 

## <a name="how-to-access-events"></a>Események elérése

Van néhány eltérő módot, amelyekkel Service Fabric-események érhető el:
* Az események naplózása ETW/Windows-eseménynaplók például standard csatornákon keresztül, és az alábbi ábra szerint bármely felügyeleti eszköz, amely támogatja ezeket a Log Analytics például. Alapértelmezés szerint a portálon létrehozott fürtök diagnosztika engedélyezve van, és a Windows Azure diagnostics-ügynök az események küldése az Azure table storage rendelkeznek, de továbbra is szeretné ez integrálható, így a Log Analytics-erőforrás. További információk a konfigurálása a [Azure Diagnostics-ügynök](service-fabric-diagnostics-event-aggregation-wad.md) a fürt további naplóit vagy teljesítményszámlálóit csomópontmetrikák diagnostics konfigurációjának módosítása és a [Log Analytics-integráció](service-fabric-diagnostics-event-analysis-oms.md)
* Az EventStore szolgáltatás Rest API-k, amelyek lehetővé teszik, hogy a fürt lekérdezése közvetlenül, vagy a Service Fabric ügyféloldali kódtár használatával. Lásd: [lekérdezés EventStore API-k a fürthöz kapcsolódó események](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>További lépések
* További információ a fürtben – a figyelés [figyelése a fürt és a platform](service-fabric-diagnostics-event-generation-infra.md).
* További információ az EventStore service – [EventStore szolgáltatás áttekintése](service-fabric-diagnostics-eventstore.md)
