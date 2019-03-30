---
title: Service Fabric-szolgáltatások rendelkezésre állása |} A Microsoft Docs
description: Hibák észlelése, a feladatátvétel és a recovery Services ismerteti
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: dd10af0d3c8a57168a27a039286ea0ec4c1dad02
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662745"
---
# <a name="availability-of-service-fabric-services"></a>Service Fabric-szolgáltatások rendelkezésre állása
Ez a cikk áttekintést nyújt, hogyan kezeli az Azure Service Fabric a egy szolgáltatás rendelkezésre állását.

## <a name="availability-of-service-fabric-stateless-services"></a>A Service Fabric állapotmentes szolgáltatások rendelkezésre állása
Service Fabric-szolgáltatások lehet állapot nélküli vagy állapotalapú. Állapotmentes szolgáltatás egy alkalmazás szolgáltatása, amely nem rendelkezik egy [helyi állapotot](service-fabric-concepts-state.md) , kell lennie a magas rendelkezésre álló vagy megbízható.

Állapotmentes szolgáltatás létrehozásához szükséges meghatározása egy `InstanceCount`. A példányok száma határozza meg, amely futnia kell a fürtben lévő alkalmazáslogika az állapotmentes szolgáltatás példányainak számát. Példányok számának növelése az állapotmentes szolgáltatás kiterjesztése ajánlott módja.

Ha nem sikerül egy állapot nélküli nevű-szolgáltatás egy példányát, egy új példányt egy jogosult csomóponton a fürtben jön létre. Például állapotmentes szolgáltatáspéldány sikertelen lehet a csomópont1, és újra létrehozni a Csomópont5 számítógépre.

## <a name="availability-of-service-fabric-stateful-services"></a>A Service Fabric állapotalapú szolgáltatások rendelkezésre állása
Az állapotalapú szolgáltatások egy hozzá társított állapottal rendelkezik. A Service Fabric az állapotalapú szolgáltatások replikák csoportként van modellezve. Minden egyes replikának egy futó példányával, a kód a szolgáltatás. A replika is tartalmaz az állam, hogy a szolgáltatás egy példányát. Olvasási és írási műveleteket egy replika nevű, a *elsődleges*. Állapot módosítása írási műveletek a következők *replikált* a többi replikához a replika, nevű *aktív másodlagos példány hozható létre*, és a alkalmazni. 

Csak egy elsődleges replika is lehet, de több aktív másodlagos replika is lehet. Aktív másodlagos replikák száma konfigurálható, és replikák megnövelt számú tűri egyidejű szoftverek és a hardveres hibák esetén nagyobb számú.

Ha az elsődleges replika leáll, a Service Fabric biztosítja, aktív másodlagos replikára az új elsődleges replika. Az aktív másodlagos replika már rendelkezik az állapot, a frissített verzió keresztül *replikációs*, és további olvasási és írási műveletek feldolgozása továbbra is. Ez a folyamat más néven *újrakonfigurálás* , és az erről a [újrakonfigurálás](service-fabric-concepts-reconfiguration.md) cikk.

Folyamatban van egy elsődleges vagy másodlagos aktív replika fogalma más néven a *replika szerepkör*. Ezek a replikák olyan további a [replikák és példányok](service-fabric-concepts-replica-lifecycle.md) cikk. 

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakról további információkért tekintse meg a következő cikkeket:

- [Service Fabric-szolgáltatások méretezése](service-fabric-concepts-scalability.md)
- [Service Fabric-szolgáltatások particionálása](service-fabric-concepts-partitioning.md)
- [Állapot definiálása és kezelése](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

