---
title: Metrikák és elhelyezési beállításainak megadása az Azure Service Fabricben |} A Microsoft Docs
description: Ismerje meg, hogyan ismertetik a Service Fabric-szolgáltatás metrikákat, elhelyezési korlátozások és más elhelyezési házirendeket megadásával.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 55d75bb0bae4bf3feb370b7fdf4d80dc43bdc0ca
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736889"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Fürt resource manager beállításainak megadása a Service Fabric-szolgáltatások
A Service Fabric-fürt Resource Manager lehetővé teszi, hogy a szükséges megtennie minden vállalkozásnak nevű szolgáltatás meghatározó szabályok részletesen szabályozhatja. Minden elnevezett szolgáltatás hogyan azt kell kiosztani a fürt szabályokat adhat meg. Minden elnevezett szolgáltatás is meghatározhat jelentésre, beleértve az mennyire fontos vannak az adott szolgáltatásra szeretné metrikák készletét. Három különböző feladatokra szolgáltatások konfigurálása felszámolja:

1. Elhelyezési korlátozások konfigurálása
2. Metrikák konfigurálása
3. Speciális elhelyezési házirendeket és az egyéb szabályokkal (kevésbé gyakori) konfigurálása

## <a name="placement-constraints"></a>Elhelyezési korlátozások
Elhelyezési korlátozások segítségével szabályozhatja, hogy mely csomópontok a fürtben a szolgáltatás ténylegesen futhatnak. Általában egy adott nevű szolgáltatáspéldány vagy egy adott típusú korlátozott futtathatók csomópontot egy adott típusú összes szolgáltatást. Elhelyezési korlátozások bővíthető. Minden csomópont-típusonként tulajdonságai készletét határozza meg, és válassza ki azokat a megkötésekkel rendelkező szolgáltatások létrehozásakor. Egy szolgáltatás-elhelyezési megkötések futás közben is módosíthatja. Ez lehetővé teszi, hogy reagálni az igények változásaira, a fürt vagy a szolgáltatás követelményeinek. Egy adott csomópont a tulajdonságai dinamikusan is frissíthetők a fürtben. Az elhelyezési korlátozások és a konfigurálásukról további információt található [Ez a cikk](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Mérőszámok
Metrikák olyan erőforrásokat, amelyek egy adott névvel ellátott szolgáltatást kell. A szolgáltatás konfigurációja mennyi erőforrás minden egyes állapot-nyilvántartó replika vagy adott állapot nélküli szolgáltatáspéldány használ fel, alapértelmezés szerint tartalmaz. Metrikák is a súlyozás, amely jelzi, hogy mennyire fontos, hogy a metrika terheléselosztási van erre a szolgáltatásra, esetben kompromisszumokra lenne szükség.

## <a name="advanced-placement-rules"></a>Speciális elhelyezési szabályok
Elhelyezési szabályok, amelyek hasznosak lehetnek a kevésbé gyakori helyzetek más típusai is vannak. Néhány példa:
- Korlátok, amelyek segítik a földrajzilag elosztott fürtöket
- Bizonyos architektúrák

Más elhelyezési szabályok összefüggéseket vagy a szabályzatok vannak konfigurálva.

## <a name="next-steps"></a>További lépések
- Metrikák, hogyan kezeli a Service Fabric-fürt erőforrás-kezelő a használat és a kapacitás a fürtben. Metrikák és a konfigurálásukról kapcsolatos további információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)
- Kapcsolat egy módot konfigurálhatja a szolgáltatásokhoz. Nem gyakori, de ha szüksége lesz rá talál további információt, [Itt](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Nincsenek számos különböző elhelyezési szabályokat, a szolgáltatás további forgatókönyvek kezelése konfigurálható. Azt is megismerheti ezeket különböző elhelyezési házirendeket [Itt](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Elölről kezdődik, és [, a Service Fabric fürterőforrás-kezelő bemutatása](service-fabric-cluster-resource-manager-introduction.md)
- Ismerje meg hogyan a fürterőforrás-kezelő felügyeli, és elosztja a terhelést a fürtben, tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
- A fürterőforrás-kezelő leíró a fürt számos lehetőség áll. A velük kapcsolatos további információért tekintse meg a cikk a [leíró, Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)
