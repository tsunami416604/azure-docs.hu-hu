---
title: C# IoT Edge modul az Azure Data Box Edge-hez | Microsoft dokumentumok
description: Ismerje meg, hogyan fejleszthet egy C# IoT Edge-modult, amely telepíthető a Data Box Edge-en.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/06/2019
ms.author: alkohli
ms.openlocfilehash: 3aa1190fb713c2fbdedcb1ce84a65d4263693827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942549"
---
# <a name="develop-a-c-iot-edge-module-to-move-files-on-data-box-edge"></a>C# IoT Edge modul fejlesztése a Data Box Edge fájljainak áthelyezéséhez

Ez a cikk bemutatja, hogyan hozhat létre egy IoT Edge-modult a Data Box Edge-eszközzel való üzembe helyezéshez. Az Azure Data Box Edge egy olyan tárolási megoldás, amelynek használatával adatokat dolgozhat fel, majd egy hálózaton keresztül elküldheti azokat az Azure-ba.

Az Azure IoT Edge-modulokat a Data Box Edge-tel együtt az Azure-ba való áthelyezéssorán átalakíthatja. A jelen cikkben használt modul megvalósítja a logikát, hogy egy fájlt egy helyi megosztásról egy felhőmegosztásra másolja a Data Box Edge-eszközön.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Hozzon létre egy tároló beállításjegyzék et a modulok (Docker-rendszerképek) tárolására és kezelésére.
> * Hozzon létre egy IoT Edge-modult a Data Box Edge-eszközön való üzembe helyezéshez.


## <a name="about-the-iot-edge-module"></a>Az IoT Edge modul

A Data Box Edge-eszköz telepítheti és futtathatja az IoT Edge-modulokat. Peremhálózati modulok lényegében Docker-tárolók, amelyek egy adott feladatot hajtanak végre, például egy üzenetet egy eszközről, átalakítegy üzenetet, vagy üzenetet küldeni egy IoT Hub. Ebben a cikkben létrehoz egy modult, amely fájlokat másol egy helyi megosztásról egy felhőmegosztásra a Data Box Edge-eszközön.

1. A fájlok a Data Box Edge eszköz helyi megosztására kerülnek.
2. A fájlesemény-generátor létrehoz egy fájleseményt a helyi megosztásra írt minden fájlhoz. A fájlesemények akkor is létrejönnek, amikor egy fájlt módosítanak. A fájlesemények ezután elküldésre kerülnek az IoT Edge Hubba (az IoT Edge futásidejű).
3. Az IoT Edge egyéni modul feldolgozza a fájleseményt egy fájlesemény-objektum létrehozásához, amely a fájl relatív elérési útját is tartalmazza. A modul abszolút elérési utat hoz létre a relatív fájlelérési út használatával, és másolja a fájlt a helyi megosztásról a felhőmegosztásra. A modul ezután törli a fájlt a helyi megosztásról.

![Hogyan működik az Azure IoT Edge modul a Data Box Edge-en?](./media/data-box-edge-create-iot-edge-module/how-module-works-1.png)

Miután a fájl a felhőmegosztásban van, automatikusan feltölti az Azure Storage-fiókjába.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- A Data Box Edge eszköz, amely fut.

    - Az eszköz is rendelkezik egy társított IoT Hub-erőforrás.
    - Az eszköz Edge számítási szerepkör konfigurálva van.
    További információ: [A számítási beállítás](data-box-edge-deploy-configure-compute.md#configure-compute) a Data Box Edge-hez című területen.

- A következő fejlesztési források:

    - [Visual Studio kód](https://code.visualstudio.com/).
    - [C# bővítmény a Visual Studio Code-hoz (szolgáltató: OmniSharp) ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
    - [Azure IoT Edge-bővítmény a Visual Studio-kódhoz.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
    - [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Előfordulhat, hogy a szoftver letöltéséhez és telepítéséhez fiókot kell létrehoznia.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Az Azure-beli tároló-beállításjegyzék egy privát Docker-tárolójegyzék az Azure-ban, amelyben tárolhatja és kezelheti privát Docker-tárolóinak rendszerképeit. A felhőben elérhető két népszerű Docker-beállításjegyzék-szolgáltatás az Azure Container Registry és a Docker Hub. Ez a cikk a tárolóbeállítás-nyilvántartást használja.

1. Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .
2. Válassza **az Erőforrás > tárolók > tárolók beállításjegyzékének létrehozása lehetőséget.** Kattintson **a Létrehozás gombra.**
3. Adja meg:

   1. Az Azure-on belüli egyedi **rendszerleíró adatbázis név,** amely 5–50 alfanumerikus karaktert tartalmaz.
   2. **Válasszon egy előfizetést**.
   3. Hozzon létre új vagy válasszon egy meglévő **erőforráscsoportot**.
   4. Válasszon egy **helyet**. Azt javasoljuk, hogy ez a hely megegyezik a Data Box Edge erőforrástársított.
   5. A **Rendszergazdai felhasználó** beállítást váltsa **Engedélyezés** értékre.
   6. Állítsa a **termékváltozatot Alapszintűre.**

      ![Tárolóregisztrációs adatbázis létrehozása](./media/data-box-edge-create-iot-edge-module/create-container-registry-1.png)
 
4. Kattintson a **Létrehozás** gombra.
5. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, és válassza a **Hozzáférési kulcsok** elemet.

    ![Hozzáférési kulcsok beszerezése](./media/data-box-edge-create-iot-edge-module/get-access-keys-1.png)
 
6. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. Ezeket az értékeket később a Docker-rendszerkép közzétételére a beállításjegyzékben, és adja hozzá a rendszerleíró hitelesítő adatokat az Azure IoT Edge futásidejű.


## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása

A következő lépések a .NET Core 2.1 SDK alapján ioT Edge modulprojektet hoznak létre. A projekt a Visual Studio-kódot és az Azure IoT Edge bővítményt használja.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Létrehozhat egy C#-megoldást, amelyet a saját kódjával testreszabhat.

1. A Visual Studio-kódban válassza **a > parancspaletta megtekintése** lehetőséget a VS Code parancspaletta megnyitásához.
2. A parancskatalógusban írja be és futtassa az **Azure: Sign in** parancsot, és az utasításokat követve jelentkezzen be az Azure-fiókjába. Ha már be van jelentkezve, ezt a lépést kihagyhatja.
3. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. A parancskatalógusban adja meg az alábbi információkat a megoldás létrehozásához:

    1. Válassza ki azt a mappát, ahol a megoldást létre szeretné hozni.
    2. Adja meg a megoldás nevét, vagy fogadja el az alapértelmezett **EdgeSolution** nevet.
    
        ![Új megoldás létrehozása 1](./media/data-box-edge-create-iot-edge-module/create-new-solution-1.png)

    3. Válassza a **C# modul** modulsablont.
    4. Cserélje le az alapértelmezett modulnevet a hozzárendelni kívánt névre, ebben az esetben a **FileCopyModule**.
    
        ![Új megoldás létrehozása 2](./media/data-box-edge-create-iot-edge-module/create-new-solution-2.png)

    5. Adja meg az előző szakaszban létrehozott tárolóbeállításjegyzéket az első modul képtárházaként. Cserélje le a **localhost:5000** értéket a bejelentkezési kiszolgáló kimásolt értékére.

        Az utolsó karakterlánc `<Login server name>/<Module name>`így néz ki. Ebben a példában a `mycontreg2.azurecr.io/filecopymodule`karakterlánc a következő: .

        ![Új megoldás létrehozása 3](./media/data-box-edge-create-iot-edge-module/create-new-solution-3.png)

4. Nyissa meg **a Fájl > Mappa megnyitása .**

    ![Új megoldás létrehozása 4](./media/data-box-edge-create-iot-edge-module/create-new-solution-4.png)

5. Tallózással és mutasson a korábban létrehozott **EdgeSolution** mappára. A VS Code ablak betölti az IoT Edge-megoldás munkaterületét az öt legfelső szintű összetevővel. A cikkben szereplő **.vscode,** **.gitignore** fájl, **.env** fájl és **deployment.template.json** nem szerkeszti.
    
    Az egyetlen módosított összetevő a modulok mappája. Ez a mappa rendelkezik a modul C# kódjával és a Docker-fájlokkal a modul tárolórendszerképként való létrehozásához.

    ![Új megoldás létrehozása 5](./media/data-box-edge-create-iot-edge-module/create-new-solution-5.png)

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code explorer ben nyissa meg **a FileCopyModule > > modulokat Program.cs**.
2. A **FileCopyModule névtér**tetején adja hozzá a következőket a később használt típusok utasításainak használatával. **A Microsoft.Azure.Devices.Client.Transport.Mqtt** egy protokoll az IoT Edge Hubnak történő üzenetek küldésére.

    ```
    namespace FileCopyModule
    {
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using Newtonsoft.Json;
    ```
3. Adja hozzá az **InputFolderPath** és **a OutputFolderPath** változót a Program osztályhoz.

    ```
    class Program
        {
            static int counter;
            private const string InputFolderPath = "/home/input";
            private const string OutputFolderPath = "/home/output";
    ```

4. Közvetlenül az előző lépés után adja hozzá a **FileEvent** osztályt az üzenet törzsének meghatározásához.

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

5. Az **Init metódusban**a kód létrehoz és konfigurál egy **ModuleClient** objektumot. Ez az objektum lehetővé teszi, hogy a modul csatlakozzon a helyi Azure IoT Edge futásidő mqtt protokoll használatával üzenetek küldésére és fogadására. Az Init metódusban használt kapcsolati sztringet az IoT Edge-futtatókörnyezet biztosítja a modulnak. A kód regisztrálja a FileCopy visszahívási üzenetek fogadására egy IoT Edge hub a **bemeneti1** végponton keresztül. Cserélje le az **Init metódust** a következő kódra.

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

6. Távolítsa el a **PipeMessage metódus** kódját, és a helyére szúrja be a **FileCopy**kódot.

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
8. A [projekthez meglévő kódmintát is letölthet.](https://azure.microsoft.com/resources/samples/data-box-edge-csharp-modules/?cdn=disable) Ezután ellenőrizheti a mintában szereplő **program.cs** fájllal mentett fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és kódot adott hozzá a FileCopyModule-hoz a helyi megosztásból a felhőmegosztásba fájlok másolásához. Most létre kell hoznia a megoldást tárolórendszerképként, és le kell küldenie a tárolóregisztrációs adatbázisba.

1. A VSCode-ban nyissa meg az új Visual Studio Code integrált terminált > új Visual Studio Code integrált terminálmegnyitásához.
2. Jelentkezzen be a Dockerbe a következő parancs beírásával az integrált terminálon.

    `docker login <ACR login server> -u <ACR username>`

    Használja a tároló beállításjegyzékéből másolt bejelentkezési kiszolgálót és felhasználónevet.

    ![IoT Edge megoldás létrehozása és leküldése](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-1.png)

2. Amikor a rendszer jelszót kér, adja meg a jelszót. A bejelentkezési kiszolgáló, a felhasználónév és a jelszó értékeit is lekérheti az Azure Portal tárolóbeállításában lévő **access keys** ből.
 
3. Miután a hitelesítő adatok at megadott, leküldéses a modul lemezképet az Azure-tároló beállításjegyzékbe. A VS Code Explorerben kattintson a jobb gombbal a **module.json** fájlra, és válassza **az IoT Edge-megoldás összeállítása és leküldése lehetőséget.**

    ![IoT Edge megoldás létrehozása és leküldése](./media/data-box-edge-create-iot-edge-module/build-iot-edge-solution-2.png)
 
    Amikor azt mondja a Visual Studio Code-nak a megoldás létrehozásához, két parancsot futtat az integrált terminálon: a docker build et és a docker push-ot. A két parancs létrehozza a kódot, tárolóba helyezi a CSharpModule.dll fájlt, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba.

    A rendszer kérni fogja a modulplatform kiválasztását. Válassza ki a Linuxnak megfelelő *amd64* lehetőséget.

    ![Platform kiválasztása](./media/data-box-edge-create-iot-edge-module/select-platform.png)

    > [!IMPORTANT] 
    > Csak a Linux-modulok támogatottak.

    A következő figyelmeztetés jelenhet meg, amelyet figyelmen kívül hagyhat:

    *Program.cs(77,44): figyelmeztetés CS1998: Ez az aszinkron módszer nem rendelkezik "await" operátorokkal, és szinkronmódon fog futni. Fontolja meg a "await" operátor használatával a nem blokkoló API-hívások megvárására, vagy a "await Task.Run(...)" (Feladathoz kötött) futtatási(...) - a háttérszálon végzett CPU-kötésű munka elvégzéséhez.*

4. A VS Code integrált termináljában láthatja a teljes tárolórendszerképet címkével együtt. A képcím a module.json fájlban található , a `<repository>:<version>-<platform>`formátumú információból épül fel. Ebben a cikkben így `mycontreg2.azurecr.io/filecopymodule:0.0.1-amd64`kell kinéznie.

## <a name="next-steps"></a>További lépések

A modul Data Box Edge-en történő üzembe helyezéséhez és futtatásához olvassa el a [Modul hozzáadása](data-box-edge-deploy-configure-compute.md#add-a-module)című témakör lépéseit.
