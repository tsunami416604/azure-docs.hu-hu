---
title: Újrakonfigurálás az Azure Service Fabricben
description: Ismerje meg az állapotalapú szolgáltatásreplikák konfigurációit és a Service Fabric újrakonfigurálásának folyamatát a konzisztencia és a rendelkezésre állás karbantartása érdekében a módosítás során.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609995"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Újrakonfigurálás az Azure Service Fabricben
A *konfiguráció* a replikák és azok szerepkörei egy állapotalapú szolgáltatás partícióját.

Az *újrakonfigurálás* az a folyamat, amelynek során az egyik konfigurációt áthelyezik egy másik konfigurációba. Módosítja a replikák készletét egy állapotalapú szolgáltatás partíciójára. A régi konfigurációt az *előző konfigurációnak (PC)* nevezik, az új at az *aktuális konfigurációnak (CC)* pedig. Az Azure Service Fabric újrakonfigurálási protokollja megőrzi a konzisztenciát, és fenntartja a rendelkezésre állást a replikakészlet módosításai során.

A feladatátvevő-kezelő a rendszer különböző eseményeire adott válaszként újrakonfigurálja a konfigurációkat. Ha például az elsődleges sikertelen, majd újrakonfigurálás indul egy aktív másodlagos elsődleges előléptetése érdekében. Egy másik példa az alkalmazásfrissítésekre adott válasz, amikor szükség lehet az elsődleges másik csomópontra való áthelyezésére a csomópont frissítéséhez.

## <a name="reconfiguration-types"></a>Újrakonfigurálási típusok
Az újrakonfigurálások két kategóriába sorolhatók:

- Újrakonfigurálás, ahol az elsődleges változik:
    - **Feladatátvétel:** A feladatátvétel egy futó elsődleges hiba miatt újrakonfigurálás.
    - **SwapPrimary**: Swaps olyan újrakonfigurációk, ahol a Service Fabric kell mozgatni a futó elsődleges egyik csomópontról a másikra, általában válaszul a terheléselosztás vagy a frissítés.

- Újrakonfigurálások, ahol az elsődleges nem változik.

## <a name="reconfiguration-phases"></a>Újrakonfigurálási fázisok
Az újrakonfigurálás több fázisban folytatódik:

- **0. fázis:** Ez a fázis a swap-elsődleges újrakonfigurálásokban történik, ahol az aktuális elsődleges átviszi az állapotát az új elsődleges és az aktív másodlagos átmenetek.

- **1. fázis:** Ez a fázis olyan újrakonfigurálások során történik, ahol az elsődleges változik. Ebben a fázisban a Service Fabric azonosítja a megfelelő elsődleges az aktuális replikák között. Erre a fázisra nincs szükség a swap-elsődleges újrakonfigurálások során, mert az új elsődleges már ki lett választva. 

- **2. fázis:** Ebben a fázisban a Service Fabric biztosítja, hogy az összes adat elérhető az aktuális konfiguráció replikáinak többségében.

Számos más fázis, amelyek csak belső használatra.

## <a name="stuck-reconfigurations"></a>Beragadt újrakonfigurálások
Az újrakonfigurálások számos okból *elakadhatnak.* Néhány a közös okok közé tartozik:

- **Replikák le:** Egyes újrakonfigurálási fázisok a konfigurációban lévő replikák többségének fel kell lépnie.
- **Hálózati vagy kommunikációs problémák**: Az újrakonfiguráláshoz hálózati kapcsolat ra van szükség a különböző csomópontok között.
- **API-hibák**: Az újrakonfigurálási protokoll megköveteli, hogy a szolgáltatás megvalósításai befejeznek bizonyos API-kat. Például ha nem tartja tiszteletben a törlési jogkivonatot egy megbízható szolgáltatásban, akkor a SwapPrimary újrakonfigurálása elakad.

A rendszerösszetevőkből , például a System.FM, a System.RA és a System.RAP fájlból származó állapotjelentések segítségével diagnosztizálhatja, hogy hol ragadt be az újrakonfigurálás. A [rendszerállapot-jelentés lap](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) ezeket az állapotjelentéseket ismerteti.

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmairól az alábbi cikkekben talál további információt:

- [A Reliable Services életciklusa – C#](service-fabric-reliable-services-lifecycle.md)
- [Rendszerállapot-jelentések](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Replikák és példányok](service-fabric-concepts-replica-lifecycle.md)
