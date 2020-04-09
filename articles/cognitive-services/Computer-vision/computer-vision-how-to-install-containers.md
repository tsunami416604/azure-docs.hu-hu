---
title: Konténerek telepítése és futtatása - Computer Vision
titleSuffix: Azure Cognitive Services
description: A Számítógép-látás tárolók letöltése, telepítése és futtatása ebben a forgatókönyv-oktatóanyagban.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5f36c429041a8182551d1f077f0a1229f520e8c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879343"
---
# <a name="install-and-run-read-containers-preview"></a>Tárolók telepítése és futtatása (előzetes verzió)

A tárolók lehetővé teszik a Computer Vision API-k futtatását a saját környezetében. A tárolók kiválóan megfelelnek a speciális biztonsági és adatirányítási követelményeknek. Ebből a cikkből megtudhatja, hogyan tölthet le, telepíthet és futtathat computer vision tárolót.

Egyetlen Docker-tároló, *Read*, érhető el a Computer Vision. Az *Olvasás* tároló lehetővé teszi a különböző felületekkel és háttérrel rendelkező különböző objektumok, például nyugták, poszterek és névjegykártyák *képi képeiből* származó nyomtatott szöveg észlelését és kinyerését. Ezenkívül az *Olvasás* tároló észleli a *kézzel írt szöveget* a képeken, és PDF- és TIFF- és többoldalas támogatást biztosít. További információt az API [olvasása](concept-recognizing-text.md#read-api) dokumentációban talál.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

A tárolók használata előtt meg kell felelnie az alábbi előfeltételeknek:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motort egy [gazdaszámítógépen](#the-host-computer)kell telepíteni. A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy a tárolók csatlakozhassanak az Azure-hoz, és számlázási adatokat küldjenek az Azure-ba. <br><br> **Windows**rendszeren a Docker-t is be kell állítani linuxos tárolók támogatására.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a nyilvántartásokról, az adattárakról, a tárolókról és a tárolórendszerképekről, valamint az alapvető `docker` parancsok ismeretéről.| 
|Computer Vision forrás |A tároló használatához a következőkre van szüksége:<br><br>Egy Azure Computer Vision erőforrás és a kapcsolódó API-kulcs a végpont URI.An Azure **Computer Vision** resource and the associated API key the endpoint URI. Mindkét érték elérhető az áttekintés és a kulcsok oldalon az erőforrás, és szükséges a tároló elindításához.<br><br>**{API_KEY}**: A **Kulcsok** lapon elérhető két erőforráskulcs egyike<br><br>**{ENDPOINT_URI}**: Az **Áttekintés** lapon megadott végpont|

## <a name="request-access-to-the-private-container-registry"></a>Hozzáférés kérése a privát tároló beállításjegyzékéhez

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Speciális vektorbővítmény támogatása

A **gazdaszámítógép** a docker-tárolót futtató számítógép. Az *állomásnak támogatnia kell* [a speciális vektorbővítményeket](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Az AVX2-támogatás a Linux-állomásokon a következő paranccsal ellenőrizheti:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> A gazdaszámítógép az AVX2 támogatásához *szükséges.* A tartály *nem fog* megfelelően működni Az AVX2 támogatás nélkül.

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és ajánlások

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>A tárolókép beszerezése`docker pull`

Az olvasáshoz készült tárolórendszerképek elérhetők.

| Tároló | Konténer-nyilvántartás / Adattár / Képneve |
|-----------|------------|
| Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltsön le egy tárolólemezképet.

### <a name="docker-pull-for-the-read-container"></a>Docker-lekéréseaz olvasási tárolóhoz

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdaszámítógépen](#the-host-computer)van, használja a következő eljárást a tárolóval való munkához.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a szükséges számlázási beállításokkal. További [példák](computer-vision-resource-container-config.md) a `docker run` parancs állnak rendelkezésre. 
1. [Kérdezze meg a tároló előrejelzési végpontját.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Futtassa a tartályt`docker run`

Használja a [docker run](https://docs.docker.com/engine/reference/commandline/run/) parancsot a tároló futtatásához. Tekintse meg [a szükséges paraméterek összegyűjtését](#gathering-required-parameters) `{API_KEY}` a be- és értékek `{ENDPOINT_URI}` begyűjtésének részleteiről.

[Példák](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` a parancs állnak rendelkezésre.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Futtatja a tároló olvasása a tárolórendszerképből.
* 8 processzormag és 16 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi az 5000-es TCP-portot, és egy pszeudo-TTY-t rendel a tárolóhoz.
* A tároló automatikus eltávolítása kilépés után. A tárolórendszerkép továbbra is elérhető a gazdaszámítógépen.

További [példák](./computer-vision-resource-container-config.md#example-docker-run-commands) a `docker run` parancs állnak rendelkezésre. 

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. 

Használja az `http://localhost:5000`állomás, , tároló API-k.

### <a name="asynchronous-read"></a>Aszinkron olvasás

A és `GET /vision/v2.0/read/operations/{operationId}` `POST /vision/v2.0/read/core/asyncBatchAnalyze` a műveletek összehangoltan aszinkron olvasni egy képet, hasonlóan ahhoz, ahogy a Computer Vision szolgáltatás használja ezeket a megfelelő REST-műveleteket. Az aszinkron POST metódus visszaad egy `operationId` olyan módszert, amely a HTTP GET-kérelem azonosítójaként használatos.

A swagger felhasználói felületen `asyncBatchAnalyze` válassza ki a kibontásához a böngészőben. Ezután válassza **a Kipróbálás válassza ki** > **A fájl kiválasztása lehetőséget.** Ebben a példában a következő képet fogjuk használni:

![tabulátorok és szóközök](media/tabs-vs-spaces.png)

Ha az aszinkron POST sikeresen lefutott, **http 202** állapotkódot ad vissza. A válasz részeként van `operation-location` egy fejléc, amely rendelkezik a kérelem eredményvégpontját.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

A `operation-location` a teljesen minősített URL-t, és keresztül érhető el a HTTP GET. Itt van a JSON választ `operation-location` végrehajtó URL-t az előző kép:

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

A `POST /vision/v2.0/read/core/Analyze` művelet segítségével szinkron módon olvashat egy képet. Amikor a rendszerképet teljes egészében olvassa, majd, és csak ezután az API-t aD vissza egy JSON-választ. Ez alól csak akkor lehet kivételt tenni, ha hiba történik. Hiba esetén a következő JSON-értéket adja vissza:

```json
{
    status: "Failed"
}
```

A JSON-válaszobjektum objektuma ugyanazt az objektumdiagramot használja, mint az aszinkron verzió. Ha Ön JavaScript-felhasználó, és típusbiztonságot szeretne, a következő típusok használhatók a `AnalyzeResult` JSON-válasz objektumként történő leadására.

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

Egy használati eset például tekintse meg a <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">TypeScript sandbox-ot, <span class="docon docon-navigate-external x-hidden-focus"></span> </a> és válassza a **Futtatás** lehetőséget a könnyű használat megjelenítéséhez.

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót kimeneti [csatlakoztatással](./computer-vision-resource-container-config.md#mount-settings) és naplózással futtatja, a tároló naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Cognitive Services-tárolók számlázási adatokat küld az Azure-ba az Azure-fiókjában a megfelelő erőforrás használatával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezekről a beállításokról a [Tárolók konfigurálása](./computer-vision-resource-container-config.md)című témakörben talál további információt.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben a Computer Vision-tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat ismertet. Összegezve:

* A Computer Vision egy Linux-tárolót biztosít a Docker számára, amely beágyazta az olvasást.
* A tárolórendszerképek az Azure-ban a "Container Preview" tároló beállításjegyzékből töltődnek le.
* A tárolórendszerképek a Dockerben futnak.
* A REST API vagy az SDK segítségével a tárolók gazdaURI-jának megadásával hívhatja meg a műveleteket olvasási tárolókban.
* A tároló példányosításakor meg kell adnia a számlázási adatokat.

> [!IMPORTANT]
> A Cognitive Services-tárolók nem rendelkeznek licenccel anélkül, hogy az Azure-hoz csatlakoznának a méréshez. Az ügyfeleknek lehetővé kell tenni, hogy a tárolók mindig kommunikálják a számlázási adatokat a mérési szolgáltatással. A Cognitive Services-tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Tekintse át [a tárolók konfigurálása](computer-vision-resource-container-config.md) a konfigurációs beállításokat
* Tekintse át [a Computer Vision áttekintését,](Home.md) ha többet szeretne megtudni a nyomtatott és kézzel írt szöveg felismeréséről
* A tároló által támogatott módszerekről a [Computer Vision API-ban](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) talál részleteket.
* A Számítógép-látás funkcióval kapcsolatos problémák megoldásához olvassa el a [Gyakran ismételt kérdéseket (GYIK).](FAQ.md)
* További [Cognitive Services-tárolók használata](../cognitive-services-container-support.md)
