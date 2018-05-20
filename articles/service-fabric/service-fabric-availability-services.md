---
title: A Service Fabric-szolgáltatások rendelkezésre állásának |} Microsoft Docs
description: Ismerteti a hiba észlelése, a feladatátvételt és a helyreállítási szolgáltatások
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0794c0e190ecbc4cce808e94f98bb0ac63d1075a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="availability-of-service-fabric-services"></a>A Service Fabric-szolgáltatások rendelkezésre állása
Ez a cikk áttekintést hogyan kezeli az Azure Service Fabric a szolgáltatás rendelkezésre állását.

## <a name="availability-of-service-fabric-stateless-services"></a>A Service Fabric állapotmentes szolgáltatások rendelkezésre állása
A Service Fabric szolgáltatások állapot-nyilvántartó vagy állapotmentes lehet. Egy állapotmentes szolgáltatások egy olyan alkalmazás-szolgáltatás, amely nem rendelkezik egy [helyi állapot](service-fabric-concepts-state.md) , kell lennie a magas rendelkezésre állású vagy megbízható.

Állapot nélküli szolgáltatás létrehozásához szükséges meghatározása egy `InstanceCount`. A példányok száma határozza meg, hogy a fürt futnia kell az állapotmentes szolgáltatások alkalmazáslogikát példányainak száma. Az ajánlott módszer az állapot nélküli szolgáltatás kiterjesztése példányok számának növelése.

Ha az állapot nélküli nevű-szolgáltatás példányának nem sikerül, új példányt egy jogosult a fürt csomópontjain jön létre. Állapot nélküli szolgáltatáspéldány például az 1. csomópont sikertelen lehet, és újra létrehozni a Csomópont5 számítógépre.

## <a name="availability-of-service-fabric-stateful-services"></a>A Service Fabric állapotalapú szolgáltatások rendelkezésre állása
Az állapotalapú szolgáltatás a vele társított állapotban vannak. A Service Fabric egy állapotalapú szolgáltatás replikák készletként van modellezve. Minden egyes replikának a kód a szolgáltatás egy futó példány. A replika is rendelkezik, hogy a szolgáltatás állapotát egy példányával. Olvasási és írási műveleteket egy másodpéldány nevű, a *elsődleges*. Az állapot változásainak írási műveletek esetében *replikált* a többi replikához a replika, úgynevezett *aktív másodlagos*, és alkalmazza. 

Csak egy elsődleges replika lehet, de több aktív másodlagos replika lehet. Aktív másodlagos replikák száma konfigurálható, és a replikák nagyobb számú tűri nagyobb száma párhuzamos szoftver- és hardver meghibásodása.

Ha az elsődleges másodpéldány leáll, a Service Fabric teszi egyik aktív másodlagos replikán az új elsődleges replika. Ez a aktív másodlagos másodpéldány már rendelkezik az állapotát, a frissített verzió keresztül *replikációs*, és feldolgozási további olvasási/írási műveletek folytathatók. Ezt a folyamatot nevezik *újrakonfigurálás* és a további leírása a [újrakonfigurálás](service-fabric-concepts-reconfiguration.md) cikk.

Folyamatban egy elsődleges vagy másodlagos aktív replika fogalma is ismert, a *másodpéldányi szerepköre*. Ezekre a replikákra részelemcímkék ismertetését további a [replikák és példányok](service-fabric-concepts-replica-lifecycle.md) cikk. 

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakat további információkért tekintse meg a következő cikkeket:

- [A Service Fabric szolgáltatások méretezése](service-fabric-concepts-scalability.md)
- [A Service Fabric szolgáltatások particionálás](service-fabric-concepts-partitioning.md)
- [Létrehozásához és kezeléséhez állapota](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

