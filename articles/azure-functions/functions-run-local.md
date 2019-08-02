---
title: A Azure Functions Core Tools használata | Microsoft Docs
description: Megtudhatja, hogyan teheti meg az Azure functions szolgáltatást a parancssorból vagy a terminálból a helyi számítógépen, mielőtt futtatja őket a Azure Functionson.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: glenga
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: cee91de9e0a5d75be258705f636248a6a6213664
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444055"
---
# <a name="work-with-azure-functions-core-tools"></a>Azure Functions Core Tools használata

Azure Functions Core Tools lehetővé teszi a függvények fejlesztését és tesztelését a helyi számítógépen a parancssorból vagy a terminálból. A helyi függvények csatlakozhatnak az élő Azure-szolgáltatásokhoz, és a függvények a teljes functions Runtime használatával hibakeresést végezhetnek a helyi számítógépen. Az Azure-előfizetésben is üzembe helyezhet egy Function alkalmazást.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

A functions a helyi számítógépen való fejlesztése és az Azure-ba való közzétételük az alapeszközök használatával az alábbi alapvető lépéseket követi:

> [!div class="checklist"]
> * [Telepítse az alapvető eszközöket és a függőségeket.](#v2)
> * [Function app-projekt létrehozása nyelvspecifikus sablonból.](#create-a-local-functions-project)
> * [Trigger-és kötési bővítmények regisztrálása.](#register-extensions)
> * [Tároló és egyéb kapcsolatok definiálása.](#local-settings-file)
> * [Hozzon létre egy függvényt egy triggerből és egy nyelvspecifikus sablonból.](#create-func)
> * [A függvény helyi futtatása](#start)
> * [A projekt közzététele az Azure-ban](#publish)

## <a name="core-tools-versions"></a>Alapvető eszközök verziói

A Azure Functions Core Tools két verziója létezik. A használt verzió a helyi fejlesztési környezettől, a [választott nyelvtől](supported-languages.md)és a szükséges támogatási szinttől függ:

+ 1\. x verzió: a futtatókörnyezet 1. x verzióját támogatja. Az eszközök ezen verziója csak Windows rendszerű számítógépeken támogatott, és egy NPM- [csomagból](https://docs.npmjs.com/getting-started/what-is-npm)van telepítve. Ezzel a verzióval a nem hivatalosan támogatott kísérleti nyelveken hozhat létre függvényeket. További információ: [Azure functions támogatott nyelvei](supported-languages.md)

+ [2. x verzió](#v2): [a futtatókörnyezet 2. x verzióját](functions-versions.md)támogatja. Ez a verzió támogatja a Windows, a [MacOS](#brew)és a [Linux](#linux) [rendszert](#windows-npm). Platform-specifikus csomagkezelő vagy NPM használata a telepítéshez.

Ha másként nincs jelezve, a cikkben szereplő példák a 2. x verzióra vonatkoznak.

## <a name="install-the-azure-functions-core-tools"></a>Az Azure Functions Core Tools telepítése

[Azure functions Core Tools] tartalmaz egy olyan verziót, amely a helyi fejlesztési számítógépen futtatható Azure functions futtatókörnyezetet is felhasználja. Emellett parancsokat is biztosít a függvények létrehozásához, az Azure-hoz való kapcsolódáshoz és a functions-projektek üzembe helyezéséhez.

### <a name="v2"></a>2-es verzió. x

Az eszközök 2. x verziója a .NET Core-ra épülő Azure Functions Runtime 2. x verziót használja. Ez a verzió a .NET Core 2. x összes platformján támogatott, beleértve a Windows, a [MacOS](#brew)és a [Linux](#linux) [rendszert](#windows-npm)is. 

> [!IMPORTANT]
> A .NET Core 2. x SDK a bővítmények használatával történő telepítésének követelményét []kihagyhatja.

#### <a name="windows-npm"></a>Windows

A következő lépések a NPM segítségével telepítik a Windows rendszerhez tartozó alapvető eszközöket. A csokit is [](https://chocolatey.org/)használhatja. További információ: [alapvető eszközök – fontos](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)információk.

1. Telepítse a [Node.js]-t, amely tartalmazza a NPM. Az eszközök 2. x verziója esetében csak a Node. js 8,5-es és újabb verziói támogatottak.

1. Telepítse a Core Tools csomagot:

    ```bash
    npm install -g azure-functions-core-tools
    ```

   A NPM letöltése és telepítése eltarthat néhány percig.

1. Ha nem tervezi a bővítmények használatát [], telepítse a Windowshoz készült [.net Core 2. x SDK](https://www.microsoft.com/net/download/windows)-t.

#### <a name="brew"></a>MacOS és Homebrew

A következő lépések a Homebrew-t használják a fő eszközök macOS rendszeren való telepítéséhez.

1. Telepítse a [Homebrew](https://brew.sh/)-t, ha még nincs telepítve.

1. Telepítse a Core Tools csomagot:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

1. Ha nem tervezi a bővítmények használatát [], telepítse [a .net Core 2. x SDK-t a MacOS rendszerhez](https://www.microsoft.com/net/download/macos).


#### <a name="linux"></a>Linux (Ubuntu/Debian) APT-vel

Az alábbi [lépések segítségével](https://wiki.debian.org/Apt) telepítheti az alapvető eszközöket az Ubuntu/Debian Linux-disztribúción. Más Linux-disztribúciók esetében tekintse meg az [alapvető eszközök readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux)című témakört.

1. Regisztrálja megbízhatóként a Microsoft-termékkulcsot:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Győződjön meg arról, hogy az Ubuntu-kiszolgáló az alábbi táblázatban található megfelelő verziók egyikét futtatja. Az apt-forrás hozzáadásához futtassa a következő parancsot:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Linux-disztribúció | Version |
    | --------------- | ----------- |
    | Ubuntu 18,10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17,04    | `zesty`     |
    | Ubuntu 16.04/Linux mint 18    | `xenial`  |

1. Telepítse a Core Tools csomagot:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Ha nem tervezi a bővítmények használatát [], telepítse [a .net Core 2. x SDK-t a Linux rendszerhez](https://www.microsoft.com/net/download/linux).

## <a name="create-a-local-functions-project"></a>Egy helyi Functions-projekt létrehozása

A functions projekt könyvtára tartalmazza a [Host. JSON](functions-host-json.md) és a [Local. Settings. JSON](#local-settings-file)fájlt, valamint az egyes függvények kódját tartalmazó almappákat. Ez a könyvtár egyenértékű egy Azure-beli Function alkalmazással. A functions mappa struktúrájával kapcsolatos további tudnivalókért tekintse meg a [Azure functions fejlesztői útmutató](functions-reference.md#folder-structure)című témakört.

A 2. x verzióhoz az inicializáláskor ki kell választania a projekt alapértelmezett nyelvét, és az összes funkció hozzáadásra kerül az alapértelmezett nyelvi sablonok használatával. Az 1. x verzióban az egyes függvények létrehozásakor minden alkalommal meg kell adnia a nyelvet.

A terminál ablakban vagy egy parancssorban futtassa a következő parancsot a projekt és a helyi git-tárház létrehozásához:

```bash
func init MyFunctionProj
```

Amikor megadja a projekt nevét, létrejön egy új, a névvel ellátott mappa, amely inicializálva van. Ellenkező esetben az aktuális mappa inicializálása megtörtént.  
A 2. x verzióban a parancs futtatásakor ki kell választania egy futtatókörnyezetet a projekthez. Ha a JavaScript-függvények fejlesztését tervezi, válassza a **csomópont**:

```output
Select a worker runtime:
dotnet
node
```

A fel/le nyílbillentyűk használatával válassza ki a nyelvet, majd nyomja le az ENTER billentyűt. A kimenet a következő példához hasonlít egy JavaScript-projekthez:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init`a a következő beállításokat támogatja, amelyek csak a 2. x verziójúak, hacsak másként nincs jelezve:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Egy C# parancsfájl-(. CSX) projekt inicializálása. A következő parancsokban meg kell adni `--csx` . |
| **`--docker`** | Hozzon létre egy Docker a tárolóhoz a kiválasztott `--worker-runtime`alaprendszerkép használatával. Akkor használja ezt a beállítást, ha egyéni Linux-tárolóba szeretne közzétenni. |
| **`--force`** | A projekt inicializálása akkor is, ha vannak meglévő fájlok a projektben. Ez a beállítás felülírja a meglévő fájlokat ugyanazzal a névvel. A Project mappában található egyéb fájlok nem érintettek. |
| **`--no-source-control -n`** | Megakadályozza a git-tárház alapértelmezett létrehozását az 1. x verzióban. A 2. x verzióban a git-tárház alapértelmezés szerint nincs létrehozva. |
| **`--source-control`** | Meghatározza, hogy a git-tárház létrejött-e. Alapértelmezés szerint a tárház nem jön létre. Amikor `true`létrejön egy adattár. |
| **`--worker-runtime`** | Beállítja a projekt nyelvi futtatókörnyezetét. A támogatott értékek `dotnet`a `node` következők: (JavaScript `java`), `python`és. Ha nincs beállítva, a rendszer arra kéri, hogy válassza ki a futtatókörnyezetet az inicializálás során. |

> [!IMPORTANT]
> Alapértelmezés szerint a Core Tools 2. x verziója a .net futtatókörnyezethez [ C# (.](functions-dotnet-class-library.md) csproj) hoz létre Function app-projekteket. A C# Visual Studióval vagy a Visual Studio Code-hoz használható projektek a tesztelés során és az Azure-ba való közzététel során fordíthatók le. Ha ehelyett az 1. x verzióban és a portálon létrehozott ugyanazon C# parancsfájl-(. CSX) fájlokat szeretné létrehozni és használni, akkor a függvények létrehozásakor és `--csx` telepítésekor meg kell adnia a paramétert is.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem települnek át automatikusan, ha a projekt közzé van téve az Azure-ban. A közzétételkor használja a `--publish-local-settings` kapcsolót, és győződjön [meg](#publish) arról, hogy ezek a beállítások bekerülnek az Azure-beli Function alkalmazásba. Vegye figyelembe, hogy a **ConnectionStrings** lévő értékek soha nem lesznek közzétéve.

A Function app Settings értékeit környezeti változókként is beolvashatja a kódban. További információkért tekintse meg az alábbi nyelvspecifikus témakörök környezeti változók című szakaszát:

* [C#előfordított](functions-dotnet-class-library.md#environment-variables)
* [C# script (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Ha nincs beállítva érvényes tárolási kapcsolódási karakterlánc, [`AzureWebJobsStorage`] és az emulátor nincs használatban, a következő hibaüzenet jelenik meg:

> A AzureWebJobsStorage hiányzó értéke a local. Settings. JSON fájlban. Ez a HTTP-n kívüli összes eseményindító esetében kötelező. Futtathatja a "funkció Azure functionapp Fetch-app- \<Settings\>functionAppName" parancsot, vagy megadhat egy kapcsolatok karakterláncot a local. Settings. JSON fájlban.

### <a name="get-your-storage-connection-strings"></a>A tárolási kapcsolatok karakterláncának beolvasása

Még ha a Storage emulatort is szeretné használni a fejlesztéshez, érdemes lehet egy tényleges tárolási kapcsolatban is tesztelni. Feltételezve, hogy már [létrehozott egy Storage-fiókot](../storage/common/storage-create-storage-account.md), a következő módokon szerezhet be érvényes tárolási kapcsolatok karakterláncot:

+ A [Azure Portal]. Navigáljon a Storage-fiókjához, válassza a **hozzáférési kulcsok** lehetőséget a **Beállítások**területen, majd másolja a **kapcsolati karakterlánc** egyik értékét.

  ![A Azure Portalból származó kapcsolatok karakterláncának másolása](./media/functions-run-local/copy-storage-connection-portal.png)

+ Az Azure-fiókhoz való kapcsolódáshoz használjon [Azure Storage Explorer](https://storageexplorer.com/) . Az **Explorerben**bontsa ki az előfizetését, válassza ki a Storage-fiókját, és másolja az elsődleges vagy másodlagos kapcsolatok sztringjét.

  ![A Storage Explorerból származó kapcsolatok karakterláncának másolása](./media/functions-run-local/storage-explorer.png)

+ Az alábbi parancsok egyikével töltheti le a kapcsolódási karakterláncot az Azure-ból a Core Tools használatával:

  + Egy meglévő Function alkalmazás összes beállításának letöltése:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Egy adott Storage-fiókhoz tartozó kapcsolatok karakterláncának beolvasása:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Ha még nem jelentkezett be az Azure-ba, a rendszer felszólítja erre.

## <a name="create-func"></a>Függvény létrehozása

Hozzon létre egy függvényt, futtassa a következő parancsot:

```bash
func new
```

A 2. x verzióban a futtatáskor `func new` a rendszer kéri, hogy válasszon egy sablont a Function app alapértelmezett nyelvén, majd a rendszer kéri, hogy válasszon egy nevet a függvénynek. Az 1. x verzióban a rendszer azt is kéri, hogy válassza ki a nyelvet.

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

A függvény kódja a megadott nevű almappában jön létre, ahogy az a következő üzenetsor-trigger kimenetében látható:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Ezeket a beállításokat a paranccsal is megadhatja a következő argumentumok használatával:

| Argumentum     | Leírás                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (2. x verzió) Ugyanazokat C# a szkripteket (. CSX) hozza létre, amelyek az 1. x verzióban és a portálon használatosak. |
| **`--language -l`**| A sablon programozási nyelve, például C#, F#, vagy JavaScript. Ez a beállítás az 1. x verzióban szükséges. A 2. x verzióban ne használja ezt a kapcsolót, vagy válasszon olyan nyelvet, amely megfelel a munkavégző futtatókörnyezetnek. |
| **`--name -n`** | A függvény neve. |
| **`--template -t`** | A `func templates list` parancs használatával megtekintheti az elérhető sablonok teljes listáját az egyes támogatott nyelvekhez.   |

Ha például JavaScript HTTP-triggert szeretne létrehozni egyetlen parancsban, futtassa a következőt:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Ha üzenetsor által aktivált függvényt szeretne létrehozni egyetlen parancsban, futtassa a következőt:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Függvények helyi futtatása

Functions-projekt futtatásához futtassa a functions gazdagépet. A gazdagép a projekt összes funkciója számára engedélyezi az eseményindítókat:

```bash
func host start
```

A `host` parancs csak az 1. x verzióban szükséges.

`func host start`a a következő lehetőségeket támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | A Futtatás előtt ne hozzon létre aktuális projektet. Csak a DotNet-projektekhez. Az alapértelmezett érték false (hamis). Csak 2. x verzió. |
| **`--cert`** | A titkos kulcsot tartalmazó. pfx-fájl elérési útja. Csak a `--useHttps`alkalmazásban használatos. Csak 2. x verzió. |
| **`--cors-credentials`** | A csak a cookie-k és a hitelesítési fejlécek használatának engedélyezése csak a 2. x verzióban. |
| **`--cors`** | A CORS-eredetek vesszővel tagolt listája, szóközök nélkül. |
| **`--language-worker`** | A nyelv feldolgozójának konfigurálásához szükséges argumentumok. Csak 2. x verzió. |
| **`--nodeDebugPort -n`** | A csomópont-hibakereső által használandó port. Alapértelmezett: A Launch. JSON vagy a 5858 érték. Csak 1. x verzió. |
| **`--password`** | A jelszó vagy egy olyan fájl, amely egy. pfx fájl jelszavát tartalmazza. Csak a `--cert`alkalmazásban használatos. Csak 2. x verzió. |
| **`--port -p`** | A figyelni kívánt helyi port. Alapértelmezett érték: 7071. |
| **`--pause-on-error`** | A folyamat bezárása előtt szüneteltesse a további adatokat. Csak akkor használható, ha egy integrált fejlesztői környezetből (IDE) indít alapszintű eszközöket.|
| **`--script-root --prefix`** | A futtatni vagy telepíteni kívánt Function alkalmazás gyökeréhez tartozó elérési út megadására szolgál. Ez olyan lefordított projektek esetében használatos, amelyek a projektfájlok almappában hozhatók elő. Ha például létrehoz egy C# Class Library-projektet, a Host. JSON, a local. Settings. JSON és a function. JSON fájlok egy olyan *gyökérkönyvtárban* jönnek létre, amelynek elérési `MyProject/bin/Debug/netstandard2.0`útja hasonló. Ebben az esetben állítsa az előtagot `--script-root MyProject/bin/Debug/netstandard2.0`a következőre:. Ez a Function alkalmazás gyökere, ha az Azure-ban fut. |
| **`--timeout -t`** | A függvények gazdagépének időtúllépése másodpercben. Alapértelmezett: 20 másodperc.|
| **`--useHttps`** | Kötés helyett a következőhöz `http://localhost:{port}`: `https://localhost:{port}` . Alapértelmezés szerint ez a beállítás megbízható tanúsítványt hoz létre a számítógépen.|

Az C# osztályazonosítók (. csproj) esetében meg kell `--build` adnia a Library. dll előállításának lehetőségét.

A functions-gazdagép indításakor a HTTP-triggert függvények URL-címét adja meg:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>A helyileg futtatott hitelesítés nem kényszeríti ki a HTTP-végpontokat. Ez azt jelenti, hogy az összes helyi HTTP- `authLevel = "anonymous"`kérelem a következőképpen van kezelve:. További információkért lásd a http- [kötést ismertető cikket](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Tesztelési adat átadása egy függvénynek

A függvények helyi teszteléséhez [indítsa el a functions gazdagépet](#start) , és hívja meg a végpontokat a helyi kiszolgálón a HTTP-kérelmek használatával. A hívott végpont a függvény típusától függ.

>[!NOTE]
> A jelen témakörben szereplő példák a cURL eszközzel küldenek HTTP-kéréseket a terminálról vagy egy parancssorból. Az Ön által választott eszköz használatával HTTP-kéréseket küldhet a helyi kiszolgálónak. A cURL eszköz alapértelmezés szerint a Linux-alapú rendszereken érhető el. Windows rendszeren először le kell töltenie és telepítenie kell a [curl eszközt](https://curl.haxx.se/).

A tesztelési funkciókkal kapcsolatos általánosabb információkért lásd: [stratégiák a kód teszteléséhez Azure functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>A HTTP és a webhook által aktivált függvények

A következő végpontot hívja meg helyileg a HTTP és a webhook által aktivált függvények futtatásához:

    http://localhost:{port}/api/{function_name}

Győződjön meg arról, hogy ugyanazt a kiszolgálónevet és portot használja, amelyet a functions-gazdagép figyel. Ez a függvény gazdagépének indításakor generált kimenetben jelenik meg. Ezt az URL-címet bármely, az trigger által támogatott HTTP-módszerrel meghívhatja.

A következő curl-parancs elindítja `MyHttpTrigger` a gyors üzembe helyezési függvényt egy Get kérelemből a lekérdezési karakterláncban átadott _Name_ paraméterrel.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

A következő példa ugyanazt a függvényt _hívja meg a_ kérelem törzsében a post kérelem átadásakor:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

A lekérdezési karakterláncban található adatok beolvasása egy böngészőből is elvégezhető. Minden más HTTP-módszer esetében a cURL, a Hegedűs, a Poster vagy egy hasonló HTTP-tesztelési eszközt kell használnia.

#### <a name="non-http-triggered-functions"></a>Nem HTTP által aktivált függvények

A HTTP-triggereken és webhookokon kívüli egyéb függvények esetében az adminisztrációs végpont meghívásával helyileg is tesztelheti a függvényeket. Ha ezt a végpontot egy HTTP POST-kérelemmel hívja meg a helyi kiszolgálón, a függvényt indítja el. Szükség esetén a POST kérelem törzsében is elvégezheti a tesztelési célú adatellenőrzést. Ez a funkció hasonló a Azure Portal **teszt** lapjához.

A nem HTTP-függvények elindításához a következő rendszergazdai végpontot kell meghívni:

    http://localhost:{port}/admin/functions/{function_name}

Ahhoz, hogy egy függvény rendszergazdai végpontján át lehessen adni a tesztelési feladatokat, meg kell adnia a POST kérelem üzenet törzsében lévő összes adatát. Az üzenet törzsének a következő JSON-formátummal kell rendelkeznie:

```JSON
{
    "input": "<trigger_input>"
}
```

Az `<trigger_input>` érték a függvény által várt formátumú értékeket tartalmaz. A következő curl-példa egy `QueueTriggerJS` függvényre való bejegyzés. Ebben az esetben a bemenet egy karakterlánc, amely egyenértékű a várólistában várható üzenettel.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>A parancs `func run` használata az 1. x verzióban

>[!IMPORTANT]
> A `func run` parancs nem támogatott az eszközök 2. x verziójában. További információ: [Azure functions futtatókörnyezet verzióinak](set-runtime-version.md)megcélzása.

A függvényt közvetlenül `func run <FunctionName>` is meghívhatja, és a függvényhez bemeneti adatokat is megadhat. Ez a parancs hasonló egy függvény futtatásához a Azure Portal **teszt** lapján.

`func run`a a következő lehetőségeket támogatja:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Beágyazott tartalom. |
| **`--debug -d`** | A függvény futtatása előtt csatoljon egy hibakeresőt a gazdagéphez.|
| **`--timeout -t`** | Várakozási idő (másodpercben), amíg a helyi függvények gazdagépe nem áll készen.|
| **`--file -f`** | A tartalomként használandó fájlnév.|
| **`--no-interactive`** | A nem kéri a bevitelt. Automatizálási forgatókönyvek esetén hasznos.|

Ha például egy HTTP-triggert használó függvényt szeretne meghívni, és tartalmat kell átadnia, futtassa a következő parancsot:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Közzététel az Azure-ban

A Azure Functions Core Tools kétféle telepítési típust támogat: a Function-projektfájlok közvetlen üzembe helyezése a Function alkalmazásban a [zip üzembe helyezésével](functions-deployment-technologies.md#zip-deploy) és az [Egyéni Docker-tároló üzembe helyezésével](functions-deployment-technologies.md#docker-container). Már létre kell hoznia [egy Function alkalmazást az Azure-](functions-cli-samples.md#create)előfizetésében, amelyre telepíteni fogja a kódot. A fordítást igénylő projekteket úgy kell felépíteni, hogy a bináris fájlok üzembe helyezhetők legyenek.

### <a name="project-file-deployment"></a>Üzembe helyezés (projektfájlok)

Ha a helyi kódot egy Azure-beli Function alkalmazásban szeretné közzétenni, `publish` használja a következő parancsot:

```bash
func azure functionapp publish <FunctionAppName>
```

Ez a parancs egy meglévő Function alkalmazásba tesz közzé az Azure-ban. Hibaüzenet jelenik meg, ha olyan `<FunctionAppName>` közzétételi kísérletet próbál végrehajtani, amely nem létezik az előfizetésben. Ha szeretné megtudni, hogyan hozhat létre egy Function alkalmazást a parancssorból vagy a terminál ablakból az Azure CLI használatával, tekintse meg [a Függvényalkalmazás létrehozása kiszolgáló](./scripts/functions-cli-create-serverless.md)nélküli végrehajtáshoz című témakört. Alapértelmezés szerint a parancs üzembe helyezi az alkalmazást [a központi telepítési csomagból](run-functions-from-deployment-package.md)való futtatásra. Az ajánlott telepítési mód letiltásához használja a `--nozip` kapcsolót.

>[!IMPORTANT]
> Ha a Azure Portalban hoz létre egy Function alkalmazást, a függvény alapértelmezés szerint a Function Runtime 2. x verzióját használja. Ha azt szeretné, hogy a Function app a futtatókörnyezet 1. x verzióját használja, kövesse az [1. x verzió futtatásának](functions-versions.md#creating-1x-apps)utasításait.
> Egy meglévő függvényt tartalmazó Function alkalmazás futásidejű verziója nem módosítható.

A következő közzétételi beállítások mindkét verzióra érvényesek: 1. x és 2. x.

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  A beállítások közzététele a local. Settings. JSON fájlban az Azure-ba, ha a beállítás már létezik, a rendszer megkéri a felülírásra. Ha a Storage emulatort használja, először módosítsa az alkalmazás beállításait egy [tényleges Storage-kapcsolatban](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | A használatakor a `--publish-local-settings -i` rendszer letiltja az Alkalmazásbeállítások felülírására vonatkozó kérést.|

A következő közzétételi beállítások csak a 2. x verzióban támogatottak:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Csak a beállítások közzététele és a tartalom kihagyása. Az alapértelmezett érték a prompt. |
|**`--list-ignored-files`** | A közzététel során figyelmen kívül hagyott fájlok listáját jeleníti meg, amely a. funcignore fájlon alapul. |
| **`--list-included-files`** | A közzétett fájlok listáját jeleníti meg, amely a. funcignore fájlon alapul. |
| **`--nozip`** | Kikapcsolja `Run-From-Package` az alapértelmezett üzemmódot. |
| **`--build-native-deps`** | Kihagyja a generálás. Wheels mappát a Python-függvények alkalmazásainak közzétételekor. |
| **`--additional-packages`** | A natív függőségek kiépítésekor telepítendő csomagok listája. Például: `python3-dev libevent-dev`. |
| **`--force`** | Bizonyos helyzetekben figyelmen kívül hagyhatja a közzététel előtti ellenőrzést. |
| **`--csx`** | C# Parancsfájl-(. CSX) projekt közzététele. |
| **`--no-build`** | A DotNet-függvények felépítése kihagyható. |
| **`--dotnet-cli-params`** | A lefordított C# (. csproj) függvények közzétételekor a Core Tools a "DotNet Build-output bin/publish" metódust hívja meg. Az erre átadott paraméterek a parancssorhoz lesznek hozzáfűzve. |

### <a name="deployment-custom-container"></a>Üzembe helyezés (egyéni tároló)

Azure Functions lehetővé teszi a Function projekt üzembe helyezését egy [Egyéni Docker](functions-deployment-technologies.md#docker-container)-tárolóban. További információkért lásd: [függvény létrehozása Linuxon egyéni rendszerkép használatával](functions-create-function-linux-custom-image.md). Az egyéni tárolók Docker kell rendelkezniük. Ha Docker szeretne létrehozni egy alkalmazást, használja a--Docker kapcsolót `func init`.

```bash
func deploy
```

A következő egyéni tároló üzembe helyezési lehetőségei érhetők el:

| Beállítás     | Leírás                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Annak a Docker-beállításjegyzéknek a neve, amelyre az aktuális felhasználó bejelentkezett. |
| **`--platform`** | Üzemeltetési platform a Function alkalmazáshoz. Az érvényes beállítások a következők`kubernetes` |
| **`--name`** | Function alkalmazás neve. |
| **`--max`**  | Igény szerint beállíthatja a alkalmazásban telepítendő Function app-példányok maximális számát. |
| **`--min`**  | Igény szerint beállíthatja az alkalmazásban telepítendő Function app-példányok minimális számát. |
| **`--config`** | Egy opcionális telepítési konfigurációs fájl beállítása. |

## <a name="monitoring-functions"></a>Figyelési függvények

A függvények végrehajtásának ajánlott figyelése az Azure Application Insights integrálásával történik. Ha a Azure Portalban hoz létre egy Function alkalmazást, a rendszer alapértelmezés szerint ezt az integrációt végzi. Ha azonban az Azure CLI használatával hozza létre a Function alkalmazást, az Azure-beli Function alkalmazásban nem történik meg az integráció.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

További információért lásd: [Azure functions figyelése](functions-monitoring.md).
## <a name="next-steps"></a>További lépések

A Azure Functions Core Tools [nyílt forráskódú, és a githubon fut](https://github.com/azure/azure-functions-cli).  
Egy hiba vagy szolgáltatás kérésének megkereséséhez [Nyisson meg egy GitHub-problémát](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
[kiterjesztési csomagok]: functions-bindings-register.md#extension-bundles
