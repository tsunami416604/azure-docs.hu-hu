---
title: Tárolók telepítése és futtatása – Computer Vision
titleSuffix: Azure Cognitive Services
description: Hogyan letöltése, telepítése és a Computer Vision-tárolókat futtathat az bemutató oktatóanyag.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 7c137572fadd07254343b7b4c34b5a63534b9d88
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936997"
---
# <a name="install-and-run-computer-vision-containers"></a>Computer Vision tárolók telepítése és futtatása

A tárolók lehetővé teszik a Computer Vision API-k futtatását a saját környezetében. A tárolók az adott biztonsági és adatirányítási követelményekhez is kiválóak. Ebből a cikkből megtudhatja, hogyan töltheti le, telepítheti és futtathatja Computer Vision tárolót.

Két Docker-tároló áll rendelkezésre a Computer Visionhoz: *Szövegfelismerés* és *olvasás*. A *szövegfelismerés* tároló lehetővé teszi a *nyomtatott szövegek* észlelését és kinyerését különböző felületekkel és hátterekkel, például nyugtákkal, poszterekkel és üzleti kártyákkal ellátott képekből. Az *olvasási* tároló, azonban: a a képeken található *kézírásos szövegeket* is észleli, és PDF/TIFF/többoldalas támogatást biztosít. További információ az API-k [olvasása](concept-recognizing-text.md#read-api) dokumentációban található.

> [!IMPORTANT]
> Az szövegfelismerés tároló az olvasási tároló mellett elavult. Az olvasási tároló a szövegfelismerés-tároló elődje, és a felhasználóknak az olvasási tároló használatára kell áttérniük. Mindkét tároló csak angol nyelven működik.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)és [Linux](https://docs.docker.com/engine/installation/#supported-platforms)rendszereken. A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a `docker` tárolók lemezképéről, valamint az alapszintű parancsokról.| 
|Erőforrás Computer Vision |A tároló használatához a következőket kell tennie:<br><br>Egy Azure **Computer Vision** erőforrás és a hozzá tartozó API-kulcs a végpont URI-ja. Mindkét érték elérhető az erőforrás áttekintés és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}** : A **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}** : Az **Áttekintés** oldalon megadott végpont|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a`docker pull`

# <a name="readtabread"></a>[Olvasás](#tab/read)

Az olvasáshoz tároló lemezképek érhetők el.

| Tároló | Container Registry/adattár/rendszerkép neve |
|-----------|------------|
| Olvasás | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

# <a name="recognize-texttabrecognize-text"></a>[Szövegének felismerése](#tab/recognize-text)

A szövegfelismerés tároló lemezképei érhetők el.

| Tároló | Container Registry/adattár/rendszerkép neve |
|-----------|------------|
| szövegfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

***

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával töltse le a tárolók rendszerképét.

# <a name="readtabread"></a>[Olvasás](#tab/read)

### <a name="docker-pull-for-the-read-container"></a>Docker-lekérés az olvasási tárolóhoz

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

# <a name="recognize-texttabrecognize-text"></a>[Szövegének felismerése](#tab/recognize-text)

### <a name="docker-pull-for-the-recognize-text-container"></a>Docker-lekérés a szövegfelismerés tárolóhoz

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer) található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](computer-vision-resource-container-config.md) a `docker run` parancsra. 
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a`docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A`{ENDPOINT_URI}` és`{API_KEY}` értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört.

A`docker run` parancs [például](computer-vision-resource-container-config.md#example-docker-run-commands) elérhető.

# <a name="readtabread"></a>[Olvasás](#tab/read)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Futtatja az olvasási tárolót a tároló rendszerképből.
* 8 CPU-mag és 16 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="recognize-texttabrecognize-text"></a>[Szövegének felismerése](#tab/recognize-text)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Futtatja az szövegfelismerés tárolót a tároló rendszerképből.
* 8 CPU-mag és 16 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

***

További [példák](./computer-vision-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. 

A tároló API `http://localhost:5000`-k esetében használja a gazdagépet.

# <a name="readtabread"></a>[Olvasás](#tab/read)

### <a name="asynchronous-read"></a>Aszinkron olvasás

A koncerten a `POST /vision/v2.0/read/core/asyncBatchAnalyze` és `GET /vision/v2.0/read/operations/{operationId}` a műveletek használatával aszinkron módon olvashat egy rendszerképet, hasonlóan ahhoz, ahogyan a Computer Vision szolgáltatás a megfelelő Rest-műveleteket használja. Az aszinkron post metódus olyan `operationId` értéket ad vissza, amely a HTTP Get kérelem termékazonosító szolgál.

A hencegő felhasználói felületen válassza a `asyncBatchAnalyze` elemet a böngészőben való kibontáshoz. Ezután válassza a **kipróbálom** > a**fájl**elemet. Ebben a példában a következő képet fogjuk használni:

![tabulátorok és szóközök](media/tabs-vs-spaces.png)

Az aszinkron POST sikeres futtatása után egy **HTTP 202** állapotkódot ad vissza. A válasz részeként van egy `operation-location` fejléc, amely tartalmazza a kérelem eredmény-végpontját.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

A `operation-location` a teljes URL-cím, amely egy HTTP Get használatával érhető el. Itt látható az előző rendszerképből származó `operation-location` URL-cím végrehajtásának JSON-válasza:

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
          "boundingBox": [
            56,
            39,
            317,
            50,
            313,
            134,
            53,
            123
          ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [
                90,
                43,
                243,
                53,
                243,
                123,
                94,
                125
              ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [
                259,
                55,
                313,
                62,
                313,
                122,
                259,
                123
              ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [
            221,
            148,
            417,
            146,
            417,
            206,
            227,
            218
          ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [
                230,
                148,
                416,
                141,
                419,
                211,
                232,
                218
              ],
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

A műveletet a `POST /vision/v2.0/read/core/Analyze` rendszerképek szinkron beolvasására is használhatja. Ha a képet a teljes egészében beolvasják, akkor az API-nak csak egy JSON-választ kell visszaadnia. Ez alól az egyetlen kivétel, ha hiba történik. Ha hiba történik, a rendszer a következő JSON-t adja vissza:

```json
{
    status: "Failed"
}
```

A JSON-válasz objektuma ugyanazzal az objektum-gráfmal rendelkezik, mint az aszinkron verzió. Ha Ön JavaScript-felhasználó, és meg szeretné adni a biztonságot, a következő típusokat használhatja a JSON-válasz `AnalyzeResult` objektumként való elküldéséhez.

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

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: string | null;
}
```

Példa a használati esetekre: Itt találhatja meg a [géppel használható Sandboxot](https://aka.ms/ts-read-api-types) , és a "Futtatás" lehetőség kiválasztásával jelenítheti meg a könnyű használatot.

# <a name="recognize-texttabrecognize-text"></a>[Szövegének felismerése](#tab/recognize-text)

### <a name="asynchronous-text-recognition"></a>Aszinkron szövegek felismerése

Használhatja a `POST /vision/v2.0/recognizeText` és `GET /vision/v2.0/textOperations/*{id}*` műveletek aszinkron módon ismeri fel a képet, hogyan használja a Computer Vision service a megfelelő REST-műveletekhez hasonló nyomtatott szöveg egyeztetve. A szöveg felismerése tároló csak felismeri jelenleg nyomtatott szöveg, nem kézzel írt szöveg, ezért a `mode` a Computer Vision service műveletet a rendszer figyelmen kívül hagyja a a szöveg felismerése tároló megfelelően megadott paraméterrel.

### <a name="synchronous-text-recognition"></a>Szinkron szövegek felismerése

Használhatja a `POST /vision/v2.0/recognizeTextDirect` művelet szinkron ismeri fel a képet nyomtatott szöveg. Mivel ez a művelet szinkronban van, a művelethez tartozó kérelem törzse megegyezik `POST /vision/v2.0/recognizeText` a művelettel, de a művelethez tartozó válasz törzse megegyezik a `GET /vision/v2.0/textOperations/*{id}*` művelet által visszaadottal.

***

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti [csatlakoztatással](./computer-vision-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Számlázás

A Cognitive Services tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókja megfelelő erőforrásának használatával.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és futtatása a Computer Vision tárolók munkafolyamatokat. Összegezve:

* A Computer Vision egy Linux-tárolót biztosít a Docker számára, amely a szövegfelismerés és az olvasást is magában foglalja.
* A Container images letölthető a "Container Preview" tároló-beállításjegyzékből az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy az SDK-t, hogy szövegfelismerés vagy olvasási tárolóban lévő műveleteket Hívjon meg a tároló gazda URI azonosítójának megadásával.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](computer-vision-resource-container-config.md) a konfigurációs beállítások
* Felülvizsgálat [számítógépes Látástechnológiai áttekintése](Home.md) tudhat meg többet nyomtatott és kézzel írt szöveg felismerése
* Tekintse meg a [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) részleteiről a tároló által támogatott különböző módszereit.
* Tekintse meg [– gyakori kérdések (GYIK)](FAQ.md) a Computer Vision funkcióihoz kapcsolódó problémák megoldása.
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
