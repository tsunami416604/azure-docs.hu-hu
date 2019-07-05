---
title: Az Azure Functions központi telepítési technológiák |} A Microsoft Docs
description: Ismerje meg, hogy a különböző módokon kódot helyezhet üzembe Azure Functions funkciók rejtelmeit.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 118daf02ab59646f2926071763aa4d7e97846e04
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508222"
---
# <a name="deployment-technologies-in-azure-functions"></a>Központi telepítési technológiák az Azure Functions szolgáltatásban

Van néhány különböző technológiák segítségével üzembe helyezése az Azure Functions-projekt kódját az Azure-bA. Ez a cikk biztosít az olyan technológiák tárgyal, tájékoztatja, hogy mely technológiák érhetők el melyik változatban érhetők el a függvények, azt ismerteti, valójában mi történik, amikor az egyes módszerek szolgál, és ajánlásokat fogalmaz meg a legjobb módszer használata különböző forgatókönyvekben. A különböző eszközöket, amelyek támogatják az Azure Functions üzembe helyezése a környezettől függően a technológia helyes vannak szabva.

## <a name="deployment-technology-availability"></a>Központi telepítési technológia rendelkezésre állása

> [!IMPORTANT]
> Az Azure Functions támogatja a cross platform helyi fejlesztési és üzemeltetési két operációs rendszereken: Windows és Linux rendszerű. Nincsenek három szolgáltatási csomagok jelenleg elérhető, másként - rendelkező [fogyasztás](functions-scale.md#consumption-plan), [prémium](functions-scale.md#premium-plan), és [(App Service) dedikált](functions-scale.md#app-service-plan). Nem minden központi telepítési technológiák egyes íz az Azure Functions érhetők el.

| Központi telepítési technológia | Windows-felhasználás | Windows támogatási (előzetes verzió) | Dedikált Windows  | Linux-használat (előzetes verzió) | Dedikált Linux |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| Külső csomag URL-cím<sup>1</sup> |✔|✔|✔|✔|✔|
| Zip üzembe helyezése |✔|✔|✔| |✔|
| Docker-tároló | | | | |✔|
| A Web Deploy |✔|✔|✔| | |
| Verziókövetés |✔|✔|✔| |✔|
| Helyi Git<sup>1</sup> |✔|✔|✔| |✔|
| Felhőalapú szinkronizálás<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Portál szerkesztése |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> központi telepítési technológia igénylő [manuális eseményindító szinkronizálása](#trigger-syncing).
<sup>2</sup> portál szerkesztési engedélyezve van csak a HTTP- és időzítő eseményindító függvények a dedikált csomagok használatával Linux rendszeren.

## <a name="key-concepts"></a>Fő fogalmak

A folytatás előtt fontos ismerje meg, néhány főbb fogalmakat kritikus fontosságú központi telepítések működése az Azure Functions ismertetése.

### <a name="trigger-syncing"></a>Az eseményindító szinkronizálása

Ha bármelyik az eseményindítók, a Functions-infrastruktúra kell figyelembe venni ezeket a módosításokat. A szinkronizálás számos központi telepítési technológiák esetében automatikusan történik. Azonban bizonyos esetekben manuálisan kell szinkronizálnia az eseményindítók. A frissítések, egy külső csomag URL-CÍMÉT, helyi Git, felhőalapú szinkronizálás vagy FTP használatával történő telepítésekor ne felejtse el manuálisan szinkronizálhatja az eseményindítók kell lennie. Eseményindítók három módszerrel szinkronizálhatók:

* Indítsa újra a függvényalkalmazást az Azure Portalon
* Egy HTTP POST-kérést küld `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` használatával a [főkulcs](functions-bindings-http-webhook.md#authorization-keys).
* Egy HTTP POST-kérést küld `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. A helyőrzőket cserélje le az előfizetés-azonosító, erőforráscsoport-nevet és a függvényalkalmazás nevére.

## <a name="deployment-technology-details"></a>Központi telepítési technológia részletei  

Az Azure Functions támogatja a következő központi telepítési módszerekkel.

### <a name="external-package-url"></a>Külső csomag URL-címe

Lehetővé teszi egy távoli csomag (.zip) fájlt, amely tartalmazza a függvényalkalmazás hivatkozhat. A fájl letöltése a megadott URL-címről, és az alkalmazás fut [Run-a-Package](run-functions-from-deployment-package.md) mód.

>__Hogyan kell használni:__ Adjon hozzá `WEBSITE_RUN_FROM_PACKAGE` való az alkalmazás beállításait. Ez a beállítás értékét kell lennie egy URL - szeretné futtatni a megadott alkalmazáscsomag-fájl helyét. Beállítások adhat hozzá vagy [a portálon](functions-how-to-use-azure-function-app-settings.md#settings) vagy [az Azure CLI-vel](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Azure blob storage használatával, ha egy privát tároló használja egy [közös hozzáférésű Jogosultságkód (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) funkciók hozzáférést adhat a csomaghoz. Bármikor az alkalmazás újraindul, beolvassa a tartalmat, ami azt jelenti, hogy a hivatkozás érvényesnek kell lennie az alkalmazás teljes élettartama egy példányát.

>__Mikor érdemes használni, azt:__ Ez a egyetlen üzembe helyezési mód az Azure Functions Linux rendszeren futó, Használatalapú csomagban (előzetes verzió) támogatott. A függvényalkalmazás hivatkozik alkalmazáscsomag-fájl frissítésekor kell [manuálisan szinkronizálni az eseményindítók](#trigger-syncing) Azure állapítható meg, hogy az alkalmazás módosult.

### <a name="zip-deploy"></a>Zip üzembe helyezése

Lehetővé teszi az Azure-bA a függvényalkalmazást tartalmazó zip-fájlt. Ha szükséges, megadhatja azt is kell az alkalmazást start [Run-a-Package](run-functions-from-deployment-package.md) mód.

>__Hogyan kell használni:__ Üzembe helyezés kedvenc ügyféleszköz - [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), vagy a [Azure CLI-vel](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Manuális üzembe helyezést egy zip-fájlt a függvényalkalmazást, kövesse az utasításokat, címen található [üzembe helyezése a egy zip-fájlba vagy URL-cím](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Ezenkívül üzembe helyezést a zip, a felhasználók megadhatják, hogy az alkalmazás futtatását a [Run-a-Package](run-functions-from-deployment-package.md) mód beállításával a `WEBSITE_RUN_FROM_PACKAGE` alkalmazás beállítás értékével megegyező `1`. Ez a beállítás ajánlott, és az alkalmazások gyorsabb betöltése eredményez. Ez az a fenti ügyféleszközök alapértelmezés szerint történik.

>__Mikor érdemes használni, azt:__ Ez az Azure Functions, a Windows rendszert futtató és a dedikált tervben a linuxon futó Azure Functions ajánlott üzembe helyezési technológiát.

### <a name="docker-container"></a>Docker-tároló

Tárolórendszerkép üzembe helyezése Linux rendszerű, amely tartalmazza a függvényalkalmazást.

>__Hogyan kell használni:__ Hozzon létre egy Linux függvényalkalmazást a dedikált tervben, és adja meg, melyik tárolórendszerkép-ről futtatva. Ezt kétféleképpen teheti meg:
>
>* Hozzon létre egy Linux-függvényalkalmazást az App Service-csomag, az Azure Portalon. Válassza ki **Docker-rendszerkép** a **közzététel**, és konfigurálja a tárolót, a helyet, ahol a rendszerképe biztosít.
>* Hozzon létre egy Linux-függvényalkalmazást az App Service-csomag, az Azure CLI-n keresztül. Ismerje meg, hogyan áttekintésével [függvény létrehozása Linux rendszerben egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Egy meglévő alkalmazást, egy egyéni tároló használatával szeretné telepíteni, használja a [ `func deploy` ](functions-run-local.md#publish) parancsot, a [Azure Functions Core Tools](functions-run-local.md).

>__Mikor érdemes használni, azt:__ Használja ezt a beállítást, ha a Linuxos környezetben keresztül átfogóbb vezérlésre van szüksége a ahol fut, a függvényalkalmazást. A központi telepítési mechanizmust csak az funkciók a linuxon futó App Service-csomagban érhető el.

### <a name="web-deploy-msdeploy"></a>A Web deploy (MSDeploy)

A csomagok és helyez üzembe Windows-alkalmazásait akár minden olyan IIS-kiszolgálón, beleértve a Windows Azure-ban futó függvényalkalmazást.

>__Hogyan kell használni:__ Használja a [Azure Functions Visual Studio-eszközök](functions-create-your-first-function-visual-studio.md), és törölje a jelet a `Run from package file (recommended)` mezőbe.
>
> Letölthető, [webalkalmazás üzembe helyezése 3.6](https://www.iis.net/downloads/microsoft/web-deploy) hívja `MSDeploy.exe` közvetlenül.

>__Mikor érdemes használni, azt:__ A központi telepítési technológia támogatott és nem problémákkal rendelkezik, de az előnyben részesített mechanizmus most [Zip üzembe helyezéséhez futtassa a csomag engedélyezett](#zip-deploy). További tudnivalókért látogasson el a [Visual Studio – fejlesztési útmutató](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Verziókövetés

Csatlakozás a függvényalkalmazást egy git-tárház úgy, hogy az adott tárházba kód frissítéseit elindítja a központi telepítési teszi lehetővé. További információkért tekintse meg a [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Hogyan kell használni:__ Az Azure Functions portálon az üzembe helyezési központ segítségével történő közzététel a forráskezelőből beállítása. További információkért lásd: [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md).

>__Mikor érdemes használni, azt:__ Verziókövetés használatával saját függvényalkalmazások dolgoztunk csapatok számára az ajánlott eljárás, és ez az ideális megoldás, amely lehetővé teszi a kifinomultabb üzembe helyezési folyamatok.

### <a name="local-git"></a>Helyi git

Lehetővé teszi, hogy a helyi gépen leküldéses kód Git használatával az Azure Functions.

>__Hogyan kell használni:__ Kövesse az utasításokat, [helyi Git üzemelő példányt az Azure App Service-](../app-service/deploy-local-git.md).

>__Mikor érdemes használni, azt:__ Általánosságban véve egyéb telepítési módszerek használata ajánlott. A helyi gitből közzétételekor kell [manuálisan szinkronizálni az eseményindítók](#trigger-syncing).

### <a name="cloud-sync"></a>Felhőalapú szinkronizálás

Lehetővé teszi a tartalom az Azure Functions a Dropbox és a onedrive vállalati verzió szinkronizálása.

>__Hogyan kell használni:__ Kövesse a [tartalom szinkronizálása egy felhőbeli mappából](../app-service/deploy-content-sync.md).

>__Mikor érdemes használni, azt:__ Általánosságban véve egyéb telepítési módszerek használata ajánlott. Felhőalapú szinkronizálás közzétételekor kell [manuálisan szinkronizálni az eseményindítók](#trigger-syncing).

### <a name="ftp"></a>FTP

Lehetővé teszi közvetlenül az Azure Functions át a fájlokat.

>__Hogyan kell használni:__ Kövesse a [Telepítse központilag a tartalmakat az FTP-vagy https használatával](../app-service/deploy-ftp.md).

>__Mikor érdemes használni, azt:__ Általánosságban véve egyéb telepítési módszerek használata ajánlott. FTP-közzétételekor kell [manuálisan szinkronizálni az eseményindítók](#trigger-syncing).

### <a name="portal-editing"></a>Portál szerkesztése

A portálalapú-szerkesztő használatával teszi lehetővé a függvényalkalmazás a fájlok közvetlenül szerkesztheti (lényegében üzembe helyezése kattintva bármikor **mentése**).

>__Hogyan kell használni:__ Hogy a függvények az Azure Portalon szerkesztheti, rendelkeznie kell [a functions portálon létrehozott](functions-create-first-azure-function.md). Bármely más központi telepítési módszer használatával lehetővé teszi a funkció csak olvasható és nem folyamatos portál módosítható, egy egyetlen hiteles forrásaként megőrzése érdekében. Térjen vissza olyan állapotban, amelyben szerkesztheti a fájlokat az Azure portal használatával, manuálisan kapcsolja be a a szerkesztési mód vissza `Read/Write` , és távolítsa el az alkalmazás központi telepítési kapcsolatos beállítások (például `WEBSITE_RUN_FROM_PACKAGE`). 

>__Mikor érdemes használni, azt:__ A portál egy nagyszerű mód arra, hogy az Azure Functions használatának első lépései, de bármilyen intenzívebb fejlesztési munkálatok során, az ügyfél azokat az eszközöket ajánlott:
>
>* [Ismerkedés a VS Code használatával](functions-create-first-function-vs-code.md)
>* [Ismerkedés az Azure Functions Core Tools használatával](functions-run-local.md)
>* [Ismerkedés a Visual Studio használatával](functions-create-your-first-function-visual-studio.md)

Az alábbi táblázat az operációs rendszerek és, amelynek portál szerkesztése támogatott nyelvek:

| | Windows-felhasználás | Windows támogatási (előzetes verzió) | Dedikált Windows | Linux-használat (előzetes verzió) | Dedikált Linux |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C#Parancsfájl |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (előzetes verzió) | | | | | |
| PowerShell (előzetes verzió) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> Portál szerkesztési engedélyezve van a csak a HTTP- és időzítő eseményindító függvények a dedikált csomagok használatával Linux rendszeren.

## <a name="deployment-slots"></a>Üzembehelyezési pontok

A függvényalkalmazás Azure-ba történő telepítésekor telepíthet egy külön üzembe helyezési pont helyett közvetlenül az éles környezetbe. Az üzembe helyezési pontok további információkért lásd: [az Azure App Service-pontjainak dokumentáció](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Támogatási szintet az üzembe helyezési pontok

Nincsenek támogatási két szintet:

* _Általánosan elérhető (GA)_ – teljes körűen támogatott, és éles használatra jóváhagyott.
* _Előzetes verzió_ – még nem támogatott, de a várt a későbbiekben elérni a végleges verzió állapota.

| Az operációs rendszer/üzemeltetési terv | Támogatási szint |
| --------------- | ------ |
| Windows-felhasználás | Előzetes verzió |
| Windows támogatási (előzetes verzió) | Előzetes verzió |
| Dedikált Windows | Általánosan elérhető |
| Linux-felhasználás | Nem támogatott |
| Dedikált Linux | Általánosan elérhető |

## <a name="next-steps"></a>További lépések

További információ a következő cikkekben a függvényalkalmazások üzembe helyezése: 

+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)
+ [Folyamatos készregyártás az Azure DevOps használatával](functions-how-to-azure-devops.md)
+ [Zip-telepítések az Azure Functions szolgáltatáshoz](deployment-zip-push.md)
+ [Az Azure Functions futtatása egy csomagfájlt](run-functions-from-deployment-package.md)
+ [A függvényalkalmazás az Azure Functions erőforrások üzembe helyezésének automatizálása](functions-infrastructure-as-code.md)
