---
title: A lekérdezés hatókörének naplózása Azure Monitor Log Analyticsban | Microsoft Docs
description: A Azure Monitor Log Analyticsban lévő napló lekérdezés hatókörét és időtartományát ismerteti.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357103"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>A lekérdezés hatókörének és időtartományának naplózása Azure Monitor Log Analytics
Ha [a Azure Portal log Analyticsban](get-started-portal.md)futtat [naplózási lekérdezést](log-query-overview.md) , a lekérdezés által kiértékelt adatok halmaza a hatókörtől és a kiválasztott időtartománytól függ. Ez a cikk a hatókört és az időtartományt ismerteti, valamint azt, hogyan állíthatja be az egyes igényektől függően. Emellett leírja a különböző típusú hatókörök viselkedését is.


## <a name="query-scope"></a>Lekérdezési hatókör
A lekérdezési hatókör határozza meg a lekérdezés által kiértékelt rekordokat. Ez általában egyetlen Log Analytics munkaterület vagy Application Insights alkalmazás összes rekordját tartalmazza. A Log Analytics lehetővé teszi egy adott figyelt Azure-erőforrás hatókörének beállítását is. Ez lehetővé teszi, hogy az erőforrás-tulajdonos csak a saját adatterületére koncentráljon, még akkor is, ha az adott erőforrás több munkaterületre ír.

A hatókör mindig a Log Analytics ablak bal felső részén jelenik meg. Az ikon azt jelzi, hogy a hatókör egy Log Analytics munkaterület vagy egy Application Insights alkalmazás. Nincs ikon egy másik Azure-erőforrást jelez.

![Hatókör](media/scope/scope.png)

A hatókört a Log Analytics elindításához használt módszer határozza meg, és bizonyos esetekben a hatókört a lehetőségre kattintva módosíthatja. A következő táblázat felsorolja a használt hatókör különböző típusait, valamint az egyes adatok különböző részleteit.

| Lekérdezési hatókör | A hatókörben lévő rekordok | Kiválasztás | Hatókör módosítása |
|:---|:---|:---|:---|
| Log Analytics-munkaterület | A Log Analytics munkaterület összes rekordja. | Válassza a **naplók** lehetőséget a **Azure monitor** menüben vagy a **log Analytics munkaterületek** menüből.  | A hatókört bármely más erőforrástípus esetében módosíthatja. |
| Application Insights alkalmazás | A Application Insights alkalmazás összes rekordja. | A Application Insights **Áttekintés** lapján válassza az **elemzés** lehetőséget. | A hatókört csak egy másik Application Insights alkalmazásra lehet módosítani. |
| Erőforráscsoport | Az erőforráscsoport összes erőforrása által létrehozott rekordok. Több Log Analytics munkaterületről származó adatok is szerepelhetnek. | Válassza ki a **naplókat** az erőforráscsoport menüjében. | A hatókör nem módosítható.|
| Előfizetést | Az előfizetésben található összes erőforrás által létrehozott rekordok. Több Log Analytics munkaterületről származó adatok is szerepelhetnek. | Válassza a **naplók** lehetőséget az előfizetés menüből.   | A hatókör nem módosítható. |
| Egyéb Azure-erőforrások | Az erőforrás által létrehozott rekordok. Több Log Analytics munkaterületről származó adatok is szerepelhetnek.  | Válassza ki a **naplók** elemet az erőforrás menüben.<br>VAGY<br>Válassza ki a **naplók** elemet a **Azure monitor** menüben, majd válasszon ki egy új hatókört. | A hatókör nem módosítható ugyanarra az erőforrás-típusra. |

### <a name="limitations-when-scoped-to-a-resource"></a>Erőforrásokra vonatkozó hatókörre vonatkozó korlátozások

Ha a lekérdezési hatókör egy Log Analytics munkaterület vagy egy Application Insights alkalmazás, a Portálon és az összes lekérdezési parancson belül minden lehetőség elérhető. Ha egy erőforrás hatóköre nem érhető el, a portálon az alábbi beállítások nem érhetők el, mert egyetlen munkaterülethez vagy alkalmazáshoz vannak társítva:

- Mentés
- Lekérdezési tallózó
- Új riasztási szabály

Az alábbi parancsok nem használhatók a lekérdezésekben, ha az erőforrás hatóköre, mivel a lekérdezés hatóköre már tartalmaz minden olyan munkaterületet, amely az adott erőforráshoz vagy erőforrás-készlethez tartozó adattal rendelkezik:

- [App](app-expression.md)
- [munkaterület](workspace-expression.md)
 

## <a name="query-limits"></a>Lekérdezési korlátok
Előfordulhat, hogy az Azure-erőforrásokra vonatkozó üzleti követelmények több Log Analytics munkaterületre is írhatnak adatbevitelt. A munkaterületnek nem kell ugyanabban a régióban lennie, mint az erőforrás, és egy adott munkaterület több régióban is gyűjthet adatokat az erőforrásokból.  

Ha a hatókört erőforrásra vagy erőforrás-készletre állítja be, a Log Analytics különösen hatékony funkciója, mivel lehetővé teszi, hogy egyetlen lekérdezésben automatikusan összevonja az elosztott adatmennyiséget. Jelentős hatással lehet a teljesítményre, ha az adatok több Azure-régióban lévő munkaterületekről kérhetők le.

Log Analytics segít megvédeni a több régióban munkaterületekre kiterjedő lekérdezések túlzott terhelését azáltal, hogy bizonyos számú régió használatakor figyelmeztetést vagy hibát ad ki. A lekérdezés figyelmeztetést küld, ha a hatókör 5 vagy több régióban tartalmaz munkaterületeket. Ez a művelet továbbra is futni fog, de a folyamat végrehajtása túl hosszú időt vehet igénybe.

![Lekérdezési figyelmeztetés](media/scope/query-warning.png)

A lekérdezés le lesz tiltva, ha a hatókör 20 vagy több régióban is tartalmaz munkaterületeket. Ebben az esetben a rendszer arra kéri, hogy csökkentse a munkaterület-régiók számát, és próbálkozzon újra a lekérdezés futtatásával. A legördülő lista az összes régiót megjeleníti a lekérdezés hatókörében, ezért csökkentse a régiók számát, mielőtt újra megpróbálja futtatni a lekérdezést.

![Sikertelen lekérdezés](media/scope/query-failed.png)


## <a name="time-range"></a>Időtartomány
Az időtartomány meghatározza a lekérdezés által a rekord létrehozásakor kiértékelt rekordok készletét. Ezt a rendszer a munkaterület vagy alkalmazás összes rekordján a következő táblázatban megadott módon definiálja: standard tulajdonság.

| Hely | Tulajdonság |
|:---|:---|
| Log Analytics-munkaterület          | TimeGenerated |
| Application Insights alkalmazás | időbélyeg     |

Állítsa be az időtartományt úgy, hogy kijelöli a Log Analytics ablak tetején található időválasztót.  Kiválaszthat egy előre meghatározott időszakot, vagy az **Egyéni** lehetőséget választva megadhat egy adott időtartományt.

![Időválasztó](media/scope/time-picker.png)

Ha olyan szűrőt állít be a lekérdezésben, amely a fenti táblázatban látható standard Time tulajdonságot használja, akkor az időválasztó megváltoztatja a **lekérdezésben beállított**időt, és az időválasztó le van tiltva. Ebben az esetben a leghatékonyabban a lekérdezés tetejére helyezheti a szűrőt, így minden további feldolgozásnak csak a szűrt rekordokkal kell dolgoznia.

![Szűrt lekérdezés](media/scope/query-filtered.png)

Ha egy másik munkaterületről vagy alkalmazásból kéri le az adatok lekéréséhez a [munkaterület](workspace-expression.md) vagy az [alkalmazás parancsát](app-expression.md) , az időválasztó eltérően működhet. Ha a hatókör egy Log Analytics munkaterület, és használja az **alkalmazást**, vagy ha a hatókör egy Application Insights alkalmazás, és Ön a **munkaterületet**használja, log Analytics előfordulhat, hogy nem érti, hogy a szűrőben használt tulajdonságnak meg kell határoznia az időszűrőt.

A következő példában a hatókör egy Log Analytics munkaterületre van beállítva.  A lekérdezés **munkaterületet** használ az adatok másik log Analytics munkaterületről való lekéréséhez. Az időválasztó a **lekérdezésben megadott** értékre változik, mert egy olyan szűrőt lát, amely a várt **TimeGenerated** tulajdonságot használja.

![Lekérdezés munkaterülettel](media/scope/query-workspace.png)

Ha a lekérdezés a Application Insights alkalmazásból származó adatok lekérésére használja az **alkalmazást** , log Analytics nem ismeri fel a szűrő **timestamp** tulajdonságát, és az időválasztó változatlan marad. Ebben az esetben mindkét szűrőt alkalmazza a rendszer. A példában csak az elmúlt 24 órában létrehozott rekordok szerepelnek a lekérdezésben, annak ellenére, hogy a **Where** záradékban 7 nap van megadva.

![Lekérdezés az alkalmazással](media/scope/query-app.png)

## <a name="next-steps"></a>Következő lépések

- [Útmutató a Azure Portal log Analyticsjának használatáról](get-started-portal.md).
- [Útmutató a lekérdezések írásához](get-started-queries.md).
