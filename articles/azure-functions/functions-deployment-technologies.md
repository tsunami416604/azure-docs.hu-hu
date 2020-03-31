---
title: Telepítési technológiák az Azure Functionsben
description: Ismerje meg a különböző módon telepítheti a kódot az Azure Functions.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277127"
---
# <a name="deployment-technologies-in-azure-functions"></a>Telepítési technológiák az Azure Functionsben

Néhány különböző technológiák segítségével üzembe helyezheti az Azure Functions projektkódját az Azure-ba. Ez a cikk részletes listát tartalmaz ezekről a technológiákról, leírja, hogy mely technológiák állnak rendelkezésre, amelyekhez a Funkciók ízei rendelkezésre állnak, elmagyarázza, hogy mi történik az egyes módszerek használatakor, és javaslatokat tartalmaz a különböző forgatókönyvekben használható legjobb módszerre . Az Azure Functionsben való üzembe helyezést támogató különböző eszközök a megfelelő technológiára vannak hangolva a környezetük alapján. Általánosságban elmondható, hogy a zip-telepítés az Azure Functions ajánlott üzembe helyezési technológiája.

## <a name="deployment-technology-availability"></a>A telepítési technológia elérhetősége

Az Azure Functions támogatja a platformfüggetlen helyi fejlesztést és üzemeltetést Windows és Linux rendszeren. Jelenleg három tárhely áll rendelkezésre:

+ [Használat](functions-scale.md#consumption-plan)
+ [Prémium](functions-scale.md#premium-plan)
+ [Dedikált (App Service)](functions-scale.md#app-service-plan)

Minden terv különböző viselkedésű. Nem minden üzembe helyezési technológia érhető el az Azure Functions egyes ízéhez. Az alábbi ábra bemutatja, hogy mely telepítési technológiák támogatottak az operációs rendszer és az üzemeltetési terv egyes kombinációihoz:

| Telepítési technológia | Windows-felhasználás | Windows Prémium rendszer | Dedikált Windows  | Linux-fogyasztás | Linux Prémium | Linux dedikált |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Külső csomag URL-címe<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip telepítése |✔|✔|✔|✔|✔|✔|
| Docker-tároló | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Verziókövetés |✔|✔|✔| |✔|✔|
| Helyi Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Felhőszinkronizálás<sup>1</sup> |✔|✔|✔| |✔|✔|
| <sup>FTP 1</sup> |✔|✔|✔| |✔|✔|
| Portál szerkesztése |✔|✔|✔| |<sup>✔ 2.</sup>|<sup>✔ 2.</sup>|

<sup>1</sup> Üzembe helyezési technológia, amely [kézi eseményindító-szinkronizálást](#trigger-syncing)igényel.  
<sup>2</sup> A portál szerkesztése csak a LINUX-alapú függvények HTTP és Timer eseményindítóihoz engedélyezett prémium és dedikált csomagok használatával.

## <a name="key-concepts"></a>Fő fogalmak

Néhány kulcsfontosságú fogalmak kritikus fontosságúak az Azure Functions ben az üzembe helyezések működésének megértéséhez.

### <a name="trigger-syncing"></a>Eseményindító-szinkronizálás

Ha módosítja bármelyik eseményindítók, a Funkciók infrastruktúra tisztában kell lennie a változásokat. A szinkronizálás számos telepítési technológiánál automatikusan történik. Bizonyos esetekben azonban manuálisan kell szinkronizálnia az eseményindítókat. Ha a frissítéseket egy külső csomag URL-címére, helyi Gitre, felhőszinkronizálásra vagy FTP-re hivatkozva telepíti, manuálisan kell szinkronizálnia az eseményindítókat. Az eseményindítókat a következő három féleképpen szinkronizálhatja:

* A függvényalkalmazás újraindítása az Azure Portalon
* HTTP POST-kérelem `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` küldése a [főkulcs használatára.](functions-bindings-http-webhook-trigger.md#authorization-keys)
* HTTP POST-kérelem `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`küldése a rendszernek. Cserélje le a helyőrzőket az előfizetés-azonosítójára, az erőforráscsoport nevére és a függvényalkalmazás nevére.

### <a name="remote-build"></a>Távoli összeállítás

Az Azure Functions automatikusan végrehajthatja a zip-telepítések után kapott kódra épül. Ezek a buildek kissé eltérően viselkednek attól függően, hogy az alkalmazás Windows vagy Linux rendszeren fut.These builds is be a slightly differently as whether your app is running on Windows or Linux. A távoli buildek nem hajtják végre, ha egy alkalmazás korábban úgy lett beállítva, hogy [a Futtatás csomagból](run-functions-from-deployment-package.md) módban fusson. A távoli build használatának megismeréséhez keresse meg a [zip deploy](#zip-deploy).d.

> [!NOTE]
> Ha problémái vannak a távoli buildel, annak az lehet az oka, hogy az alkalmazást a funkció elérhetővé tették (2019. augusztus 1.) előtt hozták létre. Próbáljon meg létrehozni egy `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` új függvényalkalmazást, vagy futjon a függvényalkalmazás frissítéséhez. Ez a parancs két próbálkozást is igénybe vehet a sikerhez.

#### <a name="remote-build-on-windows"></a>Távoli build a Windows rendszeren

A Windows rendszeren futó összes függvényalkalmazás rendelkezik egy kis felügyeleti alkalmazással, az SCM (vagy [Kudu)](https://github.com/projectkudu/kudu)webhelyen. Ez a hely kezeli az üzembe helyezés nagy részét, és az Azure Functions logikáját.

Amikor egy alkalmazást telepít a Windows rendszerbe, `dotnet restore` nyelvspecifikus parancsok, például (C#) vagy `npm install` (JavaScript) futnak.

#### <a name="remote-build-on-linux"></a>Távoli build Linuxon

A távoli build Linuxon való engedélyezéséhez a következő [alkalmazásbeállításokat](functions-how-to-use-azure-function-app-settings.md#settings) kell beállítani:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Alapértelmezés szerint mind az [Azure Functions Core Tools,](functions-run-local.md) mind az [Azure Functions Extension for Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) távoli buildeket hajt végre linuxos üzembe helyezéskor. Emiatt mindkét eszköz automatikusan létrehozza ezeket a beállításokat az Azure-ban. 

Amikor az alkalmazások linuxos távolságból épülnek, [a központi telepítési csomagból futnak.](run-functions-from-deployment-package.md) 

##### <a name="consumption-plan"></a>Használatalapú csomag

A felhasználási tervben futó Linux függvényalkalmazások nem rendelkeznek SCM/Kudu-val, amely korlátozza a telepítési lehetőségeket. A fogyasztási tervben futó Linux-függvényalkalmazások azonban támogatják a távoli buildeket.

##### <a name="dedicated-and-premium-plans"></a>Dedikált és prémium csomagok

A [Dedikált (App Service) csomagban](functions-scale.md#app-service-plan) linuxos függvényalkalmazások és a [prémium csomag](functions-scale.md#premium-plan) korlátozott SCM/Kudu webhelytel is rendelkezik.

## <a name="deployment-technology-details"></a>A telepítési technológia részletei

A következő telepítési módszerek érhetők el az Azure Functions.

### <a name="external-package-url"></a>Külső csomag URL-címe

A külső csomag URL-címét használhatja a függvényalkalmazást tartalmazó távoli csomag (.zip) fájlhivatkozáshoz. A fájl a megadott URL-címről töltődik le, és az alkalmazás [a Futtatás csomagból](run-functions-from-deployment-package.md) módban fut.

>__Hogyan kell használni:__ Adja `WEBSITE_RUN_FROM_PACKAGE` hozzá az alkalmazás beállításaihoz. Ennek a beállításnak egy URL-címnek kell lennie (a futtatni kívánt csomagfájl helye). A beállításokat [hozzáadhatja a portálon](functions-how-to-use-azure-function-app-settings.md#settings) vagy [az Azure CLI használatával.](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 
>
>Ha Azure Blob storage-t használ, használjon [egy közös hozzáférésű aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót, hogy a Functions hozzáférést biztosítson a csomaghoz. Az alkalmazás újraindításakor lekéri a tartalom egy példányát. A hivatkozásnak az alkalmazás élettartamára érvényesnek kell lennie.

>__Mikor érdemes használni:__ Külső csomag URL-cím az egyetlen támogatott üzembe helyezési módszer az Azure Functions linuxos a használati tervben, ha a felhasználó nem szeretné, hogy egy [távoli build](#remote-build) fordul elő. Amikor frissíti a csomagfájlt, amely egy függvényalkalmazás-hivatkozások, manuálisan kell [szinkronizálni a kiváltó eseményindítók,](#trigger-syncing) hogy az Azure-ban az alkalmazás megváltozott.

### <a name="zip-deploy"></a>Zip telepítése

A zip-telepítés használatával lelökheti a függvényalkalmazást tartalmazó .zip fájlt az Azure-ba. Szükség esetén beállíthatja, hogy az alkalmazás [a csomagból kezdje](run-functions-from-deployment-package.md)el a futást, vagy megadhatja, hogy távoli build történjen.Optionally, you can set your app to start running from package , or specify that a [remote build](#remote-build) occurs.

>__Hogyan kell használni:__ Üzembe helyezés a kedvenc ügyféleszközével: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio](functions-develop-vs.md#publish-to-azure), vagy a parancssorból az Azure Functions [Core Tools](functions-run-local.md#project-file-deployment)segítségével. Alapértelmezés szerint ezek az eszközök zip telepítést használnak, és [a csomagból futnak.](run-functions-from-deployment-package.md) A Core Tools és a Visual Studio Code bővítmény egyaránt engedélyezi a [távoli létrehozást](#remote-build) linuxos üzembe helyezéskor. Ha manuálisan szeretne .zip fájlt telepíteni a függvényalkalmazásba, kövesse a [Deploy from a .zip fájlból vagy URL-címről](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)című részben található utasításokat.

>Ha zip deploy használatával telepíti, beállíthatja, hogy az alkalmazás [csomagból fusson.](run-functions-from-deployment-package.md) A csomagból való `WEBSITE_RUN_FROM_PACKAGE` futtatáshoz állítsa `1`az alkalmazásbeállítás értékét . Javasoljuk, hogy zip telepítés. Ez gyorsabb betöltési időt biztosít az alkalmazások számára, és ez az alapértelmezett a VS Code, a Visual Studio és az Azure CLI esetében. 

>__Mikor érdemes használni:__ A Zip-üzembe helyezés az Azure Functions ajánlott üzembe helyezési technológiája.

### <a name="docker-container"></a>Docker-tároló

Üzembe helyezhet egy Linux-tárolórendszerképet, amely tartalmazza a függvényalkalmazást.

>__Hogyan kell használni:__ Hozzon létre egy Linux-függvényalkalmazást a Prémium vagy dedikált csomagban, és adja meg, hogy melyik tárolórendszerképből fusson. Ezt két módon teheti meg:
>
>* Hozzon létre egy Linux-függvényalkalmazást egy Azure App Service-csomagon az Azure Portalon. A **Közzététel**területen válassza a **Docker-lemezképet,** majd konfigurálja a tárolót. Adja meg azt a helyet, ahol a kép található.
>* Hozzon létre egy Linux-függvényalkalmazást egy App Service-csomagon az Azure CLI használatával. Ha tudni szeretné, hogyan, olvassa [el a Függvény létrehozása Linuxon egyéni lemezkép használatával](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)című témakört.
>
>Ha egy meglévő alkalmazásba egyéni tároló használatával telepíti, az [`func deploy`](functions-run-local.md#publish) Azure Functions Core [Tools](functions-run-local.md)alkalmazásban használja a parancsot.

>__Mikor érdemes használni:__ Használja a Docker-tároló beállítást, ha több vezérlésre van szüksége a Linux-környezetben, ahol a függvényalkalmazás fut. Ez a telepítési mechanizmus csak Linuxon futó függvények esetén érhető el.

### <a name="web-deploy-msdeploy"></a>Webes telepítés (MSDeploy)

Web telepítése csomagok és telepíti a Windows-alkalmazások bármely IIS-kiszolgáló, beleértve a függvény alkalmazások futó Windows az Azure-ban.

>__Hogyan kell használni:__ Használja [a Visual Studio eszközeit az Azure Functions szolgáltatáshoz.](functions-create-your-first-function-visual-studio.md) Törölje a jelet a **Futtatás csomagfájlból (ajánlott)** jelölőnégyzetből.
>
>Letöltheti a [Web Deploy 3.6-os t,](https://www.iis.net/downloads/microsoft/web-deploy) és közvetlenül is hívhat. `MSDeploy.exe`

>__Mikor érdemes használni:__ A webtelepítés támogatott, és nincsenek problémái, de az előnyben részesített mechanizmus a [Futtatás csomagból engedélyezve van.](#zip-deploy) További információ: [Visual Studio development guide](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Verziókövetés

A forrásvezérlővel csatlakoztathatja a függvényalkalmazást egy Git-tárházhoz. A kód frissítése a tárházban elindítja a központi telepítést. További információ: [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Hogyan kell használni:__ Használja a telepítési központot a portál Funkciók területén a forrásvezérlőből történő közzététel beállításához. További információ: [Folyamatos üzembe helyezés az Azure Functions számára.](functions-continuous-deployment.md)

>__Mikor érdemes használni:__ A forrásvezérlés használata a legjobb gyakorlat a funkcióalkalmazásokkal közösen működő csapatok számára. A forrásvezérlés egy jó telepítési lehetőség, amely lehetővé teszi a kifinomultabb üzembe helyezési folyamatok.

### <a name="local-git"></a>Helyi Git

A helyi Git segítségével leküldéses kódot a helyi gépről az Azure Functions segítségével Git.

>__Hogyan kell használni:__ Kövesse a [Helyi Git-telepítés](../app-service/deploy-local-git.md)az Azure App Service utasításokat.

>__Mikor érdemes használni:__ Általában azt javasoljuk, hogy egy másik telepítési módszer használatát. Amikor a helyi Git-ből tesz közzé, manuálisan kell [szinkronizálnia az eseményindítókat.](#trigger-syncing)

### <a name="cloud-sync"></a>Felhőszinkronizálás

A felhőszinkronizálás segítségével szinkronizálhatja a tartalmat a Dropboxból és a OneDrive-ból az Azure Functionsszolgáltatásba.

>__Hogyan kell használni:__ Kövesse a [Tartalom szinkronizálása felhőmappából](../app-service/deploy-content-sync.md)című dokumentum utasításait.

>__Mikor érdemes használni:__ Általában azt javasoljuk, más telepítési módszerek. Ha felhőalapú szinkronizálással tesz közzé közzétételt, manuálisan kell [szinkronizálnia az eseményindítókat.](#trigger-syncing)

### <a name="ftp"></a>FTP

Az FTP segítségével közvetlenül átvihetfájlokat az Azure Functions be.

>__Hogyan kell használni:__ Kövesse a [Tartalom ftp/s használatával történő telepítése](../app-service/deploy-ftp.md)című részben található utasításokat.

>__Mikor érdemes használni:__ Általában azt javasoljuk, más telepítési módszerek. Ftp-n keresztül történő közzétételkor manuálisan kell [szinkronizálni az eseményindítókat.](#trigger-syncing)

### <a name="portal-editing"></a>Portál szerkesztése

A portálalapú szerkesztőben közvetlenül szerkesztheti a függvényalkalmazásban található fájlokat (lényegében minden alkalommal, amikor menti a módosításokat).

>__Hogyan kell használni:__ Ahhoz, hogy szerkeszthesd a függvényeket az Azure Portalon, létre kell [hoznia a függvényeket a portálon.](functions-create-first-azure-function.md) Egyetlen igazságforrás megőrzése érdekében bármely más központi telepítési módszer rel a függvény csak olvasható, és megakadályozza a portál folyamatos szerkesztését. Ha vissza szeretne térni egy olyan állapotba, amelyben szerkesztheti a fájlokat az `Read/Write` Azure Portalon, manuálisan visszaforgathatja a szerkesztési módot, és eltávolíthatja a központi telepítéssel kapcsolatos alkalmazásbeállításokat (például). `WEBSITE_RUN_FROM_PACKAGE` 

>__Mikor érdemes használni:__ A portál egy jó módja annak, hogy az Azure Functions. Az intenzívebb fejlesztési munkához javasoljuk, hogy az alábbi ügyféleszközök egyikét használja:
>
>* [Visual Studio kód](functions-create-first-function-vs-code.md)
>* [Az Azure Functions alapvető eszközei (parancssor)](functions-run-local.md)
>* [Vizuális stúdió](functions-create-your-first-function-visual-studio.md)

Az alábbi táblázat a portálszerkesztést támogató operációs rendszereket és nyelveket mutatja be:

| | Windows-felhasználás | Windows Prémium rendszer | Dedikált Windows | Linux-fogyasztás | Linux Prémium | Linux dedikált |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# parancsfájl |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (előzetes verzió) | | | | | | |
| PowerShell (előzetes verzió) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup>A portál szerkesztése csak a Linux funkcióihoz használt HTTP és Timer eseményindítók esetén engedélyezett prémium és dedikált csomagok használatával.

## <a name="deployment-slots"></a>Üzembehelyezési pontok

Amikor üzembe helyezi a függvényalkalmazást az Azure-ban, központi telepítést akkor is üzembe helyezheti, hogy ne közvetlenül az éles környezetben. A központi telepítési helyekről további információt az [Azure Functions telepítési tárolók](../app-service/deploy-staging-slots.md) dokumentációjában talál.

## <a name="next-steps"></a>További lépések

Olvassa el ezeket a cikkeket, ha többet szeretne megtudni a függvényalkalmazások üzembe helyezéséről: 

+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)
+ [Folyamatos kézbesítés az Azure DevOps használatával](functions-how-to-azure-devops.md)
+ [Zip-telepítések az Azure Functionshez](deployment-zip-push.md)
+ [Az Azure-függvények futtatása csomagfájlból](run-functions-from-deployment-package.md)
+ [Erőforrás-telepítés automatizálása a függvényalkalmazáshoz az Azure Functionsben](functions-infrastructure-as-code.md)
