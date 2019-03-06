---
title: Az Azure IoT Edge-eszközök – a blokkblobok Store |} A Microsoft Docs
description: Egy Azure Blob Storage-modul üzembe helyezése az IoT Edge-eszköz a peremhálózaton adatok tárolására.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 556ed3553185445432f9f95731ccfec0578fab62
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455666"
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

Az Azure piactér biztosít az IoT Edge modulok, amelyek közvetlenül az IoT Edge-eszközökön, beleértve az Azure Blob Storage, az IoT Edge-ben is telepíthetők. Kövesse az alábbi lépéseket a modul az Azure Portalról üzembe helyezéséhez.

1. Az a [az Azure portal](https://portal.azure.com), keressen a "Azure Blob Storage az IoT Edge". És **kiválasztása** a keresési eredmények a Marketplace-ről.

   ![Keresés a Marketplace-en modul létrehozása](./media/how-to-store-data-blob/marketplace-module.png)

2. Válassza ki az IoT Edge-eszköz, ez a modul fogadásához. Az a **IoT Edge-modul a Céleszközök** lap, adja meg a következő információkat:

   1. Válassza ki a **előfizetés** , amely tartalmazza az IoT hub használata esetén.

   2. Válassza ki a **az IoT Hub**.

   3. Ha ismeri a **IoT Edge-eszköz neve**, adja meg, amely a szövegmezőben. Vagy válassza **található eszköz** választhat az IoT hub IoT Edge-eszközök listáját. 
   
   4. Kattintson a **Létrehozás** gombra.

   Most, hogy egy IoT Edge-modul az Azure Marketplace-ről úgy döntött, és ki az IoT Edge-eszköz, a modul fogadásához, ekkor átkerül egy három lépéses varázsló, amellyel meghatározhatja, hogy pontosan milyen a modul telepítve lesz.

3. Az a **modulok hozzáadása** lépés a modulok beállítása varázsló, vegye figyelembe, hogy a **AzureBlobStorageonIoTEdge** modul már szerepel a **üzembe helyezési modulok**. 

2. Válassza ki a blob storage-modulban nyissa meg a modul részletei telepítési-modulok listájáról. 

   ![Válassza ki a modul neve, nyissa meg a modul részletei](./media/how-to-store-data-blob/open-module-details.png)

3. Az a **egyéni IoT Edge-modulok** lapon, az Azure Blob Storage, az IoT Edge-modul frissítéséhez az alábbi lépéseket követve:

   1. Módosítsa a modul **neve** kisbetűssé kell. A modul igény szerint átnevezheti, vagy használjon `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >Az Azure IoT Edge egy kis-és nagybetűket, hívásokat modulokat, és a Storage SDK alapértelmezés szerint a kisbetűs. Annak érdekében, hogy a kapcsolatok az Azure Blob Storage az IoT Edge-modul nem szakad, adjon meg egy kis nevet. 

   2. Az alapértelmezett **tároló létrehozása beállítások** a port-kötéseket, amely a tárolóra van szüksége, de Ön is hozzá kell adnia a storage-fiók adatait és a egy kötést a tároló könyvtár az eszközön. Írja felül a JSON-t a portálon az alábbi JSON-ra:
    
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
   3. Frissítse a JSON-t másolta a következő információkat: 

      * Cserélje le `<your storage account name>` emlékszik névvel. Fiókneveket három való huszonnégy karakteres, kisbetűket és számokat kell lennie.
      * Cserélje le `<your storage account key>` 64 bájt méretű base64 kulccsal. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni.
      * Cserélje le `<storage directory bind>` tároló operációs rendszertől függően. Adja meg a nevét egy [kötet](https://docs.docker.com/storage/volumes/) vagy az IoT Edge-eszköz, ahol azt szeretné, hogy a blob modul tárolja az adatokat egy könyvtár abszolút elérési útját. Az storage directory kötés az eszközön, akkor adjon meg egy helyet a hely beállítása a modul képezi le. 

         * Linux-tárolókat:  **\<. tárolási elérési útja >: / blobroot**. Ha például/srv/containerdata: / blobroot. Vagy a kötet: / blobroot. 
         * Windows-tárolók:  **\<. tárolási elérési útja >: C: / BlobRoot**. Például: C: / ContainerData:C: / BlobRoot. Másik lehetőségként saját – kötet: C: / blobroot.
   
      > [!IMPORTANT]
      > Ne változtassa meg a második fele az storage directory kötést, érték, amely a modul egy adott helyre mutat. A storage directory kötési mindig kell végződnie **: / blobroot** Linux-tárolókhoz és **: C: / BlobRoot** Windows-tárolókhoz.

      ![Frissítés modul tároló-létrehozási beállítások – portál](./media/how-to-store-data-blob/edit-module.png)

   4. Kattintson a **Mentés** gombra.

4. Válassza ki **tovább** a varázsló a következő lépéssel folytatja.
5. Az a **útvonalak megadása** . lépés a varázslóban válassza a **tovább**.
6. Az a **áttekintése telepítési** . lépés a varázslóban válassza a **küldés**.
7. Miután az üzembe helyezés elküldött, akkor térjen vissza a **IoT Edge** az IoT hub oldalon. Válassza ki az IoT Edge-eszköz célként való üzembe helyezéséhez, hogy megnyissa annak részletes adatait. 
8. Az eszköz részletek győződjön meg arról, hogy a blob storage-modulban is szerepel a listán **központi telepítésben megadott** és **eszköz által jelentett**. A modul elindult az eszközön, és ezután jelentett vissza az IoT hub egy kis ideig is eltarthat. Frissítse az oldalt, hogy a frissített állapotot. 

### <a name="visual-studio-code-templates"></a>A Visual Studio Code-sablonok

Az Azure IoT Edge segítségével peremhálózati megoldásokat fejleszthet a Visual Studio Code-sablonok biztosít. Ezeket a lépéseket igényel, hogy [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen telepítve és konfigurálva a [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

A következő lépések segítségével hozzon létre egy új IoT Edge-megoldás a blob storage-modulban, és konfigurálja a manifest nasazení. 

1. Válassza ki **nézet** > **paletta parancs**. 

2. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **meglévő modul (adjon meg teljes URL-címe)**. |
   | Provide a module name (Modulnév megadása) | Adja meg például az all-kisbetűk nevét, a modul **Azure BLOB Storage szolgáltatásról**.<br><br>Fontos kisbetűs nevét az Azure Blob Storage használata az IoT Edge-modul. IoT Edge egy kis-és nagybetűket, modulok kontextusban való megnevezésekor, és a Storage SDK alapértelmezés szerint a kisbetűs. |
   | A modul adja meg a Docker-rendszerkép | Adja meg a lemezkép URI-ja: **mcr.microsoft.com/azure-blob-storage:latest** |

   A VS Code időt vesz igénybe, a megadott információt, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakban. A megoldássablon hoz létre, amely tartalmazza a blob storage modul rendszerképének jegyzékfájl a központi telepítési sablont, de be kell állítania a modul a létrehozási beállítások. 

3. Nyissa meg **deployment.template.json** az új megoldás munkaterületet, és a Keresés a **modulok** szakaszban. A következő konfigurációs módosításokat:

   1. Törölje a **tempSensor** modult, mert a nem ehhez a telepítéshez szükséges. 

   2. Másolja és illessze be az alábbi kódot a **createOptions** mezőjét, a blob storage-modulban: 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![A modul createOptions – VS Code frissítése](./media/how-to-store-data-blob/create-options.png)

4. Frissítse a létrehozási lehetőségek JSON `<storage directory bind>` tároló operációs rendszertől függően. Adja meg a nevét egy [kötet](https://docs.docker.com/storage/volumes/) vagy az IoT Edge-eszköz, ahol azt szeretné, hogy a blob modul tárolja az adatokat egy könyvtár abszolút elérési útját. Az storage directory kötés az eszközön, akkor adjon meg egy helyet a hely beállítása a modul képezi le.  

   * Linux-tárolókat:  **\<. tárolási elérési útja >: / blobroot**. Ha például/srv/containerdata: / blobroot. Vagy a kötet: / blobroot.
   * Windows-tárolók:  **\<. tárolási elérési útja >: C: / BlobRoot**. Például: C: / ContainerData:C: / BlobRoot. Másik lehetőségként saját – kötet: C: / blobroot.
   
   > [!IMPORTANT]
   > Ne változtassa meg a második fele az storage directory kötést, érték, amely a modul egy adott helyre mutat. A storage directory kötési mindig kell végződnie **: / blobroot** Linux-tárolókhoz és **: C: / BlobRoot** Windows-tárolókhoz.

5. Mentse a **deployment.template.json** fájlt.

6. Nyissa meg a **.env** a megoldás munkaterületen lévő fájlt. 

7. A .env fájl container registry hitelesítő adatok fogadására van beállítva, de már nincs szüksége, amely a blob storage-lemezkép, mivel a nyilvánosan elérhető. Ehelyett cserélje le a fájl két új környezeti változókat: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Adjon meg egy értéket a `STORAGE_ACCOUNT_NAME`, fiókneveket három való huszonnégy karakteres, kisbetűket és számokat kell lennie. Adjon meg egy 64 bájt méretű base64 kulcsot a `STORAGE_ACCOUNT_KEY`. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni. 

   Nem tartalmazza a szóközöket vagy az Ön értékeket idézőjelek közé. 

9. Mentse az **.env** fájlt. 

10. Kattintson a jobb gombbal **deployment.template.json** válassza **hozzon létre IoT Edge-manifest nasazení**. 

11. A Visual Studio Code az adatokat a deployment.template.json és .env, és a segítségével hozzon létre egy új központi telepítési jegyzékfájl vesz igénybe. Manifest nasazení jön létre egy új **config** a megoldás munkaterület mappájában. Miután ezt a fájlt, a lépésekkel [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md) vagy [üzembe helyezése az Azure IoT Edge-modulok az Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Csatlakozás a blob storage-modulban

A fiók neve és a fiókkulcsot, hogy konfigurálta-e a modul az IoT Edge-eszközön a blobtároló elérésére használható. 

Adja meg a tárolási blob végpontja az IoT Edge-eszköz végrehajtott módosítások hozzá kérelmeket. Is [hozzon létre egy kapcsolati karakterláncot egy explicit storage-végpont](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) az IoT Edge-eszköz adatait és a beállított fiók nevét. 

1. Olyan modulok, amelyek telepítve vannak, az "Azure Blob Storage az IoT Edge" futtató azonos peremhálózati eszköz, a blob végpontja van: `http://<module name>:11002/<account name>`. 
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

## <a name="try-it-out"></a>Próbálja ki!

Az Azure Blob Storage dokumentációja tartalmazza az rövid útmutatók, amelyek számos nyelven mintakódot. Ezek a minták teszteléséhez az IoT Edge-ben az Azure Blob Storage a blob végpontja, hogy a blob storage-modulban mutasson módosításával futtathatja.

Az alábbi rövid útmutatókat használja, így sikerült telepítheti őket, IoT Edge-modulok mellett a blob storage-modulban is IoT Edge által támogatott nyelvek:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>Támogatott tárolási műveletek

BLOB storage-modulok IoT Edge ugyanazt az Azure Storage SDK-k használata, és konzisztensek legyenek az Azure Storage block blob végpontok API 2018-03-28-as verzióját. Újabb kiadásaiban ügyfelek igényeitől függnek. 

Nem minden Azure Blob Storage-műveletek az Azure Blob Storage, az IoT Edge-ben támogatottak. A következő szakaszokban mely műveletek nem támogatottak. 

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

