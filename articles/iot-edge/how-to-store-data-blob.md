---
title: Blokkos Blobok tárolása az eszközökön – Azure IoT Edge | Microsoft Docs
description: 'Megtudhatja, hogyan használhatók a többrétegű és az élettartamú funkciók: támogatott blob Storage-műveletek és kapcsolódás a blob Storage-fiókhoz.'
author: arduppal
manager: mchad
ms.author: arduppal
ms.reviewer: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 55edc69e706fad8888146e9d97541a1c2bae821d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163795"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Az Azure Blob Storage IoT Edge

Az Azure Blob Storage on IoT Edge egy [blokk blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) Storage-megoldást biztosít a szélén. A IoT Edge eszközön található blob Storage-modul úgy viselkedik, mint egy Azure Block blob szolgáltatás, kivéve a blokk blobokat a IoT Edge eszközön helyileg tárolja. A blobokat ugyanazzal az Azure Storage SDK-módszerekkel érheti el, vagy letilthatja a már használt BLOB API-hívásokat. Ez a cikk ismerteti az Azure Blob Storage IoT Edge-tárolóval kapcsolatos fogalmakat, amelyek a IoT Edge eszközön futó blob-szolgáltatást futtatnak.

Ez a modul a következő helyzetekben hasznos:
* az adattárolást és a felhőbe való átadást követően helyileg kell tárolni az adattárolást. Ezek az adatforrások lehetnek videók, képek, pénzügyi, kórházi vagy egyéb strukturálatlan adat.
* Ha az eszközök korlátozott kapcsolattal rendelkező helyen találhatók.
* Ha a lehető leggyorsabban szeretné feldolgozni az adatfeldolgozást, hogy alacsony késéssel lehessen elérni az adatelérést, úgy, hogy a lehető leghamarabb reagáljon a vészhelyzetekre.
* Ha csökkenteni kívánja a sávszélességi költségeket, és nem kívánja átvinni a felhőbe a terabájtos adatátvitelt. Az adatfeldolgozást helyileg végezheti el, és csak a feldolgozott adatbevitelt küldheti a felhőbe.

Videó megtekintése a gyors bevezetéshez
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Ez a modul a **deviceToCloudUpload** és a **deviceAutoDelete** funkciókhoz tartozik.

a **deviceToCloudUpload** konfigurálható funkció. Ez a függvény automatikusan feltölti az adatait a helyi blob Storage-ból az Azure-ba, és időszakos internetkapcsolatot támogat. A következőket teszi lehetővé:

- A deviceToCloudUpload funkció be-és kikapcsolása.
- Válassza ki az adatmásolási sorrendet az Azure-ba (például NewestFirst vagy OldestFirst).
- Adja meg azt az Azure Storage-fiókot, amelyhez az adatait fel szeretné tölteni.
- Itt adhatja meg az Azure-ba feltölteni kívánt tárolókat. Ez a modul lehetővé teszi a forrás és a cél tároló nevének megadását.
- Válassza ki a Blobok azonnali törlésének lehetőségét a Cloud Storage-ba való feltöltés befejezése után
- Végezzen teljes blob-feltöltést (`Put Blob` művelettel), és tiltsa le a szint feltöltését (`Put Block` és `Put Block List` műveletekkel).

Ez a modul blokk szintű feltöltést használ, ha a blob blokkokat tartalmaz. Íme néhány gyakori forgatókönyv:

- Az alkalmazás egy korábban feltöltött blob néhány blokkját frissíti, ez a modul csak a frissített blokkokat tölti fel, a teljes blobot nem.
- A modul feltölti a blobot, és az internetkapcsolat leáll, amikor a kapcsolat helyreáll, csak a többi blokkot tölti fel, a teljes blobot nem.

Ha a folyamat váratlanul leáll (például áramkimaradás) a blob feltöltése során, akkor a feltöltés miatti összes blokk újra fel lesz töltve, amint a modul ismét online állapotba kerül.

a **deviceAutoDelete** konfigurálható funkció. Ez a függvény automatikusan törli a blobokat a helyi tárolóból, ha a megadott időtartam (percben mérve) lejár. A következőket teszi lehetővé:

- A deviceAutoDelete funkció be-és kikapcsolása.
- Itt adhatja meg azt az időt percben (deleteAfterMinutes), amely után a Blobok automatikusan törlődnek.
- Válassza ki a blob megtartásának lehetőségét, ha a deleteAfterMinutes értéke lejár.


## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

- A fejlesztői gépet vagy virtuális gépet IoT Edge eszközként használhatja a gyors útmutató a [Linux](quickstart-linux.md) vagy [Windows rendszerű eszközökhöz](quickstart.md)című témakör lépéseit követve.

- A támogatott operációs rendszerek és architektúrák listáját a [Azure IoT Edge támogatott rendszerek](support.md#operating-systems) című rész tartalmazza. A IoT Edge modul Azure Blob Storage a következő architektúrákat támogatja:
    - Windows AMD64
    - Linux AMD64
    - Linux ARM32
    - Linux ARM64 (előzetes verzió)

Felhőerőforrások:

Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>deviceToCloudUpload és deviceAutoDelete tulajdonságai

A **deviceToCloudUploadProperties** és a **deviceAutoDeleteProperties**beállításához használja a modul kívánt tulajdonságait. A kívánt tulajdonságok az üzembe helyezés során állíthatók be, és később is megváltozhatnak, ha a modult az újbóli üzembe helyezés nélkül szerkeszti. Javasoljuk, hogy ellenőrizze a "Twin modult" `reported configuration` és `configurationValidation`, hogy az értékek megfelelően legyenek propagálva.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

A beállítás neve `deviceToCloudUploadProperties`. Ha a IoT Edge szimulátort használja, állítsa be az értékeket a kapcsolódó környezeti változókra ezekhez a tulajdonságokhoz, amelyek a magyarázat szakaszban találhatók meg.

| Tulajdonság | Lehetséges értékek | Magyarázat |
| ----- | ----- | ---- |
| uploadOn | igaz, hamis | Alapértelmezés szerint `false`re van állítva. Ha be szeretné kapcsolni a szolgáltatást, állítsa ezt a mezőt `true`ra. <br><br> Környezeti változó: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Lehetővé teszi az Azure-ba történő adatmásolási sorrend kiválasztását. Alapértelmezés szerint `OldestFirst`re van állítva. A sorrendet a blob utolsó módosítási ideje határozza meg. <br><br> Környezeti változó: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` egy kapcsolódási karakterlánc, amely lehetővé teszi, hogy megadja azt a Storage-fiókot, amelyhez az adatait fel szeretné tölteni. `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`megadásával. Adja hozzá a megfelelő EndpointSuffix az Azure-ban, ahol az adatfeltöltés megtörténik, és ez a globális Azure, a Government Azure és a Microsoft Azure Stack számára is változhat. <br><br> Itt adhatja meg az Azure Storage SAS-kapcsolódási karakterláncát. Ezt a tulajdonságot azonban akkor kell frissítenie, amikor lejár. <br><br> Környezeti változó: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Lehetővé teszi az Azure-ba feltölteni kívánt tárolók nevének megadását. Ez a modul lehetővé teszi a forrás és a cél tároló nevének megadását. Ha nem adja meg a cél tároló nevét, a rendszer automatikusan hozzárendeli a tároló nevét `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`ként. Létrehozhat sablon sztringeket a cél tároló neveként, tekintse meg a lehetséges értékek oszlopot. <br>*% h – > IoT Hub neve (3-50 karakter). <br>*% d – > IoT Edge eszköz azonosítója (1 – 129 karakter). <br>*% m – > modul neve (1 – 64 karakter). <br>*% c – > a forrás tárolójának neve (3 – 63 karakter). <br><br>A tároló nevének maximális mérete 63 karakter, a tároló nevének automatikus kiosztása, ha a tároló mérete meghaladja az 63 karaktert, az egyes szakaszokat (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) a 15 értékre vágja. karaktereket. <br><br> Környezeti változó: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | igaz, hamis | Alapértelmezés szerint `false`re van állítva. Ha a `true`re van állítva, a rendszer automatikusan törli az adatok törlését a felhőalapú tárhelyre való feltöltés befejezésekor. <br><br> Környezeti változó: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

A beállítás neve `deviceAutoDeleteProperties`. Ha a IoT Edge szimulátort használja, állítsa be az értékeket a kapcsolódó környezeti változókra ezekhez a tulajdonságokhoz, amelyek a magyarázat szakaszban találhatók meg.

| Tulajdonság | Lehetséges értékek | Magyarázat |
| ----- | ----- | ---- |
| deleteOn | igaz, hamis | Alapértelmezés szerint `false`re van állítva. Ha be szeretné kapcsolni a szolgáltatást, állítsa ezt a mezőt `true`ra. <br><br> Környezeti változó: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Itt adhatja meg az időt percben. Ha ez az érték lejár, a modul automatikusan törli a blobokat a helyi tárolóból. <br><br> Környezeti változó: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | igaz, hamis | Alapértelmezés szerint `true`re van állítva, és megőrzi a blobot, amíg a deleteAfterMinutes lejár. Beállíthatja, hogy `false`, és törli az adatvesztést, amint a deleteAfterMinutes lejár. Megjegyzés: ennek a tulajdonságnak a működéséhez a uploadOn True értékre kell állítani. <br><br> Környezeti változó: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>SMB-megosztás használata helyi tárolóként
Az SMB-megosztást helyi tárolási útvonalként is megadhatja, ha Windows-tárolót telepít a Windows-gazdagépen a modulhoz.

Győződjön meg arról, hogy az SMB-megosztás és a IoT-eszköz kölcsönösen megbízható tartományokban van.

`New-SmbGlobalMapping` PowerShell-parancs futtatásával az SMB-megosztás helyileg képezhető le a Windows rendszerű IoT-eszközön.

Alább láthatók a konfigurációs lépések:
```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```
Példa: <br>
`$creds = Get-Credential` <br>
`New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G: `

Ez a parancs a hitelesítő adatokat fogja használni a távoli SMB-kiszolgálóval való hitelesítéshez. Ezután képezze le a távoli megosztás elérési útját a G: meghajtóbetűjelre (bármilyen más elérhető meghajtóbetűjel lehet). A IoT-eszköz most már a G: meghajtó elérési útjára van leképezve az adatmennyiség. 

Győződjön meg arról, hogy a IoT-eszköz felhasználója képes olvasni/írni a távoli SMB-megosztást.

Az üzembe helyezéshez a `<storage mount>` értéke lehet **G:/ContainerData: C:/BlobRoot**. 

## <a name="granting-directory-access-to-container-user-on-linux"></a>Címtár-hozzáférés megadása a tároló felhasználója számára Linux rendszeren
Ha a Linux-tárolók létrehozási lehetőségeiben a [Volume Mount](https://docs.docker.com/storage/volumes/) for Storage szolgáltatást használta, akkor nem kell további lépéseket végrehajtania, de ha a [kötési csatlakoztatást](https://docs.docker.com/storage/bind-mounts/) használta, akkor ezek a lépések szükségesek a szolgáltatás megfelelő futtatásához.

A minimális jogosultság elve alapján korlátozhatja a felhasználók hozzáférési jogait a munkájuk elvégzéséhez szükséges minimális engedélyekre, ez a modul tartalmaz egy felhasználót (név: absie, azonosító: 11000) és egy felhasználói csoportot (név: absie, azonosító: 11000). Ha a tároló **gyökérként** van elindítva (az alapértelmezett felhasználó a **root**), a szolgáltatás az alacsony jogosultságú **absie** felhasználóként fog elindulni. 

Ez a viselkedés lehetővé teszi, hogy a szolgáltatás megfelelő működéséhez az gazdaelérési út-kötésekhez szükséges engedélyek konfigurálása elengedhetetlen, ellenkező esetben a szolgáltatás a hozzáférés-megtagadási hibákkal összeomlik. A címtár-kötésben használt elérési utat a tároló felhasználójának kell elérhetőnek lennie (például: absie 11000). Az alábbi parancsoknak a gazdagépen való futtatásával megadhatja a tároló felhasználói hozzáférését a címtárhoz:

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

Példa:<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata `


Ha a szolgáltatást a **absie**eltérő felhasználóként kell futtatnia, akkor az egyéni felhasználói azonosítót a CreateOptions "felhasználó" tulajdonságában adhatja meg a telepítési jegyzékben. Ebben az esetben az alapértelmezett vagy a gyökérszintű csoport AZONOSÍTÓját kell használnia `0`.

```json
"createOptions": { 
  "User": "<custom user ID>:0" 
} 
```
Most adja meg a tároló felhasználói hozzáférését a címtárhoz
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>Naplófájlok konfigurálása

A modul naplófájljainak konfigurálásával kapcsolatos információkért tekintse meg az alábbi, az [éles környezettel kapcsolatos ajánlott eljárásokat](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Kapcsolódás a blob Storage-modulhoz

Használhatja a modulhoz konfigurált fiók nevét és a hozzá tartozó fiókot a blob Storage IoT Edge eszközön való eléréséhez.

Adja meg a IoT Edge eszközét blob-végpontként minden olyan tárolási kérelemhez, amelyet Ön tesz. A explicit tárolási végponthoz a IoT Edge eszköz információi és a beállított fióknév használatával [hozható létre a kapcsolatok karakterlánca](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) .

- Azon modulok esetében, amelyek az Azure Blob Storage IoT Edge modulon futnak, a blob végpont a következő: `http://<module name>:11002/<account name>`.
- Egy másik eszközön futó modulok vagy alkalmazások esetén ki kell választania a hálózatának megfelelő végpontot. A hálózat beállításától függően válassza ki a végpont formátumát úgy, hogy a külső modulból vagy alkalmazásból érkező adatforgalom elérheti az Azure Blob Storaget IoT Edge modulon futtató eszközt. A forgatókönyvhöz tartozó blob-végpont az alábbiak egyike:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage – gyors üzembe helyezési minták

Az Azure Blob Storage dokumentációja több nyelven is tartalmaz gyors üzembe helyezési mintát. Ezeket a mintákat futtathatja IoT Edge Azure-Blob Storage teszteléséhez, ha módosítja a blob-végpontot a helyi blob Storage-modulhoz való kapcsolódáshoz.

A következő rövid útmutatók a IoT Edge által is támogatott nyelveket használják, így a blob Storage modul mellett IoT Edge modulként is üzembe helyezhetők:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
    - Az SDK használata során ismert probléma van, mivel a modul ezen verziója nem adja vissza a blob létrehozási idejét. Ezért néhány módszer, például a List Blobok nem működnek. Megkerülő megoldásként állítsa explicit módon az API-verziót a blob-ügyfélen a "2017-04-17" értékre. <br>Példa: `block_blob_service._X_MS_VERSION = '2017-04-17'`
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)
- [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-v10.md)
- [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
- [Go](../storage/blobs/storage-quickstart-blobs-go.md)
- [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Kapcsolódjon a helyi tárhelyhez Azure Storage Explorer

A helyi Storage-fiókhoz való kapcsolódáshoz [Azure Storage Explorert](https://azure.microsoft.com/features/storage-explorer/) használhat.

1. Azure Storage Explorer letöltése és telepítése

1. Kapcsolódás az Azure Storage-hoz kapcsolati sztring használatával

1. Adja meg a kapcsolatok karakterláncát: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Folytassa a kapcsolódás lépéseivel.

1. Tároló létrehozása a helyi Storage-fiókon belül

1. Fájlok feltöltésének megkezdése blokk blobként.
   > [!NOTE]
   > Ez a modul nem támogatja a Blobok oldalát.

1. Az Azure Storage-fiókok Storage Explorer is csatlakoztathatók. Ez a konfiguráció egyetlen nézetet biztosít a helyi Storage-fiókhoz és az Azure Storage-fiókhoz is

## <a name="supported-storage-operations"></a>Támogatott tárolási műveletek

A blob Storage-modulok IoT Edge az Azure Storage SDK-kat használják, és konzisztensek az Azure Storage API 2017-04-17-es verziójával, amely blokkolja a blob-végpontokat. 

Mivel az Azure Blob Storage nem támogatja az összes Azure Blob Storage műveletet a IoT Edge-on, ez a szakasz felsorolja az egyes állapotok állapotát.

### <a name="account"></a>Fiók

Támogatott

- Tárolók listázása

Támogatott

- A blob szolgáltatás tulajdonságainak beolvasása és beállítása
- Elővizsgálati blob kérése
- BLOB szolgáltatás statisztikáinak beolvasása
- Fiókadatok beolvasása

### <a name="containers"></a>Containers

Támogatott

- Tároló létrehozása és törlése
- Tároló tulajdonságainak és metaadatainak beolvasása
- Blobok listázása
- Tároló ACL lekérése és beállítása
- Tároló metaadatainak beállítása

Támogatott

- Bérlet tárolója

### <a name="blobs"></a>Blobok

Támogatott

- Blobok elhelyezése, beolvasása és törlése
- BLOB tulajdonságainak beolvasása és beállítása
- BLOB metaadatainak beolvasása és beállítása

Támogatott

- Címbérleti blob
- Pillanatkép-blob
- BLOB másolásának másolása és megszakítása
- BLOB törlésének visszavonása
- BLOB-rétegek beállítása

### <a name="block-blobs"></a>Blokkblobok

Támogatott

- Put blokk
- Letiltási és lekérési lista

Támogatott

- Blokk elhelyezése URL-címről

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid IoT Edge Integration
> [!CAUTION]
> A IoT Edge Event Grid integrációja előzetes verzióban érhető el

Ez az Azure-Blob Storage IoT Edge modulban mostantól a IoT Edge Event Grid-integrációt is biztosít. Az integrációval kapcsolatos részletes információkért tekintse meg a [modulok üzembe helyezésére, az események közzétételére és az esemény kézbesítésének ellenőrzésére vonatkozó oktatóanyagot](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Kibocsátási megjegyzések

A jelen modulhoz tartozó [Docker hub kibocsátási megjegyzései](https://hub.docker.com/_/microsoft-azure-blob-storage)

## <a name="feedback"></a>Visszajelzés

Visszajelzése fontos számunkra, hogy ez a modul és funkciói hasznos és könnyen használhatók legyenek. Ossza meg visszajelzését, és tudassa velünk, hogyan javíthatjuk.

A következő címen érhető el: absiotfeedback@microsoft.com

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [helyezheti üzembe az Azure Blob Storaget IoT Edge](how-to-deploy-blob.md)

Naprakészen tarthatja a legújabb frissítéseket és bejelentéseket az [Azure Blob Storage on IoT Edge blogon](https://aka.ms/abs-iot-blogpost)
