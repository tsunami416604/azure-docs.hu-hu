---
title: Docker-tárolók – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS-tároló betölti a betanított vagy közzétett alkalmazást egy docker-tárolóba, és hozzáférést biztosít a lekérdezés-előrejelzésekhez a tároló API-végpontjaiból.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: dapine
ms.openlocfilehash: 308a474970db54022e5351fdf349d9572fbafb0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219928"
---
# <a name="install-and-run-luis-docker-containers"></a>LUIS docker-tárolók telepítése és futtatása
 
A Language Understanding (LUIS) tároló betölti a betanított vagy közzétett language understanding modell. [Luis-alkalmazásként](https://www.luis.ai)a docker-tároló hozzáférést biztosít a lekérdezés-előrejelzések a tároló API-végpontok. Lekérdezési naplók at a tárolóból, és töltse fel őket vissza a nyelvi megértés alkalmazást az alkalmazás előrejelzési pontosságának javítása érdekében.

Az alábbi videó bemutatja a tároló használatát.

[![A Cognitive Services tárolóbemutatója](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A LUIS-tároló futtatásához vegye figyelembe a következő előfeltételeket:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motort egy [gazdaszámítógépen](#the-host-computer)kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy a tárolók csatlakozhassanak az Azure-hoz, és számlázási adatokat küldjenek az Azure-ba. <br><br> **Windows**rendszeren a Docker-t is be kell állítani linuxos tárolók támogatására.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a nyilvántartásokról, az adattárakról, a tárolókról és a tárolórendszerképekről, valamint az alapvető `docker` parancsok ismeretéről.| 
|Azure-erőforrás `Cognitive Services` és LUIS [csomagolt alkalmazásfájl](luis-how-to-start-new-app.md) |A tároló használatához a következőkre van szüksége:<br><br>* A _Cognitive Services_ Azure-erőforrás és a kapcsolódó számlázási kulcs a számlázási végpont URI. Mindkét érték elérhető az áttekintés és a kulcsok oldalon az erőforrás, és szükséges a tároló elindításához. <br>* Egy betanított vagy közzétett alkalmazás, amely a hozzá tartozó alkalmazásazonosítóval rendelkező tárolóhoz csatlakoztatott bemenetként van csomagolva. A csomagolt fájlt a LUIS-portálról vagy a szerzői API-kból szerezheti be. Ha luis-csomagolt alkalmazást kap a [szerzői API-kból,](#authoring-apis-for-package-file)akkor a _szerzői kulcsra_is szüksége lesz.<br><br>Ezek a követelmények a parancssori argumentumok átadására szolgálnak a következő változóknak:<br><br>**{AUTHORING_KEY}**: Ez a kulcs a csomagban alkalmazás behívására szolgál a LUIS szolgáltatásból a felhőben, és feltöltheti a lekérdezési naplókat a felhőbe. A formátum `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}**: Ezzel az azonosítóval választhatja ki az alkalmazást. A formátum `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}**: Ezzel a kulccsal indítható el a tároló. A végpontkulcs két helyen található. Az első az Azure Portal a _Cognitive Services-erőforrás_ kulcslistájában. A végpontkulcs a Kulcsok és végpontbeállítások lapon található LUIS-portálon is elérhető. Ne használja az indítógombot.<br><br>**{ENDPOINT_URI}**: A végpont az Áttekintés lapon megadott aszerint.<br><br>A [szerzői kulcs és a végpontkulcs](luis-boundaries.md#key-limits) különböző célokat szolgál. Ne használja őket szinonimaként. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Szerkesztési API-k a csomagfájlhoz

A csomagolt alkalmazások api-k készítési api-jai:

* [Közzétett csomag API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Nem közzétett, csak betanított csomag API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és ajánlások

Ez a tároló támogatja a beállítások minimális és ajánlott értékeit:

|Tároló| Minimális | Ajánlott | Tps<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|LUIS|1 mag, 2 GB memória|1 mag, 4 GB memória|20, 40|

* Minden magnak legalább 2,6 gigahertzesnek (GHz) vagy gyorsabbnak kell lennie.
* TPS - tranzakciók másodpercenként

A mag és `--cpus` a `--memory` memória megfelel nek a `docker run` és a beállításoknak, amelyek a parancs részeként használatosak.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolókép beszerezése`docker pull`

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs segítségével töltsön le `mcr.microsoft.com/azure-cognitive-services/luis` egy tárolóképet a tárházból:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Az elérhető címkék teljes leírását, például `latest` az előző parancsban használt, lásd: [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) a Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdaszámítógépen](#the-host-computer)van, használja a következő eljárást a tárolóval való munkához.

![A nyelvismertetési (LUIS) tároló használatának folyamata](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportálja](#export-packaged-app-from-luis) a tárolót a LUIS portalról vagy a LUIS API-kból.
1. A csomagfájl áthelyezése a [gazdaszámítógép](#the-host-computer)szükséges **beviteli** könyvtárába . Ne nevezze át, ne módosítsa, írja felül és ne bontsa ki a LUIS-csomagfájlt.
1. [Futtassa a tárolót](#run-the-container-with-docker-run)a szükséges _bemeneti csatlakoztatási_ és számlázási beállításokkal. További [példák](luis-container-configuration.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre. 
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint). 
1. Ha végzett a tárolóval, [importálja a végpontnaplókat](#import-the-endpoint-logs-for-active-learning) a LUIS-portál kimeneti csatlakoztatásából, és [állítsa le](#stop-the-container) a tárolót.
1. A LUIS-portál [aktív tanulás](luis-how-to-review-endpoint-utterances.md) a végpont **kimondott szövegáttekintése** lapon az alkalmazás javítása.

A tárolóban futó alkalmazás nem módosítható. Annak érdekében, hogy az alkalmazás módosítása a tárolóban, meg kell változtatni az alkalmazást a LUIS szolgáltatás a [LUIS](https://www.luis.ai) portálon, vagy használja a LUIS [authoring API-k.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) Ezután betanítása és/vagy közzététele, majd töltse le az új csomagot, és futtassa újra a tárolót.

A tárolóban lévő LUIS-alkalmazás nem exportálható vissza a LUIS-szolgáltatásba. Csak a lekérdezési naplók tölthetők fel. 

## <a name="export-packaged-app-from-luis"></a>Csomagolt alkalmazás exportálása a LUIS-ból

A LUIS-tároló egy betanított vagy közzétett LUIS-alkalmazást igényel a felhasználói kimondott szöveg előrejelzési lekérdezéseinek megválaszolásához. A LUIS-alkalmazás lehívásához használja a betanított vagy közzétett csomag API-t. 

Az alapértelmezett hely `input` az alkönyvtár a `docker run` parancs futtatásának helyéhez viszonyítva.  

Helyezze a csomagfájlt egy könyvtárba, és hivatkozzon erre a könyvtárra bemeneti csatlakoztatásként a docker-tároló futtatásakor. 

### <a name="package-types"></a>Csomagtípusok

A bemeneti csatlakoztatási könyvtár egyszerre tartalmazhatja az alkalmazás **éles**, **átmeneti**és **verziós** modelljeit. Minden csomag fel van szerelve.

|Csomag típusa|Végpont lekérdezése API|Lekérdezés elérhetősége|Csomagfájlnév formátuma|
|--|--|--|--|
|Verziószámozott|GET, POST|Csak tartályban|`{APP_ID}_v{APP_VERSION}.gz`|
|Előkészítés|GET, POST|Azure és tároló|`{APP_ID}_STAGING.gz`|
|Production|GET, POST|Azure és tároló|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Ne nevezze át, ne módosítsa, írja felül és ne bontsa ki a LUIS csomagfájlokat.

### <a name="packaging-prerequisites"></a>Csomagolási előfeltételek

A LUIS-alkalmazások csomagolása előtt a következőkre van szüksége:

|Csomagolási követelmények|Részletek|
|--|--|
|Azure _Cognitive Services_ erőforráspéldány|A támogatott régiók közé tartoznak a következők:<br><br>USA nyugati`westus`( )<br>Nyugat-Európa`westeurope`( )<br>Kelet -`australiaeast`Ausztrália Kelet ( )|
|Betanított vagy közzétett LUIS-alkalmazás|Nem [támogatott függőségek nélkül.][unsupported-dependencies] |
|Hozzáférés a [gazdaszámítógép](#the-host-computer)fájlrendszeréhez |A gazdaszámítógépnek engedélyeznie kell a [bemeneti csatlakoztatást](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Alkalmazáscsomag exportálása a LUIS portálról

A [LUIS-portál](https://www.luis.ai) lehetővé teszi a betanított vagy közzétett alkalmazáscsomag exportálását.

### <a name="export-published-apps-package-from-luis-portal"></a>Közzétett alkalmazáscsomag exportálása a LUIS portálról

A közzétett alkalmazáscsomag a **Saját alkalmazások** listoldalon érhető el. 

1. Jelentkezzen be a LUIS [portálra.](https://www.luis.ai)
1. Jelölje be a listában az alkalmazás nevének bal oldalán található jelölőnégyzetet. 
1. Válassza ki az **Elem exportálása** elemet a lista feletti környezetfüggő eszköztárból.
1. Válassza **az Exportálás tárolóhoz (GZIP)** lehetőséget.
1. Válassza ki az **éles tárolóhely** vagy az **átmeneti tárolóhely környezetét.**
1. A csomag letölthető a böngészőből.

![A közzétett csomag exportálása a tárolóhoz az Alkalmazáslap Exportálás menüjéből](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Verziózott alkalmazáscsomag exportálása a LUIS portálról

A verziózott alkalmazáscsomag a **Verziók** listalapról érhető el.

1. Jelentkezzen be a LUIS [portálra.](https://www.luis.ai)
1. Jelölje ki az alkalmazást a listában. 
1. Válassza a **Kezelés lehetőséget** az alkalmazás navigációs sávján.
1. Válassza a **Verziók** lehetőséget a bal oldali navigációs sávon.
1. Jelölje be a listában a verziónévtől balra lévő jelölőnégyzetet.
1. Válassza ki az **Elem exportálása** elemet a lista feletti környezetfüggő eszköztárból.
1. Válassza **az Exportálás tárolóhoz (GZIP)** lehetőséget.
1. A csomag letölthető a böngészőből.

![A betanított csomag exportálása a tárolóhoz a Verziók lap Exportálás menüjéből](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Közzétett alkalmazáscsomag exportálása API-ból

A következő REST API-módszerrel csomagoljon egy már [közzétett](luis-how-to-publish-app.md)LUIS-alkalmazást. Az API-hívás helyőrzőinek megfelelő értékei helyett a HTTP-specifikáció alatti táblázat ot használva.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Helyőrző | Érték |
|-------------|-------|
| **{APP_ID}** | A közzétett LUIS-alkalmazás alkalmazásazonosítója. |
| **{SLOT_NAME}** | A közzétett LUIS-alkalmazás környezete. Használja az alábbi értékek egyikét:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | A közzétett LUIS-alkalmazás LUIS-fiók szerzői kulcsa.<br/>A szerzői kulcsot a LUIS portál **Felhasználói beállítások** lapján szerezheti be. |
| **{AZURE_REGION}** | A megfelelő Azure-régió:<br/><br/>`westus`- USA nyugati<br/>`westeurope`- Nyugat-Európa<br/>`australiaeast`- Ausztrália Kelet |

A közzétett csomag letöltéséhez olvassa el az [API dokumentációját itt.][download-published-package] Ha a letöltés sikeresen megtörtént, a válasz egy LUIS-csomagfájl. Mentse a fájlt a tároló bemeneti csatlakoztatásához megadott tárolóhelyre. 

### <a name="export-versioned-apps-package-from-api"></a>Verziózott alkalmazáscsomag exportálása API-ból

A következő REST API-módszer rel a már [betanított](luis-how-to-train.md)LUIS-alkalmazások csomagolásához használható. Az API-hívás helyőrzőinek megfelelő értékei helyett a HTTP-specifikáció alatti táblázat ot használva.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Helyőrző | Érték |
|-------------|-------|
| **{APP_ID}** | A betanított LUIS-alkalmazás alkalmazásazonosítója. |
| **{APP_VERSION}** | A betanított LUIS-alkalmazás alkalmazásverziója. |
| **{AUTHORING_KEY}** | A közzétett LUIS-alkalmazás LUIS-fiók szerzői kulcsa.<br/>A szerzői kulcsot a LUIS portál **Felhasználói beállítások** lapján szerezheti be. |
| **{AZURE_REGION}** | A megfelelő Azure-régió:<br/><br/>`westus`- USA nyugati<br/>`westeurope`- Nyugat-Európa<br/>`australiaeast`- Ausztrália Kelet |

A verzióval elvégzett csomag letöltéséhez olvassa el az [API dokumentációját itt.][download-versioned-package] Ha a letöltés sikeresen megtörtént, a válasz egy LUIS-csomagfájl. Mentse a fájlt a tároló bemeneti csatlakoztatásához megadott tárolóhelyre. 

## <a name="run-the-container-with-docker-run"></a>Futtassa a tartályt`docker run`

Használja a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot a tároló futtatásához. Tekintse meg [a szükséges paraméterek összegyűjtését](#gathering-required-parameters) `{API_KEY}` a be- és értékek `{ENDPOINT_URI}` begyűjtésének részleteiről.

[Példák](luis-container-configuration.md#example-docker-run-commands) `docker run` a parancs állnak rendelkezésre.

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

* Ez a példa a `C:` meghajtón kívüli könyvtárat használja a Windows engedélyütközésének elkerülésére. Ha egy adott könyvtárat kell használnia bemeneti könyvtárként, előfordulhat, hogy meg kell adnia a docker-szolgáltatás engedélyét. 
* Ne módosítsa az argumentumok sorrendjét, kivéve, ha ismeri a docker-tárolók.
* Ha más operációs rendszert használ, használja a megfelelő konzolt/terminált, a mappa szintaxisát a csatlakoztatásokhoz, és a sor folytatási karakterét a rendszerhez. Ezek a példák egy sorfolytatási karakterrel rendelkező Windows-konzolt feltételeznek. `^` Mivel a tároló egy Linux operációs rendszer, a céltartó egy Linux-stílusú mappa szintaxist használ.

A parancs a következőket hajtja végre:

* Egy tárolót futtat a LUIS-tárolórendszerképből
* Betölti a LUIS-alkalmazást a bemeneti csatlakoztatásból a *C:\input*on , amely a tárológazdagépen található
* Két PROCESSZORmag és 4 gigabájt (GB) memória lefoglalása
* Kiteszi az 5000-es TCP-portot, és pszeudo-TTY-t rendel a tárolóhoz
* Tároló és A LUIS-naplók mentése a Tárológazdagépen található *C:\output*kimeneti csatlakoztatáshoz
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen. 

További [példák](luis-container-configuration.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre. 

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](#billing).
> Az ApiKey érték a **kulcs** az **Azure Resources** lap a LUIS `Cognitive Services` portálon, és az Azure erőforrás-kulcsok lapon is elérhető.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>A tároló által támogatott végponti API-k

Mind a [V2-es,](luis-migration-api-v3.md) mind a V3-as API-verziók elérhetők a tárolóval. 

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. A közzétett (átmeneti vagy éles) alkalmazások végpontjai _nak más_ útvonala van, mint a verziózott alkalmazások végpontjainak.

Használja az `http://localhost:5000`állomás, , tároló API-k.

# <a name="v3-prediction-endpoint"></a>[V3 előrejelzési végpont](#tab/v3)

|Csomag típusa|HTTP-parancs|Útválasztás|Lekérdezési paraméterek|
|--|--|--|--|
|Közzétett|GET, POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Verziószámozott|GET, POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

A lekérdezési paraméterek a lekérdezési válaszban megadott módszereket és adatokat konfigurálják:

|Lekérdezési paraméter|Típus|Cél|
|--|--|--|
|`query`|sztring|A felhasználó utterance (kifejezés).|
|`verbose`|logikai|Logikai érték, amely azt jelzi, hogy az előre jelzett modellek összes metaadatát adja-e vissza. Az alapértelmezett érték a false (hamis).|
|`log`|logikai|Naplózza a lekérdezéseket, amelyek később aktív [tanulásra](luis-how-to-review-endpoint-utterances.md)használhatók. Az alapértelmezett érték a false (hamis).|
|`show-all-intents`|logikai|Logikai érték, amely azt jelzi, hogy az összes szándékot vagy csak a legfelső pontozási szándékot adja-e vissza. Az alapértelmezett érték a false (hamis).|

# <a name="v2-prediction-endpoint"></a>[V2 előrejelzési végpont](#tab/v2)

|Csomag típusa|HTTP-parancs|Útválasztás|Lekérdezési paraméterek|
|--|--|--|--|
|Közzétett|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Verziószámozott|GET, POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

A lekérdezési paraméterek a lekérdezési válaszban megadott módszereket és adatokat konfigurálják:

|Lekérdezési paraméter|Típus|Cél|
|--|--|--|
|`q`|sztring|A felhasználó utterance (kifejezés).|
|`timezoneOffset`|szám|Az időzónaOffset lehetővé teszi az előre összeállított entitás datetimeV2 által használt [időzóna módosítását.](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)|
|`verbose`|logikai|Az összes szándékot és azok pontszámait adja vissza, ha igaz értékre van állítva. Az alapértelmezett érték hamis, amely csak a legfelső leképezést adja vissza.|
|`staging`|logikai|Az átmeneti környezet eredményeiből származó lekérdezést ad eredményül, ha értéke igaz. |
|`log`|logikai|Naplózza a lekérdezéseket, amelyek később aktív [tanulásra](luis-how-to-review-endpoint-utterances.md)használhatók. Alapértelmezett érték: true (igaz).|

***

### <a name="query-the-luis-app"></a>A LUIS alkalmazás lekérdezése

Példa a CURL parancsra egy közzétett alkalmazás tárolójának lekérdezésére:

# <a name="v3-prediction-endpoint"></a>[V3 előrejelzési végpont](#tab/v3)

Egy modell lekérdezéséhez egy tárolóhelyen, használja a következő API-t:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Ha lekérdezéseket **Staging** szeretne az `production` átmeneti környezetbe, cserélje le az útvonalon a következőre: `staging`

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Verziószámozott modell lekérdezéséhez használja a következő API-t:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[V2 előrejelzési végpont](#tab/v2)

Egy modell lekérdezéséhez egy tárolóhelyen, használja a következő API-t:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Ha lekérdezéseket szeretne készíteni az **átmeneti** környezetbe, módosítsa az **átmeneti** lekérdezési karakterlánc paraméterértékét igaz értékre: 

`staging=true`

Verziószámozott modell lekérdezéséhez használja a következő API-t:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
A verziónév legfeljebb 10 karakterből állhat, és csak az URL-ben engedélyezett karaktereket tartalmazza.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>A végpontnaplók importálása aktív tanuláshoz

Ha a LUIS-tárolóhoz meg van adva egy kimeneti csatlakoztatás, `{INSTANCE_ID}` az alkalmazáslekérdezési naplófájlok a kimeneti könyvtárba kerülnek, ahol a tárolóazonosító található. Az alkalmazáslekérdezési napló tartalmazza a lekérdezést, a választ és az időbélyegeket a LUIS-tárolóba küldött minden egyes előrejelzési lekérdezéshez. 

A következő helyen látható a tároló naplófájljainak beágyazott könyvtárszerkezete.
```
/output/luis/{INSTANCE_ID}/
```
 
A LUIS-portálon válassza ki az alkalmazást, majd válassza **a Végpontnaplók importálása** lehetőséget a naplók feltöltéséhez. 

![Tároló naplófájljainak importálása aktív tanuláshoz](./media/luis-container-how-to/upload-endpoint-log-files.png)

A napló feltöltése után [tekintse át a végpont](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) kimondott szövegét a LUIS-portálon.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

A tároló leállításához a tárolót futtató parancssori környezetben nyomja le a **Ctrl+C billentyűkombinációt.**

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót kimeneti [csatlakoztatással](luis-container-configuration.md#mount-settings) és naplózással futtatja, a tároló naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A LUIS-tároló számlázási adatokat küld az Azure-ba, az Azure-fiókjában lévő _Cognitive Services-erőforrás_ használatával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezekről a beállításokról a [Tárolók konfigurálása](luis-container-configuration.md)című témakörben talál további információt.

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben a luis-tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat ismertet. Összegezve:

* Language Understanding (LUIS) biztosít egy Linux-tároló docker endpont-lekérdezés-előrejelzések az utterances.
* A tárolóképek a Microsoft Container Registry (MCR) rendszerből töltődnek le.
* A tárolórendszerképek a Dockerben futnak.
* A REST API segítségével lekérdezheti a tároló végpontok a tároló gazdaURI-jának megadásával.
* A tároló példányosításakor meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
> A Cognitive Services-tárolók nem rendelkeznek licenccel anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. A Cognitive Services-tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Tekintse át [a Tárolók konfigurálása](luis-container-configuration.md) a konfigurációs beállításokat.
* Lásd: [LUIS-tároló korlátozások](luis-container-limitations.md) ismert képességkorlátozások.
* A LUIS funkcióval kapcsolatos problémák megoldásához olvassa el a [Hibaelhárítás](troubleshooting.md) című témakört.
* További [Cognitive Services-tárolók használata](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
