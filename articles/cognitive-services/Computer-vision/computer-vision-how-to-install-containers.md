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
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: afccce5ca9101ed1e30f69264abae7ad85b4902b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564582"
---
# <a name="install-and-run-recognize-text-containers"></a>szövegfelismerés tárolók telepítése és futtatása

A Computer Vision ismeri fel a szöveg részét is egy Docker-tárolóként érhető el. Lehetővé teszi, és nyomtatott szöveg kinyerése a különféle objektumokat a különféle felületekkel és hátterek, például a visszaigazolások és poszterek vagy tájékoztató névjegykártyák képét.  
> [!IMPORTANT]
> A szöveg felismerése tároló jelenleg csak angol nyelven használható.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Szövegfelismerés tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)és [Linux](https://docs.docker.com/engine/installation/#supported-platforms)rendszereken. A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a `docker` tárolók lemezképéről, valamint az alapszintű parancsokról.| 
|Erőforrás Computer Vision |A tároló használatához a következőket kell tennie:<br><br>Egy Azure **Computer Vision** erőforrás és a hozzá tartozó API-kulcs a végpont URI-ja. Mindkét érték elérhető az erőforrás áttekintés és kulcsok oldalain, és a tároló indításához szükséges.<br><br>**{API_KEY}** : A **kulcsok** oldalon található két elérhető erőforrás-kulcs egyike<br><br>**{ENDPOINT_URI}** : Az **Áttekintés** oldalon megadott végpont|

## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat az egyes szövegfelismerés tárolók számára lefoglalható minimális és ajánlott CPU-magokat és memóriát ismerteti.

| Tároló | Minimális | Ajánlott |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|szövegfelismerés|1 mag, 8 GB memória, 0,5 TPS|2 mag, 8 GB memória, 1 TPS|0,5, 1|

* Minden mag legalább 2,6 gigahertz (GHz) vagy gyorsabb lehet.
* TPS – tranzakció/másodperc

Az alap és a memória a `--cpus` `docker run` parancs `--memory` részeként használt és beállításoknak felel meg.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a`docker pull`

A szövegfelismerés tároló lemezképei érhetők el. 

| Tároló | Tárház |
|-----------|------------|
|szövegfelismerés | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs használatával töltse le a tárolók rendszerképét.


### <a name="docker-pull-for-the-recognize-text-container"></a>Docker-lekérés a szövegfelismerés tárolóhoz

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a gazdagépen [](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run)a kötelező számlázási beállításokkal. További [példák](computer-vision-resource-container-config.md) a `docker run` parancsra. 
1. [A tároló előrejelzési végpontjának lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása a`docker run`

A tároló futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A parancs a következő paramétereket használja:

| Helyőrző | Value |
|-------------|-------|
|{API_KEY} | Ez a kulcs a tároló elindítására szolgál, és elérhető az Azure `Cognitive Services` Keys lapon.  |
|{ENDPOINT_URI} | A számlázási végpont URI-értéke. Példa:`https://westus.api.cognitive.microsoft.com/vision/v2.0`|

A következő BILLING_ENDPOINT_URI példában látható `vision/v2.0` módon hozzá kell adnia az útválasztást a végpont URI-hoz.

Cserélje le ezeket a paramétereket a saját értékeire a következő `docker run` példában szereplő parancsban.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Ez a parancs:

* Felismerő tároló futtatása a tároló rendszerképből
* Egy CPU mag és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen. 

További [példák](./computer-vision-resource-container-config.md#example-docker-run-commands) a `docker run` parancsra. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontjának lekérdezése

A tároló REST-alapú lekérdezés-előrejelzési végpont API-kat biztosít. 

A tároló API `http://localhost:5000`-k esetében használja a gazdagépet.

### <a name="asynchronous-text-recognition"></a>Aszinkron szövegek felismerése

Használhatja a `POST /vision/v2.0/recognizeText` és `GET /vision/v2.0/textOperations/*{id}*` műveletek aszinkron módon ismeri fel a képet, hogyan használja a Computer Vision service a megfelelő REST-műveletekhez hasonló nyomtatott szöveg egyeztetve. A szöveg felismerése tároló csak felismeri jelenleg nyomtatott szöveg, nem kézzel írt szöveg, ezért a `mode` a Computer Vision service műveletet a rendszer figyelmen kívül hagyja a a szöveg felismerése tároló megfelelően megadott paraméterrel.

### <a name="synchronous-text-recognition"></a>Szinkron szövegek felismerése

Használhatja a `POST /vision/v2.0/recognizeTextDirect` művelet szinkron ismeri fel a képet nyomtatott szöveg. Mivel ez a művelet szinkronban van, a művelethez tartozó kérelem törzse megegyezik `POST /vision/v2.0/recognizeText` a művelettel, de a művelethez tartozó válasz törzse megegyezik a `GET /vision/v2.0/textOperations/*{id}*` művelet által visszaadottal.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti csatlakoztatással [](./computer-vision-resource-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához. 


## <a name="billing"></a>Számlázás

A szövegfelismerés tárolók számlázási adatokat küldenek az Azure-nak az Azure-fiókjában lévő _szövegfelismerés_ -erőforrás használatával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta szövegfelismerés tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* A szövegfelismerés Linux-tárolót biztosít a Docker számára, és beágyazza a felismerés szövegét.
* Tárolórendszerképek letöltődnek az a Microsoft Container Registry (MCR) az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* A REST API vagy az SDK használatával meghívhatja a műveleteket szövegfelismerés tárolókban a tároló gazdagép URI azonosítójának megadásával.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services tárolók nem küldenek ügyféladatokat (például az elemzett képet vagy szöveget) a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](computer-vision-resource-container-config.md) a konfigurációs beállítások
* Felülvizsgálat [számítógépes Látástechnológiai áttekintése](Home.md) tudhat meg többet nyomtatott és kézzel írt szöveg felismerése  
* Tekintse meg a [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) részleteiről a tároló által támogatott különböző módszereit.
* Tekintse meg [– gyakori kérdések (GYIK)](FAQ.md) a Computer Vision funkcióihoz kapcsolódó problémák megoldása.
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
