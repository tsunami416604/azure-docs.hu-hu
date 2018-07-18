---
title: Az Azure Functions Core Tools használata |} A Microsoft Docs
description: Megtudhatja, hogyan programozásához és teszteléséhez a parancssort vagy a terminálban a helyi számítógépen az Azure functions, az Azure Functions futtatása előtt.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 44485d04dad3ff9dfc6067a3737989c5d273541f
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116180"
---
# <a name="work-with-azure-functions-core-tools"></a>Az Azure Functions Core Tools használata

Az Azure Functions Core Tools lehetővé teszi a fejlesztés és tesztelés a helyi számítógépen a parancssorba vagy terminálba az a funkciók. A helyi függvények élő Azure-szolgáltatások is csatlakozhat, és akkor is a függvények hibakeresése a helyi számítógépen a teljes Functions futtatókörnyezete használatával. Akkor is telepítheti egy függvényalkalmazást az Azure-előfizetéshez.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Core Tools-verzió

Nincsenek az Azure Functions Core Tools két verziója. A verzió, a helyi fejlesztési környezetét, az nyelvválasztás, és a szükséges támogatási szint függ:

+ [Verzió 1.x](#v1): verziót támogatja, amely általánosan elérhető (GA) a futtatókörnyezet 1.x. Ez a verzió, az eszközök csak Windows-számítógépeken támogatott, és telepítve van a egy [npm-csomag](https://docs.npmjs.com/getting-started/what-is-npm). Az ebben a verzióban a kísérleti nem hivatalosan támogatott nyelvek funkciókat is létrehozhat. További információkért lásd: [támogatott nyelv az Azure Functions szolgáltatásban](supported-languages.md)

+ [Verzió 2.x](#v2): verziója támogatja a 2.x verziójú futtatókörnyezet. Ez a verzió támogatja a [Windows](#windows-npm), [macOS](#brew), és [Linux](#linux). Telepítési platformspecifikus csomagkezelők vagy az npm-et használ. A 2.x verziójú futtatókörnyezet, például ez a alapvető eszközök verziója jelenleg előzetes verzióban érhető el.

Ha másként nincs jelezve, az ebben a cikkben szereplő példák verziójára vonatkoznak 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Az Azure Functions Core Tools telepítése

[Az Azure Functions Core Tools] ugyanazzal a futtatókörnyezettel-megoldás, amely a helyi fejlesztői számítógépen való futtatása az Azure Functions runtime verzióját. Függvények létrehozása, csatlakoztatása az Azure-és függvény projektek telepítésére parancsokat is tartalmaz.

### <a name="v1"></a>Verzió 1.x

Az eszközök az eredeti verzió a Functions 1.x modul használja. Ez a verzió a .NET-keretrendszer (4.7.1) használ, és csak a Windows-számítógépeken támogatott. Előtt a verzió 1.x eszközök telepítése, be kell [NodeJS telepítése](https://docs.npmjs.com/getting-started/installing-node), amely tartalmazza az npm-et.

Használja a következő parancsot a verzió 1.x eszközök telepítéséhez:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Verzió 2.x

>[!NOTE]
> Az Azure Functions runtime 2.0 előzetes verzióban érhető el, és az Azure Functions jelenleg nem minden funkciója támogatott. További információkért lásd: [Azure Functions-verziók](functions-versions.md) 

Verzió 2.x-es eszközök használja az Azure Functions runtime 2.x verziója a .NET Core-alapú. Ez a verzió a platformfüggetlen .NET Core 2.x támogatja, beleértve a támogatott [Windows](#windows-npm), [macOS](#brew), és [Linux](#linux).

#### <a name="windows-npm"></a>Windows

Az alábbi lépéseket a Core Tools telepítése Windows npm-et használja. Is [helyezés a Chocolatey](https://chocolatey.org/). További információkért lásd: a [Core Tools információs](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Telepítés [2.1-es verziója a .NET Core for Windows](https://www.microsoft.com/net/download/windows).

2. Telepítés [Node.js], amely tartalmazza az npm-et. Verzió 2.x verzióját az eszközök csak Node.js 8.5 és újabb verziói támogatottak.

3. A Core Tools csomag telepítéséhez:

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>A homebrew-val MacOS

Az alábbi lépéseket a Homebrew használatával a Core Tools telepítése macOS rendszeren.

1. Telepítés [macOS-hez készült .NET Core 2.1](https://www.microsoft.com/net/download/macos).

2. Telepítés [Homebrew](https://brew.sh/), ha az még nem telepítette.

3. A Core Tools csomag telepítéséhez:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) az APT

Az alábbi lépések az [APT](https://wiki.debian.org/Apt) Core Tools telepítése az Ubuntu vagy a Debian Linux-disztribúció. Más Linux-disztribúciók, lásd: a [Core Tools információs](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Telepítés [.NET Core 2.1-es Linux](https://www.microsoft.com/net/download/linux).

2. Megbízható Microsoft-termékkulcs regisztrálásához:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Ellenőrizze az Ubuntu server fut-e a megfelelő verziók egyikével az alábbi táblázatot. Az apt-forrás hozzáadása, futtassa:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Linux-disztribúció | Verzió |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04-/ Linux Mentaízű 18-ra    | `xenial`  |

4. A Core Tools csomag telepítéséhez:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Egy helyi Functions-projekt létrehozása

A functions projektkönyvtár fájlokat tartalmazza [host.json](functions-host-json.md) és [local.settings.json](#local-settings-file), a kód az egyes funkciók almappáit mentén. Ez a könyvtár megegyezik egy függvényalkalmazást az Azure-ban. A Functions mappastruktúra kapcsolatos további információkért tekintse meg a [Azure Functions fejlesztői útmutató](functions-reference.md#folder-structure).

Verzió 2.x kell inicializálva van, és minden függvény hozzáadja az alapértelmezett nyelv sablonjainak, válassza ki a projekt alapértelmezett nyelvét. A verzió 1.x, akkor válassza ki a nyelvet minden alkalommal, amikor létrehoz egy függvényt.

A terminálablakban, vagy a parancssorból futtassa a projektet és a helyi Git-tárház létrehozásához a következő parancsot:

```bash
func init MyFunctionProj
```

A verzió 2.x, ha a parancs futtatása választania kell egy modult a projekthez. Ha azt tervezi, JavaScript-függvények kifejlesztése, válassza a **csomópont**:

```output
Select a worker runtime:
dotnet
node
```

A felfelé és lefelé nyíl billentyűk, válasszon egy nyelvet, majd nyomja le az Enter. A kimenet egy JavaScript-projekt az alábbi példához hasonlóan néz ki:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

Helyi Git-tárház nélkül a projekt létrehozásához használja a `--no-source-control [-n]` lehetőséget.

## <a name="register-extensions"></a>Bővítmények regisztrálása

A verzió 2.x verzióját az Azure Functions futtatókörnyezettel, explicit módon regisztrálnia kell a kötési bővítményeket (kötés esetében), amelyek használatával a függvényalkalmazást.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

További információkért lásd: [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Helyi fájl

A fájl local.settings.json Alkalmazásbeállítások, a kapcsolati karakterláncok és a beállítások az Azure Functions Core Tools tárolja. Az alábbi struktúrával rendelkezik:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

| Beállítás      | Leírás                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Ha a beállítása **igaz**, minden értéket a helyi gép kulcsa segítségével titkosítja. A használt `func settings` parancsokat. Alapértelmezett érték **hamis**. |
| **Értékek** | Alkalmazásbeállítások és a helyi futtatás során használt kapcsolati karakterláncok gyűjteménye. Ezeket az értékeket felelnek meg az alkalmazásbeállításokat a függvényalkalmazáshoz az Azure-ban, mint például **AzureWebJobsStorage** és **AzureWebJobsDashboard**. Számos eseményindítók és kötések rendelkezik olyan tulajdonságot, amely hivatkozik a kapcsolati karakterlánc Alkalmazásbeállítás, például **kapcsolat** számára a [Blob storage-eseményindító](functions-bindings-storage-blob.md#trigger---configuration). Az ilyen tulajdonságok szüksége lesz a megadott alkalmazás-beállítás a **értékek** tömb. <br/>**AzureWebJobsStorage** eltérő HTTP eseményindítók egy szükséges alkalmazás beállítás. Ha rendelkezik a [az Azure storage emulator](../storage/common/storage-use-emulator.md) telepítve helyben, beállíthatja **AzureWebJobsStorage** való `UseDevelopmentStorage=true` és Core Tools használja az emulátorban. Ez akkor hasznos, a fejlesztés során, de a telepítés előtt egy tényleges storage-kapcsolattal kell tesztelni. |
| **Gazdagép** | Ebben a szakaszban beállítások testre szabhatja a Functions gazdafolyamat helyi futtatás során. |
| **LocalHttpPort** | Beállítja az alapértelmezett portot használja, amikor a függvények helyi állomás (`func host start` és `func run`). A `--port` parancssori kapcsoló elsőbbséget élvez ezt az értéket. |
| **CORS** | Meghatározza az engedélyezett eredetek [eltérő eredetű erőforrások megosztása (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Források szóközök nélküli szövegláncként egy vesszővel tagolt lista formájában vannak megadva. A helyettesítő karaktert tartalmazó értéket (\*) támogatott, amely lehetővé teszi a kérelmek bármilyen forrásból. |
| **Kapcsolati Sztringjei** | Ne használja a gyűjtemény a kapcsolati karakterláncokat a függvénykötésnek használják. Ez a gyűjtemény csak használják keretrendszerek, a kapcsolati karakterláncok be kell szereznie a **kapcsolati Sztringjei** szakaszában egy konfigurációs fájlba, például [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Kapcsolati karakterláncok ezt az objektumot a rendszer felveszi a környezetbe, a szolgáltató típusát [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Ebben a gyűjteményben lévő elemek az Azure-ban nincs közzétéve a többi alkalmazás beállításokkal. Ezekre az értékekre, explicit módon kell hozzáadnia a **kapcsolati karakterláncok** szakaszában a **Alkalmazásbeállítások** a függvényalkalmazás számára. |

A függvény alkalmazás beállítások értékeit is elolvashatja a kódban környezeti változókként. További információkért tekintse meg a környezeti változók szakaszban az alábbi nyelvspecifikus referencia-témakörök:

+ [C# előre lefordított](functions-dotnet-class-library.md#environment-variables)
+ [C# script (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

A local.settings.json fájlban beállítások csak által használt funkciók eszközök helyi futtatás során. Alapértelmezés szerint ezek a beállítások nem települnek át automatikusan az Azure-bA a projekt közzétételekor. Használja a `--publish-local-settings` váltson [közzétételekor](#publish) , hogy ezek a beállítások lettek hozzáadva a függvényalkalmazáshoz az Azure-ban. Az értékek **kapcsolati Sztringjei** soha nem kerülnek közzétételre.

Ha nincs érvényes tárolási kapcsolati karakterlánc beállítása a **AzureWebJobsStorage** és az emulatort használja, a következő hibaüzenet jelenik meg:  

>Hiányzó értéke AzureWebJobsStorage a local.settings.json. Ez azért szükséges, az összes eseményindítók nem HTTP. Futtathatja a "func azure functionapp fetch-alkalmazás-beállítások <functionAppName>", vagy adjon meg egy kapcsolati karakterláncot local.settings.json.

### <a name="get-your-storage-connection-strings"></a>A storage kapcsolati karakterláncok beolvasása

Akkor is, ha a fejlesztés a storage emulatort használja, érdemes teszt egy tényleges storage-kapcsolattal. Ha már rendelkezik [létrehozott egy tárfiókot](../storage/common/storage-create-storage-account.md), olvashatók be egy érvényes tárolási kapcsolati karakterlánc a következő módszerek valamelyikével:

+ Az a [Azure Portal]. Lépjen a tárfiókhoz válassza **hozzáférési kulcsok** a **beállítások**, majd másolja ki az egyik a **kapcsolati karakterlánc** értékeket.

  ![Kapcsolati karakterlánc másolása az Azure Portalról](./media/functions-run-local/copy-storage-connection-portal.png)

+ Használat [Azure Storage Explorer](http://storageexplorer.com/) csatlakozni az Azure-fiókjával. Az a **Explorer**, bontsa ki az előfizetést, válassza ki a tárfiókját, és másolja az elsődleges vagy másodlagos kapcsolati karakterláncot. 

  ![Másolja a kapcsolati karakterláncot a Storage Explorer](./media/functions-run-local/storage-explorer.png)

+ Core Tools használatával töltse le a kapcsolati karakterláncot az Azure-ban, a következő parancsok egyikét:

    + Minden beállítás meglévő függvényalkalmazással letöltése:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + Egy adott tárfiók kapcsolati karakterláncának lekérése:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    Ön már aláírással nem Azure-ba, amikor erre kéri.

## <a name="create-func"></a>Függvény létrehozása

Hozzon létre egy függvényt, futtassa a következő parancsot:

```bash
func new
```

A verzió 2.x futtatásakor `func new` válasszon ki egy sablont a függvényalkalmazás alapértelmezett nyelvén kéri, majd válassza ki a függvény nevét is megkezdésére. A verzió 1.x, válassza ki a nyelvet a is kéri.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

Függvény kód jön létre egy almappát a megadott függvény neve a következő Üzenetsor eseményindító kimenetben láthatja:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

A parancs az alábbi argumentumok használatával is megadhatja ezeket a beállításokat:

| Argumentum     | Leírás                            |
| ------------------------------------------ | -------------------------------------- |
| **`--language -l`**| A sablon programozási nyelvet, például C#, F # vagy JavaScript. Ez a beállítás szükséges verzió 1.x. A verzió 2.x, nem használja ezt a beállítást, vagy válassza ki a projekt alapértelmezett nyelvet. |
| **`--template -t`** | A sablon nevét, amely az értékek egyike lehet:<br/><ul><li>`Blob trigger`</li><li>`Cosmos DB trigger`</li><li>`Event Grid trigger`</li><li>`HTTP trigger`</li><li>`Queue trigger`</li><li>`SendGrid`</li><li>`Service Bus Queue trigger`</li><li>`Service Bus Topic trigger`</li><li>`Timer trigger`</li></ul> |
| **`--name -n`** | A függvény nevét. |

Ha például egyetlen paranccsal hozzon létre egy JavaScript HTTP-eseményindító, futtassa:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Egy üzenetsor által aktivált függvény létrehozásához egyetlen paranccsal futtassa:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Függvény helyi futtatása

A Functions-projekt futtatása, futtassa az a funkciók gazdagép. A gazdagép lehetővé teszi, hogy a projekt összes Functions eseményindítók:

```bash
func host start
```

`func host start` a következő beállításokat támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | A helyi port figyelésére. Alapértelmezett érték: 7071. |
| **`--debug <type>`** | Elindul az a gazdagép, a hibakeresési port meg, hogy lehet kapcsolódni a **func.exe** feldolgozása a [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) vagy [Visual Studio 2017](functions-dotnet-class-library.md). A *\<típus\>* lehetőségeket vannak `VSCode` és `VS`.  |
| **`--cors`** | CORS-források, szóközök nélküli szövegláncként vesszővel tagolt listája. |
| **`--nodeDebugPort -n`** | A csomópont hibakereső használandó port. Alapértelmezett: Launch.json vagy 5858 egy értéket. |
| **`--debugLevel -d`** | A konzol nyomkövetési szintet (kikapcsolva, részletes, adatok, figyelmeztetés vagy hiba). Alapértelmezett: információ.|
| **`--timeout -t`** | Az a funkciók gazdagép indítása, másodpercek alatt időtúllépése. Alapértelmezett érték: 20 másodperc.|
| **`--useHttps`** | Kösse `https://localhost:{port}` helyett a `http://localhost:{port}`. Alapértelmezés szerint ez a beállítás a számítógép megbízható tanúsítványt hoz létre.|
| **`--pause-on-error`** | Mielőtt kilépne a folyamat szüneteltetéséhez további adatokat. Használt Core Tools a Visual Studio vagy a VS Code elindításához.|

A Functions gazdagép indításakor, azt az URL-cím a HTTP által aktivált függvények kimenete:

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="passing-test-data-to-a-function"></a>A függvénynek megadásának teszt adatai

A függvények helyi tesztelése, [indítsa el a függvények gazdagép](#start) és végpontok meghívása HTTP-kérelmek használatával a helyi kiszolgálón. A végpont hívja függvény típusától függ.

>[!NOTE]  
> Ebben a témakörben szereplő példák a terminált vagy parancssort a HTTP-kéréseket küldhet a cURL eszköz használható. A választott eszköz használatával HTTP-kérelmeket küldjön a helyi kiszolgálón. A cURL eszköz alapértelmezés szerint a Linux-alapú rendszerekhez érhető el. A Windows, akkor először le kell töltenie, és telepítse a [cURL eszköz](https://curl.haxx.se/).

A tesztelési funkciók további általános információkért lásd: [stratégiát a kódot tesztelés az Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>A HTTP és webhookok által aktivált függvények

A következő meghívása HTTP- és helyi futtatásához végpont által aktivált függvények:

    http://localhost:{port}/api/{function_name}

Ellenőrizze, hogy a ugyanazt a kiszolgálónevet és a portot, amelyet a Functions gazdagép figyel a következőn:. Jelenik meg ez a kimenet jön létre, ha a függvény gazdagép indítása. Az URL-cím használatával az eseményindító által támogatott bármely HTTP-metódus hívása. 

A következő cURL-parancs eseményindítók a `MyHttpTrigger` egy GET kérelmet a rövid útmutató a függvénynek a _neve_ paramétert a lekérdezési karakterláncban. 

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
Az alábbi példában ugyanannak a függvénynek egy POST-kérés átadja a nevű _neve_ a kérelem törzsében szereplő:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Kérelmek beolvasása egy böngészőből, a lekérdezési karakterláncban adatok átadására is felvehető. Minden más HTTP-metódusok a cURL, Fiddler, Postman vagy egy hasonló HTTP tesztelési eszközt kell használnia.  

#### <a name="non-http-triggered-functions"></a>Nem HTTP által aktivált függvények

HTTP-triggerek és webhookok eltérő funkciók mindenfajta tesztelheti a függvények helyi felügyeleti végpont meghívásával. A HTTP POST-kérelmet a végpontot hív-e a helyi kiszolgálón aktiválja a függvényt. Tesztadatok a POST-kérés törzse végrehajtásával lehetősége van átadni. Ez a funkció hasonlít a **teszt** lap az Azure Portalon.  

-HTTP funkciók elindítani a következő rendszergazda végpont hívja meg:

    http://localhost:{port}/admin/functions/{function_name}

Tesztadatok egy függvény a felügyeleti végponthoz való megfelelés esetén meg kell adni az adatokat egy POST-kérelem üzenet törzsében. Az üzenettörzs szükséges a következő JSON formátummal rendelkezik:

```JSON
{
    "input": "<trigger_input>"
}
````

A `<trigger_input>` érték a függvény által várt formátumú adatokat tartalmaz. A következő cURL-példában a bejegyzés egy `QueueTriggerJS` függvény. Ebben az esetben a bemeneti egy karakterláncérték, amely megegyezik a várható, hogy az üzenetsorban található üzenet.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Használatával a `func run` parancsot a verzió 1.x

>[!IMPORTANT]  
> A `func run` parancs nem támogatott verzió a 2.x-es eszközök. További információkért lásd a témakör [bemutatásához az Azure Functions runtime verziók](set-runtime-version.md).

Egy függvény segítségével közvetlenül is hívhat `func run <FunctionName>` , és adja meg a bemeneti adatokat a függvény. Ez a parancs hasonlít egy függvény használatával fut a **teszt** lap az Azure Portalon. 

`func run` a következő beállításokat támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Beágyazott tartalom. |
| **`--debug -d`** | Csatoljon egy hibakeresőt a gazdagép-folyamat, mielőtt futtatná a függvényt.|
| **`--timeout -t`** | Idő (másodpercben) várja meg, amíg készen áll a függvények helyi gazdagépen.|
| **`--file -f`** | A tartalom használandó fájl neve.|
| **`--no-interactive`** | Nem kéri a bemenetet. Automation-forgatókönyvek esetén hasznos.|

Ha például egy HTTP-eseményindítóval aktivált függvényt hívja, és adja át a tartalomtörzs, futtassa a következő parancsot:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Helyileg naplófájlok megtekintése

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Közzététel az Azure-bA

A Functions-projekt közzététele az Azure-ban egy függvényalkalmazás, használja a `publish` parancsot:

```bash
func azure functionapp publish <FunctionAppName>
```

Használhatja a következő beállításokat:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Közzétételi beállítások a local.settings.json az Azure-ba, kéri a felhasználót, felülírása, ha a beállítás már létezik. A storage emulatort használja, ha módosítja az Alkalmazásbeállítás- [tényleges tárolási kapcsolat](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Együtt kell használni `-i`. Felülírja a helyi érték AppSettings az Azure-ban, ha különböző. Az alapértelmezett érték kérése.|

Ez a parancs az Azure-ban meglévő függvényalkalmazással tesz közzé. Hiba akkor fordul elő, amikor a `<FunctionAppName>` az előfizetés nem létezik. Megtudhatja, hogyan hozhat létre egy függvényalkalmazást a parancssort vagy terminálablakot az Azure CLI-vel, tekintse meg a [hozzon létre egy Függvényalkalmazást, kiszolgáló nélküli végrehajtáshoz](./scripts/functions-cli-create-serverless.md).

A `publish` parancs feltölti az a funkciók projekt könyvtár tartalmát. Ha törli a fájlokat helyileg, a `publish` parancs nem törli őket az Azure-ból. Használatával törölheti az Azure-ban a [Kudu eszköz](functions-how-to-use-azure-function-app-settings.md#kudu) a a [Azure Portal].  

>[!IMPORTANT]  
> Ha függvényalkalmazást hoz létre az Azure-ban, akkor verzióját használja, alapértelmezés szerint a függvény futtatókörnyezetét 1.x. Győződjön meg arról, a függvény Alkalmazásverzió használatát a 2.x verziójú futtatókörnyezet, adja hozzá az Alkalmazásbeállítás `FUNCTIONS_EXTENSION_VERSION=beta`.  
Az alábbi Azure CLI-kód használatával adja hozzá ezt a beállítást a függvényalkalmazáshoz:

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>További lépések

Az Azure Functions Core Tools van [nyílt forráskód és a Githubon található](https://github.com/azure/azure-functions-cli).  
A fájl egy programhiba vagy szolgáltatás kérelmet [nyisson meg egy GitHub-problémát](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Az Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
