---
title: Alkalmazásteljesítmény – gyakori kérdések az Azure web Apps |} Microsoft Docs
description: Adott válaszok rendelkezésre állását, teljesítményét és alkalmazással kapcsolatos problémák kapcsolatos gyakori kérdések az Azure App Service Web Apps szolgáltatása.
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
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 9c8eda1c4a65465a93b9c3f9caf23a6663073f26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159290"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Az Azure-webalkalmazások alkalmazásteljesítmény – gyakori kérdések

Ez a cikk rendelkezik válaszok gyakran ismételt kérdések (GYIK) kapcsolatos teljesítményproblémák alkalmazás számára a [az Azure App Service Web Apps szolgáltatásának](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Miért van lassú alkalmazásom?

Több tényezővel hozzájárulhat teljesítménycsökkenés az alkalmazást. Hibaelhárítási lépések részletes: [kapcsolatos problémák elhárítása lassú webes alkalmazás teljesítménye](app-service-web-troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hogyan hibáinak elhárítása a nagy CPU-felhasználás forgatókönyv?

Néhány magas CPU-felhasználás forgatókönyvet az alkalmazás valóban szüksége elegendő számítási erőforrás. Ebben az esetben fontolja meg egy magasabb szintű szolgáltatási réteg méretezhetők, az alkalmazás az összes szükséges erőforrások lekérése. Más, magas CPU-felhasználás oka lehet hibás hurkot vagy egy kódolási gyakorlatot. Mi van kiváltó megnövekedett CPU-felhasználás betekintést első egy kétlépéses folyamat. Először hozzon létre a folyamatkép-kiírást, és majd elemezze a folyamatkép-kiírást. További információkért lásd: [rögzíteni és elemezni a Web Apps magas CPU-felhasználás memóriaképfájl](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hogyan hibáinak elhárítása a nagy memória-felhasználás forgatókönyv?

Néhány nagy memória-felhasználás forgatókönyvet az alkalmazás valóban szüksége elegendő számítási erőforrás. Ebben az esetben fontolja meg egy magasabb szintű szolgáltatási réteg méretezhetők, az alkalmazás az összes szükséges erőforrások lekérése. A többi időszakban a kódban programhiba okozhatja memóriavesztés. A kódolási célszerű is növelheti a rendszermemóriát. Mi az indításhoz magas memória fogyasztása egy kétlépéses folyamat betekintést beolvasásakor. Először hozzon létre a folyamatkép-kiírást, és majd elemezze a folyamatkép-kiírást. Az Azure Site bővítmény katalógusából összeomlási diagnosztikai hatékonyan végezheti mindkét ezeket a lépéseket. További információkért lásd: [rögzíteni és elemezni az időszakos felső memóriaterület memóriaképfájl Web Apps](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hogyan automatizálható az App Service web apps PowerShell használatával?

PowerShell-parancsmagok segítségével kezeli és tartja karban az App Service web Apps alkalmazások. A blogbejegyzésben [automatizálása a PowerShell használatával az Azure App Service-ben futó webalkalmazások](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/), azt mutatják be, hogyan Azure Resource Manager-alapú PowerShell-parancsmagok használatával gyakori feladatok automatizálásához. A blogbejegyzést is rendelkezik különböző web apps felügyeleti feladatok mintakód. Minden App Service web apps parancsmagok szintaxisát és leírásokat, lásd: [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hogyan tekinthetem meg a webalkalmazás-eseménynaplók?

A webalkalmazás-eseménynaplók megtekintése:

1. Jelentkezzen be a [Kudu webhely](https://*yourwebsitename*.scm.azurewebsites.net).
2. A menüben válasszon ki **Debug konzol** > **CMD**.
3. Válassza ki a **naplófájlok** mappát.
4. Az eseménynaplók megtekintésére, jelölje be a ceruza ikonra a **eventlog.xml**.
5. A naplók letöltéséhez futtassa a PowerShell-parancsmagot `Save-AzureWebSiteLog -Name webappname`.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hogyan rögzítése a saját webes alkalmazás a felhasználói módú memóriakép?

Rögzítheti a felhasználói módú memóriakép webalkalmazás:

1. Jelentkezzen be a [Kudu webhely](https://*yourwebsitename*.scm.azurewebsites.net).
2. Válassza ki a **Process Explorer** menü.
3. Kattintson a jobb gombbal a **w3wp.exe** vagy a webjobs-feladat folyamatban.
4. Válassza ki **memóriakép letöltése** > **teljes biztonsági másolat**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hogyan tekinthetem meg folyamatszintű információ a webalkalmazás?

A webalkalmazás folyamatszintű adatok megtekintéséhez használatos időkategóriát két lehetőség közül választhat:

*   Az Azure Portalon:
    1. Nyissa meg a **Process Explorer** a webalkalmazás számára.
    2. A részletek megtekintéséhez válasszon a **w3wp.exe** folyamat.
*   A Kudu konzolon:
    1. Jelentkezzen be a [Kudu webhely](https://*yourwebsitename*.scm.azurewebsites.net).
    2. Válassza ki a **Process Explorer** menü.
    3. Az a **w3wp.exe** folyamat, jelölje be **tulajdonságok**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Szeretnék saját alkalmazás tallózással, jelenik meg "Hiba 403 – Ez a webalkalmazás le lett állítva." Hogyan lehet elhárítani ezt?

Három feltétel ezt a hibát okozhatják:

* A webalkalmazás elérte a számlázási korlátozása, és a webhely le van tiltva.
* A webalkalmazás le lett állítva, a portálon.
* A webalkalmazás elérte a szabad is vonatkozhatnak, vagy megosztott méretezési service-csomag erőforrás kvótát.

Mi okozza a hibát, és a probléma megoldásához, kövesse a lépéseket [Web Apps: "Hiba 403 – Ez a webalkalmazás leállt"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Hol kaphatok további információk a kvótái és korlátai különböző App Service-csomagok?

További információ a kvótái és korlátai: [App Service limits](../azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hogyan csökkentheti a irányuló első kérelem válaszideje üresjárati idő után?

Alapértelmezés szerint a webalkalmazások a memóriából, ha a beállított időn üresjáratban. Ezzel a módszerrel a rendszer állapotokban erőforrásokat. A hátránya az, hogy a válasz az első kérésre, miután a webes alkalmazás memóriából hosszabb, hogy a webalkalmazás betöltése, és indítsa el a kérelmek teljesítése. A Basic és Standard service-csomagokról bekapcsolása a **mindig a** mindig be van töltve az alkalmazás mindig a beállítást. Ezzel a megoldással hosszabb a betöltési idők, miután az alkalmazás tétlen. Módosíthatja a **mindig a** beállítást:

1. Az Azure-portálon keresse meg a webes alkalmazást.
2. Válassza ki **Alkalmazásbeállítások**.
3. A **mindig a**, jelölje be **a**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hogyan sikertelen kérelmek nyomkövetése bekapcsolása?

Sikertelen kérelmek nyomkövetése bekapcsolása:

1. Az Azure-portálon keresse meg a webes alkalmazást.
3. Válassza ki **összes beállítás** > **diagnosztikai naplók**.
4. A **sikertelen kérelmek nyomkövetése**, jelölje be **a**.
5. Kattintson a **Mentés** gombra.
6. A webalkalmazás panelen, jelölje ki **eszközök**.
7. Válassza ki **Visual Studio online-hoz**.
8. Ha a beállítás nem **a**, jelölje be **a**.
9. Válassza ki **Ugrás**.
10. Válassza ki **Web.config**.
11. System.webServer adja hozzá ezt a konfigurációt (rögzítéséhez egy adott URL-cím):

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
12. Teljesítményével kapcsolatos problémák elhárításához vegye fel ezt a konfigurációt (Ha a rögzítés kérelem több mint 30 másodpercig tart):
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
13. A sikertelen kérelmek nyomkövetési letöltését a [portal](https://portal.azure.com), nyissa meg a webhelyet.
15. Válassza ki **eszközök** > **Kudu** > **Ugrás**.
18. A menüben válasszon ki **Debug konzol** > **CMD**.
19. Válassza ki a **naplófájlok** mappára, és válassza a mappa kezdetű névvel **W3SVC**.
20. Az XML-fájlt, jelölje ki a ceruza ikonra.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>"A munkavégző folyamat" Százalékos memóriakorlátot "miatt újrahasznosítást kért." a következő üzenet jelenik meg Hogyan kezelje a probléma?

A rendelkezésre álló memória maximális mérete 32 bites folyamat (akár 64 bites operációs rendszeren) pedig 2 GB. Alapértelmezés szerint a munkavégző folyamat a 32-bit van beállítva az App Service (az örökölt webes alkalmazásainak kompatibilitás-ellenőrzése).

Fontolja meg az áttérést a 64 bites folyamatokat, így a webes munkavégző szerepkörben további memória előnyeit élvezheti. Ez elindítja a újra kell indítani a webes alkalmazást, úgy ütemezze ennek megfelelően.

Ne feledje, hogy egy 64 bites-környezet megköveteli-e egy alapszintű vagy Standard service-csomagot. Ingyenes, és megosztott tervek mindig 32-bit-es környezetben fusson.

További információkért lásd: [webes alkalmazások konfigurálása az App Service](web-sites-configure.md).

## <a name="why-does-my-request-time-out-after-240-seconds"></a>Miért nem a kérelem időtúllépés 240 másodperc után?

Az Azure Load Balancer négy perces alapértelmezett időtúllépést beállítás van. Ez általában egy webes kérelem időkorlátja megfelelő választ. Ha a webalkalmazás a háttérben történő feldolgozás van szüksége, Azure webjobs-feladatok használatát javasoljuk. Az Azure web app meghívhatja a webjobs-feladatok, és értesíti, ha a háttérben történő feldolgozása befejeződik. A webjobs-feladatok, beleértve a várólisták és eseményindítók segítségével többféle módszer közül választhat.

Webjobs-feladatok a háttérben történő feldolgozás szolgál. Mértékű háttérben történő feldolgozási webjobs-feladat a kívánt módon teheti meg. További információ a webjobs-feladatok: [háttérfeladatok futtatása a webjobs-feladatok](web-sites-create-web-jobs.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>Az ASP.NET Core alkalmazásokhoz, amelyek az App Service-ben egyes esetekben nem válaszol. Hogyan oldja meg a probléma?

Egy ismert hiba, mely egy korábbi [vércse verzió](https://github.com/aspnet/KestrelHttpServer/issues/1182) időnként nem válaszol az App Service-ben üzemeltetett ASP.NET Core 1.0 alkalmazás okozhat. Akkor is megjelenhet ez az üzenet: "a megadott CGI-alkalmazás hibát észlelt, és a kiszolgáló megszakította a folyamatot."

Ez a probléma fennáll a vércse 1.0.2-es verzióját. Ebben a verzióban megtalálható az ASP.NET Core 1.0.3 frissítésében. A probléma megoldásához, győződjön meg arról, hogy az alkalmazás függőségeit vércse 1.0.2-es használatához frissítenie. Azt is megteheti, hogy is két kerülő megoldások valamelyikével blogbejegyzést ismertetett [lassú teljesítmény az ASP.NET Core 1.0 állít ki az App Service web apps](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>A webalkalmazás fájlstruktúrájával nem található a rendszernapló fájljaiban. Hogyan tudhatom őket?

Ha az App Service a helyi gyorsítótár szolgáltatását használja, az App Service-példány a naplófájlok és az adatok mappák mappaszerkezetét érinti. Helyi gyorsítótár használata esetén almappák a naplófájlok tárolási és adatmappáinak jönnek létre. Az almappák használja az elnevezési minta "egyedi azonosítója" + időbélyegző. Minden almappa felel meg a Virtuálisgép-példány a webalkalmazás fut, vagy futott.

Annak meghatározásához, hogy használ-e helyi gyorsítótár, ellenőrizze, hogy az App Service **Alkalmazásbeállítások** fülre. Ha a helyi gyorsítótár használatban van, az alkalmazás beállítása `WEBSITE_LOCAL_CACHE_OPTION` értéke `Always`.

Ha nem használja a helyi gyorsítótárban, és ezzel a problémával találkozik, a támogatási kérelem elküldéséhez.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Hibaüzenet jelenik meg: "kísérlet történt olyan módon, a hozzáférési engedélyeket tiltott hozzáférés." Hogyan lehet elhárítani ezt?

Ez a hiba rendszerint azért fordul elő, ha elfogytak, a kimenő TCP-kapcsolatok a Virtuálisgép-példányon. Az App Service-ben vannak korlátozva beállíthatja, hogy minden egyes Virtuálisgép-példány kimenő kapcsolatok maximális számát. További információkért lásd: [Cross-VM numerikus korlátok](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Ezt a hibát is okozhat, ha megpróbál hozzáférni egy helyi cím az alkalmazásból. További információkért lásd: [helyi cím kérelmek](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Kimenő kapcsolatok a web app alkalmazásban kapcsolatos további információkért lásd a következő blogbejegyzésben található kapcsolatos [kimenő kapcsolatok Azure websitesra](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Visual Studio használata távoli my App Service webalkalmazás hibakeresése?

A részletes forgatókönyv bemutatja, hogy a webalkalmazás hibakeresése a Visual Studio használatával, lásd: [távoli hibakereséshez az App Service webalkalmazásba](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/).
