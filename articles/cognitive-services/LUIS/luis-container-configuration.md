---
title: Docker-tároló beállításai – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS Container Runtime-környezet a `docker run` parancs argumentumai alapján van konfigurálva. A LUIS számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett.
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
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795013"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding Docker-tárolók konfigurálása 

A **Language Understanding** (Luis) tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. A LUIS számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. A parancshoz több [példa](#example-docker-run-commands) is rendelkezésre áll. A tároló-specifikus beállítások a bemeneti [csatlakoztatási beállítások](#mount-settings) és a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokat tartalmaz:

|Kötelező|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-setting)|A számlázási információk nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetria támogatásának hozzáadását a tárolóhoz.|
|Igen|[Számlázás](#billing-setting)|Meghatározza a szolgáltatási erőforrás végpontjának URI-JÁT az Azure-ban.|
|Igen|[EULA](#eula-setting)| Azt jelzi, hogy elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Írási napló és opcionálisan metrikus adatok egy Fluent-kiszolgáló számára.|
|Nem|[Http-proxy](#http-proxy-credentials-settings)|HTTP-proxy konfigurálása kimenő kérések készítéséhez.|
|Nem|[Naplózás](#logging-settings)|ASP.NET Core naplózási támogatást biztosít a tárolóhoz. |
|Igen|[Csatlakoztatja](#mount-settings)|Adatok olvasása és írása a gazdagépről a tárolóba és a tárolóból a gazdagépre.|

> [!IMPORTANT]
> A [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting)és [`Eula`](#eula-setting) beállításokat a rendszer együtt használja, és mindhárom esetben érvényes értékeket kell megadnia. Ellenkező esetben a tároló nem indul el. A tárolók létrehozásához szükséges konfigurációs beállításokkal kapcsolatos további információkért lásd: [számlázás](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>ApiKey-beállítás

A `ApiKey` beállítás megadja a tároló számlázási adatainak nyomon követéséhez használt Azure-erőforrás kulcsát. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie a [`Billing`](#billing-setting) konfigurációs beállításhoz megadott _Cognitive Services_ erőforráshoz.

Ez a beállítás a következő helyeken érhető el:

* Azure Portal: **Cognitive Services** erőforrás-kezelés a **kulcsok** területen
* LUIS-portál: **kulcsok és végpont-beállítások** lap. 

Ne használja az indító kulcsot vagy a szerzői kulcsot. 

## <a name="applicationinsights-setting"></a>ApplicationInsights-beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Számlázási beállítás

A `Billing` beállítás határozza meg az Azure-beli _Cognitive Services_ erőforrás végponti URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _Cognitive Services_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyeken érhető el:

* Azure Portal: **Cognitive Services** áttekintés, címkézett `Endpoint`
* LUIS Portal: **kulcsok és végponti beállítások** lap a végpont URI részeként.

| Kötelező | Név | Data type | Leírás |
|----------|------|-----------|-------------|
| Igen      | `Billing` | sztring | Számlázási végpont URI-ja. A számlázási URI beszerzésével kapcsolatos további információkért lásd: a [szükséges paraméterek összegyűjtése](luis-container-howto.md#gathering-required-parameters). További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../cognitive-services-custom-subdomains.md). |

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

A LUIS-tároló nem használ bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazda operációs rendszertől függően változhat. Emellett előfordulhat, hogy a [gazdaszámítógép](luis-container-howto.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

A következő táblázat a támogatott beállításokat ismerteti.

|Kötelező| Név | Data type | Leírás |
|-------|------|-----------|-------------|
|Igen| `Input` | Sztring | A bemeneti csatlakoztatás célja. Az alapértelmezett érték `/input`. Ez a LUIS-csomag fájljainak helye. <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nem| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a naplók helye. Ide tartozik a LUIS-lekérdezési naplók és a tároló-naplók. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa Docker-futtatási parancsokra

A következő példák a konfigurációs beállításokat használják a `docker run` parancsok írására és használatára.  A rendszer futtatása után a tároló továbbra is futni fog, amíg [le nem állítja](luis-container-howto.md#stop-the-container) .

* Ezek a példák a `C:` meghajtón lévő könyvtárat használják, hogy elkerülje az engedélyek ütközését a Windows rendszeren. Ha egy adott könyvtárat kell használnia bemeneti könyvtárként, előfordulhat, hogy meg kell adnia a Docker szolgáltatás engedélyét. 
* Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.
* Ha más operációs rendszert használ, használja a megfelelő konzolt/terminált, a csatlakoztatások mappájának szintaxisát, valamint a rendszer vonal folytatási karakterét. Ezek a példák olyan Windows-konzolt feltételeznek, amely egy sor folytatási karakterrel `^`. Mivel a tároló egy Linux operációs rendszer, a cél csatlakoztatása a Linux-stílusú mappa szintaxisát használja.

Cserélje le a {_argument_name_} értéket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az `LUIS` erőforrás Endpoint kulcsa az Azure `LUIS` kulcsok lapon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető az Azure `LUIS` – áttekintés oldalon.| Lásd az explicit példákhoz [szükséges paraméterek összegyűjtését](luis-container-howto.md#gathering-required-parameters) ismertető témakört. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A tároló futtatásához meg kell adni a `Eula`, `Billing`és `ApiKey` beállításokat. Ellenkező esetben a tároló nem indul el. További információ: [számlázás](luis-container-howto.md#billing).
> A ApiKey értéke a LUIS portál kulcsok és végpontok lapjának **kulcsa** , és az Azure `Cognitive Services` Resource Keys oldalon is elérhető. 

### <a name="basic-example"></a>Alapszintű példa

A következő példa a tároló futtatásához lehetséges legkevesebb argumentumot tartalmazhatja:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>ApplicationInsights példa

A következő példa úgy állítja be a ApplicationInsights argumentumot, hogy a telemetria küldje el Application Insights a tároló futása közben:

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

A következő parancs beállítja a naplózási szintet, `Logging:Console:LogLevel`a naplózási szint [`Information`re ](https://msdn.microsoft.com)való konfigurálásához. 

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

* [A tárolók telepítésének és futtatásának](luis-container-howto.md) áttekintése
* A LUIS-funkciókkal kapcsolatos problémák megoldásához tekintse meg a [hibaelhárítást ismertető témakört](troubleshooting.md) .
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
