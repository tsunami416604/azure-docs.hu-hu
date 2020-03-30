---
title: Blob storage üzembe helyezése a modulon az eszközre - Azure IoT Edge
description: Üzembe helyezhet egy Azure Blob Storage-modult az IoT Edge-eszközre az adatok peremhálózaton való tárolásához.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 04b145622a1a4237b576a1bb512b5f749f9c3823
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133335"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Az Azure Blob Storage az IoT Edge-ben modul üzembe helyezése az eszközön

A modulok at ioT Edge-eszközökre többféleképpen is üzembe helyezheti, és mindegyik az IoT Edge-modulokon lévő Azure Blob Storage-ban működik. A két legegyszerűbb módszer az Azure Portal vagy a Visual Studio Code sablonok használata.

## <a name="prerequisites"></a>Előfeltételek

- Egy [IoT-központ](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésben.
- IoT [Edge-eszköz](how-to-register-device.md) az IoT Edge futásidejű telepítve.
- [Visual Studio-kód](https://code.visualstudio.com/) és az [Azure IoT-eszközök,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) ha üzembe helyezi a Visual Studio-kód.

## <a name="deploy-from-the-azure-portal"></a>Üzembe helyezés az Azure Portalról

Az Azure Portalon végigvezeti a központi telepítési jegyzékfájl létrehozásához, és lenyomja a központi telepítés tion-eszközre.

### <a name="select-your-device"></a>Válassza ki a készüléket

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az IoT-központot.
1. Válassza az **IoT Edge** lehetőséget a menüből.
1. Kattintson a céleszköz azonosítójára az eszközök listájából.
1. Válassza **a Modulok beállítása**lehetőséget.

### <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzékfájl konfigurálása

A központi telepítési jegyzékfájl egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan adatfolyamok a modulok között, és a modul twins kívánt tulajdonságait. Az Azure Portalon van egy varázsló, amely végigvezeti a központi telepítési jegyzék létrehozásán. Három lépésből áll, lapokba rendezve: **Modulok**, **Útvonalak**és **Véleményezés + Létrehozás**.

#### <a name="add-modules"></a>Modulok hozzáadása

1. A lap **IoT Edge-modulok** szakaszában kattintson a **Hozzáadás** legördülő menüre, és válassza az **IoT Edge Module** lehetőséget az **IoT Edge Module hozzáadása** lap megjelenítéséhez.

2. A **Modulbeállítások** lapon adja meg a modul nevét, majd adja meg a tárolórendszerkép URI-ját:

   Példák:
  
   - **IoT peremhálózati modul neve**:`azureblobstorageoniotedge`
   - **Kép URI**-`mcr.microsoft.com/azure-blob-storage:latest`

   ![Ikermodul beállításai](./media/how-to-deploy-blob/addmodule-tab1.png)

   Ne válassza a **Hozzáadás** lehetőséget, amíg meg nem adta az értékeket a **Modulbeállítások**, **a Tároló létrehozása beállítások**és a Modul **ikerbeállítások** lapján az eljárásban leírtak szerint.

   > [!IMPORTANT]
   > Az Azure IoT Edge kis- és nagybetűket kér a modulokhoz, és a Storage SDK is alapértelmezés szerint kisbetűs. Bár a modul neve az [Azure Marketplace-en](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **Az AzureBlobStorageonIoTEdge,** a név kisbetűs módosítása segít biztosítani, hogy az Azure Blob Storage-hoz az IoT Edge modul hoz való kapcsolatok nem szakad meg.

3. Nyissa meg a **Tároló létrehozása beállításai** lapot.

   ![Ikermodul beállításai](./media/how-to-deploy-blob/addmodule-tab3.png)

   Másolja és illessze be a következő JSON-t a dobozba, hogy a tárfiók adatait és az eszközön lévő tárolócsatlakoztatást biztosítsa.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. Frissítse a **Tárolólétrehozási beállításokba** másolt JSON-t a következő információkkal:

   - Cserélje `<your storage account name>` le egy nevet, hogy emlékszel. A fióknevek nek 3 és 24 karakter között kell lenniük, kisbetűkkel és számokkal. Nincsenek szóközök.

   - Cserélje `<your storage account key>` le egy 64 bájtos alap64 kulcsra. Hozhat létre egy kulcsot eszközökkel, mint [generateplus](https://generate.plus/en/base64). Ezeket a hitelesítő adatokat fogja használni a blobstorage eléréséhez más modulokból.

   - Cserélje `<storage mount>` ki a tároló operációs rendszerének megfelelően. Adja meg egy [kötet](https://docs.docker.com/storage/volumes/) nevét vagy egy meglévő könyvtár abszolút elérési útját az IoT Edge-eszközön, ahol a blob modul tárolja az adatait. A tárolócsatlakoztatás leképezi az eszközön megadott helyet a modul egy meghatározott helyére.

     - Linux-tárolók esetén a formátum * \<tárolási útvonal vagy kötet>:/blobroot*. Példa:
         - [kötetcsatlakoztatás](https://docs.docker.com/storage/volumes/)használata: **my-volume:/blobroot**
         - [bind-csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata : **/srv/containerdata:/blobroot**. Győződjön meg arról, hogy kövesse a [címtár-hozzáférést a tároló felhasználójának](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Windows-tárolók esetén a formátum a * \<tárolási útvonal vagy a kötet>:C:/BlobRoot*. Példa:
         - [kötetcsatlakoztatás](https://docs.docker.com/storage/volumes/)használata: **my-volume:C:/blobroot**.
         - [bind csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata : **C:/ContainerData:C:/BlobRoot**.
         - A helyi meghajtó használata helyett leképezheti az SMB-hálózati helyet, további információért olvassa el [az SMB-megosztás használata helyi tárolóként című témakört.](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Ne módosítsa a tárolócsatlakoztatás imperációs értékének második felét, amely a modul egy adott helyére mutat. A tárolócsatlakoztatásnak mindig **a :/blobroot** kapcsolóval kell végződnie a Linux-tárolók és **a :C:/BlobRoot** Windows-tárolókhoz.

5. A **Modul ikerbeállítások** lapon másolja a következő JSON-t, és illessze be a mezőbe.

   ![Ikermodul beállításai](./media/how-to-deploy-blob/addmodule-tab4.png)

   Konfigurálja az egyes tulajdonságok megfelelő értékkel, ahelyőrzők által jelzett módon. Ha az IoT Edge szimulátort használja, állítsa be az értékeket a kapcsolódó környezeti változókra ezekhez a tulajdonságokhoz az [eszközToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)által leírt tulajdonságokhoz.

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   A készülék konfigurálásával kapcsolatos további tudnivalókért tekintse meg a modul telepítése után a [Készülék](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)Telepítése lehetőséget. A kívánt tulajdonságokról a [Kívánt tulajdonságok definiálása vagy frissítése](module-composition.md#define-or-update-desired-properties)című témakörben talál további információt.

6. Válassza a **Hozzáadás** lehetőséget.

7. Válassza a **Tovább: Útvonalak lehetőséget** az útvonalak szakaszhoz való folytatáshoz.

#### <a name="specify-routes"></a>Útvonalak megadása

Tartsa meg az alapértelmezett útvonalakat, és válassza a **Tovább: Véleményezés + létrehozás** lehetőséget a véleményezési szakasz folytatásához.

#### <a name="review-deployment"></a>Telepítés áttekintése

A véleményezési szakasz a JSON központi telepítési jegyzéket jeleníti meg, amely az előző két szakaszban megadott beállítások alapján jött létre. Van még két modul deklarált, hogy nem adja hozzá: **$edgeAgent** és **$edgeHub**. Ez a két modul alkotja az [IoT Edge futásidejű,](iot-edge-runtime.md) és minden üzembe helyezéskor szükséges alapértelmezett.

Tekintse át a központi telepítési adatokat, majd válassza a **Létrehozás lehetőséget.**

### <a name="verify-your-deployment"></a>A telepítés ellenőrzése

Miután létrehozta a központi telepítést, visszatér az **IoT Edge-lap** az IoT hub.

1. Válassza ki az IoT Edge-eszközt, amelyet a központi telepítéssel célzott meg a részletek megnyitásához.
1. Az eszköz részleteiben ellenőrizze, hogy a blob storage modul szerepel-e **a központi telepítésben megadott** és az eszköz által **jelentett**ként.

Eltarthat néhány percig, amíg a modul elindul az eszközön, és majd jelenteni kell az IoT Hubnak. Frissítse a lapot a frissített állapot megtekintéséhez.

## <a name="deploy-from-visual-studio-code"></a>Telepítés a Visual Studio-kódból

Az Azure IoT Edge sablonokat biztosít a Visual Studio Code-ban, hogy segítsen a peremhálózati megoldások fejlesztésében. Az alábbi lépésekkel hozzon létre egy új IoT Edge-megoldást egy blob tárolómodullal, és konfigurálja a központi telepítési jegyzékfájlt.

1. Válassza **a Nézet** > **parancspaletta**lehetőséget .

1. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot.

   ![Új IoT Edge-megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

   Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki a helyet a fejlesztői gépen a Visual Studio-kódhoz a megoldásfájlok létrehozásához. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldásnak, vagy fogadja el az alapértelmezett **EdgeSolution nevet.** |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Meglévő modul lehetőséget (Adja meg a teljes kép URL-címét).** |
   | Provide a module name (Modulnév megadása) | Adja meg a modul kisbetűs nevét, például **az azureblobstorageoniotedge.enter**a all-case name for your module, like azureblobstoragestorageiotedge .<br/><br/>Fontos, hogy az Azure Blob Storage az IoT Edge modul kisbetűs nevét használja. Az IoT Edge a kis- és nagybetűket, amikor modulokra hivatkozik, és a Storage SDK alapértelmezés szerint kisbetűs. |
   | Docker-rendszerkép biztosítása a modulhoz | Adja meg a lemezkép URI-ját: **mcr.microsoft.com/azure-blob-storage:latest** |

   A Visual Studio Code átveszi a megadott információkat, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakba. A megoldássablon létrehoz egy központi telepítési jegyzékfájl sablont, amely tartalmazza a blob storage modul lemezképét, de konfigurálnia kell a modul létrehozási beállításait.

1. Nyissa *meg a deployment.template.json t* az új megoldás-munkaterületen, és keresse meg a modulok **szakaszt.** Hajtsa végre a következő konfigurációs módosításokat:

   1. Törölje a **SimulatedTemperatureSensor** modult, mivel ez a központi telepítés nem szükséges.

   1. Másolja a következő kódot a `createOptions` mezőbe:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Frissítési modul createOptions - Visual Studio kód](./media/how-to-deploy-blob/create-options.png)

1. Cserélje `<your storage account name>` le egy nevet, hogy emlékszel. A fióknevek nek 3 és 24 karakter között kell lenniük, kisbetűkkel és számokkal. Nincsenek szóközök.

1. Cserélje `<your storage account key>` le egy 64 bájtos alap64 kulcsra. Hozhat létre egy kulcsot eszközökkel, mint [generateplus](https://generate.plus/en/base64). Ezeket a hitelesítő adatokat fogja használni a blobstorage eléréséhez más modulokból.

1. Cserélje `<storage mount>` ki a tároló operációs rendszerének megfelelően. Adja meg a [kötet](https://docs.docker.com/storage/volumes/) nevét vagy az IoT Edge-eszközön lévő könyvtár abszolút elérési útját, ahol a blobmodul tárolja az adatait. A tárolócsatlakoztatás leképezi az eszközön megadott helyet a modul egy meghatározott helyére.  

     - Linux-tárolók esetén a formátum * \<tárolási útvonal vagy kötet>:/blobroot*. Példa:
         - [kötetcsatlakoztatás](https://docs.docker.com/storage/volumes/)használata: **my-volume:/blobroot**
         - [bind-csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata : **/srv/containerdata:/blobroot**. Győződjön meg arról, hogy kövesse a [címtár-hozzáférést a tároló felhasználójának](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Windows-tárolók esetén a formátum a * \<tárolási útvonal vagy a kötet>:C:/BlobRoot*. Példa:
         - [kötetcsatlakoztatás](https://docs.docker.com/storage/volumes/)használata: **my-volume:C:/blobroot**.
         - [bind csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata : **C:/ContainerData:C:/BlobRoot**.
         - A helyi meghajtó használata helyett leképezheti az SMB hálózati helyét, további információkért [lásd: Az SMB-megosztás használata helyi tárolóként](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Ne módosítsa a tárolócsatlakoztatás imperációs értékének második felét, amely a modul egy adott helyére mutat. A tárolócsatlakoztatásnak mindig **a :/blobroot** kapcsolóval kell végződnie a Linux-tárolók és **a :C:/BlobRoot** Windows-tárolókhoz.

1. Konfigurálja [a deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) eszközt a modulhoz a következő JSON hozzáadásával a *deployment.template.json* fájlhoz. Konfigurálja az egyes tulajdonságot a megfelelő értékkel, és mentse a fájlt. Ha az IoT Edge szimulátort használja, állítsa be az értékeket a kapcsolódó környezeti változókhoz ezekhez a tulajdonságokhoz, amelyek et az [eszközToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) magyarázatszakaszában talál.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![az azureblobstorageoniotedge kívánt tulajdonságainak beállítása - Visual Studio-kód](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   A készülék konfigurálásával kapcsolatos további tudnivalókért tekintse meg a modul telepítése után a [Készülék](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)Telepítése lehetőséget. A tárolók létrehozásának beállításairól, az újraindítási házirendről és a kívánt állapotról az [EdgeAgent kívánt tulajdonságai](module-edgeagent-edgehub.md#edgeagent-desired-properties)című témakörben talál további információt.

1. Mentse a *deployment.template.json* fájlt.

1. Kattintson a jobb gombbal **a deployment.template.json fájlra,** és válassza **az IoT Edge telepítési jegyzékfájl létrehozása parancsot.**

1. A Visual Studio Code a *deployment.template.json* fájlban megadott információkat használja fel egy új központi telepítési jegyzékfájl létrehozásához. A központi telepítési jegyzékfájl egy új **konfigurációs** mappában jön létre a megoldás munkaterületén. Miután rendelkezik ezzel a fájllal, kövesse az [Azure IoT Edge-modulok visual studio-kódból történő üzembe helyezésének lépéseit,](how-to-deploy-modules-vscode.md) vagy [telepítheti az Azure IoT Edge-modulokat az Azure CLI 2.0-s használatával.](how-to-deploy-modules-cli.md)

## <a name="deploy-multiple-module-instances"></a>Több modulpéldány telepítése

Ha az Azure Blob Storage több példányát szeretné telepíteni az IoT Edge-modulon, `HostPort` meg kell adnia egy másik tárolási útvonalat, és módosítania kell a modul által kötődik értéket. A blob tárolómodulok mindig elérhetővé teszik az 11002-es portot a tárolóban, de deklarálhatja, hogy melyik porthoz van kötve az állomáson.

A **Container Create Options** (az Azure Portalon) vagy a **createOptions** mező (a Visual Studio-kód `HostPort` *deployment.template.json* fájljában) szerkesztéséhez módosítsa az értéket:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Amikor további blob tárolómodulokhoz csatlakozik, módosítsa a végpontot a frissített gazdaportra.

## <a name="configure-proxy-support"></a>Proxytámogatás konfigurálása

Ha a szervezet proxykiszolgálót használ, konfigurálnia kell az edgeAgent és az edgeHub futásidejű moduljaiproxy-támogatását. Ez a folyamat két feladatot foglal magában:

- Konfigurálja a futásidejű démonokat és az IoT Edge-ügynököt az eszközön.
- Állítsa be a HTTPS_PROXY környezeti változóa a központi telepítési jegyzékfájlJSON-fájlban lévő modulokhoz.

Ezt a folyamatot az [IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációkonfigurálása](how-to-configure-proxy-support.md)című ismertetés ismerteti.

Emellett egy blob tárolási modul is megköveteli a HTTPS_PROXY beállítást a jegyzékfájlban. Közvetlenül szerkesztheti a központi telepítési jegyzékfájl, vagy az Azure Portalon.

1. Keresse meg a sok beli központot az Azure Portalon, és válassza a bal oldali ablaktábla **menüjében** az Iot Edge lehetőséget.

1. Válassza ki a konfigurálni kívánt modullal rendelkező eszközt.

1. Válassza **a Modulok beállítása**lehetőséget.

1. Az **IoT Edge-modulok** szakasza a lap, válassza ki a blob storage modul.

1. Az **IoT Edge Module frissítése** lapon válassza a **Környezeti változók** lapot.

1. Adja `HTTPS_PROXY` meg az **érték** **nevét** és proxy URL-címét.

      ![HTTPS_PROXY környezeti változó beállítása](./media/how-to-deploy-blob/https-proxy-config.png)

1. Kattintson **a Frissítés**gombra, majd **a Véleményezés + Létrehozás parancsra.**

1. Vegye figyelembe, hogy a proxy hozzáadódik a modulhoz a központi telepítési jegyzékfájlban, és válassza a **Create (Létrehozás) lehetőséget.**

1. Ellenőrizze a beállítást az eszköz részletei lapon a modul kiválasztásával, és az **IoT Edge Modules Details** lap alsó részén válassza a **Környezeti változók** lapot.

      ![HTTPS_PROXY környezeti változó beállítása](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>További lépések

További információ az [IoT Edge-en található Azure Blob Storage szolgáltatásról.](how-to-store-data-blob.md)

A központi telepítési jegyzékek működéséről és létrehozásáról további információt [az IoT Edge-modulok használatba és újrafelhasználtkezelésének ismertetése](module-composition.md)című témakörben talál.
