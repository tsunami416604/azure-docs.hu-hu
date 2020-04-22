---
title: Diagnosztikai naplózás engedélyezése
description: Ismerje meg, hogyan engedélyezheti a diagnosztikai naplózást, és hogyan adhat hozzá műszereket az alkalmazáshoz, valamint hogyan férhet hozzá az Azure által naplózott információkhoz.
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769986"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Alkalmazások diagnosztikanaplózásának engedélyezése az Azure App Service-ben
## <a name="overview"></a>Áttekintés
Az Azure beépített diagnosztikát biztosít az [App Service-alkalmazások](overview.md)hibakereséséhez. Ebben a cikkben megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást, és hogyan adhat hozzá műszereket az alkalmazáshoz, valamint hogyan érheti el az Azure által naplózott adatokat.

Ez a cikk az [Azure Portalon](https://portal.azure.com) és az Azure CLI-t használja a diagnosztikai naplók használatával. A Visual Studio használatával végzett diagnosztikai naplók használatáról az [Azure hibaelhárítása a Visual Studióban](troubleshoot-dotnet-visual-studio.md)című témakörben talál további információt.

> [!NOTE]
> A jelen cikkben található naplózási utasítások mellett az Azure Monitoring új, integrált naplózási funkcióval is rendelkezik. Erről a funkcióról további tudnivalókat talál a Naplók küldése az [Azure Monitornak (előzetes verzió)](#send-logs-to-azure-monitor-preview) című szakaszban. 
>
>

|Típus|Platform|Hely|Leírás|
|-|-|-|-|
| Alkalmazásnaplózás | Windows, Linux | App Service fájlrendszer és/vagy Az Azure Storage blobok | Naplózza az alkalmazáskód által létrehozott üzeneteket. Az üzeneteket a kiválasztott webes keretrendszer vagy közvetlenül az alkalmazáskódból hozhatja létre a nyelv szabványos naplózási mintájával. Minden üzenethez a következő kategóriák vannak hozzárendelve: **Kritikus**, **Hiba**, **Figyelmeztetés**, **Információ**, **Hibakeresés**és **Nyomkövetés**. Az alkalmazásnaplózás engedélyezésekor megadhatja, hogy milyen részletes enyelegyen a naplózás.|
| Webkiszolgáló naplózása| Windows | App Service fájlrendszer vagy Az Azure Storage blobok| Nyers HTTP-kérelem adatok [w3C kiterjesztett naplófájl formátumban.](/windows/desktop/Http/w3c-logging) Minden naplóüzenet olyan adatokat tartalmaz, mint a HTTP-módszer, az erőforrás URI-ja, az ügyfél IP-címe, az ügyfélport, a felhasználói ügynök, a válaszkód és így tovább. |
| Részletes hibaüzenetek| Windows | App Service fájlrendszer | A *.htm* hibalapok másolatai, amelyeket az ügyfélböngészőnek küldtek volna. Biztonsági okokból a részletes hibaoldalakat nem kell elküldeni az éles környezetben lévő ügyfeleknek, de az App Service minden alkalommal mentheti a hibaoldalt, amikor 400-as vagy nagyobb HTTP-kóddal járó alkalmazáshiba történik. A lap olyan információkat tartalmazhat, amelyek segíthetnek meghatározni, hogy a kiszolgáló miért adja vissza a hibakódot. |
| Sikertelen kérelmek nyomkövetése | Windows | App Service fájlrendszer | A sikertelen kérelmekrészletes nyomon követésére vonatkozó információk nyomon követése, beleértve a kérelem feldolgozásához használt IIS-összetevők és az egyes összetevőkben eltöltött idő nyomon követését. Ez akkor hasznos, ha javítani szeretné a webhely teljesítményét, vagy el szeretne különíteni egy adott HTTP-hibát. Minden sikertelen kérelemhez egy mappa jön létre, amely tartalmazza az XML-naplófájlt, és az XSL stíluslapot a naplófájl megtekintéséhez. |
| Központi telepítés naplózása | Windows, Linux | App Service fájlrendszer | Naplók, ha tartalmat tesz közzé egy alkalmazásban. A központi telepítés naplózása automatikusan történik, és nincsenek konfigurálható beállítások a központi telepítés naplózásához. Ez segít meghatározni, hogy miért egy központi telepítés sikertelen. Ha például egyéni [központi telepítési parancsfájlt](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)használ, a központi telepítési naplózás segítségével megállapíthatja, hogy miért hibásak a parancsfájl. |

> [!NOTE]
> Az App Service egy dedikált, interaktív diagnosztikai eszközt biztosít az alkalmazás hibaelhárításához. További információ: [Azure App Service diagnostics overview](overview-diagnostics.md).
>
> Ezenkívül más Azure-szolgáltatásokkal is javíthatja az alkalmazás naplózási és figyelési képességeit, például az [Azure Monitort.](../azure-monitor/app/azure-web-apps.md)
>

## <a name="enable-application-logging-windows"></a>Alkalmazásnaplózás engedélyezése (Windows)

> [!NOTE]
> A blobstorage alkalmazásnaplózása csak az App Service-szolgáltatással azonos régióban használhatja a tárfiókokat.

Ha engedélyezni szeretné az alkalmazásnaplózást az Azure Portalon a [Windows-alkalmazások](https://portal.azure.com)számára, keresse meg az alkalmazást, és válassza **az App Service-naplók lehetőséget.**

Válassza **a Be** lehetőséget az **Alkalmazásnaplózás (fájlrendszer)** vagy **az Alkalmazásnaplózás (Blob)** vagy mindkettő esetén. 

A **Fájlrendszer** beállítás ideiglenes hibakeresésre szolgál, és 12 órán belül kikapcsol. A **Blob** beállítás hosszú távú naplózás, és szüksége van egy blob storage-tároló naplók írásához.  A **Blob** beállítás további információkat is tartalmaz a naplóüzenetekben, például a naplóüzenet`InstanceId`( eredetforrás virtuálisgép-példányának azonosítóját ( ), a szálazonosítót (`Tid`) és egy részletesebb időbélyeget ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks).

> [!NOTE]
> Jelenleg csak .NET alkalmazásnaplók írhatók a blob storage.Currently only .NET application logs can be written to the blob storage. Java, PHP, Node.js, Python alkalmazásnaplók csak az App Service fájlrendszeren tárolhatók (kódmódosítások nélkül a naplók külső tárolóba való írásához).
>
> Ha [újragenerálja a tárfiók hozzáférési kulcsait,](../storage/common/storage-create-storage-account.md)a frissített hozzáférési kulcsok használatához alaphelyzetbe kell állítania a megfelelő naplózási konfigurációt. Ehhez tegye a következőket:
>
> 1. A **Konfigurálás** lapon állítsa a megfelelő naplózási szolgáltatást **Ki**beállításra. Mentse el a beállítást.
> 2. Engedélyezze a naplózást a tárfiók blobújra. Mentse el a beállítást.
>
>

Válassza ki a **Szint**, vagy a naplózni kívánt részletek szintjét. Az alábbi táblázat az egyes szintekben szereplő naplókategóriákat mutatja be:

| Szint | Belefoglalt kategóriák |
|-|-|
|**Letiltva** | None |
|**Hiba** | Hiba, kritikus |
|**Figyelmeztetés** | Figyelmeztetés, Hiba, Kritikus|
|**Információ** | Információ, Figyelmeztetés, Hiba, Kritikus|
|**Részletes** | Nyomkövetés, hibakeresés, információ, figyelmeztetés, hiba, kritikus (minden kategória) |

Ha végzett, válassza a **Mentés gombot.**

## <a name="enable-application-logging-linuxcontainer"></a>Alkalmazásnaplózás engedélyezése (Linux/Tároló)

Ha engedélyezni szeretné az alkalmazások naplózását linuxos alkalmazásokhoz vagy egyéni tárolóalkalmazásokhoz az [Azure Portalon,](https://portal.azure.com)keresse meg az alkalmazást, és válassza **az App Service-naplók lehetőséget.**

Az **alkalmazásnaplózásban**válassza a **Fájlrendszer**lehetőséget.

A **Kvóta (MB)** csoportban adja meg az alkalmazásnaplók lemezkvótáját. A **Megőrzési időszak (Napok)** beállításban állítsa be, hogy a naplókhány napig őrizhetők meg.

Ha végzett, válassza a **Mentés gombot.**

## <a name="enable-web-server-logging"></a>Webkiszolgáló naplózásának engedélyezése

Ha engedélyezni szeretné a Windows-alkalmazások webkiszolgáló-naplózását az [Azure Portalon,](https://portal.azure.com)keresse meg az alkalmazást, és válassza **az App Service-naplók lehetőséget.**

Webkiszolgáló **naplózásához**válassza **a Storage lehetőséget** a blobstorage-on lévő naplók tárolására, vagy a **Fájlrendszer** lehetőséget az App Service fájlrendszernaplóinak tárolására. 

A **Megőrzési időszak (Napok)** beállításban állítsa be, hogy a naplókhány napig őrizhetők meg.

> [!NOTE]
> Ha [újragenerálja a tárfiók hozzáférési kulcsait,](../storage/common/storage-create-storage-account.md)a frissített kulcsok használatához alaphelyzetbe kell állítania a megfelelő naplózási konfigurációt. Ehhez tegye a következőket:
>
> 1. A **Konfigurálás** lapon állítsa a megfelelő naplózási szolgáltatást **Ki**beállításra. Mentse el a beállítást.
> 2. Engedélyezze a naplózást a tárfiók blobújra. Mentse el a beállítást.
>
>

Ha végzett, válassza a **Mentés gombot.**

## <a name="log-detailed-errors"></a>Részletes hibák naplózása

Ha menteni szeretné a hibalapot vagy a Windows-alkalmazások sikertelen nyomkövetését az [Azure Portalon,](https://portal.azure.com)keresse meg az alkalmazást, és válassza **az App Service-naplók**lehetőséget.

A **Részletes hibanaplózás** vagy **a Sikertelen kérelmek nyomkövetése csoportban**válassza **a Be**lehetőséget, majd a **Mentés**lehetőséget.

Mindkét típusú naplók az App Service fájlrendszer tárolja. Legfeljebb 50 hiba (fájl/mappa) őrződik meg. Ha a HTML-fájlok száma meghaladja az 50-et, a legrégebbi 26 hiba automatikusan törlődik.

## <a name="add-log-messages-in-code"></a>Naplóüzenetek hozzáadása a kódhoz

Az alkalmazáskódban a szokásos naplózási lehetőségek segítségével naplóüzeneteket küldhet az alkalmazásnaplókba. Például:

- ASP.NET alkalmazások a [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) osztály segítségével naplózhatják az információkat az alkalmazás diagnosztikai naplójába. Például:

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- Alapértelmezés szerint a ASP.NET Core a [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) naplózási szolgáltatót használja. További információt [az Azure-beli core naplózás ASP.NET című témakörben talál.](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)

## <a name="stream-logs"></a>Naplók streamelése

A naplók valós idejű streamelése előtt engedélyezze a kívánt naplótípust. A .txt, .log vagy .htm végződésű fájlokba írt, a */LogFiles* könyvtárban (d:/home/logfiles) tárolt minden információt az App Service streamel.

> [!NOTE]
> Bizonyos típusú naplózási puffer írása a naplófájlba, ami az adatfolyamban nem sorrendben lévő eseményeket eredményezhet. Előfordulhat például, hogy egy alkalmazásnapló-bejegyzés, amely akkor következik be, amikor egy felhasználó felkeres egy oldalt, és megjelenik az adatfolyamban, mielőtt az oldalkéréshez megfelelő HTTP-naplóbejegyzés jelenik meg.
>

### <a name="in-azure-portal"></a>Az Azure Portalon

Ha naplókat szeretne streamelni az [Azure Portalon,](https://portal.azure.com)keresse meg az alkalmazást, és válassza **a Naplófolyam**lehetőséget. 

### <a name="in-cloud-shell"></a>A felhőhéjban

A [Cloud Shellben](../cloud-shell/overview.md)élő naplók streameléséhez használja a következő parancsot:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

Adott események, például hibák szűréséhez használja a **--Filter** paramétert. Például:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
Adott naplótípusok, például a HTTP szűréséhez használja a **--Path** paramétert. Például:

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>A helyi terminálon

A helyi konzolon lévő naplók streameléséhez telepítse az [Azure CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli) és [jelentkezzen be a fiókjába.](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) Miután bejelentkezett, követte a [Cloud Shell utasításait](#in-cloud-shell)

## <a name="access-log-files"></a>Hozzáférés naplófájlok

Ha konfigurálja az Azure Storage blobok beállítást egy naplótípushoz, olyan ügyféleszközre van szüksége, amely együttműködik az Azure Storage-szal. További információ: [Azure Storage Client Tools](../storage/common/storage-explorers.md).

Az App Service fájlrendszerben tárolt naplók esetében a legegyszerűbb módja a ZIP-fájl letöltése a böngészőben a következő helyen:

- Linux/tároló alkalmazások:`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows-alkalmazások:`https://<app-name>.scm.azurewebsites.net/api/dump`

Linux/container alkalmazások esetében a ZIP-fájl konzolkimeneti naplókat tartalmaz mind a docker-gazdagéphez, mind a docker-tárolóhoz. Egy kicsinyített alkalmazás esetén a ZIP-fájl minden példányhoz egy naplókészletet tartalmaz. Az App Service fájlrendszerben ezek a naplófájlok a */home/LogFiles* könyvtár tartalmát képezik.

Windows-alkalmazások esetén a ZIP-fájl tartalmazza az App Service *fájlrendszerD:\Home\LogFiles* könyvtárának tartalmát. Ez a következő szerkezet:

| Napló típusa | Címtár | Leírás |
|-|-|-|
| **Alkalmazásnaplók** |*/LogFiles/Alkalmazás/* | Egy vagy több szövegfájlt tartalmaz. A naplóüzenetek formátuma a használt naplózási szolgáltatótól függ. |
| **Sikertelen kérelmek nyomkövetései** | */LogFiles/W3SVC#########/* | XML-fájlokat és XSL-fájlt tartalmaz. A formázott XML-fájlokat a böngészőben tekintheti meg. |
| **Részletes hibanaplók** | */LogFiles/DetailedErrors/* | HTM hibafájlokat tartalmaz. A HTM fájlokat a böngészőben tekintheti meg.<br/>A sikertelen kérelmek nyomkövetései megtekintésének másik módja, ha a portálon az alkalmazáslapjára navigál. A bal oldali menüben válassza a **Problémák diagnosztizálása és megoldása**parancsot, majd keresse meg a **Sikertelen kérelemkövetési naplók kifejezést,** majd kattintson az ikonra a kívánt nyomkövetés tallózásához és megtekintéséhez. |
| **Webkiszolgáló-naplók** | */LogFiles/http/RawLogs/* | [W3C kiterjesztett naplófájlformátummal](/windows/desktop/Http/w3c-logging)formázott szövegfájlokat tartalmaz. Ez az információ szövegszerkesztővel vagy olyan segédprogrammal olvasható, mint [a Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619).<br/>Az App Service nem `s-computername` `s-ip`támogatja `cs-version` a , vagy mezőket. |
| **Telepítési naplók** | */LogFiles/Git/* és */deployments/* | A belső központi telepítési folyamatok által létrehozott naplókat, valamint a Git-telepítések naplóit tartalmazza. |

## <a name="send-logs-to-azure-monitor-preview"></a>Naplók küldése az Azure Monitornak (előzetes verzió)

Az új [Azure Monitor-integrációval](https://aka.ms/appsvcblog-azmon) [diagnosztikai beállításokat (előzetes verzió) hozhat létre](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting) a storage-fiókok, az eseményközpontok és a Log Analytics számára. 

> [!div class="mx-imgBorder"]
> ![Diagnosztikai beállítások (előzetes verzió)](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>Támogatott naplótípusok

Az alábbi táblázat a támogatott naplótípusokat és leírásokat tartalmazza: 

| Napló típusa | Windows-támogatás | Linux (Docker) támogatása | Leírás |
|-|-|-|
| AppServiceConsoleLogs | Tba | Igen | Normál kimenet és standard hiba |
| AppServiceHTTPLogs | Igen | Igen | Webkiszolgáló-naplók |
| AppServiceEnvironmentPlatformLogs | Igen | Igen | App Service-környezet: méretezés, konfigurációs módosítások és állapotnaplók|
| AppServiceAuditLogs | Igen | Igen | Bejelentkezési tevékenység FTP-n és Kudu-n keresztül |
| AppServiceFileAuditLogs | Igen | TBD | Fájlváltozások FTP-n és Kudu-n keresztül |
| AppServiceAppLogs | Tba | Java SE & Tomcat | Alkalmazásnaplók |

## <a name="next-steps"></a><a name="nextsteps"></a>Következő lépések
* [Lekérdezési naplók az Azure Monitorsegítségével](../azure-monitor/log-query/log-query-overview.md)
* [Az Azure App Service figyelése](web-sites-monitor.md)
* [Az Azure App Service hibáinak elhárítása a Visual Studióban](troubleshoot-dotnet-visual-studio.md)
* [Alkalmazásnaplók elemzése a HDInsightban](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
