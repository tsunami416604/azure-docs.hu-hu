---
title: Docker-tároló beállításai
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS tároló futtatási környezetet segítségével konfigurálható a `docker run` parancssori argumentumokat. A LUIS rendelkezik néhány választható beállítások mellett számos szükséges beállításokat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: diberry
ms.openlocfilehash: ee08f5e15180a618d1a9c48b7d59b9e1f8bc90ae
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56329115"
---
# <a name="configure-language-understanding-docker-containers"></a>Language Understanding Docker-tárolók konfigurálása 

A **Language Understanding** tároló-futtatókörnyezet (LUIS) szolgáltatással konfigurálható a `docker run` parancssori argumentumokat. A LUIS rendelkezik néhány választható beállítások mellett számos szükséges beállításokat. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tároló-specifikus beállításokat is a bemeneti [csatlakoztatási beállítások](#mount-settings) és a számlázási beállításait. 

Tároló beállításai [hierarchikus](#hierarchical-settings) és állítható be [környezeti változók](#environment-variable-settings) vagy a docker [parancssori argumentumok](#command-line-argument-settings).

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokkal rendelkezik:

|Szükséges|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-setting)|Számlázási adatok nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba.|
|Igen|[Billing](#billing-setting)|Az Azure-ban található szolgáltatás-erőforrás végponti URI-ját adja meg.|
|Igen|[Eula](#eula-setting)| Azt jelzi, hogy Ön már elfogadta a tároló licencét.|
|Nem|[Fluentd](#fluentd-settings)|Napló írási és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.|
|Nem|[Http Proxy](#http-proxy-credentials-settings)|Egy HTTP-proxy konfigurálása, hogy a kimenő kérelmek.|
|Nem|[Logging](#logging-settings)|Biztosítja a naplózás a tároló támogatása ASP.NET Core. |
|Ige|[Fluentd](#mount-settings)|Napló- és opcionálisan metrikaadatok írása egy Fluentd-kiszolgálóra.|

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>A beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a apikey tulajdonsággal végzett tesztelése és az értéknek kell lennie egy érvényes kulcsot a _Language Understanding_ megadott erőforrás a [ `Billing` ](#billing-setting) konfigurációs beállítás.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **Language Understanding** erőforrás-kezelés alatt **kulcsok**
* A LUIS-portálon: **Kulcsok és végpontbeállítások** lapot. 

Ne használja az alapszintű vagy a szerzői műveletekhez részben billentyűt. 

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Számlázás beállítása

A `Billing` beállítás határozza meg a végpont URI-t, a _Language Understanding_ erőforrást az Azure-ban használt mérni a tároló számlázási adatokat. Meg kell adnia egy értéket a konfigurációs beállítás, és az értéknek kell lennie egy érvényes végpont URI-t a egy _Language Understanding_ erőforrást az Azure-ban. A tároló használati jelentések kapcsolatos 10 – 15 percenként.

Ez a beállítás a következő helyen található:

* Az Azure Portalon: **Language Understanding** áttekintése, címkével `Endpoint`
* A LUIS-portálon: **Kulcsok és végpontbeállítások** lap, a végpont URI részeként.

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

A LUIS-tároló nem használja a bemeneti vagy kimeneti csatlakoztatja képzési vagy szolgáltatás adatok tárolására. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Ezenkívül a [gazdaszámítógép](luis-container-howto.md#the-host-computer)a csatlakoztatási helye nem lehet elérni a docker szolgáltatás fiókja által használt engedélyek közötti ütközés miatt, és a gazdagép csatlakoztatásához hely engedélyeket. 

A következő táblázat ismerteti a támogatott beállítások.

|Szükséges| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Igen| `Input` | Karakterlánc | A bemeneti csatlakoztatási célját. Az alapértelmezett érték `/input`. Ez az az LUIS-fájlok helyét. <br><br>Példa:<br>`--mount type=bind,src=c:\input,target=/input`|
|Nem| `Output` | Karakterlánc | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a LUIS lekérdezések naplói és a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>Hierarchikus beállításai

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]


## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](luis-container-howto.md#stop-the-container) azt.


* **Vonal-folytatási karakter**: Az alábbi szakaszok a docker-parancsokat használhatja a fordított perjel `\`, egy sor folytatási karaktert. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. 
* **Argument sorrend**: Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a docker-tárolókat.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{ENDPOINT_KEY} | A végpont kulcs a betanított LUIS-alkalmazás. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | A számlázási végpont értékét az Azure Portalon Language Understanding áttekintése oldalon érhető el.|https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](luis-container-howto.md#billing).
> Apikey tulajdonsággal végzett tesztelése értéke a **kulcs** a kulcsok és a végpontok lapon a LUIS-portálon, és érhető el az Azure Language Understanding Resource kulcsok oldalon is. 

### <a name="basic-example"></a>Alapszintű példa

Az alábbi példa a legkevesebb argumentumokat is futtathatók, a tároló rendelkezik:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Az előző parancs a könyvtárat használja ki a `c:` meghajtón a Windows-engedély ütközések elkerülése érdekében. Ha szeretné használni a bemeneti könyvtár egy adott címtárhoz, előfordulhat, hogy kell biztosítania a docker szolgáltatás engedéllyel. Az előző docker parancsot használja a fordított perjel `\`, egy sor folytatási karaktert. Cserélje le, vagy távolítsa el ezt a alapján a [gazdaszámítógép](luis-container-howto.md#the-host-computer) operációs rendszerre vonatkozó követelmények. Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a docker-tárolókat.


### <a name="applicationinsights-example"></a>Applicationinsights – példa

Az alábbi példa telemetriát küldjön az Application Insightsba, amíg a tároló fut-e ApplicationInsights argumentumának állítja be:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example-with-command-line-arguments"></a>Parancssori argumentumok naplózását példa

A következő parancs beállítja a naplózási szint `Logging:Console:LogLevel`, konfigurálhatja a naplózási szint [ `Information` ](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel=Information
```

### <a name="logging-example-with-environment-variable"></a>Naplózás példában a környezeti változó

A következő parancsokat használja egy környezeti változó nevű `Logging:Console:LogLevel` konfigurálhatja a naplózási szint [ `Information` ](https://msdn.microsoft.com). 

```bash
SET Logging:Console:LogLevel=Information
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={APPLICATION_ID} \
```

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](luis-container-howto.md)
* Tekintse meg [hibaelhárítás](troubleshooting.md) LUIS funkció kapcsolatos problémák megoldásához.
* Több [Cognitive Services-tárolók](../cognitive-services-container-support.md)
