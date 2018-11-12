---
title: Az Azure App Service web Apps-alkalmazások diagnosztikai célú naplózásának engedélyezése
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
ms.openlocfilehash: 0c22072d0eaa328fdf786421344e8ef2caaa575c
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515658"
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>Az Azure App Service web Apps-alkalmazások diagnosztikai célú naplózásának engedélyezése
## <a name="overview"></a>Áttekintés
Az Azure biztosít beépített diagnosztikai funkciókkal, amelyek segítik a hibakeresést egy [App Service-webalkalmazás](https://go.microsoft.com/fwlink/?LinkId=529714). Ebből a cikkből megtudhatja, hogyan diagnosztikai naplózás engedélyezése és az üzemállapot-alkalmazását, valamint hogyan lehet hozzáférni azokhoz az információkhoz, amelyeket az Azure naplóz.

Ez a cikk a [az Azure portal](https://portal.azure.com) és az Azure CLI-vel való együttműködéshez a diagnosztikai naplók. Diagnosztikai naplók a Visual Studio használatával való munka információkért lásd: [Azure hibaelhárítása a Visual Studióban](web-sites-dotnet-troubleshoot-visual-studio.md).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Web server diagnostics és az application diagnostics
Az App Service web apps adja meg a diagnosztikai adatok naplózása a webalkalmazás-kiszolgáló és a webes alkalmazás funkciói. Ezek logikailag elkülönített **kiszolgálódiagnosztika webes** és **az application diagnostics**.

### <a name="web-server-diagnostics"></a>Webes kiszolgálódiagnosztika
Engedélyezheti vagy letilthatja a naplók a következő típusú:

* **Részletes hibanaplózás** – részletes (állapotkód: 400 vagy nagyobb) hibát jelző HTTP-állapotkódok hiba adatait. Tartalmazhat, amelyek segíthetnek meghatározni, miért érdemes a kiszolgáló a következő hibakódot adta vissza információt.
* **Sikertelen kérelmek nyomkövetésére vonatkozó** – részletes információk a sikertelen kérelmek, beleértve a nyomkövetés feldolgozni a kérelmet, és az egyes összetevőkben ideje használja az IIS-összetevőt. Ez akkor hasznos, ha próbált webhely teljesítményének növelése vagy elkülönítése, mi kell visszaadni egy adott HTTP hiba okozza.
* **Webalkalmazás-kiszolgáló naplózási** – HTTP-tranzakciót használatával kapcsolatos információkat a [W3C bővített naplófájlformátum](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ez hasznos, teljes webhelymetrikák például kezelt kérések, vagy hogy hány kérésnek egy adott IP-címről számának meghatározásakor.

### <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
Az Application diagnostics lehetővé teszi egy webalkalmazás által létrehozott adatok rögzítését. ASP.NET-alkalmazások használhatják a [System.Diagnostics.Trace](https://msdn.microsoft.com/library/36hhw2t6.aspx) osztályt az alkalmazásnaplóba diagnosztikai információk naplózása. Példa:

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

Futásidőben a naplók segíthetnek a hibaelhárításban kérheti le. További információkért lásd: [hibaelhárítása Azure-webalkalmazások Visual studióban](web-sites-dotnet-troubleshoot-visual-studio.md).

App Service web apps szolgáltatásban is naplózza az üzembe helyezési információkat, ha tartalmat tesz közzé egy webalkalmazás. Automatikusan megtörténik, és nem a központi telepítési naplózáshoz konfigurációs beállításokat. Üzembe helyezés naplózás lehetővé teszi, hogy meghatározhatja, miért érdemes a központi telepítés nem sikerült. Egy egyéni üzembehelyezési szkript használ, előfordulhat, hogy meghatározni, miért nem működik a parancsfájl például használjon központi telepítési naplózást.

## <a name="enablediag"></a>Diagnosztika engedélyezése
Diagnosztika engedélyezése a a [az Azure portal](https://portal.azure.com), nyissa meg a webalkalmazás az lapját, és kattintson a **beállítások > diagnosztikai naplók**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Rész-naplók](./media/web-sites-enable-diagnostic-log/logspart.png)

Amikor engedélyezi a **az application diagnostics**, azt is választhatja a **szint**. Ez a beállítás lehetővé teszi az adatokat, a rögzített szűrést **tájékoztató**, **figyelmeztetés**, vagy **hiba** információkat. Értékre állítaná **részletes** minden információt az alkalmazás által előállított naplók.

> [!NOTE]
> Ellentétben a web.config fájl módosítása, az Application diagnostics engedélyezése vagy diagnosztikai napló szintek módosítása nem indul, amely az alkalmazást futtató belül alkalmazástartomány.
>
>

A **alkalmazásnaplózás**, bekapcsolhatja a hibakeresési célokra az ideiglenes fájl helyrendszer-beállítást. 12 óra múlva automatikusan kikapcsolja ezt a beállítást. A blob beállítást válassza ki a naplók írhat egy blob-tárolóba is bekapcsolhatja.

A **webkiszolgálói naplózás**, választhat **tárolási** vagy **fájlrendszer**. Kiválasztásával **tárolási** lehetővé teszi, hogy válasszon egy tárfiókot, és egy blobtárolót a naplók írt. 

Ha a naplók tárolása a fájlrendszer, a fájlok elérhető FTP, és letöltött Zip-archívumot, Azure parancssori felület használatával.

Alapértelmezés szerint naplók nem törlődnek automatikusan (kivéve a **Application Logging (fájlrendszer)**). Naplók automatikus törléséhez állítsa be a **megőrzési ideje (nap)** mező.

> [!NOTE]
> Ha Ön [a tárfiók hozzáférési kulcsainak újragenerálása](../storage/common/storage-create-storage-account.md), alaphelyzetbe kell állítania a megfelelő naplózási konfiguráció frissített kulcsok használatához. Ehhez tegye a következőket:
>
> 1. Az a **konfigurálása** lapon, a megfelelő naplózási szolgáltatás beállítása **ki**. A beállítás mentéséhez.
> 2. Engedélyezze a naplózást a naplótárolásifiók-blob vagy table újra. A beállítás mentéséhez.
>
>

Fájlrendszer, a table storage vagy a blob storage bármilyen kombinációját is engedélyezhető egyszerre, és az egyes naplózási szintekhez rendelkezik. Például érdemes a hibák és figyelmeztetések naplózása hosszú távú megoldás, miközben a rendszer naplózását részletes szintű blob storage-bA.

Habár minden három tárolási helyek naplózott eseményeket, ugyanazokat az alapvető információkat biztosít **táblatároló** és **a blob storage-** további információk, például a példány Azonosítóját, a hozzászóláslánc azonosítója és a egy egyéb naplózása részletes timestamp (osztásjelek formátum), mint a naplózás **fájlrendszer**.

> [!NOTE]
> A tárolt adatok **táblatároló** vagy **a blob storage-** csak érhetők el a storage-kliens vagy olyan alkalmazás, amely közvetlenül is dolgozhat a tárolórendszerek használatával. Például a Visual Studio 2013 tartalmaz, amelyek segítségével ismerje meg a tábla- vagy blob storage a Storage Explorer, és a HDInsight érhessék el az a blob storage-ban tárolt adatokat. Egy alkalmazás, amely hozzáfér az Azure Storage egyikének használatával is kiírhatja a [Azure SDK-k](https://azure.microsoft.com/downloads/).
>

## <a name="download"></a> Hogyan: naplók letöltése
Diagnosztikai adatok tárolása a webes alkalmazáshoz fájlrendszerben közvetlenül az FTP használatával is elérhetők. Le is tölthetők, egy Zip-archívumot az Azure CLI használatával.

A könyvtárstruktúra, a naplók vannak tárolva a következőképpen történik:

* **Protokoly aplikací** -/LogFiles/alkalmazás /. Ez a mappa tartalmaz egy vagy több, az alkalmazásadatok naplózása által előállított adatokat tartalmazó szöveges fájlok.
* **Sikertelen kérelmek nyomkövetési** -/ LogFiles/W3SVC ### /. Ez a mappa tartalmaz egy XSL-fájl és a egy vagy több XML-fájlt. Győződjön meg arról, le kell tölteni a XSL-fájl ugyanabban a könyvtárban való, az XML-fájl fájl, mert a XSL-fájl formázására és az Internet Explorer megtekintve XML fájl(ok) tartalmát szűrés funkciót biztosít.
* **Részletes hibanaplókat** -/LogFiles/DetailedErrors /. Ez a mappa tartalmaz egy vagy több HTTP-hibaüzeneteket előfordult széleskörű információkat biztosító .htm fájlt.
* **Webalkalmazás-naplók** -/LogFiles/http/RawLogs. Ez a mappa tartalmaz egy vagy több szöveges fájlok formázva a [W3C bővített naplófájlformátum](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).
* **Telepítési naplók** -/ LogFiles/Git. Ez a mappa tartalmazza az Azure web Apps alkalmazások által használt belső üzembe helyezési folyamat által létrehozott naplók, valamint-naplókban a Git-telepítéseket. Telepítési naplók D:\home\site\deployments alapján is megtalálhatja.

### <a name="ftp"></a>FTP

Tekintse meg az alkalmazás FTP-kiszolgálóhoz FTP-kapcsolat megnyitásához [alkalmazás üzembe helyezése az Azure App Service-ben FTP/S használatával](app-service-deploy-ftp.md).

Ha csatlakoztatva van a webalkalmazás FTP/S-kiszolgálóra, nyissa meg a **LogFiles** a naplófájlokat tároló mappa.

### <a name="download-with-azure-cli"></a>Töltse le az Azure CLI-vel
Töltse le a naplófájlok, az Azure parancssori felülettel, nyisson meg egy új parancssort, PowerShell, a Bash vagy a terminál-munkamenetet, és adja meg a következő parancsot:

    az webapp log download --resource-group resourcegroupname --name webappname

Ez a parancs menti a naplókat a webalkalmazás neve "webappname" nevű fájlba **diagnostics.zip** az aktuális könyvtárban található.

> [!NOTE]
> Ha nem telepítette az Azure CLI-vel, vagy nincs konfigurálva, hogy az Azure-előfizetéssel, lásd: [hogyan használja az Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
>
>

## <a name="how-to-view-logs-in-application-insights"></a>Hogyan: naplók megtekintése az Application insights szolgáltatásban
A Visual Studio Application Insights a szűréshez és a naplók keresése, és a kérésekkel és más eseményekkel való korreláláshoz a naplók eszközöket biztosít.

1. Az Application Insights SDK hozzáadása a projekthez a Visual Studióban.
   * A Megoldáskezelőben kattintson jobb gombbal a projektre, és válassza ki az Application Insights hozzáadása. Az interface végigvezeti lépéseket, amelyek tartalmazzák az Application Insights-erőforrás létrehozása. [További információ](../application-insights/app-insights-asp-net.md)
2. A nyomkövetés-figyelő csomag hozzáadása a projekthez.
   * Kattintson jobb gombbal a projektre, és válassza a NuGet-csomagok kezelése. Válassza ki `Microsoft.ApplicationInsights.TraceListener` [további](../application-insights/app-insights-asp-net-trace-logs.md)
3. Töltse fel a projektet, majd futtassa készítése a naplózási adatokat.
4. Az a [az Azure portal](https://portal.azure.com/), keresse meg az új Application Insights-erőforrást, és nyissa meg a **keresési**. A naplózási adatokat, és a kérést, használatának és egyéb telemetriát kell megjelennie. Valamennyi telemetria is igénybe vehet néhány percet késik: a frissítés parancsra. [További információ](../application-insights/app-insights-diagnostic-search.md)

[További tudnivalók a teljesítmény nyomon követése az Application insights segítségével](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a> Útmutató: Stream naplók
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

    az webapp log tail --name webappname --resource-group myResourceGroup

Ez a parancs "webappname" nevű webalkalmazást csatlakozik, és elkezdeni a streamelést az ablak információk alkalmazásnapló-események bekövetkezésekor a webalkalmazásban. Minden olyan információt írni a .txt, .log vagy .htm végződésű (d:/home/logfiles) /LogFiles a címtárban tárolt fájlok a helyi konzol adatfolyamként.

Adott események, hibák, például szűréséhez használja a **– szűrő** paraméter. Példa:

    az webapp log tail --name webappname --resource-group myResourceGroup --filter Error

Szűrés konkrét naplófájlokból típusok, például a HTTP, használja a **– elérési út** paraméter. Példa:

    az webapp log tail --name webappname --resource-group myResourceGroup --path http

> [!NOTE]
> Ha nem telepítette az Azure CLI-vel, vagy nincs konfigurálva, hogy az Azure-előfizetéssel, lásd: [hogyan használja az Azure CLI](../cli-install-nodejs.md).
>
>

## <a name="understandlogs"></a> Hogyan: megismerheti a diagnosztikai naplók
### <a name="application-diagnostics-logs"></a>Application diagnostics-naplók
Az Application diagnostics adatait tárolja egy megadott formátumban, a .NET-alkalmazásokban, attól függően, hogy naplókat a fájlrendszer, a table storage vagy a blob storage tárolja. Az Alap az adatkészlethez tartozó tárolt összes három tárolási típusok közötti – a dátum és idő az esemény történt, a Folyamatazonosítója, amely az eseményt, az esemény típusa (információk, figyelmeztetés, hiba) és az eseményüzenet előállított kérelemegysége megegyezik.

**Fájlrendszer**

Az egyes sorok naplózza a fájlrendszer vagy fogadott streamelési használatával a következő formátumban kell megadni:

    {Date}  PID[{process ID}] {event type/level} {message}

Ha például egy hibaesemény a következőképpen fog a következő mintához hasonló:

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

A fájlrendszer naplózás legalapvetőbb információkat talál a három elérhető módszerek, csak az idő, Folyamatazonosító, Eseményszint, és üzenet megadása.

**Table Storage**

Ha jelentkezik a table storage, a további tulajdonságok médiatár a tábla, valamint az esemény részletesebb tájékoztatást a tárolt adatok használhatók. Minden entitás (sor), a táblában tárolt szolgálnak a következő tulajdonságok (oszlop).

| Tulajdonság neve | / Formátumban |
| --- | --- |
| PartitionKey |Az esemény yyyyMMddHH formátumú dátum/idő |
| RowKey |Egy GUID azonosítót, amely egyedileg azonosítja az entitást |
| Időbélyeg |A dátum és időpont, amikor az esemény történt |
| EventTickCount |A dátum és időpont, amikor az esemény történt, osztásjelek formátumban (nagyobb pontosság) |
| Alkalmazásnév |A webalkalmazás neve |
| Szint |Eseményszint (például hiba, figyelmeztetés, információ) |
| EventId |Ez az esemény esemény azonosítója<p><p>Az alapértelmezett érték 0, ha nincs megadva |
| Példány azonosítója |A webalkalmazás, amely a még akkor is történt a példány |
| Folyamatazonosító |Folyamat azonosítója |
| TID |Az esemény előállított szál hozzászóláslánc azonosítója |
| Üzenet |Eseménynapló-üzenet részletei |

**Blob Storage**

Blob storage-ba történő bejelentkezéskor adatok vesszővel elválasztott értékeket (CSV) formátum van tárolva. Hasonló a table storage, további mezőket a rendszer naplózza az esemény kapcsolatos részletesebb információkat biztosít. A következő tulajdonságokat a fürt megosztott kötetei szolgáltatás minden egyes sorára használhatók:

| Tulajdonság neve | / Formátumban |
| --- | --- |
| Dátum |A dátum és időpont, amikor az esemény történt |
| Szint |Eseményszint (például hiba, figyelmeztetés, információ) |
| Alkalmazásnév |A webalkalmazás neve |
| Példány azonosítója |A webes alkalmazás, amely az esemény példánya |
| EventTickCount |A dátum és időpont, amikor az esemény történt, osztásjelek formátumban (nagyobb pontosság) |
| EventId |Ez az esemény esemény azonosítója<p><p>Az alapértelmezett érték 0, ha nincs megadva |
| Folyamatazonosító |Folyamat azonosítója |
| TID |Az esemény előállított szál hozzászóláslánc azonosítója |
| Üzenet |Eseménynapló-üzenet részletei |

A blob tárolt adatokat ehhez hasonlóan néz ki az alábbi példához:

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> A napló az első sor az oszlopfejléceket tartalmaz, ebben a példában határozzák meg.
>
>

### <a name="failed-request-traces"></a>Sikertelen kérelmek nyomkövetési
Sikertelen kérelmek nyomkövetési nevű XML-fájlokban van tárolva **fr ### .xml**. A naplózott információk megkönnyítése XSL stíluslapok nevű **freb.xsl** megtalálható az XML-fájlok könyvtárába. Ha megnyit egy XML-fájlt az Internet Explorer, az Internet Explorer a XSL stíluslap egy formázott megjelenítése a nyomkövetési adatok, az alábbi példához hasonló biztosításához használt:

![sikertelen kérelmek, a böngészőben](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>Részletes hibanaplókat
Részletes hibanaplókat olyan HTML-dokumentumok, amelyek előfordult HTTP-hibák részletes információkat biztosítanak. Egyszerűen csak a HTML-dokumentumok, mivel azok tekinthet meg webböngészővel.

### <a name="web-server-logs"></a>Webkiszolgáló naplói
A webkiszolgáló-naplók használatával formázott a [W3C bővített naplófájlformátum](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Ez az információ egy szövegszerkesztő használatával olvashatja, vagy segédprogramok használatával például elemzett [naplóelemző](https://go.microsoft.com/fwlink/?LinkId=246619).

> [!NOTE]
> Az Azure-webalkalmazások által előállított naplók nem támogatják a **s-computername**, **s-ip**, vagy **cs-version** mezőket.
>
>

## <a name="nextsteps"></a> Következő lépések
* [Web Apps alkalmazások figyelése](web-sites-monitor.md)
* [Hibaelhárítás Azure-webalkalmazások Visual studióban](web-sites-dotnet-troubleshoot-visual-studio.md)
* [Elemzése a HDInsight webalkalmazás-naplók](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> Ha az Azure App Service-t az Azure-fiók regisztrálása előtt szeretné kipróbálni, ugorjon [Az Azure App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. Itt azonnal létrehozhat egy ideiglenes, kezdő szintű webalkalmazást az App Service szolgáltatásban. Ehhez nincs szükség bankkártyára, és nem jár kötelezettségekkel.
>
>
