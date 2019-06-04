---
title: Tároló - űrlap felismerő konfigurálása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja a képernyő felismerő tároló elemezni az űrlap és a táblák adatait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/31/2019
ms.author: pafarley
ms.openlocfilehash: 28acc2d1eafacb9e53fac3e3cce092738401f838
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475381"
---
# <a name="configure-form-recognizer-containers"></a>Űrlap felismerő tárolók konfigurálása

Űrlap felismerő tárolók egy stabil felhőalapú képességek és a peremhálózati helye kihasználásához optimalizált alkalmazásarchitektúra ügyfelek engedélyezése.

A **űrlap felismerő** tároló-futtatókörnyezet használatával lett konfigurálva a `docker run` parancssori argumentumokat. Ez a tároló rendelkezik néhány választható beállítások mellett számos szükséges beállításokat. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tároló-specifikus beállítások a számlázási beállításokat is.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a apikey tulajdonsággal végzett tesztelése és az értéknek kell lennie egy érvényes kulcsot a _űrlap felismerő_ megadott erőforrás a [ `Billing` ](#billing-configuration-setting) konfigurációs beállítás.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **Űrlap-felismerő a** erőforrás-kezelés alatt **kulcsok**

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg a végpont URI-t, a _űrlap felismerő_ erőforrást az Azure-ban használt mérni a tároló számlázási adatokat. Meg kell adnia egy értéket a konfigurációs beállítás, és az értéknek kell lennie egy érvényes végpont URI-t a egy _űrlap felismerő_ erőforrást az Azure-ban. A tároló használati jelentések kapcsolatos 10 – 15 percenként.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **Űrlap-felismerő a** áttekintése, címkével `Endpoint`

|Kötelező| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Licencfeltételek beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxybeállításai hitelesítő adatok

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Csatlakoztatási beállítások

Kötés használatát csatlakoztatja az adatok olvasását és írását, és a tárolóból. Megadhat egy bemeneti csatlakoztatási vagy csatlakoztatási kimeneti megadásával a `--mount` beállítást a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot.

A képernyő felismerő tároló egy bemeneti és kimeneti csatlakoztatási van szükség. A bemeneti csatlakoztatási lehet csak olvasható, és jelzi a tanítási és pontozási folyamatokat adatok eléréséhez szükséges. A kimeneti csatlakoztatási kell lennie a írható, és a modellek és az ideiglenes adatokat szeretne tárolni fogja.

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](form-recognizer-container-howto.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a Docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket.

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Kötelező| `Input` | String | A bemeneti csatlakoztatási célját. Az alapértelmezett érték `/input`.    <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Kötelező| `Output` | String | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`.  <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](form-recognizer-container-howto.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: Az alábbi szakaszok a Docker-parancsokat használhatja a fordított perjel `\`, egy sor folytatási karaktert. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján.
* **Argument sorrend**: Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a Docker-tárolókat.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot szolgál a tárolót, és az Azure Portalon űrlap felismerő kulcsok lapján található.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI azonosítóját az Azure Portalon az űrlap felismerő áttekintése oldalon érhető el.|
|{COMPUTER_VISION_API_KEY}| A kulcsot az Azure Portalon számítógép Vision API-kulcsok oldalon érhető el.|
|{COMPUTER_VISION_ENDPOINT_URI}|A számlázási végpont. Felhőalapú számítógépes Látástechnológiai erőforrás használja, ha az URI értéket érhető el az Azure Portalon számítógép Vision API – Áttekintés oldalon. Ha használ olyan `cognitive-services-recognize-text` tároló, használja a számlázási végpont URL-címe a tárolóhoz, az átadott a `docker run` parancsot.|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing-configuration-setting).
> Apikey tulajdonsággal végzett tesztelése értéke a **kulcs** Azure űrlap felismerő erőforrás kulcsok lapjáról.

## <a name="form-recognizer-container-docker-examples"></a>Űrlap-felismerő tároló Docker-példák

Az alábbi Docker-példák vannak az űrlap felismerő tároló.

### <a name="basic-example-for-form-recognizer"></a>Az űrlap felismerő alapszintű példa

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Az űrlap felismerő naplózási példa

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>További lépések

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](form-recognizer-container-howto.md)
