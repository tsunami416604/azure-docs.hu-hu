---
title: Docker-tároló beállításai – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS tároló futtatási környezetet segítségével konfigurálható a `docker run` parancssori argumentumokat. A LUIS rendelkezik néhány választható beállítások mellett számos szükséges beállításokat.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: dapine
ms.openlocfilehash: e6a13688bba1c3a0e62e427e078e78c8f8dd4e70
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560614"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding Docker-tárolók konfigurálása 

A **Language Understanding** (Luis) tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. A LUIS rendelkezik néhány választható beállítások mellett számos szükséges beállításokat. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tároló-specifikus beállításokat is a bemeneti [csatlakoztatási beállítások](#mount-settings) és a számlázási beállításait. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokkal rendelkezik:

|Szükséges|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-setting)|Számlázási adatok nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba.|
|Igen|[Billing](#billing-setting)|Az Azure-ban található szolgáltatás-erőforrás végponti URI-ját adja meg.|
|Igen|[Eula](#eula-setting)| Azt jelzi, hogy Ön már elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Napló írási és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.|
|Nem|[Http-proxy](#http-proxy-credentials-settings)|HTTP-proxy konfigurálása kimenő kérések készítéséhez.|
|Nem|[Logging](#logging-settings)|Biztosítja a naplózás a tároló támogatása ASP.NET Core. |
|Ige|[Fluentd](#mount-settings)|Napló- és opcionálisan metrikaadatok írása egy Fluentd-kiszolgálóra.|

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>A beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie  a [`Billing`](#billing-setting) konfigurációs beállításhoz megadott Cognitive Services erőforráshoz.

Ez a beállítás a következő helyeken érhető el:

* Azure Portal: **Cognitive Services** Erőforrás-kezelés a **kulcsok** területen
* LUIS-portál: **Kulcsok és végpont-beállítások** lap. 

Ne használja az alapszintű vagy a szerzői műveletekhez részben billentyűt. 

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Számlázás beállítása

A `Billing` beállítás határozza meg az Azure-beli _Cognitive Services_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _Cognitive Services_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyeken érhető el:

* Azure Portal: **Cognitive Services** A címkével ellátott áttekintés`Endpoint`
* LUIS-portál: A **kulcsok és végpontok beállításai** lap a végpont URI-ja részeként.

Az alábbi táblázatban látható `luis/v2.0` módon vegye fel az útválasztást az URL-címre:


|Kötelező| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

A LUIS-tároló nem használja a bemeneti vagy kimeneti csatlakoztatja képzési vagy szolgáltatás adatok tárolására. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](luis-container-howto.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket. 

A következő táblázat ismerteti a támogatott beállítások.

|Szükséges| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Igen| `Input` | Karakterlánc | A bemeneti csatlakoztatási célját. Az alapértelmezett érték `/input`. Ez az az LUIS-fájlok helyét. <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nem| `Output` | Karakterlánc | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a LUIS lekérdezések naplói és a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](luis-container-howto.md#stop-the-container) azt.

* Ezek a példák a `c:` meghajtón lévő könyvtárat használják, hogy elkerüljék az engedélyek ütközését a Windowson. Ha szeretné használni a bemeneti könyvtár egy adott címtárhoz, előfordulhat, hogy kell biztosítania a docker szolgáltatás engedéllyel. 
* Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a docker-tárolókat.
* Ha más operációs rendszert használ, használja a megfelelő konzolt/terminált, a csatlakoztatások mappájának szintaxisát, valamint a rendszer vonal folytatási karakterét. Ezek a példák egy sor folytatási karakterrel `^`rendelkező Windows-konzolt feltételeznek. Mivel a tároló egy Linux operációs rendszer, a cél csatlakoztatása a Linux-stílusú mappa szintaxisát használja.

Ne feledje, hogy `luis/v2.0` a következő táblázatban látható módon belefoglalja az útválasztást az URL-címre.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{API_KEY} | A végpont kulcs a betanított LUIS-alkalmazás. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URL} | A számlázási végpont értéke elérhető az Azure `Cognitive Services` Áttekintés oldalán. |https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](luis-container-howto.md#billing).
> A ApiKey értéke a Luis portál kulcsok és végpontok lapjának **kulcsa** , és az Azure `Cognitive Services` Resource Keys oldalon is elérhető. 

### <a name="basic-example"></a>Alapszintű példa

Az alábbi példa a legkevesebb argumentumokat is futtathatók, a tároló rendelkezik:

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Applicationinsights – példa

Az alábbi példa telemetriát küldjön az Application Insightsba, amíg a tároló fut-e ApplicationInsights argumentumának állítja be:

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

A következő parancs beállítja a naplózási szint `Logging:Console:LogLevel`, konfigurálhatja a naplózási szint [ `Information` ](https://msdn.microsoft.com). 

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

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](luis-container-howto.md)
* A LUIS [](troubleshooting.md) -funkciókkal kapcsolatos problémák megoldásához tekintse meg a hibaelhárítást ismertető témakört.
* További [Cognitive Services tárolók](../cognitive-services-container-support.md) használata
