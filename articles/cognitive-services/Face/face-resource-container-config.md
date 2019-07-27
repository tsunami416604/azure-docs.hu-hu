---
title: Tárolók konfigurálása – FACE API
titleSuffix: Azure Cognitive Services
description: Tárolók konfigurációs beállításait.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dapine
ms.openlocfilehash: 6dd047b0ba7f9a123ffcc014cff5604466946d07
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564103"
---
# <a name="configure-face-docker-containers"></a>Arc Docker-tárolók konfigurálása

Az **arc** -tároló futásidejű környezete a `docker run` parancs argumentumai alapján van konfigurálva. Ez a tároló számos kötelező beállítással rendelkezik, és néhány választható beállítás mellett. Több [példák](#example-docker-run-commands) a parancs érhetők el. A tárolóra jellemző beállítások a számlázási beállítások. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), és [ `Eula` ](#eula-setting) beállítások együtt használja, és meg kell adnia az érvényes értékek mindhárom azokat; egyéb a tároló nem indul el. Egy tároló példányosítása a konfigurációs beállítások használatával kapcsolatos további információkért lásd: [számlázási](face-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Konfigurációs beállítás apikey tulajdonsággal végzett tesztelése

A `ApiKey` beállítás határozza meg a számlázási adatokat tároló nyomon követésére használt Azure-erőforrás kulcs. Meg kell adnia egy értéket a ApiKey, és az értéknek érvényes kulcsnak kell lennie  a [`Billing`](#billing-configuration-setting) konfigurációs beállításhoz megadott Cognitive Services erőforráshoz.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** Erőforrás-kezelés a **kulcsok** területen

## <a name="applicationinsights-setting"></a>Applicationinsights – beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfigurációs beállítás

A `Billing` beállítás határozza meg az Azure-beli _Cognitive Services_ erőforrás végpontjának URI-ját, amely a tároló számlázási adatainak mérésére szolgál. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végponti URI-nek kell lennie az Azure-beli _Cognitive Services_ erőforráshoz. A tároló 10 – 15 percen belül jelentést készít a használatról.

Ez a beállítás a következő helyen érhető el:

* Azure Portal: **Cognitive Services** A címkével ellátott áttekintés`Endpoint`

Ne feledje, hogy a példában látható módon hozzáadja az _arc_ -útválasztást a végpont URI-hoz. 

|Kötelező| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Karakterlánc | A számlázás végpont URI azonosítója<br><br>Példa:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

<!-- specific to face only -->

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
  A Face tároló Azure Storage és az Azure Cosmos DB használatával ezek négy típusú adatok szét a tartós tároláshoz. Azure Storage BLOB és üzenetsor adatokat kezeli. A metaadatok és a gyorsítótár-adatok Azure Cosmos DB kezelik. A Face tároló leállt, vagy eltávolítja, ha az adatok a tároló storage-ban tárolt, az Azure Storage és Azure Cosmos DB marad.  
  Az Azure storage-forgatókönyv által használt erőforrások az alábbi további követelményekkel rendelkezik.
  * Az Azure Storage-erőforrást kell használnia a StorageV2 fiók típusa
  * A Azure Cosmos DB erőforrásnak a Azure Cosmos DB API-ját kell használnia a MongoDB

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

A Face containers nem használ bemeneti vagy kimeneti csatlakoztatásokat a képzési és a szolgáltatási adatok tárolásához. 

A gazdagép csatlakoztatási helye a pontos szintaxisa a gazdagép operációs rendszere függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](face-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el, mert a Docker-szolgáltatásfiók és a gazdagép csatlakoztatási helye engedélyekkel kapcsolatos engedélyek ütköznek. 

|Választható| Name (Név) | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Karakterlánc | A Face containers nem használja ezt.|
|Választható| `Output` | Karakterlánc | A kimeneti csatlakoztatási célját. Az alapértelmezett érték `/output`. Ez az a hely a naplófájlok. Ez magában foglalja a tároló naplóit. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa docker-parancsok futtatása 

Az alábbi példák bemutatják, hogyan írhat, és használja a konfigurációs beállítások segítségével `docker run` parancsokat.  Ha fut, a tároló továbbra is fut, amíg ki nem [leállítása](face-how-to-install-containers.md#stop-the-container) azt.

* **Vonal-folytatási karakter**: A következő részben található Docker-parancsok a háttér perjelet `\`használják, mint a sor folytatási karaktere. Cserélje le, vagy távolítsa el ezt a gazdagép operációs rendszerre vonatkozó követelmények alapján. 
* **Argumentumok sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem ismeri a Docker-tárolókat.

Cserélje le a(z)_argument_name_} a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
|{API_KEY} | A Cognitive Services erőforrás Endpoint kulcsa. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{ENDPOINT_URI} | A végpont értéke, beleértve a régiót és az arc-útválasztást.|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](face-how-to-install-containers.md#billing).
> A ApiKey értéke az Azure  `Cognitive Services` Resource Keys oldal kulcsa. 

## <a name="face-container-docker-examples"></a>Face Container Docker-példák

A következő Docker-példák a Face tárolóra vonatkoznak. 

### <a name="basic-example"></a>Alapszintű példa 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Példa naplózásra 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [telepítéséről és a tárolókat futtatják](face-how-to-install-containers.md)
