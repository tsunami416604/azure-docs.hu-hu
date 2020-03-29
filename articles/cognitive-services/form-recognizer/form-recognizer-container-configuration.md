---
title: Tároló konfigurálása a Form Recognizer számára
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként konfigurálhatja az Űrlapfelismerő tárolót űrlap- és táblaadatok elemzésére.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 5439ec0c0aab5b8c127b651147e4b25d27c58390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379623"
---
# <a name="configure-form-recognizer-containers"></a>Űrlapfelismerő tárolók konfigurálása

Az Azure Form Recognizer tárolók használatával olyan alkalmazásarchitektúrát hozhat létre, amely a robusztus felhőbeli képességek és a peremhálózati helykihasználás előnyeit is kihasználja.

Az Űrlapfelismerő tároló futásidejű környezetét `docker run` a parancsargumentumok használatával konfigurálhatja. Ez a tároló számos szükséges és néhány választható beállítással rendelkezik. Néhány példa, tekintse meg a ["Példa docker parancsok futtatása"](#example-docker-run-commands) című szakaszban. A tároló-specifikus beállítások a számlázási beállításokat.

> [!IMPORTANT]
> A Form Recognizer tárolók jelenleg a Form Recognizer API 1.0-s verzióját használják. Az API legújabb verzióját a felügyelt szolgáltatás használatával érheti el.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) és a beállítások együtt használatosak. Mindhárom beállításhoz érvényes értékeket kell megadnia; ellenkező esetben a tároló nem indul el. A tároló konstiniformatika érdekében ezeknek a konfigurációs beállításoknak a használatával kapcsolatos további [tudnivalókért olvassa el a Számlázás című témakört.](form-recognizer-container-howto.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítása

A `ApiKey` beállítás megadja az Azure-erőforráskulcsot, amely a tároló számlázási adatainak nyomon követésére szolgál. Az ApiKey értékének érvényes kulcsnak kell lennie a "Számlázási `Billing` konfiguráció beállítása" szakaszban megadott _űrlapfelismerő_ erőforráshoz.

Ezt a beállítást az Azure Portalon, az **Űrlapfelismerő erőforrás-kezelése**csoportban, a **Kulcsok**csoportban találja.

## <a name="applicationinsights-setting"></a>ApplicationInsights beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfiguráció beállítása

A `Billing` beállítás határozza meg a végpont _URI-a formfelismerő_ erőforrás az Azure-ban, amely a tároló számlázási adatainak mérésére szolgál. Ennek a konfigurációs beállításnak az Azure-beli _űrlapfelismerő_ erőforrás érvényes végpontURI-értékének kell lennie. A tároló 10–15 percenként jelenti a használatot.

Ezt a beállítást az Azure Portalon, az **Űrlapfelismerő áttekintése** **csoportBan**találja.

|Kötelező| Név | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI. A számlázási URI beszerzésével kapcsolatos további információkért [lásd: A szükséges paraméterek összegyűjtése.](form-recognizer-container-howto.md#gathering-required-parameters) További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](../cognitive-services-custom-subdomains.md) |

## <a name="eula-setting"></a>Eula beállítás

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gördülékeny beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Csatlakoztatási beállítások

A kötéscsatlakoztatások segítségével adatokat olvashat és írhat a tárolóba és a tárolóból. A `--mount` [ `docker run` parancsban](https://docs.docker.com/engine/reference/commandline/run/)megadhatja a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást.

Az Űrlapfelismerő tárolóhoz bemeneti és kimeneti csatlakoztatás szükséges. A bemeneti csatlakoztató lehet csak olvasható, és ez szükséges a betanításhoz és pontozáshoz használt adatok eléréséhez. A kimeneti csatlakoztatásnak írhatónak kell lennie, és a modellek és az ideiglenes adatok tárolására használhatja.

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazdaoperációs rendszertől függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](form-recognizer-container-howto.md#the-host-computer) csatlakoztatási helye nem érhető el a Docker-szolgáltatásfiók engedélyei és az állomáscsatlakoztatási hely engedélyei közötti ütközés miatt.

|Optional| Név | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Kötelező| `Input` | Sztring | A bemeneti tartó célja. Az alapértelmezett érték `/input`.    <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Kötelező| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`.  <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa a docker-futtatási parancsokra

Az alábbi példák a konfigurációs beállításoksegítségével `docker run` szemléltetik a parancsok írását és használatát. Amikor fut, a tároló addig fut, amíg meg nem [állítja.](form-recognizer-container-howto.md#stop-the-container)

* **Sor-folytatás karakter**: A Docker-parancsok a következő\\szakaszokban egy fordított perjel ( ) a vonal folytatása karakter. Cserélje le vagy távolítsa el ezt a karaktert, a gazdaoperációs rendszer követelményeitől függően.
* **Argumentum sorrendje:** Ne módosítsa az argumentumok sorrendjét, hanem ismeri a Docker-tárolók.

Cserélje le a következő táblázatban szereplő {_argument_name_} elemet a saját értékeire:

| Helyőrző | Érték |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | A kulcs, amivel a tároló tanusította. Az Azure Portal űrlapfelismerő kulcsok lapon érhető el. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | A számlázási végpont URI-értéke az Azure Portal űrlapfelismerő áttekintése lapon érhető el.|
| **{COMPUTER_VISION_API_KEY}** | A kulcs az Azure Portal Computer Vision API Keys lapon érhető el.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | A számlázási végpont. Ha felhőalapú Computer Vision erőforrást használ, az URI-érték elérhető az Azure PortalComputer Vision API áttekintése oldalon. Ha egy *cognitive-services-recognize-text* tárolót használ, használja a számlázási végpont URL-jét, amely átlett a tárolóak a `docker run` parancsban. |

Lásd [a szükséges paraméterek összegyűjtését](form-recognizer-container-howto.md#gathering-required-parameters) az értékek beszerzésének részleteiért.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A tároló futtatásához `Eula`adja `Billing`meg `ApiKey` a , és a beállításokat; ellenkező esetben a tároló nem indul el. További információ: [Billing](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Űrlapfelismerő tároló Docker-példák

Az alábbi Docker-példák az űrlapfelismerő tárolóra szolgálnak.

### <a name="basic-example-for-form-recognizer"></a>Alappélda az űrlapfelismerőszámára

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

### <a name="logging-example-for-form-recognizer"></a>Példa az Űrlapfelismerő naplózására

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

* Tekintse át [a Tárolók telepítése és futtatása című témakört.](form-recognizer-container-howto.md)
