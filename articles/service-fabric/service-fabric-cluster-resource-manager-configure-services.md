---
title: "Metrikák és elhelyezési beállításainak megadása az Azure mikroszolgáltatások |} Microsoft Docs"
description: "A Service Fabric-szolgáltatás leíró metrikákat, placement Constraints korlátozásokat és egyéb elhelyezési házirendeket megadásával."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0ae4e874d0fd0922295a4ec7ad719a0a1fb108c8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>A Service Fabric-szolgáltatások fürt resource manager beállításainak konfigurálása
A Service Fabric fürt erőforrás-kezelő lehetővé teszi, hogy a minden részletre kiterjedő szabályozhatják a minden személy nevű szolgáltatás meghatározó szabályok. Minden elnevezett szolgáltatás hogyan azt kell kiosztani a fürt szabályokat adhat meg. Minden elnevezett szolgáltatás is meghatározhatók szeretnének metrikák készletét a jelentéshez, beleértve a fontos hogyan vannak az adott szolgáltatáshoz. A három különböző feladatok szolgáltatások konfigurálása megszakad:

1. Egy elhelyezési korlátozás konfigurálása
2. Metrikák konfigurálása
3. Speciális elhelyezési házirendeket és az egyéb szabályokkal (kevésbé közös) konfigurálása

## <a name="placement-constraints"></a>Egy elhelyezési korlátozás
Egy elhelyezési korlátozás segítségével szabályozhatja, hogy mely csomópontok a fürtben a szolgáltatás ténylegesen futtatható. Általában egy adott nevű szolgáltatáspéldány vagy egy adott típusú korlátozott futtatásához egy adott típusú csomópont összes szolgáltatás. Egy elhelyezési korlátozás is kiterjeszthető. Minden csomópont típusonkénti tulajdonságok a kulcstulajdonságokat határozza meg, és válassza ki azokat a-korlátozásokkal rendelkező szolgáltatások létrehozásakor. A szolgáltatás egy elhelyezési korlátozás futása közben is módosíthatja. Ez lehetővé teszi, hogy a fürt vagy a szolgáltatás követelményeinek változásait. Egy adott csomópont a Tulajdonságok dinamikusan is frissíthetők a fürt. Egy elhelyezési korlátozás és konfigurálásukról további információt található [Ez a cikk](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Mérőszámok
Adatok gyűjtése le egy adott névvel ellátott szolgáltatást igénylő erőforrások készletét. A szolgáltatás konfigurációja mennyi erőforrás minden egyes állapot-nyilvántartó replika vagy állapotmentes szolgáltatáspéldány használ fel, alapértelmezés szerint tartalmazza. Metrikák is, amely meghatározza, hogy mennyire fontos, hogy a metrika terheléselosztás van az adott szolgáltatáshoz, esetben mellékhatásokkal szükséges súlyt.

## <a name="advanced-placement-rules"></a>Speciális elhelyezési szabályokat
Nincsenek más típusú elhelyezési szabályokat, amelyek hasznosak lehetnek a kisebb gyakori forgatókönyvet. Néhány példa:
- Földrajzilag elosztott fürtöket segítenek megkötések
- Egyes alkalmazás-architektúra

Más elhelyezési szabályokat korrelációk és házirendek úgy vannak konfigurálva.

## <a name="next-steps"></a>További lépések
- Adatok gyűjtése le hogyan kezeli a Service Fabric fürt erőforrás-kezelő a használati és a fürt teljes kapacitását. A metrikák és konfigurálásuk módját kapcsolatos további tudnivalókért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)
- Kapcsolat a szolgáltatások a állíthatja be az egyik mód. Nincs közös, de ha esetleg szükség lenne rá megismerheti az [Itt](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Nincsenek sok különböző elhelyezési szabályokat, amelyek a szolgáltatás nem tudja kezelni a további helyzeteket is konfigurálhatja. Azt is megtudhatja, azokat a különböző elhelyezési házirendeket kapcsolatos [Itt](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Indítsa el az elejétől és [Bevezetés a Service Fabric fürt Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Hogyan kezeli a fürt erőforrás-kezelő, és elosztja a terhelést a fürt kapcsolatos további tudnivalókért tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
- A fürt erőforrás-kezelő rendelkezik a fürt leíró számos lehetőséget. További információkért róluk, tekintse meg a cikk a [leíró a Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)
