---
title: Tárolók konfigurálása – Translator Text
titleSuffix: Azure Cognitive Services
description: A Translator Text közös konfigurációs keretrendszerrel rendelkező tárolókat biztosít, így egyszerűen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetria és biztonsági beállításait.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507670"
---
# <a name="configure-translator-text-docker-containers"></a>Translator Text Docker-tárolók konfigurálása

Translator Text az egyes tárolókat közös konfigurációs keretrendszerrel látja el, így egyszerűen konfigurálhatja és kezelheti a tárolók tárolási, naplózási és telemetria és biztonsági beállításait.

## <a name="configuration-settings"></a>Konfigurációs beállítások

A tároló a következő konfigurációs beállításokkal rendelkezik:

|Kötelező|Beállítás|Cél|
|--|--|--|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetria támogatásának hozzáadását a tárolóhoz.|
|Igen|[EULA](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Naplózza a naplót, és opcionálisan metrikus adatokat is egy Fluent-kiszolgálóra.|
|Nem|HTTP-proxy|Konfigurál egy HTTP-proxyt a kimenő kérések elkészítése céljából.|
|Nem|[Naplózás](#logging-settings)|ASP.NET Core naplózási támogatást biztosít a tárolóhoz. |
|Nem|[Tartók](#mount-settings)|Adatokat olvas és ír a gazdagépről a tárolóba, a tárolóból pedig vissza a gazdagépre.|

> [!IMPORTANT]
> A [`Eula`](#eula-setting) beállítást a `accept`értékkel kell megadni; Ellenkező esetben a tároló nem indul el.

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="eula-setting"></a>Végfelhasználói licencszerződés beállítása

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluent beállítások

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxy hitelesítő adatainak beállításai

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Naplózási beállítások
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Csatlakoztatási beállítások

A tárolóban lévő és onnan érkező adatok olvasására és írására a kötési csatlakoztatások használhatók. Adja meg a bemeneti csatlakoztatást vagy a kimeneti csatlakoztatást úgy, hogy megadja a `--mount` lehetőséget a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsban.

A Translator Text tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Optional| Name (Név) | Data type | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Translator Text tárolók nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra 

A következő példák a konfigurációs beállításokat használják a `docker run` parancsok írására és használatára.  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](how-to-install-containers.md#stop-the-container) .

* **Vonal-folytatási karakter**: a következő szakaszban lévő Docker-parancsok a háttér perjelet (`\`) használják vonal-folytatási karakterként. Cserélje le vagy távolítsa el a gazdagép operációs rendszerének követelményei alapján. 
* **Argumentumok sorrendje**: ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

## <a name="translator-text-container-docker-examples"></a>Translator Text Container Docker-példák

A következő Docker-példák a Translator Text tárolóra vonatkoznak.

### <a name="basic-example"></a>Alapszintű példa

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>Példa naplózásra

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>További lépések

* [A tárolók telepítésének és futtatásának](how-to-install-containers.md) áttekintése
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
