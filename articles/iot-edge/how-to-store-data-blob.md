---
title: Az Azure IoT Edge-eszközök – a blokkblobok Store |} A Microsoft Docs
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
ms.openlocfilehash: a40389ca378826aef1b6aa136f8f5d69783c638e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881220"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Az Azure Blob Storage IoT Edge

Az IoT Edge-ben az Azure Blob Storage biztosít egy [blokkblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) tárolási megoldás a peremhálózaton. A IoT Edge eszközön található blob Storage-modul úgy viselkedik, mint egy Azure Block blob szolgáltatás, kivéve a blokk blobokat a IoT Edge eszközön helyileg tárolja. Ugyanazokkal a módszerekkel az Azure storage SDK-t a blobok elérése, vagy a blob API-hívás, amely már jártas letiltása. Ez a cikk ismerteti az Azure Blob Storage IoT Edge-tárolóval kapcsolatos fogalmakat, amelyek a IoT Edge eszközön futó blob-szolgáltatást futtatnak.

Ez a modul a következő helyzetekben hasznos:
* az adattárolást és a felhőbe való átadást követően helyileg kell tárolni az adattárolást. Ezen adat lehet videók, képek, pénzügyi adat, kórházi adat vagy egyéb strukturálatlan adat.
* Ha az eszközök korlátozott kapcsolattal rendelkező helyen találhatók.
* Ha a lehető leggyorsabban szeretné feldolgozni az adatfeldolgozást, hogy alacsony késéssel lehessen elérni az adatelérést, úgy, hogy a lehető leghamarabb reagáljon a vészhelyzetekre.
* Ha csökkenteni kívánja a sávszélességi költségeket, és nem kívánja átvinni a felhőbe a terabájtos adatátvitelt. Az adatfeldolgozást helyileg végezheti el, és csak a feldolgozott adatbevitelt küldheti a felhőbe.

Videó megtekintése a gyors bevezetéshez
> [!VIDEO https://www.youtube.com/embed/QhCYCvu3tiM]

Ez a modul a **deviceToCloudUpload** és a **deviceAutoDelete** funkciókhoz tartozik.

a **deviceToCloudUpload** konfigurálható funkció. Ez a függvény automatikusan feltölti az adatait a helyi blob Storage-ból az Azure-ba, és időszakos internetkapcsolatot támogat. A következőket teszi lehetővé:

- A deviceToCloudUpload funkció be-és kikapcsolása.
- Válassza ki az adatmásolási sorrendet az Azure-ba (például NewestFirst vagy OldestFirst).
- Adja meg azt az Azure Storage-fiókot, amelyhez az adatait fel szeretné tölteni.
- Itt adhatja meg az Azure-ba feltölteni kívánt tárolókat. Ez a modul lehetővé teszi a forrás és a cél tároló nevének megadását.
- Válassza ki a Blobok azonnali törlésének lehetőségét a Cloud Storage-ba való feltöltés befejezése után
- Hajtsa végre a Blobok `Put Blob` teljes feltöltését (a művelettel) `Put Block` , `Put Block List` és tiltsa le a szint feltöltését (using és Operations).

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

A **deviceToCloudUploadProperties** és a **deviceAutoDeleteProperties**beállításához használja a modul kívánt tulajdonságait. A kívánt tulajdonságok az üzembe helyezés során állíthatók be, és később is megváltozhatnak, ha a modult az újbóli üzembe helyezés nélkül szerkeszti. Javasoljuk, hogy ellenőrizze a "Twin modul" `reported configuration` értékét `configurationValidation` , és győződjön meg arról, hogy az értékek megfelelően propagálva vannak.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

A beállítás neve`deviceToCloudUploadProperties`

| Mező | Lehetséges értékek | Magyarázat | Környezeti változó |
| ----- | ----- | ---- | ---- |
| uploadOn | true, false | `false` Alapértelmezett értékre van állítva. Ha be szeretné kapcsolni a szolgáltatást, állítsa ezt a mezőt a `true`következőre:. | `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Lehetővé teszi az Azure-ba történő adatmásolási sorrend kiválasztását. `OldestFirst` Alapértelmezett értékre van állítva. A sorrendet a blob utolsó módosítási ideje határozza meg | `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`a egy olyan kapcsolódási karakterlánc, amely lehetővé teszi, hogy megadja azt a Storage-fiókot, amelyhez az adatait fel szeretné tölteni. Itt adhatja meg `Azure Storage Account Name`:,. `End point suffix` `Azure Storage Account Key` Adja hozzá a megfelelő EndpointSuffix az Azure-ban, ahol az adatfeltöltés megtörténik, és ez a globális Azure, a Government Azure és a Microsoft Azure Stack számára is változhat. <br><br> Itt adhatja meg az Azure Storage SAS-kapcsolódási karakterláncát. Ezt a tulajdonságot azonban akkor kell frissítenie, amikor lejár.  | `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Lehetővé teszi az Azure-ba feltölteni kívánt tárolók nevének megadását. Ez a modul lehetővé teszi a forrás és a cél tároló nevének megadását. Ha nem adja meg a cél tároló nevét, a rendszer automatikusan hozzárendeli a tároló nevét `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`. Létrehozhat sablon sztringeket a cél tároló neveként, tekintse meg a lehetséges értékek oszlopot. <br>*% h – > IoT Hub neve (3-50 karakter). <br>*% d – > IoT Edge eszköz azonosítója (1 – 129 karakter). <br>*% m – > modul neve (1 – 64 karakter). <br>*% c – > a forrás tárolójának neve (3 – 63 karakter). <br><br>A tároló nevének maximális mérete 63 karakter, a tároló nevének automatikus kiosztása, ha a tároló mérete meghaladja az 63 karaktert, az egyes szakaszokat (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) a 15 értékre vágja. karaktereket. | `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target: <targetName>` |
| deleteAfterUpload | true, false | `false` Alapértelmezett értékre van állítva. Ha a értékre van `true`állítva, akkor a rendszer automatikusan törli az adatok törlését a felhőalapú tárhelyre való feltöltés befejezésekor. | `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` |


### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

A beállítás neve`deviceAutoDeleteProperties`

| Mező | Lehetséges értékek | Magyarázat | Környezeti változó |
| ----- | ----- | ---- | ---- |
| deleteOn | true, false | `false` Alapértelmezett értékre van állítva. Ha be szeretné kapcsolni a szolgáltatást, állítsa ezt a mezőt a `true`következőre:. | `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Itt adhatja meg az időt percben. A modul automatikusan törli a blobokat a helyi tárolóból, ha ez az érték lejár | `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Alapértelmezés szerint a értékre van `true`állítva, és megőrzi a blobot, amíg a deleteAfterMinutes lejár. Beállíthatja, hogy `false` a és a deleteAfterMinutes lejárata után azonnal törölje az adatvesztést. Megjegyzés: Ahhoz, hogy ennek a tulajdonságnak a uploadOn működjön, igaz értékre kell állítani| `deviceAutoDeleteProperties__retainWhileUploading={false,true}` |

## <a name="using-smb-share-as-your-local-storage"></a>SMB-megosztás használata helyi tárolóként
Az SMB-megosztást helyi tárolási útvonalként is megadhatja, ha Windows-tárolót telepít a Windows-gazdagépen a modulhoz.

Győződjön meg arról, hogy az SMB-megosztás és a IoT-eszköz kölcsönösen megbízható tartományokban van.

A PowerShell- `New-SmbGlobalMapping` parancs futtatásával az SMB-megosztás helyileg képezhető le a Windows rendszerű IoT-eszközön.

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

Az üzemelő példány értéke `<storage mount>` lehet **G:/ContainerData: C:/BlobRoot**. 

## <a name="granting-directory-access-to-container-user-on-linux"></a>Címtár-hozzáférés megadása a tároló felhasználója számára Linux rendszeren
Ha a Linux-tárolók létrehozási lehetőségeiben a [Volume Mount](https://docs.docker.com/storage/volumes/) for Storage szolgáltatást használta, akkor nem kell további lépéseket végrehajtania, de ha a [kötési csatlakoztatást](https://docs.docker.com/storage/bind-mounts/) használta, akkor ezek a lépések szükségesek a szolgáltatás megfelelő futtatásához.

A minimális jogosultság elve alapján korlátozhatja a felhasználók hozzáférési jogait a munkájuk elvégzéséhez szükséges minimális engedélyekre, ez a modul tartalmaz egy felhasználót (név: absie, azonosító: 11000) és egy felhasználói csoport (név: absie, azonosító: 11000). Ha a tároló gyökérként van elindítva (az alapértelmezett felhasználó a **root**), a szolgáltatás az alacsony jogosultságú **absie** felhasználóként fog elindulni. 

Ez a viselkedés lehetővé teszi, hogy a szolgáltatás megfelelő működéséhez az gazdaelérési út-kötésekhez szükséges engedélyek konfigurálása elengedhetetlen, ellenkező esetben a szolgáltatás a hozzáférés-megtagadási hibákkal összeomlik. A címtár-kötésben használt elérési utat a tároló felhasználójának kell elérhetőnek lennie (például: absie 11000). Az alábbi parancsoknak a gazdagépen való futtatásával megadhatja a tároló felhasználói hozzáférését a címtárhoz:

```terminal
sudo chown -R 11000:11000 <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

Példa:<br>
`sudo chown -R 11000:11000 /srv/containerdata` <br>
`sudo chmod -R 700 /srv/containerdata `


Ha a szolgáltatást a **absie**eltérő felhasználóként kell futtatnia, akkor az egyéni felhasználói azonosítót a CreateOptions "felhasználó" tulajdonságában adhatja meg a telepítési jegyzékben. Ebben az esetben az alapértelmezett vagy a gyökérszintű csoport AZONOSÍTÓját `0`kell használnia.

```json
“createOptions”: { 
  “User”: “<custom user ID>:0” 
} 
```
Most adja meg a tároló felhasználói hozzáférését a címtárhoz
```terminal
sudo chown -R <user ID>:<group ID> <blob-dir> 
sudo chmod -R 700 <blob-dir> 
```

## <a name="configure-log-files"></a>Naplófájlok konfigurálása

A modul naplófájljainak konfigurálásával kapcsolatos információkért tekintse meg az alábbi, az [éles környezettel kapcsolatos ajánlott eljárásokat](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Csatlakozás a blob storage-modulban

A fiók neve és a fiókkulcsot, hogy konfigurálta-e a modul az IoT Edge-eszközön a blobtároló elérésére használható.

Adja meg a tárolási blob végpontja az IoT Edge-eszköz végrehajtott módosítások hozzá kérelmeket. Is [hozzon létre egy kapcsolati karakterláncot egy explicit storage-végpont](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) az IoT Edge-eszköz adatait és a beállított fiók nevét.

- Azon modulok esetében, amelyek az Azure Blob Storage IoT Edge modulon futnak, a blob végpont a következő: `http://<module name>:11002/<account name>`.
- Egy másik eszközön futó modulok vagy alkalmazások esetén ki kell választania a hálózatának megfelelő végpontot. A hálózat beállításától függően válassza ki a végpont formátumát úgy, hogy a külső modulból vagy alkalmazásból érkező adatforgalom elérheti az Azure Blob Storaget IoT Edge modulon futtató eszközt. A forgatókönyvhöz tartozó blob-végpont az alábbiak egyike:
  - `http://<device IP >:11002/<account name>`
  - `http://<IoT Edge device hostname>:11002/<account name>`
  - `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob Storage – gyors üzembe helyezési minták

Az Azure Blob Storage dokumentációja több nyelven is tartalmaz gyors üzembe helyezési mintát. Ezeket a mintákat futtathatja IoT Edge Azure-Blob Storage teszteléséhez, ha módosítja a blob-végpontot a helyi blob Storage-modulhoz való kapcsolódáshoz.

A következő rövid útmutatók a IoT Edge által is támogatott nyelveket használják, így a blob Storage modul mellett IoT Edge modulként is üzembe helyezhetők:

- [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Java](../storage/blobs/storage-quickstart-blobs-java-v10.md)
- [Python](../storage/blobs/storage-quickstart-blobs-python.md)
- [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-v10.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Kapcsolódjon a helyi tárhelyhez Azure Storage Explorer

A helyi Storage [](https://azure.microsoft.com/features/storage-explorer/) -fiókhoz való kapcsolódáshoz Azure Storage Explorert használhat.

1. Azure Storage Explorer letöltése és telepítése

1. Kapcsolódás az Azure Storage-hoz kapcsolati sztring használatával

1. Adja meg a kapcsolatok karakterláncát:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

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

- Put blokk
- PUT és a blokk-lista lekérése

Nem támogatott:

- Blokk PUT URL-címről

## <a name="release-notes"></a>Kibocsátási megjegyzések

A jelen modulhoz tartozó [Docker hub kibocsátási megjegyzései](https://hub.docker.com/_/microsoft-azure-blob-storage)

## <a name="feedback"></a>Visszajelzés

Visszajelzése fontos számunkra, hogy ez a modul és funkciói hasznos és könnyen használhatók legyenek. Ossza meg visszajelzését, és tudassa velünk, hogyan javíthatjuk.

A következő címen érhető el:absiotfeedback@microsoft.com

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [helyezheti üzembe az Azure Blob Storaget IoT Edge](how-to-deploy-blob.md)

Naprakészen tarthatja a legújabb frissítéseket és bejelentéseket az [Azure Blob Storage on IoT Edge blogon](https://aka.ms/abs-iot-blogpost)
