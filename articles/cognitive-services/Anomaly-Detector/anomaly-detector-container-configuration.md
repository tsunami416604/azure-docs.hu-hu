---
title: Tároló konfigurálása anomália-detektor API-hoz
titleSuffix: Azure Cognitive Services
description: Az anomália érzékelő API-tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 617a8fc823b7c40d047e5825dc31b095da132f29
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321455"
---
# <a name="configure-anomaly-detector-containers"></a>Rendellenesség-Kiderítő tárolók konfigurálása

Az **anomália detektor** tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tárolóra jellemző beállítások a számlázási beállítások. 

# <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokkal rendelkezik:

|Szükséges|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-configuration-setting)|Számlázási adatok nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba.|
|Igen|[Billing](#billing-configuration-setting)|Az Azure-ban található szolgáltatás-erőforrás végponti URI-ját adja meg.|
|Igen|[Eula](#eula-setting)| Azt jelzi, hogy Ön már elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Napló írási és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.|
|Nem|[Http-proxy](#http-proxy-credentials-settings)|HTTP-proxy konfigurálása kimenő kérések készítéséhez.|
|Nem|[Logging](#logging-settings)|Biztosítja a naplózás a tároló támogatása ASP.NET Core. |
|Nem|[Fluentd](#mount-settings)|Napló- és opcionálisan metrikaadatok írása egy Fluentd-kiszolgálóra.|

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie  a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott anomália-detektor erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Anomália detektor** Erőforrás-kezelés a **kulcsok** területen

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás megadja az Azure-beli _rendellenesség_ -Kiderítő erőforrás végponti URI-ját a tároló számlázási adatainak méréséhez. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli anomália- _detektor_ erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Anomália detektor** A címkével ellátott áttekintés`Endpoint`

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

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

Az anomáliák Kiderítő tárolói nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](anomaly-detector-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Karakterlánc | Az anomália detektor tárolói nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása 

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](anomaly-detector-container-howto.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: A következő részben található Docker-parancsok a hátsó perjelet `\`használják, mint a bash-rendszerhéj vonalának folytatási karaktere. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. Például a Windows vonal folytatási karaktere egy kalap `^`. Cserélje le a vissza perjelet a kalapra. 
* **Argumentumok sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a zárójelben `{}`lévő értéket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{API_KEY} | Az anomália-detektor erőforrásának Endpoint kulcsa. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | A számlázási végpont értéke, beleértve a régiót.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](anomaly-detector-container-howto.md#billing).
> A ApiKey értéke az Azure-beli anomáliák Detektorának erőforrás-kulcsai oldalának **kulcsa** . 

## <a name="anomaly-detector-container-docker-examples"></a>Anomáliák detektor Container Docker-példák

A következő Docker-példák az anomália detektor tárolóhoz tartoznak. 

### <a name="basic-example"></a>Alapszintű példa 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Parancssori argumentumok naplózását példa

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
