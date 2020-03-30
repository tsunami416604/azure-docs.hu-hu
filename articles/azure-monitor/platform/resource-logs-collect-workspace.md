---
title: Azure-erőforrásnaplók gyűjtése a Log Analytics-munkaterületen
description: Megtudhatja, hogyan streamelheti az Azure-erőforrásnaplókat egy Log Analytics-munkaterületre az Azure Monitorban.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 36bd464624118b7671a3879bcc1d34114bba9ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248592"
---
# <a name="collect-azure-platform-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure platformnaplók gyűjtése a Log Analytics-munkaterületen az Azure Monitorban
Az Azure-beli [platformnaplók,](platform-logs-overview.md) beleértve az Azure-tevékenységnaplót és az erőforrásnaplókat, részletes diagnosztikai és naplózási információkat nyújtanak az Azure-erőforrásokhoz és az Azure platformhoz, amelytől függenek. Ez a cikk az erőforrás-naplók gyűjtését ismerteti egy Log Analytics-munkaterületen, amely lehetővé teszi, hogy elemezze azokat az Azure Monitor naplókban gyűjtött egyéb figyelési adatokkal hatékony naplólekérdezések használatával, valamint az Azure Monitor egyéb funkcióinak, például a riasztásoknak és a riasztásoknak és a Képi. 


## <a name="what-you-can-do-with-platform-logs-in-a-workspace"></a>A munkaterületi platformnaplókkal kapcsolatban használható műveletek
A Platformnaplók gyűjtése a Log Analytics-munkaterületre lehetővé teszi az összes Azure-erőforrás naplóinak együttes elemzését, valamint az [Azure Monitor-naplók](data-platform-logs.md) számára elérhető összes funkció kihasználását, amely a következőket tartalmazza:

* **Naplólekérdezések** – [Naplólekérdezések](../log-query/log-query-overview.md) létrehozása egy hatékony lekérdezési nyelvet, hogy gyorsan elemezze, és betekintést nyerhet a diagnosztikai adatokat, és elemezni más forrásokból az Azure Monitorban gyűjtött adatokkal.
* **Riasztás –** Proaktív értesítést kaphat az erőforrásnaplókban azonosított kritikus feltételekről és mintákról [az Azure Monitor naplóriasztásainak](alerts-log.md)használatával.
* **Vizualizációk** – A naplólekérdezés eredményeit egy Azure-irányítópultra rögzítheti, vagy egy interaktív jelentés részeként belefoglalhatja egy munkafüzetbe.

## <a name="prerequisites"></a>Előfeltételek
Új munkaterületet kell [létrehoznia,](../learn/quick-create-workspace.md) ha még nem rendelkezik ilyentel. A munkaterület nem kell ugyanabban az előfizetésben, mint az erőforrás-küldő naplók, amíg a felhasználó, aki konfigurálja a beállítást rendelkezik a megfelelő RBAC-hozzáférés mindkét előfizetéshez.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
Platformnaplók küldése a Log Analytics-munkaterületre és más célhelyekre egy Azure-erőforrás diagnosztikai beállításának létrehozásával. A naplók [és metrikák azure-beli gyűjtéséhez](diagnostic-settings.md) olvassa el a Diagnosztikai beállítás létrehozása című témakört.


## <a name="activity-log-collection"></a>Tevékenységnapló gyűjtése
A tevékenységnaplót egyetlen előfizetésből legfeljebb öt Log Analytics-munkaterületre küldheti. A Log Analytics-munkaterületen gyűjtött erőforrás-napló adatok az **AzureActivity-táblában** tárolódnak. 

## <a name="resource-log-collection-mode"></a>Erőforrásnapló-gyűjtési mód
A Log Analytics-munkaterületen gyűjtött erőforrásnapló-adatokat az [Azure Monitor naplók struktúrája](../log-query/logs-structure.md)című táblázatban leírt táblák tárolják. Az erőforrásnaplók által használt táblák attól függnek, hogy az erőforrás milyen típusú gyűjteményt használ:

- Azure-diagnosztika – Minden írt adat az _AzureDiagnostics_ táblához.
- Erőforrás-specifikus – Az adatok az erőforrás egyes kategóriáihoz tartozó egyedi táblába vannak írva.

### <a name="azure-diagnostics-mode"></a>Azure diagnosztika mód 
Ebben a módban az _AzureDiagnostics_ táblában gyűjti a [diagnosztikai beállításokból](diagnostic-settings.md) származó összes adatot. Ez a legtöbb Azure-szolgáltatás által ma használt örökölt módszer.

Mivel több erőforrástípus adatokat küld ugyanabba a táblába, a séma a különböző gyűjtött adattípusok sémájának halmaza.

Vegye figyelembe a következő példát, amikor a diagnosztikai beállítások gyűjtése ugyanabban a munkaterületen történik a következő adattípusokhoz:

- Az 1.  
- Az 1.  
- A 2. szolgáltatás naplói (G, H és I oszlopokból álló sémával)  

Az AzureDiagnostics tábla a következőképpen fog kinézni:  

| ResourceProvider    | Kategória     | A  | B  | C#  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogok    | x1 | y1 | Z1 |    |    |    |    |    |    |
| Microsoft.Service1 | Hibanaplók    |    |    |    | Q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogok    |    |    |    |    |    |    | j1 | k1 | 1. |
| Microsoft.Service1 | Hibanaplók    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogok    |    |    |    |    |    |    | j3 | k3 | 3. |
| Microsoft.Service1 | AuditLogok    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Erőforrás-specifikus
Ebben a módban a kijelölt munkaterület egyes táblái jönnek létre a diagnosztikai környezetben kiválasztott kategóriákhoz. Ez a módszer ajánlott, mivel sokkal könnyebbé teszi az adatokkal való munkát a naplólekérdezésekben, jobb felderíthetőséget biztosít a sémák és azok szerkezete számára, javítja a teljesítményt mind a betöltési késés, mind a lekérdezési idők között, valamint lehetővé teszi az RBAC-jogok megadását egy adott táblázatot. Az összes Azure-szolgáltatás végül átkerül az erőforrás-specifikus módba. 

A fenti példa három tábla létrehozását eredményezné:
 
- A *Table Service1AuditLogs* az alábbiak szerint:

    | Erőforrás-szolgáltató | Kategória | A | B | C# |
    | -- | -- | -- | -- | -- |
    | Szolgáltatás1 | AuditLogok | x1 | y1 | Z1 |
    | Szolgáltatás1 | AuditLogok | x5 | y5 | z5 |
    | ... |

- *A Service1ErrorLogs* tábla az alábbiak szerint:  

    | Erőforrás-szolgáltató | Kategória | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Szolgáltatás1 | Hibanaplók |  Q1 | w1 | e1 |
    | Szolgáltatás1 | Hibanaplók |  q2 | w2 | e2 |
    | ... |

- *A Table Service2AuditLogs* az alábbiak szerint:  

    | Erőforrás-szolgáltató | Kategória | G | H | I |
    | -- | -- | -- | -- | -- |
    | Szolgáltatás2 | AuditLogok | j1 | k1 | 1.|
    | Szolgáltatás2 | AuditLogok | j3 | k3 | 3.|
    | ... |



### <a name="select-the-collection-mode"></a>A gyűjtési mód kiválasztása
A legtöbb Azure-erőforrás azure **diagnosztikai** vagy **erőforrás-specifikus módban** írja az adatokat a munkaterületre anélkül, hogy választási lehetőséget adna. Az [egyes szolgáltatások dokumentációjában](diagnostic-logs-schema.md) megtudhatja, hogy milyen módot használ. Az összes Azure-szolgáltatás végül erőforrás-specifikus módot fog használni. Az átmenet részeként egyes erőforrások lehetővé teszik, hogy kiválasszon egy módot a diagnosztikai beállításban. Minden új diagnosztikai beállításhoz erőforrás-specifikus módot kell megadnia, mivel ez megkönnyíti az adatok kezelését, és segíthet elkerülni az összetett áttelepítéseket egy későbbi időpontban.
  
   ![Diagnosztikai beállítások mód választó](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Jelenleg **az Azure-diagnosztika** és **az erőforrás-specifikus** mód csak akkor választható ki, ha konfigurálja a diagnosztikai beállítást az Azure Portalon. Ha cli, PowerShell vagy Rest API használatával konfigurálja a beállítást, akkor az **azure-diagnosztika**lesz az alapértelmezett.

Meglévő diagnosztikai beállításokat erőforrás-specifikus módra módosíthatja. Ebben az esetben a már összegyűjtött adatok az _AzureDiagnostics_ táblában maradnak, amíg el nem távolítják a munkaterület megőrzési beállítása szerint. Az új adatokat a külön táblázatban gyűjtjük. Az [egyesítő](https://docs.microsoft.com/azure/kusto/query/unionoperator) operátor segítségével mindkét tábla adatait kérdezheti le.

Továbbra is tekintse meg az [Azure Updates](https://azure.microsoft.com/updates/) blogot az Erőforrás-specifikus módot támogató Azure-szolgáltatásokkal kapcsolatos hirdetmények megtekintéséhez.

### <a name="column-limit-in-azurediagnostics"></a>Oszlopkorlát az AzureDiagnostics ban
Az Azure Monitor naplók bármely táblához 500-as tulajdonságkorlát van. Ha ezt a korlátot eléri, az első 500-on kívüli tulajdonsággal rendelkező adatokat tartalmazó sorok at a betöltési időpontban eldobják. Az *AzureDiagnostics* tábla különösen érzékeny erre a korlátra, mivel az összes Azure-szolgáltatás írása tulajdonságait tartalmazza.

Ha több szolgáltatásból gyűjt erőforrásnaplókat, az _AzureDiagnostics_ túllépheti ezt a korlátot, és az adatok kimaradnak. Amíg az összes Azure-szolgáltatás támogatja az erőforrás-specifikus módot, konfigurálnia kell az erőforrásokat, hogy több munkaterületre írjanak, hogy csökkentsék az 500 oszlopkorlát elérésének lehetőségét.

### <a name="azure-data-factory"></a>Azure Data Factory
Az Azure Data Factory, mert egy nagyon részletes naplókészlet, egy olyan szolgáltatás, amely ről ismert, hogy írjon nagy számú oszlopot, és potenciálisan _az AzureDiagnostics_ túllépi a korlátot. Az erőforrás-specifikus mód engedélyezése előtt konfigurált diagnosztikai beállításokhoz minden egyedileg elnevezett felhasználói paraméterhez új oszlop jön létre bármely tevékenység gel szemben. További oszlopok jönnek létre a tevékenységbemenetek és -kimenetek részletes jellege miatt.
 
A naplók áttelepítése az erőforrás-specifikus mód a lehető leghamarabb. Ha ezt nem tudja azonnal megtenni, egy köztes alternatíva az Azure Data Factory-naplók elkülönítése a saját munkaterületükre, hogy minimálisra csökkentse annak esélyét, hogy ezek a naplók hatással legyenek a munkaterületeken gyűjtött más naplótípusokra.


## <a name="next-steps"></a>További lépések

* [További információ az erőforrásnaplókról.](platform-logs-overview.md)
* [Diagnosztikai beállítás létrehozásával naplót és metrikákat gyűjthet az Azure-ban.](diagnostic-settings.md)
