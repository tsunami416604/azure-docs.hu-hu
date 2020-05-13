---
title: Azure Monitor az Azure cache for Redis (előzetes verzió) | Microsoft Docs
description: Ez a cikk a Redis Cache funkció Azure Monitor ismerteti, amely az Azure cache-t biztosítja a Redis-tulajdonosok számára a teljesítmény-és kihasználtsági problémák gyors megismerése érdekében.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210973"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Ismerkedés az Azure cache Azure Monitor Redis (előzetes verzió)

A Redis (előzetes verzió) Azure cache-hez készült Azure Monitor a teljes körű interaktív felhasználói élményben tekinti át az összes Azure-gyorsítótár teljesítményének, hibáinak, kapacitásának és működési állapotának áttekintését. Ez a cikk segítséget nyújt az új figyelési élmény előnyeinek megismeréséhez, valamint arról, hogy miként módosíthatja és igazíthatja a felhasználói élményt a szervezet egyedi igényeinek megfelelően.

## <a name="introduction"></a>Introduction (Bevezetés)

A tapasztalatok megismerése előtt meg kell ismernie, hogyan jeleníti meg és jeleníti meg az információkat.

A következőket biztosítja:

* Az Azure cache **méretezési perspektívája** , hogy az összes előfizetése egyetlen helyen Redis az erőforrásokat, a szelektív hatókört csak azokra az előfizetésekre és erőforrásokra lehet kiértékelni, amelyeket érdekel.

* Egy adott Azure cache részletes **elemzését** Redis-erőforráshoz, amely segít a problémák diagnosztizálásában, illetve a kategóriánkénti kihasználtság, a hibák, a kapacitás és a műveletek részletes elemzésének elvégzésében. Az egyik lehetőség kiválasztásával részletesen áttekintheti a releváns adatokat.  

* **Testreszabható** – ez a felület Azure monitor munkafüzet-sablonokra épül, így módosíthatja, hogy milyen mérőszámok jelenjenek meg, módosíthatók vagy állíthatók be a határértékekhez igazodó küszöbértékek, majd egy egyéni munkafüzetbe menthetők. A munkafüzetek diagramjai ezután rögzíthetők az Azure-irányítópultokon.  

Ez a funkció nem igényli, hogy bármit engedélyezzen vagy konfiguráljan, a rendszer alapértelmezés szerint a Redis-hez tartozó Azure cache-t gyűjti.

>[!NOTE]
>A szolgáltatáshoz való hozzáférés díjmentes, és a [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/) lapon leírtak szerint csak az Ön által konfigurált vagy engedélyezett Azure monitor alapvető funkciókért kell fizetnie.

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Az Azure cache kihasználtsági és teljesítmény-metrikáinak megtekintése a Redis-hez

Ha szeretné megtekinteni a Storage-fiókok kihasználtságát és teljesítményét az összes előfizetésében, hajtsa végre a következő lépéseket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **figyelőre** , és válassza a **figyelő**lehetőséget.

    ![A "monitor" szót tartalmazó keresőmező és egy olyan legördülő lista, amely egy sebességmérő stílusú képpel rendelkező szolgáltatásokat mutat](./media/cosmosdb-insights-overview/search-monitor.png)

3. Válassza ki **Az Azure cache Redis (előzetes verzió)** lehetőséget.

Ha ez a lehetőség nem jelenik meg, kattintson a **továbbiak** lehetőségre, és válassza **Az Azure cache lehetőséget a Redis**.

### <a name="overview"></a>Áttekintés

Az **Áttekintés**során a táblázat a Redis-metrikák interaktív Azure cache-t jeleníti meg. Az eredményeket az alábbi legördülő listából kiválasztott beállítások alapján szűrheti:

* **Előfizetések** – csak az Azure cache-t használó előfizetések jelennek meg a Redis-erőforráshoz.  

* **Azure cache for Redis** – kiválaszthatja az összes, egy részhalmazt vagy egy Azure-gyorsítótárat a Redis-erőforráshoz.

* **Időtartomány** – alapértelmezés szerint az utolsó 4 órányi információt jeleníti meg a megfelelő beállítások alapján.

A legördülő lista alatti számláló csempe a kijelölt előfizetésekben a Redis-erőforrások Azure-gyorsítótárának teljes számát összesíti. A munkafüzet oszlopaihoz feltételes színkódolás vagy intenzitástérképei van a tranzakciós metrikák jelentéséhez. A legmélyebb szín a legmagasabb és a világosabb szín a legalacsonyabb értékeken alapul.

A Redis-erőforrások Azure-gyorsítótára melletti legördülő menü kiválasztásával megjelenítheti a teljesítménymutatók részletezését az egyes erőforrások szintjén:

![Képernyőkép az áttekintő felületről](./media/redis-cache-insights-overview/overview.png)

Ha a kék színnel jelölt Redis-erőforráshoz tartozó Azure cache elemet választja, a rendszer a társított fiók alapértelmezett **áttekintését** fogja igénybe venni. Ez a,,,,,,, `Used Memory` `Used Memory Percentage` `Server Load` `Server Load Timeline` `CPU` `Connected Clients` `Cache Misses` `Errors (Max)` .

### <a name="operations"></a>Műveletek

Válassza a lap tetején a **műveletek** lehetőséget, majd megnyílik a munkafüzet sablonjának **műveletek** rész. Ez azt mutatja,,,, `Total Operations` `Total Operations Timeline` `Operations Per Second` `Gets` `Sets` .

![Képernyőkép az áttekintő felületről](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>Használat

A lap tetején kattintson a **használat** elemre, és megnyílik a munkafüzet sablonjának **használati** része. Ez azt mutatja,,,, `Cache Read` `Cache Read Timeline` `CacheWrite` `CacheHits` `Cache Misses` .

![Képernyőkép az áttekintő felületről](./media/redis-cache-insights-overview/usage.png)

Válassza ki a **hibák** elemet az oldal tetején, és megnyílik a munkafüzet sablonjának **hibák** szakasza. Ez a,,,,,,, `Total Errors` `Failover/Errors` `UnresponsiveClient/Errors` `RDB/Errors` `AOF/Errors` `Export/Errors` `Dataloss/Errors` `Import/Errors` .

![Képernyőfelvétel a HTTP-kérelem típusa szerinti bontásban fellépő hibákról](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>Metrika-definíciók

Az ezeket a munkafüzeteket alkotó metrika-definíciók teljes listáját az [elérhető metrikák és jelentéskészítési időközök című cikkben](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals)tekintheti meg.

## <a name="pin-export-and-expand"></a>PIN-kód, exportálás és Kibontás

A metrikus szakaszok bármelyikét rögzítheti egy [Azure-irányítópultra](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) , ha a szakasz jobb felső sarkában található gombostű ikonra kattint.

![Metrikus szakasz rögzítése az irányítópulton – példa](./media/cosmosdb-insights-overview/pin.png)

Az Excel formátumba való exportáláshoz kattintson a gombostű ikon bal oldalán található lefelé mutató nyíl ikonra.

![Munkafüzet exportálása ikon](./media/cosmosdb-insights-overview/export.png)

A munkafüzet összes legördülő nézetének kibontásához vagy összecsukásához válassza az Exportálás ikon bal oldalán található Kibontás ikont:

![Munkafüzet kibontása ikon](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Az Azure cache Azure Monitor testreszabása a Redis (előzetes verzió)

Mivel ez a felület Azure monitor munkafüzet-sablonokra épül, **testreszabhatja**a  >  **Edit** módosítást, és **mentheti** a módosított verzió egy példányát egy egyéni munkafüzetbe. 

![Sáv testreszabása](./media/cosmosdb-insights-overview/customize.png)

A munkafüzetek egy erőforráscsoporthoz lesznek mentve, vagy az Ön számára magánjellegű **saját jelentések** szakaszban, vagy a **megosztott jelentések** szakaszban, amely mindenki számára elérhető az erőforráscsoporthoz való hozzáféréssel. Az Egyéni munkafüzet mentése után nyissa meg a munkafüzet-katalógust, és indítsa el.

![A munkafüzet-katalógus indítása a parancssáv alapján](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>További lépések

* A [metrikai riasztások](../platform/alerts-metric.md) és a [szolgáltatás állapotára vonatkozó értesítések](../../service-health/alerts-activity-log-service-notifications.md) konfigurálása automatizált riasztások beállításához a problémák észlelése érdekében.

* Ismerkedjen meg a forgatókönyvekkel, amelyek támogatják az új és a meglévő jelentések testreszabását, valamint az [interaktív jelentések Azure monitor-munkafüzetekkel való létrehozását](../app/usage-workbooks.md)ismertető áttekintést.
