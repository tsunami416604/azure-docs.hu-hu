---
title: Az alkalmazások teljesítményével kapcsolatos gyakori kérdések
description: Válaszok at kaphat az Azure App Service rendelkezésre állásával, teljesítményével és alkalmazásával kapcsolatos gyakori kérdésekre.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 433f5885c7f057226e78c4ae57e03d7619004d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259863"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Alkalmazásteljesítmény – gyakori kérdések az Azure-beli webalkalmazásokhoz

> [!NOTE]
> Előfordulhat, hogy az alábbi irányelvek némelyike csak Windows vagy Linux App Services rendszeren működik. A Linux App Services például alapértelmezés szerint 64 bites módban fut.
>

Ez a cikk választ ad az [Azure App Service Web Apps szolgáltatásával](https://azure.microsoft.com/services/app-service/web/)kapcsolatos alkalmazásteljesítménnyel kapcsolatos gyakori kérdésekre .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Miért lassú az alkalmazásom?

Több tényező is hozzájárulhat az alkalmazás teljesítményének lassúsításához. A részletes hibaelhárítási lépéseket a [Webappok lassú teljesítményének elhárítása című témakörben talál.](troubleshoot-performance-degradation.md)

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Hogyan háríthatók el a magas cpu-fogyasztású forgatókönyv?

Egyes magas CPU-felhasználású forgatókönyvek esetén az alkalmazás valóban több számítási erőforrást igényelhet.Ebben az esetben fontolja meg egy magasabb szolgáltatási szintre való skálázást, hogy az alkalmazás megkapja az összes szükséges erőforrást. Máskor a magas CPU-fogyasztást rossz hurok vagy kódolási gyakorlat okozhatja. A megnövekedett CPU-felhasználás talódó két részből álló folyamat. Először hozzon létre egy folyamatmemóriaképet, majd elemezze a folyamatmemóriaképet. További információ: [Dump file rögzítése és elemzése a webes alkalmazások magas processzorfogyasztása miatt.](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/)

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Hogyan háríthatók el a magas memóriafogyasztási esetek?

Egyes nagy memóriafelhasználású esetekben az alkalmazás valóban több számítási erőforrást igényelhet.Ebben az esetben fontolja meg egy magasabb szolgáltatási szintre való skálázást, hogy az alkalmazás megkapja az összes szükséges erőforrást. Máskor a kódban lévő hiba memóriavesztést okozhat. A kódolási gyakorlat is növelheti a memóriafelhasználást.A magas memóriafelhasználást kiváltó adatok két részből álló folyamat. Először hozzon létre egy folyamatmemóriaképet, majd elemezze a folyamatmemóriaképet. Az Azure Site Extension Gallery összeomlás-diagnosztizáló ja- hatékonyan hajthatja végre ezeket a lépéseket. További információt a [Képklazomfájl rögzítése és elemzése című](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/)témakörben talál a webalkalmazások időszakosan nagy memóriája miatt.

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Hogyan automatizálhatom az App Service-webalkalmazásokat a PowerShell használatával?

A PowerShell-parancsmagokkal kezelheti és karbantarthatja az App Service-webalkalmazásokat. Blogbejegyzésünkben [automatizálhatja az Azure App Service-ben tárolt webalkalmazásokat a PowerShell használatával,](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)és bemutatjuk, hogyan használhatja az Azure Resource Manager-alapú PowerShell-parancsmagokat a gyakori feladatok automatizálására. A blogbejegyzésben mintakód is szerepel a különböző webalkalmazások kezelési feladataihoz. Az App Service összes webalkalmazás-parancsmagjának leírását és szintaxisát az [Az.Websites ( Az.Websites ) témakörben talál.](/powershell/module/az.websites)

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Hogyan tekinthetem meg a webalkalmazás eseménynaplóit?

A webalkalmazás eseménynaplóinak megtekintése:

1. Jelentkezzen be a [Kudu webhelyére.](https://*yourwebsitename*.scm.azurewebsites.net)
2. A menüben válassza a **Debug Console** > **CMD parancsot**.
3. Jelölje ki a **LogFiles** mappát.
4. Az eseménynaplók megtekintéséhez kattintson az **eventlog.xml**fájl melletti ceruza ikonra.
5. A naplók letöltéséhez futtassa a `Save-AzureWebSiteLog -Name webappname`PowerShell-parancsmabot.

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Hogyan rögzíthetem a webalkalmazás felhasználói módú memóriaképét?

A webalkalmazás felhasználói módú memóriaképének rögzítése:

1. Jelentkezzen be a [Kudu webhelyére.](https://*yourwebsitename*.scm.azurewebsites.net)
2. Válassza a **Folyamatkezelő menüt.**
3. Kattintson a jobb gombbal a **w3wp.exe** vagy a WebJob folyamatra.
4. Válassza a Memóriakép teljes **memóriaképének** > **letöltése**lehetőséget.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Hogyan tekinthetem meg a webalkalmazás folyamatszintű adatait?

A webalkalmazás folyamatszintű adatainak megtekintésére két lehetőség közül választhat:

*   Az Azure Portalon:
    1. Nyissa meg a **webalkalmazás Folyamatkezelőját.**
    2. A részletek megtekintéséhez válassza a **w3wp.exe folyamatot.**
*   A Kudu konzolon:
    1. Jelentkezzen be a [Kudu webhelyére.](https://*yourwebsitename*.scm.azurewebsites.net)
    2. Válassza a **Folyamatkezelő menüt.**
    3. A **w3wp.exe** folyamathoz válassza a **Tulajdonságok lehetőséget.**

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Amikor az alkalmazásba böngészem, a "403-as hiba – Ez a webalkalmazás leállt" jelenik meg. Hogyan oldhatom meg ezt?

Ezt a hibát három feltétel okozhatja:

* A webalkalmazás elérte a számlázási korlátot, és a webhely le van tiltva.
* A webalkalmazás leállt a portálon.
* A webalkalmazás elérte az erőforráskvóta-korlátot, amely egy ingyenes vagy megosztott méretezési szolgáltatáscsomagra vonatkozhat.

A hiba okának megtekintéséhez és a probléma megoldásához kövesse a Web Apps következő [lépéseit: "403-as hiba – Ez a webalkalmazás leáll"](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Hol tudhatok meg többet a különböző App Service-csomagok kvótáiról és korlátairól?

A kvótákról és a korlátozásokról az [App Service-korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című témakörben talál további információt. 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Hogyan csökkenthetem az első kérelem válaszidejét az alapjárati idő után?

Alapértelmezés szerint a webalkalmazások törlődnek, ha egy meghatározott ideig tétlenek. Így a rendszer erőforrásokat tud megóbisdni. A hátránya az, hogy a válasz az első kérés után a webalkalmazás eltávolítása hosszabb, hogy a webalkalmazás betöltése és a válaszok kiszolgálásának megkezdése. Az Alapszintű és a Standard szolgáltatási csomagokban bekapcsolhatja az **Mindig bekapcsolva** beállítást, hogy az alkalmazás mindig betöltődjön. Ez kiküszöböli a hosszabb betöltési időt az alkalmazás tétlenségután. A **Mindig bekapcsolva** beállítás módosítása:

1. Az Azure Portalon nyissa meg a webalkalmazást.
2. **Konfiguráció** kiválasztása
3. Válassza **az Általános beállítások lehetőséget**.
4. A **Mindig bekapcsolva**lehetőséget válassza **a Be**lehetőséget.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Hogyan kapcsolhatom be a sikertelen kérelmek nyomkövetését?

A sikertelen kérelmek nyomkövetésének bekapcsolása:

1. Az Azure Portalon nyissa meg a webalkalmazást.
3. Válassza az **Összes beállításdiagnosztikai** > **napló lehetőséget.**
4. A **sikertelen kérelmek nyomkövetéséhez**válassza **a Be**lehetőséget.
5. Kattintson a **Mentés** gombra.
6. A webalkalmazás paneljén válassza az **Eszközök**lehetőséget.
7. Válassza a **Visual Studio Online lehetőséget.**
8. Ha a beállítás nincs **bekapcsolva,** válassza **a Be**lehetőséget.
9. Válassza az **Ugrás**lehetőséget.
10. Válassza **a Web.config lehetőséget.**
11. A system.webServer fájlban adja hozzá ezt a konfigurációt (egy adott URL rögzítéséhez):

    ```xml
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
12. A lassú teljesítménnyel kapcsolatos problémák elhárításához adja hozzá ezt a konfigurációt (ha a rögzítési kérelem 30 másodpercnél tovább tart):
    ```xml
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
13. A sikertelen kérelmek nyomkövetéseinek letöltéséhez a [portálon](https://portal.azure.com)nyissa meg a webhelyet.
15. Válassza **az Eszközök** > **Kudu** > **Go**lehetőséget.
18. A menüben válassza a **Debug Console** > **CMD parancsot**.
19. Jelölje ki a **LogFiles** mappát, majd jelölje ki a **W3SVC**kezdetű mappát.
20. Az XML-fájl megtekintéséhez kattintson a ceruza ikonra.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>A "Munkavégző folyamat újrahasznosítást kért a "Memória százaléka" korlát miatt kért újrahasznosítási üzenet jelenik meg. Hogyan oldhatom meg ezt a problémát?

Egy 32 bites folyamathoz (akár 64 bites operációs rendszeren is) rendelkezésre álló maximális memóriamennyiség 2 GB. Alapértelmezés szerint a munkavégző folyamat 32 bitesre van állítva az App Service-ben (az örökölt webalkalmazásokkal való kompatibilitás érdekében).

Fontolja meg a 64 bites folyamatokra való váltást, hogy kihasználhassa a Web Worker szerepkörben rendelkezésre álló további memóriát. Ez elindítja a webalkalmazás újraindítását, ezért ennek megfelelően ütemezze.

Azt is vegye figyelembe, hogy a 64 bites környezetben alapszintű vagy szabványos szolgáltatási csomagra van szükség. Az ingyenes és a megosztott csomagok mindig 32 bites környezetben futnak.

További információt a [Webapps konfigurálása az App Service szolgáltatásban című témakörben talál.](configure-common.md)

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Miért időkérés 230 másodperc után?

Az Azure Load Balancer alapértelmezett tétlen időtúlterhelési beállítása négy perc. Ez általában egy webes kérelem reklamációjának ésszerű válaszhatára. Ha a webalkalmazás háttérfeldolgozást igényel, javasoljuk az Azure WebJobs használatát. Az Azure webapp hívhatwebjobs, és értesítést kap, ha a háttér feldolgozás befejeződött. A WebJobs több módszer közül is választhat, beleértve a várólistákat és az eseményindítókat.

A WebJobs háttérfeldolgozásra szolgál. A WebJobban annyi háttérfeldolgozást tehet, amennyit csak szeretne. A WebJobs alkalmazásról további információt a [Háttérfeladatok futtatása webfeladatokkal](webjobs-create.md)című témakörben talál.

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Az App Service-ben üzemeltetett core alkalmazások néha nem válaszolnak. Hogyan oldhatom meg ezt a problémát?

Egy korábbi [Kestrel-verzióval](https://github.com/aspnet/KestrelHttpServer/issues/1182) kapcsolatos ismert probléma miatt előfordulhat, hogy az App Service-ben üzemeltetett ASP.NET Core 1.0-s alkalmazás időnként leáll. A következő üzenet jelenhet meg: "A megadott CGI-alkalmazás hibát észlelt, és a kiszolgáló leállította a folyamatot."

A probléma a Kestrel 1.0.2-es verziójában történt. Ez a verzió a ASP.NET Core 1.0.3 frissítésrésze. A probléma megoldásához frissítse az alkalmazásfüggőségeket a Kestrel 1.0.2 használatára. Azt is megteheti, hogy a blogbejegyzésben ismertetett két megoldás egyikét [használja, ASP.NET Core 1.0 lassú perf problémáit az App Service-webalkalmazásokban.](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites)


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nem találom a naplófájlokat a webalkalmazás fájlstruktúrájában. Hogyan találhatom meg őket?

Ha az App Service Helyi gyorsítótár szolgáltatását használja, az érinti az App Service-példány LogFiles és Data mappáinak mappaszerkezetét. A helyi gyorsítótár használata esetén almappák jönnek létre a LogFiles és az Data tárolómappában. Az almappák az "egyedi azonosító" elnevezési mintát + időbélyegzőt használják. Minden almappa egy virtuálisgép-példánynak felel meg, amelyben a webalkalmazás fut vagy fut.

Annak megállapításához, hogy használja-e a helyi gyorsítótárat, ellenőrizze az App **Service-alkalmazás beállításai** lapot. Ha helyi gyorsítótárat használ, `WEBSITE_LOCAL_CACHE_OPTION` az alkalmazás `Always`beállítása a .

Ha nem használja a helyi gyorsítótárat, és a problémát tapasztalja, nyújtson be támogatási kérelmet.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>A következő üzenet jelenik meg: "Kísérlet történt a szoftvercsatorna elérésére a hozzáférési engedélyei által tiltott módon." Hogyan oldhatom meg ezt?

Ez a hiba általában akkor fordul elő, ha a virtuálisgép-példány kimenő TCP-kapcsolatai kimerültek. Az App Service-ben a korlátozások az egyes virtuálisgép-példányokhoz létrehozható kimenő kapcsolatok maximális száma. További információ: [Cross-VM numerikus korlátok](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

Ez a hiba akkor is előfordulhat, ha megpróbál hozzáférni egy helyi címhez az alkalmazásból. További információt a [Helyi címkérések](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests)című témakörben talál.

A webalkalmazásban lévő kimenő kapcsolatokról az [Azure-webhelyekkimenő kapcsolatairól](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)szóló blogbejegyzésben talál további információt.

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Hogyan használhatom a Visual Studiót az App Service webalkalmazás távoli hibakereséséhez?

A webalkalmazás Visual Studio használatával történő hibakeresését bemutató részletes [útmutatót az App Service webalkalmazás távoli hibakeresése](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/)című témakörben talál.
