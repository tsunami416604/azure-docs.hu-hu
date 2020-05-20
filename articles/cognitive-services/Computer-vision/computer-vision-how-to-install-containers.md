---
title: Tárolók telepítése és futtatása – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision tárolóinak letöltése, telepítése és futtatása az útmutató oktatóanyagában.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: f1b6b9ffb53972aa81d6dd1150640b8db9dd7be9
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698545"
---
# <a name="install-and-run-read-containers-preview"></a>Olvasási tárolók telepítése és futtatása (előzetes verzió)

A tárolók lehetővé teszik a Computer Vision API-k futtatását a saját környezetében. A tárolók az adott biztonsági és adatirányítási követelményekhez is kiválóak. Ebből a cikkből megtudhatja, hogyan töltheti le, telepítheti és futtathatja Computer Vision tárolót.

Egyetlen Docker-tároló, *olvasás*, Computer Vision érhető el. Az *olvasási* tároló lehetővé teszi a *nyomtatott szövegek* észlelését és kinyerését különböző felületek és hátterek, például nyugták, plakátok és névjegykártyák képeiből. Az *olvasási* tároló emellett a *kézzel írt szövegeket* is észleli a képekben, és PDF-, TIFF-és többoldalas fájlok támogatását teszi lehetővé. További információ az API-k [olvasása](concept-recognizing-text.md#read-api) dokumentációban található.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók lemezképéről, valamint az alapszintű `docker` parancsokról.| 
|Erőforrás Computer Vision |A tároló használatához a következőket kell tennie:<br><br>Egy Azure **Computer Vision** erőforrás és a hozzá tartozó API-kulcs a végpont URI-ja. Mindkét érték elérhető az erőforrás áttekintés és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}**: a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}**: az **Áttekintés** lapon megadott végpont|

## <a name="request-access-to-the-private-container-registry"></a>Hozzáférés kérése a Private Container registryhez

Töltse ki és küldje el a [kérelem űrlapját](https://aka.ms/VisionContainersPreview) , hogy hozzáférést Kérjen a tárolóhoz. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Speciális vektoros bővítmény támogatása

A **gazdaszámítógép** a Docker-tárolót futtató számítógép. A gazdagépnek *támogatnia kell* a [speciális vektoros bővítményeket](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). A Linux-gazdagépek AVX2-támogatását a következő paranccsal tekintheti meg:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> A AVX2 támogatásához a gazdaszámítógép *szükséges* . A tároló *nem fog* megfelelően működni AVX2-támogatás nélkül.

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a`docker pull`

Az olvasáshoz tároló lemezképek érhetők el.

| Tároló | Container Registry/adattár/rendszerkép neve |
|-----------|------------|
| Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával töltse le a tárolók rendszerképét.

### <a name="docker-pull-for-the-read-container"></a>Docker-lekérés az olvasási tárolóhoz

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](computer-vision-resource-container-config.md) a `docker run` parancsra. 
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a`docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A és értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört `{ENDPOINT_URI}` `{API_KEY}` .

[Examples](computer-vision-resource-container-config.md#example-docker-run-commands) A parancs például `docker run` elérhető.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Futtatja az olvasási tárolót a tároló rendszerképből.
* 8 CPU-mag és 16 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

További [példák](./computer-vision-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra. 

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. 

A tároló API-k esetében használja a gazdagépet `http://localhost:5000` .

### <a name="asynchronous-read"></a>Aszinkron olvasás

A `POST /vision/v2.0/read/core/asyncBatchAnalyze` koncerten a és a `GET /vision/v2.0/read/operations/{operationId}` műveletek használatával aszinkron módon olvashat egy rendszerképet, hasonlóan ahhoz, ahogyan a Computer Vision szolgáltatás a megfelelő Rest-műveleteket használja. Az aszinkron POST metódus olyan értéket ad vissza, `operationId` amely a HTTP Get kérelem termékazonosító szolgál.

A hencegő felhasználói felületen válassza a elemet a `asyncBatchAnalyze` böngészőben való kibontáshoz. Ezután válassza a **kipróbálom**a  >  **fájl**elemet. Ebben a példában a következő képet fogjuk használni:

![tabulátorok és szóközök](media/tabs-vs-spaces.png)

Az aszinkron POST sikeres futtatása után egy **HTTP 202** állapotkódot ad vissza. A válasz részeként van egy `operation-location` fejléc, amely tartalmazza a kérelem eredmény-végpontját.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

A a `operation-location` teljes URL-cím, amely egy HTTP Get használatával érhető el. Itt látható az `operation-location` előző rendszerképből származó URL-cím végrehajtásának JSON-válasza:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Szinkron olvasás

A `POST /vision/v2.0/read/core/Analyze` műveletet a rendszerképek szinkron beolvasására is használhatja. Ha a képet a teljes egészében beolvasják, akkor az API-nak csak egy JSON-választ kell visszaadnia. Ez alól az egyetlen kivétel, ha hiba történik. Ha hiba történik, a rendszer a következő JSON-t adja vissza:

```json
{
    status: "Failed"
}
```

A JSON-válasz objektuma ugyanazzal az objektum-gráfmal rendelkezik, mint az aszinkron verzió. Ha Ön JavaScript-felhasználó, és meg szeretné adni a biztonságot, a következő típusokat használhatja a JSON-válasz objektumként való elküldéséhez `AnalyzeResult` .

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

Példa a használati esetekre: itt **Run** <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer"> <span class="docon docon-navigate-external x-hidden-focus"></span> láthatja az írógéppel Sandboxot</a> , és a Futtatás lehetőség kiválasztásával jelenítheti meg a könnyű használatot.

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti [csatlakoztatással](./computer-vision-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Cognitive Services tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókja megfelelő erőforrásának használatával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

További információ ezekről a beállításokról: [tárolók konfigurálása](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta Computer Vision tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A Computer Vision egy Linux-tárolót biztosít a Docker számára, és beolvassa a beágyazást.
* A Container images letölthető a "Container Preview" tároló-beállításjegyzékből az Azure-ban.
* A tároló lemezképei a Docker-ben futnak.
* A REST API vagy az SDK használatával a tároló gazdagép URI azonosítójának megadásával hívhat meg műveleteket az olvasási tárolókban.
* A tárolók létrehozásakor számlázási adatokat kell megadnia.

> [!IMPORTANT]
> Cognitive Services tárolók nem futtathatók az Azure-hoz való csatlakozás nélkül. Az ügyfeleknek engedélyeznie kell, hogy a tárolók a számlázási adatokat mindig a mérési szolgáltatással kommunikáljanak. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* A [tárolók konfigurálásának](computer-vision-resource-container-config.md) áttekintése konfigurációs beállításokhoz
* A nyomtatott és a kézírásos szöveg felismerésével kapcsolatos további információkért tekintse át [Computer Vision áttekintést](Home.md)
* A tároló által támogatott metódusokkal kapcsolatos részletekért tekintse meg a [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* A Computer Vision funkcióival kapcsolatos problémák megoldásához tekintse meg a [Gyakori kérdések (GYIK)](FAQ.md) című témakört.
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
