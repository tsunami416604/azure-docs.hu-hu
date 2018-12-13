---
title: Docker-tároló beállításai
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS tároló futtatási környezetet segítségével konfigurálható a `docker run` parancssori argumentumokat. A LUIS rendelkezik néhány választható beállítások mellett számos szükséges beállításokat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: diberry
ms.openlocfilehash: 98828589832d69ada11205e471314a153a566766
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080265"
---
# <a name="configure-containers"></a>Tárolók konfigurálása

A Language Understanding (LUIS) tároló futtatási környezetet segítségével konfigurálható a `docker run` parancssori argumentumokat. A LUIS rendelkezik néhány választható beállítások mellett számos szükséges beállításokat. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tároló-specifikus beállításokat is a bemeneti [csatlakoztatási beállítások](#mount-settings) és a számlázási beállításait. 

Tároló beállításai [hierarchikus](#hierarchical-settings) és állítható be [környezeti változók](#environment-variable-settings) vagy a docker [parancssori argumentumok](#command-line-argument-settings).

## <a name="configuration-settings"></a>Konfigurációs beállítások

Ez a tároló a következő konfigurációs beállításokkal rendelkezik:

|Szükséges|Beállítás|Cél|
|--|--|--|
|Igen|[ApiKey](#apikey-setting)|Számlázási adatok nyomon követésére szolgál.|
|Nem|[ApplicationInsights](#applicationinsights-setting)|Lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba.|
|Igen|[Számlázás](#billing-setting)|Adja meg a végpont URI-t, a _Language Understanding_ erőforrást az Azure-ban.|
|Igen|[Végfelhasználói licencszerződés](#eula-setting)| Azt jelzi, hogy Ön már elfogadta a licencet, a tároló.|
|Nem|[Fluentd](#fluentd-settings)|Napló írási és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.|
|Nem|[Logging](#logging-settings)|Biztosítja a naplózás a tároló támogatása ASP.NET Core. |
|Igen|[Csatlakoztatja](#mount-settings)|Az adatok olvasását és írását [gazdaszámítógép](luis-container-howto.md#the-host-computer) tárolóba, és vissza a gazdaszámítógép-tárolóból.|

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-setting), [ `Billing` ](#billing-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>A beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a apikey tulajdonsággal végzett tesztelése és az értéknek kell lennie egy érvényes kulcsot a _Language Understanding_ megadott erőforrás a [ `Billing` ](#billing-setting) konfigurációs beállítás.

Ez a beállítás két helyen található:

* Az Azure portal: **Language Understanding** erőforrás-kezelés alatt **kulcsok**
* A LUIS-portál: **kulcsokat és a végpont beállításait** lap. 

Ne használja az alapszintű vagy a szerzői műveletekhez részben billentyűt. 

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

A `ApplicationInsights` beállítás lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba. Az Application Insights az a tároló részletes figyelését teszi lehetővé. Könnyedén figyelheti a tárolója elérhetőségéről, teljesítményéről és kihasználtságáról. Emellett egyszerűen azonosíthatja és diagnosztizálhatja a hibákat a tárolóban.

A következő táblázat ismerteti a támogatott konfigurációs beállításait a `ApplicationInsights` szakaszban.

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Nem| `InstrumentationKey` | Karakterlánc | A rendszerállapotkulcsot az Application Insights-példány melyik telemetriai adatokat a tároló küldi. További információkért lásd: [Application Insights az ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Példa:<br>`InstrumentationKey=123456789`|


## <a name="billing-setting"></a>Számlázás beállítása

A `Billing` beállítás határozza meg a végpont URI-t, a _Language Understanding_ erőforrást az Azure-ban használt mérni a tároló számlázási adatokat. Meg kell adnia egy értéket a konfigurációs beállítás, és az értéknek kell lennie egy érvényes végpont URI-t a egy _Language Understanding_ erőforrást az Azure-ban.

Ez a beállítás két helyen található:

* Az Azure portal: **Language Understanding** áttekintése, címkével `Endpoint`
* A LUIS-portálon: **kulcsokat és a végpont beállítások** lap, a végpont URI részeként.

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

## <a name="eula-setting"></a>Licencfeltételek beállítása

A `Eula` beállítás azt jelzi, hogy Ön már elfogadta a licencet, a tároló. Meg kell adnia egy értéket a konfigurációs beállítás, és az értékét állítsa `accept`.

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Eula` | Karakterlánc | Licencfeltételek elfogadásának<br><br>Példa:<br>`Eula=accept` |

Cognitive Services-tárolók licence alapján [a szerződés](https://go.microsoft.com/fwlink/?linkid=2018657) az Azure használatát szabályozó. Ha nem rendelkezik egy meglévő, az Azure használatát szabályozó megállapodást, Ön elfogadja, hogy van-e az Azure használatát szabályozó megállapodást a [Microsoft Online előfizetői szerződés](https://go.microsoft.com/fwlink/?linkid=2018755), amely magában foglalja a [Online szolgáltatások használati feltételeit ](https://go.microsoft.com/fwlink/?linkid=2018760). Az előnézetben, is elfogadja a [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://go.microsoft.com/fwlink/?linkid=2018815). A tároló használatával elfogadja ezeket a feltételeket.

## <a name="fluentd-settings"></a>Fluentd beállításai

Fluentd egy nyílt forráskódú adatgyűjtő egyesített naplózási. A `Fluentd` beállítások kezelése, a tároló kapcsolati egy [Fluentd](https://www.fluentd.org) kiszolgáló. A LUIS-tároló tartalmazza a Fluentd naplózási szolgáltató, amely lehetővé teszi, hogy a tároló is naplóznak, és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.

A következő táblázat ismerteti a támogatott konfigurációs beállításait a `Fluentd` szakaszban.

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Host` | Karakterlánc | Az IP-cím vagy a Fluentd kiszolgáló DNS-állomásneve. |
| `Port` | Egész szám | A port, a Fluentd kiszolgáló.<br/> Az alapértelmezett érték: 24224. |
| `HeartbeatMs` | Egész szám | A szívverési időköz ezredmásodpercben. Ha esemény forgalmat a rendszer elküldte, ez az időtartam lejárta előtt, szívverést küld a Fluentd kiszolgálóra. Az alapértelmezett érték: 60000 ezredmásodperc (1 perc). |
| `SendBufferSize` | Egész szám | A hálózati pufferterületének, műveletek számára lefoglalt (bájt). Az alapértelmezett érték: 32768 bájtok (32 kilobájt). |
| `TlsConnectionEstablishmentTimeoutMs` | Egész szám | Az időkorlát ezredmásodpercben, a Fluentd kiszolgálóval SSL/TLS kapcsolatot létesíteni. Az alapértelmezett érték: 10000 ezredmásodperc (10 másodperc).<br/> Ha `UseTLS` értéke hamis értékre, ezt az értéket figyelmen kívül hagyja. |
| `UseTLS` | Logikai | Azt jelzi, hogy a tároló kell használnia az SSL/TLS a Fluentd kiszolgálóval való kommunikáció során. Az alapértelmezett értéke FALSE (hamis). |

## <a name="logging-settings"></a>Naplózási beállítások

A `Logging` beállítások kezelése a tároló az ASP.NET Core-naplózás támogatása. A tárolót használja az ASP.NET Core alkalmazás használhatja az ugyanazon konfigurációs beállításokat és értékeket. 

A következő naplózási szolgáltatók támogatottak a LUIS-tároló:

|Szolgáltató|Cél|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Az ASP.NET Core `Console` naplózási szolgáltató. Az ASP.NET Core-konfigurációs beállításokat és a naplózás szolgáltatóhoz tartozó alapértelmezett értékeket támogatottak.|
|[Hibakeresés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Az ASP.NET Core `Debug` naplózási szolgáltató. Az ASP.NET Core-konfigurációs beállításokat és a naplózás szolgáltatóhoz tartozó alapértelmezett értékeket támogatottak.|
|[Lemez](#disk-logging)|A JSON-naplózás szolgáltató. A naplózás szolgáltató naplóadatokat ír a kimeneti csatlakoztatási.|

### <a name="disk-logging"></a>Lemez naplózása
  
A `Disk` naplózási szolgáltató támogatja-e a következő beállításokat:  

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Format` | Karakterlánc | A kimeneti formátum a rendszernapló fájljaiban.<br/> **Megjegyzés:** ezt az értéket kell beállítani `json` a naplózás szolgáltatónak. Ezt az értéket egy kimeneti csatlakoztatási közben hárítható el egy tároló megadása nélkül, ha hiba történik. |
| `MaxFileSize` | Egész szám | A maximális méretét megabájtban (MB), a naplófájlok. Ha az aktuális naplófájl méretét megfelel-e vagy meghaladja ezt az értéket, egy új naplófájl indítja el a naplózás szolgáltató. Ha meg van adva a -1, a naplófájl méretét csak korlátozza a maximális méretet, ha bármely, a kimeneti csatlakoztatási. Az alapértelmezett érték az 1. |

ASP.NET Core-naplózás támogatást konfigurálásával kapcsolatos további információkért lásd: [fájl konfigurációs beállítások](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

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

A LUIS-tároló beállítások hierarchikusak, és az összes tárolót a [gazdaszámítógép](luis-container-howto.md#the-host-computer) egy megosztott hierarchia használatát.

Adja meg a beállítások a következők egyikét használhatja:

* [Környezeti változók](#environment-variable-settings)
* [Parancssori argumentumok](#command-line-argument-settings)

A környezeti változó értékeit felülbírálhatja parancssori argumentumok értékeit, amelyek viszont felülírják az alapértelmezett értékeit a tároló rendszerképét. Ha eltérő értékeket egy környezeti változó és a egy parancssori argumentum ugyanazon konfigurációs beállítás megadása esetén a környezeti változó értékét a példányosított tároló használják.

|Sorrend|Helyének beállítása|
|--|--|
|1|Környezeti változó| 
|2|Parancssor|
|3|Tároló-lemezképet alapértelmezett érték|

### <a name="environment-variable-settings"></a>Környezeti változók beállításai

A környezeti változók használatának előnyei a következők:

* Több beállítások konfigurálhatók.
* Több tároló használhatja ugyanazokat a beállításokat.

### <a name="command-line-argument-settings"></a>Parancssori argumentumok beállításai

Parancssori argumentumok használatával előnye, hogy a tárolók használható különböző beállításokat.

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](luis-container-howto.md#stop-the-container) azt.


* **Vonal-folytatási karakter**: A docker-parancsokat az alábbi szakaszokban található használja a fordított perjel `\`, egy sor folytatási karaktert. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. 
* **Argument sorrend**: nem módosítsa az argumentumok sorrendje, ha tisztában van a docker-tárolókat.

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
* Tekintse meg [– gyakori kérdések (GYIK)](luis-resources-faq.md) LUIS funkció kapcsolatos problémák megoldásához.