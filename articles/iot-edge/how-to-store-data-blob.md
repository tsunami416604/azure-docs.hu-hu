---
title: Az Azure IoT Edge-eszközök – a blokkblobok Store |} A Microsoft Docs
description: Megismerheti a rétegzési és élő idő funkciók, tekintse meg a támogatott blob storage-műveletek és csatlakozhat a blob storage-fiókjában.
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: dabaa06e224c6498c0080c4546c04f40e3919bb6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448531"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Az Azure Blob Storage a peremhálózaton data Store az IoT Edge-ben (előzetes verzió)

Az IoT Edge-ben az Azure Blob Storage biztosít egy [blokkblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) tárolási megoldás a peremhálózaton. Az Azure block blob szolgáltatás viselkedik az IoT Edge-eszközön a blob storage-modulban, de a blokkblobok használatát támogatják a rendszer helyben tárolja az IoT Edge-eszközön. Ugyanazokkal a módszerekkel az Azure storage SDK-t a blobok elérése, vagy a blob API-hívás, amely már jártas letiltása.

Ez a modul tartalmaz **deviceToCloudUpload** és **deviceAutoDelete** funkciókat.
> [!NOTE]
> Az Azure Blob Storage, az IoT Edge-ben van [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A cikk gyors ismertetést nyújt a videó megtekintése
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

**deviceToCloudUpload** van egy konfigurálható funkcióval rendelkezik, amely lehetővé teszi, hogy automatikusan tölti fel az adatok a helyi blob storage-ból az Azure-bA időszakos internetes kapcsolat támogatást. Lehetővé teszi:

- Kapcsolja be-/ kikapcsolási a deviceToCloudUpload funkciót.
- Válassza ki azt a sorrendet, amelyben az adatokat az Azure-bA mint NewestFirst vagy OldestFirst másolja.
- Adja meg az Azure Storage-fiókot, amely a feltöltött adatok kívánja.
- Adja meg a tárolók az Azure-bA feltöltendő. Ez a modul lehetővé teszi a forrás és a cél a tároló nevének megadását.
- Válassza ki a felhőbeli tárhelyén való feltöltés befejeződése után azonnal törli a blobokat lehetősége
- Blob feltöltése teljes (használatával `Put Blob` művelet) és szolgáltatói feltöltésének blokkolása (használatával `Put Block` és `Put Block List` műveletek).

Ez a modul szintű feltöltésének blokkolása használja, amikor a blokkok áll a blob. Íme néhány gyakori helyzetek:

- Az alkalmazás egyes blokkolja a korábban feltöltött blob frissíti, ez a modul csak a frissített blokkok és nem a teljes blob feltölt.
- A modul blob van feltöltésével, és internetkapcsolat eltűnik, az internetkapcsolat esetén vissza újra feltölti a fennmaradó blokkok és nem a teljes blob.

Egy váratlan folyamatmegszakítás (például áramkimaradás) egy blob feltöltése közben történik, ha minden blokkok, amelyek korábban a feltöltés esedékes lesz feltöltve újra, ha a modul visszatér online állapotba.

**deviceAutoDelete** konfigurálható funkció van, ahol a modul automatikusan törli a blobokat a helyi tárolóból amikor lejár a megadott időtartam (percben). Lehetővé teszi:

- Kapcsolja be-/ kikapcsolási a deviceAutoDelete funkciót.
- Adja meg az az idő percben (deleteAfterMinutes) után, amelyek a blobok automatikusan törölve lesznek.
- Válassza ki a is megőrizheti a blob, miközben tölti fel, ha deleteAfterMinutes érték lejár.

Forgatókönyvek, ahol adatokat, például videók, képek, kampányfinanszírozási adatok szórakoztató, kórházi adatok vagy adatok helyben, később kell tárolni, amelyek helyileg sikerült feldolgozni, vagy jó példák töltődnek fel a felhőbe, ez a modul.

Ez a cikk ismerteti az Azure Blob Storage-blob szolgáltatás az IoT Edge-eszközön futó IoT Edge-tárolón kapcsolatos fogalmakat.

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

- Használhatja a fejlesztői gépén vagy egy virtuális gép IoT Edge-eszköz esetében ez a rövid útmutató lépéseit követve [Linux](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md).

- Az Azure Blob Storage, az IoT Edge-modul a következő eszköz konfigurációkat támogatja:

  | Operációs rendszer | Architektúra |
  | ---------------- | ----- | ----- |
  | Ubuntu Server 16.04 | AMD64 |
  | Ubuntu Server 18.04 | AMD64 |
  | Windows 10 IoT Enterprise | AMD64 |
  | A Windows Server 2019 | AMD64 |
  | A stretch Raspbian | ARM32 |

Felhőerőforrások:

Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload és deviceAutoDelete tulajdonságai

Kívánt tulajdonságok használatával állítsa be a deviceToCloudUploadProperties és deviceAutoDeleteProperties. Ezeket üzembe helyezés során vagy később módosítani az ikermodul ismételt üzembe helyezése nélkül szerkesztésével. Javasoljuk, hogy a "Ikermodul" ellenőrzése a `reported configuration` és `configurationValidation` , hogy megfelelően propagálva értékeket.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Ez a beállítás neve `deviceToCloudUploadProperties`

| Mező | Lehetséges értékek | Magyarázat | Környezeti változó |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | Alapértelmezés szerint van beállítva `false`, ha azt szeretné a értékre `true`| `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Lehetővé teszi, hogy kiválaszthatja a sorrendet, amelyben az adatokat az Azure-ba másolja. Alapértelmezés szerint van beállítva `OldestFirst`. A sorrend határozza meg BLOB utolsó módosítás időpontja | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` kapcsolati karakterlánc, amely lehetővé teszi, hogy adja meg az Azure Storage-fiók, amelybe az adatok feltöltése. Adja meg `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Adja hozzá a megfelelő EndpointSuffix az Azure, hol fogja feltöltött adatmennyiség, a globális Azure, Government Azure és a Microsoft Azure Stack változik. | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Az Azure-bA feltöltendő a tároló nevének megadását teszi lehetővé. Ez a modul lehetővé teszi a forrás és a cél a tároló nevének megadását. Ha nem adja meg a tároló nevét, akkor automatikusan hozzárendeli a tároló neve megegyezik `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. A célként megadott Tárolónév sablon karakterláncok létrehozása, tekintse meg a lehetséges értékek oszlopa. <br>* %h -> az IoT Hub nevére (3 – 50 karakter). <br>* %d IoT Edge-eszköz azonosítója (1. 129 karakternél) ->. <br>* %m -> modul neve (1 – 64 karakter). <br>* %c -> Forrástároló neve (3 – 63 karakter). <br><br>A tároló nevének maximális mérete 63 karakter automatikus hozzárendelése során a tároló nevét, ha a tároló mérete meghaladja azt fogja trim 63 karakter minden szakasz (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) 15-re karakter. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | Alapértelmezés szerint van beállítva `false`. Ha van beállítva `true`, azt automatikusan törli az adatok felhőbeli tárhelyén való feltöltés befejeződött | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Ez a beállítás neve `deviceAutoDeleteProperties`

| Mező | Lehetséges értékek | Magyarázat | Környezeti változó |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | Alapértelmezés szerint van beállítva `false`, ha azt szeretné a értékre `true`| `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Adja meg az idő percben. A modul automatikusan törli a blobokat a helyi tárolóból ezt az értéket lejártakor | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Alapértelmezés szerint van beállítva `true`, és megőrzi a blob bár azt tölti fel a tárolási Ha deleteAfterMinutes lejár. Beállíthatja `false` és törli az adatokat, amint deleteAfterMinutes lejár. Megjegyzés: Ez a tulajdonság uploadOn működéséhez meg kell igaz értékre| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="configure-log-files"></a>Naplófájlok konfigurálása

Információk a naplófájlokat a modul konfigurálása: ezek [éles környezetben ajánlott eljárások](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Csatlakozás a blob storage-modulban

A fiók neve és a fiókkulcsot, hogy konfigurálta-e a modul az IoT Edge-eszközön a blobtároló elérésére használható.

Adja meg a tárolási blob végpontja az IoT Edge-eszköz végrehajtott módosítások hozzá kérelmeket. Is [hozzon létre egy kapcsolati karakterláncot egy explicit storage-végpont](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) az IoT Edge-eszköz adatait és a beállított fiók nevét.

- Hol futnak az Azure Blob Storage, az IoT Edge-modul, az adott eszközön üzembe helyezett modulok, a blob végpontja van: `http://<module name>:11002/<account name>`.
- Külső modulok vagy az alkalmazásokat futtató egy másik eszközön, mint ahol az Azure Blob Storage, az IoT Edge-modul fut, majd attól függően, hogy a hálózat beállítása úgy, hogy az adatforgalom, a külső modulban vagy alkalmazás érhető el az eszközt IoT Edge-modul az Azure Blob Storage futtatja, a blob végpontja az alábbiak egyike:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>A rövid útmutató Azure Blob Storage-minták

Az Azure Blob Storage-dokumentáció több nyelven is rövid mintakód tartalmazza. Ezek a minták teszteléséhez az IoT Edge-ben az Azure Blob Storage a blob végpontja szeretne csatlakozni a helyi blob storage-modulban módosításával futtathatja.

A következő rövid minták használja, így sikerült telepítheti őket, IoT Edge-modulok mellett a blob storage-modulban is IoT Edge által támogatott nyelvek:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Csatlakozás a helyi tárolóba az Azure Storage Explorerrel

Használhat [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a helyi storage-fiókhoz való csatlakozáshoz.

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

## <a name="release-notes"></a>Kibocsátási megjegyzések

Az alábbiakban a [kibocsátási megjegyzések a docker hubon](https://hub.docker.com/_/microsoft-azure-blob-storage) a modulhoz

## <a name="feedback"></a>Visszajelzés

Visszajelzése fontos számunkra, hogy ez a modul és a szolgáltatások hasznos és könnyen használható. Ossza meg velünk tapasztalatait, és tudassa velünk, hogyan tehetjük gördülékenyebbé.

Minket érheti el absiotfeedback@microsoft.com

## <a name="next-steps"></a>További lépések

Tudjon meg többet [üzembe helyezése az Azure Blob Storage az IoT Edge-ben](how-to-deploy-blob.md)
