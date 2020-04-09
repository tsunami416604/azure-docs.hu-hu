---
title: Tárolók konfigurálása – Szövegelemzés
titleSuffix: Azure Cognitive Services
description: A Text Analytics minden tárolószámára biztosít egy közös konfigurációs keretrendszert, így könnyen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetriai adatait, valamint a tárolók biztonsági beállításait.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878670"
---
# <a name="configure-text-analytics-docker-containers"></a>Szövegelemzési docker-tárolók konfigurálása

A Text Analytics minden tárolószámára biztosít egy közös konfigurációs keretrendszert, így könnyen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetriai adatait, valamint a tárolók biztonsági beállításait.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) és a beállítások együtt vannak használva, és mindháromhoz érvényes értékeket kell megadnia; ellenkező esetben a tartály nem indul el. A tároló konstiniformatika érdekében ezeknek a konfigurációs beállításoknak a használatával kapcsolatos további [tudnivalókért olvassa el a Számlázás című témakört.](how-tos/text-analytics-how-to-install-containers.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítása

A `ApiKey` beállítás megadja az Azure erőforráskulcs a tároló számlázási adatainak nyomon követéséhez. Meg kell adnia egy értéket az ApiKey-hez, és az értéknek [`Billing`](#billing-configuration-setting) érvényes kulcsnak kell lennie a _konfigurációs_ beállításhoz megadott Text Analytics erőforráshoz.

Ez a beállítás a következő helyen található:

* Azure portal: **Szövegelemzéserőforrás-kezelés** a **Kulcsok** csoportban

## <a name="applicationinsights-setting"></a>ApplicationInsights beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfiguráció beállítása

A `Billing` beállítás adja meg a végpont URI a _Text Analytics_ erőforrás az Azure-ban használt a tároló számlázási adatainak mérésére. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végpontURI-nak kell lennie az Azure-beli _ Text Analytics-erőforráshoz._Text Analytics_ A tároló 10–15 percenként jelenti a használatot.

Ez a beállítás a következő helyen található:

* Azure portal: **Szövegelemzés** áttekintése, címkével`Endpoint`

|Kötelező| Név | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI. A számlázási URI beszerzésével kapcsolatos további információkért [lásd: A szükséges paraméterek összegyűjtése.](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](../cognitive-services-custom-subdomains.md) |

## <a name="eula-setting"></a>Eula beállítás

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gördülékeny beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http proxy hitelesítő adatok beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A kötéscsatlakoztatások segítségével adatokat olvashat és írhat a tárolóba és a tárolóból. Megadhatja a bemeneti csatlakoztatás vagy `--mount` kimeneti csatlakoztatás a beállítás megadásával a [docker futtatási](https://docs.docker.com/engine/reference/commandline/run/) parancs.

A Text Analytics-tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a betanítási vagy szolgáltatásadatok tárolására. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazdaoperációs rendszertől függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](how-tos/text-analytics-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el a docker-szolgáltatásfiók által használt engedélyek és a gazdagép csatlakoztatási helyének engedélyei közötti ütközés miatt. 

|Optional| Név | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | A Szövegelemzési tárolók nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a rönkök helye. Ez magában foglalja a tárolónaplókat is. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa a docker-futtatási parancsokra 

Az alábbi példák a konfigurációs beállításoksegítségével `docker run` szemléltetik a parancsok írását és használatát.  Futás után a tároló addig fut, amíg le nem [állítja.](how-tos/text-analytics-how-to-install-containers.md#stop-the-container)

* **Sor-folytatás karakter**: A következő szakaszokban található docker-parancsok a fordított perjelet `\`, sorfolytatási karakterként használják. Cserélje le vagy távolítsa el ezt a gazdaoperációs rendszer követelményei nek megfelelően. 
* **Argumentum sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem nagyon ismeri a docker-tárolókat.

Cserélje le {_argument_name_} -t a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az Azure `Text Analytics` Keys `Text Analytics` lapon elérhető erőforrás végpontkulcsa. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető `Text Analytics` az Azure Áttekintés oldalon.| Lásd: [a szükséges paraméterek összegyűjtése](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) explicit példákat. |

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](how-tos/text-analytics-how-to-install-containers.md#billing).
> Az ApiKey érték **Key** a kulcs `Text Analytics` az Azure Resource keys lapról. 

#### <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>További lépések

* Tekintse át [a tárolók telepítésének és futtatásának áttekintését](how-tos/text-analytics-how-to-install-containers.md)
* További [Cognitive Services-tárolók használata](../cognitive-services-container-support.md)
