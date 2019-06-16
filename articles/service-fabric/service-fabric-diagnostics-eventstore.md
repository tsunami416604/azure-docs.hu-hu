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
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: f1e7428bc0665cdd3f981bb9c2e7b1f564598f40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074242"
---
# <a name="eventstore-overview"></a>Az EventStore áttekintése

>[!NOTE]
>Kezdődően a Service Fabric verziója 6.4. az EventStore API-k csak Azure-on futó Windows-fürtök csak érhetők el. Ez a funkció a Linux, valamint az önálló fürtök portolása dolgozunk.

## <a name="overview"></a>Áttekintés

6\.2 verziójában bevezetett, a EventStore szolgáltatás az figyelési lehetőség, a Service Fabricben. Az EventStore lehetővé teszi a fürt vagy az adott számítási feladatok állapotának megjelenítése az idő.
Az EventStore egy állapotalapú Service Fabric-szolgáltatás, amely fenntartja az események a fürtből. Az esemény a Service Fabric Explorert, REST és API-k keresztül érhetők el. EventStore lekérdezi a közvetlenül a diagnosztikai adatok gyorsan minden entitás, a fürtben a fürt, és könnyebben használható:

* Fejlesztési vagy tesztelési időben diagnosztizálhatja a problémákat, vagy ahol a monitorozási folyamatban használni lehet, hogy
* Győződjön meg arról, hogy felügyeleti műveleteket a fürtön készítésének feldolgozott megfelelően
* "Pillanatkép" hogyan Service Fabric egy adott entitás implementálására beolvasása

![EventStore](media/service-fabric-diagnostics-eventstore/eventstore.png)

Az EventStore elérhető események teljes listáját lásd: [Service Fabric-események](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>Kezdődően a Service Fabric verziója 6.4. az EventStore API-k és a felhasználói felület általánosan elérhetők az Azure Windows-fürtök esetén. Ez a funkció a Linux, valamint az önálló fürtök portolása dolgozunk.

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

### <a name="azure-cluster-version-65"></a>Az Azure fürtverzió 6.5-ös +
Ha az Azure-fürthöz lekérdezi frissített 6.5-ös vagy újabb verzió, az EventStore automatikusan engedélyezve lesz a fürtön. Megfelelő, frissítenie fürtsablonhoz a következő:

* API-verziót használja `2019-03-01` vagy újabb 
* Adja hozzá a következő kódot a Tulajdonságok szakaszának a fürtben
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>6\.4 Azure fürt verziója

6\.4 verzióját használja, ha az Azure Resource Manager-sablon EventStore szolgáltatás bekapcsolása szerkesztheti. Ez történik, végrehajtásával egy [fürt konfiguráció frissítése](service-fabric-cluster-config-upgrade-azure.md) és adja hozzá az alábbi kódot, segítségével PlacementConstraints helyezi a replikákat a EventStore szolgáltatás egy adott NodeType például egy dedikált a rendszer szolgáltatások NodeType . A `upgradeDescription` szakaszban konfigurálja a config frissítés, kezdeményezzen újraindítást a csomópontokon. A szakasz egy másik frissítés távolíthatja el.

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
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
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
