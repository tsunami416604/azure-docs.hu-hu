---
title: Tároló konfigurálása az űrlap-felismerőhöz
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja az űrlap-felismerő tárolót az űrlap-és a tábla-adatelemzéshez.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 4a490e8a9f111985df9c9e8c9f73bc36d686cc2a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348694"
---
# <a name="configure-form-recognizer-containers"></a>Űrlap-felismerő tárolók konfigurálása

Az Azure űrlap-felismerő tárolók használatával olyan alkalmazás-architektúrát építhet ki, amely a robusztus Felhőbeli képességek és a peremhálózat környékének kihasználására van optimalizálva.

Az űrlap-felismerő tároló futásidejű környezetét a `docker run` parancs argumentumai segítségével állíthatja be. Ehhez a tárolóhoz több szükséges beállítás és néhány választható beállítás is tartozik. Néhány példa a ["Docker Run commands"](#example-docker-run-commands) című szakaszban található. A tárolóra jellemző beállítások a számlázási beállítások.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)a [és`Eula`](#eula-setting) a beállításokat együtt használja a rendszer. Mindhárom beállításhoz érvényes értékeket kell megadnia; Ellenkező esetben a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. A ApiKey értékének érvényes kulcsnak kell lennie a "  számlázási konfigurációs beállítás" szakaszban megadott `Billing` , az űrlap-felismerő erőforrás számára.

Ez a beállítás a Azure Portalban, a **kulcsok**területen, az **űrlap-felismerő erőforrás-kezelés**szakaszban található.

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás megadja az Azure-beli _űrlap-felismerő_ erőforrás végpont-URI-ját, amely a tároló számlázási adatainak mérésére szolgál. A konfigurációs beállítás értékének érvényes végponti URI-nak kell lennie az Azure-beli _űrlap-felismerő_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a Azure Portalban, az űrlap- **felismerő áttekintés**területén, a **végpont**területen található.

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Licencfeltételek beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Csatlakoztatási beállítások

Kötés használatát csatlakoztatja az adatok olvasását és írását, és a tárolóból. Megadhat egy bemeneti csatlakoztatást vagy egy kimeneti csatlakoztatást a `--mount` [ `docker run` parancsban](https://docs.docker.com/engine/reference/commandline/run/)található beállítás megadásával.

Az űrlap-felismerő tárolóhoz bemeneti csatlakoztatás és kimeneti csatlakoztatás szükséges. A bemeneti csatlakoztatás csak olvasható lehet, és szükséges a betanításhoz és a pontozáshoz használt adatokhoz való hozzáféréshez. A kimeneti csatlakoztatásnak írhatónak kell lennie, és a modelleket és az ideiglenes adatokat a használatával tárolja.

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](form-recognizer-container-howto.md#the-host-computer) csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók engedélyei és a gazdagép csatlakoztatási helyének engedélyei ütköznek.

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Szükséges| `Input` | Sztring | A bemeneti csatlakoztatási célját. Az alapértelmezett érték `/input`.    <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Szükséges| `Output` | Karakterlánc | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`.  <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat. Ha fut, a tároló továbbra is futni fog, amíg [le nem állítja](form-recognizer-container-howto.md#stop-the-container).

* **Vonal-folytatási karakter**: A következő szakaszban található Docker-parancsok egy hátsó perjelet (\\) használnak a vonal folytatási karakterként. Cserélje ki vagy távolítsa el ezt a karaktert a gazdagép operációs rendszerének követelményeitől függően.
* **Argumentumok sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a {_argument_name_} értéket a következő táblázatba a saját értékeivel:

| Helyőrző | Érték |
|-------------|-------|
|{FORM_RECOGNIZER_API_KEY} | A tároló elindításához használt kulcs. Ez a Azure Portal űrlap-felismerési kulcsok lapon érhető el.  |
|{FORM_RECOGNIZER_ENDPOINT_URI} | A számlázási végpont URI-értéke a Azure Portal űrlap-felismerő eszköz áttekintés lapján érhető el.|
|{COMPUTER_VISION_API_KEY}| A kulcs a Azure Portal Computer Vision API kulcsok lapon érhető el.|
|{COMPUTER_VISION_ENDPOINT_URI}|A számlázási végpont. Ha felhőalapú Computer Vision-erőforrást használ, az URI-érték a Azure Portal Computer Vision API – áttekintés oldalon érhető el. Ha *kognitív-szolgáltatások-felismerő-Text* tárolót használ, használja a `docker run` parancsban szereplő tárolónak átadott számlázási végpont URL-címét.|

> [!IMPORTANT]
> A tároló futtatásához adja meg a `Eula`, `Billing`a és `ApiKey` a beállításokat; egyéb esetben a tároló nem indul el. További információkért lásd: [számlázási](#billing-configuration-setting).

> [!NOTE] 
> A ApiKey értéke az Azure Form felismerő erőforrás-kulcsok oldalának **kulcsa** .

## <a name="form-recognizer-container-docker-examples"></a>Űrlap-felismerő tárolók Docker-példák

A következő Docker-példák az űrlap-felismerő tárolóra vonatkoznak.

### <a name="basic-example-for-form-recognizer"></a>Az űrlap-felismerő alapszintű példája

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Naplózási példa az űrlap-felismerőhöz

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>További lépések

* Tekintse át [a tárolók telepítése és futtatása](form-recognizer-container-howto.md)című ismertetőt.
