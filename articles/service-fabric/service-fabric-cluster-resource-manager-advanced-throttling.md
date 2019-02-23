---
title: A Service Fabric fürterőforrás-kezelő a szabályozás |} A Microsoft Docs
description: Megtanulhatja, hogyan konfigurálhatja a szabályozások a Service Fabric fürt Resource Manager által biztosított.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b1824fc5f93bafb26650d28277054869a3659099
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731752"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>A Service Fabric-fürt Resource Manager-szabályozás
Akkor is, ha megfelelően konfigurálta a fürterőforrás-kezelő, a fürt is első szakadhat meg. Lehetnek például egyidejű csomópont és a tartalék tartomány meghibásodása – mi történne, amely történt a frissítés során? A fürterőforrás-kezelő mindig próbálja meg mindent, javítsa ki a fürt erőforrásokat próbál átrendezése, és javítsa ki a fürt. Szabályozások érdekében, hogy a fürt használhatja a kerülési - erőforrások, adjon meg egy backstop térjen vissza a csomópontok, a hálózati partíciókat javítása, a javított bits telepítve.

Az alábbi számos különféle helyzetekben érdekében a Service Fabric fürterőforrás-kezelő számos szabályozások tartalmaz. Ezek a szabályozások összes viszonylag nagy kalapácsok. Általában ezek nem módosítása nem gondos tervezéssel és tesztelése.

Ha módosítja a fürt Resource Manager szabályozások, azokat a várt tényleges terhelését kell hangolása. Szüksége lesz néhány szabályozások helyen, akkor is, ha azt jelenti, hogy a fürt több időt vesz igénybe kerülési bizonyos esetekben előfordulhat, hogy határozza meg. Tesztelésre szükség a szabályozások a megfelelő értékek meghatározásáról. Szabályozások kell lennie, elég nagy a fürt reagálni az igények változásaira ésszerű időn belül, és elég alacsony ténylegesen a túl sok erőforrás-használat megelőzése érdekében. 

Megtudtuk, hogy ügyfeleink az esetek többségében szabályozások, mert már egy erőforrás korlátozott környezet már használja. Néhány példa a csomópontokra, vagy a lemezen való létrehozásához több állapot-nyilvántartó replikák párhuzamosan a teljesítmény-korlátozások miatt nem korlátozott a sávszélesség lenne. Nélkül szabályozások a műveletek sikerült túlterhelhetik futó ezeket az erőforrásokat, sikertelen vagy lassú műveletek miatt. Ezekben a helyzetekben ügyfelek szabályozások használt, és tudtuk, hogy azok mennyi ideig voltak kiterjesztése vesz igénybe a fürt egy stabil állapotot elérni. Ügyfelek is ismert, végül sikerült alacsonyabb általános megbízhatóságot nyújt, fut, amíg azok szabályozva lett.


## <a name="configuring-the-throttles"></a>A szabályozások konfigurálása

A Service Fabric replika típusú áthelyezések száma szabályozási kétféle módszer van. Az alapértelmezett mechanizmus, amely létezett a Service Fabric 5.7 jelöli engedélyezett egy abszolút számát, szabályozás. Ez nem működik a fürtök, bármilyen méretűek. Különösen nagyobb fürtök esetében az alapértelmezett érték lehet túl kicsi, jelentősen terheléselosztás akkor is, ha szükség a kisebb fürtök hatálytalannak közben lelassítanunk. Ez a korábbi mechanizmus felül lett írva a százalékskálájú szabályozás, ami hatékonyabb méretezést biztosít, a dinamikus fürtökkel, amelyben a szolgáltatások és a csomópontok számát sűrűn változnak.

A szabályozások a replikákat a fürtök számának százalékos alapulnak. Százalékos alapú szabályozások engedélyezése a szabály kifejezése: "ne helyezze át a replikák több mint 10 % 10 percenként", például.

A százalékskálájú szabályozás konfigurációs beállításait a következők:

  - GlobalMovementThrottleThresholdPercentage - áthelyezések száma – fürt bármikor engedélyezett maximális száma a fürtben található replikák száma százalékában kifejezve. 0 azt jelzi, hogy nincs korlát. Az alapértelmezett érték a 0. Ha ez a beállítás és a GlobalMovementThrottleThreshold is meg van adva, a korlátozóbb korlát használatos.
  - GlobalMovementThrottleThresholdPercentageForPlacement - áthelyezések száma – a fürtben található replikák száma százalékában kifejezve az elhelyezési fázisban az engedélyezett maximális száma. 0 azt jelzi, hogy nincs korlát. Az alapértelmezett érték a 0. Ha ez a beállítás és a GlobalMovementThrottleThresholdForPlacement is meg van adva, a korlátozóbb korlát használatos.
  - GlobalMovementThrottleThresholdPercentageForBalancing - áthelyezések száma – a fürtben található replikák száma százalékában kifejezve a terheléselosztási fázis során engedélyezett maximális száma. 0 azt jelzi, hogy nincs korlát. Az alapértelmezett érték a 0. Ha ez a beállítás és a GlobalMovementThrottleThresholdForBalancing is meg van adva, a korlátozóbb korlát használatos.

A szabályozás százalékos megadásakor kell megadni 5 %-os 0,05 szerint. Ezek a szabályozások rendelkezik, amelyen időköz a GlobalMovementThrottleCountingInterval, másodpercben megadott.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban futó fürtök:

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

### <a name="default-count-based-throttles"></a>Alapértelmezett száma alapján szabályozások
Ezt az információt arra az esetre, régebbi fürttel rendelkezik, vagy továbbra is megőrzi ezeket a konfigurációkat a fürtök azóta frissített biztosítunk. Általában javasoljuk, hogy ezek a fenti százalékskálájú szabályozások cserélése. A százalékskálájú szabályozás alapértelmezés szerint le van tiltva, mivel az ezek a szabályozások marad, amíg le van tiltva, és a százalékskálájú szabályozások cserélni a fürt alapértelmezett szabályozások. 

  - GlobalMovementThrottleThreshold – ezzel a beállítással teljes száma a fürtben lévő áthelyezések száma – szabályozza a bizonyos idővel. Mennyi ideig van megadva, a GlobalMovementThrottleCountingInterval másodpercek alatt. A GlobalMovementThrottleThreshold alapértelmezett értéke 1000, és az alapértelmezett értéke a GlobalMovementThrottleCountingInterval 600.
  - MovementPerPartitionThrottleThreshold – ezzel a beállítással bármely szolgáltatás partíció áthelyezések száma szabályozza a bizonyos idővel. Mennyi ideig van megadva, a MovementPerPartitionThrottleCountingInterval másodpercek alatt. A MovementPerPartitionThrottleThreshold alapértelmezett értéke 50, és az alapértelmezett értéke a MovementPerPartitionThrottleCountingInterval 600.

Ezek a szabályozások konfigurációját, a szabályozás százalékskálájú azonos mintát követi.

## <a name="next-steps"></a>További lépések
- Ismerje meg hogyan a fürterőforrás-kezelő felügyeli, és elosztja a terhelést a fürtben, tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
- A fürterőforrás-kezelő leíró a fürt számos lehetőség áll. A velük kapcsolatos további információért tekintse meg a cikk a [leíró, Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)
