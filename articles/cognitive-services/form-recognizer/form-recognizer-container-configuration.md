---
title: Tároló - űrlap felismerő konfigurálása
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan konfigurálhatja a képernyő felismerő tároló elemezni az űrlap és a táblák adatait.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7e8e7a13cd02a6f3b109a84829dba2a81fd36aaa
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296234"
---
# <a name="configure-form-recognizer-containers"></a>Űrlap felismerő tárolók konfigurálása

Az Azure-űrlap felismerő tárolók használatával hozhat létre egy stabil felhőalapú képességek és a peremhálózati helye kihasználásához optimalizált architektúra.

A képernyő felismerő tároló futtatási környezet segítségével konfigurálhatja a `docker run` parancssori argumentumokat. Ez a tároló rendelkezik több szükséges beállításokat, és néhány nem kötelező beállítások. Néhány példákért lásd a ["Példa docker-parancsok futtatása"](#example-docker-run-commands) szakaszban. A tároló-specifikus beállítások a számlázási beállításokat is.

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja. Mindhárom beállítás; meg kell adnia az érvényes értékek Ellenkező esetben a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. A apikey tulajdonsággal végzett tesztelése értéke az érvényes kulcsot kell lennie a _űrlap felismerő_ megadott erőforrás `Billing` a "konfigurációs beállítás számlázást (Billing) szakaszban.

Ezt a beállítást, az Azure Portalon, az annak **űrlap felismerő erőforrás-kezelés**alatt **kulcsok**.

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg a végpont URI-t, a _űrlap felismerő_ erőforrás, amellyel mérni a számlázási adatokat tároló Azure-ban. A konfigurációs beállítás értéke egy érvényes végpont URI-t kell lennie a egy _űrlap felismerő_ erőforrást az Azure-ban. A tároló használati jelentések kapcsolatos 10 – 15 percenként.

Ezt a beállítást, az Azure Portalon, az annak **űrlap felismerő áttekintése**alatt **végpont**.

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

Kötés használatát csatlakoztatja az adatok olvasását és írását, és a tárolóból. Megadásával megadhat egy bemeneti csatlakoztatási vagy egy kimeneti csatlakoztatási a `--mount` beállítást a [ `docker run` parancs](https://docs.docker.com/engine/reference/commandline/run/).

A képernyő felismerő tároló egy bemeneti csatlakoztatási és a egy kimeneti csatlakoztatási van szükség. A bemeneti csatlakoztatási lehet csak olvasható, és ez a tanítási és pontozási folyamatokat használt adatok hozzáférés szükséges. A kimeneti csatlakoztatási írhatók legyenek rendelkezik, és a modellek és az ideiglenes adatok tárolásához használja.

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a csatlakoztatási helye a [gazdaszámítógép](form-recognizer-container-howto.md#the-host-computer) előfordulhat, hogy nem érhető el a Docker szolgáltatás fiók és a gazdagép csatlakozási helyet engedélyeinek közötti ütközés miatt.

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Kötelező| `Input` | String | A bemeneti csatlakoztatási célját. Az alapértelmezett érték `/input`.    <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Kötelező| `Output` | String | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`.  <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat. Ha fut, a tároló továbbra is fut, amíg ki nem [leállíthassák](form-recognizer-container-howto.md#stop-the-container).

* **Vonal-folytatási karakter**: Az alábbi szakaszok a Docker-parancsokat használja egy fordított perjel (\\) sor folytatási karakterként. Cserélje le, vagy távolítsa el ezt a karaktert, attól függően, a gazdagép operációs rendszerre vonatkozó követelmények.
* **Argument sorrend**: Ne módosítsa az argumentumok sorrendje, kivéve, ha ismeri a Docker-tárolókat.

Cserélje le a(z)_argument_name_} a következő táblázatban a saját értékeire:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | A kulcsot, amellyel a tárolót. Az Azure Portalon űrlap felismerő kulcsok lapján érhető el.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI azonosítóját az Azure Portalon űrlap felismerő áttekintése oldalon érhető el.|
|{COMPUTER_VISION_API_KEY}| A kulcsot az Azure Portalon számítógép Vision API-kulcsok lapon érhető el.|
|{COMPUTER_VISION_ENDPOINT_URI}|A számlázási végpont. Ha egy felhőalapú számítógépes Látástechnológiai erőforrást használ, az Azure Portalon számítógép Vision API – áttekintés oldalra az URI értéket érhető el. Ha használ egy *cognitive services-felismerni-szöveges* tárolót, használja a számlázási végpont URL-címe a tárolóhoz, az átadott a `docker run` parancsot.|

> [!IMPORTANT]
> A tároló futtatásához adja meg a `Eula`, `Billing`, és `ApiKey` beállítások; ellenkező esetben a tároló nem indul el. További információkért lásd: [számlázási](#billing-configuration-setting).

> [!NOTE] 
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

* Felülvizsgálat [telepítése és futtatása tárolók](form-recognizer-container-howto.md).
