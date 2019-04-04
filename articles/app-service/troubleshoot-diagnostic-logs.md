---
title: Az Azure App Service-alkalmazások – diagnosztikai célú naplózásának engedélyezése
description: További információk a diagnosztikai naplózás engedélyezése és az üzemállapot-alkalmazását, valamint hogyan lehet hozzáférni azokhoz az információkhoz, amelyeket az Azure naplóz.
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 37455c278d665d05636ec120ca91b76153e53d16
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894918"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>Az Azure App Service-alkalmazások diagnosztikai célú naplózásának engedélyezése
## <a name="overview"></a>Áttekintés
Az Azure biztosít beépített diagnosztikai funkciókkal, amelyek segítik a hibakeresést egy [App Service-alkalmazás](https://go.microsoft.com/fwlink/?LinkId=529714). Ebből a cikkből megtudhatja, hogyan diagnosztikai naplózás engedélyezése és az üzemállapot-alkalmazását, valamint hogyan lehet hozzáférni azokhoz az információkhoz, amelyeket az Azure naplóz.

Ez a cikk a [az Azure portal](https://portal.azure.com) és az Azure CLI-vel való együttműködéshez a diagnosztikai naplók. Diagnosztikai naplók a Visual Studio használatával való munka információkért lásd: [Azure hibaelhárítása a Visual Studióban](troubleshoot-dotnet-visual-studio.md).

## <a name="whatisdiag"></a>Web server diagnostics és az application diagnostics
App Service-ben diagnosztikai funkciókat biztosít a naplózási információk a webalkalmazás-kiszolgáló és a webes alkalmazás. Ezek logikailag elkülönített **kiszolgálódiagnosztika webes** és **az application diagnostics**.

### <a name="web-server-diagnostics"></a>Webes kiszolgálódiagnosztika
Engedélyezheti vagy letilthatja a naplók a következő típusú:

* **Részletes hibanaplózás** – részletes információk minden kéréshez, amely HTTP-állapotkód: 400 vagy nagyobb eredményez. Tartalmazhat, amelyek segíthetnek meghatározni, miért érdemes a kiszolgáló a következő hibakódot adta vissza információt. Egy HTML-fájl jön létre minden egyes hibához, az alkalmazás fájlrendszerében, és legfeljebb 50 hibák (fájlok) megmaradnak. A HTML-fájlok száma meghaladja az 50, amikor a rendszer automatikusan töröl a legrégebbi 26 fájlokat.
* **Sikertelen kérelmek nyomkövetésére vonatkozó** – részletes információk a sikertelen kérelmek, beleértve a nyomkövetés feldolgozni a kérelmet, és az egyes összetevőkben ideje használja az IIS-összetevőt. Ez akkor hasznos, ha a webhely teljesítményének javítása vagy különíteni egy adott HTTP hiba. Egy mappa jön létre minden egyes hibához az alkalmazás fájlrendszerében. Fájl adatmegőrzési szabályzatok ugyanazok, mint a részletes hibanaplózást felett.
* **Webalkalmazás-kiszolgáló naplózási** – HTTP-tranzakciót használatával kapcsolatos információkat a [W3C bővített naplófájlformátum](/windows/desktop/Http/w3c-logging). Ez hasznos, teljes webhelymetrikák például kezelt kérések, vagy hogy hány kérésnek egy adott IP-címről számának meghatározásakor.

### <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
Az Application diagnostics lehetővé teszi egy webalkalmazás által létrehozott adatok rögzítését. ASP.NET-alkalmazások használhatják a [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) osztályt az alkalmazásnaplóba diagnosztikai információk naplózása. Példa:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Futásidőben a naplók segíthetnek a hibaelhárításban kérheti le. További információkért lásd: [hibaelhárítása az Azure App Service a Visual Studióban](troubleshoot-dotnet-visual-studio.md).

App Service-ben is naplózza telepítési információkat, amikor tartalmat tesz közzé egy alkalmazást. Automatikusan megtörténik, és nem a központi telepítési naplózáshoz konfigurációs beállításokat. Üzembe helyezés naplózás lehetővé teszi, hogy meghatározhatja, miért érdemes a központi telepítés nem sikerült. Egy egyéni üzembehelyezési szkript használ, előfordulhat, hogy meghatározni, miért nem működik a parancsfájl például használjon központi telepítési naplózást.

## <a name="enablediag"></a>Diagnosztika engedélyezése
Diagnosztika engedélyezése a a [az Azure portal](https://portal.azure.com), nyissa meg az alkalmazás lapját, és kattintson a **beállítások > diagnosztikai naplók**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Rész-naplók](./media/web-sites-enable-diagnostic-log/logspart.png)

Amikor engedélyezi a **az application diagnostics**, azt is választhatja a **szint**. Az alábbi táblázat tartalmazza az egyes fenyegetési naplók kategóriáit mutatja:

| Szint| Belefoglalt naplókategóriák |
|-|-|
|**Letiltva** | None |
|**Hiba** | Kritikus hiba |
|**Figyelmeztetés** | Figyelmeztetés, kritikus hiba|
|**Információ** | Info, figyelmeztetés, kritikus hiba|
|**Részletes** | Nyomkövetési, hibakeresési, adatok, figyelmeztetés, hiba, kritikus (az összes kategória) |
|-|-|

A **alkalmazásnaplózás**, bekapcsolhatja a hibakeresési célokra az ideiglenes fájl helyrendszer-beállítást. 12 óra múlva automatikusan kikapcsolja ezt a beállítást. A blob beállítást válassza ki a naplók írhat egy blob-tárolóba is bekapcsolhatja.

> [!NOTE]
> A blob storage .NET-alkalmazás jelenleg csak naplók csak írható. A Java, PHP, Node.js, Python, az alkalmazásnaplókat csak a fájlrendszeren (nélkül naplók írni a külső tárhelyen) kell tárolni.
>
>

A **webkiszolgálói naplózás**, választhat **tárolási** vagy **fájlrendszer**. Kiválasztásával **tárolási** lehetővé teszi, hogy válasszon egy tárfiókot, és egy blobtárolót a naplók írt. 

Ha a naplók tárolása a fájlrendszer, a fájlok elérhető FTP, és letöltött Zip-archívumot, Azure parancssori felület használatával.

Alapértelmezés szerint naplók nem törlődnek automatikusan (kivéve a **Application Logging (fájlrendszer)**). Naplók automatikus törléséhez állítsa be a **megőrzési ideje (nap)** mező.

> [!NOTE]
> Ha Ön [a tárfiók hozzáférési kulcsainak újragenerálása](../storage/common/storage-create-storage-account.md), alaphelyzetbe kell állítania a megfelelő naplózási konfiguráció frissített kulcsok használatához. Ehhez tegye a következőket:
>
> 1. Az a **konfigurálása** lapon, a megfelelő naplózási szolgáltatás beállítása **ki**. A beállítás mentéséhez.
> 2. Engedélyezze a naplózást, a storage-fiók blob újra. A beállítás mentéséhez.
>
>

A file system vagy a blob storage bármilyen kombinációját is engedélyezhető egyszerre, és egyes naplózási szintekhez rendelkezik. Például érdemes a hibák és figyelmeztetések naplózása hosszú távú megoldás, miközben a rendszer naplózását részletes szintű blob storage-bA.

Mindkét tárolási helyek naplózott események az ugyanazon alapvető adatok megadása közben **a blob storage-** naplózza a további információk, például a Példányazonosító Szálazonosító és részletesebb időbélyeg (osztásjelek formátum), mint a naplózást, és **fájlrendszer**.

> [!NOTE]
> A tárolt adatok **a blob storage-** csak érhetők el a storage-kliens vagy olyan alkalmazás, amely közvetlenül is dolgozhat a tárolórendszerek használatával. Például a Visual Studio 2013 tartalmaz, amelyek segítségével ismerje meg a blob storage a Storage Explorer, és a HDInsight érhessék el az a blob storage-ban tárolt adatokat. Egy alkalmazás, amely hozzáfér az Azure Storage egyikének használatával is kiírhatja a [Azure SDK-k](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a> kézikönyv: Letöltési naplók
Diagnosztikai adatokat tárolni az alkalmazáshoz fájlrendszerben közvetlenül az FTP használatával is elérhetők. Le is tölthetők, egy Zip-archívumot az Azure CLI használatával.

A könyvtárstruktúra, a naplók vannak tárolva a következőképpen történik:

* **Protokoly aplikací** -/LogFiles/alkalmazás /. Ez a mappa tartalmaz egy vagy több, az alkalmazásadatok naplózása által előállított adatokat tartalmazó szöveges fájlok.
* **Sikertelen kérelmek nyomkövetési** -/ LogFiles/W3SVC ### /. Ez a mappa tartalmaz egy XSL-fájl és a egy vagy több XML-fájlt. Győződjön meg arról, le kell tölteni a XSL-fájl ugyanabban a könyvtárban való, az XML-fájl fájl, mert a XSL-fájl formázására és az Internet Explorer megtekintve XML fájl(ok) tartalmát szűrés funkciót biztosít.
* **Részletes hibanaplókat** -/LogFiles/DetailedErrors /. Ez a mappa tartalmaz egy vagy több HTTP-hibaüzeneteket előfordult széleskörű információkat biztosító .htm fájlt.
* **Webalkalmazás-naplók** -/LogFiles/http/RawLogs. Ez a mappa tartalmaz egy vagy több szöveges fájlok formázva a [W3C bővített naplófájlformátum](/windows/desktop/Http/w3c-logging).
* **Telepítési naplók** -/ LogFiles/Git. Ez a mappa tartalmazza az Azure App Service által használt belső üzembe helyezési folyamat által létrehozott naplók, valamint-naplókban a Git-telepítéseket. Telepítési naplók D:\home\site\deployments alapján is megtalálhatja.

### <a name="ftp"></a>FTP

Tekintse meg az alkalmazás FTP-kiszolgálóhoz FTP-kapcsolat megnyitásához [alkalmazás üzembe helyezése az Azure App Service-ben FTP/S használatával](deploy-ftp.md).

Ha csatlakoztatva van az alkalmazás FTP/S-kiszolgálóra, nyissa meg a **LogFiles** a naplófájlokat tároló mappa.

### <a name="download-with-azure-cli"></a>Töltse le az Azure CLI-vel
Töltse le a naplófájlok, az Azure parancssori felülettel, nyisson meg egy új parancssort, PowerShell, a Bash vagy a terminál-munkamenetet, és adja meg a következő parancsot:

    az webapp log download --resource-group resourcegroupname --name appname

Ez a parancs menti a naplókat az alkalmazás neve a "appname" nevű **webapp_logs.zip** az aktuális könyvtárban található.

> [!NOTE]
> Ha még nem telepítette az Azure CLI-vel, vagy még nem konfigurálta, hogy az Azure-előfizetéssel, lásd: [hogyan használja az Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Útmutató: Naplók megtekintése az Application insights szolgáltatásban
A Visual Studio Application Insights a szűréshez és a naplók keresése, és a kérésekkel és más eseményekkel való korreláláshoz a naplók eszközöket biztosít.

1. Az Application Insights SDK hozzáadása a projekthez a Visual Studióban.
   * A Megoldáskezelőben kattintson jobb gombbal a projektre, és válassza ki az Application Insights hozzáadása. Az interface végigvezeti lépéseket, amelyek tartalmazzák az Application Insights-erőforrás létrehozása. [Részletek](../azure-monitor/app/asp-net.md)
2. A nyomkövetés-figyelő csomag hozzáadása a projekthez.
   * Kattintson jobb gombbal a projektre, és válassza a NuGet-csomagok kezelése. Válassza ki `Microsoft.ApplicationInsights.TraceListener` [további](../azure-monitor/app/asp-net-trace-logs.md)
3. Töltse fel a projektet, majd futtassa készítése a naplózási adatokat.
4. Az a [az Azure portal](https://portal.azure.com/), keresse meg az új Application Insights-erőforrást, és nyissa meg a **keresési**. A naplózási adatokat, és a kérést, használatának és egyéb telemetriát kell megjelennie. Valamennyi telemetria is igénybe vehet néhány percet késik: a frissítés parancsra. [Részletek](../azure-monitor/app/diagnostic-search.md)

[További tudnivalók a teljesítmény nyomon követése az Application insights segítségével](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> kézikönyv: Naplók streamelése
Az alkalmazások fejlesztése során hasznos gyakran közel valós idejű naplóinformációkat tekinthet. Naplózási információk streamelheti a fejlesztői környezetbe az Azure CLI használatával.

> [!NOTE]
> Bizonyos típusú naplózási puffer írni a naplófájlt, amely a Stream üzemen kívüli események eredményezhet. Például egy alkalmazás naplóbejegyzést, amikor egy felhasználó meglátogat egy oldal előfordulhat, hogy megjelenik a Stream a megfelelő HTTP-naplóbejegyzés az oldal kérelem előtt.
>
> [!NOTE]
> Bármilyen szöveges fájlba, a tárolt adatokat is a naplózási adatfolyam streameli az **D:\\otthoni\\LogFiles\\**  mappát.
>
>

### <a name="streaming-with-azure-cli"></a>Streamelés az Azure CLI-vel
Az adatfolyam adatok naplózása, nyisson meg egy új parancssort, PowerShell, a Bash vagy a terminál-munkamenetet, és adja meg a következő parancsot:

    az webapp log tail --name appname --resource-group myResourceGroup

Ez a parancs a "appname" nevű alkalmazás csatlakozik, és elkezdeni a streamelést az ablak információk alkalmazásnapló-események bekövetkezésekor az alkalmazást. Minden olyan információt írni a .txt, .log vagy .htm végződésű (d:/home/logfiles) /LogFiles a címtárban tárolt fájlok a helyi konzol adatfolyamként.

Adott események, hibák, például szűréséhez használja a **– szűrő** paraméter. Példa:

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

Szűrés konkrét naplófájlokból típusok, például a HTTP, használja a **– elérési út** paraméter. Példa:

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> Ha még nem telepítette az Azure CLI-vel, vagy még nem konfigurálta, hogy az Azure-előfizetéssel, lásd: [hogyan használja az Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> kézikönyv: Diagnosztikai naplók megértése
### <a name="application-diagnostics-logs"></a>Application diagnostics-naplók
Az Application diagnostics adatait tárolja egy megadott formátumban, a .NET-alkalmazásokban, attól függően, hogy naplókat a file system vagy a blob Storage tárolja. 

A alapkészlete, tárolt adatok esetében azonos mindkét tárolási típusok – a dátum és idő az esemény történt, az eseményt, az esemény típusa (információk, figyelmeztetés, hiba) és az eseményüzenet előállított ID procesu. Naplók tárolásához a fájlrendszer használata akkor hasznos, ha egy probléma elhárításához, mert a naplófájlok frissítés közel azonnali közvetlen hozzáférésre van szüksége. A BLOB storage archiválási célokat szolgál, mert a fájlok a gyorsítótárba, és majd a storage-tárolóba, ütemezés szerint kiürített.

**Fájlrendszer**

Az egyes sorok naplózza a fájlrendszer vagy fogadott streamelési használatával a következő formátumban kell megadni:

    {Date}  PID[{process ID}] {event type/level} {message}

Ha például egy hibaesemény a következőképpen fog a következő mintához hasonló:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

A fájlrendszer naplózás legalapvetőbb információkat talál a három elérhető módszerek, csak az idő, Folyamatazonosító, Eseményszint, és üzenet megadása.

**Blob Storage**

Blob storage-ba történő bejelentkezéskor adatok vesszővel elválasztott értékeket (CSV) formátum van tárolva. További mezők naplózza az esemény kapcsolatos részletesebb információkat biztosít. A következő tulajdonságokat a fürt megosztott kötetei szolgáltatás minden egyes sorára használhatók:

| Tulajdonság neve | / Formátumban |
| --- | --- |
| Dátum |A dátum és időpont, amikor az esemény történt |
| Szint |Eseményszint (például hiba, figyelmeztetés, információ) |
| Alkalmazásnév |Az alkalmazás neve |
| Példány azonosítója |Az esemény történt az alkalmazás példánya |
| EventTickCount |A dátum és időpont, amikor az esemény történt, osztásjelek formátumban (nagyobb pontosság) |
| EventId |Ez az esemény esemény azonosítója<p><p>Az alapértelmezett érték 0, ha nincs megadva |
| Pid |Folyamat azonosítója |
| TID |Az esemény előállított szál hozzászóláslánc azonosítója |
| Üzenet |Eseménynapló-üzenet részletei |

A blob tárolt adatokat ehhez hasonlóan néz ki az alábbi példához:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> ASP.NET Core, a naplózás használatával valósítható meg a [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) szolgáltató a szolgáltatói betétek további naplófájlok a blobtárolóba. További információkért lásd: [ASP.NET Core-naplózás az Azure-ban](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure).
>
>

### <a name="failed-request-traces"></a>Sikertelen kérelmek nyomkövetési
Sikertelen kérelmek nyomkövetési nevű XML-fájlokban van tárolva **fr ### .xml**. A naplózott információk megkönnyítése XSL stíluslapok nevű **freb.xsl** megtalálható az XML-fájlok könyvtárába. Ha megnyit egy XML-fájlt az Internet Explorer, az Internet Explorer a XSL stíluslap egy formázott megjelenítése a nyomkövetési adatok, az alábbi példához hasonló biztosításához használt:

![sikertelen kérelmek, a böngészőben](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> A formázott sikertelen kérelmek nyomkövetési megtekintéséhez egyszerűen, hogy nyissa meg az alkalmazás lapot a portálon. A bal oldali menüben válassza ki a **diagnosztizálása és a problémák megoldásában**, majd keresse meg az **sikertelen kérelmek nyomkövetési naplók**, majd kattintson a ikonra tallózása és megtekintése a nyomkövetést szeretne.
>

### <a name="detailed-error-logs"></a>Részletes hibanaplókat
Részletes hibanaplókat olyan HTML-dokumentumok, amelyek előfordult HTTP-hibák részletes információkat biztosítanak. Egyszerűen csak a HTML-dokumentumok, mivel azok tekinthet meg webböngészővel.

### <a name="web-server-logs"></a>Webkiszolgáló naplói
A webkiszolgáló-naplók használatával formázott a [W3C bővített naplófájlformátum](/windows/desktop/Http/w3c-logging). Ez az információ egy szövegszerkesztő használatával olvashatja, vagy segédprogramok használatával például elemzett [naplóelemző](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Az Azure App Service által előállított naplók nem támogatják a **s-computername**, **s-ip**, vagy **cs-version** mezőket.
>
>

## <a name="nextsteps"></a> Következő lépések
* [Az Azure App Service figyelése](web-sites-monitor.md)
* [Hibaelhárítás az Azure App Service a Visual Studióban](troubleshoot-dotnet-visual-studio.md)
* [A HDInsight-alkalmazás naplók elemzése](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
