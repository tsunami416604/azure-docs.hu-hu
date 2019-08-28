---
title: Diagnosztikai naplózás engedélyezése alkalmazások számára – Azure App Service
description: Megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást, és hogyan adhat hozzá rendszerállapotot az alkalmazáshoz, valamint hogyan érheti el az Azure által naplózott információkat.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: af6d8b61c5d49ae219e90513abb93185f957222e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074056"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Diagnosztikai naplózás engedélyezése a Azure App Serviceban lévő alkalmazásokhoz
## <a name="overview"></a>Áttekintés
Az Azure beépített diagnosztika segítségével segíti az [app Service-alkalmazások](https://go.microsoft.com/fwlink/?LinkId=529714)hibakeresését. Ebből a cikkből megtudhatja, hogyan engedélyezheti a diagnosztikai naplózást, és hogyan adhat hozzá rendszerállapotot az alkalmazáshoz, valamint hogyan érheti el az Azure által naplózott információkat.

Ez a cikk a [Azure Portal](https://portal.azure.com) és az Azure CLI-t használja a diagnosztikai naplókhoz való együttműködéshez. További információ a diagnosztikai naplók használatáról a Visual Studióban: [Az Azure hibaelhárítása a Visual Studióban](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Webkiszolgáló-diagnosztika és Application Diagnostics
A App Service diagnosztikai funkciókat biztosít a webkiszolgálóról és a webalkalmazásból származó adatok naplózásához. Ezeket a rendszer logikailag elkülöníti a webkiszolgáló- **diagnosztika** és az **Application Diagnostics**szolgáltatásban.

### <a name="web-server-diagnostics"></a>Webkiszolgáló-diagnosztika
Engedélyezheti vagy letilthatja a következő típusú naplókat:

* **Részletes hiba naplózása** – a 400-as vagy annál nagyobb HTTP-állapotkódot eredményező kérésekre vonatkozó részletes információk. Tartalmazhat olyan információkat, amelyek segíthetnek meghatározni, hogy a kiszolgáló miért adta vissza a hibakódot. A rendszer egy HTML-fájlt hoz létre az alkalmazás fájlrendszerében előforduló hibákhoz, és legfeljebb 50 hibát (fájlt) tart fenn. Ha a HTML-fájlok száma meghaladja az 50-as értéket, a rendszer a legrégebbi 26 fájlt automatikusan törli.
* **Sikertelen** kérelmek nyomkövetése – részletes információk a sikertelen kérésekről, beleértve a kérés feldolgozásához és az egyes összetevőkben használt IIS-összetevők nyomkövetését. Ez akkor hasznos, ha javítani szeretné a hely teljesítményét, vagy egy adott HTTP-hibát szeretne elkülöníteni. A rendszer létrehoz egy mappát az alkalmazás fájlrendszerében fellépett hibákhoz. A fájl-megőrzési házirendek ugyanazok, mint a fenti részletes naplózási hiba.
* **Webkiszolgáló-naplózás** – a [W3C bővített naplófájl formátumával](/windows/desktop/Http/w3c-logging)http-tranzakciókra vonatkozó információk. Hasznos lehet a helyek általános metrikáinak, például a kezelt kérelmek számának vagy egy adott IP-címről érkező kérések meghatározásakor.

### <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
Az Application Diagnostics lehetővé teszi egy webalkalmazás által létrehozott információk rögzítését. A ASP.NET-alkalmazások a [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace) osztály használatával naplózzák az adatokat az Application Diagnostics-naplóba. Példa:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Futásidőben lekérheti ezeket a naplókat, hogy segítséget nyújtson a hibaelhárításhoz. További információ: hibaelhárítási [Azure app Service a Visual Studióban](troubleshoot-dotnet-visual-studio.md).

A App Service is naplózza a telepítési adatokat, amikor tartalmat tesz közzé egy alkalmazásban. Ez automatikusan megtörténik, és nincsenek konfigurációs beállítások az üzembe helyezés naplózásához. Az üzembe helyezés naplózása lehetővé teszi annak meghatározását, hogy miért nem sikerült a központi telepítés. Ha például egyéni telepítési parancsfájlt használ, a központi telepítés naplózásával határozhatja meg, hogy miért sikertelen a parancsfájl.

## <a name="enablediag"></a>Diagnosztika engedélyezése
Ha engedélyezni szeretné a diagnosztikát [](https://portal.azure.com)a Azure Portalban, nyissa meg az alkalmazás lapját, és kattintson a **Beállítások > diagnosztikai naplók**elemre.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Naplók része](./media/web-sites-enable-diagnostic-log/logspart.png)

Az **Application Diagnostics**engedélyezésekor a **szintet**is kiválaszthatja. A következő táblázat az egyes szinteken található naplók kategóriáit tartalmazza:

| Szint| Belefoglalt naplózási kategóriák |
|-|-|
|**Letiltva** | Nincsenek |
|**Hiba** | Hiba, kritikus |
|**Figyelmeztetés** | Figyelmeztetés, hiba, kritikus|
|**Információk** | Információ, figyelmeztetés, hiba, kritikus|
|**Részletes** | Nyomkövetés, hibakeresés, információ, figyelmeztetés, hiba, kritikus (minden kategória) |
|-|-|

Az **alkalmazások naplózása**esetén a fájlrendszeri lehetőség ideiglenesen bekapcsolható hibakeresési célokra. Ez a beállítás 12 órán belül automatikusan kikapcsol. A blob Storage lehetőség bekapcsolásával kiválaszthatja a BLOB-tárolót a naplók írásához.

> [!NOTE]
> Jelenleg csak a .NET-alkalmazások naplói írhatók a blob Storage-ba. Java, PHP, Node. js, Python alkalmazás-naplók csak a fájlrendszerben tárolhatók (kód módosítása nélkül, hogy a rendszer a naplókat külső tárolóba írja).
>
>

A **webkiszolgáló naplózása**esetében kiválaszthatja a **tárolót** vagy a **fájlrendszert**. A **Storage** lehetőség kiválasztásával kiválaszthatja a Storage-fiókot, majd egy BLOB-tárolót, amelyre a naplók íródnak. 

Ha a naplófájlokat a fájlrendszerben tárolja, a fájlok FTP-n keresztül is elérhetők, vagy zip-archívumként tölthetők le az Azure CLI használatával.

Alapértelmezés szerint a rendszer nem törli automatikusan a naplókat (az **alkalmazás naplózása (filesystem)** kivételével). A naplók automatikus törléséhez állítsa be a **megőrzési időtartam (nap)** mezőt.

> [!NOTE]
> Ha újragenerálja a [Storage-fiók hozzáférési kulcsait](../storage/common/storage-create-storage-account.md), a frissített kulcsok használatához vissza kell állítania a megfelelő naplózási konfigurációt. Ehhez tegye a következőket:
>
> 1. A **configure (Konfigurálás** ) lapon állítsa be a megfelelő naplózási funkciót a kikapcsoláshoz. Mentse a beállítást.
> 2. Engedélyezze újra a naplózást a Storage-fiók blobján. Mentse a beállítást.
>
>

A fájlrendszer vagy a blob Storage bármely kombinációja engedélyezhető egyszerre, és egyéni naplózási szintű konfigurációval is rendelkezhet. Előfordulhat például, hogy a blob Storage-ban a hibákat és a figyelmeztetéseket hosszú távú naplózási megoldásként kívánja naplózni, miközben a fájlrendszer naplózását a részletes részletességgel teszi lehetővé.

Noha mindkét tárolóhely ugyanazokat az alapvető információkat adja meg a naplózott eseményekhez, a **blob Storage** további információkat naplóz, például a példány azonosítóját, a szál azonosítóját, és egy részletesebb időbélyeg (Tick formátum), mint a **fájlrendszer**naplózása.

> [!NOTE]
> A **blob Storage** -ban tárolt adatok csak Storage-ügyféllel vagy olyan alkalmazásokkal érhetők el, amelyek közvetlenül használhatók ezekkel a tárolási rendszerekkel. A Visual Studio 2013 például tartalmaz egy Storage Explorer, amely a blob Storage felderítésére használható, a HDInsight pedig hozzáférhet a blob Storage-ban tárolt adatszolgáltatásokhoz. Olyan alkalmazást is írhat, amely hozzáfér az Azure Storage-hoz az egyik [Azure SDK](https://azure.microsoft.com/downloads/)-val.
>

## <a name="download"></a>kézikönyv: Letöltési naplók
Az alkalmazás fájlrendszerében tárolt diagnosztikai adatok közvetlenül az FTP használatával érhetők el. Az Azure CLI használatával zip-archívumként is letölthető.

A naplókat tároló címtár-struktúra a következő:

* **Alkalmazás-naplók** –/LogFiles/Application/. Ez a mappa egy vagy több szövegfájlt tartalmaz, amelyek az alkalmazás naplózása által létrehozott információkat tartalmazzák.
* **Sikertelen kérelmek nyomkövetése** -/logfiles/W3SVC # # # # # # # # #/. Ez a mappa egy XSL-fájlt és egy vagy több XML-fájlt tartalmaz. Győződjön meg arról, hogy az XSL-fájlt ugyanabba a könyvtárba tölti le, mint az XML-fájl (oka) t, mert az XSL-fájl az Internet Explorerben megtekintett XML-fájl (ok) tartalmának formázására és szűrésére szolgáló funkciókat biztosít.
* **Részletes hibanapló** –/LogFiles/DetailedErrors/. Ez a mappa egy vagy több. htm fájlt tartalmaz, amelyek részletes információkat biztosítanak a bekövetkezett HTTP-hibákról.
* **Webkiszolgáló-naplók** –/LogFiles/http/RawLogs. Ez a mappa egy vagy több, a [W3C bővített naplófájl formátumával](/windows/desktop/Http/w3c-logging)formázott szövegfájlt tartalmaz.
* **Üzembe helyezési naplók** –/LogFiles/git. Ez a mappa a Azure App Service által használt belső üzembe helyezési folyamatok által létrehozott naplókat, valamint a git-telepítésekhez tartozó naplókat tartalmazza. Az üzembe helyezési naplók a D:\home\site\deployments. területen is megtalálhatók

### <a name="ftp"></a>FTP

Az alkalmazás FTP-kiszolgálójával létesített FTP-kapcsolat megnyitásával kapcsolatban lásd: [az alkalmazás telepítése Azure app Service FTP/s használatával](deploy-ftp.md).

Miután csatlakozott az alkalmazás FTP/S kiszolgálójához, nyissa meg a naplófájlok mappát, ahol a naplófájlok vannak tárolva.

### <a name="download-with-azure-cli"></a>Letöltés az Azure CLI-vel
A naplófájlok Azure parancssori felületen való letöltéséhez nyisson meg egy új parancssort, egy PowerShell-, bash-vagy terminál-munkamenetet, és írja be a következő parancsot:

    az webapp log download --resource-group resourcegroupname --name appname

Ez a parancs menti a "AppName" nevű alkalmazás naplóit egy **webapp_logs. zip** nevű fájlba az aktuális könyvtárban.

> [!NOTE]
> Ha még nem telepítette az Azure CLI-t, vagy nem konfigurálta az Azure-előfizetésének használatára, tekintse meg az [Azure CLI használatát](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)ismertető témakört.
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Útmutató: Naplók megtekintése Application Insights
A Visual Studio Application Insights a naplók szűrésére és keresésére, valamint a naplók a kérelmekkel és más eseményekkel való összevonására szolgáló eszközöket biztosít.

1. Adja hozzá a Application Insights SDK-t a projekthez a Visual Studióban.
   * A Megoldáskezelő kattintson a jobb gombbal a projektre, és válassza az Add Application Insights (Hozzáadás) lehetőséget. Az interfész végigvezeti a Application Insights erőforrás létrehozásához szükséges lépéseken. [További információ](../azure-monitor/app/asp-net.md)
2. Adja hozzá a nyomkövetési figyelő csomagját a projekthez.
   * Kattintson a jobb gombbal a projektre, és válassza a NuGet-csomagok kezelése lehetőséget. Válassza `Microsoft.ApplicationInsights.TraceListener` a [továbbiak](../azure-monitor/app/asp-net-trace-logs.md)
3. Töltse fel a projektet, és futtassa a naplózási adatok létrehozásához.
4. A [Azure Portal](https://portal.azure.com/)tallózással keresse meg az új Application Insights-erőforrást, és nyissa meg a **keresést**. A naplózási adatokat a kéréssel, a használattal és más telemetria együtt kell megtekinteni. Néhány telemetria néhány percet is igénybe vehet: kattintson a frissítés gombra. [További információ](../azure-monitor/app/diagnostic-search.md)

[További információ a teljesítmény-követésről Application Insights](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a>kézikönyv: Naplók streamelése
Az alkalmazások fejlesztése során gyakran hasznos a naplózási információk megjelenítése a közel valós időben. Az Azure CLI használatával továbbíthatja a naplózási információkat a fejlesztői környezetbe.

> [!NOTE]
> A naplózási puffer bizonyos típusai a naplófájlba írhatók, ami miatt az adatfolyamban megrendelési események is megtekinthetők. Például egy olyan alkalmazási naplóbejegyzés, amely akkor fordul elő, amikor egy felhasználó meglátogat egy oldalt az adatfolyamban, mielőtt megkezdené a megfelelő HTTP-naplóbejegyzés megadását az oldal kéréséhez.
>
> [!NOTE]
> A log streaming emellett a **D:\\\\ Home\\LogFiles** mappában tárolt szövegfájlba írt adatokat is továbbítja.
>
>

### <a name="streaming-with-azure-cli"></a>Folyamatos átvitel az Azure CLI-vel
A naplózási információk továbbításához nyisson meg egy új parancssort, egy PowerShell-, bash-vagy terminál-munkamenetet, és írja be a következő parancsot:

    az webapp log tail --name appname --resource-group myResourceGroup

Ez a parancs csatlakozik a "AppName" nevű alkalmazáshoz, és megkezdi a folyamatos átvitelt az ablakba, ahogy az alkalmazásban naplózási események történnek. A rendszer a/LogFiles könyvtárban (d:/Home/LogFiles) tárolt,. txt,. log vagy. htm kiterjesztésű fájlokba írt információkat továbbítja a helyi konzolra.

Adott események, például hibák szűréséhez használja a **--Filter** paramétert. Példa:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Adott típusú naplók (például HTTP) szűréséhez használja a **--path** paramétert. Példa:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Ha még nem telepítette az Azure CLI-t, vagy nem konfigurálta az Azure-előfizetésének használatára, tekintse meg az [Azure CLI használatát](../cli-install-nodejs.md)ismertető témakört.
>
>

## <a name="understandlogs"></a>kézikönyv: Diagnosztikai naplók ismertetése
### <a name="application-diagnostics-logs"></a>Application Diagnostics-naplók
Az Application Diagnostics a .NET-alkalmazások adott formátumában tárolja az adatokat, attól függően, hogy a naplófájlokat a fájlrendszer vagy a blob Storage tárolóba tárolja-e a rendszer. 

A tárolt adatok alapkészlete ugyanaz, mint a tárolási típusok között – az esemény dátuma és időpontja, az eseményt előkészítő folyamat azonosítója, az esemény típusa (információ, figyelmeztetés, hiba) és az esemény üzenete. Ha azonnali hozzáférésre van szüksége egy probléma megoldásához, a fájlrendszer a log Storage szolgáltatáshoz való használata akkor hasznos, ha a naplófájlok azonnal frissülnek. A blob Storage archiválási célokra szolgál, mert a fájlok gyorsítótárazva vannak, majd egy ütemezett tárolóba vannak ürítve.

**Fájlrendszer**

A fájlrendszerbe bejelentkezett vagy a streaming használatával fogadott sorok formátuma a következő:

    {Date}  PID[{process ID}] {event type/level} {message}

Egy hibaüzenet például a következő mintához hasonlóan jelenik meg:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

A fájlrendszerbe való naplózás a három elérhető módszer legalapvetőbb információit biztosítja, amely csak az időt, a folyamat AZONOSÍTÓját, az esemény szintjét és az üzenetet biztosítja.

**Blob Storage**

A blob Storage-ba való naplózáskor az adatok vesszővel tagolt (CSV) formátumban tárolódnak. A rendszer további mezőket naplóz, hogy részletesebb információkat szolgáltasson az eseményről. A CSV-sorok a következő tulajdonságokat használják:

| Tulajdonság neve | Érték/formátum |
| --- | --- |
| Date |Az esemény dátuma és időpontja |
| Szint |Esemény szintje (például hiba, figyelmeztetés, információ) |
| ApplicationName |Az alkalmazás neve |
| InstanceId |Az alkalmazás azon példánya, amelyen az esemény bekövetkezett |
| EventTickCount |Az esemény dátuma és időpontja, Tick formátumban (nagyobb pontosság) |
| EventId |Az esemény eseményazonosító<p><p>Alapértelmezett értéke 0, ha nincs megadva |
| PID |Folyamat azonosítója |
| TID |Az eseményt gyártó szálhoz tartozó szál azonosítója |
| Message |Esemény részletei üzenet |

A blobokban tárolt adatértékek az alábbi példához hasonlóan jelennek meg:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> ASP.NET Core esetében a naplózás a [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) szolgáltató használatával történik, ez a szolgáltató további naplófájlokat helyez el a blob-tárolóba. További információ: [ASP.net Core naplózás az Azure-ban](/aspnet/core/fundamentals/logging).
>
>

### <a name="failed-request-traces"></a>Sikertelen kérelmek nyomkövetése
A sikertelen kérelmek nyomkövetését a **fr # # # # # # #. XML**nevű XML-fájlban tárolja a rendszer. Ahhoz, hogy könnyebb legyen megtekinteni a naplózott adatokat, a **Sikertelen kérelmek eseménytárolási. xsl** nevű XSL-stíluslap ugyanabban a könyvtárban van megadva, mint az XML-fájlok. Ha az Internet Explorerben megnyit egy XML-fájlt, az Internet Explorer az XSL-stíluslapot használva megadja a nyomkövetési adatok formázott megjelenítését, az alábbi példához hasonlóan:

![a böngészőben megtekintett sikertelen kérelem](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> Egy egyszerű módszer a formázott sikertelen kérelmek nyomkövetésének megtekintésére, hogy megkeresse az alkalmazás lapját a portálon. A bal oldali menüben válassza a **diagnosztizálás és megoldás problémák**elemet, majd keresse meg a **Sikertelen kérelmek nyomkövetésére**vonatkozó naplókat, majd kattintson a ikonra a kívánt nyomkövetés megkereséséhez és megtekintéséhez.
>

### <a name="detailed-error-logs"></a>Részletes hibák naplói
A részletes naplófájlok olyan HTML-dokumentumok, amelyek részletesebb információkat biztosítanak a bekövetkezett HTTP-hibákról. Mivel ezek egyszerűen HTML-dokumentumok, webböngészővel is megtekinthetők.

### <a name="web-server-logs"></a>Webkiszolgáló naplói
A webkiszolgáló naplófájljai a [W3C bővített naplófájl formátumával](/windows/desktop/Http/w3c-logging)vannak formázva. Ezek az információk egy szövegszerkesztővel olvashatók be, vagy a segédprogramok, például a [log Parser](https://go.microsoft.com/fwlink/?LinkId=246619)használatával elemezhetők.

> [!NOTE]
> A Azure App Service által létrehozott naplók nem támogatják az **s-számítógépnév**, az **s-IP**vagy a **cs-Version** mezőket.
>
>

## <a name="nextsteps"></a> Következő lépések
* [A Azure App Service figyelése](web-sites-monitor.md)
* [Hibaelhárítási Azure App Service a Visual Studióban](troubleshoot-dotnet-visual-studio.md)
* [Alkalmazás-naplók elemzése a HDInsight-ben](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
