---
title: Tárolók konfigurálása
titlesuffix: Face - Azure Cognitive Services
description: Tárolók konfigurációs beállításait.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: a75f5ec11644bbd64a6bafeb8585371437eedc3b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476920"
---
# <a name="configure-containers"></a>Tárolók konfigurálása

A Face tárolót használja egy közös keretrendszer konfigurációs, hogy a egyszerűen konfigurálása és kezelése a storage, a naplózás és a telemetriai adatok és a biztonsági beállítások a tárolókhoz.

## <a name="configuration-settings"></a>Konfigurációs beállítások

Konfigurációs beállításai a Face tároló a hierarchikus, és az összes tárolót a megosztott hierarchia esetén az alábbi felső szintű struktúrával alapján:

* [ApiKey](#apikey-configuration-setting)
* [ApplicationInsights](#applicationinsights-configuration-settings)
* [Hitelesítés](#authentication-configuration-settings)
* [Számlázás](#billing-configuration-setting)
* [CloudAI](#cloudai-configuration-settings)
* [Végfelhasználói licencszerződés](#eula-configuration-setting)
* [Fluentd](#fluentd-configuration-settings)
* [HTTP-proxy hitelesítő adat beállításai](#http-proxy-credentials-settings)
* [Logging](#logging-configuration-settings)
* [Csatlakoztatja](#mounts-configuration-settings)

Használhat [környezeti változók](#configuration-settings-as-environment-variables) vagy [parancssori argumentumok](#configuration-settings-as-command-line-arguments) konfigurációs beállításainak megadásához, a Face tároló hárítható el.

A környezeti változó értékeit felülbírálhatja parancssori argumentumok értékeit, amelyek viszont felülírják az alapértelmezett értékeit a tároló rendszerképét. Más szóval ha, adjon meg eltérő értékeket egy környezeti változó és a egy parancssori argumentum ugyanazon konfigurációs beállítás, például `Logging:Disk:LogLevel`, majd hozza létre a tárolót, a környezeti változó értékét használja a rendszer által a példányosított a tároló.

### <a name="configuration-settings-as-environment-variables"></a>Környezeti változókként konfigurációs beállításai

Használhatja a [ASP.NET Core környezeti változó szintaxis](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#configuration-by-environment) konfigurációs beállításainak megadásához.

A tároló beolvassa a felhasználói környezeti változókat, amikor a tároló példányosítása. Ha egy környezeti változó már létezik, a környezeti változó értékét felülírja az alapértelmezett érték a megadott konfigurációs beállítás. A környezeti változók használatával előnye, hogy több konfigurációs beállításokkal tárolók létrehozása előtt, és több tároló automatikusan használhatja ugyanazokat a konfigurációs beállításokat.

Például a következő parancsokat használja a konzolon naplózási szint konfigurálása egy környezeti változó [LogLevel.Information](https://msdn.microsoft.com), majd példányosít egy tárolót a Face tárolórendszerképet. A környezeti változó értékét felülírja az alapértelmezett konfigurációs beállítás.

  ```Docker
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
  ```

### <a name="configuration-settings-as-command-line-arguments"></a>Parancssori argumentumok konfigurációs beállítást

Használhatja a [ASP.NET Core parancssori argumentum szintaxisa](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.1&tabs=basicconfiguration#arguments) konfigurációs beállításainak megadásához.

A nem kötelező konfigurációs beállításokat is megadhat `ARGS` paraméterében a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) letöltött tárolórendszerképet a tároló példányosítása használt parancs. A parancssori argumentumok használatával előnye, hogy a tárolók használatával is egy másik, egyéni konfigurációs beállítások.

Az alábbi parancs például példányosít egy tárolót a Face tárolórendszerképet, és konfigurálja a naplózási szint LogLevel.Information, az alapértelmezett konfigurációs beállítás felülbírálja a konzolon.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Console:LogLevel=Information
  ```

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` konfigurációs beállítással a konfigurációs kulcsot a Face erőforrás segítségével nyomon követhető a számlázási adatokat tároló Azure-ban. Meg kell adnia egy értéket a konfigurációs beállítás, és az értéknek kell lennie egy érvényes konfigurációs kulcsot a megadott Face erőforrás a [ `Billing` ](#billing-configuration-setting) konfigurációs beállítás.

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-configuration-setting) konfigurációs beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom őket. Ellenkező esetben a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](face-how-to-install-containers.md#billing).

## <a name="applicationinsights-configuration-settings"></a>Applicationinsights – konfigurációs beállításai

A konfigurációs beállításokat a `ApplicationInsights` szakasz lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba. Az Application Insights az a tároló a kód szintre részletes figyelését teszi lehetővé. Könnyedén figyelheti a tárolója elérhetőségéről, teljesítményéről és kihasználtságáról. Emellett egyszerűen azonosíthatja és diagnosztizálhatja a hibákat a tárolóban egy felhasználó jelenti azokat várakozás nélkül.

A következő táblázat ismerteti a támogatott konfigurációs beállításait a `ApplicationInsights` szakaszban.

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `InstrumentationKey` | Karakterlánc | A rendszerállapotkulcsot az Application Insights-példány melyik telemetriai adatokat a tároló küldi. További információkért lásd: [Application Insights az ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="authentication-configuration-settings"></a>Hitelesítési beállítások

A `Authentication` konfigurációs beállítások megadása a tároló az Azure biztonsági beállításai. Bár ebben a szakaszban a konfigurációs beállítások állnak rendelkezésre, a Face tároló nem használ ebben a szakaszban.

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Storage` | Karakterlánc | A rendszerállapotkulcsot az Application Insights-példány melyik telemetriai adatokat a tároló küldi. További információkért lásd: [Application Insights az ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). |

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` konfigurációs beállítás határozza meg a számlázási adatokat tároló mérni szeretné használt URI-t az Azure-ban a Face erőforrás végpont. Meg kell adnia egy értéket a konfigurációs beállítás, és az értéknek kell lennie az Azure-ban Face erőforrás URI érvényes végpontot.

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-configuration-setting) konfigurációs beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom őket. Ellenkező esetben a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](face-how-to-install-containers.md#billing).

## <a name="cloudai-configuration-settings"></a>CloudAI konfigurációs beállításai

A konfigurációs beállításokat a `CloudAI` szakaszban adja meg a tároló egyedi tároló-specifikus beállítások. A következő beállításokat, és az objektumok a Face tároló támogatottak a `CloudAI` szakasz

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Storage` | Objektum | A tárolási eset, a Face tárolót használja. További információ a storage-forgatókönyvek és kapcsolódó beállításait a `Storage` objektumazonosító, lásd: [tárolási forgatókönyv beállításai](#storage-scenario-settings) |

### <a name="storage-scenario-settings"></a>Tárolási forgatókönyv beállításai

A Face tároló tárolja a blob, a gyorsítótár, a metaadatok és a sorban lévő adatok függően mi tárol. Ha például képzési indexeket és a egy nagy embercsoportok eredményei formájában tárolja Blobadatok. A Face tárolót biztosít, két különböző adattárolási forgatókönyvekhez való interakció során és az ilyen típusú adatok tárolására:

* Memory (Memória)  
  Minden négy típusú adatokat a memóriában tárolódnak. Nem használ osztják, sem pedig az állandó. A Face tároló leállt, vagy eltávolítja, ha az összes adat a tárolóban, hogy a tároló a semmisíteni.  
  Ez az az alapértelmezett tárolási eset a Face tároló.
* Azure  
  A Face tároló Azure Storage és az Azure Cosmos DB használatával ezek négy típusú adatok szét a tartós tároláshoz. Azure Storage BLOB és üzenetsor adatokat kezeli. Azure Cosmos DB kezeli az metaadatok és a gyorsítótár-adatokat. A Face tároló leállt, vagy eltávolítja, ha az adatok a tároló storage-ban tárolt, az Azure Storage és Azure Cosmos DB marad.  
  Az Azure storage-forgatókönyv által használt erőforrások az alábbi további követelményekkel rendelkezik.
  * Az Azure Storage-erőforrást kell használnia a StorageV2 fiók típusa
  * Az Azure Cosmos DB erőforrás az Azure Cosmos DB API-t kell használnia a mongodb-hez

A storage-forgatókönyvek és a kapcsolódó konfigurációs beállítások kezeli a `Storage` objektumot, a a `CloudAI` konfigurációs szakaszban. A következő beállítások érhetők el a `Storage` objektum:

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `StorageScenario` | Karakterlánc | A tárolási eset, a tároló által támogatott. A következő értékek közül választhat<br/>`Memory` – Alapértelmezett érték. Tároló nem állandó, nem elosztott és a memórián belüli tároló használ egy csomópontos, ideiglenes használatra. A tároló leállt, vagy eltávolítja, ha a tárolót a tároló megsemmisülésekor.<br/>`Azure` -Tároló Azure-erőforrásokat használ a tároláshoz. A tároló leállítása vagy eltávolítja, a rendszer megőrzi a tároló a tárolót.|
| `ConnectionStringOfAzureStorage` | Karakterlánc | Az Azure Storage-erőforrás a tároló által használt kapcsolati karakterláncára.<br/>Ez a beállítás csak akkor, ha érvényes `Azure` van megadva a `StorageScenario` konfigurációs beállítás. |
| `ConnectionStringOfCosmosMongo` | Karakterlánc | A MongoDB kapcsolati karakterláncot az Azure Cosmos DB-erőforrás a tárolót használja.<br/>Ez a beállítás csak akkor, ha érvényes `Azure` van megadva a `StorageScenario` konfigurációs beállítás. |

Például a következő parancsot adja meg az Azure storage-forgatókönyv, és minta kapcsolati karakterláncok biztosít a Face tároló adatok tárolására használt Azure Storage és a Cosmos DB erőforrásokat.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

A tárolási eset külön-külön történik a csatlakoztatása bemeneti és kimeneti csatlakoztatása. Ezek a Funkciók, az egyetlen tároló kombinációját is megadhat. Például a következő parancsot határozza meg a Docker kötési csatlakoztatási a `D:\Output` mappát a gazdagépen, a kimeneti csatlakoztatási majd példányosít egy tárolót a Face tárolórendszerképet, naplófájlok mentése, a kimeneti csatlakoztatási JSON formátumban. A parancs is itt adhatja meg az Azure storage-forgatókönyv, és minta kapcsolati karakterláncok biztosít a Face tároló adatok tárolására használt Azure Storage és a Cosmos DB erőforrásokat.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

## <a name="eula-configuration-setting"></a>Végfelhasználói licencszerződés konfigurációs beállítás

A `Eula` konfigurációs beállítás azt jelzi, hogy Ön már elfogadta a licencet, a tároló. Meg kell adnia egy értéket a konfigurációs beállítás, és az értékét állítsa `accept`.

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-configuration-setting) konfigurációs beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom őket. Ellenkező esetben a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](face-how-to-install-containers.md#billing).

Cognitive Services-tárolók licence alapján [a szerződés](https://go.microsoft.com/fwlink/?linkid=2018657) az Azure használatát szabályozó. Ha nem rendelkezik egy meglévő, az Azure használatát szabályozó megállapodást, Ön elfogadja, hogy van-e az Azure használatát szabályozó megállapodást a [Microsoft Online előfizetői szerződés](https://go.microsoft.com/fwlink/?linkid=2018755) (amely magában foglalja a [Online szolgáltatások használati feltételeit ](https://go.microsoft.com/fwlink/?linkid=2018760)). Az előnézetben, is elfogadja a [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://go.microsoft.com/fwlink/?linkid=2018815). A tároló használatával elfogadja ezeket a feltételeket.

## <a name="fluentd-configuration-settings"></a>Fluentd konfigurációs beállításai

A `Fluentd` szakasz konfigurációs beállításait kezeli [Fluentd](https://www.fluentd.org), egy nyílt forráskódú egyesített naplózási gyűjtő. a Face tároló tartalmazza, amely lehetővé teszi, hogy a tároló naplójába Fluentd naplózási szolgáltató és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.

A következő táblázat ismerteti a támogatott konfigurációs beállításait a `Fluentd` szakaszban.

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Host` | Karakterlánc | Az IP-cím vagy a Fluentd kiszolgáló DNS-állomásneve. |
| `Port` | Egész szám | A port, a Fluentd kiszolgáló.<br/> Az alapértelmezett érték: 24224. |
| `HeartbeatMs` | Egész szám | A szívverési időköz ezredmásodpercben. Ha esemény forgalmat a rendszer elküldte, ez az időtartam lejárta előtt, szívverést küld a Fluentd kiszolgálóra. Az alapértelmezett érték: 60000 ezredmásodperc (1 perc). |
| `SendBufferSize` | Egész szám | A hálózati pufferterületének, műveletek számára lefoglalt (bájt). Az alapértelmezett érték: 32768 bájtok (32 kilobájt). |
| `TlsConnectionEstablishmentTimeoutMs` | Egész szám | Az időkorlát ezredmásodpercben, a Fluentd kiszolgálóval SSL/TLS kapcsolatot létesíteni. Az alapértelmezett érték: 10000 ezredmásodperc (10 másodperc).<br/> Ha `UseTLS` értéke hamis értékre, ezt az értéket figyelmen kívül hagyja. |
| `UseTLS` | Logikai | Azt jelzi, hogy a tároló kell használnia az SSL/TLS a Fluentd kiszolgálóval való kommunikáció során. Az alapértelmezett értéke FALSE (hamis). |


## <a name="http-proxy-credentials-settings"></a>HTTP-proxybeállításai hitelesítő adatok

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-configuration-settings"></a>Naplózás konfigurációs beállításait

A `Logging` konfigurációs beállításokat az ASP.NET Core naplózás támogatását, a tároló kezelése. A tároló, amely egy ASP.NET Core-alkalmazást is használhatja az ugyanazon konfigurációs beállításokat és értékeket. A következő naplózási szolgáltatók támogatottak a Face tároló:

* [Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)  
  Az ASP.NET Core `Console` naplózási szolgáltató. Az ASP.NET Core-konfigurációs beállításokat és a naplózás szolgáltatóhoz tartozó alapértelmezett értékeket támogatottak.
* [Hibakeresés](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)  
  Az ASP.NET Core `Debug` naplózási szolgáltató. Az ASP.NET Core-konfigurációs beállításokat és a naplózás szolgáltatóhoz tartozó alapértelmezett értékeket támogatottak.
* Lemez  
  A JSON-naplózás szolgáltató. A naplózás szolgáltató naplóadatokat ír a kimeneti csatlakoztatási.  
  A `Disk` naplózási szolgáltató támogatja-e a következő beállításokat:  

  | Name (Név) | Adattípus | Leírás |
  |------|-----------|-------------|
  | `Format` | Karakterlánc | A kimeneti formátum a rendszernapló fájljaiban.<br/> **Megjegyzés:** Ezt az értéket kell beállítani `json` a naplózás szolgáltatónak. Ezt az értéket egy kimeneti csatlakoztatási közben hárítható el egy tároló megadása nélkül, ha hiba történik. |
  | `MaxFileSize` | Egész szám | A maximális méretét megabájtban (MB), a naplófájlok. Ha az aktuális naplófájl méretét megfelel-e vagy meghaladja ezt az értéket, egy új naplófájl indítja el a naplózás szolgáltató. Ha meg van adva a -1, a naplófájl méretét csak korlátozza a maximális méretet, ha bármely, a kimeneti csatlakoztatási. Az alapértelmezett érték az 1. |

ASP.NET Core-naplózás támogatást konfigurálásával kapcsolatos további információkért lásd: [fájl konfigurációs beállítások](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#settings-file-configuration).

## <a name="mounts-configuration-settings"></a>Csatlakoztatja a konfigurációs beállítások

A Docker-tárolók biztosítják a Face tervezték is állapot nélküli, és nem módosítható. Más szóval a tárolókon belül létrehozott fájlok vannak tárolva egy írható tároló szintje, amely továbbra is fennáll, miközben csak a tároló fut-e, és nem egyszerűen érhetők el. A tároló leállítása vagy eltávolítja, a létrehozott, az adott tárolóban található fájlok megsemmisül.

Azonban mivel korábban már a Docker-tárolók, is használja a Docker-tárolási lehetőségeket, például kötetek és kötési csatlakoztatása olvasása és írása a tárolón kívüli tárolt adatokat, ha a tároló lehetővé teszi. Adja meg, és a Docker-tároló beállítások kezelésével kapcsolatos további információkért lásd: [adatkezelés a Docker](https://docs.docker.com/storage/).

> [!NOTE]
> Általában nem kell megváltoztatni az ezeket a konfigurációs beállításokat. Ehelyett a tárolót a bemeneti és kimeneti csatlakoztatása megadásakor ezeket a konfigurációs beállításokat célként megadott értékeket fogja használni. Bemeneti és kimeneti csatlakoztatása megadásával kapcsolatos további információkért lásd: [bemeneti és kimeneti csatlakoztatása](#input-and-output-mounts).

A következő táblázat ismerteti a támogatott konfigurációs beállításait a `Mounts` szakaszban.

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Input` | Karakterlánc | A bemeneti csatlakoztatási célját. Az alapértelmezett érték `/input`. |
| `Output` | Karakterlánc | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. |

### <a name="input-and-output-mounts"></a>Bemeneti és kimeneti csatlakoztatása

Alapértelmezés szerint minden tároló támogathatja egy *bemeneti csatlakoztatási*, amely a tároló tudja olvasni azok adatait, és a egy *kimeneti csatlakoztatási*, amely a tároló lehet adatokat írni. Bemeneti vagy kimeneti csatlakoztatása, nem szükséges tárolókat, és a tárolók a Face tároló által támogatott naplózási lehetőségek mellett a tároló-specifikus célokra lehessen felhasználni a bejövő és kimenő csatlakoztatása.

A Face tároló nem támogatja a bemeneti csatlakoztatása, és választhatóan támogatja a kimeneti a csatlakoztatása.

Megadhat egy bemeneti csatlakoztatási vagy csatlakoztatási kimeneti megadásával a `--mount` beállítást a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) letöltött tárolórendszerképet a tároló példányosítása használt parancsot. Alapértelmezés szerint a bemeneti csatlakoztatási használ `/input` , a cél és a kimeneti csatlakoztatási használ `/output` a célként. A Docker-tároló gazdagép számára elérhető bármely Docker tárolási lehetőség segítségével is megadható a `--mount` lehetőséget.

Például a következő parancsot határozza meg a Docker kötési csatlakoztatási a `D:\Output` mappát a gazdagépen, a kimeneti csatlakoztatási majd példányosít egy tárolót a Face tárolórendszerképet, naplófájlok mentése, a kimeneti csatlakoztatási JSON formátumban.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json
  ```

A Face tároló nem használja a bemeneti vagy kimeneti csatlakoztatja képzési vagy adatbázis-adatok tárolására. Ehelyett a Face tároló adattárolási forgatókönyvekhez képzés és az adatbázis-adatok kezeléséhez nyújt. Storage-forgatókönyvekkel kapcsolatos további információkért lásd: [tárolási forgatókönyv beállítások](#storage-scenario-settings).

## <a name="next-steps"></a>További lépések

* Több [Cognitive Services-tárolók](../cognitive-services-container-support.md)