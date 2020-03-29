---
title: Tárolók konfigurálása - Lap
titleSuffix: Azure Cognitive Services
description: A Face tároló futásidejű környezeta a `docker run` parancsargumentumok használatával van konfigurálva. Vannak mind a szükséges, mind a választható beállítások.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 3c78c9eb85c3a8be236be5c3a24bd877db204b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76167981"
---
# <a name="configure-face-docker-containers"></a>Face Docker-tárolók konfigurálása

A **Face** tároló futásidejű környezeta a `docker run` parancsargumentumok használatával van konfigurálva. Ez a tároló számos szükséges beállítással rendelkezik, valamint néhány választható beállítással. A parancsra több [példa](#example-docker-run-commands) is elérhető. A tároló-specifikus beállítások a számlázási beállításokat. 

## <a name="configuration-settings"></a>Konfigurációs beállítások

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> A [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) és a beállítások együtt vannak használva, és mindháromhoz érvényes értékeket kell megadnia; ellenkező esetben a tartály nem indul el. A tároló konstiniformatika érdekében ezeknek a konfigurációs beállításoknak a használatával kapcsolatos további [tudnivalókért olvassa el a Számlázás című témakört.](face-how-to-install-containers.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey konfigurációs beállítása

A `ApiKey` beállítás megadja az Azure erőforráskulcs a tároló számlázási adatainak nyomon követéséhez. Meg kell adnia egy értéket az ApiKey és az érték érvényes kulcsnak [`Billing`](#billing-configuration-setting) kell lennie a konfigurációs beállításhoz megadott Cognitive Services-erőforráshoz. _Cognitive Services_

Ez a beállítás a következő helyen található:

* Azure portal: **Cognitive Services** Erőforrás-kezelés, **a Kulcsok** csoportban

## <a name="applicationinsights-setting"></a>ApplicationInsights beállítás

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Számlázási konfiguráció beállítása

A `Billing` beállítás adja meg a végpont URI a _Cognitive Services-erőforrás_ az Azure-ban a tároló számlázási adatainak mérésére használt. Meg kell adnia egy értéket ehhez a konfigurációs beállításhoz, és az értéknek érvényes végpontURI-nak kell lennie az _Azure-beli Cognitive_ Services-erőforráshoz. A tároló 10–15 percenként jelenti a használatot.

Ez a beállítás a következő helyen található:

* Azure portal: **Kognitív szolgáltatások** áttekintése, címkével`Endpoint`

Ne felejtse el hozzáadni a _Face_ útválasztást a végpont URI-hoz a példában látható módon. 

|Kötelező| Név | Adattípus | Leírás |
|--|------|-----------|-------------|
|Igen| `Billing` | Sztring | Számlázási végpont URI. A számlázási URI beszerzésével kapcsolatos további információkért [lásd: A szükséges paraméterek összegyűjtése.](face-how-to-install-containers.md#gathering-required-parameters) További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](../cognitive-services-custom-subdomains.md) |

<!-- specific to face only -->

## <a name="cloudai-configuration-settings"></a>CloudAI konfigurációs beállítások

A szakasz konfigurációs beállításai a `CloudAI` tárolóra jellemző tárolóspecifikus beállításokat biztosítanak. A következő beállítások és objektumok támogatottak `CloudAI` a metszetben lévő Face tárolóhoz

| Név | Adattípus | Leírás |
|------|-----------|-------------|
| `Storage` | Objektum | A Face tároló által használt tárolási forgatókönyv. Az objektum tárolási forgatókönyveiről és `Storage` a kapcsolódó beállításokról a [Tárolási forgatókönyv beállításai című](#storage-scenario-settings) témakörben olvashat bővebben. |

### <a name="storage-scenario-settings"></a>Tárolási forgatókönyv beállításai

A Face tároló tárolja blob, gyorsítótár, metaadatok és várólista adatok, attól függően, hogy mi van tárolva. Például a betanítási indexek és eredmények egy nagy személy csoport blob adatok ként tárolja. A Face tároló két különböző tárolási forgatókönyvet biztosít az ilyen típusú adatokkal való interakció és tárolás során:

* Memory (Memória)  
  Mind a négy adattípus a memóriában tárolódik. Nem terjesztik őket, és nem is állandóak. Ha a Face tároló levan állítva vagy eltávolítja, a tároló összes tárolóban lévő adata megsemmisül.  
  Ez a Face tároló alapértelmezett tárolási forgatókönyve.
* Azure  
  A Face tároló az Azure Storage és az Azure Cosmos DB használatával osztja el ezt a négy típusú adatokat az állandó tároló között. A blob- és várólista-adatokat az Azure Storage kezeli. Metaadatok és gyorsítótár-adatok kezelése az Azure Cosmos DB. Ha a Face-tároló levan állítva vagy eltávolítva, az adott tároló összes tárolóban tárolt összes adat az Azure Storage és az Azure Cosmos DB tárolja.  
  Az Azure storage-forgatókönyv által használt erőforrások a következő további követelményekkel rendelkeznek
  * Az Azure Storage-erőforrásnak a StorageV2 fiók-fajtát kell használnia
  * Az Azure Cosmos DB-erőforrásnak az Azure Cosmos DB MongoDB-hoz való API-ját kell használnia

A tárolási forgatókönyveket és a kapcsolódó `Storage` konfigurációs `CloudAI` beállításokat az objektum kezeli a konfigurációs szakaszban. Az objektumban a `Storage` következő konfigurációs beállítások érhetők el:

| Név | Adattípus | Leírás |
|------|-----------|-------------|
| `StorageScenario` | Sztring | A tároló által támogatott tárolási forgatókönyv. A következő értékek érhetők el<br/>`Memory`- Alapértelmezett érték. A tároló nem állandó, nem elosztott és memórián belüli tárolót használ egycsomópontos, ideiglenes használatra. Ha a tárolót leállítják vagy eltávolítják, a tároló tárolója megsemmisül.<br/>`Azure`- A tároló Azure-erőforrásokat használ a tároláshoz. Ha a tárolólevan állítva vagy eltávolítva, a tároló tárolója megmarad.|
| `ConnectionStringOfAzureStorage` | Sztring | A tároló által használt Azure Storage-erőforrás kapcsolati karakterlánca.<br/>Ez a beállítás `Azure` csak akkor `StorageScenario` érvényes, ha meg van adva a konfigurációs beállításhoz. |
| `ConnectionStringOfCosmosMongo` | Sztring | A MongoDB kapcsolati karakterlánc az Azure Cosmos DB erőforrás a tároló által használt.<br/>Ez a beállítás `Azure` csak akkor `StorageScenario` érvényes, ha meg van adva a konfigurációs beállításhoz. |

Például a következő parancs megadja az Azure storage-forgatókönyvet, és mintakapcsolati karakterláncokat biztosít az Azure Storage és a Cosmos DB-erőforrások a Face tároló adatainak tárolására.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

A tárolási forgatókönyv a bemeneti csatolóktól és a kimeneti csatlakoztatásoktól elkülönítve történik. Ezeknek a szolgáltatásoknak a kombinációját egyetlen tárolóhoz adhatja meg. A következő parancs például egy Docker-kötéscsatlakoztatást határoz meg a `D:\Output` gazdagép mappájához kimeneti csatlakoztatásként, majd példányosít ja a tárolót a Face container rendszerképből, és json formátumú naplófájlokat ment a kimeneti csatlakoztatásba. A parancs az Azure storage-forgatókönyvet is meghatározza, és mintakapcsolati karakterláncokat biztosít az Azure Storage és a Cosmos DB-erőforrások hoz a Face container adatok tárolására.

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 --mount type=bind,source=D:\Output,destination=/output containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789 Logging:Disk:Format=json CloudAI:Storage:StorageScenario=Azure CloudAI:Storage:ConnectionStringOfCosmosMongo="mongodb://samplecosmosdb:0123456789@samplecosmosdb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb" CloudAI:Storage:ConnectionStringOfAzureStorage="DefaultEndpointsProtocol=https;AccountName=sampleazurestorage;AccountKey=0123456789;EndpointSuffix=core.windows.net"
  ```

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

A Face-tárolók nem használnak bemeneti vagy kimeneti csatlakoztatásokat a betanítási vagy szolgáltatásadatok tárolására. 

A gazdagép csatlakoztatási helyének pontos szintaxisa a gazdaoperációs rendszertől függően változik. Emellett előfordulhat, hogy a [gazdaszámítógép](face-how-to-install-containers.md#the-host-computer)csatlakoztatási helye nem érhető el a Docker-szolgáltatásfiók által használt engedélyek és a gazdagép csatlakoztatási helyének engedélyei közötti ütközés miatt. 

|Optional| Név | Adattípus | Leírás |
|-------|------|-----------|-------------|
|Nem engedélyezett| `Input` | Sztring | Az arctárolók nem használják ezt.|
|Optional| `Output` | Sztring | A kimeneti csatlakoztatás célja. Az alapértelmezett érték `/output`. Ez a rönkök helye. Ez magában foglalja a tárolónaplókat is. <br><br>Példa:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Példa a docker-futtatási parancsokra 

Az alábbi példák a konfigurációs beállításoksegítségével `docker run` szemléltetik a parancsok írását és használatát.  Futás után a tároló addig fut, amíg le nem [állítja.](face-how-to-install-containers.md#stop-the-container)

* **Sor-folytatás karakter**: A Docker-parancsok a következő `\`szakaszokban a fordított perjelet használják, vonalfolytatási karakterként. Cserélje le vagy távolítsa el ezt a gazdaoperációs rendszer követelményei nek megfelelően. 
* **Argumentum sorrendje**: Ne módosítsa az argumentumok sorrendjét, hacsak nem nagyon ismeri a Docker-tárolókat.

Cserélje le {_argument_name_} -t a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | Az erőforrás végpontkulcsa az `Face` `Face` Azure Keys lapon. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | A számlázási végpont értéke elérhető `Face` az Azure Áttekintés oldalon.| Lásd: [a szükséges paraméterek összegyűjtése](face-how-to-install-containers.md#gathering-required-parameters) explicit példákat. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` a lehetőséget és a beállításokat meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információ: [Billing](face-how-to-install-containers.md#billing).
> Az ApiKey érték **Key** a kulcs `Cognitive Services` az Azure Resource keys lapról. 

## <a name="face-container-docker-examples"></a>Face container Docker példák

Az alábbi Docker-példák az arctárolóhoz tartoznak. 

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

* Tekintse át [a tárolók telepítésének és futtatásának áttekintését](face-how-to-install-containers.md)
