---
title: C# IoT Edge-modul az Azure Data Box Edge |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy C# IoT Edge-modul, amely a Data Box Edge telepíthető.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/16/2018
ms.author: alkohli
ms.openlocfilehash: 8d4a99ab9d8107f1b3fbe70f59299f427bc88bd5
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465889"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge-preview"></a>A Data Box Edge (előzetes verzió) fájlok áthelyezése egy C# IoT Edge-modul fejlesztése

Ez a cikk végigvezeti egy IoT Edge-modul, központi telepítés létrehozása a Data Box Edge-eszköz használatával. Az Azure Data Box Edge egy olyan tárolási megoldás, amelynek használatával adatokat dolgozhat fel, majd egy hálózaton keresztül elküldheti azokat az Azure-ba.

Azure IoT Edge-modulok a Data Box Edge segítségével az Azure-bA áthelyezni, mert az adatok átalakításához. Az ebben a cikkben használt modul valósít meg egy fájlt egy helyi meghajtóról a Data Box Edge-eszközön felhő-megosztásra másolásához logikát.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hozzon létre egy tároló-beállításjegyzéket, tárolhatja és kezelheti a modulok (Docker-lemezképeket).
> * Hozzon létre egy IoT Edge-modul üzembe helyezése a Data Box Edge-eszközön.

> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="about-the-iot-edge-module"></a>Tudnivalók az IoT Edge-modul

A Data Box peremhálózati eszköz telepíthet és futtathat IoT Edge modulok. Edge-modulok lényegében Docker-tárolókat, amelyek egy adott feladat végrehajtására, például képes feldolgozni egy üzenetet az eszközről, egy üzenet átalakítása vagy egy üzenetet küld az IoT Hub olyan. Ebben a cikkben létrehoz egy modult, amely fájlokat másol egy helyi megosztással egy felhőbeli fájlmegosztás a Data Box Edge-eszközön.

1. Fájlok a Data Box Edge-eszközön a helyi megosztásra írt.
2. A fájl esemény generátor minden egyes fájl a helyi megosztásra írt fájl eseményt hoz létre. A fájl események majd kapnak IoT Edge hubot (az IoT Edge-futtatókörnyezet).

   > [!IMPORTANT]
   > A fájl események csak az újonnan létrehozott fájlok jönnek létre. A meglévő fájlok módosítása nem generál fájl eseményeket.

3. Az egyéni IoT Edge-modul feldolgozza a fájl eseményt, a fájl relatív elérési utat is tartalmazó fájl esemény objektum létrehozásához. A modul abszolút elérési utat a fájl relatív elérési úton állít elő, és másolja át a fájlt a helyi megosztás a megosztáshoz a felhőbe. A modul ezután törli a fájlt a helyi megosztásból.

![Az Azure IoT Edge-modul a Data Box Edge működése](./media/data-box-edge-create-iot-edge-module/how-module-works.png)

Ha a fájlt a cloud-megosztásban, azt automatikusan lekérdezi feltöltve az Azure Storage-fiók.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik:

- Data Box peremhálózati eszköz, hogy fut-e.

    - Egy IoT Hub társított erőforrást is van az eszközön. További információért ugorjon [hozzon létre egy IoT Hub erőforrást](data-box-edge-deploy-configure-compute.md#create-an-iot-hub-resource) a Data Box Edge.
    - Edge-compute-szerepkör konfigurálva van az eszközön. További információért ugorjon [állítsa be a számítási szerepkör](data-box-edge-deploy-configure-compute.md#set-up-compute-role) a Data Box Edge.

- Az alábbi fejlesztői erőforrások:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Hozzon létre egy fiókot a töltse le és telepítse a szoftver lehet.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Az Azure-beli tároló-beállításjegyzék egy privát Docker-tárolójegyzék az Azure-ban, amelyben tárolhatja és kezelheti privát Docker-tárolóinak rendszerképeit. Két népszerű Docker registry az elérhető szolgáltatások a felhőben a következők: Azure Container Registry és a Docker Hubból. Ez a cikk a Tárolóregisztrációs adatbázist használ.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza ki **erőforrás létrehozása > tároló > tároló-beállításjegyzék**. Kattintson a **Create** (Létrehozás) gombra.
3. Adja meg:

    1. Egy egyedi **beállításjegyzék neve** 5 – 50 alfanumerikus karaktert tartalmazó Azure-ban.
    2. Válasszon egy **előfizetés**.
    3. Új létrehozása vagy meglévő tárfiók kiválasztásához **erőforráscsoport**.
    4. Válasszon ki egy **helyet**. Azt javasoljuk, hogy ezen a helyen kell egyeznie a Data Box Edge erőforrás társított.
    5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
    6. Állítsa a Termékváltozat **alapszintű**.

    ![Tárolóregisztrációs adatbázis létrehozása](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Kattintson a **Létrehozás** gombra.
5. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet.

    ![Hozzáférési kulcsok lekérése](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Használja ezeket az értékeket később a tárolójegyzék hitelesítő adatainak hozzáadása az Azure IoT Edge-futtatókörnyezet és a Docker-rendszerkép közzététele a beállításjegyzékbe.


## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása

Az alábbi lépéseket hozzon létre egy IoT Edge modul a projektet, a .NET Core SDK 2.1-es verziója alapján. A projekt a Visual Studio Code és az Azure IoT Edge-bővítmény használ.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Létrehozhat egy C#-megoldást, amelyet a saját kódjával testreszabhat.

1. Válassza ki a Visual Studio Code- **Nézet > Parancskatalógus** a VS Code parancskatalógus megnyitásához.
2. A parancskatalógusban írja be és futtassa az **Azure: Sign in** parancsot, és az utasításokat követve jelentkezzen be az Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.
3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához:

    1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni.
    2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
    
        ![1. új megoldás létrehozása](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. A modul sablonjaként válassza a **C# modult**.
    4. Az alapértelmezett modulnév cserélje le a hozzárendelni kívánt név, ebben az esetben **FileCopyModule**.
    
        ![2. új megoldás létrehozása](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Adja meg a tároló-beállításjegyzékbe, amelyet az előző szakaszban a lemezképtárból, az első modulhoz. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére.

        Néz ki a végső karakterláncban `<Login server name>/<Module name>`. Ebben a példában a karakterlánc van: `mycontreg2.azurecr.io/filecopymodule`.

        ![3. új megoldás létrehozása](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Lépjen a **fájl > mappa megnyitása**.

    ![4. új megoldás létrehozása](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Keresse meg, és mutasson a **EdgeSolution** korábban létrehozott mappába. A VS Code-ablak betölti az IoT Edge megoldás munkaterület öt legfelső szintű összetevőit. Nem fogja módosítani a **.vscode** mappában **.gitignore** fájl **.env** fájlt, és a **deployment.template.json** ebben a cikkben.
    
    Az egyetlen olyan összetevő, amely módosítja az a modulokat mappa. Ebben a mappában megtalálja a C#-kódot a modul és a Docker-fájlok a modul, mint a tárolórendszerkép létrehozásához.

    ![5. új megoldás létrehozása](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa meg a **modulok > CSharpModule > Program.cs**.
2. Felső részén a **FileCopyModule névtér**, adja hozzá a következő using utasításokat a későbbi esetében. **Microsoft.Azure.Devices.Client.Transport.Mqtt** küldhet üzeneteket az IoT Edge hubot protokoll.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Adja hozzá a **InputFolderPath** és **OutputFolderPath** változó a Program osztályhoz.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/LocalShare";
            private const string OutputFolderPath = "/home/CloudShare";
    ````

    > [!IMPORTANT]
    > Jegyezze fel a `InputFolderPath` és a `OutputFolderPath`. Adja meg az elérési utak, ez a modul telepítésekor kell.

4. Adja hozzá a **MessageBody tulajdonság** osztály a Program osztályhoz. Ezek az osztályok határozzák meg a bejövő üzenetek törzsének várt sémáját.

    ```
    /// <summary>
    /// The MessageBody class defines the expected schema for the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. Az **Init** metódus használata esetén a kód létrehoz és konfigurál egy **ModuleClient** objektumot. Ez az objektum lehetővé teszi, hogy a modul csatlakozni a helyi Azure IoT Edge-futtatókörnyezet MQTT protokoll használatával üzeneteket küldjön és fogadjon. A kapcsolati karakterláncot, amely szerepel az inicializálási metódusnak által az IoT Edge-futtatókörnyezet a modulnak van megadva. A kód regisztrálja a FileCopy visszahívás útján kell fogad üzeneteket egy IoT Edge hubot keresztül a **input1** végpont.

    ```
    /// <summary>
    /// Initializes the ModuleClient and sets up the callback to receive
    /// messages containing file event information
    /// </summary>
    static async Task Init()
    {
        MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
        ITransportSettings[] settings = { mqttSetting };

        // Open a connection to the IoT Edge runtime
        ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
        await ioTHubModuleClient.OpenAsync();
        Console.WriteLine("IoT Hub module client initialized.");

        // Register callback to be called when a message is received by the module
        await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FileCopy, ioTHubModuleClient);
    }
    ```

6. Szúrja be a kódot a **FileCopy**.

    ```
            /// <summary>
        /// This method is called whenever the module is sent a message from the IoT Edge Hub. 
        /// This method deserializes the file event, extracts the corresponding relative file path, and creates the absolute input file path using the relative file path and the InputFolderPath.
        /// This method also forms the absolute output file path using the relative file path and the OutputFolderPath. It then copies the input file to output file and deletes the input file after the copy is complete.
        /// </summary>
        static async Task<MessageResponse> FileCopy(Message message, object userContext)
        {
            int counterValue = Interlocked.Increment(ref counter);

            try
            {
                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                if (!string.IsNullOrEmpty(messageString))
                {
                    var fileEvent = JsonConvert.DeserializeObject<FileEvent>(messageString);

                    string relativeFileName = fileEvent.ShareRelativeFilePath.Replace("\\", "/");
                    string inputFilePath = InputFolderPath + relativeFileName;
                    string outputFilePath = OutputFolderPath + relativeFileName;

                    if (File.Exists(inputFilePath))                
                    {
                        Console.WriteLine($"Moving input file: {inputFilePath} to output file: {outputFilePath}");
                        var outputDir = Path.GetDirectoryName(outputFilePath);
                        if (!Directory.Exists(outputDir))
                        {
                            Directory.CreateDirectory(outputDir);
                        }

                        File.Copy(inputFilePath, outputFilePath, true);
                        Console.WriteLine($"Copied input file: {inputFilePath} to output file: {outputFilePath}");
                        File.Delete(inputFilePath);
                        Console.WriteLine($"Deleted input file: {inputFilePath}");
                    } 
                    else
                    {
                        Console.WriteLine($"Skipping this event as input file doesn't exist: {inputFilePath}");   
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Caught exception: {0}", ex.Message);
                Console.WriteLine(ex.StackTrace);
            }

            Console.WriteLine($"Processed event.");
            return MessageResponse.Completed;
        }

    }
    ```

7. Mentse el ezt a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldás, és a fájlok másolása a felhő megosztás helyi megosztásból FileCopyModule hozzá kódot. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A Visual Studio Code integrált termináljában az alábbi paranccsal jelentkezzen be a Dockerbe.

    `docker login <ACR login server> -u <ACR username>`

    Használja a bejelentkezési kiszolgáló és a felhasználónév, amelyet a tárolóregisztrációs adatbázis fájlból kimásolt. 

    ![Hozhat létre, és küldje le az IoT Edge-megoldás](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Amikor jelszó megadását kéri, adja meg a jelszót. A bejelentkezési kiszolgáló, a felhasználónevet és jelszót a tartozó értékeket is lekérhet a **Tárelérési kulcsok** a az Azure Portalon a tárolóregisztrációs adatbázis.
 
3. Miután a hitelesítő adatok a modul rendszerképének küldhet az Azure container registrybe. A VS Code Explorerben kattintson a jobb gombbal a **module.json** fájlt, és válassza ki **hozhat létre, és küldje le az IoT Edge megoldás**.

    ![Hozhat létre, és küldje le az IoT Edge-megoldás](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Megadhatja a Visual Studio Code hozhat létre egy megoldást, amikor két parancs futása az integrált terminálon: docker build és a docker leküldéses. A két parancs létrehozza a kódot, tárolóba helyezi a CSharpModule.dll fájlt, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba.

    A következő figyelmeztetés, amely figyelmen kívül hagyhatja jelenhetnek meg:

    *Program.cs(77,44): CS1998 figyelmeztetés: az async módszert hiányzik "await" operátorok és szinkron módon fog futni. Fontolja meg az "await" operátor nem blokkoló API-hívások await, vagy 'await Task.Run(...)' ezt a háttérbeli szálon processzorigénye munkát.*

4. A VS Code integrált termináljában láthatja a teljes tárolórendszerképet címkével együtt. A rendszerkép címét, amely formátumban a module.json fájlban található információk alapján készült `<repository>:<version>-<platform>`. Ebben a cikkben hasonlóan kell kinéznie `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`.

## <a name="next-steps"></a>További lépések

Üzembe helyezését, és ez a modul (Data Box Edge) futtatásához, olvassa el a [adjon hozzá egy egyéni modult](data-box-edge-deploy-configure-compute.md#add-a-custom-module).
