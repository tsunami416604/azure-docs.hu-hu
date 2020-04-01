---
title: 'Oktatóanyag: Hibaelhárítás az Azure Monitorsegítségével'
description: Ismerje meg, hogy az Azure Monitor és a Log Analytics hogyan segíti az App Service webalkalmazás figyelésében. Az Azure Monitor maximalizálja a rendelkezésre állást azáltal, hogy átfogó megoldást biztosít a környezetek figyelésére.
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 2/28/2020
ms.openlocfilehash: d543a9364311b2cf5f0258fbf9185d27bb1bfb2f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78399536"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>Oktatóanyag: Egy App Service-alkalmazás hibaelhárítása az Azure Monitorsegítségével

> [!NOTE]
> Az Azure Monitor és az App Service [integrációja előzetes verzióban](https://aka.ms/appsvcblog-azmon)érhető el.
>

[App Service linuxos](app-service-linux-intro.md) nyújt egy jól skálázható, önjavító web hosting szolgáltatás segítségével a Linux operációs rendszer. [Az Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) maximalizálja az alkalmazások és szolgáltatások rendelkezésre állását és teljesítményét azáltal, hogy átfogó megoldást kínál a felhőbeli és helyszíni környezetekt tartalmazó telemetriai adatok gyűjtésére, elemzésére és a telemetriai adatokra való reagálásra.

Ez az oktatóanyag bemutatja, hogyan háríthatja el az alkalmazásokat az [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)használatával. A mintaalkalmazás olyan kódot tartalmaz, amely a memória kimerítésére és a HTTP 500-as hibák rakoncáta, így diagnosztizálhatja és javíthatja a problémát az Azure Monitor használatával.

Ha elkészült, egy mintaalkalmazás fut az [Azure Monitorba](https://docs.microsoft.com/azure/azure-monitor/overview)integrált Linux-alkalmazásszolgáltatáson.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Webalkalmazás konfigurálása az Azure Monitorsegítségével
> * Konzolnaplók küldése a Log Analytics szolgáltatásba
> * A webalkalmazás-hibák azonosítása és elhárítása naplólekérdezésekkel

Az oktatóanyag lépései macOS, Linux és Windows rendszerre is vonatkoznak.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következőkre lesz szükség:

- [Azure-előfizetés](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

Először több parancsot futtathelyileg egy mintaalkalmazás beállításához, amelyet ezzel az oktatóanyaggal használhat. A parancsok klónoznak egy mintaalkalmazást, azure-erőforrásokat hoznak létre, központi telepítési felhasználót hoznak létre, és telepítik az alkalmazást az Azure-ba. A rendszer kérni fogja a telepítési felhasználó létrehozásának részeként megadott jelszót. 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure master
```

## <a name="configure-azure-monitor-preview"></a>Az Azure Monitor konfigurálása (előzetes verzió)

### <a name="create-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása

Most, hogy telepítette a mintaalkalmazást az Azure App Service szolgáltatásba, konfigurálja a figyelési képességet az alkalmazás hibaelhárításához, ha problémák merülnek fel. Az Azure Monitor a naplóadatokat egy Log Analytics-munkaterületen tárolja. A munkaterület olyan tároló, amely adatokat és konfigurációs adatokat tartalmaz.

Ebben a lépésben hozzon létre egy Log Analytics-munkaterületet az Azure Monitor alkalmazással való konfigurálásához.

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Az Azure Monitor Log Analytics, az adatok betöltése és az adatmegőrzés.](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása

A diagnosztikai beállítások segítségével bizonyos Azure-szolgáltatások metrikákat gyűjthet az Azure Monitor Logs-ba, hogy a naplólekérdezések használatával más figyelési adatokkal elemezhessék. Ebben az oktatóanyagban engedélyezi a webkiszolgálót és a szabványos kimeneti/hibanaplókat. A naplótípusok és -leírások teljes listáját a [támogatott naplótípusok](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#supported-log-types) és leírások teljes listájában láthatja.

A következő parancsokat futtatja az AppServiceConsoleLogs (standard kimenet/hiba) és az AppServiceHTTPLogs (webkiszolgálói naplók) diagnosztikai beállításainak létrehozásához. Cserélje le _ \<az alkalmazásnév->_ és _ \<a munkaterület-név->_ az értékekre. 

> [!NOTE]
> Az első két `resourceID` `workspaceID`parancs és a , a `az monitor diagnostic-settings create` parancsban használandó változók. A parancsról további információt a Diagnosztikai beállítások létrehozása az [Azure CLI használatával](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-using-azure-cli) című témakörben talál.
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

## <a name="troubleshoot-the-app"></a>Az alkalmazás hibáinak elhárítása

Nyissa meg a következő címet: `http://<app-name>.azurewebsites.net`.

A mintaalkalmazás, az ImageConverter `JPG` a `PNG`benne lévő képeket a rendszerből konvertálja. A hiba szándékosan került a kódot az oktatóanyag. Ha elegendő képet választ ki, az alkalmazás HTTP 500-as hibát okoz a képkonvertálás során. Képzeld el, ez a forgatókönyv nem volt figyelembe véve a fejlesztési fázisban. Az Azure Monitor segítségével hárítja el a hibát.

### <a name="verify-the-app-is-works"></a>Az alkalmazás működésének ellenőrzése

A képek konvertálásához kattintson a gombra, `Tools` és válassza a lehetőséget. `Convert to PNG`

![Kattintson az "Eszközök" menüpontra, és válassza a "Konvertálás PNG-vé" lehetőséget.](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

Jelölje ki az első `convert`két képet, és kattintson a gombra. Ez sikeresen konvertálható.

![Az első két kép kijelölése](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>Az alkalmazás megszakítása

Most, hogy két kép sikeres konvertálásával ellenőrizte az alkalmazást, megpróbáljuk konvertálni az első öt képet.

![Az első öt kép konvertálása](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

Ez a művelet `HTTP 500` sikertelen, és olyan hibát hoz létre, amelyet nem teszteltek a fejlesztés során.

![A konvertálás HTTP 500-as hibát eredményez](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>Az Azure Monitor naplóinak megtekintése naplólekérdezéssel

Lássuk, milyen naplók érhetők el a Log Analytics-munkaterületen. 

Kattintson erre a [Log Analytics-munkaterületi hivatkozásra](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces) az Azure Portalon lévő munkaterület eléréséhez.

Az Azure Portalon válassza ki a Log Analytics-munkaterületet.

### <a name="log-queries"></a>Naplólekérdezések

A naplólekérdezések segítségével teljes mértékben kihasználhatja az Azure Monitor naplókban gyűjtött adatok értékét. Naplólekérdezések segítségével azonosíthatja a naplókat mind AppServiceHTTPLogs és AppServiceConsoleLogs. A [naplólekérdezésekkel kapcsolatos](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) további információkért tekintse meg a naplólekérdezés áttekintését.

### <a name="view-appservicehttplogs-with-log-query"></a>AppServiceHTTPLog-naplók megtekintése naplólekérdezéssel

Most, hogy elértük az alkalmazást, tekintsük meg a HTTP-kérelmekhez kapcsolódó adatokat, amelyek a `AppServiceHTTPLogs`ban találhatók.

1. Kattintson `Logs` a bal oldali navigációra.

![Napló anlytics worksace naplók](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. Keresse `appservice` meg a `AppServiceHTTPLogs`keresést, és kattintson duplán a lehetőségre.

![Naplóelemzési munkaterületi táblák](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. Kattintson a `Run` gombra.

![Log Analytics-munkaterületi alkalmazásszolgáltatás HTTP-naplói](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

A `AppServiceHTTPLogs` lekérdezés az elmúlt 24 órában az összes kérelmet visszaadja. Az `ScStatus` oszlop a HTTP-állapotot tartalmazza. A hibák `HTTP 500` diagnosztizálásához `ScStatus` korlátozza a lekérdezés500-re, és futtassa a lekérdezést az alábbi módon:

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>AppServiceConsoleLogs megtekintése naplólekérdezéssel

Most, hogy megerősítette a HTTP 500s, vessünk egy pillantást a standard kimeneti / hibák az alkalmazásból. Ezek a naplók az "AppServiceConsoleLogs" területen találhatók.

(1) `+` Kattintson ide, ha új lekérdezést szeretne létrehozni. 

(2) Kattintson `AppServiceConsoleLogs` duplán `Run`a táblázatra, és kattintson a gombra. 

Mivel az öt kép konvertálása kiszolgálói hibákat eredményez, az `ResultDescription` alábbi módon láthatja, hogy az alkalmazás hibákat is ír-e a hibák szűrésével:

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

Az `ResultDescription` oszlopban a következő hiba jelenik meg:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>Csatlakozás AppServiceHTTPLogs és AppServiceConsoleLogs

Most, hogy azonosította a HTTP 500-as és a normál hibákat, meg kell erősítenie, hogy van-e összefüggés az üzenetek között. Ezután az időbélyegző alapján összeilleszti `TimeGenerated`a táblázatokat.

> [!NOTE]
> A következő lekérdezést készítettük elő:
>
> - HTTPLogs szűrők 500 hibához
> - Lekérdezések konzolnaplók
> - Összeáll a táblákkal`TimeGenerated`
>

Futtassa az alábbi lekérdezést:

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

Az `ResultDescription` oszlopban a következő hiba jelenik meg a webkiszolgálóhibáival egy időben:

```
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

Az üzenet szerint a memória kimerítette a 20-as `process.php`vonalon. Most megerősítette, hogy az alkalmazás hibát okozott a HTTP 500-as hiba során. Vessünk egy pillantást a kódot, hogy azonosítsa a problémát.

## <a name="identify-the-error"></a>A hiba azonosítsa

A helyi könyvtárban `process.php` nyissa meg a és nézze meg a 20-as sort. 

```php
imagepng($imgArray[$x], $filename);
```

Az első `$imgArray[$x]`argumentum egy változó, amely az összes átalakítást igénylő (memórián belüli) JPEG-eket tartalmazza. Azonban `imagepng` csak a kép konvertálására van szüksége, és nem minden képre. A képek előzetes betöltése nem szükséges, és a memória kimerülését okozhatja, ami HTTP 500-as évekhez vezethet. Frissítsük a kódot a képek igény szerinti betöltéséhez, hogy lássuk, megoldja-e a problémát. Ezután javítani fogja a kódot a memóriaprobléma megoldására.

## <a name="fix-the-app"></a>Az alkalmazás javítása

### <a name="update-locally-and-redeploy-the-code"></a>A kód frissítése helyileg és ismételt üzembe helyezése

A memóriakimerülés `process.php` kezeléséhez a következő módosításokat hajthatja végre:

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

### <a name="browse-to-the-azure-app"></a>Tallózás az Azure alkalmazásban

Nyissa meg a következő címet: `http://<app-name>.azurewebsites.net`. 

A képek konvertálása nem okozhatja tovább a HTTP 500-as hibákat.

![Az Azure App Service-ben futó PHP-alkalmazás](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

Törölje a diagnosztikai beállítást a következő paranccsal:

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Webalkalmazás konfigurálása az Azure Monitorsegítségével
> * Naplók küldése a Log Analytics szolgáltatásba
> * A webalkalmazás-hibák azonosítására és elhárítására használt naplólekérdezések

## <a name="next-steps"></a><a name="nextsteps"></a>Következő lépések
* [Lekérdezési naplók az Azure Monitorsegítségével](../../azure-monitor/log-query/log-query-overview.md)
* [Az Azure App Service hibáinak elhárítása a Visual Studióban](../troubleshoot-dotnet-visual-studio.md)
* [Alkalmazásnaplók elemzése a HDInsightban](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
