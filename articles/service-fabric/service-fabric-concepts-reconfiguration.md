---
title: "Az Azure Service Fabric újrakonfigurálás |} Microsoft Docs"
description: "A Service Fabric partíció újrakonfigurálása ismertetése"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.openlocfilehash: ee2808286012720210070817d7ee6c2349247858
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Az Azure Service Fabric újrakonfigurálása
A *konfigurációs* van definiálva, a replikákat és a partíció egy állapotalapú szolgáltatás szerepkörét.

A *újrakonfigurálás* egy konfigurációs áthelyezése egy másik konfigurációs folyamata. Az módosítja a partíció egy állapotalapú szolgáltatás replikakészletének. A régi konfigurációs elnevezése a *előző konfigurációt (számítógép)*, és az új konfiguráció elnevezése a *aktuális konfigurációja (CC)*. Az Azure Service Fabric-újrakonfigurálás protokoll konzisztencia megőrzi, és rendelkezésre állási fenntartja a replikakészlethez módosításokat során.

Feladatátvevőfürt-kezelő különböző eseményekre válaszként újrakonfigurálás indít el a rendszerben. Például ha az elsődleges nem tud majd egy újrakonfigurálás előléptetni egy aktív másodlagos egy másik elsődleges lehet kezdeményezni. Egy másik példa esetén adott válasz alkalmazásfrissítések az elsődleges áthelyezése egy másik csomópontra a csomópont frissítéséhez szükség lehet.

## <a name="reconfiguration-types"></a>Újrakonfigurálás típusok
Újrakonfigurálás két kategóriába sorolhatók:

- Ha az elsődleges jal újrakonfigurálás:
    - **Feladatátvételi**: feladatátvételek újrakonfigurálás válaszként a sikertelen a futó elsődleges.
    - **SwapPrimary**: swap, ahol a Service Fabric kell helyeznie egy futó elsődleges egyik csomópontról egy másikra, általában a válasz a(z) terheléselosztást újrakonfigurálás vagy a verziófrissítésre.

- Ha az elsődleges nem módosítása újrakonfigurálás.

## <a name="reconfiguration-phases"></a>Újrakonfigurálás fázisok
Egy újrakonfigurálás halad több lépésből áll:

- **Phase0**: Ebben a fázisban történik, ha az aktuális elsődleges átviszi a az új elsődleges és a tér át aktív másodlagos swap elsődleges újrakonfigurálás.

- **Phase1**: Ebben a fázisban történik, ha az elsődleges jal újrakonfigurálás alatt. Ebben a fázisban a Service Fabric azonosítja a megfelelő elsődleges az aktuális replika között. Ebben a fázisban nem a lapozófájl-kapacitás elsődleges újrakonfigurálás alatt van szükség, mert az új elsődleges már ki lett választva. 

- **Phase2**: Ebben a fázisban a Service Fabric biztosítja, hogy minden adatot az aktuális konfiguráció a replikák többsége érhető el.

Nincsenek más fázisok csak belső használatra szolgálnak.

## <a name="stuck-reconfigurations"></a>Rögzített újrakonfigurálás
Újrakonfigurálás kaphat *akadt* a számos okból. A gyakori okai a következők:

- **Replikák le**: néhány újrakonfigurálás fázisok fel kell a konfigurációban a replikák többsége szükséges.
- **Hálózati vagy kommunikációs problémák**: újrakonfigurálás csomópontjai közötti hálózati kapcsolat szükséges.
- **Sikertelen API**: az újrakonfigurálás protokoll megköveteli, hogy a szolgáltatás megvalósítások Befejezés bizonyos API-k. Például a megszakítási lexikális elem szerepel a megbízható szolgáltatás nem érvényesítenie azt eredményezi, hogy elakadnak SwapPrimary újrakonfigurálás.

Használja a rendszer összetevőkből állapotjelentések, például System.FM System.RA vagy System.RAP, ahol egy újrakonfigurálás diagnosztizálásához Beragadt. A [rendszer állapotfigyelő jelentés oldalról](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) ezek állapotjelentések ismerteti.

## <a name="next-steps"></a>Következő lépések
A Service Fabric fogalmakat további információkért tekintse meg a következő cikkeket:

- [A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)
- [Rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md)
