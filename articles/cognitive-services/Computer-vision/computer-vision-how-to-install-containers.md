---
title: Hogyan telepítheti és futtathatja a tárolók – Computer Vision
titlesuffix: Azure Cognitive Services
description: Hogyan letöltése, telepítése és a Computer Vision-tárolókat futtathat az bemutató oktatóanyag.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 02/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a2c4f68a35d5baa3d7933243737d02d720409289
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671590"
---
# <a name="install-and-run-recognize-text-containers"></a>Telepítse és futtassa a szöveg felismerése tárolók

A Computer Vision ismeri fel a szöveg részét is egy Docker-tárolóként érhető el. Lehetővé teszi, és nyomtatott szöveg kinyerése a különféle objektumokat a különféle felületekkel és hátterek, például a visszaigazolások és poszterek vagy tájékoztató névjegykártyák képét.  
> [!IMPORTANT]
> A szöveg felismerése tároló jelenleg csak angol nyelven használható.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Szöveg felismerése tárolók használata előtt a következő előfeltételeknek kell megfelelnie:

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **A Windows**, a Docker Linux-tárolók támogatása is kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.| 
|Ismeri fel a szöveg erőforrás |A tároló használatához rendelkeznie kell:<br><br>A [ _szöveg felismerése_ ](vision-api-how-to-topics/howtosubscribe.md) Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket az Azure Portalon ismeri fel a szöveg áttekintése és a kulcsok oldalon érhető el, és a tároló indításához szükséges.<br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>A privát tárolóregisztrációs hozzáférés kérése

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>A számítógép

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott processzormagot és memóriát lefoglalni az egyes szöveg felismerése tárolók.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
|Szövegének felismerése|1 mag, 8 GB memória, 0,5 TPS|2 cores, 8 GB memory, 1 TPS|

Egyes maghoz kell lennie legalább 2.6-os gigahertz (GHz) vagy gyorsabb.

Core és a memória felel meg a `--cpus` és `--memory` beállítások, amelyek részeként használhatók a `docker run` parancsot.


## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép beolvasása `docker pull`

Szöveg ismeri fel a tárolórendszerképeket érhetők el. 

| Tároló | Adattár |
|-----------|------------|
|Szövegének felismerése | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Használja a [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal letöltheti egy tárolórendszerképet.


### <a name="docker-pull-for-the-recognize-text-container"></a>A szöveg felismerése tároló docker pull

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-with-docker-run), a szükséges a számlázás a beállításokat. További [példák](computer-vision-resource-container-config.md) , a `docker run` parancs érhetők el. 
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatásához `docker run`

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot a tároló futtatásához. A parancs paraméterei a következők:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot szolgál a tárolót, és az Azure Portalon ismeri fel a szöveg kulcsok lapján található.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI azonosítóját.|

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ezzel a paranccsal:

* A tároló rendszerképét felismerése tárolóban fut
* Több processzormaggal és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson. 

További [példák](./computer-vision-resource-container-config.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

A tároló REST-alapú lekérdezési előrejelzési végpontot API-kat biztosít. 

Használja a gazdagép https://localhost:5000, API-k tároló.

### <a name="asynchronous-text-recognition"></a>Aszinkron szövegek felismerése

Használhatja a `POST /vision/v2.0/recognizeText` és `GET /vision/v2.0/textOperations/*{id}*` műveletek aszinkron módon ismeri fel a képet, hogyan használja a Computer Vision service a megfelelő REST-műveletekhez hasonló nyomtatott szöveg egyeztetve. A szöveg felismerése tároló csak felismeri jelenleg nyomtatott szöveg, nem kézzel írt szöveg, ezért a `mode` a Computer Vision service műveletet a rendszer figyelmen kívül hagyja a a szöveg felismerése tároló megfelelően megadott paraméterrel.

### <a name="synchronous-text-recognition"></a>Szinkron szövegek felismerése

Használhatja a `POST /vision/v2.0/recognizeTextDirect` művelet szinkron ismeri fel a képet nyomtatott szöveg. Mivel ez a művelet szinkron, ehhez a művelethez a kérelem törzsében megegyezik a számára, amely a `POST /vision/v2.0/recognizeText` műveletet, de a válasz törzs esetében ez a művelet nem ugyanaz, mint amellyel által visszaadott a `GET /vision/v2.0/textOperations/*{id}*` műveletet.

## <a name="stop-the-container"></a>Állítsa le a tároló

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

A kimenet futtatásakor a tároló [csatlakoztatási](./computer-vision-resource-container-config.md#mount-settings) és naplózás engedélyezve van, a tárolót hoz létre a naplófájlokat, amelyek hasznosak a hibaelhárítás indítása, vagy a tároló futtatása közben történik. 

## <a name="containers-api-documentation"></a>Tároló API-dokumentáció

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Számlázás

Szöveg felismerése tárolók Küldés a számlázási adatokat az Azure-ba, a használatával egy _szöveg felismerése_ erőforrást az Azure-fiókjával. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](./computer-vision-resource-container-config.md).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a munkafolyamat letöltése, telepítése és a futó tárolók szöveg felismerése. Összegezve:

* Ismeri fel a szöveget egy Linux-tárolót biztosít a Docker, ESP szövegének felismerése.
* Tárolórendszerképek letöltődnek az a Microsoft Container Registry (MCR) az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val szöveg felismerése tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](computer-vision-resource-container-config.md) a konfigurációs beállítások
* Felülvizsgálat [számítógépes Látástechnológiai áttekintése](Home.md) tudhat meg többet nyomtatott és kézzel írt szöveg felismerése  
* Tekintse meg a [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) részleteiről a tároló által támogatott különböző módszereit.
* Tekintse meg [– gyakori kérdések (GYIK)](FAQ.md) a Computer Vision funkcióihoz kapcsolódó problémák megoldása.
* Több [Cognitive Services-tárolók](../cognitive-services-container-support.md)
