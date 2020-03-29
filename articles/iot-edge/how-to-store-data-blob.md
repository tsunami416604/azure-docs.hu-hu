---
title: Blokkblobok tárolása az eszközökön - Azure IoT Edge | Microsoft dokumentumok
description: Ismerje meg a rétegezést és az élő funkciókat, tekintse meg a támogatott blobstorage-műveleteket, és csatlakozzon a blobstorage-fiókhoz.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509818"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Adatok tárolása a peremhálózaton az Azure Blob Storage az IoT Edge-ben segítségével

Az Azure Blob Storage az IoT [append blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Edge-en egy [blokkblob-tárolót](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) biztosít a peremhálózaton. Az IoT Edge-eszközön lévő blobstorage-modul úgy viselkedik, mint egy Azure blobszolgáltatás, kivéve, hogy a blobok helyileg vannak tárolva az IoT Edge-eszközön. A blobok eléréséhez az azure storage SDK-metódusok vagy blob API-hívások, amelyek már megszokták. Ez a cikk ismerteti az Azure Blob Storage-hoz kapcsolódó fogalmak ioT Edge-tároló, amely egy blob szolgáltatás fut az IoT Edge-eszközön.

Ez a modul a következő esetekben hasznos:

* ahol az adatokat helyileg kell tárolni, amíg fel nem dolgozhatók vagy át nem vihetők a felhőbe. Ezek az adatok lehetnek videók, képek, pénzügyi adatok, kórházi adatok vagy bármely más strukturálatlan adat.
* ha az eszközök korlátozott kapcsolattal rendelkező helyen találhatók.
* ha azt szeretné, hogy hatékonyan dolgozza fel az adatokat helyileg, hogy alacsony késésű hozzáférést az adatokhoz, úgy, hogy a lehető leggyorsabban reagálhat a vészhelyzetekre.
* ha csökkenteni szeretné a sávszélesség-költségeket, és el szeretné kerülni a terabájtnyi adat átvitelét a felhőbe. Az adatokat helyileg is feldolgozhatja, és csak a feldolgozott adatokat küldheti el a felhőbe.

Nézze meg a videót a gyors bevezetéshez
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Ez a modul **jön-val készülékToCloudUpload** és **deviceAutoDelete** funkciókat.

**deviceToCloudUpload** egy konfigurálható funkció. Ez a funkció automatikusan feltölti az adatokat a helyi blob storage az Azure-ba szakaszos internetkapcsolat támogatás. Ez lehetővé teszi, hogy:

* Kapcsolja be/ki a készüléketToCloudUpload funkciót.
* Válassza ki az adatok Azure-ba másolásának sorrendjét, például a NewestFirst vagy a LegrégebbiElő.
* Adja meg azt az Azure Storage-fiókot, amelyre az adatokat fel szeretné tölteni.
* Adja meg az Azure-ba feltölteni kívánt tárolókat. Ez a modul lehetővé teszi a forrás- és céltárolónevek megadását.
* Válassza ki a blobok azonnali törlését, miután a felhőalapú tárhelyre való feltöltés befejeződött
* Végezze el a `Put Blob` teljes blob feltöltést `Put Block`(a művelet használatával) és a blokkszintű feltöltést (a használatával `Put Block List` és `Append Block` a műveletekkel).

Ez a modul blokkszintű feltöltést használ, amikor a blob blokkokból áll. Íme néhány gyakori forgatókönyv:

* Az alkalmazás frissíti a korábban feltöltött blokkblob néhány blokkját, vagy új blokkokat fűz hozzáfűzésblobhoz, ez a modul csak a frissített blokkokat tölti fel, és nem a teljes blobot.
* A modul feltölti blob és az internet-kapcsolat eltűnik, amikor a kapcsolat ismét újra feltölti csak a fennmaradó blokkokat, és nem az egész blob.

Ha egy blob feltöltése során váratlan folyamatmegszakítás (például áramkimaradás) történik, a feltöltéshez esedékes összes blokk újra feltöltődik, amint a modul újra online állapotba kerül.

**deviceAutoDelete** egy konfigurálható funkció. Ez a funkció automatikusan törli a blobokat a helyi tárolóból, amikor a megadott időtartam (percben mérve) lejár. Ez lehetővé teszi, hogy:

* Kapcsolja be/ki az eszközAutomatikus törlés szolgáltatást.
* Adja meg az időt percben (deleteAfterMinutes), amely után a blobok automatikusan törlődnek.
* Válassza ki a blob megtartására, miközben feltölti, ha a deleteAfterMinutes érték lejár.

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* A fejlesztői gépet vagy egy virtuális gépet IoT Edge-eszközként használhatja a [Linux](quickstart-linux.md) vagy Windows eszközök gyorshasználatának [lépéseit](quickstart.md)követve.

* A támogatott operációs rendszerek és architektúrák listáját az [Azure IoT Edge által támogatott rendszerek](support.md#operating-systems) ben található. Az Azure Blob Storage az IoT Edge modul a következő architektúrákat támogatja:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (előzetes verzió)

Felhőerőforrások:

Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload és deviceAutoDelete tulajdonságok

Használja a modul kívánt tulajdonságait, hogy állítsa **eszközToCloudUploadProperties** és **deviceAutoDeleteProperties**. A kívánt tulajdonságok at lehet beállítani az üzembe helyezés során, vagy később módosítható a modul iker szerkesztésével, anélkül, hogy újra telepíteni. Javasoljuk, `configurationValidation` hogy ellenőrizze a `reported configuration` "Module Twin" és győződjön meg arról, értékek megfelelően propagált.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

A beállítás neve `deviceToCloudUploadProperties`. Ha az IoT Edge-szimulátort használja, állítsa be az értékeket a kapcsolódó környezeti változók hoz ezekhez a tulajdonságokhoz, amelyek a magyarázat szakaszban találhatók.

| Tulajdonság | Lehetséges értékek | Magyarázat |
| ----- | ----- | ---- |
| feltöltéson | igaz, hamis | Alapértelmezés `false` szerint be van állítva. Ha be szeretné kapcsolni a funkciót, `true`állítsa ezt a mezőt a -ra. <br><br> Környezeti változó:`deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, LegrégebbiElső | Lehetővé teszi az adatok Azure-ba másolásának sorrendjét. Alapértelmezés `OldestFirst` szerint be van állítva. A sorrendet a Blob utolsó módosítási ideje határozza meg. <br><br> Környezeti változó:`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`egy kapcsolati karakterlánc, amely lehetővé teszi, hogy adja meg a tárfiókot, amelyre az adatokat feltölteni szeretné. Adja `Azure Storage Account Name` `Azure Storage Account Key`meg `End point suffix`a , , . Adja hozzá az Azure megfelelő EndpointSuffix-ját, ahol az adatok at feltölti, az a globális Azure, a Government Azure és a Microsoft Azure Stack esetében változik. <br><br> Itt adhatja meg az Azure Storage SAS-kapcsolati karakterláncát. De ezt a tulajdonságot frissítenie kell, amikor lejár. <br><br> Környezeti változó:`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Lehetővé teszi az Azure-ba feltölteni kívánt tárolónevek megadását. Ez a modul lehetővé teszi a forrás- és céltárolónevek megadását. Ha nem adja meg a céltároló nevét, akkor automatikusan hozzárendeli a tároló nevét `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Sablonkarakterláncokat hozhat létre a céltároló nevéhez, és kiveheti a lehetséges értékek oszlopot. <br>* %h -> IoT hub neve (3-50 karakter). <br>* %d -> IoT Edge eszközazonosító (1–129 karakter). <br>* %m -> modulneve (1–64 karakter). <br>* %c -> forrástároló neve (3–63 karakter). <br><br>A tárolónév maximális mérete 63 karakter, miközben automatikusan hozzárendeli a céltároló nevét, ha a tároló mérete meghaladja a 63 karaktert, minden szakaszt (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) 15-re vág. Karakterek. <br><br> Környezeti változó:`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | igaz, hamis | Alapértelmezés `false` szerint be van állítva. Ha be van `true`állítva, akkor automatikusan törli az adatokat, amikor a felhőtárhelyre való feltöltés befejeződött. <br><br> **VIGYÁZAT:** Ha hozzáfűző blobokat használ, ez a beállítás a sikeres feltöltés után törli a hozzáfűző blobokat a helyi tárolóból, és a blobokhoz való jövőbeli hozzáfűzési blokkműveletek sikertelenek lesznek. Körültekintően használja ezt a beállítást, ne engedélyezze ezt, ha az alkalmazás ritkán fűz hozzá műveleteket, vagy nem támogatja a folyamatos hozzáfűzési műveleteket<br><br> Környezeti változó: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`. |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

A beállítás neve `deviceAutoDeleteProperties`. Ha az IoT Edge-szimulátort használja, állítsa be az értékeket a kapcsolódó környezeti változók hoz ezekhez a tulajdonságokhoz, amelyek a magyarázat szakaszban találhatók.

| Tulajdonság | Lehetséges értékek | Magyarázat |
| ----- | ----- | ---- |
| törlés | igaz, hamis | Alapértelmezés `false` szerint be van állítva. Ha be szeretné kapcsolni a funkciót, `true`állítsa ezt a mezőt a -ra. <br><br> Környezeti változó:`deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Adja meg az időt percben. A modul automatikusan törli a blobokat a helyi tárolóból, amikor ez az érték lejár. <br><br> Környezeti változó:`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| megtartásWhileUploading | igaz, hamis | Alapértelmezés szerint a `true`be van állítva, és megtartja a blobot, miközben feltölti a felhőalapú tárhelyre, ha deleteAfterMinutes lejár. Beállíthatja, hogy, `false` és törli az adatokat, amint deleteAfterMinutes lejár. Megjegyzés: Ahhoz, hogy ez a tulajdonság működjön uploadOn kell állítani, hogy igaz.  <br><br> **VIGYÁZAT:** Ha hozzáfűző blobokat használ, ez a beállítás törli a hozzáfűző blobokat a helyi tárolóból, amikor az érték lejár, és a blobok hoz való jövőbeli hozzáfűző blokk műveletek sikertelenek lesznek. Érdemes lehet győződjön meg arról, hogy a lejárati érték elég nagy az alkalmazás által végrehajtott hozzáfűzési műveletek várható gyakoriságához.<br><br> Környezeti változó:`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Az SMB-megosztás használata helyi tárolóként

Az SMB-megosztást helyi tárolási útvonalként is megadhatja, amikor a modul Windows-tárolóját windowsos gazdagépen telepíti.

Győződjön meg arról, hogy az SMB-megosztás és az IoT-eszköz kölcsönösen megbízható tartományokban található.

A PowerShell parancs futtatásával `New-SmbGlobalMapping` leképezheti az SMB-megosztást helyileg a Windows rendszert futtató IoT-eszközön.

Az alábbiakban a konfigurációs lépéseket:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Példa:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Ez a parancs a hitelesítő adatokat fogja használni a távoli SMB-kiszolgálóval való hitelesítéshez. Ezután rendelje hozzá a távoli megosztás elérési útját a G: meghajtóbetűjelhez (bármely más rendelkezésre álló meghajtóbetűjel lehet). Az IoT-eszköz most már rendelkezik az adatkötet leképezve egy elérési útra a G: meghajtón.

Győződjön meg arról, hogy az IoT-eszköz felhasználója tud olvasni/olvasni a távoli SMB-megosztásra.

A központi telepítéshez `<storage mount>` a **g:/ContainerData:C:/BlobRoot**értéke lehet.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Címtár-hozzáférés megadása a tárolófelhasználónak Linuxon

Ha a Linux-tárolók létrehozási beállításaiban [kötetcsatlakoztatási](https://docs.docker.com/storage/volumes/) tárolót használt, akkor nem kell további lépéseket tennie, de ha [kötéscsatlakoztatási beállítást](https://docs.docker.com/storage/bind-mounts/) használt, akkor ezek a lépések szükségesek a szolgáltatás megfelelő futtatásához.

A felhasználók hozzáférési jogainak a munkájuk elvégzéséhez szükséges minimális engedélyekre való korlátozásának elve alapján ez a modul tartalmaz egy felhasználót (név: absie, ID: 11000) és egy felhasználói csoportot (név: absie, ID: 11000). Ha a tároló **root** (alapértelmezett felhasználó **root)** néven indul el, a szolgáltatásunk alacsony jogosultságú **absie** felhasználóként indul el.

Ez a viselkedés teszi a konfiguráció az engedélyek gazdaelérési utak kötődik döntő fontosságú a szolgáltatás megfelelő működéséhez, különben a szolgáltatás összeomlik a hozzáférés megtagadva hibákat. A címtárkötésben használt elérési utat a tároló felhasználójának kell elérhetővé tennie (például: absie 11000). A tároló felhasználói számára a könyvtárhoz való hozzáférést az alábbi parancsok végrehajtásával biztosíthatja az állomáson:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Példa:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Ha a szolgáltatást nem absie felhasználóként kell **futtatnia,** megadhatja az egyéni felhasználói azonosítót a createOptions mezőben a központi telepítési jegyzék "Felhasználó" tulajdonsága alatt. Ebben az esetben az alapértelmezett vagy gyökércsoport-azonosítót kell használnia. `0`

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Most adjon hozzáférést a tároló felhasználói könyvtárához

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Naplófájlok konfigurálása

A naplófájlok modulhoz való konfigurálásáról az [éles környezetben ajánlott eljárásokban](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics)talál tájékoztatást.

## <a name="connect-to-your-blob-storage-module"></a>Csatlakozás a blobstorage modulhoz

Használhatja a fiók nevét és a fiókkulcs, amely a modul a modul eléréséhez a blob storage az IoT Edge-eszközön.

Adja meg az IoT Edge-eszközt blob-végpontként a hozzá küldött tárterület-kérelmekhez. Létrehozhat [egy kapcsolati karakterláncot egy explicit tárolási végponthoz](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) az IoT Edge-eszköz adatai és a beállított fióknév használatával.

* Az on-t az Azure Blob Storage ioT Edge-modulon futó ugyanazon az eszközön üzembe `http://<module name>:11002/<account name>`helyezett modulok esetében a blobvégpont a következő: .
* Egy másik eszközön futó modulok vagy alkalmazások esetén ki kell választania a megfelelő végpontot a hálózathoz. A ttól függően, hogy a hálózati beállítás, válasszon egy végpont formátumot úgy, hogy a külső modul vagy alkalmazás adatforgalmat érhet el az eszközt futtató Azure Blob Storage IoT Edge modul. A blob végpont ebben a forgatókönyvben az egyik:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Az Azure Blob Storage rövid útmutatómintái

Az Azure Blob Storage dokumentációja több nyelven is tartalmaz rövid útmutató mintakódot. Ezeket a mintákat futtathatja az Azure Blob Storage IoT Edge-en való teszteléséhez a blob végpont jának módosításával, hogy csatlakozzon a helyi blobstorage-modulhoz.

A következő rövid útmutató minták olyan nyelveket használnak, amelyeket az IoT Edge is támogat, így a blob storage modul mellett IoT Edge-modulokként is üzembe helyezheti őket:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * A Python SDK V2.1-es verziójához tartozó verziók egy ismert problémával rendelkeznek, amely miatt a modul nem ad vissza blob létrehozási időt. E miatt a probléma miatt egyes módszerek, például a listablobok nem működnek. Kerülő megoldásként explicit módon állítsa be az API-verziót a blob-ügyfél "2017-04-17" .As a workaround, explicit set the API version on the blob client to '2017-04-17'. Példa:`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Blob minta hozzáfűzése](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Ugrás](../storage/blobs/storage-quickstart-blobs-go.md)
* [Php](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Csatlakozás a helyi tárhelyhez az Azure Storage Explorer segítségével

Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) segítségével csatlakozhat a helyi tárfiókhoz.

1. Az Azure Storage Explorer letöltése és telepítése

1. Csatlakozás az Azure Storage-hoz kapcsolati karakterlánc használatával

1. Adja meg a kapcsolati karakterláncot:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. A csatlakozás lépésein keresztül.

1. Tároló létrehozása a helyi tárfiókon belül

1. Kezdje el feltölteni a fájlokat blokkblobként vagy blobok hozzáfűzéseként.
   > [!NOTE]
   > Ez a modul nem támogatja a lapblobokat.

1. Az Azure storage-fiókok csatlakoztatása a Storage Explorerben is. Ez a konfiguráció egyetlen nézetet biztosít a helyi tárfiókhoz és az Azure storage-fiókhoz

## <a name="supported-storage-operations"></a>Támogatott tárolási műveletek

Az IoT Edge-en lévő Blob storage-modulok az Azure Storage SDK-kat használják, és konzisztensek az Azure Storage API 2017-04-17-es verziójával blokkblob-végpontokhoz.

Mivel nem minden Azure Blob Storage-műveletet támogat az Azure Blob Storage az IoT Edge-en, ez a szakasz az egyes állapotokat sorolja fel.

### <a name="account"></a>Fiók

Támogatott:

* Tárolók listázása

Támogatott:

* Blob szolgáltatás tulajdonságainak beése és beállítása
* Elővizsgálati blobkérelem
* Blob-szolgáltatás statisztikáinak beszereznie
* Fiókadatok beszereznie

### <a name="containers"></a>Containers

Támogatott:

* Tároló létrehozása és törlése
* Tárolótulajdonságok és metaadatok beszerezni
* Blobok listázása
* Tároló ACL be- és beállítása
* Tároló metaadatainak beállítása

Támogatott:

* Tároló bérlete

### <a name="blobs"></a>Blobok

Támogatott:

* Blob elhelyezhetése, bekerülése és törlése
* Blob-tulajdonságok beési és beállítása
* Blob metaadatainak beése és beállítása

Támogatott:

* Bérletblob
* Pillanatkép blob
* Másolási blob másolása és megszakítása
* Blob törlésének törlése
* Blob-szint beállítása

### <a name="block-blobs"></a>Blokkblobok

Támogatott:

* Tedd blokk
* Lista elrakása és bekerülése

Támogatott:

* Blokk url-ből való letiltása

### <a name="append-blobs"></a>Hozzáfűző blobok

Támogatott:

* Hozzáfűző blokk

Támogatott:

* Hozzáfűzési blokk az URL-címből

## <a name="event-grid-on-iot-edge-integration"></a>Eseményrács az IoT Edge-integráción

> [!CAUTION]
> Az IoT Edge Event Griddel való integráció előzetes verzióban érhető el

Ez az Azure Blob Storage az IoT Edge-modul most már integrációt biztosít az IoT Edge Event Grid használatával. Az integrációval kapcsolatos részletes információkért tekintse meg [a modulok üzembe helyezéséről, az események közzétételéről és az eseménykézbesítés ellenőrzéséről szóló oktatóanyagban.](../event-grid/edge/react-blob-storage-events-locally.md)

## <a name="release-notes"></a>Kibocsátási megjegyzések

Itt vannak a [modul docker hubjában található kibocsátási megjegyzések](https://hub.docker.com/_/microsoft-azure-blob-storage)

## <a name="feedback"></a>Visszajelzés

Az Ön visszajelzése fontos számunkra, hogy ez a modul és annak funkciói hasznosak és könnyen használhatóak legyenek. Kérjük, ossza meg visszajelzését, és tudassa velünk, hogyan tudunk javítani.

Elérhet minket aabsiotfeedback@microsoft.com

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [telepítheti az Azure Blob Storage-ot az IoT Edge-en](how-to-deploy-blob.md)

Legyen naprakész a legújabb frissítésekkel és bejelentésekkel az [IoT Edge-blogon található Azure Blob Storage szolgáltatásban](https://aka.ms/abs-iot-blogpost)
