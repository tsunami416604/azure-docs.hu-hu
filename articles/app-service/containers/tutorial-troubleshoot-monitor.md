---
title: 'Oktatóanyag: a Azure Monitor hibáinak megoldása'
description: Megtudhatja, hogyan segíti a Azure Monitor és a Log Analytics a App Service webalkalmazás figyelését. Azure Monitor maximalizálja a rendelkezésre állást azáltal, hogy átfogó megoldást kínál a környezetek figyelésére.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 2/28/2020
ms.openlocfilehash: c4aee7c7e78c6799874194697fb3bc9c4aa33b38
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227980"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Oktatóanyag: App Service-alkalmazás hibakeresése Azure Monitor

> [!NOTE]
> Az App Service-integráció Azure Monitor [előzetes](https://aka.ms/appsvcblog-azmon)verzióban érhető el.
>

A [Linuxon futó App Service](app-service-linux-intro.md) hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) maximalizálja az alkalmazások és szolgáltatások rendelkezésre állását és teljesítményét azáltal, hogy átfogó megoldást kínál a Felhőbeli és a helyszíni környezetek telemetria gyűjtésére, elemzésére és működésére.

Ez az oktatóanyag bemutatja, hogyan lehet elhárítani egy alkalmazást a [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)használatával. A minta alkalmazás a memóriához és a HTTP 500 hibákat okozó kódot tartalmaz, így diagnosztizálhatja és elháríthatja a problémát a Azure Monitor használatával.

Ha elkészült, egy App Serviceon futó minta-alkalmazás fog futni, amely a [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)integrált Linux rendszeren fut.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Webalkalmazás konfigurálása Azure Monitor
> * Konzol naplófájljainak küldése Log Analytics
> * A webalkalmazási hibák azonosítása és hibaelhárítása a naplók használatával

Az oktatóanyag lépései macOS, Linux és Windows rendszerre is vonatkoznak.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- [Azure-előfizetés](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Először is futtasson több parancsot helyileg az oktatóanyaghoz használni kívánt minta alkalmazás beállításához. A parancsok klónozása, Azure-erőforrások létrehozása, üzembe helyezési felhasználó létrehozása és az alkalmazás üzembe helyezése az Azure-ban. A rendszer kérni fogja az üzembe helyezési felhasználó létrehozásának részeként megadott jelszót. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>Azure Monitor konfigurálása (előzetes verzió)

### <a name="create-a-log-analytics-workspace"></a>Log Analytics munkaterület létrehozása

Most, hogy üzembe helyezte a minta alkalmazást a Azure App Servicera, a figyelési funkció konfigurálásával elháríthatja az alkalmazást, amikor problémák merülnek fel. A Azure Monitor Log Analytics munkaterületen tárolja a naplózási adattárakat. A munkaterület egy olyan tároló, amely adatokat és konfigurációs adatokat tartalmaz.

Ebben a lépésben egy Log Analytics munkaterületet hoz létre az alkalmazással való Azure Monitor konfigurálásához.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Azure Monitor Log Analytics esetében az adatok betöltését és az adatmegőrzést kell fizetnie.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása

A diagnosztikai beállításokkal bizonyos Azure-szolgáltatások metrikáit gyűjthetik be Azure Monitor naplókba más figyelési adatokkal való elemzéshez a naplók használatával. Ebben az oktatóanyagban engedélyezheti a webkiszolgálót és a standard kimeneti/hibanapló-naplókat. A naplózási típusok és leírások teljes listájáért lásd: [támogatott naplózási típusok](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types) .

A következő parancsok futtatásával diagnosztikai beállításokat hozhat létre a AppServiceConsoleLogs (normál kimenet/hiba) és a AppServiceHTTPLogs (webkiszolgáló-naplók) számára. Cserélje le az _\<app-name >_ és az _\<munkaterület neve >_ értékeit. 

> [!NOTE]
> Az első két parancs, `resourceID` és `workspaceID`, a `az monitor diagnostic-settings create` parancsban használandó változók. További információ a parancsról: [diagnosztikai beállítások létrehozása az Azure CLI használatával](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli) .
>

```bash

resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'

```

## <a name="troubleshoot-the-app"></a>Az alkalmazás hibáinak megoldása

Nyissa meg a következő címet: `http://<app-name>.azurewebsites.net`.

A ImageConverter `JPG` a tartalmazott képeket a `PNG`ba konvertálja. Ebben az oktatóanyagban egy hibát szándékosan helyeztek el a kódban. Ha elég képet választ, az alkalmazás HTTP 500 hibát hoz létre a lemezkép átalakítása során. Képzelje el, hogy ezt a forgatókönyvet nem tekintették a fejlesztési fázisban. A hiba megoldásához Azure Monitor fog használni.

### <a name="verify-the-app-is-works"></a>Az alkalmazás működésének ellenőrzése

A képek konvertálásához kattintson a `Tools` elemre, és válassza a `Convert to PNG`lehetőséget.

![Kattintson az eszközök elemre, és válassza a "Konvertálás PNG-re" lehetőséget.](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Válassza ki az első két rendszerképet, és kattintson a `convert`elemre. A konvertálás sikeresen megtörténik.

![Válassza ki az első két képet](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Az alkalmazás megszakítása

Most, hogy ellenőrizte az alkalmazást két rendszerkép sikeres konvertálásával, megpróbáljuk átalakítani az első öt képet.

![Első öt rendszerkép konvertálása](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Ez a művelet meghiúsul, és olyan `HTTP 500` hibát hoz létre, amelyet a fejlesztés során nem teszteltek.

![A konvertálás HTTP 500-es hibát eredményez](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Napló lekérdezés használata Azure Monitor naplók megtekintéséhez

Lássuk, milyen naplók érhetők el a Log Analytics munkaterületen. 

Kattintson erre a [log Analytics munkaterület hivatkozásra](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) a munkaterület Azure Portal való eléréséhez.

A Azure Portal válassza ki Log Analytics munkaterületét.

### <a name="log-queries"></a>Naplólekérdezések

A naplók lekérdezései segítségével teljes mértékben kihasználhatja Azure Monitor naplókban összegyűjtött adatok értékét. A naplók segítségével azonosíthatja a naplókat mind a AppServiceHTTPLogs, mind a AppServiceConsoleLogs. A naplók lekérdezésével kapcsolatos további információkért tekintse meg a [napló lekérdezésének áttekintése](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) című témakört.

### <a name="view-appservicehttplogs-with-log-query"></a>AppServiceHTTPLogs megtekintése a log lekérdezéssel

Most, hogy elértük az alkalmazást, tekintsük át a HTTP-kérésekhez tartozó, a `AppServiceHTTPLogs`ban található adatmennyiséget.

1. Kattintson `Logs` a bal oldali navigációs sávon.

![Log Anlytics Worksace-naplók](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Keresse meg `appservice`, majd kattintson duplán a `AppServiceHTTPLogs`elemre.

![Log Analytics-munkaterület táblái](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Kattintson a `Run` gombra.

![Log Analytics munkaterület App Service HTTP-naplók](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

A `AppServiceHTTPLogs` lekérdezés az elmúlt 24 órában az összes kérelmet visszaadja. A `ScStatus` oszlop a HTTP-állapotot tartalmazza. A `HTTP 500` hibák diagnosztizálásához korlátozza a `ScStatus` 500-re, és futtassa a lekérdezést az alábbiak szerint:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>AppServiceConsoleLogs megtekintése a log lekérdezéssel

Most, hogy megerősítette a HTTP-500, vessünk egy pillantást az alkalmazás szabványos kimenetére/hibáira. Ezek a naplók a "AppServiceConsoleLogs" címen találhatók.

(1) a `+` gombra kattintva hozzon létre egy új lekérdezést. 

(2) kattintson duplán a `AppServiceConsoleLogs` táblára, és kattintson az `Run`elemre. 

Mivel az öt rendszerkép konvertálása kiszolgálói hibákba ütközik, megtekintheti, hogy az alkalmazás hibákat is ír-e, ha az alábbi ábrán látható módon szűri `ResultDescription` a hibákat:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

A `ResultDescription` oszlopban a következő hibaüzenet jelenik meg:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>AppServiceHTTPLogs és AppServiceConsoleLogs csatlakoztatása

Most, hogy már azonosította a HTTP-500 és a standard hibákat, meg kell erősítenie, hogy van-e az üzenetek közötti korreláció. Ezután összekapcsolja a táblákat az időbélyegző alapján, `TimeGenerated`.

> [!NOTE]
> A rendszer előkészített egy lekérdezést, amely a következő műveleteket végzi el:
>
> - HTTPLogs szűrők 500 hibákhoz
> - A konzol naplófájljainak lekérdezése
> - Összekapcsolja a táblákat `TimeGenerated`
>

Futtassa az alábbi lekérdezést:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

A `ResultDescription` oszlopban a következő hibaüzenet jelenik meg a webkiszolgáló hibáival megegyező időpontban:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

Az üzenetek állapotának memóriája kimerült a `process.php`20. sorában. Ezzel megerősítette, hogy az alkalmazás hibát jelzett a HTTP 500-hiba során. Vessünk egy pillantást a probléma azonosítására szolgáló kódra.

## <a name="identify-the-error"></a>A hiba azonosítása

A helyi könyvtárban nyissa meg a `process.php`, és tekintse meg a 20. sort. 

```php
imagepng($imgArray[$x], $filename);
```

A `$imgArray[$x]`első argumentuma az összes jpg (memóriában) való átalakítást igénylő változó. `imagepng` azonban csak a konvertált lemezképre van szükség, és nem minden lemezképre. A lemezképek előzetes betöltése nem szükséges, és okozhatja a memória kimerülését, ami a HTTP-500 vezethet. Frissítse a kódot a lemezképek igény szerinti betöltéséhez, és ellenőrizze, hogy a probléma feloldódik-e. Következő lépésként javítani fogja a kódot a memória problémájának megoldásához.

## <a name="fix-the-app"></a>Javítsa ki az alkalmazást

### <a name="update-locally-and-redeploy-the-code"></a>A kód frissítése helyileg és ismételt üzembe helyezése

A következő módosításokat hajtja végre `process.php` a memória kimerülésének kezeléséhez:

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "Load images on-demand in process.php"
git push azure master
```

### <a name="browse-to-the-azure-app"></a>Tallózással keresse meg az Azure-alkalmazást

Nyissa meg a következő címet: `http://<app-name>.azurewebsites.net`. 

A lemezképek konvertálása nem eredményezheti a HTTP 500 hibákat.

![Az Azure App Service-ben futó PHP-alkalmazás](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

Törölje a diagnosztikai beállítást a következő paranccsal:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Webalkalmazás konfigurálása Azure Monitor
> * Naplók elküldése a Log Analyticsba
> * A webalkalmazások hibáinak azonosítására és elhárítására használt naplózási lekérdezések

## <a name="nextsteps"></a> Következő lépések
* [Naplók lekérdezése Azure Monitor](../../azure-monitor/log-query/log-query-overview.md)
* [Hibaelhárítási Azure App Service a Visual Studióban](../troubleshoot-dotnet-visual-studio.md)
* [Alkalmazás-naplók elemzése a HDInsight-ben](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
