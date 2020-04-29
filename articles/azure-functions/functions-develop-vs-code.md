---
title: Azure Functions fejlesztése a Visual Studio Code használatával
description: Megtudhatja, hogyan fejlesztheti és tesztelheti Azure Functions a Visual Studio Code-hoz készült Azure Functions bővítménnyel.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277166"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Azure Functions fejlesztése a Visual Studio Code használatával

A [Visual Studio Code Azure functions-bővítménye] lehetővé teszi a függvények helyi fejlesztését és üzembe helyezését az Azure-ban. Ha ez a felhasználói élmény Azure Functions, akkor a [Azure functions bevezetésével](functions-overview.md)többet is megtudhat.

A Azure Functions bővítmény a következő előnyöket biztosítja:

* Függvények szerkesztése, létrehozása és futtatása a helyi fejlesztői számítógépen.
* Tegye közzé a Azure Functions projektet közvetlenül az Azure-ban.
* A Visual Studio Code előnyeinek kihasználásával különböző nyelveken írhat függvényeket.

A bővítmény a következő nyelvekkel használható, amelyeket a Azure Functions Runtime a 2. x verziótól kezdődően támogat:

* [C# lefordítva](functions-dotnet-class-library.md)
* [C#-parancsfájl](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Ehhez az szükséges, hogy [a C#-szkriptet a projekt alapértelmezett nyelvének megfelelően állítsa be](#c-script-projects).

Ebben a cikkben a példák jelenleg csak a JavaScript (node. js) és a C# Class Library függvények esetében érhetők el.  

Ez a cikk részletesen ismerteti, hogyan használhatók a Azure Functions bővítmény a függvények fejlesztéséhez és az Azure-ban való közzétételéhez. A cikk elolvasása előtt létre kell [hoznia az első függvényt a Visual Studio Code használatával](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Egyetlen Function-alkalmazás esetében ne keverje a helyi fejlesztést és a portál fejlesztését. Amikor egy helyi projektből tesz közzé egy Function alkalmazást, a telepítési folyamat felülírja a portálon kifejlesztett összes funkciót.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt telepítené és futtatja a [Azure functions-Azure functions bővítményt]a[Visual Studio Code]-hoz, meg kell felelnie a következő követelményeknek:

* A [Visual Studio Code](https://code.visualstudio.com/) telepítve van az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Az előfizetésében a [Visual Studio Code használatával történő közzétételkor](#publish-to-azure)a szükséges egyéb erőforrások, például az Azure Storage-fiók létrehozása is létrejön.

> [!IMPORTANT]
> A funkciókat helyileg fejlesztheti és közzéteheti az Azure-ban anélkül, hogy helyileg kellene elindítania és futtatnia őket. A függvények helyi futtatásához néhány további követelménynek kell megfelelnie, beleértve a Azure Functions Core Tools automatikus letöltését is. További információért lásd [a projekt helyi futtatásának további követelményeit](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

A functions bővítmény lehetővé teszi egy Function app-projekt létrehozását az első függvénnyel együtt. A következő lépések bemutatják, hogyan hozhat létre egy HTTP-triggert függvényt egy új functions-projektben. A [http-trigger](functions-bindings-http-webhook.md) a legegyszerűbb függvény trigger-sablonja, amely bemutatja.

1. Az **Azure: functions**lapon válassza a **create Function (függvény létrehozása** ) ikont:

    ![Függvény létrehozása](./media/functions-develop-vs-code/create-function.png)

1. Válassza ki a Function app-projekt mappáját, majd **válasszon egy nyelvet a függvény projekthez**.

1. Ha még nem telepítette az alapvető eszközöket, a rendszer arra kéri, hogy válassza ki a telepítendő alapeszközök **egy verzióját** . Válassza a 2. x vagy újabb verzió elemet. 

1. Válassza ki a **http-trigger** függvény sablonját, vagy a **kihagyás** gombra kattintva hozzon létre egy projektet anélkül, hogy függvényt kellene létrehoznia. Később bármikor [hozzáadhat egy függvényt a projekthez](#add-a-function-to-your-project) .

    ![A HTTP-eseményindító sablon kiválasztása](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Írja be a **HttpExample** nevet a függvény neveként, majd válassza az ENTER, majd a **függvény** engedélyezése lehetőséget. Ehhez az engedélyezési szinthez meg kell adnia egy [funkcióbillentyűk](functions-bindings-http-webhook-trigger.md#authorization-keys) használatát a függvény végpontjának meghívásakor.

    ![Függvény engedélyezésének kiválasztása](./media/functions-develop-vs-code/create-function-auth.png)

    A rendszer egy függvényt hoz létre a választott nyelven, valamint a sablonban egy HTTP által aktivált függvényhez.

    ![HTTP által aktivált függvények sablonja a Visual Studio Code-ban](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Létrehozott projektfájlok

A Project sablon létrehoz egy projektet a választott nyelven, és telepíti a szükséges függőségeket. Az új projekt bármilyen nyelven rendelkezik a következő fájlokkal:

* **Host. JSON**: lehetővé teszi a functions gazdagép konfigurálását. Ezek a beállítások akkor lépnek érvénybe, ha a functions szolgáltatást helyileg futtatja, és az Azure-ban futtatja őket. További információ: [Host. JSON-dokumentáció](functions-host-json.md).

* **Local. Settings. JSON**: a függvények helyi futtatásakor használt beállításokat tartja karban. Ezeket a beállításokat csak akkor használja a rendszer, ha helyileg futtatja a függvényeket. További információ: [Local Settings fájl](#local-settings-file).

    >[!IMPORTANT]
    >Mivel a local. Settings. JSON fájl tartalmazhat titkos kódokat, ki kell zárnia azt a projekt forrásának vezérlőjéből.

A nyelvtől függően ezek a többi fájl is létrejön:

# <a name="c"></a>[C#\#](#tab/csharp)

* A függvényt megvalósító [HttpExample.cs-függvénytár-fájl](functions-dotnet-class-library.md#functions-class-library-project) .

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Egy Package. JSON fájl a gyökérkönyvtárban.

* Egy HttpExample mappa, amely tartalmazza a [function. JSON definíciós fájlt](functions-reference-node.md#folder-structure) és az [index. js fájlt](functions-reference-node.md#exporting-a-function), amely a függvény kódját tartalmazó Node. js fájlt tartalmaz.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

Ezen a ponton hozzáadhat bemeneti és kimeneti kötéseket a függvényhez a [function. JSON fájl módosításával](#add-a-function-to-your-project) vagy egy [paraméter egy C# Class Library-függvényhez való hozzáadásával](#add-a-function-to-your-project).

[Új függvényt is hozzáadhat a projekthez](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Kötési bővítmények telepítése

A HTTP-és időzítő-eseményindítók kivételével a kötések a kiterjesztési csomagokban vannak implementálva. Telepítenie kell a kiterjesztési csomagokat a szükséges eseményindítók és kötések számára. A kötési bővítmények telepítésének folyamata a projekt nyelvétől függ.

# <a name="c"></a>[C#\#](#tab/csharp)

A terminál ablakban futtassa a [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a projektben szükséges kiterjesztési csomagok telepítéséhez. A következő parancs telepíti az Azure Storage bővítményt, amely a blob, a várólista és a Table Storage kötéseit valósítja meg.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Függvény hozzáadása a projekthez

Hozzáadhat egy új függvényt egy meglévő projekthez az előre meghatározott függvények trigger-sablonjainak használatával. Új függvény-trigger hozzáadásához válassza az F1 billentyűt a parancs paletta megnyitásához, majd keresse meg és futtassa a következő parancsot **Azure functions: Create Function**. Az utasításokat követve válassza ki az trigger típusát, és határozza meg az trigger szükséges attribútumait. Ha az triggerhez hozzáférési kulcs vagy kapcsolati karakterlánc szükséges a szolgáltatáshoz való kapcsolódáshoz, a függvény trigger létrehozása előtt készüljön fel.

A művelet eredménye a projekt nyelvétől függ:

# <a name="c"></a>[C#\#](#tab/csharp)

A rendszer új C#-függvénytárat (. cs) ad hozzá a projekthez.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Egy új mappa jön létre a projektben. A mappa egy új function. JSON fájlt és az új JavaScript-kódrészletet tartalmaz.

---

## <a name="add-input-and-output-bindings"></a>Bemeneti és kimeneti kötések hozzáadása

A függvényt kiterjesztheti a bemeneti és kimeneti kötések hozzáadásával. A kötések hozzáadásának folyamata a projekt nyelvétől függ. További információ a kötésekről: [Azure functions eseményindítók és kötések fogalmai](functions-triggers-bindings.md).

Az alábbi példák egy nevű `outqueue`tárolási sorhoz csatlakoznak, ahol a Storage-fiók kapcsolati karakterlánca a `MyStorageConnection` local. Settings. JSON alkalmazásban van beállítva.

# <a name="c"></a>[C#\#](#tab/csharp)

A Function metódus frissítésével adja hozzá a következő paramétert `Run` a metódus-definícióhoz:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Ehhez a kódhoz hozzá kell adnia a következő `using` utasítást:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

A `msg` paraméter egy `ICollector<T>` típus, amely a függvény befejeződése után kimeneti kötésbe írt üzenetek gyűjteményét jelöli. Egy vagy több üzenetet ad hozzá a gyűjteményhez. Ezeket az üzeneteket a rendszer a függvény befejeződése után elküldi a várólistára.

További információt a [várólista-tároló kimeneti kötési](functions-bindings-storage-queue-output.md) dokumentációjában talál.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

A Visual Studio Code lehetővé teszi kötések hozzáadását a function. JSON fájlhoz a promptok kényelmes készletének követésével. Kötés létrehozásához kattintson a jobb gombbal (CTRL + kattintás macOS rendszeren) a Function **. JSON** fájlt a Function mappában, és válassza a **kötés hozzáadása**elemet:

![Kötés hozzáadása meglévő JavaScript-függvényhez ](media/functions-develop-vs-code/function-add-binding.png)

A következő példa arra kéri, hogy adjon meg egy új tárolási kimeneti kötést:

| Kérdés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Kötési irány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kijelölése iránysal** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista kötése. |
| **A kódban a kötés azonosítására használt név** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az az üzenetsor, amelybe az üzenet el lesz küldve** | `outqueue` | Annak a sornak a neve, amelyet a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza az első használatkor. |
| **Válassza a beállítás elemet a "local. Settings. JSON" fájlból.** | `MyStorageConnection` | A Storage-fiókhoz tartozó kapcsolatok karakterláncát tartalmazó Alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a Function alkalmazással létrehozott Storage-fiókhoz tartozó kapcsolatok karakterláncát. |

Ebben a példában a függvény. JSON fájljában a következő `bindings` kötést adja hozzá a tömbhöz:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Ugyanazt a kötési definíciót közvetlenül is hozzáadhatja a function. JSON fájlhoz.

A függvény kódjában a `msg` kötés a következővel érhető el `context`, az alábbi példában látható módon:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

További információért lásd a várólista- [tároló kimeneti kötési](functions-bindings-storage-queue-output.md) referenciáját.

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Visual Studio Code lehetővé teszi, hogy közvetlenül az Azure-ban tegye közzé a functions-projektet. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe.

Ha Visual Studio Code-ból tesz közzé egy új Function alkalmazást az Azure-ban, a gyors Function alkalmazás létrehozási útvonalát és a speciális elérési utat is felkínáljuk. 

A Visual Studio Code-ból való közzétételkor kihasználhatja a [zip üzembe helyezési](functions-deployment-technologies.md#zip-deploy) technológiát. 

### <a name="quick-function-app-create"></a>Gyors funkció alkalmazás létrehozása

Ha az **+ új Function alkalmazás létrehozása az Azure-ban**lehetőséget választja, a bővítmény automatikusan generál értékeket a Function alkalmazás által igényelt Azure-erőforrásokhoz. Ezek az értékek a kiválasztott Function app-név alapján jelennek meg. A Project új Azure-beli Function alkalmazásban való közzétételére szolgáló alapértelmezett beállításokkal kapcsolatos példát a [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure)rövid útmutatójában talál.

Ha explicit neveket kíván megadni a létrehozott erőforrásokhoz, ki kell választania a speciális létrehozási útvonalat.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Projekt közzététele egy új Function alkalmazásban az Azure-ban speciális beállítások használatával

A következő lépésekkel teheti közzé a projektjét a speciális létrehozási beállításokkal létrehozott új Function alkalmazásban:

1. Az **Azure: functions** területen válassza az **üzembe helyezés függvényalkalmazás** ikont.

    ![A függvényalkalmazás beállításai](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Ha nincs bejelentkezve, a rendszer felszólítja, hogy **Jelentkezzen be az Azure-** ba. **Létrehozhat egy ingyenes Azure-fiókot**is. A böngészőből való bejelentkezés után lépjen vissza a Visual Studio Code-ba.

1. Ha több előfizetéssel rendelkezik, **válasszon egy előfizetést** a Function alkalmazáshoz, majd válassza az **+ új Függvényalkalmazás létrehozása az Azure-ban... _Speciális_**. Ez a _speciális_ beállítás nagyobb mértékű irányítást biztosít az Azure-ban létrehozott erőforrások felett. 

1. Az utasításokat követve adja meg a következő információkat:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Function app kiválasztása az Azure-ban | Új függvényalkalmazás létrehozása az Azure-ban | A következő üzenetben írjon be egy globálisan egyedi nevet, amely azonosítja az új Function alkalmazást, majd válassza az ENTER billentyűt. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`. |
    | Operációs rendszer kiválasztása | Windows | A Function alkalmazás Windows rendszeren fut. |
    | Üzemeltetési csomag kiválasztása | Használatalapú csomag | A kiszolgáló nélküli [fogyasztási csomag üzemeltetése](functions-scale.md#consumption-plan) használatos. |
    | Válasszon futtatókörnyezetet az új alkalmazáshoz | A projekt nyelve | A futtatókörnyezetnek meg kell egyeznie a közzétett projekttel. |
    | Válasszon ki egy erőforráscsoportot az új erőforrásokhoz | Új erőforráscsoport létrehozása | A következő parancssorba írja be az erőforráscsoport nevét, például: `myResourceGroup`, majd kattintson az ENTER gombra. Választhat egy meglévő erőforráscsoportot is. |
    | Válasszon Storage-fiókot | Új tárfiók létrehozása | A következő üzenetben írjon be egy globálisan egyedi nevet a Function alkalmazás által használt új Storage-fiókhoz, majd válassza az ENTER billentyűt. A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokból és kisbetűkből állhat. Választhat egy meglévő fiókot is. |
    | Válasszon helyet az új erőforrásokhoz | régió | Válasszon egy helyet a közeli [régióban](https://azure.microsoft.com/regions/) vagy más, a funkciókhoz hozzáférő szolgáltatások közelében. |

    Megjelenik egy értesítés a Function alkalmazás létrehozása és a központi telepítési csomag alkalmazása után. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is.

## <a name="republish-project-files"></a>Projektfájlok ismételt közzététele

A [folyamatos üzembe helyezés](functions-continuous-deployment.md)beállításakor az Azure-beli Function alkalmazás frissül, amikor a forrásfájlok frissülnek a csatlakoztatott forrás helyén. Javasoljuk a folyamatos üzembe helyezést, de a Project-fájlok frissítéseit is újra közzéteheti a Visual Studio Code-ból.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

1. A Visual Studio Code-ban válassza az F1 elemet a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a **Azure functions: üzembe helyezés a Function alkalmazásban**lehetőséget.

1. Ha nincs bejelentkezve, a rendszer felszólítja, hogy **Jelentkezzen be az Azure-** ba. Miután bejelentkezett a böngészőből, lépjen vissza a Visual Studio Code-ba. Ha több előfizetéssel rendelkezik, **válasszon ki egy előfizetést** , amely tartalmazza a Function alkalmazást.

1. Válassza ki a meglévő Function alkalmazást az Azure-ban. Ha a függvény alkalmazásban található összes fájl felülírásával kapcsolatos figyelmeztetést kap, válassza a **telepítés** lehetőséget a figyelmeztetés visszaigazolásához és a folytatáshoz.

A projekt újraépítése, újracsomagolása és feltöltése az Azure-ba történik. A meglévő projektet az új csomag váltja fel, a Function app pedig újraindul.

## <a name="get-the-url-of-the-deployed-function"></a>Az üzembe helyezett függvény URL-címének beolvasása

Egy HTTP által aktivált függvény meghívásához szüksége lesz a függvény URL-címére, amikor a rendszer üzembe helyezi a Function alkalmazásban. Ez az URL-cím tartalmazza az összes szükséges [funkcióbillentyűk](functions-bindings-http-webhook-trigger.md#authorization-keys). A bővítmény használatával lekérheti a telepített függvények URL-címeit.

1. Válassza az F1 billentyűt a parancs paletta megnyitásához, majd keresse meg és futtassa a parancsot **Azure functions: Copy Function URL-cím**.

1. Az utasításokat követve válassza ki a Function alkalmazást az Azure-ban, majd adja meg a meghívni kívánt HTTP-eseményindítót.

A függvény URL-címét a vágólapra másolja a rendszer, valamint a `code` lekérdezési paraméter által átadott szükséges kulcsokat. HTTP-eszköz használatával küldhet POST kéréseket, vagy egy böngészőt a kérések távoli függvénynek való beolvasásához.  

## <a name="run-functions-locally"></a>Függvények helyi futtatása

A Azure Functions bővítmény lehetővé teszi functions-projekt futtatását a helyi fejlesztői számítógépen. A helyi futtatókörnyezet megegyezik az Azure-beli Function alkalmazást futtató futtatókörnyezettel. A helyi beállítások a [Local. Settings. JSON fájlból](#local-settings-file)lesznek beolvasva.

### <a name="additional-requirements-for-running-a-project-locally"></a>További követelmények a projekt helyi futtatásához

A functions-projekt helyi futtatásához meg kell felelnie a következő további követelményeknek:

* Telepítse a [Azure functions Core Tools](functions-run-local.md#v2)2. x vagy újabb verzióját. A rendszer automatikusan letölti és telepíti a központi eszközkészletet a projekt helyi indításakor. Az alapvető eszközök tartalmazzák a teljes Azure Functions futtatókörnyezetet, így a letöltés és a telepítés is eltarthat egy ideig.

* Telepítse az Ön által választott nyelvhez tartozó követelményeket:

    | Nyelv | Követelmény |
    | -------- | --------- |
    | **C #** | [C#-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI-eszközök](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Hibakereső a Java-bővítményhez](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 vagy újabb](https://maven.apache.org/) |
    | **JavaScript** | [Node. js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python-3.6.8](https://www.python.org/downloads/) ajánlott|

    <sup>*</sup>Aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).

### <a name="configure-the-project-to-run-locally"></a>A projekt konfigurálása helyileg történő futtatásra

A functions Runtime az Azure Storage-fiókot belsőleg használja az összes trigger típushoz, a HTTP és webhookok kivételével. Ezért az **Values. AzureWebJobsStorage** kulcsot egy érvényes Azure Storage-fiók kapcsolódási karakterlánccá kell beállítania.

Ez a szakasz a [Visual Studio Code-hoz készült Azure Storage-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) használja a [Azure Storage Explorer](https://storageexplorer.com/) a kapcsolódáshoz és a tárolási kapcsolati karakterlánc lekéréséhez.

A Storage-fiók kapcsolódási karakterláncának beállítása:

1. A Visual Studióban nyissa meg a **Cloud Explorer alkalmazást** **, bontsa ki** > a Storage-**fiók**elemet, majd válassza a **Tulajdonságok** lehetőséget, és másolja ki az **elsődleges kapcsolatok karakterláncának** értékét.

2. A projektben nyissa meg a local. Settings. JSON fájlt, és állítsa be a **AzureWebJobsStorage** kulcs értékét a másolt kapcsolódási karakterláncra.

3. Az előző lépés megismétlésével egyedi kulcsokat adhat hozzá az **értékek** tömbhöz a függvények által igényelt egyéb kapcsolatokhoz.

További információ: [Local Settings fájl](#local-settings-file).

### <a name="debugging-functions-locally"></a>Függvények helyi hibakeresése  

A függvények hibakereséséhez válassza az F5 lehetőséget. Ha még nem töltötte le a [Core Tools][Azure functions Core Tools], a rendszer erre kéri. Ha a Core Tools telepítése és futtatása történik, a kimenet megjelenik a terminálban. Ez ugyanaz, mint a `func host start` Core Tools parancs terminálból való futtatása, de további felépítési feladatokkal és egy csatolt hibakeresővel.  

Ha a projekt fut, a függvényeket aktiválhatja úgy, ahogy a projekt üzembe helyezése az Azure-ban. Ha a projekt hibakeresési módban fut, a rendszer a várt módon a Visual Studio Code-ban találja a töréspontokat.

A HTTP-eseményindítók kérelem URL-címe a terminál kimenetében jelenik meg. A HTTP-triggerekhez tartozó funkcióbillentyűk nem használhatók, ha egy projekt helyileg fut. További információ: stratégiák a [kód teszteléséhez Azure functions](functions-test-a-function.md).  

További információ: [Azure Functions Core Tools][Azure functions Core Tools]használata.

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem települnek át automatikusan, ha a projekt közzé van téve az Azure-ban. A közzététel befejeződése után lehetősége van a beállítások közzétételére a local. Settings. JSON fájlból a Function alkalmazásba az Azure-ban. További információ: [Alkalmazásbeállítások közzététele](#publish-application-settings).

A **ConnectionStrings** lévő értékek soha nem lesznek közzétéve.

A Function Application Alkalmazásbeállítások értékei környezeti változókként is olvashatók a kódban. További információkért tekintse meg a nyelvspecifikus hivatkozási cikkek környezeti változók című részeit:

* [C# előre lefordított](functions-dotnet-class-library.md#environment-variables)
* [C#-szkript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Alkalmazásbeállítások az Azure-ban

A projekt local. Settings. JSON fájljának beállításai megegyeznek az Azure-beli Function alkalmazásban található beállításokkal. A local. Settings. JSON fájlhoz hozzáadott beállításokat is hozzá kell adni az Azure-beli Function alkalmazáshoz. Ezeket a beállításokat a projekt közzétételekor nem kell automatikusan feltölteni. Hasonlóképpen, [a portálon](functions-how-to-use-azure-function-app-settings.md#settings) létrehozott összes beállítást le kell tölteni a helyi projektbe.

### <a name="publish-application-settings"></a>Alkalmazásbeállítások közzététele

Az Azure-beli Function alkalmazásban a szükséges beállítások közzétételének legegyszerűbb módja a projekt közzététele után megjelenő **feltöltési beállítások** hivatkozás használata:

![Alkalmazásbeállítások feltöltése](./media/functions-develop-vs-code/upload-app-settings.png)

A beállításokat a **Azure functions: feltöltés helyi beállítás** parancs használatával is közzéteheti a parancs palettáján. Az Azure-ban a **Azure functions: Add New Setting** parancs használatával egyéni beállításokat adhat hozzá az alkalmazás beállításaihoz.

> [!TIP]
> A közzététel előtt mentse a local. Settings. JSON fájlt.

Ha a helyi fájl titkosítva van, a rendszer visszafejti, közzéteszi és titkosítja újra. Ha vannak olyan beállítások, amelyek ütköző értékekkel rendelkeznek a két helyen, a rendszer kéri, hogy válassza ki a folytatás módját.

Megtekintheti az alkalmazás meglévő beállításait az **Azure: functions** területen az előfizetés, a Function alkalmazás és az **Alkalmazásbeállítások**kibontásával.

![A Function app-beállítások megtekintése a Visual Studio Code-ban](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Beállítások letöltése az Azure-ból

Ha az Azure-ban hozta létre az Alkalmazásbeállítások használatát, letöltheti őket a local. Settings. JSON fájlba a **Azure functions: távoli beállítások letöltése** paranccsal.

A feltöltéshez hasonlóan, ha a helyi fájl titkosítva van, annak visszafejtése, frissítése és titkosítása újra megtörténik. Ha vannak olyan beállítások, amelyek ütköző értékekkel rendelkeznek a két helyen, a rendszer kéri, hogy válassza ki a folytatás módját.

## <a name="monitoring-functions"></a>Figyelési függvények

Ha [helyileg futtatja a függvényeket](#run-functions-locally), a rendszer a naplófájlba továbbítja az adatok továbbítását. Ha a functions-projekt egy Azure-beli Function alkalmazásban fut, akkor a naplózási adatok is beszerezhetők. Csatlakozhat az Azure-beli streaming-naplókhoz, és megtekintheti a közel valós idejű naplózási adatait, vagy engedélyezheti Application Insights a Function app működésének részletesebb megismeréséhez.

### <a name="streaming-logs"></a>Folyamatos átviteli naplók

Az alkalmazások fejlesztésekor gyakran hasznos a naplózási információk megjelenítése a közel valós időben. Megtekintheti a függvények által generált naplófájlok streamjét. Ez a kimenet egy HTTP által aktivált függvényre irányuló kérelem továbbítási naplóinak példája:

![A folyamatos átviteli naplók kimenete HTTP-trigger esetén](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

További információ: [streaming logs](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> A folyamatos átviteli naplók csak a functions gazdagép egyetlen példányát támogatják. Ha a függvény több példányra van méretezve, a más példányokból származó adatok nem jelennek meg a log streamben. A Application Insights [élő metrikastream](../azure-monitor/app/live-stream.md) több példányt támogat. A streaming Analytics közel valós időben is a [mintavételezésen](functions-monitoring.md#configure-sampling)alapul.

### <a name="application-insights"></a>Application Insights

Javasoljuk, hogy figyelje a függvények végrehajtását úgy, hogy integrálja a Function alkalmazást a Application Insightsával. Ha a Azure Portalban hoz létre egy Function alkalmazást, az integráció alapértelmezés szerint bekövetkezik. Ha a Visual Studio Publishing szolgáltatásban hozza létre a Function alkalmazást, integrálnia kell Application Insights magát.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

További információért lásd: [Azure functions figyelése](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# parancsfájl-projektek

Alapértelmezés szerint a rendszer minden C#-projektet [c# lefordított függvénytár-projektként](functions-dotnet-class-library.md)hoz létre. Ha inkább C# parancsfájl-projekteket szeretne dolgozni, a C#-parancsfájlt az alapértelmezett nyelvként kell kiválasztania a Azure Functions bővítmény beállításaiban:

1. Válassza a **fájl** > **beállításai** > **Beállítások**lehetőséget.

1. Lépjen a **felhasználói beállítások** > **bővítmények** > **Azure functions**.

1. Válassza ki a **C # szkriptet** az **Azure-függvényből: projekt nyelve**.

A fenti lépések elvégzése után a mögöttes alapeszközökre irányuló `--csx` hívások közé tartozik a beállítás, amely C# parancsfájl-(. CSX) projektfájlt hoz létre és tesz közzé. Ha ez az alapértelmezett nyelv van megadva, a rendszer minden olyan projektet, amelyet a C# parancsfájl-projektjeihez alapértelmezettként hoz létre. A rendszer nem kéri a projekt nyelvének kiválasztását az alapértelmezett beállítás megadásakor. Ha más nyelveken szeretne projekteket létrehozni, módosítania kell ezt a beállítást, vagy el kell távolítania a felhasználói beállítások. JSON fájlból. Ha eltávolítja ezt a beállítást, a rendszer ismét rákérdez a nyelv kiválasztására a projekt létrehozásakor.

## <a name="command-palette-reference"></a>A Command paletta referenciája

A Azure Functions bővítmény egy hasznos grafikus felületet biztosít az Azure-beli Function apps-alkalmazásokkal való interakcióhoz. Ugyanez a funkció a Command paletta (F1) parancsként is elérhető. Ezek a Azure Functions parancsok elérhetők:

|Azure Functions parancs  | Leírás  |
|---------|---------|
|**Új beállítások hozzáadása**  |  Létrehoz egy új Alkalmazásbeállítás-beállítást az Azure-ban. További információ: [Alkalmazásbeállítások közzététele](#publish-application-settings). Előfordulhat, hogy a [beállítást a helyi beállításokra is le kell töltenie](#download-settings-from-azure). |
| **Központi telepítési forrás konfigurálása** | A Function alkalmazást az Azure-ban csatlakoztatja egy helyi git-tárházhoz. További információ: [Azure functions folyamatos üzembe helyezése](functions-continuous-deployment.md). |
| **Kapcsolódás a GitHub-tárházhoz** | Összekapcsolja a Function alkalmazást egy GitHub-adattárral. |
| **Függvény URL-címének másolása** | Lekérdezi az Azure-ban futó HTTP-triggerű függvények távoli URL-címét. További információ: az [üzembe helyezett függvény URL-címének lekérése](#get-the-url-of-the-deployed-function). |
| **Function-alkalmazás létrehozása az Azure-ban** | Létrehoz egy új Function-alkalmazást az előfizetésben az Azure-ban. További információt az [Azure-beli új Function App-alkalmazásban való közzétételről](#publish-to-azure)szóló szakaszban talál.        |
| **Visszafejtési beállítások** | Visszafejti a Azure Functions által titkosított [helyi beállításokat](#local-settings-file) **: Titkosítsa a beállításokat**.  |
| **függvényalkalmazás törlése** | Eltávolít egy Function alkalmazást az előfizetésből az Azure-ban. Ha a App Service-csomagban nincsenek más alkalmazások, lehetősége van törölni azt is. Más erőforrások, például a Storage-fiókok és-erőforráscsoportok nem törlődnek. Az összes erőforrás eltávolításához Ehelyett [törölje az erőforráscsoportot](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). A helyi projekt nincs hatással. |
|**Függvény törlése**  | Eltávolít egy meglévő függvényt egy Azure-beli Function alkalmazásból. Mivel ez a törlés nem befolyásolja a helyi projektet, érdemes lehet helyileg eltávolítani a függvényt, majd újból [közzétenni a projektet](#republish-project-files). |
| **Proxy törlése** | Eltávolít egy Azure Functions proxyt a Function alkalmazásból az Azure-ban. További információ a proxykkal kapcsolatban: [Azure functions-proxyk használata](functions-proxies.md). |
| **Beállítás törlése** | Egy Function app-beállítás törlése az Azure-ban. Ez a törlés nem érinti a helyi. Settings. JSON fájl beállításait. |
| **Leválasztás az adattárból**  | Eltávolítja a [folyamatos üzembe helyezési](functions-continuous-deployment.md) kapcsolatot egy Azure-beli Function alkalmazás és egy verziókövetés tárháza között. |
| **Távoli beállítások letöltése** | Az Azure-ban kiválasztott Function alkalmazás beállításainak letöltése a local. Settings. JSON fájlba. Ha a helyi fájl titkosítva van, annak visszafejtése, frissítése és titkosítása újra megtörténik. Ha vannak olyan beállítások, amelyek ütköző értékekkel rendelkeznek a két helyen, a rendszer kéri, hogy válassza ki a folytatás módját. A parancs futtatása előtt ne felejtse el menteni a local. Settings. JSON fájl módosításait. |
| **Beállítások szerkesztése** | Megváltoztatja egy meglévő Function app-beállítás értékét az Azure-ban. Ez a parancs nem érinti a helyi. Settings. JSON fájl beállításait.  |
| **Beállítások titkosítása** | Titkosítja a `Values` tömb egyes elemeit a [helyi beállításokban](#local-settings-file). Ebben a fájlban a `IsEncrypted` is értékre van `true`állítva, amely megadja, hogy a helyi futtatókörnyezet visszafejtse a beállításokat a használat előtt. A helyi beállítások titkosításával csökkentheti az értékes információk szivárgásának kockázatát. Az Azure-ban az Alkalmazásbeállítások mindig titkosítva tárolódnak. |
| **A művelet végrehajtása most** | Manuálisan elindít egy [időzítő által aktivált függvényt](functions-bindings-timer.md) az Azure-ban. Ez a parancs tesztelésre szolgál. Ha többet szeretne megtudni a nem HTTP-függvények az Azure-ban való aktiválásáról, olvassa el [a nem http-triggert futtató függvények kézi futtatása](functions-manually-run-non-http.md)című témakört. |
| **Projekt inicializálása a VS Code-ban való használatra** | Hozzáadja a szükséges Visual Studio Code Project-fájlokat egy meglévő functions-projekthez. Ezzel a paranccsal dolgozhat a Core Tools használatával létrehozott projekttel. |
| **Azure Functions Core Tools telepítése vagy frissítése** | A [Azure functions Core Tools]telepítése vagy frissítése, amely a függvények helyi futtatására szolgál. |
| **Ismételt üzembe helyezés**  | Lehetővé teszi, hogy egy csatlakoztatott git-tárházból újratelepítse a projektfájlok egy adott üzembe helyezését az Azure-ban. Ha újra közzé szeretné tenni a helyi frissítéseket a Visual Studio Code-ból, [tegye közzé újra a projektet](#republish-project-files). |
| **Beállítások átnevezése** | Megváltoztatja egy meglévő Function app-beállítás kulcsának nevét az Azure-ban. Ez a parancs nem érinti a helyi. Settings. JSON fájl beállításait. Miután átnevezte az Azure-beli beállításokat, [töltse le ezeket a módosításokat a helyi projektbe](#download-settings-from-azure). |
| **Indítsa újra** | Újraindítja a Function alkalmazást az Azure-ban. A frissítések telepítése a Function alkalmazást is újraindítja. |
| **AzureWebJobsStorage beállítása**| Az `AzureWebJobsStorage` Alkalmazásbeállítás értékének beállítása. Ezt a beállítást a Azure Functions kötelező megadni. Akkor van beállítva, amikor egy Function-alkalmazás létrejön az Azure-ban. |
| **Kezdés** | Elindít egy leállított Function alkalmazást az Azure-ban. |
| **Adatfolyam-naplók indítása** | Elindítja az Azure-beli Function alkalmazás adatfolyam-naplóit. Ha a naplózási információkat közel valós időben kell megtekinteni, használja az Azure-ban a távoli hibaelhárítás során használt adatfolyam-naplókat. További információ: [streaming logs](#streaming-logs). |
| **Leállítás** | Leállítja az Azure-ban futó Function alkalmazást. |
| **Folyamatos átviteli naplók leállítása** | Leállítja az Azure-beli Function alkalmazás adatfolyam-naplóit. |
| **Váltás tárolóhely-beállításként** | Ha engedélyezve van, gondoskodik arról, hogy egy alkalmazás beállítása megmaradjon egy adott üzembe helyezési pontnál. |
| **Azure Functions Core Tools eltávolítása** | Eltávolítja a bővítmény által igényelt Azure Functions Core Toolst. |
| **Helyi beállítások feltöltése** | Feltölti a beállításokat a local. Settings. JSON fájlból a kiválasztott Function alkalmazásba az Azure-ban. Ha a helyi fájl titkosítva van, a rendszer visszafejti, feltölti és titkosítja újra. Ha vannak olyan beállítások, amelyek ütköző értékekkel rendelkeznek a két helyen, a rendszer kéri, hogy válassza ki a folytatás módját. A parancs futtatása előtt ne felejtse el menteni a local. Settings. JSON fájl módosításait. |
| **Véglegesítés megtekintése a GitHubon** | Megjeleníti a legutóbbi véglegesítés egy adott központi telepítésben, ha a Function alkalmazás egy adattárhoz csatlakozik. |
| **Telepítési naplók megtekintése** | Megjeleníti az Azure-beli Function alkalmazáshoz megadott központi telepítés naplóit. |

## <a name="next-steps"></a>További lépések

További információ a Azure Functions Core Toolsről: [a Azure functions Core Tools használata](functions-run-local.md).

Ha többet szeretne megtudni a függvények .NET-es kódtáraként való fejlesztéséről, tekintse meg a [Azure functions C# fejlesztői referenciát](functions-dotnet-class-library.md). A cikk hivatkozásokat is tartalmaz arra vonatkozóan, hogyan használhatók az attribútumok a Azure Functions által támogatott különböző típusú kötések deklarálása céljából.

[Azure Functions-bővítmény a Visual Studio Code-hoz]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md
