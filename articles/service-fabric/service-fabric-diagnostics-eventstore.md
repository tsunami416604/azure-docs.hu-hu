---
title: Az Azure Service Fabric eseménytárolója
description: Ismerje meg az Azure Service Fabric EventStore, egy módja annak, hogy egy fürt vagy számítási feladatok állapotát bármikor megismerheti és figyelheti.
author: srrengar
ms.topic: conceptual
ms.date: 6/6/2019
ms.author: srrengar
ms.openlocfilehash: d23c8114bf10ef3225775accef6910c0ba539e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645735"
---
# <a name="eventstore-overview"></a>EventStore – áttekintés

>[!NOTE]
>A Service Fabric 6.4-es verziójától. az EventStore API-k csak az Azure-ban futó Windows-fürtök esetén érhetők el. Dolgozunk a portolás ezt a funkciót a Linux, valamint a mi önálló klaszterek.

## <a name="overview"></a>Áttekintés

A 6.2-es verzióban bevezetett EventStore szolgáltatás a Service Fabric figyelési lehetőség. Az EventStore lehetővé teszi a fürt vagy a munkaterhelések állapotának megértését egy adott időpontban.
Az EventStore egy állapotalapú Service Fabric szolgáltatás, amely a fürtből származó eseményeket tart karban. Az esemény a Service Fabric Explorer, rest és API-k on keresztül érhető el. Az EventStore közvetlenül lekérdezi a fürtöt, hogy diagnosztikai adatokat kapjon a fürt bármely entitásáról, és a következőkre kell használni:

* A fejlesztés vagy tesztelés során felmerülő problémák diagnosztizálása, illetve a figyelési folyamat használata
* Annak ellenőrzése, hogy a fürtön végrehajtott felügyeleti műveletek feldolgozása megfelelő en van-e
* "Pillanatkép" beszerezni egy adott entitással való interakciót a Service Fabric

![EventStore (Rendezvény)](media/service-fabric-diagnostics-eventstore/eventstore.png)

Az EventStore-ban elérhető események teljes listáját a [Service Fabric-események](service-fabric-diagnostics-event-generation-operational.md)című témakörben láthatja.

>[!NOTE]
>A Service Fabric 6.4-es verziójától. az EventStore API-k és a felhasználói felület általában elérhető az Azure Windows-fürtök. Dolgozunk a portolás ezt a funkciót a Linux, valamint a mi önálló klaszterek.

Az EventStore szolgáltatás lekérdezhető a fürt minden egyes entitásához és entitástípusához elérhető eseményekről. Ez azt jelenti, hogy a következő szinteken kérdezhet eseményeket:
* Fürt: magára a fürtre jellemző események (pl. fürtfrissítés)
* Csomópontok: minden csomópontszintű esemény
* Csomópont: egy csomópontra jellemző események, amelyeket a`nodeName`
* Alkalmazások: minden alkalmazásszintű esemény
* Alkalmazás: egy alkalmazásra jellemző események, amelyeket a`applicationId`
* Szolgáltatások: a fürtök összes szolgáltatásának eseményei
* Szolgáltatás: események egy adott szolgáltatás által azonosított`serviceId`
* Partíciók: események az összes partícióról
* Partíció: események egy adott partícióról, amelyet a`partitionId`
* Partícióreplikák: események az összes replikák / példányok egy adott partíción azonosított`partitionId`
* Partícióreplika: események egy adott `replicaId` replika / példány által azonosított és`partitionId`

Ha többet szeretne megtudni az API-ról, olvassa el az [EventStore API referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore).

Az EventStore szolgáltatás is képes korrelálni események a fürtben. Az eventstore szolgáltatás képes összekapcsolni ezeket az eseményeket, hogy segítsen azonosítani a fürttevékenységeinek okait, ha megnézi az okat, amelyek egyszerre különböző entitásokból készültek, és amelyek hatással lehetnek egymásra. Ha például az egyik alkalmazás véletlenül nem kifogástalan állapotúvá válik az indukált módosítások nélkül, az EventStore a `Error` `Warning` platform által elérhetővé tett egyéb eseményeket is megvizsgálja, és ezt egy vagy eseményhez is korrelálhatja. Ez segít a gyorsabb hibaészlelésben és a kiváltó okok elemzésében.

## <a name="enable-eventstore-on-your-cluster"></a>Az EventStore engedélyezése a fürtön

### <a name="local-cluster"></a>Helyi fürt

A [fürtfabricSettings.json webhelyén](service-fabric-cluster-fabric-settings.md)adja hozzá az EventStoreService szolgáltatást addOn szolgáltatásként, és hajtsa végre a fürt frissítését.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Az Azure cluster 6.5-ös verziója
Ha az Azure-fürt 6.5-ös vagy újabb verzióra frissül, az EventStore automatikusan engedélyezve lesz a fürtön. A leiratkozáshoz frissítenie kell a fürtsablont a következőkkel:

* Api-verzió használata `2019-03-01` vagy újabb verzió 
* Adja hozzá a következő kódot a fürt tulajdonságszakaszához
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Az Azure cluster 6.4-es verziója

Ha a 6.4-es verziót használja, szerkesztheti az Azure Resource Manager-sablont az EventStore szolgáltatás bekapcsolásához. Ez úgy történik, hogy végrehajtja a [fürt konfigurációs frissítés,](service-fabric-cluster-config-upgrade-azure.md) és hozzáadja a következő kódot, akkor Elhelyezésconstraints segítségével az EventStore szolgáltatás replikáit egy adott NodeType,például egy NodeType dedikált a rendszerszolgáltatások. A `upgradeDescription` szakasz úgy konfigurálja a konfigurációs frissítést, hogy indítsa el a csomópontok újraindítását. A szakaszt eltávolíthatja egy másik frissítésből.

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
* Első lépések az EventStore API-val – [Az EventStore API-k használata az Azure Service Fabric-fürtökben](service-fabric-diagnostics-eventstore-query.md)
* További információ az EventStore – [Service Fabric-események](service-fabric-diagnostics-event-generation-operational.md) által kínált események listájáról
* A figyelés és a diagnosztika áttekintése a Service Fabric ben – [Figyelés és diagnosztika a Service Fabric-hez](service-fabric-diagnostics-overview.md)
* Az API-hívások teljes listájának megtekintése - [EventStore REST API-referencia](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-eventsstore)
* További információ a fürt figyeléséről – [A fürt és a platform figyelése.](service-fabric-diagnostics-event-generation-infra.md)
