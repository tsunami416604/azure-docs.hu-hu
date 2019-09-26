---
title: Docker-tárolók – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS-tároló betölti a betanított vagy közzétett alkalmazást egy Docker-tárolóba, és hozzáférést biztosít a tároló API-végpontjai lekérdezési előrejelzéséhez.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: 7de529c9478b78ff9dd042df90a061f0a96f5427
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316403"
---
# <a name="install-and-run-luis-docker-containers"></a>LUIS Docker-tárolók telepítése és futtatása
 
A Language Understanding (LUIS) tároló betölti a betanított vagy közzétett Language Understanding modellt. Luis- [alkalmazásként](https://www.luis.ai)a Docker-tároló hozzáférést biztosít a tároló API-végpontjának lekérdezési előrejelzéséhez. Lekérdezési naplókat gyűjthet a tárolóból, és feltöltheti őket a Language Understanding alkalmazásba az alkalmazás előrejelzési pontosságának javítása érdekében.

A következő videó bemutatja, hogyan használhatja ezt a tárolót.

[![Cognitive Services tároló bemutató](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A LUIS-tároló futtatásához vegye figyelembe a következő előfeltételeket:

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)és [Linux](https://docs.docker.com/engine/installation/#supported-platforms)rendszereken. A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a `docker` tárolók lemezképéről, valamint az alapszintű parancsokról.| 
|Azure `Cognitive Services` -erőforrás és Luis [csomagolt alkalmazás](luis-how-to-start-new-app.md#export-app-for-containers) fájlja |A tároló használatához a következőket kell tennie:<br><br>* Egy _Cognitive Services_ Azure-erőforrás és a kapcsolódó számlázási kulcs a számlázási végpont URI-ja. Mindkét érték elérhető az erőforrás áttekintés és kulcsok oldalain, és a tároló indításához szükséges. <br>* Egy betanított vagy közzétett alkalmazás, amely csatlakoztatott bemenetként van csomagolva a tárolóhoz a hozzá tartozó alkalmazás-AZONOSÍTÓval. A csomagolt fájlt a LUIS portálról vagy a szerzői API-k használatával szerezheti be. Ha a [szerzői API](#authoring-apis-for-package-file)-kkal becsomagolta a Luis csomagolási alkalmazást, a _szerzői kulcsra_is szüksége lesz.<br><br>Ezek a követelmények a parancssori argumentumok átadására szolgálnak a következő változókra:<br><br>**{AUTHORING_KEY}** : Ezzel a kulccsal lekérheti a csomagolt alkalmazást a felhőben található LUIS szolgáltatásból, és feltöltheti a lekérdezési naplókat a felhőbe. A formátum `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`:.<br><br>**{APPLICATION_ID}** : Ez az azonosító az alkalmazás kiválasztására szolgál. A formátum `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`:.<br><br>**{API_KEY}** : Ez a kulcs a tároló elindítására szolgál. A végpont kulcsa két helyen található. Az első az _Cognitive Services_ erőforrás kulcsai listán szereplő Azure Portal. A végpont kulcsa a LUIS portálon is elérhető a kulcsok és a végpont beállításai lapon. Ne használja az alapszintű kulcsot.<br><br>**{ENDPOINT_URI}** : Az Áttekintés oldalon megadott végpont.<br><br>A [szerzői kulcs és a végpont kulcsa](luis-boundaries.md#key-limits) eltérő célokat szolgál. Ne használja szinonimaként. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>A csomagfájl API-jai készítése

API-k készítése csomagolt alkalmazásokhoz:

* [Közzétett csomag API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Nem közzétett, csak betanított csomag API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

Ez a tároló a beállítások minimális és ajánlott értékeit támogatja:

|Tároló| Minimális | Ajánlott | TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|LUIS|1 mag, 2 GB memória|1 mag, 4 GB memória|20, 40|

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* TPS – tranzakció/másodperc

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a`docker pull`

A paranccsal töltheti le az `mcr.microsoft.com/azure-cognitive-services/luis` adattárból a tároló képét: [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával töltse le a tárolók rendszerképét.

Az elérhető címkék teljes leírását, például `latest` az előző parancsban használt információt lásd: [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) on Docker hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a gazdagépen [](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

![Language Understanding (LUIS) tároló használatának folyamata](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Csomag exportálása](#export-packaged-app-from-luis) a tárolóhoz a Luis Portalról vagy Luis API-kkal.
1. Helyezze át a csomag fájlját a gazdagépen a [](#the-host-computer)szükséges **bemeneti** könyvtárba. A LUIS-csomagfájl átnevezése, módosítása, felülírása vagy kibontása.
1. [Futtassa a tárolót](##run-the-container-with-docker-run)a szükséges _bemeneti csatlakoztatási_ és számlázási beállításokkal. További [példák](luis-container-configuration.md#example-docker-run-commands) a `docker run` parancsra. 
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint). 
1. Ha végzett a tárolóval, importálja [a végponti naplókat](#import-the-endpoint-logs-for-active-learning) a Luis-portál kimeneti csatlakoztatásáról, és [állítsa le](#stop-the-container) a tárolót.
1. Az alkalmazás fejlesztéséhez használja a LUIS Portal [aktív tanulását](luis-how-to-review-endpoint-utterances.md) a **végpontok áttekintése hosszúságú kimondott szöveg** oldalon.

A tárolóban futó alkalmazás nem módosítható. A tárolóban lévő alkalmazás módosításakor a Luis [-portálon](https://www.luis.ai) , vagy a Luis [authoring API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)-k használatával módosítania kell az alkalmazást a Luis szolgáltatásban. Ezután végezze el a betanítást és/vagy közzétételt, majd töltsön le egy új csomagot, majd futtassa újra a tárolót.

A tárolón belüli LUIS-alkalmazás nem exportálható vissza a LUIS szolgáltatásba. Csak a lekérdezési naplók tölthetők fel. 

## <a name="export-packaged-app-from-luis"></a>Csomagolt alkalmazás exportálása a LUIS-ből

A LUIS-tárolónak egy betanított vagy közzétett LUIS-alkalmazást kell megadnia a felhasználói hosszúságú kimondott szöveg előrejelzési lekérdezései megválaszolásához. A LUIS-alkalmazás beszerzéséhez használja a betanított vagy a közzétett csomag API-t. 

Az alapértelmezett hely az a `input` alkönyvtár, amelyben a `docker run` parancsot futtatja.  

Helyezze a csomagfájl egy könyvtárba, és hivatkozzon erre a könyvtárra a Docker-tároló futtatásakor bemeneti csatlakoztatásként. 

### <a name="package-types"></a>Csomagok típusai

A bemeneti csatlakoztatási könyvtár az alkalmazás **éles üzemi**, **előkészítési**és betanított verzióit is tartalmazhatja. Az összes csomag csatlakoztatva van. 

|Csomag típusa|Lekérdezési végpont API|Lekérdezés elérhetősége|Csomag fájlnevének formátuma|
|--|--|--|--|
|Betanítva|LETÖLTÉS, KÖZZÉTÉTEL|Csak tároló|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Fájlok másolása folyamatban|LETÖLTÉS, KÖZZÉTÉTEL|Azure és Container|`{APPLICATION_ID}_STAGING.gz`|
|Üzemi|LETÖLTÉS, KÖZZÉTÉTEL|Azure és Container|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Ne nevezze át, módosítsa, írja felül vagy bontsa ki a LUIS-csomagok fájljait.

### <a name="packaging-prerequisites"></a>A csomagolás előfeltételei

A LUIS-alkalmazás csomagolása előtt a következőkkel kell rendelkeznie:

|Csomagolási követelmények|Részletek|
|--|--|
|Azure- _Cognitive Services_ erőforrás-példány|A támogatott régiók a következők:<br><br>USA nyugati régiója (`westus`)<br>Nyugat-Európa`westeurope`()<br>Kelet-Ausztrália (`australiaeast`)|
|Betanított vagy közzétett LUIS-alkalmazás|Nem [támogatott függőségek](#unsupported-dependencies)nélkül. |
|Hozzáférés a [gazdaszámítógép](#the-host-computer)fájlrendszeréhez |A gazdaszámítógépnek engedélyeznie kell egy [bemeneti csatlakoztatást](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Alkalmazáscsomag exportálása a LUIS portálról

A LUIS [portál](https://www.luis.ai) lehetővé teszi a betanított vagy közzétett alkalmazás csomagjának exportálását. 

### <a name="export-published-apps-package-from-luis-portal"></a>Közzétett alkalmazás csomagjának exportálása a LUIS portálról

A közzétett alkalmazás csomagja a **saját alkalmazások** listája lapon érhető el. 

1. Jelentkezzen be a LUIS [portálra](https://www.luis.ai).
1. Jelölje be az alkalmazás nevének bal oldalán található jelölőnégyzetet a listában. 
1. Válassza ki a lista fölötti környezetfüggő eszköztár **Exportálás** elemét.
1. Válassza **az Exportálás a tárolóba (gzip)** lehetőséget.
1. Válassza ki az **üzemi tárolóhely** vagy az **átmeneti tárolóhely**környezetét.
1. A csomag le van töltve a böngészőből.

![A tároló közzétett csomagjának exportálása az alkalmazás oldalának exportálás menüjéből](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Betanított alkalmazás csomagjának exportálása a LUIS-portálról

A betanított alkalmazás csomagja a **verziók** listája lapon érhető el. 

1. Jelentkezzen be a LUIS [portálra](https://www.luis.ai).
1. Válassza ki az alkalmazást a listában. 
1. Az alkalmazás navigációs sávján válassza a **kezelés** lehetőséget.
1. A bal oldali navigációs sávon válassza a **verziók** lehetőséget.
1. Jelölje be a listában a verzió nevének bal oldalán található jelölőnégyzetet.
1. Válassza ki a lista fölötti környezetfüggő eszköztár **Exportálás** elemét.
1. Válassza **az Exportálás a tárolóba (gzip)** lehetőséget.
1. A csomag le van töltve a böngészőből.

![Exportálja a tárolóhoz tartozó betanított csomagot a verziók oldal exportálás menüjéből.](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Közzétett alkalmazás csomagjának exportálása az API-ból

A következő REST API módszer használatával csomagolja ki a már [közzétett](luis-how-to-publish-app.md)Luis-alkalmazást. Az API-hívásban szereplő helyőrzők saját megfelelő értékeinek helyettesítése a HTTP-specifikáció alatt található táblázat használatával.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Helyőrző | Value |
|-------------|-------|
| **{APPLICATION_ID}** | A közzétett LUIS-alkalmazás alkalmazás-azonosítója. |
| **{APPLICATION_ENVIRONMENT}** | A közzétett LUIS-alkalmazás környezete. Használja az alábbi értékek egyikét:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | A LUIS-fiók szerzői kulcsa a közzétett LUIS-alkalmazáshoz.<br/>A szerzői kulcsot a LUIS-portál **felhasználói beállítások** lapján szerezheti be. |
| **{AZURE_REGION}** | A megfelelő Azure-régió:<br/><br/>`westus`– USA nyugati régiója<br/>`westeurope`– Nyugat-Európa<br/>`australiaeast`– Kelet-Ausztrália |

A közzétett csomag letöltéséhez tekintse meg az [API-dokumentációt itt][download-published-package]. Ha a fájl letöltése sikeres volt, a válasz egy LUIS csomagfájl. Mentse a fájlt a tároló bemeneti csatlakoztatásához megadott tárolási helyre. 

### <a name="export-trained-apps-package-from-api"></a>Betanított alkalmazás csomagjának exportálása az API-ból

A következő REST API módszer használatával csomagolja ki a már betanított LUIS- [](luis-how-to-train.md)alkalmazást. Az API-hívásban szereplő helyőrzők saját megfelelő értékeinek helyettesítése a HTTP-specifikáció alatt található táblázat használatával.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Helyőrző | Value |
|-------------|-------|
| **{APPLICATION_ID}** | A betanított LUIS alkalmazás alkalmazás-azonosítója. |
| **{APPLICATION_VERSION}** | A betanított LUIS alkalmazás alkalmazásának verziója. |
| **{AUTHORING_KEY}** | A LUIS-fiók szerzői kulcsa a közzétett LUIS-alkalmazáshoz.<br/>A szerzői kulcsot a LUIS-portál **felhasználói beállítások** lapján szerezheti be. |
| **{AZURE_REGION}** | A megfelelő Azure-régió:<br/><br/>`westus`– USA nyugati régiója<br/>`westeurope`– Nyugat-Európa<br/>`australiaeast`– Kelet-Ausztrália |

A betanított csomag letöltéséhez tekintse meg az [API dokumentációját itt][download-trained-package]. Ha a fájl letöltése sikeres volt, a válasz egy LUIS csomagfájl. Mentse a fájlt a tároló bemeneti csatlakoztatásához megadott tárolási helyre. 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a`docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A`{ENDPOINT_URI}` és`{API_KEY}` értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört.

[](luis-container-configuration.md#example-docker-run-commands) A`docker run` parancs például elérhető.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Ez a példa a `C:` meghajtón lévő könyvtárat használja, hogy elkerülje az engedélyek ütközését a Windowson. Ha szeretné használni a bemeneti könyvtár egy adott címtárhoz, előfordulhat, hogy kell biztosítania a docker szolgáltatás engedéllyel. 
* Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.
* Ha más operációs rendszert használ, használja a megfelelő konzolt/terminált, a csatlakoztatások mappájának szintaxisát, valamint a rendszer vonal folytatási karakterét. Ezek a példák egy sor folytatási karakterrel `^`rendelkező Windows-konzolt feltételeznek. Mivel a tároló egy Linux operációs rendszer, a cél csatlakoztatása a Linux-stílusú mappa szintaxisát használja.

Ez a parancs:

* Tároló futtatása a LUIS-tároló rendszerképből
* Betölti a (z) *C:\input*-on található Luis alkalmazást a Container Host-on.
* Két CPU-mag és 4 gigabájt (GB) memória kiosztása
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Tároló-és LUIS-naplókat ment a *C:\output*-on lévő, a tároló gazdagépén található kimeneti csatlakoztatásra.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen. 

További [példák](luis-container-configuration.md#example-docker-run-commands) a `docker run` parancsra. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).
> A ApiKey értéke a Luis portál **Azure Resources** lapján található `Cognitive Services` **kulcs** , amely az Azure Resource Keys oldalon is elérhető.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>A tároló által támogatott Endpoint API-k

Az API v2 és [v3 (előzetes verzió)](luis-migration-api-v3.md) verziója is elérhető a tárolóban. 

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. A közzétett (átmeneti vagy éles) alkalmazások végpontjai _eltérő_ útvonalon rendelkeznek, mint a betanított alkalmazások végpontja. 

A tároló API `http://localhost:5000`-k esetében használja a gazdagépet. 

|Csomag típusa|Módszer|Útvonal|Lekérdezési paraméterek|
|--|--|--|--|
|Közzétett|[LETÖLTÉS](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [KÖZZÉTÉTEL](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>& előkészítés<br>[& timezoneOffset]<br>[& részletesen]<br>[& napló]<br>|
|Betanítva|LETÖLTÉS, KÖZZÉTÉTEL|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[& timezoneOffset]<br>[& részletesen]<br>[& napló]|

A lekérdezési paraméterek a lekérdezési válaszban megadhatják, hogy hogyan és mit ad vissza:

|Lekérdezési paraméter|Type|Cél|
|--|--|--|
|`q`|Karakterlánc|A felhasználó megnyilatkozása.|
|`timezoneOffset`|number|A timezoneOffset lehetővé teszi az [](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) előre elkészített entitások datetimeV2 által használt időzóna módosítását.|
|`verbose`|boolean|Az összes leképezést és azok pontszámát adja vissza, ha igaz értékre van állítva. Az alapértelmezett érték a False (hamis), amely csak a felső leképezést adja vissza.|
|`staging`|boolean|Az átmeneti környezet eredményeiből származó lekérdezést adja vissza, ha igaz értékre van állítva. |
|`log`|boolean|Naplózza a lekérdezéseket, amelyek később az [aktív tanuláshoz](luis-how-to-review-endpoint-utterances.md)is felhasználhatók. Az alapértelmezett érték a True (igaz).|

### <a name="query-published-app"></a>Közzétett alkalmazás lekérdezése

A közzétett alkalmazások tárolójának lekérdezésére szolgáló CURL-parancs például a következő:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Az **átmeneti** környezet lekérdezéséhez módosítsa az **átmeneti** lekérdezési karakterlánc paraméterének értékét igaz értékre: 

`staging=true`

### <a name="query-trained-app"></a>Betanított alkalmazás lekérdezése

Egy példa a betanított alkalmazás tárolójának lekérdezésére szolgáló CURL-parancsra: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
A verzió neve legfeljebb 10 karakterből állhat, és csak az URL-címekben engedélyezett karaktereket tartalmazza. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Az aktív tanuláshoz tartozó végponti naplók importálása

Ha a Luis-tárolóhoz kimeneti csatlakoztatás van megadva, a rendszer az App Query naplófájljait a kimeneti könyvtárba menti, `{INSTANCE_ID}` ahol a a tároló azonosítója. Az alkalmazás-lekérdezési napló tartalmazza a LUIS-tárolóba küldött egyes előrejelzési lekérdezések lekérdezését, válaszát és időbélyegét. 

A következő helyen a tároló naplófájljainak beágyazott címtár-szerkezete látható.
```
/output/luis/{INSTANCE_ID}/
```
 
A LUIS portálon válassza ki az alkalmazást, majd válassza a **végponti naplók importálása** elemet a naplók feltöltéséhez. 

![Tároló naplófájljának importálása az aktív tanuláshoz](./media/luis-container-how-to/upload-endpoint-log-files.png)

A napló feltöltése után tekintse [át a](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) hosszúságú kimondott szöveg végpontját a Luis portálon.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

A tároló leállításához a parancssori környezetben, ahol a tároló fut, nyomja le a **CTRL + C**billentyűkombinációt.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti csatlakoztatással [](luis-container-configuration.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A LUIS-tároló számlázási adatokat küld az Azure-nak az Azure-fiókja _Cognitive Services_ erőforrásának használatával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>A `latest` tároló által támogatott függőségek

A 2019 Build kiadásban kiadott legújabb tároló a következőt fogja támogatni:

* Bing helyesírás-ellenőrzés: kérelmek a lekérdezés-előrejelzési végponthoz a `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` lekérdezési karakterlánc paraméterei alapján. További információért használja a [Bing Spell Check v7](luis-tutorial-bing-spellcheck.md) -oktatóanyagot. Ha ezt a funkciót használja, a tároló elküldi a teljes Bing Spell Check v7-erőforrást.
* [Új előre összeépített tartományok](luis-reference-prebuilt-domains.md): ezek a nagyvállalati szintű tartományok többek között entitásokat, például hosszúságú kimondott szöveg és mintákat tartalmaznak. Kiterjesztheti ezeket a tartományokat saját használatra. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>A tárolóhoz tartozó `latest` függőségek nem támogatottak

A [tárolóba való exportáláshoz](#export-packaged-app-from-luis)el kell távolítania a nem támogatott függőségeket a Luis-alkalmazásból. Ha tárolóra próbál exportálni, a LUIS-portál ezeket a nem támogatott szolgáltatásokat jelenti, amelyeket el kell távolítania.

LUIS-alkalmazást használhat, ha az **nem tartalmazza** a következő függőségek egyikét sem:

Nem támogatott alkalmazások konfigurációi|Részletek|
|--|--|
|A tároló kulturális környezetei nem támogatottak| Holland (NL-NL)<br>Japán (ja-JP)<br>A német nyelv csak a [1.0.2-tokenizer](luis-language-support.md#custom-tokenizer-versions)támogatott.|
|Nem támogatott entitások minden kultúrához|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) előre összeépített entitás minden kultúrához|
|Nem támogatott entitások angol (en-US) kulturális környezetekben|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) előre elkészített entitások|
|Beszéd alapozó|A tárolóban a külső függőségek nem támogatottak.|
|Hangulatelemzés|A tárolóban a külső függőségek nem támogatottak.|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta a Language Understanding (LUIS) tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* Language Understanding (LUIS) egy Linux-tárolót biztosít a Docker számára, amely a hosszúságú kimondott szöveg végponti lekérdezési előrejelzéseit biztosítja.
* A rendszer letölti a tároló lemezképeit a Microsoft Container Registryról (MCR).
* Tárolórendszerképek futtatása a Docker.
* REST API használatával lekérdezheti a tároló végpontjait a tároló gazdagép URI azonosítójának megadásával.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](luis-container-configuration.md) a konfigurációs beállítások
* A LUIS [](troubleshooting.md) -funkciókkal kapcsolatos problémák megoldásához tekintse meg a hibaelhárítást ismertető témakört.
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip