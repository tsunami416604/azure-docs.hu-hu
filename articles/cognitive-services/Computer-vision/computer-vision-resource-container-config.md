---
title: Tárolók konfigurálása – Computer Vision
titleSuffix: Azure Cognitive Services
description: Szövegfelismerés tárolók különböző beállításainak konfigurálása Computer Visionban.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 3e1dc68ec67e8a7a24c3459519df80a8faf2fc01
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565658"
---
# <a name="configure-recognize-text-docker-containers"></a>szövegfelismerés Docker-tárolók konfigurálása

A **szövegfelismerés** tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tárolóra jellemző beállítások a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás megadja a tároló `Cognitive Services` számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie  a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott Cognitive Services erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** Erőforrás-kezelés a **kulcsok** területen

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _Cognitive Services_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _Cognitive Services_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** A címkével ellátott áttekintés`Endpoint`

Ne felejtse el `vision/v1.0` hozzáadni az útválasztást a végpont URI-hoz az alábbi táblázatban látható módon. 

|Kötelező| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

A Computer Vision tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](computer-vision-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Választható| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Computer Vision tárolók nem használják ezt.|
|Választható| `Output` | Karakterlánc | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása 

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](computer-vision-how-to-install-containers.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: A következő részben található Docker-parancsok a háttér perjelet `\`használják, mint a sor folytatási karaktere. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. 
* **Argumentumok sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Ne felejtse el `vision/v1.0` hozzáadni az útválasztást a végpont URI-hoz az alábbi táblázatban látható módon. 

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{API_KEY} | A Cognitive Services erőforrás Endpoint kulcsa. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | A számlázási végpont értéke, beleértve a régiót.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](computer-vision-how-to-install-containers.md#billing).
> A ApiKey értéke az Azure  `Cognitive Services` Resource Keys oldal kulcsa. 

## <a name="recognize-text-container-docker-examples"></a>A szöveges tároló Docker-példák felismerése

A következő Docker-példák a felismerhető szöveg tárolóra vonatkoznak. 

### <a name="basic-example"></a>Alapszintű példa 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Példa naplózásra 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](computer-vision-how-to-install-containers.md)
