---
title: A Backup Center áttekintése
description: Ez a cikk áttekintést nyújt az Azure-hoz készült Backup Centerről.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 2857433e00a678603e30c8e5fc276020c4658f2e
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614282"
---
# <a name="overview-of-backup-center"></a>A Backup Center áttekintése

A Backup Center **egyetlen egységes felügyeleti élményt** nyújt az Azure-ban a nagyvállalatok számára a nagy méretű biztonsági másolatok szabályozására, figyelésére, üzemeltetésére és elemzésére. Ezért az Azure natív felügyeleti tapasztalataival összhangban van.

A Backup Center legfontosabb előnyei a következők:

* **Önálló ablaktábla a biztonsági másolatok kezeléséhez** – a Backup Center úgy lett kialakítva, hogy jól működjön a nagyméretű és az elosztott Azure-környezetek között. A Backup Center segítségével hatékonyan kezelheti a több számítási feladatra, tárolóra, előfizetésre, régióra és [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) -bérlőre kiterjedő biztonsági mentéseket.
* **DataSource-központú felügyelet** – a Backup Center olyan nézeteket és szűrőket biztosít, amelyek a biztonsági mentésben szereplő adatforrásokra (például virtuális gépekre és adatbázisokra) vannak központosítva. Ez lehetővé teszi az erőforrás-tulajdonos vagy a biztonsági mentési rendszergazda számára az elemek biztonsági mentésének figyelését és működését anélkül, hogy az adott elemre vonatkozó biztonsági mentésre kellene összpontosítani. Ennek a kialakításnak a kulcsfontosságú funkciója, hogy az adatforrás-specifikus tulajdonságok, például az adatforrás-előfizetés, az adatforrás-erőforráscsoport és az adatforrás-címkék alapján szűrheti a nézeteket. Ha például a szervezet a különböző részlegekhez tartozó virtuális gépekhez különböző címkéket rendel, akkor a Backup Center használatával szűrheti a biztonsági mentési információkat a mögöttes virtuális gépek címkéjén alapuló, a tár címkére való összpontosítás nélkül.
* **Csatlakoztatott élmények** – a Backup Center natív integrációt biztosít a meglévő Azure-szolgáltatásokhoz, amelyek lehetővé teszik a nagy méretekben történő felügyeletet. A Backup Center például a [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) élményt használja a biztonsági másolatok szabályozásához. Emellett [Azure-munkafüzeteket](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) és [Azure monitor naplókat](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) is használ, amelyek segítenek a biztonsági másolatok részletes jelentéseinek megtekintéséhez. Így nem kell új alapelveket megtanulnia a Backup Center által kínált változatos funkciók használatához.

## <a name="supported-scenarios"></a>Támogatott esetek

* A biztonsági mentési központ jelenleg támogatott az Azure-beli virtuális gépek biztonsági mentéséhez és a Azure Database for PostgreSQL Server biztonsági mentéshez.
* A támogatott és nem támogatott forgatókönyvek részletes listáját a [támogatási mátrixban](backup-center-support-matrix.md) találja.

## <a name="get-started"></a>Bevezetés

A Backup Center használatának megkezdéséhez keresse meg a Azure Portal **biztonsági mentési központot** , és navigáljon a **Backup Center (előzetes verzió)** irányítópultra.

![Backup Center – keresés](./media/backup-center-overview/backup-center-search.png)

Az első megjelenített képernyő az **Áttekintés**. Két csempét tartalmaz – a **feladatokat** és a **biztonsági mentési példányokat**.

![Backup Center csempéi](./media/backup-center-overview/backup-center-overview-widgets.png)

A **feladatok** csempén az elmúlt 24 órában az összes biztonsági mentési és visszaállítással kapcsolatos feladat összefoglaló nézete látható. A befejezett, sikertelen és folyamatban lévő feladatok számával kapcsolatos információkat is megtekintheti. A csempe bármelyik számának kiválasztásával további információkat tekinthet meg egy adott adatforrás-típussal, a művelet típusával és állapotával kapcsolatos feladatokkal kapcsolatban.

A **biztonsági mentési példányok** csempén megjelenik az összes biztonsági mentési példány összesített nézete a Backup-birtokon. Megtekintheti például a helyreállítható állapotú biztonságimásolat-példányok számát a védelemre beállított példányok számával szemben. A csempe bármelyik számának kiválasztásával további információkat tekinthet meg a biztonsági mentési példányokról egy adott adatforrás-típus és egy védelmi állapot tekintetében.

Tekintse meg a következő videót a Backup Center képességeinek megismeréséhez:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

A [következő lépésekkel](#next-steps) megismerheti a Backup Center által biztosított különböző képességeket, és hogy miként használhatja ezeket a képességeket a biztonsági mentési környezet hatékony kezeléséhez.

## <a name="next-steps"></a>További lépések

* [Biztonsági másolatok figyelése és üzemeltetése](backup-center-monitor-operate.md)
* [A biztonsági mentési hagyaték szabályozása](backup-center-govern-environment.md)
* [Bepillantást nyerhet a biztonsági másolatokra](backup-center-obtain-insights.md)
* [Műveletek végrehajtása a Backup Center használatával](backup-center-actions.md)
