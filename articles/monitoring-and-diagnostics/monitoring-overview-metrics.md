---
title: A Microsoft Azure-ban mérőszámok áttekintése
description: Metrikák és a Microsoft Azure használati áttekintése
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d61ac48aa7c51bc4b215a7d56b1bbedfdc613f9f
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287556"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>A Microsoft Azure-ban mérőszámok áttekintése
Ez a cikk ismerteti a metrikák Mik a Microsoft Azure-ban előnyeiket, és hogyan használja őket.  

## <a name="what-are-metrics"></a>Mik azok a metrikák?
Az Azure Monitor lehetővé teszi, hogy értékes információkhoz juthat a teljesítmény és az Azure-ban a számítási feladatok állapotát a telemetriai adatok felhasználásához. Az Azure telemetriai adatokat a legfontosabb típus a legtöbb Azure-erőforrások által kibocsátott (más néven teljesítményszámlálók) metrikák. Az Azure Monitor konfigurálása és figyelése és hibaelhárítása a metrikák felhasználásához több módszert is biztosít.

## <a name="what-are-the-characteristics-of-metrics"></a>Mik azok a metrikák jellemzői?
Metrikák a következő jellemzőkkel rendelkeznek:

* Az összes metrikákhoz **perces gyakoriságot** (kivéve, ha egy metrika-definícióban másképpen megadva). Kap egy metrikaérték percenként az erőforrás, így közel valós idejű képet az állapot és az erőforrás állapotát.
* Metrikák **érhető el azonnal**. Nem kell részt, vagy további diagnosztikai beállítása.
* Elérheti **93 a napok** egyes metrika. Gyorsan megtekintheti a legutóbbi és havi trendeket a teljesítmény vagy az erőforrás állapotát.
* Bizonyos metrikák rendelkezhet nevű név-érték pár attribútumok **dimenziók**. Ezek lehetővé teszi további szegmens, és Fedezze fel a metrika kifejezőbb módon.

## <a name="what-can-you-do-with-metrics"></a>Tudja, mire a metrikák?
Metrikák engedélyezése, hogy a következő feladatokat végezheti el:


- Metrika konfigurálása **szabályt, amely elküld egy értesítést vagy tart a művelet automatikus riasztás** mikor a metrika átlépi-e a beállított küszöbértéket. Műveletek szabályozott [Műveletcsoportok](monitoring-action-groups.md). Példa műveletek közé tartoznak, e-mail, telefon és SMS-értesítések, kezdve egy runbookot, és több webhook hívása. **Automatikus skálázási** egy speciális automatizált műveleteket, amely lehetővé teszi a méretezhető, Ön felfelé és lefelé a terhelés kezelése érdekében, még ha nincs terhelés alatt tarthatja a költségeket alacsonyabb erőforrás. Konfigurálhatja az automatikus skálázási beállítás szabály horizontálisan le- illetve meg a küszöbérték átlépését metrika alapján.
- **Útvonal** az összes metrikát *Application Insights* vagy *Log Analytics* engedélyezéséhez az azonnali elemzések, search és egyéni riasztások a metrikák adatait az erőforrások közül. Metrikák is streamelheti egy *Eseményközpont*, lehetővé téve, és irányíthatja őket az Azure Stream Analytics vagy egyéni alkalmazásokról, közel valós idejű elemzés céljából. Beállíthat egy Eseményközpont streamelési a diagnosztikai beállítások használatával.
- **Archív** az erőforrás megfelelőségét, naplózás, vagy offline jelentéskészítésre teljesítmény vagy egészségügyi előzményeit.  A metrikák irányíthatja az Azure Blob storage, az erőforrás diagnosztikai beállításainak konfigurálásakor.
- Használja a **az Azure portal** felderítését, eléréséhez, és megtekintheti az összes metrikáit, válasszon ki egy erőforrást, és a metrikák egy diagramon jeleníti meg. Az erőforrás (például egy virtuális gép, a webhely vagy a logikai alkalmazás) a teljesítmény ehhez rögzítse a diagramot az irányítópulton követheti nyomon.  
- **Bővített analitika** vagy a teljesítmény vagy a használati trendeket az erőforrás jelent.
- **Lekérdezés** metrikák, a PowerShell-parancsmagok vagy többplatformos REST API használatával.
- **Felhasználását** a metrikák az új Azure Monitor REST API-kon keresztül.

  ![Továbbítani tudja a metrikák az Azure monitorban](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>A portálon keresztül hozzáférés-metrikák
Következő egy gyors útmutató bemutatja, hogyan hozhat létre egy metrikadiagram az Azure portal használatával.

### <a name="to-view-metrics-after-creating-a-resource"></a>Erőforrás létrehozása után metrikák megtekintése
1. Nyissa meg az Azure Portalt.
2. Hozzon létre egy Azure App Service-webhelyen.
3. Miután létrehozott egy webhely, nyissa meg a **áttekintése** a webhely paneljén.
4. Megtekintheti, hogy új metrikákat, mint egy **figyelés** csempére. Módosíthatja a csempét, majd további metrikák kiválasztásához.

   ![Az Azure Monitor erőforrás metrikáit](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Összes metrikát egyetlen helyen történő eléréséhez
1. Nyissa meg az Azure Portalt.
2. Lépjen az új **figyelő** fülre, és majd, és válassza ki a **metrikák** alatta lehetőséget.
3. Válassza ki az előfizetés, erőforráscsoport és az erőforrás nevét a legördülő listából.
4. Tekintse meg a rendelkezésre álló metrikák listáját. Ezután válassza ki a metrika iránt, és azt jeleníti meg.
5. Kitűzheti, az irányítópulton kattintson a jobb felső sarokban a PIN-kódot.

   ![Az Azure monitorban egyetlen helyen történő összes mérőszámok elérése](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> (Azure Resource Manager-alapú) virtuális gépek gazdagép-szintű metrikákat érhető el, és a virtuálisgép-méretezési csoportok további diagnosztikai beállítások nélkül. Windows és Linux-példányok új gazdagép-szintű metrikák érhetők el. Ezek a metrikák nem tévesztendő össze az operációsrendszer-vendégszintű metrikákat, hogy mikor kapcsolja be az Azure Diagnostics szolgáltatást a virtuális gépek vagy virtuálisgép-méretezési csoportok a hozzáférést a rendszer. Konfiguruje se Diagnostika kapcsolatos további információkért lásd: [Mi a Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Az Azure Monitor egy új metrikák elérhető előzetes verzióban érhető el diagram is tartalmaz. Ez lehetővé teszi a felhasználók számára, hogy egy diagramban különböző erőforrásokból származó metrikák átfedő. A felhasználók is jeleníti, szegmens, és mérőszámmal az új diagramkészítési élmény a többdimenziós metrikák szűrése. További [kattintson ide](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>A REST API-n keresztül hozzáférést metrikák
Az Azure-metrikák az Azure Monitor API-kon keresztül érhetők el. Két API-k, amelyek segítségével felderítése, és a mérőszámok elérése:

* Használja a [Azure Monitor metrikadefiníciók REST API-val](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) metrikák, és minden olyan dimenziót, szolgáltatásként elérhető listájának eléréséhez.
* Használja a [Azure Monitor-metrikák REST API](https://docs.microsoft.com/rest/api/monitor/metrics) szegmentálására, szűrésére és eléréséhez a tényleges mérőszámadatokat.

> [!NOTE]
> Ez a cikk ismerteti a mérőszámok segítségével a [új API-metrikák](https://docs.microsoft.com/rest/api/monitor/) az Azure-erőforrásokhoz. Az új metrikadefinícióinak és mérőszámok API-k API-verzió a 2018-01-01. Az örökölt metrikadefinícióinak beolvasása és a metrikák elérhető lesz a 2014-04-01-es verziójú API-val.
>
>

Részletes bemutató az Azure Monitor REST API-k használatával, lásd: [Azure Monitor REST API-t bemutató](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Metrikák exportálása
Megnyithatja a **diagnosztikai beállítások** panel a **figyelő** lapra, és az exportálási beállítások mérőszámok megtekintéséhez. Metrikák (és választhat a diagnosztikai naplók) legyen irányítva a Blob storage, az Azure Event Hubsba, vagy a Log Analytics az ebben a cikkben korábban említett használati esetek.

 ![Exportálási beállítások metrikák az Azure monitorban](./media/monitoring-overview-metrics/MetricsOverview3.png)

A Resource Manager-sablonok használatával konfigurálhat [PowerShell](insights-powershell-samples.md), [Azure CLI-vel](insights-cli-samples.md), vagy [REST API-k](https://msdn.microsoft.com/library/dn931943.aspx).

> [!NOTE]
> A többdimenziós metrikák diagnosztikai beállításokon keresztül történő küldése jelenleg nem támogatott. A dimenziókkal rendelkező metrikák egybesimított, egydimenziós metrikákként vannak exportálva, összesített dimenzióértékekkel.
>
> *Például*: Egy eseményközpont „Bejövő üzenetek” metrikája üzenetsoronként deríthető fel és ábrázolható. Ha azonban diagnosztikai beállításokon keresztül van exportálva, a metrika az eseményközpontban lévő összes üzenetsor összes bejövő üzeneteként lesz ábrázolva.
>
>

## <a name="take-action-on-metrics"></a>Művelet végrehajtása a metrikák
Értesítéseket fogadni, vagy automatizált műveletek végrehajtása a metrikaadatokat, konfigurálhatja úgy a riasztási szabályok vagy az automatikus méretezési beállítások.

### <a name="configure-alert-rules"></a>Riasztási szabályok konfigurálása
Riasztási szabályok metrikákra vonatkozóan is beállíthatja. Ezek a riasztási szabályok ellenőrizheti, ha egy metrika egy bizonyos küszöbértéket elért. Nincsenek Azure Monitor által kínált két metrikaalapú riasztási képességek.

Metrikákhoz kapcsolódó riasztások: azokat Önt e-mailen keresztül, vagy bármilyen egyéni szkript futtatásához használható webhook aktiválódik. A webhook használatával konfigurálja a külső termékintegrációk.

 ![Metrikák és a riasztási szabályok az Azure monitorban](./media/monitoring-overview-metrics/MetricsOverview4.png)

Újabb metrikariasztásokat is képes figyelni több mérőszámok és küszöbök, erőforrás, és értesítse Önt keresztül egy [műveletcsoport](monitoring-action-groups.md). Tudjon meg többet [itt újabb riasztások](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Automatikus skálázás az Azure erőforrások
Egyes Azure-erőforrások támogatja a skálázást fel- vagy több példányban is megtalálhatja a számítási feladatok kezelésére. Az automatikus méretezés App Service (webalkalmazások), a virtuálisgép-méretezési csoportok és a klasszikus Azure-Felhőszolgáltatások vonatkozik. Vertikális fel- vagy, ha egy bizonyos metrikát, amely hatással van a számítási feladatok átlép egy küszöbértéket, Ön által megadott automatikus skálázási szabályokat konfigurálhat. További információkért lásd: [automatikus méretezés áttekintése](monitoring-overview-autoscale.md).

 ![Metrikák és az Azure Monitor automatikus méretezés](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>További információ a támogatott szolgáltatások és metrikák
Megtekintheti a metrikákat, és a támogatott szolgáltatások részletes listáját [Azure Monitor-metrikák--erőforrás típus szerint a támogatott mérőszámok](monitoring-supported-metrics.md).

## <a name="next-steps"></a>További lépések
Tekintse meg az egész cikkben hivatkozásokat. Emellett további információ:  

* [Gyakori metrikák az automatikus skálázás](insights-autoscale-common-metrics.md)
* [Riasztási szabályok létrehozása](insights-alerts-portal.md)
* [A Log Analytics használatával az Azure storage-naplók elemzése](../log-analytics/log-analytics-azure-storage.md)
