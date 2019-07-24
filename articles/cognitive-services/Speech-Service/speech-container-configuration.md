---
title: Beszédfelismerési tárolók konfigurálása
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési tároló
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: 8a8b0e18c1db7a2e2fc08819aa2f2d64d650ded6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321356"
---
# <a name="configure-speech-service-containers"></a>Beszédfelismerési szolgáltatás tárolóinak konfigurálása

A beszédfelismerési tárolók lehetővé teszik, hogy az ügyfelek egy olyan beszédfelismerési alkalmazás-architektúrát építsenek ki, amely a robusztus Felhőbeli képességek és a peremhálózat környékének kihasználására optimalizált. A jelenleg támogatott két beszédfelismerési tároló a **beszéd-szöveg** és a **szöveg-beszéd**. 

A **beszédfelismerési** tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tárolóra jellemző beállítások a számlázási beállítások. 

# <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie  a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott beszédfelismerési erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Beszéd** Erőforrás-kezelés a **kulcsok** területen

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás megadja az Azure-beli _beszédfelismerési_ erőforrás végponti URI-ját a tároló számlázási adatainak méréséhez. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nak  kell lennie az Azure-beli beszédfelismerési erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Beszéd** A címkével ellátott áttekintés`Endpoint`

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

## <a name="eula-setting"></a>Licencfeltételek beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

Kötés használatát csatlakoztatja az adatok olvasását és írását, és a tárolóból. Megadhat egy bemeneti csatlakoztatási vagy csatlakoztatási kimeneti megadásával a `--mount` beállítást a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot.

A beszédfelismerési tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](speech-container-howto.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket. 

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | A beszédfelismerési tárolók nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása 

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](speech-container-howto.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: A következő részben található Docker-parancsok a háttér perjelet `\`használják, mint a sor folytatási karaktere. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. 
* **Argumentumok sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{API_KEY} | A beszédfelismerési erőforrás API-kulcsa. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | A végpont értéke, beleértve a régiót.|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing-configuration-setting).
> A ApiKey értéke az Azure Speech Resource Keys oldalának **kulcsa** . 

## <a name="speech-container-docker-examples"></a>Beszédfelismerési tároló – példák

A következő Docker-példák a beszédfelismerési tárolóra vonatkoznak. 

### <a name="basic-example-for-speech-to-text"></a>Alapszintű példa a beszéd szövegére

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="basic-example-for-text-to-speech"></a>Alapszintű példa szövegről beszédre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Példa a beszéd szövegének naplózására

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>Példa a szöveg és a beszéd naplózására

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](speech-container-howto.md)