---
title: Alkalmazások teljesítményének – gyakori kérdések – Azure App Service-ben |} A Microsoft Docs
description: Az Azure App Service Web Apps funkcióját a rendelkezésre állási, teljesítmény és alkalmazásproblémák kapcsolatos gyakori kérdésekre választ kaphat.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 3a0b92e2c2a588fa8ef7edcddaa8c692780814f0
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53388918"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Alkalmazások teljesítményének – gyakori kérdések az Azure Web Apps esetében

Ez a cikk rendelkezik adott válaszok a gyakori kérdések (GYIK) kapcsolatos alkalmazásteljesítménnyel kapcsolatos problémák esetében a [Azure App Service Web Apps funkciójával](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Miért érdemes az alkalmazásom lassú?

Számos tényező lehet, hogy hozzájárul a teljesítmény az alkalmazás. Részletes hibaelhárítási lépéseket: [hibaelhárítás lassú WebApp teljesítményének](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hogyan háríthatom el a magas CPU-használat a forgatókönyv?

Az egyes magas CPU-használati forgatókönyvek az alkalmazás valóban szükség lehet több számítási erőforrást. Ebben az esetben érdemes lehet magasabb szolgáltatási szintre skálázást, így az alkalmazás megjeleníti az összes szükséges erőforrást. Más, magas CPU-használat oka lehet egy rossz hurkot, vagy egy kódolási gyakorlatot. Első kezdeményezi megnövekedett processzorhasználatot betekintést az egy kétlépéses folyamat. Először hozzon létre egy folyamatot memóriakép, és a folyamat memóriakép majd elemezni. További információkért lásd: [rögzítésével és elemzésével a magas CPU-használat, a Web Apps memóriaképfájl](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hogyan háríthatom el a magas memória-felhasználás forgatókönyv?

Magas memória-felhasználás bizonyos esetekben az alkalmazás valóban szükség lehet több számítási erőforrást. Ebben az esetben érdemes lehet magasabb szolgáltatási szintre skálázást, így az alkalmazás megjeleníti az összes szükséges erőforrást. A többi időszakban a kódot tartalmaz hibát okozhat memóriavesztés. A kódolási gyakorlatot is növelheti a memóriát. Első kezdeményezi használat egy kétlépéses folyamat: magas memória betekintést. Először hozzon létre egy folyamatot memóriakép, és a folyamat memóriakép majd elemezni. Az Azure Site bővítmény katalógus összeomlási diagnosztikai hatékonyan is el tudja végezni mind ezeket a lépéseket. További információkért lásd: [rögzítésével és elemzésével az időszakos nagy memória memóriaképfájlt a Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hogyan automatizálható az App Service web apps PowerShell-lel?

PowerShell-parancsmagok segítségével kezeli és tartja karban az App Service web apps szolgáltatásban. Az ebben a blogbejegyzésben [automatizálása a PowerShell-lel az Azure App Service-ben üzemeltetett webalkalmazások](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), megtudhatja, hogyan automatizálhatja a gyakori feladatokat az Azure Resource Manager-alapú PowerShell-parancsmagok használatával. A blogbejegyzést a web apps különböző felügyeleti feladatainak mintakód is tartalmaz. Leírások és minden App Service web apps parancsmagjainak szintaxisa: [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hogyan tekinthetem meg a webes alkalmazás eseménynaplóit?

A webes alkalmazás eseménynaplóit megtekintése:

1. Jelentkezzen be a [Kudu-webhely](https://*yourwebsitename*.scm.azurewebsites.net).
2. Válassza a menüben **hibakeresési konzolt** > **CMD**.
3. Válassza ki a **LogFiles** mappát.
4. Az eseménynaplók megtekintésére, kattintson a ceruza ikonra a **eventlog.xml**.
5. A naplók letöltéséhez futtassa a PowerShell-parancsmag `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hogyan rögzítése a saját webes alkalmazás a felhasználói módú memóriakép?

A webalkalmazás egy felhasználói módú memóriakép rögzítésére:

1. Jelentkezzen be a [Kudu-webhely](https://*yourwebsitename*.scm.azurewebsites.net).
2. Válassza ki a **Process Explorer** menü.
3. Kattintson a jobb gombbal a **w3wp.exe** vagy a webjobs-feladat folyamatban.
4. Válassza ki **memóriakép letöltése** > **teljes memóriakép**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hogyan tudom megtekinteni az folyamatszintű adatait saját webalkalmazás?

A webalkalmazás folyamatszintű megtekintésére két lehetősége van:

*   Az Azure Portalon:
    1. Nyissa meg a **Process Explorer** a webalkalmazás számára.
    2. A részletek megtekintéséhez válassza ki a **w3wp.exe** folyamat.
*   A Kudu konzol:
    1. Jelentkezzen be a [Kudu-webhely](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Válassza ki a **Process Explorer** menü.
    3. Az a **w3wp.exe** folyamat válassza **tulajdonságok**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Ha szeretnék saját alkalmazás megkeresése tallózással, látom "403-as hiba – Ez a webalkalmazás leállt." Hogyan oldhatom fel ez?

Három feltétel ezt a hibát okozhatják:

* A webalkalmazás elérte a számlázási korlátot, és a webhely le van tiltva.
* A webalkalmazás le lett állítva a portálon.
* A webalkalmazás elérte a alkalmazni lehet egy ingyenes, vagy megosztott scale service-csomag erőforráskvóta határát.

Tekintse meg, mi okozza a hibát, és a probléma megoldásához, kövesse a lépéseket a [Web Apps: "403-as hiba – Ez a webalkalmazás leállt"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Hol találhatok további többet kvótái és korlátai a különféle App Service-csomagok?

További információ a kvóták és korlátozások: [az App Service korlátai](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hogyan csökkentheti a az első kérelem válaszideje üresjárati idő után?

Alapértelmezés szerint a webalkalmazások távolítva a memóriából ha azok egy megadott ideig inaktív. Ezzel a módszerrel a rendszer erőforrások is megőrzése. A hátránya, hogy az első kérelem után a webes alkalmazás memóriából adott válasz hosszabb, a web app, tölthet be és indítsa el a teljesítése engedélyezéséhez. Az alapszintű és standard szintű szolgáltatáscsomag, bekapcsolhatja a **Always On** a beállítást, hogy az alkalmazás mindig betöltve. Ezzel elkerülhető, miután az alkalmazás üresjárati hosszabb betöltési időt. Módosíthatja a **Always On** beállítást:

1. Az Azure Portalon lépjen a webalkalmazáshoz.
2. Válassza ki **Alkalmazásbeállítások**.
3. A **Always On**válassza **a**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hogyan bekapcsolja a sikertelen kérelmek nyomkövetése?

Sikertelen kérelmek nyomkövetése bekapcsolása:

1. Az Azure Portalon lépjen a webalkalmazáshoz.
3. Válassza ki **minden beállítás** > **diagnosztikai naplók**.
4. A **sikertelen kérelmek nyomkövetését**válassza **a**.
5. Kattintson a **Mentés** gombra.
6. Válassza ki a webalkalmazás panelére, **eszközök**.
7. Válassza ki **Visual Studio Online**.
8. Ha a beállítás nem **a**válassza **a**.
9. Válassza ki **Go**.
10. Válassza ki **Web.config**.
11. System.webServer adja hozzá ezt a konfigurációt (a rögzítése egy adott URL-címe):

    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Teljesítményével kapcsolatos problémák elhárításához, adja hozzá ezt a konfigurációt (Ha a rögzítés kérelem van véve a több mint 30 másodperc):
    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. A sikertelen kérelmek nyomkövetési letöltését a [portál](https://portal.azure.com), nyissa meg a webhelyet.
15. Válassza ki **eszközök** > **Kudu** > **Go**.
18. Válassza a menüben **hibakeresési konzolt** > **CMD**.
19. Válassza ki a **LogFiles** mappát, és válassza ki a mappát egy kezdetű névvel rendelkező **W3SVC**.
20. Az XML-fájl megtekintéséhez válassza a ceruza ikonra.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>"A munkavégző folyamat újraindítása miatt" Százalékos memóriakorlátját "kért." a következő üzenet jelenik meg Hogyan oldja meg a probléma?

A rendelkezésre álló memória maximális mérete 32 bites a folyamatot (akár a 64 bites operációs rendszeren) mérete 2 GB. Alapértelmezés szerint a munkavégző folyamat van beállítva, a 32 bites (a régi webes alkalmazásokkal való kompatibilitásáról) App Service-ben.

Fontolja meg az áttérést a 64 bites folyamatokat, így kihasználhatja a webes feldolgozói szerepkörben további memória. Ez aktiválja újra kell indítani a webes alkalmazást, ezért ütemezze ennek megfelelően.

Azt is vegye figyelembe, hogy egy 64 bites környezet egy alapszintű vagy standard szintű szolgáltatáscsomag szükséges. Az ingyenes és közös csomagok mindig 32-bit-es környezetben futnak.

További információkért lásd: [webalkalmazások konfigurálása az App Service-ben](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Miért nem a kérelem időkorlátja 230 másodperc után?

Az Azure Load Balancer rendelkezik a négy perces üresjárati időkorlát alapértelmezett beállítása. Ez általában a webes kérelem ésszerű válasz határidőt. Ha a webalkalmazás háttérben történő feldolgozás van szüksége, az Azure WebJobs használatát javasoljuk. Az Azure web Apps segítségével meghívhatja a webjobs-feladatok, és értesíti, ha a háttérben történő feldolgozása befejeződött. A WebJobs, beleértve az üzenetsorokat és eseményindítók használatával többféle módszer közül választhat.

Webjobs-feladatok háttérben történő feldolgozás van tervezve. Legtöbb háttérben történő feldolgozási webjobs-feladat a kívánt módon teheti meg. Webjobs-feladatok kapcsolatos további információkért lásd: [háttérfeladatok futtatása WebJobs-feladatokkal](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core alkalmazásokhoz, amelyek az App Service-ben néha nem válaszol. Hogyan lehet kijavítani a hibát a probléma?

Egy ismert probléma, amely egy korábbi [Kestrel verzió](https://github.com/aspnet/KestrelHttpServer/issues/1182) időnként nem válaszol az App Service-ben futó ASP.NET Core 1.0-s alkalmazás vezethet. Emellett előfordulhat, hogy megjelenik ez az üzenet: "A megadott CGI-alkalmazás hibába ütközött, és a kiszolgáló leállt, a folyamat."

Ezt a problémát megoldottuk a Kestrel 1.0.2-es verzióját. Ez a verzió része az ASP.NET Core 1.0.3 frissítés. A probléma megoldásához ellenőrizze az alkalmazás függőségei a Kestrel 1.0.2-es használatához frissítenie. Azt is megteheti, hogy is két kerülő megoldások valamelyikével azt ebben a blogbejegyzésben leírt [ASP.NET Core 1.0-s lassú teljesítmény az App Service web apps problémák](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>A naplófájlok nem található webalkalmazás fájl szerkezete Honnan tudhatom meg őket?

Ha az App Service helyi gyorsítótára funkcióját használja, a mappastruktúra az App Service-példány LogFiles és az adatok mappa érinti. Helyi gyorsítótár használata esetén a naplófájlok tárolási és adatmappáinak almappák jönnek létre. Az almappák használja az elnevezési minta "egyedi azonosító" + időbélyeg. Minden almappa felel meg a webalkalmazás fut, vagy futott egy Virtuálisgép-példánnyal.

Annak megállapításához, hogy helyi gyorsítótárat használ-e, ellenőrizze az App Service **Alkalmazásbeállítások** fülre. Ha a helyi gyorsítótár használatban van, az Alkalmazásbeállítás `WEBSITE_LOCAL_CACHE_OPTION` értékre van állítva `Always`.

Ha nem használja a helyi gyorsítótárban, és ezzel a problémával találkozik, küldjön egy támogatási kérést.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Hibaüzenet jelenik meg: "kísérlet történt olyan módon, a hozzáférési engedélyeket tiltott hozzáférés." Hogyan oldhatom fel ez?

Ez a hiba általában akkor fordul elő, ha elfogytak, a kimenő TCP-kapcsolatok Virtuálisgép-példányon. Az App Service-ben vannak korlátozva, hogy az egyes Virtuálisgép-példányok kimenő kapcsolatok maximális számát. További információkért lásd: [közötti-virtuális gép numerikus korlátok](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Ezt a hibát is okozhat, ha megpróbálja elérni a helyi cím az alkalmazásból. További információkért lásd: [helyi cím kérelmek](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Kimenő kapcsolatok a webalkalmazásban kapcsolatos további információkért lásd a következő blogbejegyzésben: kapcsolatos [kimenő kapcsolatok az Azure-webhelyek](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hogyan használhatom a Visual Studio távoli hibakeresési App Service webalkalmazás?

Részletes útmutató, amely bemutatja, hogyan webes alkalmazás hibakeresése a Visual Studio használatával, lásd: [távoli hibakeresése az App Service webalkalmazás](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
