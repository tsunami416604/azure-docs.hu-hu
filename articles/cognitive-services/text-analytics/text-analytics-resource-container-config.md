---
title: Tárolók konfigurálása
titlesuffix: Text Analytics - Azure Cognitive Services
description: Szövegelemzés biztosít az egyes tárolók és a egy közös keretrendszer konfigurációs, egyszerűen konfigurálása és kezelése a storage, a naplózás és a telemetriai adatok és a biztonsági beállítások a tárolókhoz.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: dapine
ms.openlocfilehash: 2a9b9ce5109315d940d6dcadf395489612faddec
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356935"
---
# <a name="configure-text-analytics-docker-containers"></a>Text Analytics Docker-tárolók konfigurálása

Szövegelemzés biztosít az egyes tárolók és a egy közös keretrendszer konfigurációs, egyszerűen konfigurálása és kezelése a storage, a naplózás és a telemetriai adatok és a biztonsági beállítások a tárolókhoz.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie  a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott Text Analytics erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: Erőforrás-kezelés **text Analytics** a **kulcsok** területen

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _text Analytics_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nak kell lennie egy __text Analytics_ erőforráshoz az Azure-ban. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Text Analytics** A címkével ellátott áttekintés`Endpoint`

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A kötelező számlázási végpont URI-ja |

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

A Text Analytics tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket. 

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Karakterlánc | Text Analytics tárolók nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása 

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: A következő részben található Docker-parancsok a háttér perjelet `\`használják, mint a sor folytatási karaktere. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. 
* **Argumentumok sorrendje**: Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a docker-tárolókat.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{API_KEY} | Az `Text Analytics` erőforrás Endpoint kulcsa elérhető az Azure `Text Analytics` Keys lapon. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
|{ENDPOINT_URI} | A számlázási végpont értéke elérhető az Azure `Text Analytics` Áttekintés oldalán.|`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](how-tos/text-analytics-how-to-install-containers.md#billing).
> A ApiKey értéke az Azure  `Text Analytics` Resource Keys oldal kulcsa. 

## <a name="key-phrase-extraction-container-docker-examples"></a>A Key kifejezés kinyerő tárolók Docker-példái

A következő Docker-példák a kulcs kifejezés kinyerési tárolóra vonatkoznak. 

### <a name="basic-example"></a>Alapszintű példa 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Példa naplózásra 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/keyphrase Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="language-detection-container-docker-examples"></a>Nyelvfelismerés tárolók Docker-példák

A következő Docker-példák a nyelvfelismerés tárolóra vonatkoznak. 

### <a name="basic-example"></a>Alapszintű példa

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Példa naplózásra

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/language Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```
 
## <a name="sentiment-analysis-container-docker-examples"></a>Az érzelmek elemzése tároló Docker-példák

A következő Docker-példák az érzelmek elemzése tárolóra vonatkoznak. 

### <a name="basic-example"></a>Alapszintű példa

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Példa naplózásra

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing={ENDPOINT_URI} ApiKey={API_KEY} Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](how-tos/text-analytics-how-to-install-containers.md)
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
