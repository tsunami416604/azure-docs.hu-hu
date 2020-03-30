---
title: Lekérdezéshatókör naplózása az Azure Monitor loganalytics szolgáltatásában | Microsoft dokumentumok
description: Az Azure Monitor Log Analytics naplószolgáltatásának hatókörét és időtartományát ismerteti.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249593"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Lekérdezéshatókör és időtartomány naplózása az Azure Monitor loganalytics szolgáltatásában
Amikor [naplólekérdezést](log-query-overview.md) futtat [az Azure Portalon a Log Analytics szolgáltatásban,](get-started-portal.md)a lekérdezés által kiértékelt adatok a hatókörtől és az időtartománytól függ. Ez a cikk ismerteti a hatókör és az időtartomány, és hogyan állíthatja be az egyes követelményektől függően. Azt is ismerteti a viselkedését a különböző típusú hatókörök.


## <a name="query-scope"></a>Lekérdezési hatókör
A lekérdezéshatókör határozza meg a lekérdezés által kiértékelt rekordokat. Ez általában tartalmazza az összes rekordot egy Log Analytics-munkaterület vagy Application Insights-alkalmazás. A Log Analytics azt is lehetővé teszi, hogy egy adott figyelt Azure-erőforrás hatókörét állítsa be. Ez lehetővé teszi, hogy egy erőforrás-tulajdonos csak az adataikra összpontosítson, még akkor is, ha az erőforrás több munkaterületre ír.

A hatókör mindig a Log Analytics ablak bal felső részén jelenik meg. Egy ikon jelzi, hogy a hatókör egy Log Analytics-munkaterület vagy egy Application Insights-alkalmazás. Nincs ikon jelzi egy másik Azure-erőforrás.

![Hatókör](media/scope/scope.png)

A hatókört a Log Analytics indításához használt módszer határozza meg, és bizonyos esetekben a hatókört kattintással módosíthatja. Az alábbi táblázat a használt hatókör különböző típusait és az egyes hatókörek különböző részleteit sorolja fel.

| Lekérdezési hatókör | A hatókörbe tartozó rekordok | Hogyan válasszuk ki | Hatókör módosítása |
|:---|:---|:---|:---|
| Log Analytics-munkaterület | A Log Analytics-munkaterület összes rekordja. | Válassza a **Naplók lehetőséget** az **Azure Monitor** menüben vagy a **Log Analytics-munkaterületek** menüben.  | A hatókör t bármely más erőforrástípusra módosíthatja. |
| Application Insights-alkalmazás | Az Application Insights alkalmazás összes rekordja. | Válassza az **Analytics** lehetőséget az Application Insights **áttekintése** lapján. | Csak egy másik Application Insights-alkalmazás hatókörét módosíthatja. |
| Erőforráscsoport | Az erőforráscsoport összes erőforrása által létrehozott rekordok. Több Log Analytics-munkaterületadatait is tartalmazhat. | Válassza az erőforráscsoport menü **Naplók parancsát.** | A hatókör nem módosítható.|
| Előfizetés | Az előfizetés összes erőforrása által létrehozott rekordok. Több Log Analytics-munkaterületadatait is tartalmazhat. | Válassza az előfizetés menü **Naplók parancsát.**   | A hatókör nem módosítható. |
| Egyéb Azure-erőforrások | Az erőforrás által létrehozott rekordok. Több Log Analytics-munkaterületadatait is tartalmazhat.  | Válassza az erőforrás menü **Naplók parancsát.**<br>VAGY<br>Válassza a **Naplók lehetőséget** az **Azure Monitor** menüben, majd válasszon ki egy új hatókört. | A hatókör csak ugyanarra az erőforrástípusra módosítható. |

### <a name="limitations-when-scoped-to-a-resource"></a>Erőforrásokra vonatkozó hatókörre vonatkozó korlátozások

Ha a lekérdezéshatókör egy Log Analytics-munkaterület vagy egy Application Insights-alkalmazás, a portál on és az összes lekérdezési parancs elérhető. Ha azonban hatóköre egy erőforráshoz tartozik, a portálon a következő beállítások nem érhetők el, mert egyetlen munkaterülethez vagy alkalmazáshoz vannak társítva:

- Mentés
- Lekérdezéskezelő
- Új riasztási szabály

A következő parancsok nem használhatók a lekérdezésben, ha hatókört használ egy erőforráshoz, mivel a lekérdezéshatókör már tartalmaz olyan munkaterületeket, amelyek az adott erőforrás vagy erőforráskészlet adatait tartalmazzák:

- [App](app-expression.md)
- [Munkaterület](workspace-expression.md)
 

## <a name="query-limits"></a>Lekérdezési korlátok
Előfordulhat, hogy üzleti követelmények egy Azure-erőforrás adatok írása több Log Analytics-munkaterületekre. A munkaterület nem kell ugyanabban a régióban, mint az erőforrás, és egyetlen munkaterület adatokat gyűjthet nek erőforrásokból a különböző régiókban.  

A hatókör erőforrásra vagy erőforráskészletre való beállítása a Log Analytics különösen hatékony funkciója, mivel lehetővé teszi az elosztott adatok automatikus összesítését egyetlen lekérdezésben. Jelentősen befolyásolhatja a teljesítményt, ha az adatokat több Azure-régió munkaterületeiről kell lekérni.

A Log Analytics segít megvédeni a több régió munkaterületeit átszelő lekérdezések túlzott terhelése ellen azáltal, hogy figyelmeztetést vagy hibát ad ki bizonyos számú régió használata esetén. A lekérdezés figyelmeztetést kap, ha a hatókör 5 vagy több régióban lévő munkaterületeket tartalmaz. továbbra is futni fog, de túl sok időt vehet igénybe.

![Lekérdezési figyelmeztetés](media/scope/query-warning.png)

A lekérdezés nem fut, ha a hatókör 20 vagy több régióban lévő munkaterületeket tartalmaz. Ebben az esetben a rendszer kéri, hogy csökkentse a munkaterületi területek számát, és próbálja meg újra futtatni a lekérdezést. A legördülő menü megjeleníti a lekérdezés hatókörében lévő összes régiót, és a lekérdezés ismételt futtatása előtt csökkentenie kell a régiók számát.

![A lekérdezés nem sikerült](media/scope/query-failed.png)


## <a name="time-range"></a>Időtartomány
Az időtartomány a lekérdezéshez a rekord létrehozása alapján kiértékelt rekordok készletét határozza meg. Ezt a munkaterület vagy alkalmazás minden rekordjának szabványos tulajdonsága határozza meg az alábbi táblázatban megadottak szerint.

| Hely | Tulajdonság |
|:---|:---|
| Log Analytics-munkaterület          | TimeGenerated |
| Application Insights-alkalmazás | időbélyeg     |

Állítsa be az időtartományt a Log Analytics ablak tetején lévő időválasztóból való kiválasztásával.  Kijelölhet egy előre definiált időszakot, vagy az **Egyéni** lehetőséget választva megadhat egy adott időtartományt.

![Időválasztó](media/scope/time-picker.png)

Ha olyan szűrőt állít be a lekérdezésben, amely a fenti táblázatban látható mértékadó időtulajdonságot használja, az időválasztó **beállítása a lekérdezésben**értékre változik, és az időválasztó le van tiltva. Ebben az esetben a leghatékonyabb, ha a szűrőt a lekérdezés tetejére helyezi, így minden későbbi feldolgozásnak csak a szűrt rekordokkal kell működnie.

![Szűrt lekérdezés](media/scope/query-filtered.png)

Ha a [munkaterület](workspace-expression.md) vagy az [alkalmazásparanccsal](app-expression.md) adatokat kér le egy másik munkaterületről vagy alkalmazásból, az időválasztó eltérően viselkedhet. Ha a hatókör egy Log Analytics-munkaterület, és **alkalmazást**használ, vagy ha a hatókör egy Application Insights-alkalmazás, és **munkaterületet**használ, akkor előfordulhat, hogy a Log Analytics nem érti, hogy a szűrőben használt tulajdonság határozza meg az időszűrőt.

A következő példában a hatókör egy Log Analytics-munkaterületre van beállítva.  A lekérdezés **munkaterület** segítségével olvassa be az adatokat egy másik Log Analytics-munkaterületről. Az időválasztó **a Beállítás a lekérdezésben** értékre változik, mert a várt **TimeGenerated** tulajdonságot használó szűrőt lát.

![Lekérdezés munkaterülettel](media/scope/query-workspace.png)

Ha a lekérdezés **alkalmazás** segítségével lekéri az adatokat egy Application Insights-alkalmazás mégis, log Analytics nem ismeri fel az **időbélyeg** tulajdonság a szűrőben, és az időválasztó változatlan marad. Ebben az esetben mindkét szűrő alkalmazása megigazul. A példában csak az elmúlt 24 órában létrehozott rekordok szerepelnek a lekérdezésben, annak ellenére, hogy 7 napot ad meg a **Where** záradékban.

![Lekérdezés alkalmazással](media/scope/query-app.png)

## <a name="next-steps"></a>További lépések

- A [Log Analytics azure-portálon való használatának oktatóanyaga.](get-started-portal.md)
- Séta a [bemutató írásban lekérdezések](get-started-queries.md).
