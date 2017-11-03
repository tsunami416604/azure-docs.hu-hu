---
title: "A Microsoft Azure-ban mérőszámok áttekintése |} Microsoft Docs"
description: "Metrikák és a használatukat a Microsoft Azure-ban – áttekintés"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: johnkem
ms.openlocfilehash: eb519aab87c13e8836bf1d41992812762f0cd737
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>A Microsoft Azure-ban mérőszámok áttekintése
Ez a cikk ismerteti, hogy milyen adatok gyűjtése le van a Microsoft Azure-ban az előnyöket, és használatuk indítása.  

## <a name="what-are-metrics"></a>Mik azok a metrikák?
Azure figyelő címinfrastruktúra megjelenítési lehetőségeinek a teljesítmény- és a munkaterhelések Azure állapot telemetriai adatok felhasználását teszi lehetővé. A legfontosabb Azure telemetriai adatok típus (más néven teljesítményszámlálók) vagy az Azure erőforrások által kibocsátott metrikákat. Az Azure figyelő többféleképpen is konfigurálhatja, és felhasználhatják a figyeléshez és hibaelhárításhoz metrikákat.

## <a name="what-can-you-do-with-metrics"></a>Mire szolgál a metrikák?
Metrikák egy értékes telemetriai adatok forrását, és lehetővé teszik a következő feladatokat:

* **A teljesítmény nyomon** az erőforrás (például egy virtuális gép, a webhely vagy a logikai alkalmazást) a metrikák a portál diagramon ábrázolásához és, hogy a diagramot irányítópulton való rögzítéshez.
* **Probléma értesítés** , amely teljesítményére hatással van az erőforrás metrika ebbe a meghatározott küszöbérték.
* **Konfigurálja az automatikus műveleteket**, például az automatikus skálázás egy erőforrás vagy runbook kiváltó metrika ebbe a meghatározott küszöbérték.
* **Hajtsa végre a speciális elemzés** vagy a teljesítmény vagy a használati trendeket a erőforrás jelentés.
* **Archív** az erőforrás teljesítmény vagy a rendszerállapot előzményeinek **megfelelőség és naplózás** céljából.

## <a name="what-are-the-characteristics-of-metrics"></a>Mik azok a metrikák jellemzői?
Metrikák a következő jellemzőkkel rendelkezik:

* Összes metrikát rendelkezik **egy perces gyakoriságot**. Kapott a metrika értékét percenként az erőforrás felkínálva közel valós idejű információkat az állapot és az erőforrás állapotát.
* Adatok gyűjtése le van **érhető el azonnal**. Nem kell részt vevő, vagy állítsa be a további diagnosztikához.
* Van-e hozzáférési **előzmények 30 napnyi** minden mérőszám. Gyorsan megtalálhatja a legutóbbi és havi kapcsolatos trendeket szemlélteti a teljesítmény- vagy az erőforrás állapotát.
* Néhány metrikák is attribútumokkal név-érték pár nevű **dimenziók**. Ezek lehetővé teszik a további szegmentálni, és vizsgálja meg a metrika részletesebb módon.

További lehetőségek:

* Metrika konfigurálása **szabályt, amely értesítést küld vagy fogad automatikus művelet riasztás** amikor keverve használ a metrika a beállított küszöbértéket. Automatikus skálázás, amely lehetővé teszi a bejövő kérések teljesítéséhez erőforrás horizontális vagy tölti be a webhely vagy a számítási erőforrások további automatizált műveletet. Konfigurálhatja az automatikus skálázási beállítás szabály bejövő vagy kimenő méretezése a küszöbértéket meghaladó metrika alapján.

* **Útvonal** összes metrikákat az Application Insights vagy Naplóelemzés (OMS) azonnali analytics, a Keresés és a egyéni riasztási metrikai adatok az erőforrások közül. Adatfolyam formájában a mérni kívánt Eseményközpontban, amely lehetővé teszi majd irányíthatja őket Azure Stream Analytics vagy egyéni alkalmazások, a közel valós idejű elemzési is. Beállíthatja az Event Hubs streaming a diagnosztikai beállítások használatával.

* **Archiválja a mérni kívánt tárolási** a hosszabb megőrzési vagy offline jelentéskészítésre használja őket. Az erőforrás diagnosztikai beállításainak konfigurálásakor irányítani tudja a metrikákat, az Azure Blob storage.

* Könnyen felderítése, fér hozzá, és **megtekintése összes metrikát** Azure-portálon a jelöljön ki egy erőforrást, és a metrikák a diagram megrajzolásához.

* **Felhasználását** a metrikák az új Azure figyelő REST API-kon keresztül.

* **Lekérdezés** metrikákat a PowerShell-parancsmagok és a platformok közötti REST API használatával.

  ![Az Azure a figyelő a metrikák útválasztását](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Hozzáférés metrikákat a portálon
Az alábbiakban látható egy gyors útmutató arról, hogyan metrika diagram létrehozása az Azure-portál használatával.

### <a name="to-view-metrics-after-creating-a-resource"></a>Erőforrás létrehozása után metrikák megtekintése
1. Nyissa meg az Azure-portálon.
2. Az Azure App Service-webhelyet hoz létre.
3. Miután létrehozott egy webhelyen, lépjen a **áttekintése** a webhely paneljén.
4. Új mérőszámok, megtekintheti a **figyelés** csempére. Módosíthatja a csempén, majd válassza ki a további metrikákat.

   ![Az Azure-figyelő erőforrás metrikáit](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Egy helyen lévő összes metrikát eléréséhez
1. Nyissa meg az Azure-portálon.
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

* Használja a [Azure figyelő metrika definíciók REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricdefinitions) metrikákat, és egyetlen dimenzióhoz sem, a szolgáltatás számára rendelkezésre álló listájának eléréséhez.
* Használja a [Azure figyelő metrikák REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metrics) szegmentálni, szűréséhez, és a tényleges metrikai adatok elérésére.

> [!NOTE]
> Ez a cikk ismerteti a metrikák keresztül a [új API-t a metrikák](https://docs.microsoft.com/en-us/rest/api/monitor/) az Azure-erőforrások. Az API-verzió az új metrikai meghatározásainak és a metrikák API-k 2017-05-01. dátumú előnézeti. Az örökölt metrikai meghatározásainak és a metrikák elérhető verzió 2014-04-01 API-val.
>
>

Az Azure figyelő REST API-kkal részletes útmutatást lásd: [Azure figyelő REST API-forgatókönyv](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Exportálás metrikák
Nyissa meg a **diagnosztikai beállítások** részen a **figyelő** lapra, és a metrikák exportálási beállítások megtekintéséhez. Választható ki metrikák (és diagnosztikai naplók) a Blob storage irányíthatja át Azure Event Hubs felé, vagy az OMS-be az ebben a cikkben korábban említett használati eseteket.

 ![Exportálási beállítások a Azure figyelő metrikák](./media/monitoring-overview-metrics/MetricsOverview3.png)

A Resource Manager-sablonok, konfigurálható [PowerShell](insights-powershell-samples.md), [Azure CLI](insights-cli-samples.md), vagy [REST API-k](https://msdn.microsoft.com/library/dn931943.aspx).

## <a name="take-action-on-metrics"></a>Művelet végrehajtása a metrikák
Értesítéseket vagy automatikus műveletek végrehajtása a metrikaadatokat, konfigurálhatja a riasztási szabályok vagy az automatikus skálázási beállításokat.

### <a name="configure-alert-rules"></a>A riasztási szabályok konfigurálása
Riasztási szabályok metrikák a konfigurálható. Ezek a riasztási szabályok ellenőrizheti, ha egy metrika elért egy bizonyos küszöböt. Nincsenek Azure-figyelő által kínált két mérőszám riasztási képességek.

Metrika riasztások: is majd e-mailben értesítse arról, vagy egy egyéni parancsfájlok futtatásához használható webhook érvényesítést. A webhook segítségével is konfigurálhatja a harmadik féltől származó terméket integrációja.

 ![Metrikák és az Azure-figyelő riasztási szabályok](./media/monitoring-overview-metrics/MetricsOverview4.png)

Majdnem valós idejű riasztások (előzetes verzió): ezek, hogy a számítógép több metrikákat, és a küszöbértékek erőforrás figyelése és majd értesítést kérhet a keresztül egy [művelet csoport](/monitoring-action-groups.md). További részletek Leran [közel valós idejű metrika riasztásokat Itt](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Automatikus skálázási az Azure erőforrások
Egyes Azure-erőforrások támogatja, a méretezés fel- vagy a munkaterhelések kezelésére több példány. Automatikus skálázás App Service (webalkalmazások), a virtuálisgép-méretezési csoportok és a klasszikus Azure-Felhőszolgáltatások vonatkozik. Konfigurálhatja az automatikus skálázási szabályok vertikális fel- vagy egy bizonyos metrika, amely hatással van a számítási feladatok ebbe a megadott küszöbértéket. További információkért lásd: [automatikus skálázás áttekintése](monitoring-overview-autoscale.md).

 ![Metrikák és az Azure a figyelő automatikus skálázás](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Támogatott szolgáltatások és
A támogatott szolgáltatások és azok metrikákat, részletes listáját megtekintheti [Azure figyelő metrikák--erőforrás típusonkénti támogatott metrikák](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Következő lépések
Ez a cikk hivatkozásaiban hivatkozik. Ezenkívül további információk:  

* [Az automatikus skálázás közös metrikák](insights-autoscale-common-metrics.md)
* [A riasztási szabályok létrehozása](insights-alerts-portal.md)
* [A Naplóelemzési az Azure storage naplóinak elemzése](../log-analytics/log-analytics-azure-storage.md)
