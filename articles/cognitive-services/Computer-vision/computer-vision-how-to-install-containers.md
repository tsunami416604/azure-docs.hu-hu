---
title: Telepítse az OCR Docker-tárolók beolvasása Computer Vision
titleSuffix: Azure Cognitive Services
description: Használja az OCR Docker-tárolók beolvasása a Computer Visionból a szöveg kinyeréséhez a képekből és a douments a helyszínen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: helyszíni, OCR, Docker, tároló
ms.openlocfilehash: acf6a391965dcba20a2dabc18648076b88c5e7c5
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536375"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Az OCR Docker-tárolók beolvasása (előzetes verzió) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

A tárolók lehetővé teszik a Computer Vision API-k a saját környezetében való futtatását. A tárolók kiválóan alkalmasak adott biztonsági és adatszabályozási követelményekhez. Ebből a cikkből megtudhatja, hogyan töltheti le, telepítheti és futtathatja Computer Vision tárolókat.

Az *olvasási* OCR-tároló lehetővé teszi a nyomtatott és a kézírásos szöveg kinyerését képekből és dokumentumokból JPEG-, PNG-, BMP-, PDF-és TIFF-fájlformátumok támogatásával. További információ az API-k [olvasása dokumentációban](concept-recognizing-text.md#read-api)található.

## <a name="read-3x-containers"></a>3. x tároló olvasása
A 3. x tároló két verziója érhető el előzetes verzióban. Mindkét verzió további pontosságot és funkciókat biztosít az előző tárolóban.

A Read 3,0 – Preview tároló a következőket biztosítja:
* Új modellek a nagyobb pontosság érdekében.
* Több nyelv támogatása ugyanazon a dokumentumon belül
* Támogatás: holland, angol, francia, német, olasz, portugál és spanyol.
* Egyetlen művelet a dokumentumok és a lemezképek esetében.
* Nagyobb méretű dokumentumok és rendszerképek támogatása.
* 0 és 1 közötti megbízhatósági pontszámok.
* A nyomtatott és a kézzel írott szöveggel rendelkező dokumentumok támogatása

A Read 3,1 – Preview tároló ugyanazokat az előnyöket biztosítja, mint a v 3.0 – előzetes verzió, további funkciókkal:

* Támogatás egyszerűsített kínai és Japán nyelvekhez.
* a nyomtatott és a kézírásos szöveg megbízhatósági pontszámai és feliratai. 
* Lehetőség szöveg kinyerésére csak a dokumentum kiválasztott oldaláról.

A használni kívánt tároló verziójának meghatározásakor vegye figyelembe, hogy a v 3.1 – Preview korábbi előzetes verzióban érhető el. Ha jelenleg olvasási 2,0-tárolót használ, tekintse meg az [áttelepítési útmutatót](read-container-migration-guide.md) az új verziók változásainak megismeréséhez.

## <a name="prerequisites"></a>Előfeltételek

A tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

|Kötelező|Szerep|
|--|--|
|A Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszereken. A Docker és a tárolók alapszintű ismertetéséért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/docker-overview/).<br><br> A Docker-t úgy kell konfigurálni, hogy lehetővé tegye a tárolók számára az Azure-ba való kapcsolódást és a számlázási információk küldését. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók lemezképéről, valamint az alapszintű `docker` parancsokról.| 
|Erőforrás Computer Vision |A tároló használatához a következőket kell tennie:<br><br>Egy Azure **Computer Vision** erőforrás és a hozzá tartozó API-kulcs a végpont URI-ja. Mindkét érték elérhető az erőforrás áttekintés és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}**: a **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}**: az **Áttekintés** lapon megadott végpont|

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/), mielőtt hozzákezd.

## <a name="request-approval-to-run-the-container"></a>Kérelem jóváhagyása a tároló futtatásához

Töltse ki és küldje el a [kérelem űrlapját](https://aka.ms/csgate) a tároló futtatásához jóváhagyás kéréséhez. 

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

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a `docker pull`

Az olvasáshoz tároló lemezképek érhetők el.

| Tároló | Container Registry/adattár/rendszerkép neve |
|-----------|------------|
| Olvasás 2,0 – előzetes verzió | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Olvasás 3,0 – előzetes verzió | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| Olvasás 3,1 – előzetes verzió | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával töltse le a tárolók rendszerképét.

### <a name="docker-pull-for-the-read-container"></a>Docker-lekérés az olvasási tárolóhoz

# <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

# <a name="version-20-preview"></a>[2,0-es verzió – előzetes verzió](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](computer-vision-resource-container-config.md) a `docker run` parancsra. 
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a `docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A és értékek beszerzésével kapcsolatos részletekért tekintse meg a [szükséges paraméterek összegyűjtését](#gathering-required-parameters) ismertető témakört `{ENDPOINT_URI}` `{API_KEY}` .

[Examples](computer-vision-resource-container-config.md#example-docker-run-commands) A parancs például `docker run` elérhető.

# <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Futtatja az olvasási tárolót a tároló rendszerképből.
* 8 CPU-mag és 18 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

A parancs a következőket hajtja végre:

* Futtatja az olvasási tárolót a tároló rendszerképből.
* 8 CPU-mag és 18 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

# <a name="version-20-preview"></a>[2,0-es verzió – előzetes verzió](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

A parancs a következőket hajtja végre:

* Futtatja az olvasási tárolót a tároló rendszerképből.
* 8 CPU-mag és 16 gigabájt (GB) memóriát foglal le.
* Elérhetővé teszi a 5000-es TCP-portot, és egy pszeudo-TTY-t foglal le a tárolóhoz.
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.

---


További [példák](./computer-vision-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra. 

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](#billing).

Ha nagyobb átviteli sebességre van szüksége (például többoldalas fájlok feldolgozásakor), érdemes több v 3.0 vagy v 3.1 tárolót telepíteni [egy Kubernetes-fürtre](deploy-computer-vision-on-premises.md)az [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-create) és az [Azure üzenetsor](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction)használatával.

Ha az Azure Storage-t használja a lemezképek feldolgozásra történő tárolására, létrehozhat egy, a tároló meghívásakor használandó [kapcsolódási karakterláncot](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) .

A kapcsolódási karakterlánc megkeresése:

1. Navigáljon a Azure Portal **Storage-fiókjaihoz** , és keresse meg a fiókját.
2. A bal oldali navigációs listában kattintson a **hozzáférési kulcsok** elemre.
3. A kapcsolatok karakterlánca a **kapcsolatok karakterlánca** alatt lesz.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat nyújt. 

# <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

A tároló API-khoz használja a gazdagépet (`http://localhost:5000`). A hencegő útvonalat a következő helyen tekintheti meg: `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` .

# <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

A tároló API-khoz használja a gazdagépet (`http://localhost:5000`). A hencegő útvonalat a következő helyen tekintheti meg: `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[2,0-es verzió – előzetes verzió](#tab/version-2)

A tároló API-khoz használja a gazdagépet (`http://localhost:5000`). A hencegő útvonalat a következő helyen tekintheti meg: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Aszinkron olvasás


# <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

A `POST /vision/v3.1/read/analyze` koncerten a és a `GET /vision/v3.1/read/operations/{operationId}` műveletek használatával aszinkron módon olvashat egy rendszerképet, hasonlóan ahhoz, ahogyan a Computer Vision szolgáltatás a megfelelő Rest-műveleteket használja. Az aszinkron POST metódus olyan értéket ad vissza, `operationId` amely a HTTP Get kérelem termékazonosító szolgál.


A hencegő felhasználói felületen válassza a elemet a `asyncBatchAnalyze` böngészőben való kibontáshoz. Ezután válassza a **kipróbálom**a  >  **fájl**elemet. Ebben a példában a következő képet fogjuk használni:

![tabulátorok és szóközök](media/tabs-vs-spaces.png)

Az aszinkron POST sikeres futtatása után egy **HTTP 202** állapotkódot ad vissza. A válasz részeként van egy `operation-location` fejléc, amely tartalmazza a kérelem eredmény-végpontját.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

A a `operation-location` teljes URL-cím, amely egy HTTP Get használatával érhető el. Itt látható az `operation-location` előző rendszerképből származó URL-cím végrehajtásának JSON-válasza:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

A `POST /vision/v3.0/read/analyze` koncerten a és a `GET /vision/v3.0/read/operations/{operationId}` műveletek használatával aszinkron módon olvashat egy rendszerképet, hasonlóan ahhoz, ahogyan a Computer Vision szolgáltatás a megfelelő Rest-műveleteket használja. Az aszinkron POST metódus olyan értéket ad vissza, `operationId` amely a HTTP Get kérelem termékazonosító szolgál.

A hencegő felhasználói felületen válassza a elemet a `asyncBatchAnalyze` böngészőben való kibontáshoz. Ezután válassza a **kipróbálom**a  >  **fájl**elemet. Ebben a példában a következő képet fogjuk használni:

![tabulátorok és szóközök](media/tabs-vs-spaces.png)

Az aszinkron POST sikeres futtatása után egy **HTTP 202** állapotkódot ad vissza. A válasz részeként van egy `operation-location` fejléc, amely tartalmazza a kérelem eredmény-végpontját.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

A a `operation-location` teljes URL-cím, amely egy HTTP Get használatával érhető el. Itt látható az `operation-location` előző rendszerképből származó URL-cím végrehajtásának JSON-válasza:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[2,0-es verzió – előzetes verzió](#tab/version-2)

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

---

> [!IMPORTANT]
> Ha több olvasási tárolót helyez üzembe egy terheléselosztó mögött, például a Docker-összeállítás vagy a Kubernetes alatt, külső gyorsítótárral kell rendelkeznie. Mivel előfordulhat, hogy a feldolgozó tároló és a GET kérelem tárolója nem azonos, a külső gyorsítótár tárolja az eredményeket, és megosztja őket a tárolók között. A gyorsítótár-beállításokkal kapcsolatos további információkért lásd: [Computer Vision Docker-tárolók konfigurálása](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).

### <a name="synchronous-read"></a>Szinkron olvasás

A következő művelettel lehet szinkronban olvasni egy rendszerképet. 

# <a name="version-31-preview"></a>[3,1-es verzió – előzetes verzió](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

`POST /vision/v3.0/read/syncAnalyze`

# <a name="version-20-preview"></a>[2,0-es verzió – előzetes verzió](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Ha a képet a teljes egészében beolvasják, akkor az API-nak csak egy JSON-választ kell visszaadnia. Ez alól az egyetlen kivétel, ha hiba történik. Ha hiba történik, a rendszer a következő JSON-t adja vissza:

```json
{
    "status": "Failed"
}
```

A JSON-válasz objektuma ugyanazzal az objektum-gráfmal rendelkezik, mint az aszinkron verzió. Ha Ön JavaScript-felhasználó, és szeretné beírni a biztonságot, érdemes lehet az írógéppel használni a JSON-válasz elküldéséhez.

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
* A nyomtatott és a kézírásos szöveg felismerésével kapcsolatos további információkért tekintse át [Computer Vision áttekintést](overview.md)
* A tároló által támogatott metódusokkal kapcsolatos részletekért tekintse meg a [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .
* A Computer Vision funkcióival kapcsolatos problémák megoldásához tekintse meg a [Gyakori kérdések (GYIK)](FAQ.md) című témakört.
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
