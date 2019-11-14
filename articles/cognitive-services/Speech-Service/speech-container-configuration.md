---
title: Beszédfelismerési tárolók konfigurálása
titleSuffix: Azure Cognitive Services
description: A Speech Services minden tárolót közös konfigurációs keretrendszerrel biztosít, így egyszerűen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetria és biztonsági beállításait.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: f68bf989202c209f89ea273fee8d7610a49415ed
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075738"
---
# <a name="configure-speech-service-containers"></a>Beszédfelismerési szolgáltatás tárolóinak konfigurálása

A beszédfelismerési tárolók lehetővé teszik, hogy az ügyfelek egy olyan beszédfelismerési alkalmazás-architektúrát építsenek ki, amely a robusztus Felhőbeli képességek és a peremhálózat környékének kihasználására optimalizált. Az általunk támogatott négy beszédes tároló, a **beszéd-szöveg**, az **Egyéni beszéd-** szöveg, a **szöveg és a beszéd**, valamint az **egyéni szöveg-beszéd**.

A **beszédfelismerési** tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tárolóra jellemző beállítások a számlázási beállítások.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott _beszédfelismerési_ erőforráshoz.

Ez a beállítás a következő helyen érhető el:

- Azure Portal: a **beszédfelismerés** erőforrás-kezelése a **kulcsok** területen

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _beszédfelismerési_ erőforrás végponti URI-ját a tároló számlázási adatainak méréséhez. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nak kell lennie az Azure-beli _beszédfelismerési_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

- Azure Portal: a **beszédfelismerés** áttekintése, címkézett `Endpoint`

| Kötelező | Name (Név) | Data type | Leírás |
| -------- | ---- | --------- | ----------- |
| Igen | `Billing` | Sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](speech-container-howto.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Licencfeltételek beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

Kötés használatát csatlakoztatja az adatok olvasását és írását, és a tárolóból. Megadhat egy bemeneti csatlakoztatási vagy csatlakoztatási kimeneti megadásával a `--mount` beállítást a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot.

A normál beszédfelismerési tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolására. Az egyéni beszédfelismerési tárolók azonban a kötetek csatlakoztatására támaszkodnak.

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](speech-container-howto.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket.

| Optional | Name (Név) | Data type | Leírás |
| -------- | ---- | --------- | ----------- |
| Nem engedélyezett | `Input` | Sztring | A normál beszédfelismerési tárolók nem használják ezt. Az egyéni beszédfelismerési tárolók a [mennyiségi csatlakoztatásokat](#volume-mount-settings)használják.                                                                                    |
| Optional | `Output` | Sztring | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Kötet csatlakoztatási beállításai

Az egyéni beszédfelismerési tárolók a [mennyiségi csatlakoztatásokat](https://docs.docker.com/storage/volumes/) használják az egyéni modellek megtartására. Megadhat egy kötet csatlakoztatását úgy, hogy hozzáadja a `-v` (vagy `--volume`) lehetőséget a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancshoz.

Az egyéni modelleket a rendszer az első alkalommal tölti le, amikor új modellt töltenek be az egyéni Speech Container Docker Run parancs részeként. Egy egyéni beszédfelismerési tároló egyazon `ModelId` szekvenciális futtatása a korábban letöltött modellt fogja használni. Ha nincs megadva a kötet csatlakoztatása, az egyéni modellek nem maradhatnak meg.

A kötet csatlakoztatási beállítása három színes `:` tagolt mezőből áll:

1. Az első mező a számítógép kötetének neve, például _C:\input_.
2. A második mező a tároló könyvtára, például _/usr/local/models_.
3. A harmadik mező (opcionális) a beállítások vesszővel tagolt listája, további információ: [kötetek használata](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Kötet csatlakoztatása – példa

```bash
-v C:\input:/usr/local/models
```

Ez a parancs a gazdagépek _C:\input_ könyvtárat csatlakoztatja a tárolók _/usr/local/models_ könyvtárához.

> [!IMPORTANT]
> A mennyiségi csatlakoztatási beállítások csak a **Custom Speech szövegre** és az **egyéni szöveg-beszéd** tárolóra vonatkoznak. A normál **beszéd – szöveg** és szöveg – **beszéd** tárolók nem használnak mennyiségi csatlakoztatást.

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat. Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](speech-container-howto.md#stop-the-container) azt.

- **Vonal-folytatási karakter**: a következő szakaszban lévő Docker-parancsok a háttér perjelet (`\`) használják vonal-folytatási karakterként. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján.
- **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Az `Speech` erőforrás Endpoint kulcsa az Azure `Speech` kulcsok lapon.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `Speech` – áttekintés oldalon. | Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](speech-container-howto.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el. További információkért lásd: [számlázási](#billing-configuration-setting).
> A ApiKey értéke az Azure Speech Resource Keys oldalának **kulcsa** .

## <a name="speech-container-docker-examples"></a>Beszédfelismerési tároló – példák

A következő Docker-példák a beszédfelismerési tárolóra vonatkoznak.

## <a name="speech-to-texttabstt"></a>[Beszédfelismerés](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Alapszintű példa a beszédfelismerési szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Naplózási példa a beszédfelismerési szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-texttabcstt"></a>[Custom Speech – szöveg](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Alapszintű példa Custom Speech-szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Naplózási példa Custom Speech-szövegre

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speechtabtss"></a>[Szövegfelolvasás](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Alapszintű példa szövegről beszédre

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Naplózási példa szöveg – beszéd

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speechtabctts"></a>[Egyéni szöveg – beszéd](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Alapszintű példa egyéni szöveg-beszéd átalakításra

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Példa naplózásra egyéni szöveg – beszéd

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [telepítéséről és a tárolókat futtatják](speech-container-howto.md)
