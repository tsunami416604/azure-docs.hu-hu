---
title: A Microsoft Azure-ban mérőszámok áttekintése
description: Metrikák és a használatukat a Microsoft Azure-ban – áttekintés
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 3501c8d35968ecf8e32c806dfb05ccfebc7f4386
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264221"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>A Microsoft Azure-ban mérőszámok áttekintése
Ez a cikk ismerteti, hogy milyen adatok gyűjtése le van a Microsoft Azure-ban az előnyöket, és használatuk indítása.  

## <a name="what-are-metrics"></a>Mik azok a metrikák?
Azure figyelő címinfrastruktúra megjelenítési lehetőségeinek a teljesítmény- és a munkaterhelések Azure állapot telemetriai adatok felhasználását teszi lehetővé. A legfontosabb Azure telemetriai adatok típus (más néven teljesítményszámlálók) vagy az Azure erőforrások által kibocsátott metrikákat. Az Azure figyelő többféleképpen is konfigurálhatja, és felhasználhatják a figyeléshez és hibaelhárításhoz metrikákat.

## <a name="what-are-the-characteristics-of-metrics"></a>Mik azok a metrikák jellemzői?
Metrikák a következő jellemzőkkel rendelkezik:

* Összes metrikát rendelkezik **egy perces gyakoriságot** (kivéve, ha a egy metrika-definíció). Kapott a metrika értékét percenként az erőforrás felkínálva közel valós idejű információkat az állapot és az erőforrás állapotát.
* Adatok gyűjtése le van **érhető el azonnal**. Nem kell részt vevő, vagy állítsa be a további diagnosztikához.
* Van-e hozzáférési **93 a napok** minden mérőszám. Gyorsan megtalálhatja a legutóbbi és havi kapcsolatos trendeket szemlélteti a teljesítmény- vagy az erőforrás állapotát.
* Néhány metrikák is attribútumokkal név-érték pár nevű **dimenziók**. Ezek lehetővé teszik a további szegmentálni, és vizsgálja meg a metrika részletesebb módon.

## <a name="what-can-you-do-with-metrics"></a>Mire szolgál a metrikák?
Metrikák lehetővé teszik a következő feladatokat:


- Metrika konfigurálása **szabályt, amely értesítést küld vagy fogad automatikus művelet riasztás** amikor keverve használ a metrika a beállított küszöbértéket. Műveletek vezérli [művelet csoportok](monitoring-action-groups.md). Példa műveletek a következők lehetnek: e-mail, telefon és SMS-értesítések, hívja a webhook egy runbookot, és több indítása. **Automatikus skálázás** egy speciális automatizált művelet, amelynek segítségével méretezhető, erőforrás felfelé és lefelé terhelhető még költséges tartása alacsonyabb, amikor nem terhelés alatt van. Konfigurálhatja az automatikus skálázási beállítás szabály bejövő vagy kimenő méretezése a küszöbértéket meghaladó metrika alapján.
- **Útvonal** összes mérni kívánt *Application Insights* vagy *Naplóelemzési* azonnali analytics, a Keresés és a egyéni riasztási metrikai adatok az erőforrások közül. Is adatfolyam formájában a metrikák egy *Eseményközpont*, így lehetővé teszi, és Azure Stream Analytics vagy egyéni alkalmazások, a közel valós idejű elemzési irányíthatja őket. Beállíthatja az Event Hubs streaming a diagnosztikai beállítások használatával.
- **Archív** az erőforrás a megfelelés, a naplózás vagy kapcsolat nélküli jelentéskészítési célból a teljesítmény vagy a rendszerállapot előzményeit.  Az erőforrás diagnosztikai beállításainak konfigurálásakor irányítani tudja a metrikákat, az Azure Blob storage.
- Használja a **Azure-portálon** felderítésére, eléréséhez, és minden metrikák megtekintéséhez jelöljön ki egy erőforrást, és a metrikák a diagram megrajzolásához. Az erőforrás (például egy virtuális gép, a webhely vagy a logikai alkalmazás) teljesítményének nyomon követéséhez a diagram az irányítópulton való rögzítéshez.  
- **Hajtsa végre a speciális elemzés** vagy a teljesítmény vagy a használati trendeket a erőforrás jelentés.
- **Lekérdezés** metrikákat a PowerShell-parancsmagok és a platformok közötti REST API használatával.
- **Felhasználását** a metrikák az új Azure figyelő REST API-kon keresztül.

  ![Az Azure a figyelő a metrikák útválasztását](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Hozzáférés metrikákat a portálon
Az alábbiakban látható egy gyors útmutató arról, hogyan metrika diagram létrehozása az Azure-portál használatával.

### <a name="to-view-metrics-after-creating-a-resource"></a>Erőforrás létrehozása után metrikák megtekintése
1. Nyissa meg az Azure Portalt.
2. Az Azure App Service-webhelyet hoz létre.
3. Miután létrehozott egy webhelyen, lépjen a **áttekintése** a webhely paneljén.
4. Új mérőszámok, megtekintheti a **figyelés** csempére. Módosíthatja a csempén, majd válassza ki a további metrikákat.

   ![Az Azure-figyelő erőforrás metrikáit](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Egy helyen lévő összes metrikát eléréséhez
1. Nyissa meg az Azure Portalt.
2. Keresse meg az új **figyelő** fülre, és majd, és válassza ki a **metrikák** alatta lehetőséget.
3. Válassza ki az előfizetés, erőforráscsoport és az erőforrás nevét a legördülő listából.
4. Az elérhető mérőszámok lista megtekintése. Ezután jelölje be a metrika kapcsolatban, és azt megrajzolásához.
5. Akkor is rögzítheti az irányítópulton kattintson a jobb felső sarokban a PIN-kódot.

   ![Azure-figyelőben egyetlen helyen összes metrikát eléréséhez](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> (Azure Resource Manager-alapú) virtuális gépek gazdaszintű metrikák érhető el, és a virtulálisgép-skálázási készletekben további diagnosztikai beállítások elvégzése nélkül. Új gazdagép-szintű metrikákat a Windows és Linux-példányok érhetők el. A metrikák fel nem keverendő össze a Vendég-operációsrendszer-szintű metrikák, hogy rendelkezik-e hozzáférést, ha bekapcsolja az Azure Diagnostics a virtuális gépek vagy virtuálisgép-méretezési készlet. Diagnosztika konfigurálásával kapcsolatos további tudnivalókért lásd: [Újdonságok a Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Az Azure figyelő is rendelkeznek, egy új mérőszámok diagramkészítési élmény ebben a nézetben. Ez lehetővé teszi a felhasználók számára egy diagram erőforrásairól metrikák átfedő. A felhasználók is megrajzolásához, szegmens, és szűrheti a felhasználói élmény diagramkészítési új mérőszám használatával többdimenziós metrikák. További [kattintson ide](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>A REST API-n keresztül hozzáférést metrikák
Azure metrikák az Azure-figyelő API-k keresztül érhetők el. Két API-k, amelyek segítenek felderítése és metrikákat eléréséhez:

* Használja a [Azure figyelő metrika definíciók REST API](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) metrikákat, és egyetlen dimenzióhoz sem, a szolgáltatás számára rendelkezésre álló listájának eléréséhez.
* Használja a [Azure figyelő metrikák REST API](https://docs.microsoft.com/rest/api/monitor/metrics) szegmentálni, szűréséhez, és a tényleges metrikai adatok elérésére.

> [!NOTE]
> Ez a cikk ismerteti a metrikák keresztül a [új API-t a metrikák](https://docs.microsoft.com/rest/api/monitor/) az Azure-erőforrások. Az API-verzió az új metrikai meghatározásainak és a metrikák API-k 2018-01-01. Az örökölt metrikai meghatározásainak és a metrikák elérhető verzió 2014-04-01 API-val.
>
>

Az Azure figyelő REST API-kkal részletes útmutatást lásd: [Azure figyelő REST API-forgatókönyv](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Exportálás metrikák
Nyissa meg a **diagnosztikai beállítások** részen a **figyelő** lapra, és a metrikák exportálási beállítások megtekintéséhez. Választható ki metrikák (és diagnosztikai naplók) a Blob storage irányíthatja át az Azure Event Hubs, illetve a Naplóelemzési az ebben a cikkben korábban említett használati esetek.

 ![Exportálási beállítások a Azure figyelő metrikák](./media/monitoring-overview-metrics/MetricsOverview3.png)

A Resource Manager-sablonok, konfigurálható [PowerShell](insights-powershell-samples.md), [Azure CLI](insights-cli-samples.md), vagy [REST API-k](https://msdn.microsoft.com/library/dn931943.aspx).

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="take-action-on-metrics"></a>Művelet végrehajtása a metrikák
Értesítéseket vagy automatikus műveletek végrehajtása a metrikaadatokat, konfigurálhatja a riasztási szabályok vagy az automatikus skálázási beállításokat.

### <a name="configure-alert-rules"></a>A riasztási szabályok konfigurálása
Riasztási szabályok metrikák a konfigurálható. Ezek a riasztási szabályok ellenőrizheti, ha egy metrika elért egy bizonyos küszöböt. Nincsenek Azure-figyelő által kínált két mérőszám riasztási képességek.

Metrika riasztások: is majd e-mailben értesítse arról, vagy egy egyéni parancsfájlok futtatásához használható webhook érvényesítést. A webhook segítségével is konfigurálhatja a harmadik féltől származó terméket integrációja.

 ![Metrikák és az Azure-figyelő riasztási szabályok](./media/monitoring-overview-metrics/MetricsOverview4.png)

Újabb metrika riasztásokat is képes több metrikákat, és a küszöbértékek erőforrás figyelése és majd értesítést kérhet a keresztül egy [művelet csoport](/monitoring-action-groups.md). További információ [újabb riasztásokat Itt](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Automatikus skálázási az Azure erőforrások
Egyes Azure-erőforrások támogatja, a méretezés fel- vagy a munkaterhelések kezelésére több példány. Automatikus skálázás App Service (webalkalmazások), a virtuálisgép-méretezési csoportok és a klasszikus Azure-Felhőszolgáltatások vonatkozik. Konfigurálhatja az automatikus skálázási szabályok vertikális fel- vagy egy bizonyos metrika, amely hatással van a számítási feladatok ebbe a megadott küszöbértéket. További információkért lásd: [automatikus skálázás áttekintése](monitoring-overview-autoscale.md).

 ![Metrikák és az Azure a figyelő automatikus skálázás](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Támogatott szolgáltatások és
A támogatott szolgáltatások és azok metrikákat, részletes listáját megtekintheti [Azure figyelő metrikák--erőforrás típusonkénti támogatott metrikák](monitoring-supported-metrics.md).

## <a name="next-steps"></a>További lépések
Ez a cikk hivatkozásaiban hivatkozik. Ezenkívül további információk:  

* [Az automatikus skálázás közös metrikák](insights-autoscale-common-metrics.md)
* [A riasztási szabályok létrehozása](insights-alerts-portal.md)
* [A Naplóelemzési az Azure storage naplóinak elemzése](../log-analytics/log-analytics-azure-storage.md)
