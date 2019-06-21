---
title: Az Azure Blob Storage-modul üzembe helyezése az Azure IoT Edge-eszközök – |} A Microsoft Docs
description: Egy Azure Blob Storage-modul üzembe helyezése az IoT Edge-eszköz a peremhálózaton adatok tárolására.
author: arduppal
ms.author: arduppal
ms.date: 06/19/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: 468e4fca5e67850949e7d5826e4bc88fa504b9d6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295187"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Az eszköz üzembe helyezése az Azure Blob Storage, az IoT Edge-modul

Többféleképpen is a modulok IoT Edge-eszköz üzembe helyezése és azokat az Azure Blob Storage az IoT Edge-modulok. A két legegyszerűbb módszereket használja az Azure portal vagy a Visual Studio Code-sablonokat.

> [!NOTE]
> Az Azure Blob Storage, az IoT Edge-ben van [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

- Egy [az IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
- Egy [IoT Edge-eszköz](how-to-register-device-portal.md) az telepítve van az IoT Edge-futtatókörnyezet.
- [A Visual Studio Code](https://code.visualstudio.com/) és a [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Ha üzembe helyezése a Visual Studio Code-ot.

## <a name="deploy-from-the-azure-portal"></a>Az Azure Portalról üzembe helyezése

Az Azure Portalon végigvezeti a központi telepítési jegyzékfájl létrehozása és az üzembe helyezés leküldése az IoT Edge-eszköz.

### <a name="select-your-device"></a>Válassza ki az eszközt

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , és keresse meg az IoT hubot.
1. Válassza ki **IoT Edge** a menüből.
1. Kattintson az eszközök a listából a célként megadott eszköz Azonosítóját.
1. Válassza a **Modulok beállítása** lehetőséget.

### <a name="configure-a-deployment-manifest"></a>A manifest nasazení konfigurálása

A manifest nasazení egy JSON-dokumentum, amely azt ismerteti, hogy mely modulok üzembe helyezéséhez a modulokat, és az ikermodulokkal tulajdonságaiként közti adatfolyamok. Az Azure Portalon rendelkezik egy varázslót, amely egy központi telepítési jegyzékfájlt, a JSON-dokumentum manuálisan semmint létrehozásának módját ismerteti. Ez a három lépésből áll: **Modulok hozzáadása**, **útvonalak megadása**, és **tekintse át a központi telepítési**.

#### <a name="add-modules"></a>Modulok hozzáadása

1. Az a **üzembe helyezési modulok** című oldalon válassza **Hozzáadás**.

1. Válassza ki a legördülő listából válassza ki a modulok típusú **IoT Edge-modul**.

1. Adja meg a modul nevét, és adja meg a tároló rendszerképét:

   - **Név** -azureblobstorageoniotedge
   - **Lemezkép URI** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Hívások modulokat, és a Storage SDK-t is kisbetűssé alapértelmezés szerint az Azure IoT Edge egy kis-és nagybetűket. Bár a modul neve a [Azure Marketplace-en](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) van **AzureBlobStorageonIoTEdge**, annak érdekében, hogy a kapcsolatok az Azure Blob Storage az IoT Edge-modul segítségével kisbetűssé nevének módosítása nem szakadnak meg.

1. Az alapértelmezett **tároló létrehozása beállítások** értékek definiálása a port-kötéseket, amely a tárolóra van szüksége, de is hozzá kell a storage-fiók adatait és a egy kötést a tároló könyvtár az eszközön. Cserélje le az alapértelmezett JSON a portálon az alábbi JSON-ban:

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

1. Frissítse a JSON-t másolta a következő információkat:

   - Cserélje le `<your storage account name>` emlékszik névvel. Fiók nevének 3 – 24 karakter hosszúságú, kisbetűket és számokat kell lennie. Nem tartalmazhat szóközöket.

   - Cserélje le `<your storage account key>` 64 bájt méretű base64 kulccsal. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni.

   - Cserélje le `<storage directory bind>` az operációs rendszer alapján. Adja meg a nevét egy [kötet](https://docs.docker.com/storage/volumes/) vagy az IoT Edge-eszköz, ahol azt szeretné, hogy a blob modul tárolja az adatokat egy könyvtár abszolút elérési útját. Az storage directory kötés az eszközön, akkor adjon meg egy helyet a hely beállítása a modul képezi le.

     - Linux-tárolók esetén a formátum a következő  *\<. tárolási elérési útja >: / blobroot*. Ha például **/srv/containerdata: / blobroot** vagy **saját kötet: / blobroot**.
     - A Windows-tárolók, formátuma  *\<. tárolási elérési útja >: C: / BlobRoot*. Ha például **C: / ContainerData:C: / BlobRoot** vagy **saját-kötet: C: / blobroot**.

     > [!IMPORTANT]
     > Ne változtassa meg a második fele az storage directory kötést, érték, amely a modul egy adott helyre mutat. A storage directory kötési mindig kell végződnie **: / blobroot** Linux-tárolókhoz és **: C: / BlobRoot** Windows-tárolókhoz.

1. Állítsa be [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) tulajdonságok a modul által a következő JSON másolása és beillesztése be azt a **Set ikermodul kívánt Tulajdonságok** mezőbe. Minden egyes tulajdonság beállítása a megfelelő értéket, és mentse a telepítés folytatásához.

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

   DeviceToCloudUploadProperties és deviceAutoDeleteProperties konfigurálásával, a modul telepítését követően további információkért lásd: [szerkesztése az Ikermodul](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Kívánt tulajdonságok kapcsolatos további információkért lásd: [meghatározása vagy a frissítés kívánt tulajdonságok](module-composition.md#define-or-update-desired-properties).

1. Kattintson a **Mentés** gombra.

1. Válassza ki **tovább** továbbra is az útvonalak szakaszban.

#### <a name="specify-routes"></a>Útvonalak megadása

Hagyja meg az alapértelmezett útvonalakat, és válassza ki **tovább** továbbra is a felülvizsgálati szakaszban.

#### <a name="review-deployment"></a>Tekintse át a központi telepítés

A tekintse át a szakasz azt mutatja be, a JSON-telepítésben manifest létrehozott az előző két szakaszokban alapján. Vannak még két modul deklarálva, hogy nem adta hozzá: **$edgeAgent** és **$edgeHub**. Ezeket a modulokat két alkotják a [IoT Edge-futtatókörnyezet](iot-edge-runtime.md) és minden üzembe helyezés a szükséges alapértelmezett értékek.

Tekintse át a telepítési adatokat, majd válassza a **küldés**.

### <a name="verify-your-deployment"></a>A telepítés ellenőrzése

Miután az üzembe helyezés elküldött, akkor térjen vissza a **IoT Edge** az IoT hub oldalon.

1. Válassza ki az IoT Edge-eszköz célként való üzembe helyezéséhez, hogy megnyissa annak részletes adatait.
1. Az eszköz részletek győződjön meg arról, hogy a blob storage-modulban is szerepel a listán **központi telepítésben megadott** és **eszköz által jelentett**.

A modul elindult az eszközön, és ezután jelentett vissza az IoT hub egy kis ideig is eltarthat. Frissítse az oldalt, hogy a frissített állapotot.

## <a name="deploy-from-visual-studio-code"></a>Üzembe helyezés a Visual Studio Code-ot

Az Azure IoT Edge segítségével peremhálózati megoldásokat fejleszthet a Visual Studio Code-sablonok biztosít. Manifest nasazení konfigurálása és a egy új IoT Edge-megoldás létrehozása a blob storage-modulban, kövesse az alábbi lépéseket.

1. Válassza ki **nézet** > **paletta parancs**.

1. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**.

   ![Új IoT Edge-megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

   Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki a helyet, a Visual Studio Code a megoldás-fájlok létrehozása a fejlesztői gépen. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **meglévő modul (adjon meg teljes URL-címe)** . |
   | Provide a module name (Modulnév megadása) | Adja meg például az all-kisbetűk nevét, a modul **azureblobstorageoniotedge**.<br /><br />Fontos kisbetűs nevét az Azure Blob Storage használata az IoT Edge-modul. IoT Edge egy kis-és nagybetűket, modulok kontextusban való megnevezésekor, és a Storage SDK alapértelmezés szerint a kisbetűs. |
   | A modul adja meg a Docker-rendszerkép | Adja meg a lemezkép URI-ja: **mcr.microsoft.com/azure-blob-storage:latest** |

   A Visual Studio Code-ot tart a megadott információt, létrehoz egy IoT Edge-megoldást, majd betölti azt egy új ablakban. A megoldássablon hoz létre, amely tartalmazza a blob storage modul rendszerképének jegyzékfájl a központi telepítési sablont, de be kell állítania a modul a létrehozási beállítások.

1. Nyissa meg *deployment.template.json* az új megoldás munkaterületet, és a Keresés a **modulok** szakaszban. A következő konfigurációs módosításokat:

   1. Törölje a **tempSensor** modult, mert a nem ehhez a telepítéshez szükséges.

   1. Másolja és illessze be az alábbi kódot a `createOptions` mező:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![A modul createOptions – Visual Studio Code frissítése](./media/how-to-deploy-blob/create-options.png)

1. Cserélje le `<your storage account name>` emlékszik névvel. Fiók nevének 3 – 24 karakter hosszúságú, kisbetűket és számokat kell lennie. Nem tartalmazhat szóközöket.

1. Cserélje le `<your storage account key>` 64 bájt méretű base64 kulccsal. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni.

1. Cserélje le `<storage directory bind>` az operációs rendszer alapján. Adja meg a nevét egy [kötet](https://docs.docker.com/storage/volumes/) vagy az IoT Edge-eszköz, ahol azt szeretné, hogy a blob modul tárolja az adatokat egy könyvtár abszolút elérési útját. Az storage directory kötés az eszközön, akkor adjon meg egy helyet a hely beállítása a modul képezi le.  

      - Linux-tárolók esetén a formátum a következő  *\<. tárolási elérési útja >: / blobroot*. Ha például **/srv/containerdata: / blobroot** vagy **saját kötet: / blobroot**.
      - A Windows-tárolók, formátuma  *\<. tárolási elérési útja >: C: / BlobRoot*. Ha például **C: / ContainerData:C: / BlobRoot** vagy **saját-kötet: C: / blobroot**.

      > [!IMPORTANT]
      > Ne változtassa meg a második fele az storage directory kötést, érték, amely a modul egy adott helyre mutat. A storage directory kötési mindig kell végződnie **: / blobroot** Linux-tárolókhoz és **: C: / BlobRoot** Windows-tárolókhoz.

1. Konfigurálása [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) a modul a következő JSON-hozzáadásával a *deployment.template.json* fájlt. Egyes tulajdonságok konfigurálása a megfelelő értéket, és mentse a fájlt.

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

   ![Állítsa be a kívánt tulajdonságainak azureblobstorageoniotedge – Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   DeviceToCloudUploadProperties és deviceAutoDeleteProperties konfigurálásával, a modul telepítését követően további információkért lásd: [szerkesztése az Ikermodul](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). További információ a tároló létrehozási beállítások, indítsa újra a házirendet és a kívánt állapot, lásd: [EdgeAgent kívánt tulajdonságok](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Mentse a *deployment.template.json* fájlt.

1. Kattintson a jobb gombbal **deployment.template.json** válassza **hozzon létre IoT Edge-manifest nasazení**.

1. Visual Studio Code-ot tart a megadott információkat *deployment.template.json* , és hozzon létre egy új központi telepítési manifest fájlt használja. Manifest nasazení jön létre egy új **config** a megoldás munkaterület mappájában. Miután ezt a fájlt, a lépésekkel [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md) vagy [üzembe helyezése az Azure IoT Edge-modulok az Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Több modul-példány üzembe helyezése

Ha szeretné az Azure Blob Storage, az IoT Edge-modul több példányának telepítése, szeretné-e adjon meg egy másik tárolási elérési útja, és módosítsa a `HostPort` érték, amely a modul van kötve. A blob storage-modulok mindig tegye elérhetővé a port 11002 a tárolóban, de deklarálhatja, hogy melyik portot a gazdagépen van kötve.

Szerkesztés **tároló létrehozása beállítások** (az Azure Portalon) vagy a **createOptions** mező (a a *deployment.template.json* fájlt a Visual Studio Code) a módosítása`HostPort` érték:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

További blob storage-modulokkal való csatlakozáskor módosítsa a végpontot, hogy a gazdagép frissített portra mutat.

## <a name="next-steps"></a>További lépések

Hogyan alkalmazásjegyzékeket az üzembe helyezési a munkahelyi, és hogyan hozhat létre, azokat kapcsolatos további információkért lásd: [megismerheti, hogyan IoT Edge-modulok használják, konfigurálhatók, és újra felhasználható](module-composition.md).
