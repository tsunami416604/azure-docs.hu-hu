---
title: Az Azure Blob Storage modul üzembe helyezése az eszközökön – Azure IoT Edge | Microsoft Docs
description: Egy Azure Blob Storage-modul üzembe helyezése az IoT Edge-eszköz a peremhálózaton adatok tárolására.
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: kgremban
manager: mchad
ms.openlocfilehash: 089c90abb999751db77bbe1d89d1d118ae712b52
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947082"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Az Azure Blob Storage üzembe helyezése IoT Edge modulon az eszközön

A modulok több módon is üzembe helyezhetők egy IoT Edge eszközön, és mindegyikük az Azure Blob Storage IoT Edge-modulokban való működéséhez. A két legegyszerűbb módszereket használja az Azure portal vagy a Visual Studio Code-sablonokat.

## <a name="prerequisites"></a>Előfeltételek

- Egy [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
- Egy [IoT Edge-eszköz](how-to-register-device-portal.md) az telepítve van az IoT Edge-futtatókörnyezet.
- A [Visual Studio Code](https://code.visualstudio.com/) és az [Azure IoT eszközei](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , ha a Visual Studio Code-ból telepítenek.

## <a name="deploy-from-the-azure-portal"></a>Üzembe helyezés a Azure Portal

A Azure Portal végigvezeti az üzembe helyezési jegyzék létrehozásán és az üzembe helyezés egy IoT Edge eszközön való továbbításának végrehajtásán.

### <a name="select-your-device"></a>Válassza ki az eszközt

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.
1. Válassza ki **IoT Edge** a menüből.
1. Kattintson az eszközök a listából a célként megadott eszköz Azonosítóját.
1. Válassza a **Modulok beállítása** lehetőséget.

### <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. A Azure Portal tartalmaz egy varázslót, amely végigvezeti az üzembe helyezési jegyzék létrehozásán, a JSON-dokumentum manuális létrehozása helyett. Három lépésből áll: **Modulok hozzáadása**, **útvonalak megadása**és az **üzembe helyezés áttekintése**.

#### <a name="add-modules"></a>Modulok hozzáadása

1. Az a **üzembe helyezési modulok** című oldalon válassza **Hozzáadás**.

1. A legördülő listában válassza ki a modulok típusait **IoT Edge modult**.

1. Adja meg a modul nevét, majd adja meg a tároló rendszerképét:

   - **Név** – azureblobstorageoniotedge
   - **Rendszerkép URI-ja** – MCR.microsoft.com/Azure-Blob-Storage:Latest

   > [!IMPORTANT]
   > A Azure IoT Edge a kis-és nagybetűk megkülönböztetése, ha a modulokra irányuló hívásokat végez, és a Storage SDK is alapértelmezés szerint kisbetűs. Bár az [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) -en a modul neve **AzureBlobStorageonIoTEdge**, a név kisbetűsre való módosítása segít biztosítani, hogy az Azure Blob Storage IoT Edge modulban való kapcsolatai ne legyenek megszakítva.

1. A **tároló-létrehozási beállítások** alapértelmezett értékei határozzák meg a tároló által igényelt port-kötéseket, de a Storage-fiók adatait és egy csatlakoztatást is hozzá kell adnia az eszközön. Cserélje le az alapértelmezett JSON-t a portálon az alábbi JSON-ra:

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

1. Frissítse a vágólapra másolt JSON-t a következő információkkal:

   - Cserélje `<your storage account name>` le a nevet, és jegyezze fel. A fiókok nevének 3 – 24 karakter hosszúnak kell lennie, kisbetűkkel és számokkal. Nincsenek szóközök.

   - Cserélje `<your storage account key>` le egy 64 bájtos Base64-kulccsal. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni.

   - Cserélje `<storage mount>` le at a tároló operációs rendszerének megfelelően. Adja meg a nevét egy [kötet](https://docs.docker.com/storage/volumes/) vagy az IoT Edge-eszköz, ahol azt szeretné, hogy a blob modul tárolja az adatokat egy könyvtár abszolút elérési útját. A Storage-csatlakoztatás leképezi az eszközön az Ön által megadott helyet a modul egy készletének megfelelő helyére.

     - Linux-tárolók esetén a formátum  *\<a tárolási útvonal vagy a kötet >:/blobroot*. Példa:
         - a [Volume Mount](https://docs.docker.com/storage/volumes/)használata: **saját kötet:/blobroot** 
         - használja a [kötés csatlakoztatása](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Ügyeljen arra, hogy a címtár- [hozzáférés biztosítása a tároló felhasználójának](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) lépéseit kövesse.
     - Windows-tárolók esetén a formátum  *\<a tárolási útvonal vagy a kötet >: C:/BlobRoot*. Példa:
         - [kötet csatlakoztatása](https://docs.docker.com/storage/volumes/): **saját kötet: C:/blobroot**. 
         - [kötési csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata: **C:/ContainerData: c:/BlobRoot**.
         - A helyi meghajtó használata helyett leképezheti az SMB hálózati helyét, és további információt az SMB- [megosztás használata helyi tárolóként](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) című témakörben talál.

     > [!IMPORTANT]
     > Ne módosítsa a tárolási csatlakoztatási érték második felét, amely a modul egy adott helyére mutat. A tárolási csatlakoztatásnak mindig a következővel kell végződnie **:/blobroot** for Linux containers and **: C:/blobroot** for Windows containers.

1. Állítsa be a modul [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) tulajdonságait úgy, hogy a következő JSON-t másolja, és beilleszti a **set Module Twin 's kívánt tulajdonságok** mezőbe. Konfigurálja az egyes tulajdonságokat megfelelő értékkel, mentse, majd folytassa a telepítést.

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
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
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![set container create options, deviceAutoDeleteProperties and deviceToCloudUploadProperties properties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   További információ a deviceToCloudUploadProperties és a deviceAutoDeleteProperties konfigurálásáról a modul üzembe helyezése után: [a különálló modul szerkesztése](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). A kívánt tulajdonságokkal kapcsolatos további információkért lásd: a [kívánt tulajdonságok megadása vagy frissítése](module-composition.md#define-or-update-desired-properties).

1. Kattintson a **Mentés** gombra.

1. Válassza ki **tovább** továbbra is az útvonalak szakaszban.

#### <a name="specify-routes"></a>Útvonalak megadása

Tartsa meg az alapértelmezett útvonalakat, és kattintson a **tovább** gombra a felülvizsgálati szakasz folytatásához.

#### <a name="review-deployment"></a>Tekintse át a központi telepítés

A tekintse át a szakasz azt mutatja be, a JSON-telepítésben manifest létrehozott az előző két szakaszokban alapján. Két modul is jelent meg, amelyeket nem adott hozzá: **$edgeAgent** és **$edgeHub**. Ezeket a modulokat két alkotják a [IoT Edge-futtatókörnyezet](iot-edge-runtime.md) és minden üzembe helyezés a szükséges alapértelmezett értékek.

Tekintse át a telepítési adatokat, majd válassza a **küldés**.

### <a name="verify-your-deployment"></a>Az üzemelő példány ellenőrzése

Miután elküldte az üzembe helyezést, térjen vissza az IoT hub **IoT Edge** lapjára.

1. Válassza ki azt a IoT Edge eszközt, amelyet a központi telepítéshez céloz, hogy megnyissa a részleteit.
1. Az eszköz részletei között ellenőrizze, hogy a blob Storage modul a telepítésben és az **eszköz által jelentett**módon van **-** e felsorolva.

Néhány percet is igénybe vehet, amíg a modul elindult az eszközön, majd visszaküldhető a IoT Hubra. Frissítse az oldalt, és tekintse meg a frissített állapotot.

## <a name="deploy-from-visual-studio-code"></a>Üzembe helyezés a Visual Studio Code-ból

Az Azure IoT Edge segítségével peremhálózati megoldásokat fejleszthet a Visual Studio Code-sablonok biztosít. A következő lépésekkel hozzon létre egy új IoT Edge-megoldást egy blob Storage-modullal, és konfigurálja az üzembe helyezési jegyzéket.

1. Válassza ki **nézet** > **paletta parancs**.

1. A Command paletta írja be és futtassa a parancsot **Azure IoT Edge: Új IoT Edge megoldás**.

   ![Új IoT Edge megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

   Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki a helyet a fejlesztői gépen a Visual Studio Code-hoz a megoldás fájljainak létrehozásához. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon egy **meglévő modult (adja meg a teljes képet URL-címet)** . |
   | Provide a module name (Modulnév megadása) | Adjon meg egy teljes kisbetűs nevet a modulhoz, például **azureblobstorageoniotedge**.<br /><br />Fontos, hogy a IoT Edge modul Azure Blob Storage kisbetűs nevét használja. A IoT Edge a kis-és nagybetűk megkülönböztetésére szolgál, ha a modulokra hivatkozik, és a Storage SDK alapértelmezett értéke kisbetűs. |
   | Docker-rendszerkép megadása a modulhoz | Adja meg a rendszerkép URI-JÁT: **MCR.microsoft.com/Azure-Blob-Storage:Latest** |

   A Visual Studio Code felveszi a megadott adatokat, létrehoz egy IoT Edge megoldást, majd betölti azt egy új ablakban. A megoldássablon hoz létre, amely tartalmazza a blob storage modul rendszerképének jegyzékfájl a központi telepítési sablont, de be kell állítania a modul a létrehozási beállítások.

1. Nyissa meg *deployment.template.json* az új megoldás munkaterületet, és a Keresés a **modulok** szakaszban. Végezze el a következő konfigurációs módosításokat:

   1. Törölje a **SimulatedTemperatureSensor** modult, mivel ez nem szükséges ehhez a központi telepítéshez.

   1. Másolja és illessze be a következő kódot a `createOptions` mezőbe:

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

      ![CreateOptions modul frissítése – Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Cserélje `<your storage account name>` le a nevet, és jegyezze fel. A fiókok nevének 3 – 24 karakter hosszúnak kell lennie, kisbetűkkel és számokkal. Nincsenek szóközök.

1. Cserélje `<your storage account key>` le egy 64 bájtos Base64-kulccsal. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni.

1. Cserélje `<storage mount>` le at a tároló operációs rendszerének megfelelően. Adja meg a nevét egy [kötet](https://docs.docker.com/storage/volumes/) vagy az IoT Edge-eszköz, ahol azt szeretné, hogy a blob modul tárolja az adatokat egy könyvtár abszolút elérési útját. A Storage-csatlakoztatás leképezi az eszközön az Ön által megadott helyet a modul egy készletének megfelelő helyére.  

      
     - Linux-tárolók esetén a formátum  *\<a tárolási útvonal vagy a kötet >:/blobroot*. Példa:
         - a [Volume Mount](https://docs.docker.com/storage/volumes/)használata: **saját kötet:/blobroot** 
         - használja a [kötés csatlakoztatása](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Ügyeljen arra, hogy a címtár- [hozzáférés biztosítása a tároló felhasználójának](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) lépéseit kövesse.
     - Windows-tárolók esetén a formátum  *\<a tárolási útvonal vagy a kötet >: C:/BlobRoot*. Példa:
         - [kötet csatlakoztatása](https://docs.docker.com/storage/volumes/): **saját kötet: C:/blobroot**. 
         - [kötési csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata: **C:/ContainerData: c:/BlobRoot**.
         - A helyi meghajtó használata helyett leképezheti az SMB hálózati helyét, és további információt az SMB- [megosztás használata helyi tárolóként](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) című témakörben talál.

     > [!IMPORTANT]
     > Ne módosítsa a tárolási csatlakoztatási érték második felét, amely a modul egy adott helyére mutat. A tárolási csatlakoztatásnak mindig a következővel kell végződnie **:/blobroot** for Linux containers and **: C:/blobroot** for Windows containers.

1. Konfigurálja a [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) a modulhoz úgy, hogy hozzáadja a következő JSON-t a *Deployment. template. JSON* fájlhoz. Konfigurálja az egyes tulajdonságokat megfelelő értékkel, és mentse a fájlt.

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

   ![a azureblobstorageoniotedge kívánt tulajdonságainak beállítása – Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   További információ a deviceToCloudUploadProperties és a deviceAutoDeleteProperties konfigurálásáról a modul üzembe helyezése után: [a különálló modul szerkesztése](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). A tároló-létrehozási lehetőségekről, a szabályzatok újraindításáról és a kívánt állapotról további információt a [EdgeAgent kívánt tulajdonságok](module-edgeagent-edgehub.md#edgeagent-desired-properties)című témakörben talál.

1. Mentse a *deployment.template.json* fájlt.

1. Kattintson a jobb gombbal **deployment.template.json** válassza **hozzon létre IoT Edge-manifest nasazení**.

1. A Visual Studio Code megtekinti az *üzembe helyezés. sablon. JSON* fájlban megadott adatokat, és felhasználja egy új központi telepítési jegyzékfájl létrehozásához. Manifest nasazení jön létre egy új **config** a megoldás munkaterület mappájában. Miután ezt a fájlt, a lépésekkel [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md) vagy [üzembe helyezése az Azure IoT Edge-modulok az Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Több Modulos példány üzembe helyezése

Ha az Azure Blob Storage több példányát is telepíteni szeretné IoT Edge modulon, meg kell adnia egy másik tárolási útvonalat, és módosítania kell `HostPort` a modulhoz kötődő értéket. A blob storage-modulok mindig tegye elérhetővé a port 11002 a tárolóban, de deklarálhatja, hogy melyik portot a gazdagépen van kötve.

Módosítsa a`HostPort` **tároló létrehozási beállításait** (a Azure Portal) vagy a createOptions mezőt (a Visual Studio Code-ban található *Deployment. template. JSON* fájlban) a következő érték megváltoztatásához:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

További blob storage-modulokkal való csatlakozáskor módosítsa a végpontot, hogy a gazdagép frissített portra mutat.

## <a name="next-steps"></a>További lépések
További információ az [Azure Blob Storageról IoT Edge](how-to-store-data-blob.md)

Hogyan alkalmazásjegyzékeket az üzembe helyezési a munkahelyi, és hogyan hozhat létre, azokat kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).
