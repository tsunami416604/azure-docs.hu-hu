---
title: Mérőszámok és elhelyezési beállítások megadása
description: Ismerje meg, hogyan lehet leírni a Service Fabric-szolgáltatást metrikák, elhelyezési megkötések és egyéb elhelyezési szabályzatok megadásával.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610097"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Fürterőforrás-kezelő beállításainak konfigurálása a Service Fabric-szolgáltatásokhoz
A Service Fabric fürterőforrás-kezelő lehetővé teszi a részletes szabályozása a szabályokat, amelyek minden egyes megnevezett szolgáltatás. Minden elnevezett szolgáltatás megadhat szabályokat arra vonatkozóan, hogyan kell lefoglalni a fürtben. Minden elnevezett szolgáltatás is meghatározhatja a metrikák, amelyekjelenteni kívánt mérőszámok, beleértve, hogy mennyire fontosak az adott szolgáltatás számára. A szolgáltatások konfigurálása három különböző feladatra bontható:

1. Elhelyezési megkötések konfigurálása
2. Mérőszámok konfigurálása
3. Speciális elhelyezési házirendek és egyéb szabályok konfigurálása (kevésbé gyakori)

## <a name="placement-constraints"></a>Elhelyezési megkötések
Az elhelyezési megkötések annak szabályozására szolgálnak, hogy a fürt mely csomópontjain futtatható a szolgáltatás. Általában egy adott nevű szolgáltatáspéldány vagy egy adott típusú összes szolgáltatás, amely egy adott csomóponttípuson futtatva van. Az elhelyezési megkötések bővíthetők. A szolgáltatások létrehozásakor csomóponttípusonként tetszőleges tulajdonságkészletet megadhat, majd korlátozásokkal választhatja ki őket. A szolgáltatás elhelyezési korlátait futás közben is módosíthatja. Ez lehetővé teszi, hogy válaszoljon a fürtben vagy a szolgáltatás követelményeinek változásaira. Egy adott csomópont tulajdonságai is dinamikusan frissíthetők a fürtben. Az elhelyezési korlátozásokról és azok konfigurálásáról ebben a cikkben talál további [információt.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Mérőszámok
Metrikák az erőforrások, amelyek egy adott elnevezett szolgáltatás igényeinek. A szolgáltatás metrika konfigurációja tartalmazza, hogy az adott erőforrás ból az adott állapotalapú replika vagy állapotmentes példány a szolgáltatás alapértelmezés szerint mennyit használ fel. Metrikák is tartalmaz nak egy súlyt, amely jelzi, hogy mennyire fontos a kiegyensúlyozás, hogy a metrika az adott szolgáltatás, abban az esetben, kompromisszumok szükségesek.

## <a name="advanced-placement-rules"></a>Speciális elhelyezési szabályok
Vannak más típusú elhelyezési szabályok, amelyek kevésbé gyakori esetekben hasznosak. Néhány példa:
- Földrajzilag elosztott fürtökkel segítő kényszerek
- Bizonyos alkalmazásarchitektúrák

Az egyéb elhelyezési szabályok korrelációk vagy házirendek segítségével vannak konfigurálva.

## <a name="next-steps"></a>További lépések
- Metrikák, amelyek a Service Fabric fürterőforrás-kezelő kezeli a fürt ben a fogyasztás és a kapacitás. Ha többet szeretne megtudni a metrikákról és azok konfigurálásáról, olvassa el [ezt a cikket.](service-fabric-cluster-resource-manager-metrics.md)
- Az affinitás az egyik mód, amelyet konfigurálhat a szolgáltatásokhoz. Ez nem gyakori, de ha szüksége van [here](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) rá, itt megismerheti
- Számos különböző elhelyezési szabályok, amelyek konfigurálhatók a szolgáltatás további forgatókönyvek kezelésére. Ezekről a különböző elhelyezési irányelvekről [itt olvashat.](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Kezdje az elejétől, és [kapjon bevezetést a Szolgáltatásháló-fürt erőforrás-kezelőjébe](service-fabric-cluster-resource-manager-introduction.md)
- Ha meg szeretné tudni, hogy a fürterőforrás-kezelő hogyan kezeli és egyensúlyozza ki a terhelést a fürtben, olvassa el a [terhelés elosztásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, olvassa el ezt a cikket a [Service Fabric-fürt leírásáról](service-fabric-cluster-resource-manager-cluster-description.md)
