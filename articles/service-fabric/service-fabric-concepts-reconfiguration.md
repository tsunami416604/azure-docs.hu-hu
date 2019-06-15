---
title: Az Azure Service Fabric újrakonfigurálási |} A Microsoft Docs
description: A partíciók a Service Fabric újrakonfigurálási ismertetése
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60882197"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Az Azure Service Fabric újrakonfigurálási
A *konfigurációs* számít, ha a replikák és a egy partíció egy állapotalapú szolgáltatás szerepük.

A *újrakonfigurálás* egy konfigurációs áthelyezése egy másik konfigurációs folyamata. Ez módosítja a partíció egy állapotalapú szolgáltatás replikakészletének. A régi konfigurációt nevezzük a *korábbi konfigurációt (számítógép)* , és az új konfigurációt nevezzük a *aktuális konfigurációját (CC)* . Az Azure Service Fabric az újrakonfigurálás protokoll konzisztencia megőrzi, és fenntartja a rendelkezésre állási módosítania kellene a replikakészlethez során.

Feladatátvevőfürt-kezelő a rendszer különböző eseményekre reagáló reconfigurations kezdeményezi. Például ha az elsődleges nem tud majd egy újrakonfigurálás kezdeményezték egy aktív másodlagos elsődleges előléptetése. Egy másik példa esetén alkalmazásfrissítések válaszul annak érdekében, hogy frissítse a csomópontot az elsődleges áthelyezése másik csomópontra szükség lehet.

## <a name="reconfiguration-types"></a>Az újrakonfigurálás típusok
Reconfigurations két kategóriába sorolhatók:

- Ha megváltoztatja az elsődleges reconfigurations:
    - **Feladatátvétel**: Folyamatban lévő feladatátvételi teszteket reconfigurations válaszul a hiba a futó elsődleges.
    - **SwapPrimary**: Cseréje, ahol a Service Fabric kell áthelyezni egy futó elsődleges egyik csomópontról a másikra, a terheléselosztás általában válasz reconfigurations vagy a verziófrissítésre.

- Reconfigurations, ahol az elsődleges nem módosul.

## <a name="reconfiguration-phases"></a>Konfigurálás fázisai
Egy újrakonfigurálás onnantól több fázisban történik:

- **Phase0**: Ebben a fázisban történik, ha az aktuális elsődleges továbbítja az új elsődleges és a tér át, aktív másodlagos állapotában lapozófájl-kapacitás elsődleges reconfigurations.

- **Phase1**: Ebben a fázisban történik, ahol az elsődleges módosul reconfigurations során. Ebben a fázisban a Service Fabric azonosítja a megfelelő elsődleges az aktuális replika között. Ebben a fázisban nem szükséges lapozófájl-kapacitás elsődleges reconfigurations során, mert az új elsődleges már ki lett választva. 

- **Phase2**: Ebben a fázisban a Service Fabric biztosítja, hogy az összes adat érhető el a replikákat a jelenlegi konfiguráció a legtöbb.

Nincsenek számos más fázison megy keresztül, amelyek csak belső használatra.

## <a name="stuck-reconfigurations"></a>Elakadt reconfigurations
Beszerezheti a reconfigurations *elakadt* különböző okok miatt. A leggyakoribb okai a következők:

- **Replikák lefelé**: Néhány újrakonfigurálás fázisok, akár a konfigurációban a replikák többsége szükséges.
- **Hálózati vagy a kommunikációs problémák**: Reconfigurations csomópontjai közötti hálózati kapcsolat szükséges.
- **API-hibák**: Az újrakonfigurálás protokoll szükséges, hogy a szolgáltatásból Befejezés bizonyos API-k. Például egy reliable service-ben a megszakítás token nem érvényesítenie okoz SwapPrimary reconfigurations elakad.

Használja a rendszer összetevőinek rendszerállapot-jelentések, például System.FM, System.RA és System.RAP, diagnosztizálásához, ahol egy újrakonfigurálás elakadt. A [rendszer állapota jelentésoldal](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) ismerteti ezeket a rendszerállapot-jelentések.

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakról további információkért tekintse meg a következő cikkeket:

- [A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)
- [Rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md)
