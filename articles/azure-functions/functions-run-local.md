---
title: Fejlesztés és helyileg történő futtatása az Azure functions |} Microsoft Docs
description: Megtudhatja, hogyan kód és a helyi számítógépen az Azure functions tesztelése az Azure Functions futtatása előtt.
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
ms.date: 06/03/2018
ms.author: glenga
ms.openlocfilehash: 5613b6b30d97b88bdfa6b00f90e334f1756ad614
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294492"
---
# <a name="code-and-test-azure-functions-locally"></a>Kód és az Azure Functions helyi tesztelése

Amíg a [Azure Portal] teljes készlete eszközök fejlesztési és tesztelési Azure Functions számos fejlesztők inkább egy helyi fejlesztési felület biztosít. Az Azure Functions megkönnyíti, hogy a kedvenc kód szerkesztése és a helyi fejlesztői eszközök segítségével történő fejlesztéséhez és teszteléséhez a funkciók a helyi számítógépen. A funkciók is elindíthatja az eseményeket az Azure-ban, és a C# és JavaScript-funkcióként is debug a helyi számítógépen. 

A Visual Studio C# fejlesztő, az Azure Functions is [integrálható a Visual Studio 2017](functions-develop-vs.md).

>[!IMPORTANT]  
> A függvény ugyanazt az alkalmazást a portál fejlesztési helyi fejlesztési nem kombinálhatók. Létrehozásakor, és a helyi projektből funkciók közzététele, ha nem próbálja karbantartása, vagy módosítani a projekt kódját a portálon.

## <a name="install-the-azure-functions-core-tools"></a>Az Azure Functions Core Tools telepítése

[Az Azure Functions Core eszközök] az Azure Functions futtatókörnyezettel, amely a helyi fejlesztési számítógépen futtathatja helyi verziója. Nincs emulátor vagy szimulátor. Az azonos futásidejű powers működik az Azure-ban. Az Azure Functions Core eszközök két verziója van:

+ [Verzió 1.x](#v1): verziót támogatja a futtatókörnyezet 1.x. Ebben a verzióban csak a Windows rendszerű számítógépek támogatják, és az telepítve van egy [npm csomag](https://docs.npmjs.com/getting-started/what-is-npm).
+ [Verzió 2.x](#v2): verziót támogatja a futtatókörnyezet 2.x. Ezen verziója támogatja a [Windows](#windows-npm), [macOS](#brew), és [Linux](#linux). Használja a platform-specifikus csomag menedzserek vagy npm a telepítéshez. 

### <a name="v1"></a>Verzió 1.x

Az eszközök eredeti verzióját használja a funkciók 1.x futásidejű. Ebben a verzióban a .NET-keretrendszer (4.7.1) használ, és csak a Windows rendszerű számítógépeken támogatott. A verzió 1.x eszközök telepítése előtt [NodeJS telepítése](https://docs.npmjs.com/getting-started/installing-node), mely tartalmazza az npm.

Az alábbi parancs segítségével a verzió 1.x telepíti:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Verzió 2.x

>[!NOTE]
> Az Azure Functions futtatókörnyezettel 2.0 előzetes, és jelenleg nem minden Azure Functions támogatja. További információkért lásd: [Azure Functions verziók](functions-versions.md) 

Verzió 2.x eszközt használja az Azure Functions futtatókörnyezettel 2.x, amely a .NET Core épül. Ez a támogatott .NET Core 2.x támogatja, beleértve az összes platformon [Windows](#windows-npm), [macOS](#brew), és [Linux](#linux).

#### <a name="windows-npm"></a>Windows

Az alábbi lépéseket az npm segítségével Core eszközök telepítése Windows. Is [Chocolatey](https://chocolatey.org/). További információkért lásd: a [Core eszközök információs](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Telepítés [.NET Core 2.0 a Windows](https://www.microsoft.com/net/download/windows).

2. Telepítés [Node.js], mely tartalmazza az npm. A verzió 2.x az eszközök csak a Node.js 8.5 és újabb verziói támogatottak.

3. A Core eszközök telepítéséhez:

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>A Homebrew MacOS

Az alábbi lépéseket a Core eszközök telepítéséhez macOS Homebrew használja.

1. Telepítés [.NET Core-2.0 macOS](https://www.microsoft.com/net/download/macos).

2. Telepítés [Homebrew](https://brew.sh/), ha még nincs telepítve.

3. A Core eszközök telepítéséhez:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) rendelkező APT

Az alábbi lépéseket használata [APT](https://wiki.debian.org/Apt) az Ubuntu/Debian Linux terjesztési Core eszközök telepítéséhez. Más Linux terjesztéseket, tekintse meg a [Core eszközök információs](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Telepítés [.NET Core 2.0 Linux](https://www.microsoft.com/net/download/linux).

2. A Microsoft-termékkulcs regisztrálása megbízhatóként:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Ellenőrizze az Ubuntu server fut-e a megfelelő verziók egyike az alábbi táblázatban. A apt forrás hozzáadásához futtassa:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | A Linux-disztribúció | Verzió |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux 18 Mentaízű    | `xenial`  |

4. A Core eszközök telepítéséhez:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="run-azure-functions-core-tools"></a>Az Azure Functions Core eszközeinek futtatása

Az Azure Functions Core eszközök a következő parancs aliasok bővült:

+ **FUNC**
+ **azfun**
+ **azurefunctions**

Ezek aliasok bármelyike használható ahol `func` a példákat is megjelennek.

```bash
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Helyi funkciók-projekt létrehozása

A helyi futtatás során egy funkciók projekt-e a fájlok megegyező nevű könyvtárat [host.json](functions-host-json.md) és [local.settings.json](#local-settings-file). Ez a könyvtár megegyezik a függvény alkalmazások az Azure-ban. Az Azure Functions mappaszerkezet kapcsolatos további tudnivalókért tekintse meg a [Azure Functions fejlesztői útmutatója](functions-reference.md#folder-structure).

A következő parancsot a projekt és a helyi Git-tárház létrehozásához futtassa a terminálablakot vagy a parancssorból:

```bash
func init MyFunctionProj
```

A kimenet a következőképpen néz ki:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

A projekt nélkül egy helyi Git-tárház létrehozásához használja a `--no-source-control [-n]` lehetőséget.

## <a name="register-extensions"></a>Bővítmények regisztrálása

A verzió, az Azure Functions futtatókörnyezettel 2.x, explicit módon regisztrálnia kell az függvény alkalmazásban használt kötés extensions (kötéstípust).

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

További információkért lásd: [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Helyi fájl

A fájl local.settings.json Alkalmazásbeállítások, a kapcsolati karakterláncok és az Azure Functions Core eszközök beállításai tárolja. Az alábbi szerkezettel rendelkezik:

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
| **IsEncrypted** | Ha beállítása **igaz**, minden értéket a helyi számítógép kulccsal titkosított. A használt `func settings` parancsok. Alapértelmezett érték **hamis**. |
| **Értékek** | Az alkalmazásbeállítások és a helyi futtatás során használt kapcsolati karakterláncok gyűjteménye. Ezek megegyeznek az Azure, a függvény alkalmazás Alkalmazásbeállítások például **AzureWebJobsStorage** és **AzureWebJobsDashboard**. Sok eseményindítók és kötések rendelkezik olyan tulajdonságon, amely hivatkozik, mint a kapcsolati karakterlánc Alkalmazásbeállítás **kapcsolat** a a [Blob storage eseményindító](functions-bindings-storage-blob.md#trigger---configuration). Az ilyen tulajdonságok van szüksége a megadott alkalmazás-beállítás a **értékek** tömb. <br/>**AzureWebJobsStorage** eltérő HTTP eseményindítók kötelező alkalmazás beállítás. Ha rendelkezik a [az Azure storage emulator](../storage/common/storage-use-emulator.md) helyileg telepíteni, beállíthatja **AzureWebJobsStorage** való `UseDevelopmentStorage=true` és alapvető eszközök használja az emulátorban. Ez akkor hasznos, a fejlesztés során, de a telepítés előtt tényleges tárolási kapcsolattal kell tesztelni. |
| **Gazdagép** | Ebben a szakaszban beállítások testreszabása a funkciók gazdafolyamat, a helyi futtatás során. |
| **LocalHttpPort** | Beállítja azt a portot használja a helyi funkciók állomás fut (`func host start` és `func run`). A `--port` parancssori kapcsoló elsőbbséget élvez ezt az értéket. |
| **CORS** | Meghatározza az engedélyezett eredeteket [eltérő eredetű erőforrások megosztása (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Források, szóközök nélkül vesszővel tagolt lista formájában vannak megadva. A helyettesítő karakteres érték (\*) támogatott, amely lehetővé teszi a kérelmek bármely a forrásból. |
| **ConnectionStrings** | Ez a gyűjtemény nem használatos a a függvénykötés által használt kapcsolati karakterláncok. Ez a gyűjtemény-keretrendszerekhez, be kell szereznie a kapcsolati karakterláncok csak használja a **ConnectionStrings** egy konfigurációs szakasza fájlba, például a [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Ez az objektum kapcsolati karakterláncokat hozzáadódnak a szolgáltató típusát a környezet [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Ez a gyűjtemény elemeinek Azure nincs közzétéve a más alkalmazás beállításokkal. Explicit módon adja hozzá ezeket az értékeket a **kapcsolati karakterláncok** szakasza a **Alkalmazásbeállítások** függvény alkalmazás. |

A függvény alkalmazás beállítások értékeit is elolvashatja a kódban környezeti változóként. További információkért tekintse meg a környezeti változók szakaszban nyelvspecifikus hivatkozás témakörök közül:

+ [C# előre le fordítva](functions-dotnet-class-library.md#environment-variables)
+ [C# script (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

A local.settings.json fájl csak által használt funkciók eszközök a helyi futtatás során. Alapértelmezés szerint ezek a beállítások nem települnek át automatikusan a projektet az Azure-ba való közzétételekor. Használja a `--publish-local-settings` kapcsoló [közzétételekor](#publish) való győződjön meg arról, hogy ezek a beállítások hozzáadódnak a függvény alkalmazást az Azure-ban. Az értékek **ConnectionStrings** soha nem kerülnek közzétételre.

Ha nincs érvényes tárolási kapcsolati karakterlánc beállítása a **AzureWebJobsStorage** és az emulátor nincs használatban, a következő hibaüzenet jelenik meg:  

>Hiányzó érték a AzureWebJobsStorage local.settings.json. Ez azért szükséges, az összes eseményindítók HTTP eltérő. Futtatása "func azure functionapp fetch--Alkalmazásbeállítások <functionAppName>", vagy adjon meg egy kapcsolati karakterláncot a local.settings.json.

### <a name="get-your-storage-connection-strings"></a>A tárolási kapcsolati karakterláncok beolvasása

Akkor is, ha a storage emulator használatával fejlesztési, érdemes lehet egy tényleges tárolókapcsolat tesztelni. Feltéve, hogy már rendelkezik [egy tárfiókot létre](../storage/common/storage-create-storage-account.md), kaphat a egy érvényes tárolási kapcsolati karakterlánc a következő módszerek valamelyikével:

+ Az a [Azure Portal]. Lépjen a tárfiókhoz, jelölje be **hívóbetűk** a **beállítások**, majd másolja át az egyik a **kapcsolati karakterlánc** értékek.

  ![Másolja a kapcsolati karakterláncot az Azure portálról](./media/functions-run-local/copy-storage-connection-portal.png)

+ Használjon [Azure Tártallózó](http://storageexplorer.com/) csatlakozni az Azure-fiókjával. Az a **Explorer**, bontsa ki az előfizetés, válassza ki a tárfiók, és másolja az elsődleges vagy másodlagos kapcsolati karakterláncot. 

  ![Másolja a kapcsolati karakterláncot a Tártallózó alkalmazással](./media/functions-run-local/storage-explorer.png)

+ Alapvető eszközök segítségével töltse le a kapcsolati karakterlánc a következő parancsok egyikét az Azure-ból:

    + Minden beállítás letölthető egy meglévő függvény alkalmazást:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    + Töltse le a kapcsolati karakterlánc meghatározott tárfiókok esetén:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    
    Mindkét parancsok első jelentkezzen be Azure igényelnek.

<a name="create-func"></a>
## <a name="create-a-function"></a>Függvény létrehozása

A függvény létrehozásához futtassa a következő parancsot:

```bash
func new
``` 
`func new` a következő nem kötelező argumentum használatát is támogatja:

| Argumentum     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | A sablon programozási nyelv, például a C#, F # vagy JavaScript. |
| **`--template -t`** | A sablonnevet. |
| **`--name -n`** | A függvény nevét. |

Például a JavaScript HTTP-eseményindítóval létrehozásához futtassa:

```bash
func new --language JavaScript --template "Http Trigger" --name MyHttpTrigger
```

A várólista-eseményindítóval aktivált függvény létrehozásához futtassa:

```bash
func new --language JavaScript --template "Queue Trigger" --name QueueTriggerJS
```bash
<a name="start"></a>
## Run functions locally

To run a Functions project, run the Functions host. The host enables triggers for all functions in the project:

```bash
func host start
```

`func host start` támogatja a következő beállításokat:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | A helyi portot. Alapértelmezett érték: 7071. |
| **`--debug <type>`** | A beállítások `VSCode` és `VS`. |
| **`--cors`** | A CORS források, szóközök nélkül vesszővel tagolt listája. |
| **`--nodeDebugPort -n`** | A csomópont hibakereső használandó portot. Alapértelmezett: Launch.json vagy 5858 egy értéket. |
| **`--debugLevel -d`** | A konzol nyomkövetési szint (kikapcsolt, részletes, információ, figyelmeztetés vagy hiba). Alapértelmezett: adatait.|
| **`--timeout -t`** | Az időtúllépés másodpercben elindításához a funkciók gazdagép. Alapértelmezett: 20 másodperc.|
| **`--useHttps`** | A kötés https://localhost:{port} helyett a http://localhost:{port}. Ez a beállítás alapértelmezés szerint létrehoz megbízható tanúsítvány a számítógépen.|
| **`--pause-on-error`** | A folyamat leállítása előtt szüneteltetése további adatokat. Akkor hasznos, ha az Azure Functions Core eszközök fókusza az integrált fejlesztési környezeti (IDE).|

A funkciók gazdagép indításakor azt az URL-cím a HTTP-eseményindítókkal aktivált függvényeket kimenete:

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="vs-debug"></a>A Visual STUDIO Code vagy a Visual Studio hibakeresési

A hibakereső csatolásához át a `--debug` argumentum. JavaScript-funkcióként hibakeresési, használja a Visual Studio Code. C# funkciók a Visual Studio használata.

Hibakeresési C# funkciók, használja a `--debug vs`. Is [Azure Functions Visual Studio 2017 eszközök](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Indítsa el a gazdagépen, és állítsa be a JavaScript-hibakeresés, futtassa:

```bash
func host start --debug vscode
```

> [!IMPORTANT]
> Csak a támogatott 8.x Node.js hibakereséshez. NODE.js 9.x nem támogatott. 

Ezt követően a Visual Studio Code, az a **Debug** nézetben jelölje ki **csatlakoztatása az Azure Functions**. Töréspontokat csatolása, vizsgálja meg a változók és kód lépéseit.

![JavaScript-hibakeresés a Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Egy függvény sikeres Tesztadatok

Helyileg, a funkciók tesztelésére, [a funkciók gazdagép indítása](#start) és végpontok hívja meg a helyi kiszolgálón a HTTP-kérelmekre. A végpont meghívja a függvény függ. 

>[!NOTE]  
> Ebben a témakörben szereplő példák a cURL eszközzel HTTP-kérelmek küldése a Terminálszolgáltatások vagy a parancssorból. A kiválasztott eszköz segítségével HTTP-kérelmeket küldjön a helyi kiszolgáló. A cURL eszköz alapértelmezés szerint a Linux-alapú rendszereken érhető el. A Windows, először le kell töltenie és telepítse a [cURL eszköz](https://curl.haxx.se/).

A tesztelés funkciók további általános információkért lásd: [a kódot az Azure Functions tesztelése kapcsolatos olyan stratégiák](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>A HTTP és a webhook indított funkciók

A következő meghívja a helyi futtatásához a HTTP és a webhook végpont indított funkciók:

    http://localhost:{port}/api/{function_name}

Ügyeljen arra, hogy az azonos kiszolgáló nevére és a portot, amelyet figyeli a funkciók állomás. Is megjelenik ez a kimenet jön létre, a függvény gazdagép indításakor. Az URL-CÍMÉT az eseményindító által támogatott bármely HTTP-metódus hívása. 

A következő cURL-parancsot az eseményindítók a `MyHttpTrigger` gyors üzembe helyezés függvény érkezett egy GET kérelmet a _neve_ paramétert a lekérdezési karakterláncban. 

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
A következő példa egy fájlból egy POST kérést, hogy ugyanazt a funkciót _neve_ a kérelem törzsében szereplő:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Hogy GET kérések adatok átadására a lekérdezési karakterláncban a böngészőből. Minden egyéb HTTP-metódus, a cURL, Fiddler, Postman vagy egy hasonló HTTP vizsgálati eszköz kell használnia.  

#### <a name="non-http-triggered-functions"></a>A HTTP nélküli indított funkciók
A különböző funkciók eltérő HTTP eseményindítók és webhookokkal tesztelheti a funkciók helyileg felügyeleti végpont meghívásával. A függvény hívása a következő ehhez a végponthoz, a HTTP POST-kérelmet a helyi kiszolgáló váltja ki. Vizsgálati adatok opcionálisan átadása a végrehajtása a POST kérelem törzsét. Ez a funkció hasonlít a **teszt** fülre az Azure portálon.  

A következő rendszergazda végpont való nem HTTP-függvények hívása:

    http://localhost:{port}/admin/functions/{function_name}

Vizsgálati adatok átadása a rendszergazda végpont egy függvény, meg kell adnia egy POST kérést üzenet törzsében lévő adatokat. Az üzenettörzs szükség van a következő JSON formátummal:

```JSON
{
    "input": "<trigger_input>"
}
```` 
A `<trigger_input>` érték, amelyet a függvény várt formátumú adatokat tartalmaz. A következő cURL-példa egy mutató POST egy `QueueTriggerJS` függvény. Ebben az esetben a bemeneti érték karakterlánc, amely megfelel az várható, hogy a várakozási sorban található üzenetek.      

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Használja a `func run` parancs verziójában 1.x

>[!IMPORTANT]  
> A `func run` parancs nem támogatott verzióját az eszközök 2.x. További információkért lásd a témakör [bemutatásához az Azure Functions futásidejű verziók](set-runtime-version.md).

Egy függvény segítségével közvetlenül is hívhat `func run <FunctionName>` , és adjon meg a függvény a bemeneti adatok. Ez a parancs hasonlít fut, a függvény használatával a **teszt** fülre az Azure portálon. 

`func run` támogatja a következő beállításokat:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Beágyazott tartalmat. |
| **`--debug -d`** | A hibakereső csatolása a gazdafolyamat függvény futtatása előtt.|
| **`--timeout -t`** | Idő (másodpercben) várja meg, míg a helyi funkciók gazdagépen elkészült.|
| **`--file -f`** | A tartalom használandó fájl neve.|
| **`--no-interactive`** | Nem kéri a bemenetben. Automatizálási esetekben hasznos.|

Például egy HTTP-eseményindítóval aktivált függvény, és adja át a tartalomtörzs, futtassa a következő parancsot:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Helyileg naplófájlok megtekintése

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Közzététel az Azure platformon

A funkciók projekt közzététele függvény alkalmazásokhoz az Azure-ban, használja a `publish` parancs:

```bash
func azure functionapp publish <FunctionAppName>
```

A következő beállításokat is használhatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Közzétételi beállítások a local.settings.json az Azure-ba, arra kéri a írhatja felül, ha a beállítás már létezik. Ha a storage emulator használ, az Alkalmazásbeállítás módosítja egy [tényleges tárolókapcsolat](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Együtt kell használni `-i`. Felülírja a helyi érték AppSettings az Azure-ban, ha különböző. Alapértelmezett érték kérése.|

Ez a parancs tesz közzé egy meglévő függvény alkalmazáshoz az Azure-ban. Hiba akkor fordul elő, amikor a `<FunctionAppName>` az előfizetéshez nem létezik. A függvény alkalmazás létrehozásának a parancssort vagy terminálablakot az Azure parancssori felület használatával, lásd: [hozzon létre egy kiszolgáló nélküli végrehajtási függvény alkalmazást](./scripts/functions-cli-create-serverless.md).

A `publish` parancs feltölti a funkciók projekt könyvtár tartalmát. Ha törli a fájlokat helyileg, a `publish` parancs nem törli azokat az Azure-ból. Használatával törölheti a fájlokat az Azure-ban a [Kudu eszköz](functions-how-to-use-azure-function-app-settings.md#kudu) a a [Azure Portal].  

>[!IMPORTANT]  
> Ha az Azure-ban létrehoz egy függvény alkalmazást, akkor verzióját használja 1.x függvény futásidejű alapértelmezés szerint. A függvény az alkalmazás használatát verziója annak 2.x futtatókörnyezet, az Alkalmazásbeállítás hozzáadása `FUNCTIONS_EXTENSION_VERSION=beta`.  
A következő kódot az Azure parancssori felület használatával adja hozzá ezt a beállítást az függvény alkalmazáshoz: 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>További lépések

Az Azure Functions Core Tools [nyissa meg a forrás és a Githubon található](https://github.com/azure/azure-functions-cli).  
A következő fájl egy hiba vagy a szolgáltatás kérelem [nyissa meg a GitHub probléma](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Az Azure Functions Core eszközök]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
