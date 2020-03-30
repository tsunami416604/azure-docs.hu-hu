---
title: Szabályozás a Service Fabric fürterőforrás-kezelőben
description: Ismerje meg a Service Fabric fürterőforrás-kezelő által biztosított szabályozások konfigurálását.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452160"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>A service fabric fürterőforrás-kezelő szabályozása
Még akkor is, ha megfelelően konfigurálta a fürterőforrás-kezelőt, a fürt megszakadhat. Például lehet nek egyidejű csomópont- és tartalék tartományhibák – mi történne, ha ez egy frissítés során történne? A fürterőforrás-kezelő mindig mindent megpróbál kijavítani, és a fürt erőforrásait felemészti, és megpróbálja átszervezni és kijavítani a fürtöt. A szabályozások segítségével biztosítható a backstop, hogy a fürt erőforrásokat használhat a stabilizáláshoz – a csomópontok visszatérnek, a hálózati partíciók gyógyulnak, a javított bitek üzembe helyezése.

Az ilyen típusú helyzetek megoldásához a Service Fabric fürterőforrás-kezelő több szabályozást is tartalmaz. Ezek a fojtószelepek mind meglehetősen nagy kalapácsok. Általában nem szabad megváltoztatni gondos tervezés és tesztelés nélkül.

Ha módosítja a fürterőforrás-kezelő szabályozásait, hangolja őket a várható tényleges terheléshez. Előfordulhat, hogy bizonyos fojtószelepeket kell elhelyeznie, még akkor is, ha ez azt jelenti, hogy a fürt bizonyos helyzetekben hosszabb időt vesz igénybe. A szabályozások helyes értékeinek meghatározásához vizsgálatra van szükség. A szabályozásnak elég magasnak kell lennie ahhoz, hogy a fürt ésszerű időn keresztül reagáljon a változásokra, és elég alacsonynak ahhoz, hogy ténylegesen megakadályozza a túl sok erőforrás-felhasználást. 

Az idő nagy részében láttuk az ügyfelek használata fojtószelepek volt, mert már egy erőforrás korlátozott környezetben. Néhány példa az egyes csomópontok korlátozott hálózati sávszélessége, vagy olyan lemezek, amelyek az átviteli korlátozások miatt nem képesek sok állapotalapú replikát párhuzamosan építeni. Szabályozások nélkül a műveletek elboríthatják ezeket az erőforrásokat, ami a műveletek sikertelenvagy lassú. Ezekben a helyzetekben az ügyfelek sávszélesség-szabályozást használtak, és tudták, hogy meghosszabbítják azt az időt, amerre a fürt stabil állapotot érhet el. Az ügyfelek azt is megértették, hogy alacsonyabb általános megbízhatósággal is futhatnak, miközben fojtogatták őket.


## <a name="configuring-the-throttles"></a>A szabályozások konfigurálása

A Service Fabric két mechanizmust rendelkezik a replikamozgások számának szabályozásához. A Service Fabric 5.7 előtt létező alapértelmezett mechanizmus a szabályozást az engedélyezett lépések abszolút számaként jelöli. Ez nem működik a fürtök minden méretben. Különösen a nagy fürtök esetében az alapértelmezett érték túl kicsi lehet, jelentősen lelassítva a kiegyensúlyozást még akkor is, ha szükséges, miközben nincs hatása a kisebb fürtökre. Ezt a korábbi mechanizmust a százalékos alapú szabályozás váltotta fel, amely jobban skálázódik a dinamikus fürtökkel, amelyekben a szolgáltatások és csomópontok száma rendszeresen változik.

A szabályozások a fürtökben lévő replikák számának százalékán alapulnak. A százalékos alapú szabályozások lehetővé teszik a szabály kifejezését: "ne helyezze át a replikák 10 perc alatt 10%-ánál többet".

A százalékos szabályozás konfigurációs beállításai a következők:

  - GlobalMovementThrottleThresholdPercentage – A fürtben bármikor engedélyezett mozgások maximális száma a fürt összes replikájának százalékában kifejezve. A 0 azt jelzi, hogy nincs korlátozás. Az alapértelmezett érték a 0. Ha mind ezt a beállítást, mind a GlobalMovementThrottleThreshold-ot meg kell adni, akkor a rendszer a konzervatívabb korlátot használja.
  - GlobalMovementThrottleThresholdPercentageForPlacement – Az elhelyezési fázisban engedélyezett mozgások maximális száma a fürt összes replikájának százalékában kifejezve. A 0 azt jelzi, hogy nincs korlátozás. Az alapértelmezett érték a 0. Ha mind ezt a beállítást, mind a GlobalMovementThrottleThresholdForPlacement-t meg kell adni, akkor a rendszer a konzervatívabb korlátot használja.
  - GlobalMovementThrottleThresholdPercentageForBalancing – A kiegyenlítési fázisban engedélyezett mozgások maximális száma a fürt összes replikájának százalékában kifejezve. A 0 azt jelzi, hogy nincs korlátozás. Az alapértelmezett érték a 0. Ha mind ezt a beállítást, mind a GlobalMovementThrottleThresholdForBalancing-t meg kell adni, akkor a rendszer a konzervatívabb korlátot használja.

A fojtószelep százalékának megadásakor 5%-ot kell megadnia 0.05 értékként. A szabályozások szabályozásának időköze a GlobalMovementThrottleCountingInterval, amely másodpercben van megadva.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

a ClusterConfig.json keresztül önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtökhöz:

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

### <a name="default-count-based-throttles"></a>Alapértelmezett számalapú szabályozások
Ez az információ abban az esetben érhető el, ha régebbi fürtök, vagy továbbra is megtartja ezeket a konfigurációkat a fürtök, amelyek azóta frissített. Általában ajánlott, hogy ezeket a fenti százalékalapú szabályozásokkal cserélje ki. Mivel a százalékos alapú szabályozás alapértelmezés szerint le van tiltva, ezek a szabályozások maradnak a fürt alapértelmezett szabályozásai, amíg le nem tiltják őket, és le nem cserélik őket a százalékalapú szabályozásokkal. 

  - GlobalMovementThrottleThreshold – ez a beállítás szabályozza a fürt mozgásainak teljes számát egy ideig. Az idő mennyiség másodpercben van megadva GlobalMovementThrottleCountingInterval formában. A GlobalMovementThrottleThreshold alapértelmezett értéke 1000, a GlobalMovementThrottleCountingInterval alapértelmezett értéke pedig 600.
  - MovementPerPartitionThrottleThreshold – ez a beállítás szabályozza a szolgáltatások partícióinak mozgásainak teljes számát egy ideig. Az idő mennyiség másodpercben van megadva movementperpartitionthrottleCountingInterval formában. A MovementPerPartitionThrottleThreshold alapértelmezett értéke 50, a MovementPerPartitionThrottleCountingInterval alapértelmezett értéke pedig 600.

Ezek a szabályozások konfigurációja ugyanazt a mintát követi, mint a százalékos alapú szabályozás.

## <a name="next-steps"></a>További lépések
- Ha meg szeretné tudni, hogy a fürterőforrás-kezelő hogyan kezeli és egyensúlyozza ki a terhelést a fürtben, olvassa el a [terhelés elosztásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, olvassa el ezt a cikket a [Service Fabric-fürt leírásáról](service-fabric-cluster-resource-manager-cluster-description.md)
