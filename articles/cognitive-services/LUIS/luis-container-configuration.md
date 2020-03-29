---
title: Docker-tároló beállításai – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS-tároló futásidejű környezete a `docker run` parancsargumentumok használatával van konfigurálva. A LUIS számos szükséges beállítással rendelkezik, valamint néhány választható beállítással.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a30fcd0ec7e53c78876596baf787639e81c638db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795013"
---
# <a name="configure-language-understanding-docker-containers"></a>Nyelvi megértési Docker-tárolók konfigurálása 

A **Language Understanding** (LUIS) tároló futásidejű `docker run` környezet a parancsargumentumok használatával van konfigurálva. A LUIS számos szükséges beállítással rendelkezik, valamint néhány választható beállítással. A parancsra több [példa](#example-docker-run-commands) is elérhető. A tároló-specifikus beállítások a [bemeneti csatlakoztatási beállítások](#mount-settings) és a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokkal rendelkezik:

|Kötelező|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey (ApiKey)](#apikey-setting)|Számlázási adatok nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatás ának hozzáadását a tárolóhoz.|
|Igen|[Számlázás](#billing-setting)|Megadja a végpont URI-t az Azure-beli szolgáltatáserőforrás.|
|Igen|[Eula](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Napló és szükség esetén metrikaadatok írása fluentd kiszolgálóra.|
|Nem|[Http Proxy](#http-proxy-credentials-settings)|Http-proxy konfigurálása kimenő kérelmek hez.|
|Nem|[Naplózási](#logging-settings)|ASP.NET Core naplózási támogatást nyújt a tárolóhoz. |
|Igen|[Tartók](#mount-settings)|Adatok olvasása és írása a gazdaszámítógépről a tárolóra és a tárolóból vissza a gazdaszámítógépre.|

> [!IMPORTANT]
> A [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting), [`Eula`](#eula-setting) és a beállítások együtt vannak használva, és mindháromhoz érvényes értékeket kell megadnia; ellenkező esetben a tartály nem indul el. A tároló konstiniformatika érdekében ezeknek a konfigurációs beállításoknak a használatával kapcsolatos további [tudnivalókért olvassa el a Számlázás című témakört.](luis-container-howto.md#billing)

## <a name="apikey-setting"></a>ApiKey beállítás

A `ApiKey` beállítás megadja az Azure erőforráskulcs a tároló számlázási adatainak nyomon követéséhez. Meg kell adnia egy értéket az ApiKey és az érték érvényes kulcsnak [`Billing`](#billing-setting) kell lennie a konfigurációs beállításhoz megadott Cognitive Services-erőforráshoz. _Cognitive Services_

Ez a beállítás a következő helyeken található:

* Azure portal: **Cognitive Services** Erőforrás-kezelés, **a Kulcsok** csoportban
* LUIS-portál: **Kulcsok és végpontbeállítások** lap. 

Ne használja az indítókulcsot vagy a szerzői kulcsot. 

## <a name="applicationinsights-setting"></a>ApplicationInsights beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Számlázási beállítás

A `Billing` beállítás adja meg a végpont URI a _Cognitive Services-erőforrás_ az Azure-ban a tároló számlázási adatainak mérésére használt. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végpontURI-nak kell lennie az _Azure-beli Cognitive_ Services-erőforráshoz. A tároló 10–15 percenként jelenti a használatot.

Ez a beállítás a következő helyeken található:

* Azure portal: **Kognitív szolgáltatások** áttekintése, címkével`Endpoint`
* LUIS-portál: **Kulcsok és végpont beállítások** lap, a végpont URI részeként.

| Kötelező | Név | Adattípus | Leírás |
|----------|------|-----------|-------------|
| Igen      | `Billing` | sztring | Számlázási végpont URI. A számlázási URI beszerzésével kapcsolatos további információkért [lásd: A szükséges paraméterek összegyűjtése.](luis-container-howto.md#gathering-required-parameters) További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](../cognitive-services-custom-subdomains.md) |

## <a name="eula-setting"></a>Eula beállítás

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Gördülékeny beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A kötéscsatlakoztatások segítségével adatokat olvashat és írhat a tárolóba és a tárolóból. Megadhatja a bemeneti csatlakoztatás vagy `--mount` kimeneti csatlakoztatás a beállítás megadásával a [docker futtatási](https://docs.docker.com/engine/reference/commandline/run/) parancs. 

A LUIS-tároló nem használ bemeneti vagy kimeneti csatlakoztatásokat a betanítási vagy szolgáltatásadatok tárolására. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazdaoperációs rendszertől függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](luis-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el a docker-szolgáltatásfiók által használt engedélyek és a gazdagép csatlakoztatási helyének engedélyei közötti ütközés miatt. 

Az alábbi táblázat a támogatott beállításokat ismerteti.

|Kötelező| Név | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Igen| `Input` | Sztring | A bemeneti tartó célja. Az alapértelmezett érték `/input`. Ez a LUIS-csomagfájlok helye. <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nem| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a rönkök helye. Ez magában foglalja a LUIS lekérdezési naplók és a tároló naplók. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa a docker-futtatási parancsokra

Az alábbi példák a konfigurációs beállításoksegítségével `docker run` szemléltetik a parancsok írását és használatát.  Futás után a tároló addig fut, amíg le nem [állítja.](luis-container-howto.md#stop-the-container)

* Ezek a példák `C:` a meghajtón kívüli könyvtárat használják a Windows engedélyütközésének elkerülésére. Ha egy adott könyvtárat kell használnia bemeneti könyvtárként, előfordulhat, hogy meg kell adnia a docker-szolgáltatás engedélyét. 
* Ne módosítsa az argumentumok sorrendjét, kivéve, ha nagyon ismeri a docker-tárolók.
* Ha más operációs rendszert használ, használja a megfelelő konzolt/terminált, a mappa szintaxisát a csatlakoztatásokhoz, és a sor folytatási karakterét a rendszerhez. Ezek a példák egy sorfolytatási karakterrel rendelkező Windows-konzolt feltételeznek. `^` Mivel a tároló egy Linux operációs rendszer, a céltartó egy Linux-stílusú mappa szintaxist használ.

Cserélje le {_argument_name_} -t a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás végpontkulcsa az `LUIS` `LUIS` Azure Keys lapon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető `LUIS` az Azure Áttekintés oldalon.| Lásd: [a szükséges paraméterek összegyűjtése](luis-container-howto.md#gathering-required-parameters) explicit példákat. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el. További információ: [Billing](luis-container-howto.md#billing).
> Az ApiKey érték a **kulcs** a kulcsok és végpontok lap a LUIS `Cognitive Services` portálon, és az Azure erőforrás-kulcsok lapon is elérhető. 

### <a name="basic-example"></a>Alapszintű példa

A következő példa a lehető legkevesebb argumentumot teszi a tároló futtatásához:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Példa az ApplicationInsights

A következő példa beállítja az ApplicationInsights argumentumot, hogy telemetriát küldjön az Application Insightsnak a tároló futása közben:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Példa naplózásra 

A következő parancs a `Logging:Console:LogLevel`naplózási szintet állítja [`Information`](https://msdn.microsoft.com)be, a naplózási szint beállításához. 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>További lépések

* Tekintse át [a tárolók telepítésének és futtatásának áttekintését](luis-container-howto.md)
* A LUIS funkcióval kapcsolatos problémák megoldásához olvassa el a [Hibaelhárítás](troubleshooting.md) című témakört.
* További [Cognitive Services-tárolók használata](../cognitive-services-container-support.md)
