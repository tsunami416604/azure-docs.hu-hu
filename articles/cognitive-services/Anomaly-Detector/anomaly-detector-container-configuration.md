---
title: Tároló konfigurálása anomália-detektor API-hoz
titleSuffix: Azure Cognitive Services
description: Az anomália-érzékelő API-tárolójának futtatókörnyezeti környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: f7e04a16fa35d492b8e5e6c53a05220e8b96a38a
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795852"
---
# <a name="configure-anomaly-detector-containers"></a>Anomáliadetektor-tárolók konfigurálása

Az **anomália detektor** tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. A parancshoz több [példa](#example-docker-run-commands) is rendelkezésre áll. A tárolóra jellemző beállítások a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokat tartalmaz:

|Kötelező|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-configuration-setting)|A számlázási információk nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetria támogatásának hozzáadását a tárolóhoz.|
|Igen|[Számlázás](#billing-configuration-setting)|Meghatározza a szolgáltatási erőforrás végpontjának URI-JÁT az Azure-ban.|
|Igen|[EULA](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Írási napló és opcionálisan metrikus adatok egy Fluent-kiszolgáló számára.|
|Nem|[Http-proxy](#http-proxy-credentials-settings)|HTTP-proxy konfigurálása kimenő kérések készítéséhez.|
|Nem|[Naplózás](#logging-settings)|ASP.NET Core naplózási támogatást biztosít a tárolóhoz. |
|Nem|[Csatlakoztatja](#mount-settings)|Adatok olvasása és írása a gazdagépről a tárolóba és a tárolóból a gazdagépre.|

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting)és [`Eula`](#eula-setting) beállításokat a rendszer együtt használja, és mindhárom esetben érvényes értékeket kell megadnia. Ellenkező esetben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítás

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott _anomália-detektor_ erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **anomália-detektor** erőforrás-kezelése a **kulcsok** területen

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _rendellenesség-Kiderítő_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _anomália-detektor_ erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **anomália-detektor** áttekintése, címkézett `Endpoint`

|Kötelező| Név | Data type | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](anomaly-detector-container-howto.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Adja meg a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást úgy, hogy megadja a `--mount` lehetőséget a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsban.

Az anomáliák Kiderítő tárolói nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](anomaly-detector-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Optional| Név | Data type | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Az anomália detektor tárolói nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra 

A következő példák a konfigurációs beállításokat használják a `docker run` parancsok írására és használatára.  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](anomaly-detector-container-howto.md#stop-the-container) .

* **Vonal-folytatási karakter**: a következő részben található Docker-parancsok a `\`a hátsó perjelet használják a bash-rendszerhéj vonalának folytatási karaktere. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján. A Windows vonal folytatási karaktere például egy kalap, `^`. Cserélje le a vissza perjelet a kalapra. 
* **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le az értéket zárójelben, `{}`a saját értékeivel:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az `Anomaly Detector` erőforrás Endpoint kulcsa az Azure `Anomaly Detector` kulcsok lapon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `Anomaly Detector` – áttekintés oldalon.| Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](anomaly-detector-container-howto.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A tároló futtatásához meg kell adni a `Eula`, `Billing`és `ApiKey` beállításokat. Ellenkező esetben a tároló nem indul el.  További információ: [számlázás](anomaly-detector-container-howto.md#billing).
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

### <a name="logging-example-with-command-line-arguments"></a>Példa a naplózásra parancssori argumentumokkal

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>További lépések

* [Rendellenesség-Kiderítő tároló üzembe helyezése Azure Container Instances](how-to/deploy-anomaly-detection-on-container-instances.md)
* [További információ az anomália-érzékelő API szolgáltatásáról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
