---
title: Tárolók konfigurálása – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan konfigurálhatja a Computer Vision szövegfelismerés tárolók kötelező és választható beállításait.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 765001ae7380ff2e99e6b390930b94302ce506bf
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89433687"
---
# <a name="configure-computer-vision-docker-containers"></a>Computer Vision Docker-tárolók konfigurálása

A Computer Vision tároló futásidejű környezetét a `docker run` parancs argumentumai segítségével állíthatja be. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. A parancshoz több [példa](#example-docker-run-commands) is rendelkezésre áll. A tárolóra jellemző beállítások a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) , a [`Billing`](#billing-configuration-setting) és a [`Eula`](#eula-setting) beállítások együtt használhatók, és mindhárom esetben érvényes értékeket kell megadnia, máskülönben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](computer-vision-how-to-install-containers.md).

A tároló a következő tároló-specifikus konfigurációs beállításokkal is rendelkezik:

|Kötelező|Beállítás|Rendeltetés|
|--|--|--|
|No|ReadEngineConfig:ResultExpirationPeriod|Az eredmény lejárati ideje (óra). Az alapértelmezett érték 48 óra. A beállítás azt határozza meg, hogy a rendszeren Mikor kell törölni a felismerési eredményeket. Ha például a `resultExpirationPeriod=1` rendszer a folyamat után 1 órával törli a felismerés eredményét. Ha `resultExpirationPeriod=0` a rendszer törli az eredmény beolvasása után az elismerés eredményét.|
|No|Gyorsítótár: Redis|Lehetővé teszi az Redis tárolását az eredmények tárolásához. *Szükség* van gyorsítótárra, ha több olvasási tároló van elhelyezve egy terheléselosztó mögött.|
|No|Üzenetsor: RabbitMQ|Lehetővé teszi a RabbitMQ számára a feladatok elküldését. A beállítás akkor hasznos, ha több olvasási tároló van elhelyezve egy terheléselosztó mögött.|
|No|Tárolás::D ocumentStore:: MongoDB|Engedélyezi a MongoDB az állandó eredményű tároláshoz.|

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítás

A `ApiKey` beállítás megadja a `Cognitive Services` tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a konfigurációs beállításhoz megadott _Cognitive Services_ erőforráshoz [`Billing`](#billing-configuration-setting) .

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** erőforrás-kezelés a **kulcsok** területen

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _Cognitive Services_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _Cognitive Services_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** áttekintés, címkézett `Endpoint`

Ne felejtse el hozzáadni az `vision/v1.0` útválasztást a VÉGPONT URI-hoz az alábbi táblázatban látható módon. 

|Kötelező| Name | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI-ja<br><br>Példa:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Adja meg a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást a `--mount` [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsban található beállítás megadásával.

A Computer Vision tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](computer-vision-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Választható| Name | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Computer Vision tárolók nem használják ezt.|
|Választható| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra

Az alábbi példák a konfigurációs beállítások segítségével szemléltetik a parancsok írását és használatát `docker run` .  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](computer-vision-how-to-install-containers.md#stop-the-container) .

* **Vonal-folytatási karakter**: a következő részben lévő Docker-parancsok a háttér perjelet használják `\` , mint a sor folytatási karaktere. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján. 
* **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a {_argument_name_} értéket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás Endpoint kulcsa az `Computer Vision` Azure `Computer Vision` Keys oldalon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `Computer Vision` Áttekintés oldalán.| Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](computer-vision-how-to-install-containers.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` , a `Billing` és a `ApiKey` beállításokat meg kell adni a tároló futtatásához; egyéb esetben a tároló nem indul el.  További információ: [számlázás](computer-vision-how-to-install-containers.md#billing).
> A ApiKey értéke az Azure **Key** `Cognitive Services` Resource Keys oldal kulcsa.

## <a name="container-docker-examples"></a>Tárolók Docker-példák

A következő Docker-példák az olvasási tárolóra vonatkoznak.

### <a name="basic-example"></a>Alapszintű példa

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Példa naplózásra 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Következő lépések

* [A tárolók telepítésének és futtatásának](computer-vision-how-to-install-containers.md)áttekintése.
