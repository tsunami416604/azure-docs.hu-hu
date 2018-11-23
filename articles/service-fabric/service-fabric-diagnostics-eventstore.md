---
title: Az Azure Service Fabric esemény Store |} A Microsoft Docs
description: További tudnivalók az Azure Service Fabric EventStore
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: cd58e24a51b153d6bf217f7d32a82e882183ed73
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290671"
---
# <a name="eventstore-service-overview"></a>Az EventStore szolgáltatás áttekintése

## <a name="overview"></a>Áttekintés

6.2 verziójában bevezetett, a EventStore szolgáltatás az figyelési lehetőség, a Service Fabricben, amely lehetővé teszi, hogy a fürt vagy az adott számítási feladatok állapotának megjelenítése az idő. Az EventStore szolgáltatás a Service Fabric-események API-k (REST-végpontokat keresztül vagy az ügyféloldali kódtár keresztül elérhető) egy készlete-n keresztül tesz elérhetővé. Az EventStore API-k lehetővé teszik, hogy a közvetlenül a diagnosztikai adatok gyorsan minden entitás, a fürtben a fürt lekérdezése, és segítségével használható:

* Fejlesztési vagy tesztelési időben diagnosztizálhatja a problémákat, vagy ahol a monitorozási folyamatban használni lehet, hogy
* Győződjön meg arról, hogy felügyeleti műveleteket a fürtön készítésének feldolgozott megfelelően a fürt

Az EventStore elérhető események teljes listáját lásd: [Service Fabric-események](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Kezdődően a Service Fabric verziója 6.2. az EventStore API-k jelenleg csak Azure-on futó Windows-fürtök előzetes verzióban érhető el. Ez a funkció a Linux, valamint az önálló fürtök portolása dolgozunk.

Az EventStore szolgáltatás eseményeket, amelyek elérhetők minden entitáshoz és a fürt entitástípus lehet lekérdezni. Ez azt jelenti, hogy a következő szinteken; események lekérdezhető
* Fürt: fürt összes események
* Csomópont: az összes csomópont szintű eseményeket
* Csomópont: az egyik csomóponton alapján adott események `nodeName`
* Alkalmazások: az összes alkalmazásszintű események
* Alkalmazás: események és a egy alkalmazáshoz
* Szolgáltatások: a fürtök összes szolgáltatásra eseményei
* Szolgáltatás: egy adott szolgáltatás eseményeit
* Partíciók: az összes partíció események
* Partíció: egy adott partícióra események
* Replikák: az összes replika események / -példányok
* A replika: események egy meghatározott replikáról / példány


Az EventStore szolgáltatás is van lehetősége, a fürtben lévő események korrelációját. Megnézzük az eseményeket, amelyek egy időben a különböző entitások, amely hatással lehetett egymással készültek, az EventStore szolgáltatás nem tudja csatolni ezeket az eseményeket a fürtben lévő tevékenységek okainak azonosítása érdekében. Például ha egy, az alkalmazások a nem megfelelő állapotú, ez egy mesterségesen előidézett módosítása nélkül történik, az EventStore lesz az eseményeket is tekintse meg a platform által elérhetővé tett funkciót pedig hathatósabb sikerült Ez az egy `NodeDown` esemény. Ez segít a gyorsabb, hibaészlelés, és az alapvető okok elemzése.

Azt javasoljuk, hogy az EventStore gyors elemzés céljából, valamint hogy a pillanatkép kiindulópont annak megértéséhez, hogy a fürt működik, és dolog történik Ha a várt.

Ismerkedés az EventStore szolgáltatás használatával, lásd: [lekérdezés EventStore API-k a fürthöz kapcsolódó események](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>További lépések
* Figyelés és diagnosztika felderítése a Service Fabric - áttekintése [Monitorozás és diagnosztika a Service Fabrichez](service-fabric-diagnostics-overview.md)
* A fürt monitorozásával kapcsolatos további információkért – [figyelése a fürt és a platform](service-fabric-diagnostics-event-generation-infra.md).