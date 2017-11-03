---
title: "A Service Fabric-fürt erőforráskezelő-szabályozás |} Microsoft Docs"
description: "Ismerje meg, a szabályozások a Service Fabric fürt-kezelő által biztosított konfigurálásához."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 22223923f6783f11178b44c3c3087f17eb8dc41b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>A Service Fabric fürt erőforrás-kezelő szabályozása
Akkor is, ha megfelelően konfigurálta a erőforrás-kezelőt, a fürt képes lekérni megszakad. Például lehet egyidejű csomópont és a tartalék tartomány hibák - mi történne, a frissítés során történt? A fürt erőforrás-kezelő mindig megpróbálja mindent, javítsa ki próbálta átrendezéséhez, és hárítsa el a fürt a fürt erőforrásait fel. Szabályozások biztosítható a backstop, hogy a fürt használhassa erőforrások stabilizálásához – térjen vissza a csomópontokat, a hálózati partíciók javítandó, javított bits telepítve.

E helyzetek rendezése érdekében a Service Fabric fürt erőforrás-kezelő számos szabályozások tartalmazza. Ezek a szabályozások összes viszonylag nagy kalapácsok. Általában akkor nem szabad módosítani nélkül körültekintően megtervezve és tesztelését.

A fürt Resource Manager szabályozások módosításához kell hangolja a várt tényleges betölteni. Telepíteni kell néhány szabályozások helyen, akkor is, ha az azt jelenti, hogy a fürt hosszabb időt vesz igénybe stabilizálásához bizonyos esetekben előfordulhat, hogy határozza meg. Tesztelési szükség a szabályozások a megfelelő értékek meghatározásáról. Szabályozások kell lennie, amennyi elegendő a fürt igazodjanak elfogadható időn belül, és amely ténylegesen megakadályozza a túl sok erőforrás-felhasználás. 

Az ügyfelek is láttuk az esetek többségében szabályozások, mert már erőforrás korlátozott környezetben lett használja. Néhány példa lenne, az egyes csomópontok vagy lemezek, amelyek képesnek sok állapot-nyilvántartó replikák párhuzamos átviteli korlátozásai miatt nem korlátozott a sávszélesség. Nélkül szabályozások műveletek sikerült ne terhelje tovább ezeket az erőforrásokat, sikertelen vagy lassú művelet okozza. Ilyen helyzetekben az ügyfelek szabályozások használt, és azok mennyi ideig volt kiterjesztése tudtak időt vesz igénybe a fürt egy stabil állapotot elérni. Az ügyfelek is tisztában készíthet a futó alacsonyabb teljes megbízhatóságot, amíg azok volt halmozódni végződhetnek sikerült.


## <a name="configuring-the-throttles"></a>A szabályozások konfigurálása

Service Fabric replika típusú áthelyezések számának szabályozás kétféle módszer van. Az alapértelmezett mechanizmus, amely létezett a Service Fabric 5.7 jelöli, sávszélesség-szabályozás a kurzor engedélyezett abszolút értékként. Ez nem működik a fürtök különböző méretű. Különösen nagy fürtök esetén az alapértelmezett érték lehet túl kicsi, és jelentősen lelassult terheléselosztás akkor is, ha szükség közben nem hatású kisebb fürt. Ez a korábbi mechanizmus felül lett írva százalék alapú szabályozásával, amely dinamikus fürtökkel, amely a szolgáltatások és a csomópontok száma módosítsa rendszeresen hatékonyabb méretezést.

A szabályozások a replikákat a fürtök számának százalékos alapulnak. Alapú Percetage szabályozások engedélyezése a szabály kifejezése: "nem helyezhető át több mint 10 %-replikák 10 percenként", például.

Százalék alapú sávszélesség-szabályozás konfigurációs beállításai a következők:

  - GlobalMovementThrottleThresholdPercentage - áthelyezések bármikor, fürt engedélyezett maximális száma a fürtben lévő replikák száma százalékában kifejezve. 0 a korlátozás nélküli állapotot jelzi. Az alapértelmezett értéke 0. Ha ez a beállítás és a GlobalMovementThrottleThreshold meg van adva, a korlátozóbb korlátot szerepel.
  - GlobalMovementThrottleThresholdPercentageForPlacement - áthelyezések száma a fürtben lévő replikák százalékában kifejezve engedélyezett elhelyezési fázis során maximális száma. 0 a korlátozás nélküli állapotot jelzi. Az alapértelmezett értéke 0. Ha ez a beállítás és a GlobalMovementThrottleThresholdForPlacement meg van adva, a korlátozóbb korlátot szerepel.
  - GlobalMovementThrottleThresholdPercentageForBalancing - áthelyezések száma a fürtben lévő replikák százalékában kifejezve engedélyezett a terheléselosztási fázis során maximális száma. 0 a korlátozás nélküli állapotot jelzi. Az alapértelmezett értéke 0. Ha ez a beállítás és a GlobalMovementThrottleThresholdForBalancing meg van adva, a korlátozóbb korlátot szerepel.

A késleltetési százalékos megadásakor 0,05, akkor adja meg a 5 %. Az időköz, amelyen ezek a szabályozások szabályozza a GlobalMovementThrottleCountingInterval, a másodpercben megadott értéke.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Alapértelmezett alapján szabályozások
Ezen információ abban az esetben régebbi fürttel rendelkezik, vagy még mindig tartsa meg ezeket a konfigurációkat, azóta frissített fürtökben. Általánosságban ajánlott, hogy ezek a fenti százalék alapú szabályozások helyett. Százalék alapú sávszélesség-szabályozás alapértelmezés szerint le van tiltva, mivel ezek a szabályozások marad az alapértelmezett szabályozások fürt le van tiltva, és a százalék alapú szabályozások helyére. 

  - GlobalMovementThrottleThreshold – Ez a beállítás a fürtben lévő áthelyezések száma szabályozza a néhány idővel. Idő másodpercben, mint a GlobalMovementThrottleCountingInterval van megadva. Az alapértelmezett értéke a GlobalMovementThrottleThreshold 1000, és a GlobalMovementThrottleCountingInterval alapértelmezett értéke 600.
  - MovementPerPartitionThrottleThreshold – Ez a beállítás bármely szolgáltatás partíció áthelyezések száma szabályozza a néhány idővel. Idő másodpercben, mint a MovementPerPartitionThrottleCountingInterval van megadva. Az alapértelmezett értéke a MovementPerPartitionThrottleThreshold 50, és a MovementPerPartitionThrottleCountingInterval alapértelmezett értéke 600.

Ezek a szabályozások a konfigurációját, a százalék alapú sávszélesség-szabályozás azonos mintát követi.

## <a name="next-steps"></a>Következő lépések
- Hogyan kezeli a fürt erőforrás-kezelő, és elosztja a terhelést a fürt kapcsolatos további tudnivalókért tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
- A fürt erőforrás-kezelő rendelkezik a fürt leíró számos lehetőséget. További információkért róluk, tekintse meg a cikk a [leíró a Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)
