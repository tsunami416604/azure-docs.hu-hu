---
title: Az Azure Functions központi telepítési technológiák |} A Microsoft Docs
description: Ismerje meg a kódot helyezhet üzembe Azure Functions különböző módjait.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594395"
---
# <a name="deployment-technologies-in-azure-functions"></a>Központi telepítési technológiák az Azure Functions szolgáltatásban

Néhány különböző technológiák segítségével üzembe helyezése az Azure Functions-projekt kódját az Azure-bA. Ez a cikk biztosít az olyan technológiák tárgyal, azt ismerteti, hogy mely technológiák érhetők el melyik változatban érhetők el a függvények, azt ismerteti, mi történik, ha minden módszer használata és a legjobb módszer a különböző forgatókönyvekben használandó vonatkozó javaslatokkal szolgál . A különböző eszközöket, amelyek támogatják az Azure Functions üzembe helyezése a környezettől függően a technológia helyes vannak szabva.

## <a name="deployment-technology-availability"></a>Központi telepítési technológia rendelkezésre állása

> [!IMPORTANT]
> Az Azure Functions támogatja a helyi fejlesztési platformok közötti és a Windows és Linux rendszereken üzemeltetésére. Jelenleg három szolgáltatási csomagok érhetők el: [Felhasználás](functions-scale.md#consumption-plan), [prémium](functions-scale.md#premium-plan), és [(az Azure App Service) dedikált](functions-scale.md#app-service-plan). Minden csomag másként van. Nem minden központi telepítési technológiák egyes íz az Azure Functions érhetők el.

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
<sup>2</sup> portál szerkesztési engedélyezve van csak a HTTP- és időzítő eseményindító függvények a dedikált csomag használata Linux rendszeren.

## <a name="key-concepts"></a>Fő fogalmak

Néhány főbb fogalmak szempontból kulcsfontosságúak, központi telepítések működése az Azure Functions ismertetése.

### <a name="trigger-syncing"></a>Az eseményindító szinkronizálása

Ha bármelyik az eseményindítók, a Functions-infrastruktúra változásokat kell lennie. Szinkronizálási számos központi telepítési technológiák esetében automatikusan történik. Bizonyos esetekben azonban szinkronizálni kell az eseményindítók manuálisan. A frissítések hivatkozó egy külső csomag URL-CÍMÉT, helyi Git, felhőalapú szinkronizálás vagy az FTP telepítésekor meg kell manuálisan is szinkronizálhatja az eseményindítók. Eseményindítók három módszerrel lehet szinkronizálni:

* Indítsa újra a függvényalkalmazást az Azure Portalon
* Egy HTTP POST-kérést küld `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` használatával a [főkulcs](functions-bindings-http-webhook.md#authorization-keys).
* Egy HTTP POST-kérést küld `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. A helyőrzőket cserélje le az előfizetés-azonosító, erőforráscsoport-nevet és a függvényalkalmazás nevére.

## <a name="deployment-technology-details"></a>Központi telepítési technológia részletei 

Az alábbi telepítési módszerek érhetők el az Azure Functions szolgáltatásban.

### <a name="external-package-url"></a>Külső csomag URL-címe

Egy külső csomag URL-cím segítségével egy távoli csomag (.zip) fájlt, amely tartalmazza a függvényalkalmazás hivatkozhat. A fájl letöltése a megadott URL-címről, és az alkalmazás fut [futtassa a csomag](run-functions-from-deployment-package.md) mód.

>__Hogyan kell használni:__ Adjon hozzá `WEBSITE_RUN_FROM_PACKAGE` való az alkalmazás beállításait. Ez a beállítás értéke egy URL-címet (szeretné futtatni a megadott alkalmazáscsomag-fájl helyét) kell lennie. Beállítások adhat hozzá vagy [a portálon](functions-how-to-use-azure-function-app-settings.md#settings) vagy [az Azure CLI-vel](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Ha Azure Blob storage szolgáltatást használja, használjon egy privát tároló egy [közös hozzáférésű jogosultságkód (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) funkciók hozzáférést adhat a csomaghoz. Amikor az alkalmazás újraindul, lekérdezi a tartalom egy példányával. A hivatkozás az alkalmazás teljes élettartama érvényesnek kell lennie.

>__Mikor érdemes használni, azt:__ Külső csomag URL-CÍMÉT az Azure Functions Linux rendszeren futó, Használatalapú csomagban (előzetes verzió) az egyetlen támogatott üzembe helyezési módszert. Ha frissíti a csomagfájlt, amely a függvényalkalmazás hivatkozik, akkor meg kell [manuálisan szinkronizálni az eseményindítók](#trigger-syncing) Azure állapítható meg, hogy az alkalmazás módosult.

### <a name="zip-deploy"></a>Zip üzembe helyezése

Használat zip üzembe leküldése egy .zip fájlt, amely tartalmazza a függvényalkalmazás Azure-bA. Igény szerint beállíthatja az alkalmazás elindításához [futtassa a csomag](run-functions-from-deployment-package.md) mód.

>__Hogyan kell használni:__ Telepítse a kedvenc eszköz használatával: [A VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [a Visual Studio](functions-develop-vs.md#publish-to-azure), vagy a [az Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Manuális üzembe helyezést egy .zip-fájlt a függvényalkalmazást, kövesse a [egy .zip-fájlt vagy URL-cím üzembe helyezés](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Telepítésekor a zip használatával helyezhet üzembe, beállíthatja, hogy az alkalmazás futtatását [futtassa a csomag](run-functions-from-deployment-package.md) mód. Futtassa a csomag üzemmód beállítása, állítsa be a `WEBSITE_RUN_FROM_PACKAGE` alkalmazás beállítás értékét `1`. Azt javasoljuk, hogy a zip-telepítés. Azt az alkalmazások gyorsabb betöltése eredményez, és a VS Code, Visual Studio és az Azure CLI az alapértelmezett.

>__Mikor érdemes használni, azt:__ Zip központi telepítése az ajánlott üzembe helyezési technológiát, a Windows rendszert futtató Azure Functions és a dedikált tervben a linuxon futó Azure Functions.

### <a name="docker-container"></a>Docker-tároló

Telepíthet egy, amely tartalmazza a függvényalkalmazás Linux-tároló rendszerképét.

>__Hogyan kell használni:__ Linux függvényalkalmazás létrehozása a dedikált tervben, és adja meg, melyik tárolórendszerkép-ről futtatva. Ezt kétféleképpen teheti meg:
>
>* Linux függvényalkalmazás létrehozása az Azure App Service-csomag, az Azure Portalon. A **közzététel**, jelölje be **Docker-rendszerkép**, majd adja meg a tároló. Adja meg a helyet, ahol a rendszerképe.
>* Az App Service-csomag Linux függvényalkalmazás létrehozása az Azure parancssori felület használatával. További információ [függvény létrehozása Linux rendszerben egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Egy egyéni tároló használatával egy meglévő alkalmazást üzembe [Azure Functions Core Tools](functions-run-local.md), használja a [ `func deploy` ](functions-run-local.md#publish) parancsot.

>__Mikor érdemes használni, azt:__ Használja a Docker tároló beállítást, ha a Linuxos környezetben keresztül átfogóbb vezérlésre van szüksége, a függvényalkalmazás futtatja. A központi telepítési mechanizmust csak az funkciók a linuxon futó App Service-csomagban érhető el.

### <a name="web-deploy-msdeploy"></a>A Web Deploy (MSDeploy)

A Web Deploy-csomagok, és telepíti a Windows-alkalmazásait akár minden olyan IIS-kiszolgálón, beleértve a Windows Azure-ban futó függvényalkalmazást.

>__Hogyan kell használni:__ Használat [Azure Functions Visual Studio-eszközök](functions-create-your-first-function-visual-studio.md). Törölje a **futtatása (ajánlott) csomag fájlból** jelölőnégyzetet.
>
>Letölthető, [webalkalmazás üzembe helyezése 3.6](https://www.iis.net/downloads/microsoft/web-deploy) hívja `MSDeploy.exe` közvetlenül.

>__Mikor érdemes használni, azt:__ A Web Deploy támogatott és nem problémákkal rendelkezik, de az előnyben részesített mechanizmus [zip üzembe helyezéséhez futtassa a csomag engedélyezett](#zip-deploy). További tudnivalókért tekintse meg a [Visual Studio – fejlesztési útmutató](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Verziókövetés

Verziókövetés használatával a függvényalkalmazást egy Git-tárház csatlakozás. Egy frissítést a kódot az adott tárházba elindítja az üzembe helyezés. További információkért lásd: a [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Hogyan kell használni:__ A portálon az Azure Functions üzembe helyezési központ segítségével történő közzététel a forráskezelőből beállítása. További információkért lásd: [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md).

>__Mikor érdemes használni, azt:__ Verziókövetés használatával Ez az ajánlott eljárás, függvény alkalmazások másokkal közös használatához olyan csapatok számára. Verziókövetés a jó telepítési beállítás lehetővé teszi a kifinomultabb üzembe helyezési folyamatok.

### <a name="local-git"></a>Helyi Git

Segítségével helyi Git push kód a helyi gépen az Azure Functions a Git használatával.

>__Hogyan kell használni:__ Kövesse a [helyi Git üzemelő példányt az Azure App Service-](../app-service/deploy-local-git.md).

>__Mikor érdemes használni, azt:__ Általában azt javasoljuk, hogy a különböző üzembe helyezési módszert használja. A helyi Gitből közzétételekor kell [manuálisan szinkronizálni az eseményindítók](#trigger-syncing).

### <a name="cloud-sync"></a>Felhőalapú szinkronizálás

Használja a Felhő szinkronizálása a szinkronizálási a tartalom az Azure Functions a Dropbox és a onedrive vállalati verzió.

>__Hogyan kell használni:__ Kövesse a [tartalom szinkronizálása egy felhőbeli mappából](../app-service/deploy-content-sync.md).

>__Mikor érdemes használni, azt:__ Általában javasoljuk, hogy más telepítési módszert. Felhőalapú szinkronizálás segítségével történő közzétételekor kell [manuálisan szinkronizálni az eseményindítók](#trigger-syncing).

### <a name="ftp"></a>FTP

FTP használatával közvetlenül az Azure Functions vinni a fájlokat.

>__Hogyan kell használni:__ Kövesse a [Telepítse központilag a tartalmakat az FTP-vagy https használatával](../app-service/deploy-ftp.md).

>__Mikor érdemes használni, azt:__ Általában javasoljuk, hogy más telepítési módszert. FTP-n keresztüli közzétételekor kell [manuálisan szinkronizálni az eseményindítók](#trigger-syncing).

### <a name="portal-editing"></a>Portál szerkesztése

A szerkesztőben portálalapú közvetlenül szerkesztheti a fájlokat, amelyek a függvényalkalmazásban (lényegében üzembe helyezéshez minden alkalommal, amikor a módosítások mentése).

>__Hogyan kell használni:__ Hogy a függvények az Azure Portalon szerkesztheti, rendelkeznie kell [a functions portálon létrehozott](functions-create-first-azure-function.md). Egy egyetlen hiteles forrásaként megőrzéséhez bármely más központi telepítési módszer használatával lehetővé teszi a funkció csak olvasható, és nem folyamatos portál módosítható. Térjen vissza olyan állapotban, amelyben szerkesztheti a fájlokat az Azure Portalon, manuálisan kapcsolja be a a szerkesztési mód vissza `Read/Write` , és távolítsa el az alkalmazás központi telepítési kapcsolatos beállítások (például `WEBSITE_RUN_FROM_PACKAGE`). 

>__Mikor érdemes használni, azt:__ A portál egy jó módja az Azure Functions használatának első lépései. Intenzívebb fejlesztési munkálatok során javasoljuk, hogy az ügyfél eszközkészlet használata:
>
>* [Ismerkedés a VS Code használatával](functions-create-first-function-vs-code.md)
>* [Ismerkedés az Azure Functions Core Tools használatával](functions-run-local.md)
>* [Ismerkedés a Visual Studio használatával](functions-create-your-first-function-visual-studio.md)

Az alábbi táblázat az operációs rendszerek és a nyelvet a támogatási portál szerkesztése:

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

<sup>*</sup> Portál szerkesztési engedélyezve van csak a HTTP- és időzítő eseményindító függvények a dedikált csomag használata Linux rendszeren.

## <a name="deployment-slots"></a>Üzembehelyezési pontok

A függvényalkalmazás Azure-ba történő telepítésekor telepíthet egy külön üzembe helyezési pont helyett közvetlenül az éles környezetben való üzembe helyezéséhez. Üzembe helyezési pontok kapcsolatos további információkért lásd: [Azure App Service-tárolóhely](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Támogatási szintet az üzembe helyezési pontok

Nincsenek üzembe helyezési pontok támogatása két szintje:

* **Általánosan elérhető (GA)** : Teljes körűen támogatott és jóváhagyott éles környezetben való használatra.
* **Előzetes verzió**: Még nem támogatott, de a végleges verzió állapota elérni a jövőben várható.

| Az operációs rendszer/üzemeltetési terv | Támogatási szint |
| --------------- | ------ |
| Windows-felhasználás | Előzetes verzió |
| Windows támogatási (előzetes verzió) | Előzetes verzió |
| Dedikált Windows | Általános elérhetőség |
| Linux-felhasználás | Nem támogatott |
| Dedikált Linux | Általános elérhetőség |

## <a name="next-steps"></a>További lépések

Olvassa el az alábbi cikkek tudhat meg többet a függvényalkalmazások üzembe helyezése: 

+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)
+ [Folyamatos készregyártás az Azure DevOps használatával](functions-how-to-azure-devops.md)
+ [Zip-telepítések az Azure Functions szolgáltatáshoz](deployment-zip-push.md)
+ [Az Azure Functions futtatása egy csomagfájlt](run-functions-from-deployment-package.md)
+ [A függvényalkalmazás az Azure Functions erőforrások üzembe helyezésének automatizálása](functions-infrastructure-as-code.md)
