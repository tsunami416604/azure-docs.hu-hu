---
title: Tárolók konfigurálása – Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics az egyes tárolókat közös konfigurációs keretrendszerrel látja el, így egyszerűen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetria és biztonsági beállításait.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: f062fb2f3a653bc1b2845b92e373fdb67ba583d8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878670"
---
# <a name="configure-text-analytics-docker-containers"></a>Text Analytics Docker-tárolók konfigurálása

Text Analytics az egyes tárolókat közös konfigurációs keretrendszerrel látja el, így egyszerűen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetria és biztonsági beállításait.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)a és [`Eula`](#eula-setting) a beállításokat együtt használják, és mindhárom esetben érvényes értékeket kell megadnia. Ellenkező esetben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítás

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie _Text Analytics_ a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott Text Analytics erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **text Analytics** erőforrás-kezelés a **kulcsok** területen

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _text Analytics_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nak kell lennie egy __text Analytics_ erőforráshoz az Azure-ban. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **text Analytics** áttekintés, címkézett`Endpoint`

|Kötelező| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Adja meg a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást a `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsban található beállítás megadásával.

A Text Analytics tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Text Analytics tárolók nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra 

Az alábbi példák a konfigurációs beállítások segítségével szemléltetik a parancsok írását és használatát `docker run` .  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](how-tos/text-analytics-how-to-install-containers.md#stop-the-container) .

* **Vonal-folytatási karakter**: a következő részben lévő Docker-parancsok a háttér perjelet `\`használják, mint a sor folytatási karaktere. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján. 
* **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a {_argument_name_} értéket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az `Text Analytics` erőforrás Endpoint kulcsa elérhető az Azure `Text Analytics` Keys lapon. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `Text Analytics` Áttekintés oldalán.| Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) ismertető témakört. |

> [!IMPORTANT]
> A `Eula`tároló `Billing`futtatásához `ApiKey` meg kell adni a, a és a beállításokat. Ellenkező esetben a tároló nem indul el.  További információ: [számlázás](how-tos/text-analytics-how-to-install-containers.md#billing).
> A ApiKey értéke az Azure **Key** `Text Analytics` Resource Keys oldal kulcsa. 

#### <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>További lépések

* [A tárolók telepítésének és futtatásának](how-tos/text-analytics-how-to-install-containers.md) áttekintése
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
