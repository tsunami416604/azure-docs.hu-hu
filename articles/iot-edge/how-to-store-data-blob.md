---
title: Az Azure IoT Edge-eszközök – a blokkblobok Store |} A Microsoft Docs
description: Egy Azure Blob Storage-modul üzembe helyezése az IoT Edge-eszköz a peremhálózaton adatok tárolására.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 10/03/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e56d49208740686b51cdaef1bab778e2c08a9b58
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077920"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Az Azure Blob Storage a peremhálózaton data Store az IoT Edge-ben (előzetes verzió)

Az IoT Edge-ben az Azure Blob Storage biztosít egy [blokkblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) tárolási megoldás a peremhálózaton. Az Azure block blob szolgáltatás viselkedik az IoT Edge-eszközön a blob storage-modulban, de a blokkblobok használatát támogatják a rendszer helyben tárolja az IoT Edge-eszközön. Ugyanazokkal a módszerekkel az Azure storage SDK-t a blobok elérése, vagy a blob API-hívás, amely már jártas letiltása. 

Forgatókönyvek, ahol adatokat, például videók, képek, pénzügyi adatok, kórházi vagy adatokat kell tárolni, helyileg, később helyileg feldolgozható, vagy jó példák töltődnek fel a felhőbe, ezzel a modullal.

Ez a cikk ismerteti, hogyan telepíthető egy Azure Blob Storage, blob szolgáltatás az IoT Edge-eszközön futó IoT Edge-tárolón. 

>[!NOTE]
>Az Azure Blob Storage, az IoT Edge-ben van [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhat egy fejlesztői vagy virtuális gépet is Edge-eszközként a [Linux-](quickstart-linux.md) vagy [Windows-eszközök](quickstart.md) rövid útmutatójának lépéseit követve.
* Az Azure Blob Storage, az IoT Edge-modul a következő eszköz konfigurációkat támogatja:

   | Operációs rendszer | Architektúra |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (. októberi frissítés) | AMD64 |
   | A Windows 10 IoT Enterprise (. októberi frissítés) | AMD64 |
   | A Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Felhőerőforrások:

* Egy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 


## <a name="deploy-blob-storage-to-your-device"></a>Az eszköz üzembe helyezése a blob storage-bA

Többféleképpen is a modulok IoT Edge-eszköz üzembe helyezése, és ezek mindegyike az Azure Blob Storage az IoT Edge-modulok. A két legegyszerűbb módszereket használja az Azure portal vagy a Visual Studio Code-sablonokat. 

### <a name="azure-portal"></a>Azure Portal

#### <a name="find-the-module"></a>A modul keresése

Válassza ki a blob storage-modulban található két módszer egyikével:

1. Az Azure Portalon keresse meg a "Azure Blob Storage az IoT Edge". És **kiválasztása** a keresési eredmény elem
2. Ugrás a Marketplace-en az Azure Portalról, majd kattintson az "Eszközök internetes hálózata". A "IoT Edge-modulok" szakaszban válassza az "Azure Blob Storage az IoT Edge". Kattintson **létrehozása**

#### <a name="steps-to-deploy"></a>Telepítésének lépései

**IoT Edge-modul a Céleszközök**

1. Válassza ki az IoT Hub telepítési helyét (előfizetés).
2. Válassza ki az "IoT Hub".
3. Adja meg az "IoT Edge eszköz neve" ahol ez a modul telepíteni szeretné. Kiválaszthatja, keresse meg az eszköz "Található eszköz" használatával.
4. Kattintson a **Create** (Létrehozás) gombra.

**Modulok beállítása**

1. "Modulok hozzáadása" szakaszban a "Központi telepítés modulok" tapasztalni fogja, hogy a modul már szerepel a neve "AzureBlobStorageonIoTEdge" kezdetű. 
2. **Válassza ki** "Üzembe helyezési modulok" listájából a blob storage-modulban. "IoT Edge-egyéni modulok" oldal panel nyílik meg.
3. **Név**: módosíthatja Itt a modul neve
4. **Lemezkép URI**: cserélje le az URI-t **mcr.microsoft.com/azure-blob-storage:latest**
5. **Tároló létrehozása beállítások**: szerkessze az értékeket az alábbi JSON, és cserélje le a JSON-t a portál oldalán:
   
   ```json
   {
       "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
       ],
       "HostConfig":{
           "Binds":[
               "<storage directory bind>"
           ],
           "PortBindings":{
               "11002/tcp":[{"HostPort":"11002"}]
           }
       }
   }
   ```   
   
    * Frissítés `<your storage account name>`. Fiókneveket három való huszonnégy karakteres, kisbetűket és számokat kell lennie.
    * Frissítés `<your storage account key>` 64 bájt méretű base64 kulccsal. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni.
    * Frissítés `<storage directory bind>`. Tároló operációs rendszerétől függően. Adja meg a nevét egy [kötet](https://docs.docker.com/storage/volumes/) vagy az IoT Edge-eszköz, ahol azt szeretné, hogy a blob modul tárolja az adatokat egy könyvtár abszolút elérési útját.  

       * Linux-tárolókat:  **\<. tárolási elérési útja >: / blobroot**. Ha például/srv/containerdata: / blobroot. Vagy a kötet: / blobroot. 
       * Windows-tárolók:  **\<. tárolási elérési útja >: C: / BlobRoot**. Például: C: / ContainerData:C: / BlobRoot. Másik lehetőségként saját – kötet: C: / blobroot.
   
   > [!CAUTION]
   > Ne módosítsa a "/ blobroot" Linux és a "C:/BlobRoot" a Windows, a  **\<Storage directory kötési >** értékeket.

    ![Frissítés modul tároló-létrehozási beállítások – portál](./media/how-to-store-data-blob/edit-module.png)

6. **Mentés** "IoT Edge-egyéni modulok" értékei
7. Kattintson a **tovább** "Modulok beállítása" szakaszban.
8. Kattintson a **tovább** "Útvonalak megadása" szakaszban
9. Áttekintése után kattintson **küldés** "Tekintse át az üzembe helyezés" szakaszban.
10. Az IoT hub ellenőrizze, hogy az eszköz fut-e a blob storage-modulban 

### <a name="visual-studio-code-templates"></a>A Visual Studio Code-sablonok

Az Azure IoT Edge segítségével peremhálózati megoldásokat fejleszthet a Visual Studio Code-sablonok biztosít. Ezeket a lépéseket igényel, hogy [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen telepítve és konfigurálva a [Azure IoT Edge bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

A következő lépések segítségével hozzon létre egy új IoT Edge-megoldás a blob storage-modulban, és konfigurálja a manifest nasazení. 

1. Válassza ki **nézet** > **paletta parancs**. 

2. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: IoT Edge új megoldás**. 

3. Kövesse az utasításokat követve hozzon létre egy új megoldást: 

   1. **Válassza ki a mappa** – keresse meg a mappát, ahol szeretné létrehozni az új megoldásba.  
   
   2. **Adja meg a megoldás nevét** – adja meg a megoldás nevét, vagy fogadja el az alapértelmezett.
   
   3. **Válassza ki a modul sablon** -válassza **meglévő modul (adjon meg teljes URL-címe)**.
   
   4. **Adjon meg egy modulnév** – például írja be egy felismerhető nevet a modulnak **Azure BLOB Storage szolgáltatásról**.
   
   5. **A modul adja meg a Docker-rendszerkép** – adja meg a lemezkép URI-ja: **mcr.microsoft.com/azure-blob-storage:latest**

A VS Code időt vesz igénybe, a megadott információt, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakban. 

A megoldássablon hoz létre, amely tartalmazza a blob storage modul rendszerképének jegyzékfájl a központi telepítési sablont, de be kell állítania a modul a létrehozási beállítások. 

1. Nyissa meg **deployment.template.json** az új megoldás munkaterületet, és a Keresés a **modulok** szakaszban. 

2. Törölje a **tempSensor** modult, mert a nem ehhez a telepítéshez szükséges. 

3. Másolja és illessze be az alábbi kódot a **createOptions** mezőjét, a blob storage-modulban: 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![A modul createOptions – VS Code frissítése](./media/how-to-store-data-blob/create-options.png)

4. Frissítse a létrehozási lehetőségek JSON `<storage directory bind>` tároló operációs rendszertől függően. Adja meg a nevét egy [kötet](https://docs.docker.com/storage/volumes/) vagy az IoT Edge-eszköz, ahol azt szeretné, hogy a blob modul tárolja az adatokat egy könyvtár abszolút elérési útját.  

   * Linux-tárolókat:  **\<. tárolási elérési útja >: / blobroot**. Ha például/srv/containerdata: / blobroot. Vagy a kötet: / blobroot.
   * Windows-tárolók:  **\<. tárolási elérési útja >: C: / BlobRoot**. Például: C: / ContainerData:C: / BlobRoot. Másik lehetőségként saját – kötet: C: / blobroot.
   
   > [!CAUTION]
   > Ne módosítsa a "/ blobroot" Linux és a "C:/BlobRoot" a Windows, a  **\<Storage directory kötési >** értékeket.

5. Mentés **deployment.template.json**.

6. Nyissa meg **.env** a megoldás munkaterületen. 

7. Nem kell minden tároló beállításjegyzék-értékek megadását a blob storage-lemezkép, mivel a nyilvánosan elérhető. Ehelyett adja hozzá a két új környezeti változókat: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Adjon meg értéket a `STORAGE_ACCOUNT_NAME`, fiókneveket három való huszonnégy karakteres, kisbetűket és számokat kell lennie. És a egy 64 bájt méretű base64 kulcsot adja meg a `STORAGE_ACCOUNT_KEY`. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni. 

9. Mentés **.env**. 

10. Kattintson a jobb gombbal **deployment.template.json** válassza **hozzon létre IoT Edge-manifest nasazení**. 

A Visual Studio Code az adatokat a deployment.template.json és .env, és a segítségével hozzon létre egy új központi telepítési jegyzékfájl vesz igénybe. Manifest nasazení jön létre egy új **config** a megoldás munkaterület mappájában. Miután ezt a fájlt, a lépésekkel [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md) vagy [üzembe helyezése az Azure IoT Edge-modulok az Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Csatlakozás a blob storage-modulban

A fiók neve és a fiókkulcsot, hogy konfigurálta-e a modul az IoT Edge-eszközön a blobtároló elérésére használható. 

Adja meg a tárolási blob végpontja az IoT Edge-eszköz végrehajtott módosítások hozzá kérelmeket. Is [hozzon létre egy kapcsolati karakterláncot egy explicit storage-végpont](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) az IoT Edge-eszköz adatait és a beállított fiók nevét. 

1. Olyan modulok, amelyek telepítve vannak, az "Azure Blob Storage az IoT Edge" futtató azonos peremhálózati eszköz, a blob végpontja van: `http://<Module Name>:11002/<account name>`. 
2. A modulok, amelyek különböző peremhálózati eszköz, mint a peremhálózati eszköz, amelyben "Azure Blob Storage az IoT Edge" fut, akkor a telepítő a blob végpontja készlettől van telepítve vannak: `http://<device IP >:11002/<account name>` vagy `http://<IoT Edge device hostname>:11002/<account name>` vagy `http://<FQDN>:11002/<account name>`

## <a name="logs"></a>Logs

A tárolóban, a naplók alapján találja meg: 
* Linux: /blobroot/logs/platformblob.log

## <a name="deploy-multiple-instances"></a>Több példány üzembe helyezése

Ha azt szeretné, az Azure Blob Storage, az IoT Edge-ben több példányának telepítése, csak módosítani szeretné a hostport paramétert, amely a modul van kötve. A blob storage-modulok mindig tegye elérhetővé a port 11002 a tárolóban, de deklarálhatja, hogy melyik portot a gazdagépen van kötve. 

A modul szerkesztése lehetőség kiválasztásával módosíthatja a hostport paramétert érték létrehozása:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

További blob storage-modulokkal való csatlakozáskor módosítsa a végpontot, hogy a gazdagép frissített portra mutat. 

### <a name="try-it-out"></a>Próbálja ki!

Az Azure Blob Storage dokumentációja tartalmazza az rövid útmutatók, amelyek számos nyelven mintakódot. Ezek a minták teszteléséhez az IoT Edge-ben az Azure Blob Storage a blob végpontja, hogy a blob storage-modulban mutasson módosításával futtathatja.

Az alábbi rövid útmutatókat használja, így sikerült telepítheti őket, IoT Edge-modulok mellett a blob storage-modulban is IoT Edge által támogatott nyelvek:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>Támogatott tárolási műveletek

BLOB storage-modulok IoT Edge ugyanazt az Azure Storage SDK-k használata, és konzisztensek legyenek az Azure Storage block blob végpontok API 2018-03-28-as verzióját. Újabb kiadásaiban ügyfelek igényeitől függnek. 

Nem minden Azure Blob Storage-műveletek az Azure Blob Storage, az IoT Edge-ben támogatottak. A következő szakaszokban, mely műveletek a következők a rendszer nem támogatott. 

### <a name="account"></a>Fiók

Támogatott: 
* Tárolók listázása

Nem támogatott: 
* GET, és állítsa be a blob szolgáltatás tulajdonságai
* Kérelem előzetes blob
* Blob szolgáltatás statisztikáiról beolvasása
* Fiók adatainak beolvasása

### <a name="containers"></a>Containers

Támogatott: 
* Hozzon létre, és a tároló törlése
* A tároló tulajdonságainak és metaadatainak lekérése
* Blobok listázása

Nem támogatott: 
* GET, és állítsa be a tároló ACL
* Címbérlet-tárolók
* Set-tároló metaadatai

### <a name="blobs"></a>Blobok

Támogatott: 
* PUT, letölthet vagy törölhet blob
* Első és a blob tulajdonságainak beállítása
* GET, és állítsa be a blob metaadatai

Nem támogatott: 
* Címbérleti blobhoz
* Blob pillanatkép
* Másolja ki és a blob másolásához megszakítása
* Blob törlésének visszavonása
* Blobszint beállítása

### <a name="block-blobs"></a>Blokkblobok

Támogatott: 
* PUT letiltása: – a blokkot kell lennie legfeljebb 4 MB-nál
* PUT és a blokk-lista lekérése

Nem támogatott:
* Blokk PUT URL-címről

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

