---
title: Tárolók – Computer Vision konfigurálása
titlesuffix: Azure Cognitive Services
description: A Computer Vision szöveg felismerése tárolók különböző beállításainak konfigurálása.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4785acacf4749ebddf362fa036b6f6a17cb0056b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868378"
---
# <a name="configure-recognize-text-docker-containers"></a>Ismeri fel a szöveg a Docker-tárolók konfigurálása

A **szöveg felismerése** tároló-futtatókörnyezet használatával lett konfigurálva a `docker run` parancssori argumentumokat. Ez a tároló rendelkezik néhány választható beállítások mellett számos szükséges beállításokat. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tároló-specifikus beállítások a számlázási beállításokat is. 

Tároló beállításai [hierarchikus](#hierarchical-settings) és állítható be [környezeti változók](#environment-variable-settings) vagy a docker [parancssori argumentumok](#command-line-argument-settings).

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](computer-vision-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a apikey tulajdonsággal végzett tesztelése és az értéknek kell lennie egy érvényes kulcsot a _Computer Vision_ megadott erőforrás a [ `Billing` ](#billing-setting) konfigurációs beállítás.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **Számítógépes Látástechnológia** erőforrás-kezelés alatt **kulcsok**

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg a végpont URI-t, a _Computer Vision_ erőforrást az Azure-ban használt mérni a tároló számlázási adatokat. Meg kell adnia egy értéket a konfigurációs beállítás, és az értéknek kell lennie egy érvényes végpont URI-t a egy _számítógépes Látástechnológiai_ erőforrást az Azure-ban.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **Számítógépes Látástechnológia** áttekintése, címkével `Endpoint`

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

A Computer Vision tárolók ne használja a bemeneti vagy kimeneti csatlakoztatja képzési vagy szolgáltatás adatok tárolására. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](computer-vision-how-to-install-containers.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a Docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket. 

|Optional| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | String | Számítógép Vision tárolók ez nem használható.|
|Optional| `Output` | String | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Hierarchikus beállításai

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása 

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](computer-vision-how-to-install-containers.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: Az alábbi szakaszok a Docker-parancsokat használhatja a fordított perjel `\`, egy sor folytatási karaktert. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. 
* **Argument sorrend**: Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a Docker-tárolókat.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{BILLING_KEY} | A végpont kulcs a Computer Vision erőforrás. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | A számlázási végpontértéknek többek között a régióban.|`https://westcentralus.api.cognitive.microsoft.com/vision/v1.0`|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](computer-vision-how-to-install-containers.md#billing).
> Apikey tulajdonsággal végzett tesztelése értéke a **kulcs** Azure számítógépes Látástechnológiai erőforrás kulcsok lapjáról. 

## <a name="recognize-text-container-docker-examples"></a>Ismeri fel a szöveg tároló Docker-példák

Az alábbi Docker-példák a felismerése szöveg tároló vannak. 

### <a name="basic-example"></a>Alapszintű példa 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Parancssori argumentumok naplózását példa

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>Naplózás példában a környezeti változó

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY}
  ```

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](computer-vision-how-to-install-containers.md)
