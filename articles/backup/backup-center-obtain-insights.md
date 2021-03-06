---
title: Bepillantást nyerhet a Backup Center használatával
description: Ismerje meg, hogyan elemezheti a korábbi trendeket, és mélyebb elemzéseket készíthet a biztonsági mentésekről a Backup Center használatával.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c0f687ee123abe2f95ad0d23a6fe302f4a5c22e1
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173749"
---
# <a name="obtain-insights-using-backup-center"></a>Bepillantást nyerhet a Backup Center használatával

A korábbi trendek elemzéséhez és a biztonsági másolatok mélyebb betekintéséhez a Backup Center egy felületet biztosít a [biztonsági mentési jelentésekhez](configure-reports.md), amely [Azure monitor naplókat](../azure-monitor/platform/data-platform-logs.md) és [Azure-munkafüzeteket](../azure-monitor/platform/workbooks-overview.md)használ. A biztonsági mentési jelentések a következő képességeket kínálja:

- A Felhőbeli tárhely kiosztása és előrejelzése.

- Biztonsági mentések és visszaállítások naplózása.

- A legfontosabb trendek azonosítása különböző részletességi szinteken.

- Átláthatóbb és betekintést nyerhet a biztonsági mentések költséghatékonysági lehetőségeibe.

## <a name="supported-scenarios"></a>Támogatott esetek

- A biztonsági mentési jelentések jelenleg nem érhetők el Azure Database for PostgreSQL kiszolgáló biztonsági mentéséhez.

- A támogatott és nem támogatott forgatókönyvek részletes listáját a [támogatási mátrixban](backup-center-support-matrix.md) találja.

## <a name="get-started"></a>Bevezetés

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>A tárolók konfigurálása Log Analytics-munkaterületre való adatküldéshez

[Ismerje meg, hogyan konfigurálhatja a diagnosztikai beállításokat a tárolók méretezéséhez](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Biztonsági mentési jelentések megtekintése a Backup Center portálon

A Backup Center **biztonsági mentési jelentések** menüpontjának kiválasztásával megnyílik a jelentések. Válasszon ki egy vagy több Log Analytics munkaterületet a biztonsági másolatok legfontosabb információinak megtekintéséhez és elemzéséhez.

![Biztonsági mentési jelentések a Backup Centerben](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

A következő nézetek érhetők el:

1. **Összefoglalás** – ezen a lapon áttekintheti a Backup-hagyaték magas szintű áttekintését. [További információ](./configure-reports.md#summary)

1. **Biztonsági másolati elemek** – ezen a lapon tekinthetők meg a biztonsági mentési elem szintjén felhasználható felhőalapú tárterület információi és trendjei. [További információ](./configure-reports.md#backup-items)

1. **Használat** – ezen a lapon megtekintheti a biztonsági másolatok legfontosabb számlázási paramétereit. [További információ](./configure-reports.md#usage)

1. **Feladatok** – ezen a lapon megtekintheti a feladatok hosszan futó trendjét, például a sikertelen feladatok számát napi bontásban, valamint a feladat meghibásodásának leggyakoribb okait. [További információ](./configure-reports.md#jobs)

1. **Házirendek** – ezen a lapon megtekintheti az összes aktív házirend adatait, például a társított elemek számát, valamint az adott szabályzatban biztonsági mentés alatt álló elemek által felhasznált teljes felhőalapú tárterületet. [További információ](./configure-reports.md#policies)

1. **Optimalizálás** – ezen a lapon betekintést nyerhet a biztonsági mentések lehetséges költségtakarékos lehetőségeibe. [További információ](./configure-reports.md#optimize)

## <a name="next-steps"></a>Következő lépések

- [Biztonsági másolatok figyelése és üzemeltetése](backup-center-monitor-operate.md)
- [A biztonsági mentési hagyaték szabályozása](backup-center-govern-environment.md)
- [Műveletek végrehajtása a Backup Center használatával](backup-center-actions.md)