---
title: Tároló konfigurálása az Anomália-detektor API-hoz
titleSuffix: Azure Cognitive Services
description: Az Anomália-detektor API-tároló futásidejű környezet a `docker run` parancs argumentumok használatával van konfigurálva. Ez a tároló számos szükséges beállítással rendelkezik, valamint néhány választható beállítással.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: f7e04a16fa35d492b8e5e6c53a05220e8b96a38a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795852"
---
# <a name="configure-anomaly-detector-containers"></a>Anomáliadetektor-tárolók konfigurálása

Az **Anomália-detektor** tároló futásidejű `docker run` környezet a parancs argumentumok használatával van konfigurálva. Ez a tároló számos szükséges beállítással rendelkezik, valamint néhány választható beállítással. A parancsra több [példa](#example-docker-run-commands) is elérhető. A tároló-specifikus beállítások a számlázási beállításokat. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokkal rendelkezik:

|Kötelező|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey (ApiKey)](#apikey-configuration-setting)|Számlázási adatok nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatás ának hozzáadását a tárolóhoz.|
|Igen|[Számlázás](#billing-configuration-setting)|Megadja a végpont URI-t az Azure-beli szolgáltatáserőforrás.|
|Igen|[Eula](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Napló és szükség esetén metrikaadatok írása fluentd kiszolgálóra.|
|Nem|[Http Proxy](#http-proxy-credentials-settings)|Http-proxy konfigurálása kimenő kérelmek hez.|
|Nem|[Naplózási](#logging-settings)|ASP.NET Core naplózási támogatást nyújt a tárolóhoz. |
|Nem|[Tartók](#mount-settings)|Adatok olvasása és írása a gazdaszámítógépről a tárolóra és a tárolóból vissza a gazdaszámítógépre.|

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) és a beállítások együtt vannak használva, és mindháromhoz érvényes értékeket kell megadnia; ellenkező esetben a tartály nem indul el. A tároló konstiniformatika érdekében ezeknek a konfigurációs beállításoknak a használatával kapcsolatos további [tudnivalókért olvassa el a Számlázás című témakört.](anomaly-detector-container-howto.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítása

A `ApiKey` beállítás megadja az Azure erőforráskulcs a tároló számlázási adatainak nyomon követéséhez. Meg kell adnia egy értéket az ApiKey-hez, és az értéknek érvényes kulcsnak kell lennie a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott _Anomália-detektor_ erőforráshoz.

Ez a beállítás a következő helyen található:

* Azure portal: **Anomália-érzékelő** erőforrás-kezelése a **Kulcsok** csoportban

## <a name="applicationinsights-setting"></a>ApplicationInsights beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfiguráció beállítása

A `Billing` beállítás adja meg az _Anomália-detektor_ erőforrás az Azure-ban használt mérő számlázási adatok a tároló. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végpontURI-nak kell lennie egy _Anomália-detektor_ erőforráshoz az Azure-ban.

Ez a beállítás a következő helyen található:

* Azure portal: **Anomáliadetektor áttekintése,** címkével`Endpoint`

|Kötelező| Név | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI. A számlázási URI beszerzésével kapcsolatos további információkért [lásd: A szükséges paraméterek összegyűjtése.](anomaly-detector-container-howto.md#gathering-required-parameters) További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](../cognitive-services-custom-subdomains.md) |

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

Az Anomáliadetektor-tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a betanítási vagy szolgáltatásadatok tárolására. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazdaoperációs rendszertől függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](anomaly-detector-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el a Docker-szolgáltatásfiók által használt engedélyek és a gazdagép csatlakoztatási helyének engedélyei közötti ütközés miatt. 

|Optional| Név | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Az anomáliadetektor-tárolók nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a rönkök helye. Ez magában foglalja a tárolónaplókat is. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa a docker-futtatási parancsokra 

Az alábbi példák a konfigurációs beállításoksegítségével `docker run` szemléltetik a parancsok írását és használatát.  Futás után a tároló addig fut, amíg le nem [állítja.](anomaly-detector-container-howto.md#stop-the-container)

* **Sorfolytatási karakter**: A Következő szakaszokban található Docker-parancsok a fordított perjelet használják, `\`a bash-rendszerhéj vonalfolytatási karaktereként. Cserélje le vagy távolítsa el ezt a gazdaoperációs rendszer követelményei nek megfelelően. Például, a vonal folytatása karakter ablakok `^`egy bunkó, . Cserélje ki a hátsó perjelet a rakatra. 
* **Argumentum sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem nagyon ismeri a Docker-tárolókat.

Érték cseréje zárójelben, `{}`a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás végpontkulcsa az `Anomaly Detector` `Anomaly Detector` Azure Keys lapon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető `Anomaly Detector` az Azure Áttekintés oldalon.| Lásd: [a szükséges paraméterek összegyűjtése](anomaly-detector-container-howto.md#gathering-required-parameters) explicit példákat. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](anomaly-detector-container-howto.md#billing).
> Az ApiKey érték a **kulcs** az Azure Anomália-érzékelő erőforrás kulcsok lap. 

## <a name="anomaly-detector-container-docker-examples"></a>Anomáliadetektor-tároló docker-példák

Az alábbi Docker-példák az Anomáliadetektor tároló. 

### <a name="basic-example"></a>Alapszintű példa 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Példa naplózása parancssori argumentumokkal

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>További lépések

* [Anomália-érzékelő tároló üzembe helyezése az Azure Container-példányokba](how-to/deploy-anomaly-detection-on-container-instances.md)
* [További információ az Anomáliadetektor API-szolgáltatásról](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
