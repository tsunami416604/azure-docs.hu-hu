---
title: Az Azure Service Fabric esemény Store |} Microsoft Docs
description: További tudnivalók az Azure Service Fabric EventStore
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 7e401a310ce9a5f59473353227a9ce36767aac3c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="eventstore-service-overview"></a>EventStore szolgáltatás áttekintése

>[!NOTE]
>Től Service Fabric 6.2 verziót. a EventStore API-k jelenleg csak Azure-on futó Windows-fürtök előzetes verzióját. Ez a funkció a Linux, valamint az önálló fürtök eljárás dolgozunk.

## <a name="overview"></a>Áttekintés

6.2 verziójában bevezetett, a EventStore szolgáltatás beállítás figyelési a Service Fabric, amely a fürt vagy egy munkaterhelés állapotának megértését időben megoldást kínál. A EventStore szolgáltatást, hogy hívások API-k segítségével a Service Fabric-események közzététele. Ezen EventStore API-k lehetővé teszik a fürt közvetlenül diagnosztikai adatok eléréséhez a fürt egyetlen entitás lekérdezése, és segítségével használható:
* Fejlesztési vagy tesztelési problémák diagnosztizálása, vagy ha-t használja egy felügyeleti folyamatot
* Győződjön meg arról, hogy a fürt készítésének felügyeleti műveletek feldolgozott megfelelően a fürt

Elérhető a EventStore események teljes listájának megtekintéséhez lásd: [Service Fabric események](service-fabric-diagnostics-event-generation-operational.md).

A EventStore szolgáltatás által biztosított minden entitáshoz és a fürt entitástípus események lehet lekérdezni. Ez azt jelenti, hogy lekérdezhető az események a következő szinten;
* Fürt: fürt összes szintű események
* Csomópontok: minden csomópont szintű események
* Csomópont: események alapján egy csomópont jellemző `nodeName`
* Alkalmazások: az összes alkalmazás szintű események
* Alkalmazás: események egy alkalmazáshoz
* Szolgáltatások: a fürt összes szolgáltatás eseményeinek
* Szolgáltatás: egy adott szolgáltatás eseményeinek
* Partíciók: az összes partíció események
* Partíció: egy adott partícióra eseményeinek
* Replikák: az összes replika események / -példányok
* A replika: események az adott replika / példány


A EventStore szolgáltatás is van lehetősége, a fürt események összefüggéseket. Előfordulhat, hogy van hatással, egymástól különböző entitások az egy időben írt események megtekintésével a EventStore szolgáltatás nem tudja csatolni ezek az események segítségével azonosíthatja a tevékenységek a fürt okait. Például ha a saját alkalmazásai legyen, ez egy mesterségesen előidézett módosítások nélkül nem kifogástalan, a EventStore történik a további események is nézze meg a platform által elérhetővé tett és tudott összefüggéseket a egy `NodeDown` esemény. Ez segít a gyorsabb tárhelyhiba-észlelés és gyökérszintű analysis okoz.

Első lépésként a EventStore szolgáltatás használatával, lásd: [lekérdezés EventStore API-khoz, fürthöz kapcsolódó események](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>További lépések
* Figyelés és a Service Fabric - diagnosztika áttekintése [megfigyelési és diagnosztikai a Service Fabric](service-fabric-diagnostics-overview.md)
* További tudnivalók a figyelés a fürt- [figyelési a fürt és a platform](service-fabric-diagnostics-event-generation-infra.md).