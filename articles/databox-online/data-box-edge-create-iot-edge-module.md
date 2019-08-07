---
title: C#Azure Data Box Edge IoT Edge modulja | Microsoft Docs
description: Megtudhatja, hogyan C# fejlesztheti a Data Box Edge üzembe helyezhető IoT Edge modult.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 734ad263356ab9f91c7cb92ab174a14e0c5dd867
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775181"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>C# IoT Edge modul fejlesztése a fájlok áthelyezéséhez Data Box Edge

Ez a cikk bemutatja, hogyan hozhat létre IoT Edge modult a Data Box Edge eszközzel történő üzembe helyezéshez. Az Azure Data Box Edge egy olyan tárolási megoldás, amelynek használatával adatokat dolgozhat fel, majd egy hálózaton keresztül elküldheti azokat az Azure-ba.

Az Azure-ba való áttelepítése során Azure IoT Edge modulokat is használhat a Data Box Edge. A cikkben használt modul azt a logikát valósítja meg, hogy egy helyi megosztásból származó fájlt egy Felhőbeli megosztásra másoljon a Data Box Edge eszközön.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hozzon létre egy tároló-beállításjegyzéket a modulok tárolásához és kezeléséhez (Docker-lemezképek).
> * Hozzon létre egy IoT Edge modult a Data Box Edge eszközön való üzembe helyezéshez.


## <a name="about-the-iot-edge-module"></a>Tudnivalók a IoT Edge modulról

A Data Box Edge eszközön IoT Edge modulokat telepíthet és futtathat. Az Edge-modulok lényegében olyan Docker-tárolók, amelyek egy adott feladatot hajtanak végre, például üzeneteket töltenek le egy eszközről, átalakítanak egy üzenetet, vagy üzeneteket küldenek egy IoT Hubnak. Ebben a cikkben egy olyan modult fog létrehozni, amely egy helyi megosztásból másol fájlokat egy felhőalapú megosztásra a Data Box Edge eszközön.

1. A fájlok a Data Box Edge eszköz helyi megosztására íródnak.
2. A fájl esemény-előállítója létrehoz egy fájl eseményt a helyi megosztásba írt minden fájlhoz. A fájlra vonatkozó eseményeket a rendszer a fájl módosításakor is létrehozza. A rendszer ezután elküldi a fájl eseményeit IoT Edge hubhoz (IoT Edge futtatókörnyezetben).
3. A IoT Edge egyéni modul feldolgozza a fájl eseményt, hogy létrehozzon egy fájl-esemény objektumot, amely a fájl relatív elérési útját is tartalmazza. A modul egy abszolút elérési utat generál a fájl relatív elérési útjával, és átmásolja a fájlt a helyi megosztásról a Felhőbeli megosztásra. A modul ezután törli a fájlt a helyi megosztásról.

![Azure IoT Edge modul működése Data Box Edge](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Ha a fájl a Felhőbeli megosztásban van, a rendszer automatikusan feltölti az Azure Storage-fiókjába.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy rendszert futtató Data Box Edge eszköz.

    - Az eszközhöz hozzá van rendelve IoT Hub erőforrás is.
    - Az eszközön van konfigurált peremhálózati számítási szerepkör.
    További információért látogasson el a Data Box Edge [számításának beállítása](data-box-edge-deploy-configure-compute.md#configure-compute) című témakörre.

- A következő fejlesztői erőforrások:

    - [Visual Studio Code](https://code.visualstudio.com/).
    - [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).
    - [Azure IoT Edge-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Előfordulhat, hogy létre kell hoznia egy fiókot a szoftver letöltéséhez és telepítéséhez.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Az Azure-beli tároló-beállításjegyzék egy privát Docker-tárolójegyzék az Azure-ban, amelyben tárolhatja és kezelheti privát Docker-tárolóinak rendszerképeit. A felhőben elérhető két népszerű Docker beállításjegyzék-szolgáltatás Azure Container Registry és a Docker hub. Ez a cikk a Container Registry használja.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza **az erőforrás létrehozása > tárolók > Container Registry**lehetőséget. Kattintson a **Create** (Létrehozás) gombra.
3. Nyújt

   1. Egy egyedi, az Azure-ban található, 5 – 50 alfanumerikus karaktert tartalmazó **beállításjegyzékbeli név** .
   2. Válasszon egyelőfizetést.
   3. Hozzon létre egy új csoportot, vagy válasszon ki egy meglévő **erőforráscsoportot**.
   4. Válasszon ki egy **helyet**. Azt javasoljuk, hogy ez a hely azonos legyen a Data Box Edge erőforráshoz társított hellyel.
   5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
   6. Az SKU beállítása alapszintű értékre.

      ![Tárolóregisztrációs adatbázis létrehozása](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Kattintson a **Létrehozás** gombra.
5. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet.

    ![Hozzáférési kulcsok beolvasása](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Ezekkel az értékekkel később közzéteheti a Docker-rendszerképet a beállításjegyzékben, és hozzáadhatja a beállításjegyzékbeli hitelesítő adatokat a Azure IoT Edge Runtime szolgáltatáshoz.


## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása

A következő lépésekkel hozzon létre egy IoT Edge modul-projektet a .NET Core 2,1 SDK alapján. A projekt a Visual Studio Code és a Azure IoT Edge bővítményt használja.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Létrehozhat egy C#-megoldást, amelyet a saját kódjával testreszabhat.

1. A Visual Studio Code-ban válassza a **megtekintés > parancs paletta** elemet a vs Code parancs paletta megnyitásához.
2. A parancs palettáján írja be és futtassa az Azure **parancsot: Jelentkezzen** be, és kövesse az Azure-fiókba való bejelentkezéshez szükséges utasításokat. Ha már be van jelentkezve, ezt a lépést kihagyhatja.
3. A Command paletta írja be és futtassa a parancsot **Azure IoT Edge: Új IoT Edge megoldás**. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához:

    1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni.
    2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
    
        ![1\. új megoldás létrehozása](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. A modul sablonjaként válassza a **C# modult**.
    4. Cserélje le az alapértelmezett modul nevét a hozzárendelni kívánt névre, ebben az esetben ez a **FileCopyModule**.
    
        ![Új megoldás létrehozása 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Adja meg az előző szakaszban létrehozott tároló-beállításjegyzéket az első modul rendszerkép-tárháza. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére.

        A végső sztring így néz `<Login server name>/<Module name>`ki. Ebben a példában a karakterlánc a következő: `mycontreg2.azurecr.io/filecopymodule`.

        ![3\. új megoldás létrehozása](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Nyissa **meg a fájl > Megnyitás mappát**.

    ![4\. új megoldás létrehozása](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Tallózással keresse meg a korábban létrehozott **EdgeSolution** mappát, és mutasson rá. A VS Code ablak az öt legfelső szintű összetevővel tölti be IoT Edge megoldás munkaterületét. Ebben a cikkben nem módosítja a **. vscode** mappát, a **. gitignore** fájlt, az **. env** fájlt és a **Deployment. template. JSON** fájlt.
    
    Az egyetlen módosítható összetevő a modulok mappa. Ez a mappa rendelkezik C# a modul és a Docker-fájlok kódjával, hogy a modult tároló képként hozza létre.

    ![Új megoldás létrehozása 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa meg a **modules > FileCopyModule > program.cs**.
2. A **FileCopyModule névtér**tetején adja hozzá a következő using utasításokat a később használt típusokhoz. A **Microsoft. Azure. Devices. Client. Transport. Mqtt** egy protokoll, amely üzeneteket küld IoT Edge hubhoz.

    ```
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;
    using Newtonsoft.Json;
    ```
3. Adja hozzá a **InputFolderPath** és a **OutputFolderPath** változót a program osztályhoz.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Adja hozzá a **FileEvent** osztályt az üzenet törzsének definiálásához.

    ```
    /// <summary>
    /// The FileEvent class defines the body of incoming messages. 
    /// </summary>
    private class FileEvent
    {
        public string ChangeType { get; set; }

        public string ShareRelativeFilePath { get; set; }

        public string ShareName { get; set; }
    }
    ```

5. Az **Init** metódus használata esetén a kód létrehoz és konfigurál egy **ModuleClient** objektumot. Ez az objektum lehetővé teszi, hogy a modul az üzenetek küldéséhez és fogadásához a helyi Azure IoT Edge futtatókörnyezethez kapcsolódjon MQTT protokoll használatával. Az init metódusban használt kapcsolódási karakterláncot a IoT Edge futtatókörnyezet biztosítja a modulnak. A kód regisztrálja a FileCopy visszahívást, hogy üzeneteket fogadjon egy IoT Edge hubhoz az **input1** -végponton keresztül.

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

6. Szúrja be a **FileCopy**kódját.

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
    ```

7. Mentse el ezt a fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge megoldást, és hozzáadta a kódot a FileCopyModule, hogy fájlokat másoljon a helyi megosztásból a Felhőbeli megosztásba. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A VSCode-ben nyissa meg a Terminal > New Terminal új Visual Studio Code integrált terminál megnyitásához.
2. Jelentkezzen be a Docker-be a következő parancs beírásával az integrált terminálon.

    `docker login <ACR login server> -u <ACR username>`

    Használja a tároló-beállításjegyzékből másolt bejelentkezési kiszolgálót és felhasználónevet. 

    ![IoT Edge-megoldás létrehozása és leküldése](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Ha a rendszer jelszót kér, adja meg a jelszót. A bejelentkezési kiszolgáló, a Felhasználónév és a jelszó értékeit a Azure Portal tároló beállításjegyzékének **hozzáférési kulcsaiból** is lekérheti.
 
3. A hitelesítő adatok megadása után leküldheti a modul rendszerképét az Azure Container registrybe. A VS Code Explorerben kattintson a jobb gombbal a **Module. JSON** fájlra, és válassza a **IoT Edge megoldás kiépítése és leküldése**lehetőséget.

    ![IoT Edge-megoldás létrehozása és leküldése](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Ha közli a Visual Studio Code-ot a megoldás létrehozásával, az integrált terminálon két parancsot futtat: a Docker Build és a Docker push. A két parancs létrehozza a kódot, tárolóba helyezi a CSharpModule.dll fájlt, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba.

    A rendszer kérni fogja a modul platformjának kiválasztását. Válassza ki a Linux rendszernek megfelelő *amd64* elemet.

    ![Válasszon platformot](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Csak a Linux-modulok támogatottak.

    A következő figyelmeztetést láthatja, amelyet figyelmen kívül hagyhat:

    *Program. cs (77, 44): figyelmeztetés CS1998: Ez az aszinkron metódus nem rendelkezik "várakozási" operátorokkal, és szinkronban fog futni. Érdemes lehet a "várakozás" operátort használni, hogy a nem blokkoló API-hívásokat, vagy a "várakozási feladat. Run (...)" metódust használja a CPU-kötésű működéshez a háttérben futó szálon.*

4. A VS Code integrált termináljában láthatja a teljes tárolórendszerképet címkével együtt. A képcímet a Module. JSON fájlban található, formátummal `<repository>:<version>-<platform>`ellátott információ alapján építették. Ehhez a cikkhez hasonlóan `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`kell kinéznie.

## <a name="next-steps"></a>További lépések

A modul Data Box Edgeon való üzembe helyezéséhez és futtatásához tekintse meg a [modul hozzáadása](data-box-edge-deploy-configure-compute.md#add-a-module)című témakör lépéseit.
