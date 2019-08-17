---
title: Üzembe helyezési technológiák a Azure Functionsban | Microsoft Docs
description: Megtudhatja, milyen módokon telepíthet programkódot a Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 88b6fbbd68f1f98e50ec0f04336a022dc1580a73
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562913"
---
# <a name="deployment-technologies-in-azure-functions"></a>Üzembe helyezési technológiák Azure Functions

Néhány különböző technológiával üzembe helyezheti Azure Functions-projekt kódját az Azure-ban. Ez a cikk részletes listát nyújt ezekről a technológiákról, leírja, hogy mely technológiák érhetők el, amelyek a függvények különböző típusairól szólnak, és ismerteti, hogy mi történik az egyes módszerek használatakor, és javaslatokat tesz a legjobb módszer használatára különféle helyzetekben . A Azure Functions üzembe helyezését támogató különféle eszközök a környezetük alapján a megfelelő technológiára vannak hangolva.

## <a name="deployment-technology-availability"></a>Üzembe helyezési technológia rendelkezésre állása

Azure Functions támogatja a többplatformos helyi fejlesztést és üzemeltetést Windows és Linux rendszeren. Jelenleg három üzemeltetési csomag érhető el:

+ [Fogyasztás](functions-scale.md#consumption-plan)
+ [Prémium](functions-scale.md#premium-plan)
+ [Dedikált (App Service)](functions-scale.md#app-service-plan)

Minden csomag eltérő viselkedéssel rendelkezik. Nem minden központi telepítési technológia érhető el Azure Functions minden egyes ízét. A következő diagramon látható, hogy mely telepítési technológiák támogatottak az operációs rendszer és a üzemeltetési csomag minden kombinációja esetén:

| Üzembe helyezési technológia | Windows-felhasználás | Windows Premium (előzetes verzió) | Dedikált Windows  | Linux-felhasználás | Linux dedikált |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| Külső csomag URL-címe<sup>1</sup> |✔|✔|✔|✔|✔|
| Zip-telepítés |✔|✔|✔| |✔|
| Docker tárolók | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| Verziókövetés |✔|✔|✔| |✔|
| Helyi git<sup>1</sup> |✔|✔|✔| |✔|
| Cloud Sync<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Portál szerkesztése |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> a [manuális trigger](#trigger-syncing)-szinkronizálást igénylő üzembe helyezési technológia.  
<sup>2</sup> a portálon történő szerkesztés csak a http-és időzítő-eseményindítók esetében engedélyezett a Linux-függvények prémium és dedikált csomagok használatával.

## <a name="key-concepts"></a>Fő fogalmak

Néhány kulcsfontosságú fogalom fontos, hogy megértsük, hogyan működnek a központi telepítések Azure Functions.

### <a name="trigger-syncing"></a>Szinkronizálás elindítása

Az eseményindítók módosításakor a functions infrastruktúrájának tisztában kell lennie a változásokkal. A szinkronizálás számos üzembe helyezési technológia esetében automatikusan megtörténik. Bizonyos esetekben azonban manuálisan kell szinkronizálnia az eseményindítókat. Ha a frissítéseket egy külső csomag URL-címére, a helyi git-ra, a felhő-szinkronizálásra vagy az FTP-re hivatkozva telepíti, manuálisan kell szinkronizálnia az eseményindítókat. Az eseményindítók szinkronizálása háromféleképpen végezhető el:

* Indítsa újra a Function alkalmazást a Azure Portal
* HTTP post- `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` kérés küldése a főkulcs [](functions-bindings-http-webhook.md#authorization-keys)használatára.
* HTTP POST-kérelem küldése a `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`következőnek:. Cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és a függvény alkalmazásának nevére.

## <a name="deployment-technology-details"></a>Üzembe helyezési technológia részletei 

A következő üzembe helyezési módszerek érhetők el Azure Functionsban.

### <a name="external-package-url"></a>Külső csomag URL-címe

Külső csomag URL-címével hivatkozhat a Function alkalmazást tartalmazó távoli csomag (. zip) fájlra. A fájl a megadott URL-címről töltődik le, és az alkalmazás [csomag](run-functions-from-deployment-package.md) módban fut.

>__Használat:__ Adja `WEBSITE_RUN_FROM_PACKAGE` hozzá az alkalmazás beállításait. A beállítás értékének URL-címnek kell lennie (a futtatni kívánt adott csomagfájl helye). A beállításokat a portálon [](functions-how-to-use-azure-function-app-settings.md#settings) vagy [Az Azure CLI használatával](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)is hozzáadhatja. 
>
>Ha az Azure Blob Storage-t használja, használjon egy [közös hozzáférési aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót, amely lehetővé teszi a funkciók elérését a csomaghoz. Az alkalmazás újraindításakor a rendszer lekéri a tartalom egy példányát. A hivatkozásnak érvényesnek kell lennie az alkalmazás élettartamára.

>__Mikor érdemes használni:__ A külső csomag URL-címe az egyetlen támogatott üzembe helyezési módszer a Linux rendszeren futó Azure Functions a használati tervben. Amikor frissíti a alkalmazáscsomag által hivatkozott csomagfájl adatait, manuálisan kell szinkronizálnia az [eseményindítókat](#trigger-syncing) , hogy tájékoztassa az Azure-t arról, hogy az alkalmazás megváltozott.

### <a name="zip-deploy"></a>Zip-telepítés

A zip-telepítés használatával leküldheti a Function alkalmazást az Azure-ba tartalmazó. zip-fájlt. Azt is megteheti, hogy az alkalmazást [csomag](run-functions-from-deployment-package.md) módból való futtatásra állítja be.

>__Használat:__ Üzembe helyezés a kedvenc ügyfél-eszköz használatával: [Vs Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)vagy az [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Ha a. zip-fájlt manuálisan szeretné telepíteni a Function alkalmazásba, kövesse az [üzembe helyezés a. zip fájlból vagy URL-címről](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)című témakör utasításait.
>
>Ha a zip-telepítés használatával végzi a telepítést, beállíthatja, hogy az alkalmazás [csomag](run-functions-from-deployment-package.md) módban fusson. Ha csomag módból szeretné beállítani a futtatást, `WEBSITE_RUN_FROM_PACKAGE` állítsa a `1`értékre az Application Setting értéket. A zip-telepítést javasoljuk. Gyorsabb betöltési időt eredményez az alkalmazások számára, és ez az alapértelmezett a VS Code, a Visual Studio és az Azure CLI számára.

>__Mikor érdemes használni:__ A zip-telepítés a Windowson és a Linuxon futó függvények ajánlott központi telepítési technológiája a prémium vagy a dedikált csomagban.

### <a name="docker-container"></a>Docker tárolók

Telepítheti a Function alkalmazást tartalmazó Linux-tároló lemezképét.

>__Használat:__ Hozzon létre egy Linux-függvény alkalmazást a prémium vagy a dedikált csomagban, és adja meg, melyik tárolót szeretné futtatni. Ezt kétféleképpen teheti meg:
>
>* Hozzon létre egy Linux-függvény alkalmazást egy Azure App Service csomaggal a Azure Portal. A **közzétételhez**válassza a Docker- **rendszerkép**lehetőséget, majd konfigurálja a tárolót. Adja meg azt a helyet, ahol a rendszerkép található.
>* Hozzon létre egy Linux-függvény alkalmazást egy App Service csomagon az Azure CLI használatával. További információ: [függvény létrehozása Linux rendszeren egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Ha egy meglévő alkalmazást szeretne üzembe helyezni egy egyéni tároló használatával, a [Azure functions Core Toolsban](functions-run-local.md)használja az [`func deploy`](functions-run-local.md#publish) parancsot.

>__Mikor érdemes használni:__ Akkor használja a Docker-tároló lehetőséget, ha nagyobb mértékű vezérlésre van szüksége a Function alkalmazást futtató linuxos környezetben. Ez a központi telepítési mechanizmus csak a Linux rendszeren futó függvények esetében érhető el egy App Service csomagban.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

A web Deploy csomagokat és üzembe helyezi a Windows-alkalmazásait bármely IIS-kiszolgálón, beleértve az Azure-ban Windowson futó Function-alkalmazásokat is.

>__Használat:__ [A Visual Studio Tools for Azure functions](functions-create-your-first-function-visual-studio.md)használata. Törölje a **Futtatás a csomagfájl alapján (ajánlott)** jelölőnégyzet jelölését.
>
>Letöltheti továbbá a [web Deploy 3,6](https://www.iis.net/downloads/microsoft/web-deploy) -et `MSDeploy.exe` , és közvetlenül is meghívhatja.

>__Mikor érdemes használni:__ A web Deploy támogatott, és nem tartalmaz problémát, de az előnyben részesített mechanizmus a [zip üzembe helyezése a csomaggal engedélyezett futtatással](#zip-deploy). További információt a [Visual Studio fejlesztői útmutatójában](functions-develop-vs.md#publish-to-azure)talál.

### <a name="source-control"></a>Verziókövetés

A verziókövetés használatával a Function alkalmazást egy git-tárházhoz is összekapcsolhatjuk. A tárházban lévő kód frissítése elindítja az üzembe helyezést. További információ: [kudu wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Használat:__ A portál functions területén használja a Deployment Center alkalmazást a verziókövetés közzétételének beállításához. További információ: [Azure functions folyamatos üzembe helyezése](functions-continuous-deployment.md).

>__Mikor érdemes használni:__ A verziókövetés használata az ajánlott eljárás olyan csapatok esetében, amelyek együttműködnek a funkció alkalmazásaiban. A verziókövetés jó üzembe helyezési lehetőség, amely kifinomultabb üzembe helyezési folyamatokat tesz lehetővé.

### <a name="local-git"></a>Helyi Git

A helyi gépen a git használatával leküldheti a kódot a helyi gépről Azure Functions.

>__Használat:__ Kövesse a [helyi git-telepítés](../app-service/deploy-local-git.md)utasításait Azure app Service.

>__Mikor érdemes használni:__ Általában azt javasoljuk, hogy használjon másik telepítési módszert. Amikor a helyi git-ből tesz közzé, [manuálisan](#trigger-syncing)kell szinkronizálnia az eseményindítókat.

### <a name="cloud-sync"></a>Felhőbeli szinkronizálás

A Cloud Sync használatával szinkronizálhatja a Dropbox és a OneDrive tartalmát a Azure Functionsba.

>__Használat:__ Kövesse a [tartalom szinkronizálása egy felhőalapú mappából](../app-service/deploy-content-sync.md)című témakör utasításait.

>__Mikor érdemes használni:__ Általánosságban elmondható, hogy más üzembe helyezési módszereket is ajánlunk. A Cloud Sync használatával történő közzétételkor [manuálisan](#trigger-syncing)kell szinkronizálnia az eseményindítókat.

### <a name="ftp"></a>FTP

Az FTP használatával közvetlenül átviheti a fájlokat a Azure Functionsba.

>__Használat:__ Kövesse a [tartalom üzembe helyezése FTP/s használatával](../app-service/deploy-ftp.md)című témakör utasításait.

>__Mikor érdemes használni:__ Általánosságban elmondható, hogy más üzembe helyezési módszereket is ajánlunk. Ha FTP használatával tesz közzé, [manuálisan](#trigger-syncing)kell szinkronizálnia az eseményindítókat.

### <a name="portal-editing"></a>Portál szerkesztése

A portálon alapuló szerkesztőben közvetlenül szerkesztheti a Function alkalmazásban található fájlokat (lényegében a módosítások mentésekor minden alkalommal üzembe helyezheti őket).

>__Használat:__ Ahhoz, hogy szerkeszteni tudja a függvényeket a Azure Portalban, létre kell hoznia [a függvényeket a portálon](functions-create-first-azure-function.md). Az igazság egyetlen forrásainak megtartása érdekében bármely más üzembe helyezési módszer használatával a függvény csak olvasható, és nem teszi lehetővé a portál folyamatos szerkesztését. Ha vissza szeretne térni olyan állapotba, amelyben szerkesztheti a fájljait a Azure Portalban, manuálisan is visszakapcsolhatja a szerkesztési `Read/Write` módot, és eltávolíthatja a telepítéshez kapcsolódó alkalmazás `WEBSITE_RUN_FROM_PACKAGE`-beállításokat (például). 

>__Mikor érdemes használni:__ A portál jó módszer a Azure Functions megkezdésére. Az intenzívebb fejlesztési munka érdekében javasoljuk, hogy a következő ügyféleszközök egyikét használja:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (parancssor)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

A következő táblázat a portál szerkesztését támogató operációs rendszereket és nyelveket tartalmazza:

| | Windows-felhasználás | Windows Premium (előzetes verzió) | Dedikált Windows | Linux-felhasználás | Linux Premium (előzetes verzió)| Linux dedikált |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|:---------------:|
| C# | | | | | |
| C#Parancsfájl |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (előzetes verzió) | | | | | | |
| PowerShell (előzetes verzió) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup>A portál szerkesztése csak a HTTP-és időzítő-eseményindítók esetében engedélyezett a Linux-és prémium szintű és dedikált csomagok használatával.

## <a name="deployment-slots"></a>Üzembehelyezési pontok

Amikor üzembe helyezi a Function alkalmazást az Azure-ban, egy különálló üzembe helyezési pontra helyezheti üzembe, ahelyett, hogy közvetlenül az éles környezetbe telepítené. További információ az üzembe helyezési pontokról: [Azure app Service tárolóhelyek](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Üzembe helyezési pontok támogatási szintjei

Az üzembe helyezési pontok két szinten támogatottak:

* **Általánosan elérhető (GA)** : Teljes mértékben támogatott és jóváhagyott éles használatra.
* **Előzetes**verzió: Még nem támogatott, de a jövőben várhatóan eléri a GA-állapotot.

| Operációs rendszer/üzemeltetési csomag | Támogatási szint |
| --------------- | ------ |
| Windows-felhasználás | Előzetes verzió |
| Windows Premium (előzetes verzió) | Előzetes verzió |
| Dedikált Windows | Általános elérhetőség |
| Linux-felhasználás | Nem támogatott |
| Linux Premium (előzetes verzió) | Előzetes verzió |
| Linux dedikált | Általános elérhetőség |

## <a name="next-steps"></a>További lépések

Olvassa el ezeket a cikkeket a Function apps üzembe helyezésével kapcsolatos további információkért: 

+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)
+ [Folyamatos kézbesítés az Azure DevOps használatával](functions-how-to-azure-devops.md)
+ [A Azure Functions zip-telepítései](deployment-zip-push.md)
+ [Azure Functions futtatása csomagfájl](run-functions-from-deployment-package.md)
+ [A Function alkalmazás erőforrás-telepítésének automatizálása Azure Functions](functions-infrastructure-as-code.md)
