---
title: Az Azure IoT Edge-eszközök – a blokkblobok Store |} A Microsoft Docs
description: Megismerheti a rétegzési és élő idő funkciók, tekintse meg a támogatott blob storage-műveletek és csatlakozhat a blob storage-fiókjában.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 396af2dfd9fc53c080163a27e376328c1369d5e1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65991473"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Az Azure Blob Storage a peremhálózaton data Store az IoT Edge-ben (előzetes verzió)

Az IoT Edge-ben az Azure Blob Storage biztosít egy [blokkblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) tárolási megoldás a peremhálózaton. Az Azure block blob szolgáltatás viselkedik az IoT Edge-eszközön a blob storage-modulban, de a blokkblobok használatát támogatják a rendszer helyben tárolja az IoT Edge-eszközön. Ugyanazokkal a módszerekkel az Azure storage SDK-t a blobok elérése, vagy a blob API-hívás, amely már jártas letiltása.

> [!NOTE]
> Az Azure Blob Storage, az IoT Edge-ben van [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a modul tartalmaz **rétegezést** és **time-to-live** funkciókat.

> [!NOTE]
> Jelenleg rétegezést és az idő-to-live funkció a Linux-AMD64 és a Linux ARM32 csak érhetők el.

**Rétegezést** van egy konfigurálható funkcióval rendelkezik, amely lehetővé teszi, hogy automatikusan tölti fel az adatok a helyi blob storage-ból az Azure-bA időszakos internetes kapcsolat támogatást. Lehetővé teszi:

- Kapcsolja be-/ kikapcsolási a rétegezési szolgáltatás
- Válassza ki a sorrendet, amelyben az adatokat másolja az Azure-bA mint NewestFirst vagy OldestFirst
- Adja meg az Azure Storage-fiókot, amely a feltöltött adatok kívánja.
- Adja meg a tárolók az Azure-bA feltöltendő. Ez a modul lehetővé teszi a forrás és a cél a tároló nevének megadását.
- Teljes blob rétegezés (használatával `Put Blob` művelet) és szolgáltatói rétegezés letiltása (használatával `Put Block` és `Put Block List` műveletek).

Ez a modul használja letiltása szintű rétegzést, ha az a blob blokkokat tartalmaz. Íme néhány gyakori helyzetek:

- Az alkalmazás egyes blokkolja a korábban feltöltött blob frissíti, ez a modul csak a frissített blokkok és nem a teljes blob feltölt.
- A modul blob van feltöltésével, és internetkapcsolat eltűnik, az internetkapcsolat esetén vissza újra feltölti a fennmaradó blokkok és nem a teljes blob.

Egy váratlan folyamatmegszakítás (például áramkimaradás) egy blob feltöltése közben történik, ha minden blokkok, amelyek korábban a feltöltés esedékes lesz feltöltve újra, ha a modul visszatér online állapotba.

**Time-to-live** (TTL) olyan egy konfigurálható szolgáltatások, ahol a modul automatikusan törli a blobokat a helyi tárolóból amikor lejár a megadott időtartam (percben). TTL lehetővé teszi:

- Kapcsolja be-/ kikapcsolási a rétegezési szolgáltatás
- Adja meg az élettartam percben

Forgatókönyvek, ahol adatokat, például videók, képek, kampányfinanszírozási adatok szórakoztató, kórházi adatok vagy adatok helyben, később kell tárolni, amelyek helyileg sikerült feldolgozni, vagy jó példák töltődnek fel a felhőbe, ez a modul.

Ez a cikk ismerteti, hogyan telepíthető egy Azure Blob Storage, blob szolgáltatás az IoT Edge-eszközön futó IoT Edge-tárolón.

> [!NOTE]
> A "auto-lejárati" a videó használt használati feltételek "automatikus rétegezést" helyett "rétegezési" és a "time-to-live".

A cikk gyors ismertetést nyújt a videó megtekintése
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

- Használhatja a fejlesztői gépén vagy egy virtuális gép IoT Edge-eszköz esetében ez a rövid útmutató lépéseit követve [Linux](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md).

- Az Azure Blob Storage, az IoT Edge-modul a következő eszköz konfigurációkat támogatja:

  | Operációs rendszer | Architektúra |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Core (. októberi frissítés) | AMD64 |
  | A Windows 10 IoT Enterprise (. októberi frissítés) | AMD64 |
  | A Windows Server 2019 | AMD64 |
  | A stretch Raspbian | ARM32 |

Felhőerőforrások:

Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

## <a name="tiering-and-time-to-live-properties"></a>Rétegzési és élő idő tulajdonságai

Használni kívánt rétegezést beállításához és a time-to-live tulajdonságai. Ezeket üzembe helyezés során vagy később módosítani az ikermodul ismételt üzembe helyezése nélkül szerkesztésével. Javasoljuk, hogy a "Ikermodul" ellenőrzése a `reported configuration` és `configurationValidation` , hogy megfelelően propagálva értékeket.

### <a name="tiering-properties"></a>Rétegezési tulajdonságai

Ez a beállítás neve `tieringSettings`

| Mező | Lehetséges értékek | Magyarázat |
| ----- | ----- | ---- |
| tieringOn | IGAZ, hamis | Alapértelmezés szerint van beállítva `false`, ha azt szeretné a értékre `true`|
| backlogPolicy | NewestFirst, OldestFirst | Lehetővé teszi, hogy kiválaszthatja a sorrendet, amelyben az adatokat az Azure-ba másolja. Alapértelmezés szerint van beállítva `OldestFirst`. A sorrend határozza meg BLOB utolsó módosítás időpontja |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` kapcsolati karakterlánc, amely lehetővé teszi, hogy adja meg az Azure Storage-fiók, amelybe az adatok feltöltése. Adja meg `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Adja hozzá a megfelelő EndpointSuffix az Azure, hol fogja feltöltött adatmennyiség, a globális Azure, Government Azure és a Microsoft Azure Stack változik. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Az Azure-bA feltöltendő a tároló nevének megadását teszi lehetővé. Ez a modul lehetővé teszi a forrás és a cél a tároló nevének megadását. Ha nem adja meg a tároló nevét, akkor automatikusan hozzárendeli a tároló neve megegyezik `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. A célként megadott Tárolónév sablon karakterláncok létrehozása, tekintse meg a lehetséges értékek oszlopa. <br>* %h -> az IoT Hub nevére (3 – 50 karakter). <br>* %d IoT-eszköz azonosítója (1. 129 karakternél) ->. <br>* %m -> modul neve (1 – 64 karakter). <br>* %c -> Forrástároló neve (3 – 63 karakter). <br><br>A tároló nevének maximális mérete automatikusan hozzárendelése során a tároló nevét, ha a tároló mérete meghaladja azt fogja trim 63 karakter minden szakasz (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) – 15 karakter 63 karakter. |

### <a name="time-to-live-properties"></a>Time-to-live tulajdonságai

Ez a beállítás neve `ttlSettings`

| Mező | Lehetséges értékek | Magyarázat |
| ----- | ----- | ---- |
| ttlOn | IGAZ, hamis | Alapértelmezés szerint van beállítva `false`, ha azt szeretné a értékre `true`|
| timeToLiveInMinutes | `<minutes>` | Adja meg az élettartam percben. A modul automatikusan törli a blobokat a helyi tárolóból TTL lejárata |

## <a name="configure-log-files"></a>Naplófájlok konfigurálása

Információk a naplófájlokat a modul konfigurálása: ezek [éles környezetben ajánlott eljárások](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Csatlakozás a blob storage-modulban

A fiók neve és a fiókkulcsot, hogy konfigurálta-e a modul az IoT Edge-eszközön a blobtároló elérésére használható.

Adja meg a tárolási blob végpontja az IoT Edge-eszköz végrehajtott módosítások hozzá kérelmeket. Is [hozzon létre egy kapcsolati karakterláncot egy explicit storage-végpont](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) az IoT Edge-eszköz adatait és a beállított fiók nevét.

- Hol futnak az Azure Blob Storage, az IoT Edge-modul, az adott eszközön üzembe helyezett modulok, a blob végpontja van: `http://<module name>:11002/<account name>`.
- Egy másik eszközön, mint ahol az Azure Blob Storage, az IoT Edge-modul fut, majd attól függően, hogy a telepítő a blob üzembe helyezett modulok végpontja az alábbiak egyike:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>A rövid útmutató Azure Blob Storage-minták

Az Azure Blob Storage dokumentációja tartalmazza az rövid útmutatók, amelyek számos nyelven mintakódot. Ezek a minták teszteléséhez az IoT Edge-ben az Azure Blob Storage a blob végpontja szeretne csatlakozni a blob storage-modulban módosításával futtathatja.

Az alábbi rövid útmutatókat használja, így sikerült telepítheti őket, IoT Edge-modulok mellett a blob storage-modulban is IoT Edge által támogatott nyelvek:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Csatlakozás a helyi tárolóba az Azure Storage Explorerrel

Használhat **Azure Storage Explorer** a helyi storage-fiókhoz való csatlakozáshoz. Ez a lehetőség csak a [Azure Storage Explorer verziója 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> A következő lépésekkel, például egy kapcsolat hozzáadása a helyi storage-fiók vagy helyi tárfiókban lévő tárolók létrehozása közben hibák léphetnek fel. Hagyja figyelmen kívül, és frissítse.

1. Töltse le és telepítse az Azure Storage Explorerrel

1. Kapcsolódás az Azure Storage kapcsolati karakterlánc használatával

1. Adja meg a kapcsolati karakterlánc: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Próbálja ki való csatlakozás lépéseit.

1. Tároló belül a helyi storage-fiók létrehozása

1. Blokkblobok formájában fájlok feltöltésének megkezdése.
   > [!NOTE]
   > Ez a modul nem támogatja a lapblobokat.

1. Ha szeretné, csatlakozzon az Azure storage-fiókok, ahol tölt fel az adatokat. Biztosít egy egyetlen nézetben a helyi tárfiók mind az Azure storage-fiók

## <a name="supported-storage-operations"></a>Támogatott tárolási műveletek

BLOB storage-modulok IoT Edge ugyanazt az Azure Storage SDK-k használata, és konzisztens block blob végpontok az Azure Storage API 2017-04-17-es verziójával. Újabb kiadásaiban ügyfelek igényeitől függnek.

Nem minden Azure Blob Storage-műveletek az IoT Edge-ben az Azure Blob Storage által támogatott, mert ez a szakasz felsorolja az egyes állapota.

### <a name="account"></a>Fiók

Támogatott:

- Tárolók listázása

Nem támogatott:

- GET, és állítsa be a blob szolgáltatás tulajdonságai
- Kérelem előzetes blob
- Blob szolgáltatás statisztikáiról beolvasása
- Fiók adatainak beolvasása

### <a name="containers"></a>Containers

Támogatott:

- Hozzon létre, és a tároló törlése
- A tároló tulajdonságainak és metaadatainak lekérése
- Blobok listázása
- GET, és állítsa be a tároló ACL
- Set-tároló metaadatai

Nem támogatott:

- Címbérlet-tárolók

### <a name="blobs"></a>Blobok

Támogatott:

- PUT, letölthet vagy törölhet blob
- Első és a blob tulajdonságainak beállítása
- GET, és állítsa be a blob metaadatai

Nem támogatott:

- Címbérleti blobhoz
- Blob pillanatkép
- Másolja ki és a blob másolásához megszakítása
- Blob törlésének visszavonása
- Blobszint beállítása

### <a name="block-blobs"></a>Blokkblobok

Támogatott:

- PUT letiltása
- PUT és a blokk-lista lekérése

Nem támogatott:

- Blokk PUT URL-címről

## <a name="feedback"></a>Visszajelzés

Visszajelzése fontos számunkra, hogy ez a modul és a szolgáltatások hasznos és könnyen használható. Ossza meg velünk tapasztalatait, és tudassa velünk, hogyan tehetjük gördülékenyebbé.

Minket érheti el absiotfeedback@microsoft.com

## <a name="next-steps"></a>További lépések

Tudjon meg többet [üzembe helyezése az Azure Blob Storage az IoT Edge-ben](how-to-deploy-blob.md)
