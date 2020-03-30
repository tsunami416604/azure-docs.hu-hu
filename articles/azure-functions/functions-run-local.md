---
title: Az Azure Functions alapvető eszközeinek használata
description: Ismerje meg, hogyan kódolhatja és tesztelheti az Azure-függvényeket a parancssorból vagy a terminálból a helyi számítógépen, mielőtt futtatja őket az Azure Functions en.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 19691a654162ee3855cb257fd42e29d2e1fc0157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276659"
---
# <a name="work-with-azure-functions-core-tools"></a>Az Azure Functions alapvető eszközeinek használata

Az Azure Functions Core Tools lehetővé teszi a helyi számítógépen lévő funkciók fejlesztését és tesztelését a parancssorból vagy a terminálból. A helyi függvények csatlakozhatnak az élő Azure-szolgáltatásokhoz, és a teljes Functions futásidő használatával hibakeresést okozhatnak a helyi számítógépen. Akár egy függvényalkalmazást is üzembe helyezhet az Azure-előfizetésében.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

A helyi számítógépen a függvények fejlesztése és közzététele az Azure-ban a Core Tools használatával az alábbi alapvető lépéseket követi:

> [!div class="checklist"]
> * [Telepítse a Core Tools és a függőségek.](#v2)
> * [Függvényalkalmazás-projekt létrehozása nyelvspecifikus sablonból.](#create-a-local-functions-project)
> * [Regisztrálja az eseményindító- és kötési kiterjesztéseket.](#register-extensions)
> * [Tároló és egyéb kapcsolatok definiálása.](#local-settings-file)
> * [Hozzon létre egy függvényt egy eseményindítóból és egy nyelvspecifikus sablonból.](#create-func)
> * [Futtassa a függvényt helyileg.](#start)
> * [Tegye közzé a projektet az Azure-ban.](#publish)

## <a name="core-tools-versions"></a>A Core Tools verziói

Az Azure Functions Core Tools három verziója létezik. A használt verzió a helyi fejlesztői környezettől, [a nyelvválasztástól](supported-languages.md)és a szükséges támogatási szinttől függ:

+ **1.x-es verzió:** Támogatja az Azure Functions futásidejű 1.x verzióját. Az eszközök ezen verziója csak Windows rendszerű számítógépeken támogatott, és [npm csomagból](https://www.npmjs.com/package/azure-functions-core-tools)van telepítve.

+ [**2.x/3.x verzió**](#v2): Támogatja [az Azure Functions futásidejű 2.x vagy 3.x verzióját](functions-versions.md). Ezek a verziók támogatják a [Windowst,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [a macOS-t](/azure/azure-functions/functions-run-local?tabs=macos#v2)és a [Linuxot,](/azure/azure-functions/functions-run-local?tabs=linux#v2) és platformspecifikus csomagkezelőket vagy npm-et használnak a telepítéshez.

Eltérő rendelkezés hiányában a példák ebben a cikkben a 3.x verzió.

## <a name="install-the-azure-functions-core-tools"></a>Az Azure Functions Core Tools telepítése

[Az Azure Functions Core Tools] tartalmazza ugyanannak a futásidejűnek a verzióját, amely az Azure Functions futásidejű, a helyi fejlesztői számítógépen futtatható futásidejű. Emellett parancsokat is biztosít a függvények létrehozásához, az Azure-hoz való csatlakozáshoz és a függvényprojektek üzembe helyezéséhez.

>[!IMPORTANT]
>Az Azure [CLI-t](/cli/azure/install-azure-cli) helyileg kell telepítenie ahhoz, hogy közzé tehethesd az Azure-ban az Azure Functions Core Tools-ból.  

### <a name="version-2x-and-3x"></a><a name="v2"></a>2.x és 3.x verzió

Az eszközök 2.x/3.x verziója az Azure Functions futásidejű, .NET Core alapú. Ez a verzió minden platformon támogatott .NET Core támogatja, beleértve a [Windows,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)és [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2). 

> [!IMPORTANT]
> A .NET Core SDK telepítésének követelményét [a bővítménykötegek]segítségével megkerülheti.

# <a name="windows"></a>[Windows](#tab/windows)

A következő lépések az npm használatával telepítheti a Core Tools alkalmazást a Windows rendszerre. Ön is használja [Chocolatey](https://chocolatey.org/). További információt a [Core Tools című témakörben talál.](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)

1. Telepítse [a Node.js programot,]amely magában foglalja az npm-et.
    - Az eszközök 2.x-es verziójában csak a Node.js 8.5-ös és újabb verziói támogatottak.
    - Az eszközök 3.x-es verziójában csak a Node.js 10 és újabb verziók támogatottak.

1. Telepítse a Core Tools csomagot:

    ##### <a name="v2x"></a>2.x-es év után

    ```cmd
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>3.x-es a.

    ```cmd
    npm install -g azure-functions-core-tools@3
    ```

   Az npm-nek eltarthat néhány percig a Core Tools csomag letöltése és telepítése.

1. Ha nem tervezi a [bővítménycsomagok]használatát, telepítse a [.NET Core 2.x SDK for Windows](https://www.microsoft.com/net/download/windows)rendszert.

# <a name="macos"></a>[Macos](#tab/macos)

A következő lépések a Homebrew segítségével telepítheti a Core Tools alkalmazást macOS rendszeren.

1. Telepítse a [Homebrew](https://brew.sh/)alkalmazást, ha még nincs telepítve.

1. Telepítse a Core Tools csomagot:

    ##### <a name="v2x"></a>2.x-es év után

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>3.x-es a.

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

A következő lépések az [APT](https://wiki.debian.org/Apt) segítségével telepíthetik a Core Tools-t az Ubuntu/Debian Linux disztribúcióra. További Linux-disztribúciókról a [Core Tools című témakörben olvashat.](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux)

1. Telepítse a Microsoft csomagtár GPG kulcsát a csomag integritásának ellenőrzéséhez:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Az APT-frissítés előtt állítsa be a .NET fejlesztési forráslistát.

   Az Ubuntu APT forráslistájának beállításához futtassa ezt a parancsot:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   A Debian APT forráslistájának beállításához futtassa ezt a parancsot:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Ellenőrizze `/etc/apt/sources.list.d/dotnetdev.list` a fájlt az egyik megfelelő Linux verzió húrok alább felsorolt:

    | Linux disztribúció | Verzió |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Az APT forrásfrissítésének indítása:

    ```bash
    sudo apt-get update
    ```

1. Telepítse a Core Tools csomagot:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Ha nem tervezi a [bővítménycsomagok]használatát, telepítse [a .NET Core 2.x SDK for Linux programot.](https://www.microsoft.com/net/download/linux)

---

## <a name="create-a-local-functions-project"></a>Helyi Függvények projekt létrehozása

A függvényprojekt könyvtára tartalmazza a [host.json](functions-host-json.md) és a [local.settings.json](#local-settings-file)fájlokat, valamint az egyes függvények kódját tartalmazó almappákat. Ez a könyvtár az Azure-beli függvényalkalmazás nak felel meg. A Functions mappastruktúráról az [Azure Functions fejlesztői útmutatójában](functions-reference.md#folder-structure)olvashat bővebben.

A 2.x verzióban ki kell választania a projekt alapértelmezett nyelvét az inicializáláskor. A 2.x verzióban minden hozzáadott függvény alapértelmezett nyelvi sablonokat használ. Az 1.x verzióban a függvény ekként adja meg a nyelvet.

A terminálablakban vagy egy parancssorból futtassa a következő parancsot a projekt és a helyi Git-tárház létrehozásához:

```
func init MyFunctionProj
```

Amikor megad egy projektnevet, a rendszer egy új, ezzel a névvel rendelkező mappát hoz létre és inicializál. Ellenkező esetben az aktuális mappa inicializálásra kerül.  
A 2.x-es verzióban a parancs futtatásakor ki kell választania a projekt futásidejét. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

A fel/le nyílbillentyűkkel válasszon egy nyelvet, majd nyomja le az Enter billentyűt. Ha JavaScript- vagy TypeScript-függvényeket kíván fejleszteni, válassza a **csomópontot,** majd válassza ki a nyelvet. TypeScript van [néhány további követelmények](functions-reference-node.md#typescript). 

A kimenet a következő példához hasonlóan néz ki egy JavaScript-projektesetében:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init`a következő lehetőségeket támogatja, amelyek csak 2.x verziójúak, hacsak másként nem jelezzük:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | C# [osztálykönyvtár (.cs) projekt](functions-dotnet-class-library.md)inicializálása. |
| **`--csx`** | C# [parancsfájl (.csx) projekt](functions-reference-csharp.md)inicializálása. A következő `--csx` parancsokban meg kell adnia. |
| **`--docker`** | Hozzon létre egy Docker-fájlt egy tárolóhoz egy `--worker-runtime`alaprendszerkép segítségével, amely a kiválasztotton alapul. Akkor használja ezt a beállítást, ha egyéni Linux-tárolóban kíván közzétenni. |
| **`--docker-only`** |  Docker-fájlt ad hozzá egy meglévő projekthez. Kéri a dolgozó-runtime, ha nincs megadva, vagy be van állítva a local.settings.json. Akkor használja ezt a beállítást, ha egy meglévő projektet szeretne közzétenni egy egyéni Linux-tárolóban. |
| **`--force`** | A projekt inicializálása akkor is, ha a projektben már vannak fájlok. Ez a beállítás felülírja az azonos nevű meglévő fájlokat. A projektmappában lévő többi fájlra nincs hatással. |
| **`--java`**  | Java [projekt](functions-reference-java.md)inicializálása . |
| **`--javascript`**<br/>**`--node`**  | [JavaScript-projekt](functions-reference-node.md)inicializálása. |
| **`--no-source-control`**<br/>**`-n`** | Megakadályozza a Git-tárház alapértelmezett létrehozását az 1.x verzióban. A 2.x-es verzióban a git-tárház alapértelmezés szerint nem jön létre. |
| **`--powershell`**  | [Egy PowerShell-projekt](functions-reference-powershell.md)inicializálása. |
| **`--python`**  | [Python-projekt](functions-reference-python.md)inicializálása. |
| **`--source-control`** | Azt szabályozza, hogy létrejön-e git-tárház. Alapértelmezés szerint a tárház nem jön létre. Amikor `true`a rendszer létrehoz egy adattárat. |
| **`--typescript`**  | [TypeScript-projekt](functions-reference-node.md#typescript)inicializálása. |
| **`--worker-runtime`** | Beállítja a projekt nyelvi futásidejét. A támogatott `csharp`értékek `dotnet` `java`a `javascript``node` következők: , `powershell` `python`, `typescript`, , (JavaScript), , és . Ha nincs beállítva, a rendszer kéri, hogy az inicializálás során válassza ki a futásidejűt. |

> [!IMPORTANT]
> Alapértelmezés szerint a Core Tools 2.x verziója [c# osztályú projektekként](functions-dotnet-class-library.md) (.csproj) hozza létre a . Ezek a C# projektek, amelyek a Visual Studio vagy a Visual Studio-kód, a tesztelés során és az Azure-ban való közzététel során fordítása. Ha ehelyett az 1.x verzióban és a portálon létrehozott C# parancsfájlokkal (.csx) szeretné `--csx` létrehozni és használni, akkor a függvények létrehozásakor és telepítésekor meg kell adnia a paramétert.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem kerülnek automatikusan áttelepítésre, amikor a projekt et közzéteszik az Azure-ban. A `--publish-local-settings` kapcsoló [tegyék közzé,](#publish) győződjön meg arról, hogy ezek a beállítások hozzá adódnak a függvényalkalmazás az Azure-ban. Ne feledje, hogy a **ConnectionStrings értékei** soha nem kerülnek közzétételre.

A függvényalkalmazás beállításai értékek is olvasható a kódban, mint a környezeti változók. További információt a környezetváltozók című témakörben talál:

* [C# előre lefordítva](functions-dotnet-class-library.md#environment-variables)
* [C#-szkript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

Ha nincs érvényes tárolási kapcsolati karakterlánc beállítva, [`AzureWebJobsStorage`] és az emulátor nincs használatban, a következő hibaüzenet jelenik meg:

> Hiányzó érték az AzureWebJobsStorage a local.settings.json. Ez a HTTP-n kívüli összes eseményindítóesetében szükséges. Futtathatja a "func azure functionapp \<fetch-app-settings functionAppName"\>parancsot, vagy megadhat egy kapcsolati karakterláncot a local.settings.json ban.

### <a name="get-your-storage-connection-strings"></a>A tárolási kapcsolati karakterláncok beszerezni

Még akkor is, ha a Microsoft Azure Storage emulátor t használja a fejlesztéshez, érdemes lehet tényleges tárolási kapcsolaton keresztül tesztelni. Feltéve, hogy már [létrehozott egy tárfiókot,](../storage/common/storage-create-storage-account.md)az alábbi módokon kaphat érvényes tárolási kapcsolati karakterláncot:

- Az [Azure Portalon]keresse meg és válassza **a Storage-fiókok**lehetőséget. 
  ![Storage-fiókok kiválasztása az Azure Portalról](./media/functions-run-local/select-storage-accounts.png)
  
  Jelölje ki a tárfiókot, válassza **az Access kulcsok lehetőséget** a Beállítások **menüben,** majd másolja a **Kapcsolati karakterlánc** egyik értékét.
  ![Kapcsolati karakterlánc másolása az Azure Portalról](./media/functions-run-local/copy-storage-connection-portal.png)

- Az [Azure Storage Explorer](https://storageexplorer.com/) segítségével csatlakozhat Azure-fiókjához. Az **Intézőben**bontsa ki az előfizetést, bontsa ki a **Tárfiókok csomópontot,** válassza ki a tárfiókot, és másolja az elsődleges vagy másodlagos kapcsolati karakterláncot.

  ![Kapcsolati karakterlánc másolása a Storage Explorer programból](./media/functions-run-local/storage-explorer.png)

+ A Core Tools segítségével töltse le a kapcsolati karakterláncot az Azure-ból az alábbi parancsok egyikével:

  + Az összes beállítás letöltése egy meglévő függvényalkalmazásból:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + A kapcsolati karakterlánc beszereznie egy adott tárfiókhoz:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Ha még nincs bejelentkezve az Azure-ba, a rendszer erre kéri.

## <a name="create-a-function"></a><a name="create-func"></a>Függvény létrehozása

Függvény létrehozásához futtassa a következő parancsot:

```
func new
```

A 2.x-es verzióban `func new` a rendszer kéri, hogy válasszon ki egy sablont a függvényalkalmazás alapértelmezett nyelvén, majd a függvény nevet is meg kell választania. Az 1.x verzióban a rendszer a nyelv kiválasztását is kéri.

<pre>
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
</pre>

A függvénykód a megadott függvénynévvel rendelkező almappában jön létre, amint azt a következő várólista-eseményindító kimenet láthatja:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

Ezeket a beállításokat a parancsban az alábbi argumentumokkal is megadhatja:

| Argumentum     | Leírás                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (2.x verzió) Ugyanazokat a C# parancsfájlokat (.csx) hozza létre, amelyeket az 1.x verzióban és a portálon használnak. |
| **`--language`**, **`-l`**| A sablon programozási nyelve, például C#, F#vagy JavaScript. Ez a beállítás az 1.x verzióban szükséges. A 2.x-es verzióban ne használja ezt a beállítást, és ne válasszon olyan nyelvet, amely megfelel a dolgozói futásidőnek. |
| **`--name`**, **`-n`** | A függvény neve. |
| **`--template`**, **`-t`** | A `func templates list` paranccsal megtekintheti az egyes támogatott nyelvekhez rendelkezésre álló sablonok teljes listáját.   |

Ha például egyetlen parancsban szeretne JavaScript HTTP-eseményindítót létrehozni, futtassa a következőket:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Ha egy parancsban várólistával aktivált függvényt szeretne létrehozni, futtassa a következőket:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Függvények helyi futtatása

Functions projekt futtatásához futtassa a Functions állomást. A gazdagép engedélyezi az eseményindítókat a projekt összes függvényében. A start parancs a projekt nyelvétől függően változik.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```
# <a name="javascript"></a>[Javascript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Ezt a parancsot [virtuális környezetben](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv)kell futtatni.

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> A Functions runtime 1.x `host` verziója megköveteli a parancsot, mint a következő példában:
>
> ```
> func host start
> ```

`func start`a következő lehetőségeket támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Futás előtt ne építsen fel aktuális projektet. Csak dotnet projektekhez. Az alapértelmezett érték hamis. Az 1.x verzió esetén nem támogatott. |
| **`--cert`** | A személyes kulcsot tartalmazó .pfx fájl elérési útja. Csak a `--useHttps`programmal használható. Az 1.x verzió esetén nem támogatott. |
| **`--cors-credentials`** | A több eredetű hitelesített kérelmek (azaz a cookie-k és a hitelesítési fejléc) engedélyezése Nem támogatott az 1.x verziónál. |
| **`--cors`** | A CORS-eredet vesszővel tagolt listája szóközök nélkül. |
| **`--language-worker`** | Argumentumok a nyelvi dolgozó konfigurálásához. Engedélyezheti például a hibakeresést a nyelvi dolgozó számára [a hibakeresési port és más szükséges argumentumok megadásával.](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers) Az 1.x verzió esetén nem támogatott. |
| **`--nodeDebugPort`**, **`-n`** | A Node.js hibakereső által használandó port. Alapértelmezett: A launch.json vagy az 5858 érték értéke. Csak az 1.x verzió. |
| **`--password`** | A .pfx fájl jelszavát tartalmazó jelszó vagy fájl. Csak a `--cert`programmal használható. Az 1.x verzió esetén nem támogatott. |
| **`--port`**, **`-p`** | A helyi port hallgatni. Alapértelmezett érték: 7071. |
| **`--pause-on-error`** | A folyamatból való kilépés előtt szüneteltetheti a további bevitelt. Csak akkor használatos, ha a Core Tools-t integrált fejlesztői környezetből (IDE) indítja el.|
| **`--script-root`**, **`--prefix`** | A futtatandó vagy üzembe helyezendő függvényalkalmazás gyökérelérési útjának megadására szolgál. Ez olyan lefordított projektekhez használatos, amelyek projektfájlokat hoznak létre almappába. Ha például C# osztálykönyvtár-projektet hoz létre, a host.json, a local.settings.json és a function.json `MyProject/bin/Debug/netstandard2.0`fájlok egy gyökéralmappában jönnek létre, amelynek elérési útja a . *root* Ebben az esetben állítsa be `--script-root MyProject/bin/Debug/netstandard2.0`az előtagot . Ez a függvényalkalmazás gyökere az Azure-ban való futtatáskor. |
| **`--timeout`**, **`-t`** | A Functions állomás indításának időmeghosszabbítása másodpercben. Alapértelmezett: 20 másodperc.|
| **`--useHttps`** | Kötődik, `https://localhost:{port}` nem `http://localhost:{port}`pedig . Ez a beállítás alapértelmezés szerint megbízható tanúsítványt hoz létre a számítógépen.|

Amikor a Functions állomás elindul, a HTTP által aktivált függvények URL-címét adja ki:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Helyi futtatásesetén az engedélyezés nem kényszeríti ki a HTTP-végpontokat. Ez azt jelenti, hogy az `authLevel = "anonymous"`összes helyi HTTP-kérelem kezelése a. További információt a [HTTP-kötési cikkben](functions-bindings-http-webhook-trigger.md#authorization-keys)talál.

### <a name="passing-test-data-to-a-function"></a>Vizsgálati adatok átadása egy függvénynek

A függvények helyi teszteléséhez [indítsa el a Functions gazdagépét,](#start) és hívja meg a végpontokat a helyi kiszolgálón HTTP-kérelmek használatával. A hívott végpont a függvény típusától függ.

>[!NOTE]
> A jelen témakörben szereplő példák a cURL eszközzel http-kéréseket küldhetnek a terminálról vagy a parancssorból. A http-kérelmek et a helyi kiszolgálónak küldheti el. A cURL eszköz alapértelmezés szerint linuxos rendszereken és Windows 10 build 17063-as és újabb rendszereken érhető el. Régebbi Windows rendszerben először le kell töltenie és telepítenie kell a [cURL eszközt](https://curl.haxx.se/).

A tesztelési funkciókkal kapcsolatos általánosabb információkért lásd: [Stratégiák a kód teszteléséhez az Azure Functionsben című témakörben.](functions-test-a-function.md)

#### <a name="http-and-webhook-triggered-functions"></a>HTTP és webhook aktivált függvények

A következő végpontot a HTTP és a webhook által aktivált függvények helyi futtatására hívja meg:

    http://localhost:{port}/api/{function_name}

Ügyeljen arra, hogy ugyanazt a kiszolgálónevet és portot használja, amelyet a Functions állomás figyel. Ez a függvénygazda indításakor létrehozott kimenetben jelenik meg. Ezt az URL-címet az eseményindító által támogatott bármely HTTP-módszerrel meghívhatja.

A következő cURL parancs `MyHttpTrigger` elindítja a gyorsindítási függvényt egy GET-kérelemből, amelynek a _lekérdezési_ karakterláncban átadott névparamétere átadott.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

A következő példa ugyanaz a függvény, amelyet a kérelem törzsében a POST-kérelem átadása _nevéből_ hívnak meg:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

Get kéréseket tehet egy böngészőtől, amely adatokat ad át a lekérdezési karakterláncban. Minden más HTTP-módszer esetén cURL, Fiddler, Postman vagy hasonló HTTP-tesztkészítő eszközt kell használnia.

#### <a name="non-http-triggered-functions"></a>Nem HTTP-vel aktivált függvények

A HTTP-eseményindítóktól és webhookoktól és az Event Grid-eseményindítóktól eltérő funkciók esetében helyileg tesztelheti a függvényeket egy felügyeleti végpont hívásával. A végpont hívása http posta kéréssel a helyi kiszolgálón elindítja a funkciót. 

Az Event Grid által aktivált függvények helyi teszteléséről a [Helyi tesztelés a megjelenítő webalkalmazásával című témakörben](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)talál.

A tesztadatokat tetszés szerint átadhatja a végrehajtásnak a POST-kérelem törzsében. Ez a funkció hasonló az Azure Portal **Teszt** lapjához.

A nem HTTP-függvények indításához a következő rendszergazdai végpontot hívja meg:

    http://localhost:{port}/admin/functions/{function_name}

A tesztadatok nak egy függvény rendszergazdai végpontjának való átadására a POST-kérelem üzenet törzsében lévő adatokat kell megadnia. Az üzenet törzse a következő JSON formátummal rendelkezik:

```JSON
{
    "input": "<trigger_input>"
}
```

Az `<trigger_input>` érték a függvény által várt formátumú adatokat tartalmaz. A következő cURL példa egy `QueueTriggerJS` POST egy függvényhez. Ebben az esetben a bemenet egy karakterlánc, amely megegyezik a várólistában várhatóüzenettel.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>A `func run` parancs használata (csak az 1.x verziónál)

>[!IMPORTANT]
> A `func run` parancsot csak az eszközök 1.x verziója támogatja. További információt az [Azure Functions futásidejű verzióinak célzása](set-runtime-version.md)című témakörben talál.

Az 1.x-es verzióban közvetlenül is `func run <FunctionName>` meghívhat egy függvényt a függvény használatával, és bemeneti adatokat adhat meg. Ez a parancs hasonló egy függvény futtatásához az Azure Portal **Teszt** lapján.

`func run`a következő lehetőségeket támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Szövegközi tartalom. |
| **`--debug`**, **`-d`** | A függvény futtatása előtt csatoljon egy hibakeresőt a gazdafolyamathoz.|
| **`--timeout`**, **`-t`** | Várakozási idő (másodpercben), amíg a helyi Függvények állomás készen áll.|
| **`--file`**, **`-f`** | A tartalomként használandó fájlnév.|
| **`--no-interactive`** | Nem kér rá bemenetre. Az automatizálási forgatókönyvekhez hasznos.|

Ha például meg szeretne hívni egy HTTP-aktivált függvényt, és át szeretne adni egy tartalomtörzset, futtassa a következő parancsot:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Közzététel az Azure platformon

Az Azure Functions Core Tools kétféle üzembe helyezést támogat: a függvényprojekt-fájlok közvetlenül a függvényalkalmazásba való üzembe helyezése a [Zip Deploy](functions-deployment-technologies.md#zip-deploy) szolgáltatáson keresztül, és [egy egyéni Docker-tároló telepítése.](functions-deployment-technologies.md#docker-container) Már létre kell [hoznia egy függvényalkalmazást az Azure-előfizetésében,](functions-cli-samples.md#create)amelyre telepíti a kódot. Az összeállítást igénylő projekteket úgy kell kialakítani, hogy a bináris fájlok at lehessen telepíteni.

>[!IMPORTANT]
>Az Azure [CLI](/cli/azure/install-azure-cli) helyileg telepítve kell lennie ahhoz, hogy közzé tehethető az Azure-ban a Core Tools-ból.  

A projektmappák tartalmazhatnak olyan nyelvspecifikus fájlokat és könyvtárakat, amelyeket nem szabad közzétenni. A kizárt elemek egy .funcignore fájlban jelennek meg a gyökérprojekt mappájában.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Projektfájlok telepítése

A helyi kód közzététele egy függvényalkalmazásban az `publish` Azure-ban, használja a parancsot:

```
func azure functionapp publish <FunctionAppName>
```

Ez a parancs egy meglévő függvényalkalmazásban tesz közzé az Azure-ban. Hibaüzenetet kap, ha olyan helyen próbál `<FunctionAppName>` közzétenni, amely nem létezik az előfizetésében. Ha meg szeretné tudni, hogyan hozhat létre függvényalkalmazást a parancssorból vagy a terminálablakból az Azure CLI használatával, olvassa [el a Függvényalkalmazás létrehozása kiszolgáló nélküli végrehajtáshoz](./scripts/functions-cli-create-serverless.md)című témakört. Alapértelmezés szerint ez a parancs [távoli buildet](functions-deployment-technologies.md#remote-build) használ, és az alkalmazást [a központi telepítési csomagból futtatja.](run-functions-from-deployment-package.md) Az ajánlott telepítési mód letiltásához használja ezt a `--nozip` lehetőséget.

>[!IMPORTANT]
> Amikor létrehoz egy függvényalkalmazást az Azure Portalon, alapértelmezés szerint a függvény futásidejű 2.x-es verzióját használja. Ahhoz, hogy a függvényalkalmazás a futásidejű 1.x verzióját használja, kövesse a [Futtatás az 1.x verzióban című](functions-versions.md#creating-1x-apps)részben található utasításokat.
> Meglévő függvényeket tartalmazó függvényalkalmazás futásidejű verziója nem módosítható.

Mindkét verzióra a következő közzétételi lehetőségek érvényesek, az 1.x és a 2.x verzióra:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  A local.settings.json webhelyen közzéteheti a beállításokat az Azure-ban, és felülírja, ha a beállítás már létezik. Ha a Microsoft Azure Storage-emulátort használja, először módosítsa az alkalmazásbeállítást [tényleges tárolási kapcsolatra.](#get-your-storage-connection-strings) |
| **`--overwrite-settings -y`** | Letiltása az alkalmazásbeállítások `--publish-local-settings -i` felülírására irányuló kérdés letiltására.|

A következő közzétételi beállításokat csak a 2.x verzió támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Csak tegye közzé a beállításokat, és hagyja ki a tartalmat. Az alapértelmezett a parancs. |
|**`--list-ignored-files`** | Megjeleníti azoknak a fájloknak a listáját, amelyeket a közzététel során figyelmen kívül hagy, és amely a .funcignore fájlon alapul. |
| **`--list-included-files`** | A közzétett fájlok listáját jeleníti meg, amely a .funcignore fájlon alapul. |
| **`--nozip`** | Kikapcsolja `Run-From-Package` az alapértelmezett módot. |
| **`--build-native-deps`** | Kihagyja a .wheels mappa generálását a Python függvényalkalmazások közzétételekor. |
| **`--build`**, **`-b`** | Buildelési műveletet hajt végre, ha egy Linux függvényalkalmazásba telepíti. Elfogadja: `remote` és `local`. |
| **`--additional-packages`** | Natív függőségek létrehozásakor telepítandó csomagok listája. Például: `python3-dev libevent-dev`. |
| **`--force`** | Bizonyos esetekben figyelmen kívül hagyja a közzététel előtti ellenőrzést. |
| **`--csx`** | C# parancsfájl (.csx) projekt közzététele. |
| **`--no-build`** | Ne hozzon létre .NET osztálykönyvtár-függvényeket. |
| **`--dotnet-cli-params`** | A lefordított C# (.csproj) függvények közzétételekor az alapvető eszközök a "dotnet build --output bin/publish" (dotnet build --output bin/publish' (dotnet build --output bin/publish' (dotnet build --output bin/publish' (dotnet build --output bin/publish' (dotnet build --output bin/publish' (pont) hívások Az ehhez átadott paraméterek a parancssorba lesznek csatolva. |

### <a name="deploy-custom-container"></a>Egyéni tároló telepítése

Az Azure Functions lehetővé teszi a függvényprojekt üzembe helyezését egy [egyéni Docker-tárolóban.](functions-deployment-technologies.md#docker-container) További információ: [Függvény létrehozása Linuxon egyéni lemezkép használatával.](functions-create-function-linux-custom-image.md) Az egyéni tárolóknak Docker-fájllal kell rendelkezniük. Ha docker-fájllal szeretne alkalmazást létrehozni, használja a `func init`--dockerfile kapcsolót a területen.

```
func deploy
```

A következő egyéni tárolótelepítési lehetőségek érhetők el:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Annak a Docker-beállításjegyzéknek a neve, amelybe az aktuális felhasználó bejelentkezett. |
| **`--platform`** | A függvényalkalmazás üzemeltetési platformja. Az érvényes beállítások`kubernetes` |
| **`--name`** | Függvényalkalmazás neve. |
| **`--max`**  | Szükség esetén beállítja a telepíteni telepíthető függvényalkalmazás-példányok maximális számát. |
| **`--min`**  | Szükség esetén beállítja a minimális számú függvényalkalmazás-példányok üzembe helyezéséhez. |
| **`--config`** | Beállít egy választható központi telepítési konfigurációs fájlt. |

## <a name="monitoring-functions"></a>Felügyeleti funkciók

A függvények végrehajtásának figyelésének ajánlott módja az Azure Application Insights integrálása. Végrehajtási naplókat a helyi számítógépre is streamelhet. További információ: [Monitor Azure Functions](functions-monitoring.md).

### <a name="application-insights-integration"></a>Az Application Insights integrációja

Az Application Insights-integrációt engedélyezni kell, amikor létrehozza a függvényalkalmazást az Azure-ban. Ha valamilyen okból a függvényalkalmazás nem csatlakozik egy Application Insights-példányhoz, ezt az integrációt egyszerűen ellehet végezni az Azure Portalon. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Streamelési naplók engedélyezése

A függvények által létrehozott naplófájlok adatfolyamát a helyi számítógépen lévő parancssori munkamenetben tekintheti meg. 

#### <a name="native-streaming-logs"></a>Natív streamelési naplók

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Az ilyen típusú streamelési naplók megköveteli, hogy az Application Insights-integráció engedélyezve legyen a függvényalkalmazáshoz.   


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan fejlesztheti, tesztelheti és teheti közzé az Azure Functions Core Tools segítségével, [a Microsoft megtanulja](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) az Azure Functions Core Tools [nyílt forráskódú és a GitHubon üzemeltetett](https://github.com/azure/azure-functions-cli)modult.  
Hiba vagy szolgáltatáskérés benyújtásához [nyisson meg egy GitHub-problémát.](https://github.com/azure/azure-functions-cli/issues)

<!-- LINKS -->

[Az Azure Functions alapvető eszközei]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portál]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
["AzureWebJobsStorage"]: functions-app-settings.md#azurewebjobsstorage
[bővítőcsomagok]: functions-bindings-register.md#extension-bundles
