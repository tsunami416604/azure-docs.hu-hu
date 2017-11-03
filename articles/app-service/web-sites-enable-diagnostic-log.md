---
title: "Az Azure App Service web Apps diagnosztikai naplózás engedélyezése"
description: "Ismerje meg, hogyan diagnosztikai naplózás engedélyezése és instrumentation hozzá az alkalmazáshoz, valamint az Azure által naplózott információk elérését."
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
ms.openlocfilehash: 42644bd97443e827b523503989b0b25d3d48028c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Az Azure App Service web Apps diagnosztikai naplózás engedélyezése
## <a name="overview"></a>Áttekintés
Azure biztosít, beépített diagnosztika, elősegítve ezzel a hibakeresést egy [App Service webalkalmazásba](http://go.microsoft.com/fwlink/?LinkId=529714). Ebben a cikkben megtudhatja, hogyan diagnosztikai naplózás engedélyezése és instrumentation hozzá az alkalmazáshoz, valamint az Azure által naplózott információk elérését.

Ebben a cikkben az a [Azure Portal](https://portal.azure.com), Azure PowerShell és az Azure parancssori felület (CLI) történő együttműködésre a diagnosztikai naplókat. Diagnosztikai naplók Visual Studio használatával használatával kapcsolatos információkért lásd: [Azure hibaelhárítása a Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Web server diagnostics és az application diagnostics
App Service web apps naplózási információkat a webkiszolgáló és a webes alkalmazás diagnosztikai funkciók biztosítanak. Ezek logikailag rétegekben **web server diagnosztika** és **alkalmazásdiagnosztika**.

### <a name="web-server-diagnostics"></a>Web server diagnosztika
Engedélyezheti vagy letilthatja a naplók a következő típusú:

* **Részletes naplózás hiba** -részletes (állapotkód: 400 vagy nagyobb) hibát jelző HTTP-állapotkódok hiba adatait. Ez tartalmazhat, amelyek segíthetnek meghatározni, miért a kiszolgáló a hibakódot adott vissza adatokat.
* **Sikertelen kérelmek nyomkövetésére vonatkozó** – részletes információk a sikertelen kérelmek nyomkövetési segítségével dolgozza fel a kérelmet, és minden egyes összetevő szükséges idő az IIS-összetevők többek között. Ez akkor lehet hasznos, ha a hely teljesítményének javítása vagy különítse el, mi okozza-e egy adott HTTP hiba vissza kell adni kívánt.
* **Webalkalmazás-kiszolgáló naplózza a** -HTTP-tranzakciók használatával kapcsolatos információkat a [W3C bővített naplófájlformátum](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ez akkor hasznos, például a kezelt kéréseket, és hogy hány kérésnek egy adott IP-címről van a teljes webhelymetrikák meghatározásakor.

### <a name="application-diagnostics"></a>Application diagnostics
Application diagnostics lehetővé teszi egy webes alkalmazás által létrehozott adatok rögzítését. ASP.NET alkalmazások használhatják a [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) osztály az alkalmazásnaplóba diagnosztikai adatok naplózására. Példa:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Futásidőben kérheti le a naplók segíthetnek a hibaelhárításban. További információkért lásd: [hibaelhárítási Azure web Apps alkalmazások, a Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

App Service web apps is naplózza az információkat, amikor a tartalom közzétételére a webes alkalmazás. Ez automatikusan megtörténik, és nincsenek telepítési naplózás konfigurációs beállítások. Központi telepítés naplózás lehetővé teszi a segítségével meghatározhatja, miért nem sikerült a telepítés. Egy egyéni telepítési parancsfájl használ, előfordulhat, hogy meghatározni, miért nem sikerült a parancsfájl például használhat telepítési naplózást.

## <a name="enablediag"></a>Diagnosztika engedélyezése
A diagnosztika engedélyezéséhez a [Azure Portal](https://portal.azure.com), nyissa meg a webalkalmazás panelen, és kattintson a **beállítások > diagnosztikai naplók**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Naplók része](./media/web-sites-enable-diagnostic-log/logspart.png)

Amikor engedélyezi a **alkalmazásdiagnosztika** meg a **szint**. Ez a beállítás lehetővé teszi annak elkészült a megjelenített információkat szűrheti **tájékoztató**, **figyelmeztetés** vagy **hiba** információkat. Ezt a beállítást **részletes** naplózza az alkalmazás által létrehozott összes információt.

> [!NOTE]
> Ellentétben a web.config fájl módosítása, alkalmazásdiagnosztika engedélyezése vagy diagnosztikai naplózási szintek módosítása nem indul a az alkalmazás belül futó alkalmazástartományban.
>
>

Az a [klasszikus portál](https://manage.windowsazure.com) webalkalmazás **konfigurálása** lapon választhat **tárolási** vagy **fájlrendszer** a **webkiszolgáló naplózásának**. Kiválasztása **tárolási** választhatja ki a tárfiók, és egy blob tároló, amely a naplók lesz írva. A többi naplófájlt **diagnosztika hely** a fájlrendszer csak kerülnek.

A [klasszikus portál](https://manage.windowsazure.com) webalkalmazás **konfigurálása** lap is tartalmaz további beállításokat az application diagnostics:

* **Fájlrendszer** -tárolja az application diagnostics a webes alkalmazás fájlrendszerhez. Ezek a fájlok FTP érhető el, vagy letöltött Zip-archívum létrehozása, az Azure PowerShell vagy Azure parancssori felület (CLI) használatával.
* **TABLE storage** -alkalmazás diagnosztikai adatokat tárol a megadott Azure Storage-fiókot és a tábla nevét.
* **BLOB-tároló** -alkalmazás diagnosztikai adatokat tárol a megadott Azure-Tárfiókot és blob-tároló.
* **Megőrzési időtartam** -alapértelmezés szerint naplók nem automatikusan törlődjenek **blob-tároló**. Válassza ki **beállítani a megőrzési** , és adja meg a naplók tárolására, ha automatikusan törli a naplókat.

> [!NOTE]
> Ha Ön [a tárfiók tárelérési kulcsok újragenerálása](../storage/common/storage-create-storage-account.md), a frissített kulcsokat kíván használni a megfelelő naplózási konfiguráció kell visszaállítani. Ehhez tegye a következőket:
>
> 1. Az a **konfigurálása** lapon használatához állítsa be a megfelelő naplózási szolgáltatás **ki**. A beállítás mentése.
> 2. Engedélyezze újra a tárolási fiók blob vagy tábla naplózását. A beállítás mentése.
>
>

Fájlrendszer, a table storage vagy a blob storage kombinációja egyszerre engedélyezhető, és rendelkezik az egyes naplózási szintekhez. Például érdemes a hibák és figyelmeztetések a blob-tároló hosszú távú naplózási megoldásként engedélyezésekor a rendszer fájlnaplózási verbose szint.

Minden három tárolóhelyek naplózott események az ugyanazon alapvető adatok megadása közben **table storage** és **blob-tároló** például Példányazonosítója Szálazonosító vagy a még további információk naplózása részletes időbélyeg (osztásjelek formátum) történő naplózás mint **fájlrendszer**.

> [!NOTE]
> A tárolt információk **table storage** vagy **blob-tároló** csak segítségével férhetők el egy tárolási ügyfél vagy egy alkalmazás, amely közvetlenül a tárolórendszerek használható. Például a Visual Studio 2013 tartalmazza, amelyek segítségével megismerheti a tábla vagy a blob storage Tártallózóval, és HDInsight férhetnek hozzá a blob storage-ban tárolt adataihoz. Egy alkalmazás, aki hozzáfér az Azure Storage használatával is kiírhatja a [Azure SDK-k](/downloads/#).
>
> [!NOTE]
> Diagnosztika is engedélyezhető az Azure PowerShell használatával a **Set-AzureWebsite** parancsmag. Ha nem telepítette az Azure PowerShell, vagy nem konfigurálta az Azure-előfizetés használatára, lásd: [hogyan használható az Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

## <a name="download"></a>Hogyan: naplók letöltése
Diagnosztikai adatok tárolása a webes alkalmazás fájlrendszerben elérhető közvetlenül az FTP használatával. Az Azure PowerShell vagy az Azure parancssori felület használatával Zip-archívum létrehozása, is letölthetők.

A könyvtárstruktúra, a naplók vannak tárolva a következőképpen történik:

* **Alkalmazásnaplók** -/LogFiles/alkalmazás /. Ez a mappa tartalmaz egy vagy több alkalmazásnaplózás által előállított adatokat tartalmazó szöveges fájlt.
* **Sikertelen kérelmek nyomkövetési** -/ naplófájlok/W3SVC ### /. Ez a mappa tartalmaz egy XSL-fájlt és egy vagy több XML-fájlokat. Győződjön meg arról, hogy töltse le az XSL-fájl a azonos könyvtárba, az XML fájl, mert az XSL-fájl a formázás és a szűrést az Internet Explorer a XML-fájl tartalmának funkciókat biztosítja.
* **Részletes hibanaplókat** -/LogFiles/DetailedErrors /. Ez a mappa tartalmaz egy vagy több HTTP-hibaüzenetek történt széleskörű információkat biztosító .htm fájlt.
* **Webalkalmazás-naplói** -/LogFiles/http/RawLogs. Ez a mappa tartalmaz egy vagy több szövegfájlok formázva a [W3C bővített naplófájlformátum](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Telepítési naplói** -/ naplófájlok/Git. Ez a mappa tartalmazza az Azure web Apps alkalmazások által használt belső telepítési folyamatok által létrehozott naplók, valamint a Git-telepítésekhez naplózza.

### <a name="ftp"></a>FTP
Diagnosztikai adatok FTP használatával szeretne használni, keresse fel a **irányítópult** a webalkalmazás a [klasszikus portál](https://manage.windowsazure.com). Az a **gyors áttekintő** szakaszban a **FTP diagnosztikai naplók** hivatkozás a naplófájlok, FTP használatával eléréséhez. A **telepítési/FTP-felhasználó** bejegyzés az FTP-hely eléréséhez használandó felhasználói nevét jeleníti meg.

> [!NOTE]
> Ha a **telepítési/FTP-felhasználó** bejegyzés nincs megadva, vagy elfelejtette a jelszót a felhasználónak, használatával létrehozhat egy új felhasználó és egy jelszót a **üzembe helyezési hitelesítő adatok alaphelyzetbe** hivatkozásra a  **gyors áttekintő** szakasza a **irányítópult**.
>
>

### <a name="download-with-azure-powershell"></a>Töltse le az Azure PowerShell használatával
Töltse le a naplófájlok, indítsa el az Azure PowerShell egy új példányát, és használja a következő parancsot:

    Save-AzureWebSiteLog -Name webappname

A naplók által megadott webalkalmazás megtakarít a **-név** paraméter nevű fájlba **logs.zip** az aktuális könyvtárban található.

> [!NOTE]
> Ha nem telepítette az Azure PowerShell, vagy nem konfigurálta az Azure-előfizetés használatára, lásd: [hogyan használható az Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="download-with-azure-command-line-interface"></a>Töltse le az Azure parancssori felülettel
Töltse le a naplófájlok az Azure parancssori felület használatával, nyissa meg egy új parancssor, PowerShell, Bash vagy terminál-munkamenetet, és adja meg a következő parancsot:

    azure site log download webappname

A naplók nevű fájlba "webappname' nevű webalkalmazás megtakarít **diagnostics.zip** az aktuális könyvtárban található.

> [!NOTE]
> Ha nem telepítette az Azure parancssori felület (CLI), vagy nem konfigurálta az Azure-előfizetés használatára, lásd: [hogyan használható az Azure parancssori felület](../cli-install-nodejs.md).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Útmutató: az Application Insightsban naplók megtekintése
A Visual Studio Application Insights szűrési és keresési naplókat, valamint a válaszoknak az összekapcsolása a naplók kérések és más eseményekkel eszközöket biztosít.

1. Az Application Insights SDK hozzáadása a projekthez a Visual Studióban.
   * A Megoldáskezelőben kattintson a jobb gombbal a projekt, és válassza ki az Application Insights hozzáadása. Lesz, amely tartalmazza az Application Insights-erőforrás létrehozása lépések interaktív. [További információ](../application-insights/app-insights-asp-net.md)
2. A nyomkövetés-figyelő csomag hozzáadása a projekthez.
   * Kattintson a jobb gombbal a projekt, és válassza ki a NuGet-csomagok kezelése. Válassza ki `Microsoft.ApplicationInsights.TraceListener` [további](../application-insights/app-insights-asp-net-trace-logs.md)
3. Töltse fel a projektet, és futtassa a naplóadatok létrehozásához.
4. Az a [Azure Portal](https://portal.azure.com/), keresse meg az új Application Insights-erőforrást, és nyissa meg a **keresési**. Látni fogja, a naplóadatok kérelem, használatának és egyéb telemetriai együtt. Néhány telemetriai érkezésére néhány percbe telhet: kattintson a frissítés parancsra. [További információ](../application-insights/app-insights-diagnostic-search.md)

[További információ a teljesítmény nyomon követése az Application insights szolgáltatással](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a>Hogyan: adatfolyam-naplók
Az alkalmazások fejlesztése során gyakran célszerű közel valós idejű naplóinformációk megjelenítéséhez. A naplózási információkat a fejlesztési környezetet az Azure PowerShell vagy az Azure parancssori felület használatával történő folyamatos lehet elvégezni.

> [!NOTE]
> Naplózási puffer bizonyos típusú ír a naplófájlba, ennek eredményeként az adatfolyamban üzemen kívüli események. Például egy alkalmazás naplóbejegyzés, amely akkor fordul elő, amikor a felhasználó meglátogat egy lap esetleg jelennek meg az adatfolyam a megfelelő HTTP naplóbejegyzés a lap lekérése előtt.
>
> [!NOTE]
> Adatfolyam-naplót is adatfolyam bármely szöveges fájlba, tárolt adatokat a **D:\\otthoni\\naplófájlok\\**  mappa.
>
>

### <a name="streaming-with-azure-powershell"></a>Adatfolyam-Azure PowerShell használatával
Adatfolyam-naplózási információkat, indítsa el az Azure PowerShell egy új példányát, és használja a következő parancsot:

    Get-AzureWebSiteLog -Name webappname -Tail

Ezzel összeköti azt a webalkalmazást, amelyet a **-név** paraméter és kezdete streaming adatokat a PowerShell-ablakot a webalkalmazásban alkalmazásnapló-események bekövetkezésekor. Minden olyan információt írni a .txt, .log vagy .htm végződése (d:/otthoni/naplófájlok) /LogFiles könyvtárban tárolt fájlok adatfolyamként továbbítva a lesz a helyi konzol.

Szűrés adott események, például a hibák, használja a **-üzenet** paraméter. Példa:

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Szűrés adott napló típusok, például HTTP, használja a **-elérési út** paraméter. Példa:

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

Rendelkezésre álló elérési utak listájának megtekintéséhez használja a - ListPath paramétert.

> [!NOTE]
> Ha nem telepítette az Azure PowerShell, vagy nem konfigurálta az Azure-előfizetés használatára, lásd: [hogyan használható az Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).
>
>

### <a name="streaming-with-azure-command-line-interface"></a>Adatfolyam-Azure parancssori felületével
Adatfolyam-naplózási információk nyisson meg egy új parancssor, PowerShell, Bash vagy terminál-munkamenetet, és adja meg a következő parancsot:

    azure site log tail webappname

Ez az "webappname" nevű webes alkalmazáshoz és kezdete streaming a az ablak az információk a webalkalmazásban alkalmazásnapló-események bekövetkezésekor. Minden olyan információt írni a .txt, .log vagy .htm végződése (d:/otthoni/naplófájlok) /LogFiles könyvtárban tárolt fájlok adatfolyamként továbbítva a lesz a helyi konzol.

Szűrés adott események, például a hibák, használja a **--szűrő** paraméter. Példa:

    azure site log tail webappname --filter Error

Szűrés adott napló típusok, például HTTP, használja a **--elérési** paraméter. Példa:

    azure site log tail webappname --path http

> [!NOTE]
> Ha nem telepítette az Azure parancssori felület, vagy nem konfigurálta az Azure-előfizetés használatára, lásd: [hogyan való használata Azure parancssori felület](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a>Hogyan: diagnosztikai naplók megértése
### <a name="application-diagnostics-logs"></a>Application diagnostics naplók
Application diagnostics információkat tárol egy meghatározott formátumnak a .NET-alkalmazásokban, attól függően, hogy tárolja a fájlrendszer, a table storage vagy a blob storage-naplókat. A tárolt adatok alapkészlete megegyezik összes három tárolási típusa – dátum és idő az esemény történt, az esemény, az esemény típusa (információ, figyelmeztetés, hiba) és az eseményüzenet előállított Folyamatazonosítója.

**Fájlrendszer**

Minden egyes sorban, a rendszer naplózza a fájlrendszer vagy streaming használatával fogadott lesz a következő formátumban:

    {Date}  PID[{process id}] {event type/level} {message}

Például egy hibaesemény jelent a következőhöz hasonló:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

A fájlrendszer naplózása a három rendelkezésre álló metódusok biztosító csak az idő, folyamatazonosító, Eseményszint, és üzenet a legalapvetőbb információkat biztosít.

**Table Storage**

A table storage bejelentkezéskor további tulajdonságok használatával segítik a keresést a tábla, valamint az esemény részletesebb tájékoztatást tárolt adatokat. Minden entitás (sor), a táblában tárolt használ a következő tulajdonságok (oszlop).

| Tulajdonság neve | / Formátumban |
| --- | --- |
| PartitionKey |Dátum és idő formátumban yyyyMMddHH esemény |
| RowKey |A GUID-érték, amely egyedileg azonosítja az ehhez az entitáshoz |
| időbélyeg |A dátum és időpont, amikor az esemény történt |
| EventTickCount |A dátum és időpont, amikor az esemény történt osztásjelek formátumban (nagyobb pontosságú) |
| ApplicationName |A webes alkalmazás neve |
| Szint |Eseményszint (pl. hiba, figyelmeztetés, információ) |
| Eseményazonosító |Ez az esemény esemény azonosítója<p><p>Az alapértelmezett érték 0, ha nincs megadva |
| instanceId |A webes alkalmazás, amely a még akkor is történt példányainak |
| Azonosítója (PID) |Folyamatazonosító |
| TID |A szál az esemény előállított a Szálazonosító |
| Üzenet |Üzenet esemény részletei |

**Blob Storage**

A blob-tároló bejelentkezéskor adatok vesszővel tagolt (CSV) formátum tárolódik. A table storage hasonló további mezők naplózza az esemény kapcsolatos részletesebb információk megadására. Az alábbi tulajdonságokat a fürt megosztott kötetei szolgáltatás minden egyes sorára használhatók:

| Tulajdonság neve | / Formátumban |
| --- | --- |
| Dátum |A dátum és időpont, amikor az esemény történt |
| Szint |Eseményszint (pl. hiba, figyelmeztetés, információ) |
| ApplicationName |A webes alkalmazás neve |
| instanceId |A webes alkalmazás, amely az esemény példányainak |
| EventTickCount |A dátum és időpont, amikor az esemény történt osztásjelek formátumban (nagyobb pontosságú) |
| Eseményazonosító |Ez az esemény esemény azonosítója<p><p>Az alapértelmezett érték 0, ha nincs megadva |
| Azonosítója (PID) |Folyamatazonosító |
| TID |A szál az esemény előállított a Szálazonosító |
| Üzenet |Üzenet esemény részletei |

A blob tárolt adatok a következőhöz hasonló lenne:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> Az első sor a napló fogja tartalmazni az oszlopfejlécek, ahogy ebben a példában.
>
>

### <a name="failed-request-traces"></a>Sikertelen kérelmek nyomkövetési
Sikertelen kérelmek nyomkövetési nevű XML-fájlok tárolják **fr ### .xml**. A naplózott információk megkönnyítése XSL-stíluslap nevű **freb.xsl** található az XML-fájlok könyvtárába. Az XML-fájlok megnyitása az Internet Explorer használatával az XSL-stíluslap adja meg a nyomkövetési adatok egy formázott megjelenítése. Ez jelenik meg a következőhöz hasonló:

![a böngészőben sikertelen kérelem](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Részletes hibanaplókat.
Részletes hibanaplókat a HTML-dokumentumok, amelyek részletes információkat biztosítanak történt a HTTP-hibák. Mivel azok csak a HTML-dokumentumok, azok megtekinthetők a webböngésző.

### <a name="web-server-logs"></a>Webkiszolgáló naplóinak
A webkiszolgáló naplóinak használatával formázott a [W3C bővített naplófájlformátum](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ezeket az információkat egy szövegszerkesztővel olvasható, vagy a Delimiters segédprogramok például [napló elemző](http://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Az Azure web Apps alkalmazások által létrehozott naplók nem támogatják a **s-computername**, **s-ip**, vagy **cs-version** mezőket.
>
>

## <a name="nextsteps"></a> Következő lépések
* [Webalkalmazások figyelése](web-sites-monitor.md)
* [Hibaelhárítás a Visual Studio Azure-webalkalmazásokban](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Elemzése a Hdinsightban webalkalmazás-naplói](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
>
>

## <a name="whats-changed"></a>A változások
* Információk a Websites szolgáltatásról az App Service-re való váltásról: [Az Azure App Service és a hatása a meglévő Azure-szolgáltatásokra](http://go.microsoft.com/fwlink/?LinkId=529714)
* A régi portál, az új portálon való váltásról: [útmutató az Azure-portálon lépjen a](http://go.microsoft.com/fwlink/?LinkId=529715)
