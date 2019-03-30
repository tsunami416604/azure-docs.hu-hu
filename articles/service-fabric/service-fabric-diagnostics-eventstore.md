---
title: Az Azure Service Fabric esemény Store |} A Microsoft Docs
description: További tudnivalók az Azure Service Fabric EventStore
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: b8e1958947ced5ea2d0bd8b34667210bf935072d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662907"
---
# <a name="eventstore-service-overview"></a>Az EventStore szolgáltatás áttekintése

>[!NOTE]
>Kezdődően a Service Fabric verziója 6.4. az EventStore API-k csak Azure-on futó Windows-fürtök csak érhetők el. Ez a funkció a Linux, valamint az önálló fürtök portolása dolgozunk.

## <a name="overview"></a>Áttekintés

6.2 verziójában bevezetett, a EventStore szolgáltatás az figyelési lehetőség, a Service Fabricben. Az EventStore lehetővé teszi a fürt vagy az adott számítási feladatok állapotának megjelenítése az idő. Az EventStore egy állapotalapú Service Fabric-szolgáltatás, amely fenntartja az események a fürtből. Az esemény a Service Fabric Explorert, REST és API-k keresztül érhetők el. EventStore lekérdezi a közvetlenül a diagnosztikai adatok gyorsan minden entitás, a fürtben a fürt, és könnyebben használható:

* Fejlesztési vagy tesztelési időben diagnosztizálhatja a problémákat, vagy ahol a monitorozási folyamatban használni lehet, hogy
* Győződjön meg arról, hogy felügyeleti műveleteket a fürtön készítésének feldolgozott megfelelően
* "Pillanatkép" hogyan Service Fabric egy adott entitás implementálására beolvasása

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Az EventStore elérhető események teljes listáját lásd: [Service Fabric-események](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Kezdődően a Service Fabric verziója 6.2. az EventStore API-k jelenleg csak Azure-on futó Windows-fürtök előzetes verzióban érhető el. Ez a funkció a Linux, valamint az önálló fürtök portolása dolgozunk.

Az EventStore szolgáltatás eseményeket, amelyek elérhetők minden entitáshoz és a fürt entitástípus lehet lekérdezni. Ez azt jelenti, hogy a következő szinteken események lekérdezhető:
* Fürt: események (pl. fürt frissítése) magához a fürthöz adott
* Csomópont: az összes csomópont szintű eseményeket
* Csomópont: egy csomópont által azonosított adott események `nodeName`
* Alkalmazások: az összes alkalmazásszintű események
* Alkalmazás: események által azonosított egy alkalmazáshoz `applicationId`
* Szolgáltatások: a fürtök összes szolgáltatásra eseményei
* Szolgáltatás: egy adott szolgáltatás által azonosított események `serviceId`
* Partíciók: az összes partíció események
* Partíció: egy adott partíció által azonosított események `partitionId`
* Partícióreplikák: az összes replika események / példányok által azonosított adott partíción belül `partitionId`
* Címtárpartíció replikájának: események egy meghatározott replikáról / példány által azonosított `replicaId` és `partitionId`

Ismerje meg, további információt az API-t tekintse át a [EventStore API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

Az EventStore szolgáltatás is van lehetősége, a fürtben lévő események korrelációját. Megnézzük az eseményeket, amelyek egy időben a különböző entitások, amely hatással lehetett egymással készültek, az EventStore szolgáltatás nem tudja csatolni ezeket az eseményeket a fürtben lévő tevékenységek okainak azonosítása érdekében. Például ha egy, az alkalmazások a nem megfelelő állapotú, ez egy mesterségesen előidézett módosítása nélkül történik, az EventStore lesz az eseményeket is tekintse meg a platform által elérhetővé tett funkciót pedig hathatósabb sikerült Ez az egy `Error` vagy `Warning` esemény. Ez segít a gyorsabb, hibaészlelés, és az alapvető okok elemzése.

## <a name="enable-eventstore-on-your-cluster"></a>Az EventStore engedélyezése a fürtön

### <a name="local-cluster"></a>Helyi fürt

A [a fürtben fabricSettings.json](service-fabric-cluster-fabric-settings.md), EventStoreService hozzáadása egy bővítmény szolgáltatásai és a fürt frissítésének végrehajtása.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster"></a>Azure-fürtben

A fürt Azure Resource Manager-sablonban, bekapcsolhatja a EventStore szolgáltatás végrehajtásával egy [fürt konfiguráció frissítése](service-fabric-cluster-config-upgrade-azure.md) , és adja hozzá az alábbi kódot. A `upgradeDescription` szakaszban konfigurálja a config frissítés, kezdeményezzen újraindítást a csomópontokon. A szakasz egy másik frissítés távolíthatja el.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>További lépések
* Az EventStore API – első lépések [EventStore API-val az Azure Service Fabric-fürtök](service-fabric-diagnostics-eventstore-query.md)
* További tudnivalók az események EventStore - által kínált [Service Fabric-események](service-fabric-diagnostics-event-generation-operational.md)
* Figyelés és diagnosztika felderítése a Service Fabric - áttekintése [Monitorozás és diagnosztika a Service Fabrichez](service-fabric-diagnostics-overview.md)
* API-hívások – teljes listájának megtekintéséhez [EventStore REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* A fürt monitorozásával kapcsolatos további információkért – [figyelése a fürt és a platform](service-fabric-diagnostics-event-generation-infra.md).