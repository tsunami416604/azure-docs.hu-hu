---
title: Az Azure Monitor Log Analytics naplóbeli lekérdezés hatókörébe |} A Microsoft Docs
description: Ismerteti az Azure Monitor Log Analytics naplózási lekérdezés a hatókör és az időtartományt.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297193"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Az Azure Monitor Log Analytics naplóbeli lekérdezési hatókör és az időtartományt
Ha futtatja egy [naplólekérdezés](log-query-overview.md) a [Log Analytics az Azure Portalon](get-started-portal.md), az adatkészlethez tartozó értékelik ki, a lekérdezés attól függ, a hatókör és a kiválasztott időtartományban. Ez a cikk ismerteti a hatókör és az időtartományt, és hogyan beállíthatja egyes követelményektől függően. Emellett a hatókörök különböző típusú működését mutatja be.


## <a name="query-scope"></a>Lekérdezés hatókörébe
A lekérdezés hatókörébe a rekordokat, a lekérdezés által kiértékelt határozza meg. Ez általában tartalmazza minden rekord egy egyetlen Log Analytics-munkaterületbe vagy az Application Insights alkalmazásban. A log Analytics lehetővé teszi egy adott felügyelt Azure-beli erőforráshoz egy hatókör megadása. Ez lehetővé teszi az erőforrás tulajdonosa csak az adataikat, összpontosíthat, akkor is, ha több munkaterülettel ír ennek az erőforrásnak.

A hatókör mindig tetején megjelenik a Log Analytics ablak bal. Egy ikon azt jelzi, hogy a hatókör egy Log Analytics-munkaterület és Application Insights alkalmazásokkal. Nincs ikon azt jelzi, hogy egy másik Azure-erőforrás.

![Scope](media/scope/scope.png)

A hatókört a metódus határozza meg a Log Analytics szeretne használni, és bizonyos esetekben módosíthatja a hatókör kattintson rá. A következő táblázat felsorolja a különböző típusú használt hatókörrel és az egyes eltérő adatokkal.

| Lekérdezés hatókörébe | Rekordok hatókörében | Kiválasztása | Hatókör módosítása |
|:---|:---|:---|:---|
| Log Analytics-munkaterület | A Log Analytics-munkaterület összes rekordja. | Válassza ki **naplók** származó a **Azure Monitor** menüben vagy a **Log Analytics-munkaterületek** menü.  | Módosíthatja bármely erőforrástípussal hatókör. |
| Application Insights-alkalmazás | Az Application Insights-alkalmazás összes rekordja. | Válassza ki **Analytics** a **áttekintése** oldal az Application Insights. | Az Application Insights egy másik alkalmazás hatókör csak módosíthatja. |
| Erőforráscsoport | Az erőforráscsoport összes erőforrásának által létrehozott bejegyzéseket. Több Log Analytics-munkaterületek adatait tartalmazhatják. | Válassza ki **naplók** erőforrás csoport menüből. | Hatókör nem módosítható.|
| Előfizetés | Az előfizetésben található erőforrások által létrehozott bejegyzések. Több Log Analytics-munkaterületek adatait tartalmazhatják. | Válassza ki **naplók** az előfizetés menüből.   | Hatókör nem módosítható. |
| Egyéb Azure-erőforrások | Az erőforrás által létrehozott bejegyzéseket. Több Log Analytics-munkaterületek adatait tartalmazhatják.  | Válassza ki **naplók** az erőforrás menüben.<br>VAGY<br>Válassza ki **naplók** származó a **Azure Monitor** menüben, és válassza ki az új hatókör. | Azonos erőforrástípus hatókör csak módosíthatja. |

### <a name="limitations-when-scoped-to-a-resource"></a>Egy erőforrás hatóköre vonatkozó korlátozások

Ha a lekérdezés hatókörébe Log Analytics-munkaterület és Application Insights alkalmazásokkal, a portálon minden beállítások és az összes lekérdezés parancsainak érhetők el. Azonban hatókörrel rendelkező erőforráshoz, amikor az alábbi beállítások a portálon nem érhető el mivel korábban már egy egyetlen munkaterület vagy egy alkalmazás társítva:

- Mentés
- Lekérdezéskezelő
- Új riasztási szabály

A lekérdezésben, ha hatókörrel rendelkező erőforráshoz, mivel a lekérdezés hatókörébe már tartalmazza azokat a munkaterületeket, az adott erőforrás vagy erőforráscsoport adatokkal nem használható a következő parancsokat:

- [app](app-expression.md)
- [Munkaterület](workspace-expression.md)
 


## <a name="time-range"></a>Időtartomány
Az időtartomány rekordját, amely értékeli ki a lekérdezés alapján a rekord létrehozásakor megadja. Ez a munkaterület vagy az alkalmazásban az alábbi táblázatban megadott minden rekord standard tulajdonsága határozza meg.

| Location egység | Tulajdonság |
|:---|:---|
| Log Analytics-munkaterület          | TimeGenerated |
| Application Insights-alkalmazás | timestamp     |

Állítsa az időtartományt válassza ki azt a időválasztó a Log Analytics-ablak tetején.  Jelöljön ki egy előre meghatározott időszakot, vagy válasszon **egyéni** , adjon meg egy adott időtartományt.

![Időpontválasztó](media/scope/time-picker.png)

Ha egy szűrőt a lekérdezésben, amely a téli idő tulajdonságot használja, a fenti táblázatban látható módon, a időválasztó változik **beállítás a lekérdezésben**, és a időválasztó le van tiltva. Ebben az esetben célszerű a lehető leghatékonyabb szűrőt szeretne beállítani a felső részén a lekérdezést úgy, hogy bármilyen további feldolgozás csak kell dolgoznia a szűrt rekordok.

![Szűrt lekérdezési](media/scope/query-filtered.png)

Ha használja a [munkaterület](workspace-expression.md) vagy [alkalmazás](app-expression.md) adatokat lekérni egy másik munkaterületet, vagy alkalmazás paranccsal, a időválasztó viselkedése rendellenes lehet. Ha a hatókör egy Log Analytics-munkaterületet, és használható **alkalmazás**, vagy ha a hatókör egy Application Insights alkalmazást, és használható **munkaterület**, előfordulhat, hogy a Log Analytics nem értelmezhető az, hogy a tulajdonság szerepel majd a szűrő kell meghatározni, hogy az Időszűrő.

A következő példában a hatókör értéke egy Log Analytics-munkaterületet.  A lekérdezés **munkaterület** adatokat lekérni egy másik Log Analytics-munkaterületet. Változik a időválasztó **beállítás a lekérdezésben** , mert egy szűrő, amely a várt állapottáblázatban **TimeGenerated** tulajdonság.

![A munkaterület lekérdezése](media/scope/query-workspace.png)

Ha a lekérdezés **alkalmazás** adatokat lekérni egy Application Insights alkalmazást –, a Log Analytics nem ismeri fel a **időbélyeg** tulajdonságot a filter és a időválasztó változatlan marad. Ebben az esetben is alkalmazza a rendszer. A példában csak az elmúlt 24 órában létrehozott szereplő rekordok a lekérdezés még akkor is, ha azt adja meg a 7 nap a **ahol** záradékban.

![Az alkalmazás lekérdezése](media/scope/query-app.png)

## <a name="next-steps"></a>További lépések

- Végig egy [a Log Analytics szolgáltatást az Azure Portal oktatóanyag](get-started-portal.md).
- Végig egy [oktatóanyag lekérdezések írásáról](get-started-queries.md).