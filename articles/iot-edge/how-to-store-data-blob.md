---
title: Az Azure IoT Edge-eszközök – a blokkblobok Store |} A Microsoft Docs
description: Egy Azure Blob Storage-modul üzembe helyezése az IoT Edge-eszköz a peremhálózaton adatok tárolására.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3a0df408e70ed61355ffba319f6261f90d8e4348
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499158"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Az Azure Blob Storage a peremhálózaton data Store az IoT Edge-ben (előzetes verzió)

Az IoT Edge-ben az Azure Blob Storage biztosít egy [blokkblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) tárolási megoldás a peremhálózaton. Az Azure block blob szolgáltatás viselkedik az IoT Edge-eszközön a blob storage-modulban, de a blokkblobok használatát támogatják a rendszer helyben tárolja az IoT Edge-eszközön. Ugyanazokkal a módszerekkel az Azure storage SDK-t a blobok elérése, vagy a blob API-hívás, amely már jártas letiltása. 

Ez a modul tartalmaz **automatikus rétegezést** és **Auto-lejárati** funkciókat.

> [!NOTE]
> Jelenleg automatikus rétegezést és az automatikus-lejárati funkciók érhetők el csak a Linux-AMD64 és a Linux ARM32.

**Automatikus rétegezést** van egy konfigurálható funkcióval rendelkezik, amely lehetővé teszi, hogy automatikusan tölti fel az adatok a helyi blob storage-ból az Azure-bA időszakos internetes kapcsolat támogatást. Lehetővé teszi:
- Kapcsolja be-/ kikapcsolási a rétegezési szolgáltatás
- Válassza ki a sorrendet, amelyben az adatok lesznek másolva NewestFirst vagy OldestFirst hasonló Azure-bA
- Adja meg az Azure Storage-fiókot, amely a feltöltött adatok kívánja.
- Adja meg a tárolók az Azure-bA feltöltendő. Ez a modul lehetővé teszi a forrás és a cél a tároló nevének megadását.
- Teljes blob rétegezés (használatával `Put Blob` művelet) és szolgáltatói rétegezés letiltása (használatával `Put Block` és `Put Block List` műveletek).

Ez a modul használja letiltása szintű rétegzést, ha az a blob blokkokat tartalmaz. Íme néhány gyakori helyzetek:
- Az alkalmazás egyes blokkolja a korábban feltöltött blob frissíti, ez a modul fogja feltölteni, csak a frissített blokkok és nem a teljes blob.
- A modul blob van feltöltésével, és internetkapcsolat eltűnik, az internetkapcsolat esetén vissza újra lesz az csak a fennmaradó blokkok és nem a teljes blobot tölthet fel.

Egy váratlan folyamatmegszakítás (például áramkimaradás) egy blob feltöltése közben történik, ha minden blokkok, amelyek korábban a feltöltés esedékes lesz feltöltve újra, ha a modul visszatér online állapotba.

**Automatikus – lejárati** konfigurálható funkció van, ahol ez a modul automatikusan törli a blobokat a helyi tárolóból élettartamot (TTL) letelte. Mérik, hogy percek alatt. Lehetővé teszi:
- Kapcsolja be-/ kikapcsolási az auto-lejárati szolgáltatást
- Adja meg az élettartam percben

Forgatókönyvek, ahol adatokat, például videók, képek, kampányfinanszírozási adatok szórakoztató, kórházi adatok vagy adatok helyben, később kell tárolni, amelyek helyileg sikerült feldolgozni, vagy jó példák töltődnek fel a felhőbe, ez a modul.

Ez a cikk ismerteti, hogyan telepíthető egy Azure Blob Storage, blob szolgáltatás az IoT Edge-eszközön futó IoT Edge-tárolón. 

>[!NOTE]
>Az Azure Blob Storage, az IoT Edge-ben van [nyilvános előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

A cikk gyors ismertetést nyújt a videó megtekintése
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

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

   4. Állítsa be [automatikus rétegezést és az automatikus-lejárati](#configure-auto-tiering-and-auto-expiration-via-azure-portal) a kívánt tulajdonságok. Listázás: [automatikus rétegezést](#auto-tiering-properties) és [auto-lejárati](#auto-expiration-properties) tulajdonságok és azok lehetséges értékei. 

   5. Kattintson a **Mentés** gombra. 

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

5. Konfigurálása [automatikus rétegezést és az automatikus-lejárati](#configure-auto-tiering-and-auto-expiration-via-vscode). Listázás: [automatikus rétegezést](#auto-tiering-properties) és [auto-lejárati](#auto-expiration-properties) tulajdonságai

6. Mentse a **deployment.template.json** fájlt.

7. Nyissa meg a **.env** a megoldás munkaterületen lévő fájlt. 

8. A .env fájl container registry hitelesítő adatok fogadására van beállítva, de már nincs szüksége, amely a blob storage-lemezkép, mivel a nyilvánosan elérhető. Ehelyett cserélje le a fájl két új környezeti változókat: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. Adjon meg egy értéket a `STORAGE_ACCOUNT_NAME`, fiókneveket három való huszonnégy karakteres, kisbetűket és számokat kell lennie. Adjon meg egy 64 bájt méretű base64 kulcsot a `STORAGE_ACCOUNT_KEY`. Létrehozhat egy kulcsot a hasonló eszközök [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). A blobtároló eléréséhez az egyéb modulok ezeket a hitelesítő adatokat fogja használni. 

   Nem tartalmazza a szóközöket vagy az Ön értékeket idézőjelek közé. 

10. Mentse az **.env** fájlt. 

11. Kattintson a jobb gombbal **deployment.template.json** válassza **hozzon létre IoT Edge-manifest nasazení**. 

12. A Visual Studio Code az adatokat a deployment.template.json és .env, és a segítségével hozzon létre egy új központi telepítési jegyzékfájl vesz igénybe. Manifest nasazení jön létre egy új **config** a megoldás munkaterület mappájában. Miután ezt a fájlt, a lépésekkel [üzembe helyezése az Azure IoT Edge-modulok Visual Studio Code-ból](how-to-deploy-modules-vscode.md) vagy [üzembe helyezése az Azure IoT Edge-modulok az Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>Automatikus rétegezést és az automatikus-lejárati tulajdonságai, és a konfiguráció

Kívánt tulajdonságok használatával állítsa be az automatikus rétegezést és az automatikus-lejárati tulajdonságai. Ezeket üzembe helyezés során vagy később módosítani az ikermodul ismételt üzembe helyezése nélkül szerkesztésével. Javasoljuk, hogy a "Ikermodul" ellenőrzése a `reported configuration` és `configurationValidation` , hogy megfelelően propagálva értékeket.

### <a name="auto-tiering-properties"></a>Automatikus rétegezést tulajdonságai 
Ez a beállítás neve `tieringSettings`

| Mező | Lehetséges értékek | Magyarázat |
| ----- | ----- | ---- |
| tieringOn | IGAZ, hamis | Alapértelmezés szerint van beállítva `false`, ha azt szeretné a értékre `true`|
| backlogPolicy | NewestFirst, OldestFirst | Lehetővé teszi, hogy kiválaszthatja a sorrendet, amelyben az adatok lesz másolva az Azure-bA. Alapértelmezés szerint van beállítva `OldestFirst`. A sorrend határozza meg BLOB utolsó módosítás időpontja |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` kapcsolati karakterlánc, amely lehetővé teszi, hogy adja meg az Azure Storage-fiók, amelybe az adatok feltöltése. Adja meg `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Adja hozzá a megfelelő EndpointSuffix az Azure, hol fogja feltöltött adatmennyiség, a globális Azure, Government Azure és a Microsoft Azure Stack változik. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Az Azure-bA feltöltendő a tároló nevének megadását teszi lehetővé. Ez a modul lehetővé teszi a forrás és a cél a tároló nevének megadását. Ha nem adja meg a tároló nevét, akkor automatikusan hozzárendeli a tároló neve megegyezik `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. A célként megadott Tárolónév esetén a lehetséges értékek oszlop fizetési sablon karakterláncok hozhat létre. <br>* %h -> az IoT Hub nevére (3 – 50 karakter). <br>* %d IoT device Id (1. 129 karakternél) ->. <br>* %m -> modul neve (1 – 64 karakter). <br>* %c -> Forrástároló neve (3 – 63 karakter). <br><br>A tároló nevének maximális mérete automatikusan hozzárendelése során a tároló nevét, ha a tároló mérete meghaladja azt fogja trim 63 karakter minden szakasz (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) – 15 karakter 63 karakter. |

### <a name="auto-expiration-properties"></a>Automatikus – lejárati tulajdonságai
Ez a beállítás neve `ttlSettings`

| Mező | Lehetséges értékek | Magyarázat |
| ----- | ----- | ---- |
| ttlOn | IGAZ, hamis | Alapértelmezés szerint van beállítva `false`, ha azt szeretné a értékre `true`|
| timeToLiveInMinutes | `<minutes>` | Adja meg az élettartam percben. A modul automatikusan törli a blobokat a helyi tárolóból TTL lejárata |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Automatikus rétegezést és az automatikus-lejárati konfigurálása Azure-portálon

Állítsa be a kívánt tulajdonságokat engedélyezése automatikus rétegezést és auto-lejárati, beállíthatja ezeket az értékeket:

- **A kezdeti üzembe helyezése során**: Másolja ki a JSON-t **Set ikermodul kívánt tulajdonságai** mezőbe. Minden egyes tulajdonság konfigurálása a megfelelő értéket, mentse, és a telepítés folytatásához.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

  ![automatikus rétegezést és az automatikus-lejárati tulajdonságainak beállítása](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **A modul "Ikermodul Identity" funkciójával üzembe helyezését követően**: Ugrás a "Identity Ikermodulja" Ez a modul, másolja ki a kívánt tulajdonságok a JSON, minden egyes tulajdonság konfigurálása a megfelelő értéket, és mentse. "Ikermodul Identity" Json-fájlban ellenőrizze, hogy minden alkalommal, amikor hozzáadásakor vagy bármelyik kívánt tulajdonságot, a `reported configuration` szakasz tükrözi a módosításokat, és a `configurationValidation` szakasz mindegyik tulajdonság sikeres befejezést jelent.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```

![élettartam module_identity_twin rétegezési +](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>Konfigurálja az automatikus rétegezést és az automatikus-lejárati VSCode-n keresztül

- **A kezdeti üzembe helyezése során**: Adja hozzá az alábbi JSON-t a deployment.template.json a modulhoz tartozó kívánt tulajdonságainak definiálásához. Minden egyes tulajdonság konfigurálása a megfelelő értéket, és mentse azt.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

Ez a modul a kívánt tulajdonságok, például: ![azureblobstorageoniotedge – VS Code kívánt tulajdonságainak beállítása](./media/how-to-store-data-blob/tiering_ttl.png)

- **A modul "Ikermodul" keresztül üzembe helyezését követően**: [Szerkessze az Ikermodul](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) , ez a modul másolja a JSON a kívánt tulajdonságok minden egyes tulajdonság konfigurálása a megfelelő értéket, és mentse. "Ikermodul" Json-fájlban ellenőrizze, hogy minden alkalommal, amikor hozzáadásakor vagy bármelyik kívánt tulajdonságot, a `reported configuration` szakasz tükrözi a módosításokat, és a `configurationValidation` szakasz mindegyik tulajdonság sikeres befejezést jelent.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```
## <a name="logs"></a>Logs

Kövesse a lépéseket a [konfigurálása az IoT Edge-modulok a docker-naplók](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>Csatlakozás a blob storage-modulban

A fiók neve és a fiókkulcsot, hogy konfigurálta-e a modul az IoT Edge-eszközön a blobtároló elérésére használható. 

Adja meg a tárolási blob végpontja az IoT Edge-eszköz végrehajtott módosítások hozzá kérelmeket. Is [hozzon létre egy kapcsolati karakterláncot egy explicit storage-végpont](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) az IoT Edge-eszköz adatait és a beállított fiók nevét. 

1. A modulok, ugyanazon az "Azure Blob Storage az IoT Edge" futtató peremhálózati eszközön van telepítve, a blob végpontja van: `http://<module name>:11002/<account name>`. 
2. A modulok, telepített különböző peremhálózati eszközön, mint a peremhálózati eszköz, amelyben "Azure Blob Storage az IoT Edge" fut, akkor a telepítő a blob végpontja készlettől van: `http://<device IP >:11002/<account name>` vagy `http://<IoT Edge device hostname>:11002/<account name>` vagy `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>Több példány üzembe helyezése

Ha azt szeretné, az Azure Blob Storage, az IoT Edge-ben több példányának telepítése, adja meg a különböző tárolási elérési útja, és módosítsa a hostport paramétert, amely összeköti a modul szüksége. A blob storage-modulok mindig tegye elérhetővé a port 11002 a tárolóban, de deklarálhatja, hogy melyik portot a gazdagépen van kötve. 

A modul szerkesztése lehetőség kiválasztásával módosíthatja a hostport paramétert érték létrehozása:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

További blob storage-modulokkal való csatlakozáskor módosítsa a végpontot, hogy a gazdagép frissített portra mutat. 

## <a name="try-it-out"></a>Próbálja ki!

### <a name="azure-blob-storage-quickstart-samples"></a>Az Azure Blob Storage rövid útmutatójával minták
Az Azure Blob Storage dokumentációja tartalmazza az rövid útmutatók, amelyek számos nyelven mintakódot. Ezek a minták teszteléséhez az IoT Edge-ben az Azure Blob Storage a blob végpontja, hogy a blob storage-modulban mutasson módosításával futtathatja. Kövesse a lépéseket a [csatlakozni a blob storage-modulban](#connect-to-your-blob-storage-module)

Az alábbi rövid útmutatókat használja, így sikerült telepítheti őket, IoT Edge-modulok mellett a blob storage-modulban is IoT Edge által támogatott nyelvek:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
"Az Azure Storage Explorer" a helyi storage-fiókhoz való csatlakozáshoz is kipróbálhatja. Együttműködik az [Azure Storage Explorer verziója 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0).

> [!NOTE]
> A következő lépésekkel, például egy kapcsolat hozzáadása a helyi storage-fiók vagy helyi tárfiókban lévő tárolók létrehozása közben hibák léphetnek fel. Hagyja figyelmen kívül, és frissítse. 

1. Töltse le és telepítse az Azure Storage Explorerrel
2. Kapcsolódás az Azure Storage kapcsolati karakterlánc használatával
3. Adja meg a kapcsolati karakterlánc: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. Próbálja ki való csatlakozás lépéseit.
5. Tároló belül a helyi storage-fiók létrehozása
6. Blokkblobok formájában fájlok feltöltésének megkezdése.
   > [!NOTE]
   > Törölje a jelet a jelölőnégyzetből, és töltse fel. Ez a modul nem támogatja a lapblobokat. Megjelenik ez azonnali fájlok, például .ISO-fájlhoz, a .vhd, a .vhdx vagy a nagy fájlok feltöltése közben.

7. Ha szeretné, csatlakozzon az Azure storage-fiókok, ahol tölt fel az adatokat. Biztosít egy egyetlen nézetben a helyi tárfiók mind az Azure storage-fiók

## <a name="supported-storage-operations"></a>Támogatott tárolási műveletek

BLOB storage-modulok IoT Edge ugyanazt az Azure Storage SDK-k használata, és konzisztens block blob végpontok az Azure Storage API 2017-04-17-es verziójával. Újabb kiadásaiban ügyfelek igényeitől függnek.

Nem minden Azure Blob Storage-műveletek az Azure Blob Storage, az IoT Edge-ben támogatottak. A következő szakaszban támogatott és nem támogatott műveletek listázása.

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
* GET, és állítsa be a tároló ACL
* Set-tároló metaadatai

Nem támogatott:
* Címbérlet-tárolók

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
* PUT letiltása
* PUT és a blokk-lista lekérése

Nem támogatott:
* Blokk PUT URL-címről

## <a name="feedback"></a>Visszajelzés:
Visszajelzése nagyon fontos számunkra, hogy ez a modul és a szolgáltatások hasznos és könnyen használható. Ossza meg velünk tapasztalatait, és tudassa velünk, hogyan tehetjük gördülékenyebbé.

Akkor is fel velünk a kapcsolatot: absiotfeedback@microsoft.com 

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

