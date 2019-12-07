---
title: Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen
description: Ismerje meg, hogyan továbbíthatja az Azure-erőforrás-naplókat Log Analytics munkaterületre Azure Monitorban.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 83b91be52694076373d950e0ad785ef22671ef4f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894515"
---
# <a name="collect-azure-resource-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen Azure Monitor
Az Azure-beli [erőforrás-naplók](resource-logs-overview.md) részletes és gyakori információkat biztosítanak az Azure-erőforrások belső működéséről. Ez a cikk ismerteti az erőforrás-naplók összegyűjtését egy Log Analytics munkaterületen, amely lehetővé teszi, hogy a rendszer hatékony naplók használatával elemezze Azure Monitor naplókban gyűjtött más figyelési adatokkal, valamint más Azure Monitor funkciók, például riasztások és vizualizációk. 


## <a name="what-you-can-do-with-resource-logs-in-a-workspace"></a>A munkaterület erőforrás-naplóival végezhető műveletek
Az erőforrás-naplók Log Analytics munkaterületre való összegyűjtése lehetővé teszi az Azure-erőforrások naplóinak összegyűjtését, valamint az összes elérhető funkció kihasználása [Azure monitor naplók](data-platform-logs.md) számára, amely a következőket tartalmazza:

* **Lekérdezések naplózása** – a [napló lekérdezéseit](../log-query/log-query-overview.md) hatékony lekérdezési nyelv segítségével hozhatja létre, így gyorsan elemezheti és betekintést nyerhet a diagnosztikai adatokba, valamint elemezheti azt a Azure monitor más forrásokból gyűjtött adatokkal.
* **Riasztás** – értesítéseket kaphat az erőforrás-naplókban azonosított kritikus feltételekről és mintákról az Azure monitor-ban található [naplózási riasztások](alerts-log.md)használatával.
* **Vizualizációk** – egy naplóbeli lekérdezés eredményének rögzítése egy Azure-irányítópulton, vagy egy interaktív jelentés részeként egy munkafüzetbe belefoglalása.

## <a name="prerequisites"></a>Előfeltételek
Ha még nem rendelkezik ilyennel, [létre kell hoznia egy új munkaterületet](../learn/quick-create-workspace.md) . A munkaterületnek nem kell ugyanabban az előfizetésben lennie, mint az erőforrás-küldési naplók, feltéve, hogy a beállítást konfiguráló felhasználó mindkét előfizetéshez megfelelő RBAC-hozzáféréssel rendelkezik.

## <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása
A rendszer alapértelmezés szerint nem gyűjti az erőforrás-naplókat. Egy Azure-erőforrás diagnosztikai beállításának létrehozásával összegyűjtheti azokat egy Log Analytics munkaterületen és más célhelyeken. A részletekért lásd: [diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](diagnostic-settings.md) .

## <a name="collection-mode"></a>Gyűjtési mód
A Log Analytics munkaterületen összegyűjtött adatokat a rendszer a [Azure monitor naplók struktúrájában](../log-query/logs-structure.md)leírtak szerint táblázatokban tárolja. Az erőforrás-naplók által használt táblák attól függnek, hogy az erőforrás milyen típusú gyűjteményt használ:

- Azure Diagnostics – az összes írásos érték a _AzureDiagnostics_ tábla.
- Az erőforrás-specifikus – az erőforrás minden kategóriája esetében az egyes táblákba kerül.

### <a name="azure-diagnostics-mode"></a>Azure Diagnostics mód 
Ebben a módban a [diagnosztikai beállításokból](diagnostic-settings.md) származó összes adatok gyűjtése a _AzureDiagnostics_ táblában történik. Ez az örökölt módszer, amelyet jelenleg a legtöbb Azure-szolgáltatás használ.

Mivel a több erőforrástípus adatokat küld ugyanahhoz a táblához, annak sémája a begyűjtött különböző adattípusok sémáinak kiterjesztése.

Vegye figyelembe a következő példát, ahol a diagnosztikai beállítások gyűjtése ugyanabban a munkaterületen történik a következő adattípusok esetén:

- Az 1. szolgáltatás naplófájljai (az A, B és C oszlopokból álló sémával)  
- Az 1. szolgáltatás naplófájljai (a D, E és F oszlopokból álló sémával)  
- A 2. szolgáltatás naplófájljai (a G, H és I oszlopokból álló sémák)  

A AzureDiagnostics táblázat a következőképpen jelenik meg:  

| ResourceProvider    | Kategória     | A  | B  | C#  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft. service1 | AuditLogs    | X1 | y1 | Z1 |    |    |    |    |    |    |
| Microsoft. service1 | Alkalmazásnaplókat    |    |    |    | első | W1 | E1 csomag |    |    |    |
| Microsoft. service2 | AuditLogs    |    |    |    |    |    |    | J1 | K1 csomag | L1 |
| Microsoft. service1 | Alkalmazásnaplókat    |    |    |    | Q2 | W2 | E2 |    |    |    |
| Microsoft. service2 | AuditLogs    |    |    |    |    |    |    | J3 | K3 | L3 |
| Microsoft. service1 | AuditLogs    | x5 | y5 | Z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Erőforrás-specifikus
Ebben a módban a kiválasztott munkaterület egyes táblái jönnek létre a diagnosztikai beállításban kiválasztott kategóriákhoz. Ez a módszer azért ajánlott, mert sokkal könnyebben használható a naplózott lekérdezések adataival, és a sémák és azok struktúrájának jobb észlelését teszi lehetővé, javítja a teljesítményt mind a betöltési késés, mind a lekérdezési időpontok esetében, valamint lehetővé teszi a RBAC jogosultságok megadását adott tábla. Az összes Azure-szolgáltatás végül az erőforrás-specifikus módra lesz migrálva. 

A fenti példa három tábla létrehozását eredményezi:
 
- A tábla *Service1AuditLogs* a következőképpen történik:

    | Erőforrás-szolgáltató | Kategória | A | B | C# |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | X1 | y1 | Z1 |
    | Service1 | AuditLogs | x5 | y5 | Z5 |
    | ... |

- A tábla *Service1ErrorLogs* a következőképpen történik:  

    | Erőforrás-szolgáltató | Kategória | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | Alkalmazásnaplókat |  első | W1 | E1 csomag |
    | Service1 | Alkalmazásnaplókat |  Q2 | W2 | E2 |
    | ... |

- A tábla *Service2AuditLogs* a következőképpen történik:  

    | Erőforrás-szolgáltató | Kategória | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | J1 | K1 csomag | L1|
    | Service2 | AuditLogs | J3 | K3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>Gyűjtési mód kiválasztása
A legtöbb Azure-erőforrás az **Azure-diagnosztika** vagy az **erőforrás-specifikus mód** használatával fogja írni az adatok a munkaterületre való bevitelét anélkül, hogy választ kellene adni. Az [egyes szolgáltatásokhoz tartozó dokumentációban](diagnostic-logs-schema.md) megtekintheti az általa használt üzemmód részleteit. Az összes Azure-szolgáltatás végül erőforrás-specifikus módot fog használni. Az áttérés részeként egyes erőforrások lehetővé teszik a mód kiválasztását a diagnosztikai beállításokban. Az új diagnosztikai beállításokhoz az erőforrás-specifikus módot kell megadnia, mivel így könnyebben kezelhető az adatok kezelése, és előfordulhat, hogy egy későbbi időpontban el szeretné kerülni az összetett Migrálás elkerülését.
  
   ![Diagnosztikai beállítások mód választója](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)




> [!NOTE]
> Jelenleg az **Azure Diagnostics** és az **erőforrás-specifikus** mód csak akkor választható, ha a Azure Portal a diagnosztikai beállítást konfigurálja. Ha a parancssori felület, a PowerShell vagy a REST API használatával konfigurálja a beállítást, az alapértelmezés szerint az **Azure-diagnosztika**lesz.

Egy meglévő diagnosztikai beállítást erőforrás-specifikus módra is módosíthat. Ebben az esetben a már összegyűjtött adatok a _AzureDiagnostics_ táblában maradnak, amíg el nem távolítják a munkaterület megőrzési beállításának megfelelően. A rendszer begyűjti az új adatokat a dedikált táblába. A [Union](https://docs.microsoft.com/azure/kusto/query/unionoperator) operátorral több táblázaton keresztül is lekérdezheti az adatlekérdezéseket.

Folytassa az [Azure Updates](https://azure.microsoft.com/updates/) blogban az erőforrás-specifikus üzemmódot támogató Azure-szolgáltatásokra vonatkozó hirdetmények megtekintését.

### <a name="column-limit-in-azurediagnostics"></a>Oszlop korlátja a AzureDiagnostics
A Azure Monitor-naplókban található bármely táblához 500 tulajdonság van korlátozva. Ha elérte ezt a korlátot, minden olyan sor, amely az első 500-n kívüli tulajdonsággal rendelkező adatot tartalmaz, a rendszer elveszi a betöltési idő alatt. A *AzureDiagnostics* tábla kifejezetten erre a korlátozásra van kitéve, mivel az összes Azure-szolgáltatáshoz tartozó tulajdonságokat tartalmazza.

Ha több szolgáltatásból gyűjt erőforrás-naplókat, a _AzureDiagnostics_ túllépheti ezt a korlátot, és az adatok nem lesznek kimaradva. Amíg az összes Azure-szolgáltatás nem támogatja az erőforrás-specifikus üzemmódot, úgy kell beállítania az erőforrásokat, hogy több munkaterületre is írna, hogy csökkentse az 500-es oszlop korlátjának elérési lehetőségét.

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory a naplók nagyon részletes készlete miatt egy olyan szolgáltatás, amely nagy számú oszlop írására és potenciálisan _AzureDiagnostics_ okozhatja a korlátot. Az erőforrás-specifikus mód engedélyezése előtt konfigurált diagnosztikai beállítások esetében minden egyes egyedi névvel ellátott felhasználói paraméterhez létrejön egy új oszlop, amely minden tevékenységre kiterjed. További oszlopok jönnek létre a tevékenységek bemenetének és kimenetének részletes természete miatt.
 
A naplók áttelepíthetők az erőforrás-specifikus mód használatára a lehető leghamarabb. Ha ezt azonnal nem tudja elvégezni, egy ideiglenes alternatíva az, hogy elkülönítse Azure Data Factory naplókat a saját munkaterületére, hogy csökkentse annak esélyét, hogy ezek a naplók a munkaterületeken gyűjtött más naplózási típusokra is hatással legyenek.


## <a name="next-steps"></a>Következő lépések

* Az Azure erőforrás-naplókkal kapcsolatos további információkért lásd: [Az Azure-erőforrások naplófájljainak áttekintése](resource-logs-overview.md).
* Ha diagnosztikai beállítást szeretne létrehozni az erőforrás-naplók Log Analytics munkaterületre való gyűjtéséhez, olvassa el a [diagnosztikai beállítás létrehozása naplók és mérőszámok az Azure-ban való gyűjtéséhez](diagnostic-settings.md)című témakört.
