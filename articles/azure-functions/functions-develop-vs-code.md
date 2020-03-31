---
title: Azure-függvények fejlesztése a Visual Studio-kód használatával
description: Ismerje meg, hogyan fejleszthet és tesztelhet Azure-függvényeket a Visual Studio-kód Azure Functions bővítményhasználatával.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277166"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Azure-függvények fejlesztése a Visual Studio-kód használatával

Az [Azure Functions bővítmény a Visual Studio-kód] lehetővé teszi, hogy helyileg funkciókat, és telepítse azokat az Azure-ban. Ha ez az élmény az első az Azure Functions szolgáltatással, további információ az [Azure Functions bevezető című](functions-overview.md)része.

Az Azure Functions bővítmény a következő előnyöket nyújtja:

* A helyi fejlesztői számítógépen szerkesztheti, felépítheti és futtathatja a függvényeket.
* Tegye közzé Az Azure Functions projektet közvetlenül az Azure-ban.
* A Visual Studio Code előnyeit kihasználva különböző nyelveken írhatja le a funkciókat.

A bővítmény a következő nyelvekkel használható, amelyeket az Azure Functions futásidejű 2.x-es verziótól kezdve támogat:

* [C# összeállított](functions-dotnet-class-library.md)
* [C# parancsfájl](functions-reference-csharp.md)<sup>*</sup>
* [Javascript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Powershell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>A [C# parancsfájlt kell alapértelmezett projektnyelvként beállítani.](#c-script-projects)

Ebben a cikkben a példák jelenleg csak javascript (Node.js) és C# osztálykönyvtár-függvények.  

Ez a cikk ismerteti, hogyan használhatja az Azure Functions bővítményt függvények fejlesztéséhez és közzétételéhez az Azure-ban. A cikk elolvasása előtt [hozza létre az első függvényt a Visual Studio Code használatával.](functions-create-first-function-vs-code.md)

> [!IMPORTANT]
> Ne keverje a helyi fejlesztést és a portálfejlesztést egyetlen függvényalkalmazáshoz. Amikor egy helyi projektből tesz közzé egy függvényalkalmazást, a központi telepítési folyamat felülírja a portálon kifejlesztett funkciókat.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Functions [bővítmény][Azure Functions bővítményének telepítése]és futtatása előtt meg kell felelnie az alábbi követelményeknek:

* [A Visual Studio-kód](https://code.visualstudio.com/) telepítve van a [támogatott platformok egyikén.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Egyéb erőforrások, amelyekre szüksége van, például egy Azure tárfiók, az előfizetésben jönnek létre, amikor [a Visual Studio-kód használatával teszi közzé.](#publish-to-azure)

> [!IMPORTANT]
> Helyileg fejleszthet függvényeket, és anélkül teheti közzé őket az Azure-ban, hogy helyileg kellene elindítania és futtatnia őket. A függvények helyi futtatásához meg kell felelnie néhány további követelménynek, beleértve az Azure Functions Core Tools automatikus letöltését. További információ: [További követelmények a projekt helyi futtatásához.](#additional-requirements-for-running-a-project-locally)

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

A Funkciók bővítmény lehetővé teszi egy függvényalkalmazás-projekt létrehozását az első funkcióval együtt. A következő lépések bemutatják, hogyan hozhat létre HTTP-aktivált függvényt egy új Functions projektben. [HTTP trigger](functions-bindings-http-webhook.md) a legegyszerűbb függvény eseményindító sablon t.

1. Az **Azure-ból: Functions**, válassza a **Függvény létrehozása** ikont:

    ![Függvény létrehozása](./media/functions-develop-vs-code/create-function.png)

1. Jelölje ki a függvényalkalmazás-projekt mappáját, majd **válassza ki a függvényprojekt nyelvét.**

1. Ha még nem telepítette a Core Tools eszközt, a rendszer kéri, hogy válassza ki a telepítandó Core Tools **egy verzióját.** Válassza a 2.x-es vagy újabb verziót. 

1. Válassza ki a **HTTP trigger** függvénysablont, vagy most a **Kihagyás** lehetőséget választva függvény nélküli projektet hozhat létre. Később bármikor [hozzáadhat egy függvényt a projekthez.](#add-a-function-to-your-project)

    ![A HTTP-eseményindító sablon kiválasztása](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Írja be a **HttpExample** értéket a függvény nevéhez, és válassza az Enter lehetőséget, majd válassza a **Függvényengedélyezés** lehetőséget. Ez az engedélyezési szint megköveteli, hogy egy [függvénykulcsot,](functions-bindings-http-webhook-trigger.md#authorization-keys) amikor meghívja a függvény végpontot.

    ![Függvényengedélyezés kiválasztása](./media/functions-develop-vs-code/create-function-auth.png)

    A függvény a kiválasztott nyelven és egy HTTP-aktivált függvény sablonjában jön létre.

    ![HTTP-triggeres függvénysablon a Visual Studio-kódban](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Létrehozott projektfájlok

A projektsablon létrehoz egy projektet a kiválasztott nyelven, és telepíti a szükséges függőségeket. Bármely nyelven, az új projekt ezeket a fájlokat:

* **host.json**: Lehetővé teszi a Functions állomás konfigurálását. Ezek a beállítások akkor érvényesek, ha helyileg futtat ja a függvényeket, és amikor az Azure-ban futtatja őket. További információ: [host.json reference](functions-host-json.md).

* **local.settings.json**: A függvények helyi futtatásakor használt beállítások karbantartása. Ezek a beállítások csak akkor használatosak, ha helyileg futtatja a függvényeket. További információt a [Helyi beállítások fájl című témakörben](#local-settings-file)talál.

    >[!IMPORTANT]
    >Mivel a local.settings.json fájl titkos kulcsokat tartalmazhat, ki kell zárnia a projekt forrásvezérlőjéből.

A nyelvtől függően ezek a fájlok a következők:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs osztálytárfájl,](functions-dotnet-class-library.md#functions-class-library-project) amely megvalósítja a függvényt.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

* Package.json fájl a gyökérmappában.

* A [function.json definíciós fájlt](functions-reference-node.md#folder-structure) és az [index.js fájlt](functions-reference-node.md#exporting-a-function), a függvénykódot tartalmazó Node.js fájlt tartalmazó HttpExample mappa.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

Ezen a ponton bemeneti és kimeneti kötéseket adhat a függvényhez [a function.json fájl módosításával](#add-a-function-to-your-project) vagy [egy paraméter c# osztálykönyvtár-függvényhez való hozzáadásával.](#add-a-function-to-your-project)

Új függvényt is [hozzáadhat a projekthez.](#add-a-function-to-your-project)

## <a name="install-binding-extensions"></a>Kötési bővítmények telepítése

A HTTP és az időzítő eseményindítók kivételével a kötések bővítménycsomagokban vannak megvalósítva. Telepítenie kell a bővítménycsomagokat az eseményindítókhoz és kötésekhez, amelyeknek szükségük van rájuk. A kötésbővítmények telepítésének folyamata a projekt nyelvététől függ.

# <a name="c"></a>[C\#](#tab/csharp)

Futtassa a [dotnet add package parancsot](/dotnet/core/tools/dotnet-add-package) a Terminál ablakban a projektben szükséges bővítménycsomagok telepítéséhez. A következő parancs telepíti az Azure Storage bővítményt, amely a Blob, a Queue és a Table storage kötéseit valósítja meg.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[Javascript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Függvény hozzáadása a projekthez

Új függvényt adhat egy meglévő projekthez az előre definiált Függvények eseményindító sablonjainak egyikével. Új függvényeseményindító hozzáadásához válassza az F1 lehetőséget a parancspaletta megnyitásához, majd keresse meg és futtassa az **Azure Functions: Create Function parancsot.** Kövesse a figyelmeztetést az eseményindító típusának kiválasztásához és az eseményindító szükséges attribútumainak meghatározásához. Ha az eseményindítóhoz hozzáférési kulcs vagy kapcsolati karakterlánc szükséges a szolgáltatáshoz való csatlakozáshoz, készítse elő a függvényeseményindító létrehozása előtt.

A művelet eredményei a projekt nyelvétől függenek:

# <a name="c"></a>[C\#](#tab/csharp)

A projekthez egy új C# osztálykönyvtár (.cs) fájl kerül hozzáadásra.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Új mappa jön létre a projektben. A mappa egy új function.json fájlt és az új JavaScript kódfájlt tartalmaz.

---

## <a name="add-input-and-output-bindings"></a>Bemeneti és kimeneti kötések hozzáadása

A függvényt bemeneti és kimeneti kötések hozzáadásával bővítheti. A kötések hozzáadásának folyamata a projekt nyelvététől függ. Ha többet szeretne megtudni a kötésekről, olvassa el az [Azure Functions eseményindítók és kötések fogalmait.](functions-triggers-bindings.md)

A következő példák egy `outqueue`tárolóváróvárólistához csatlakoznak, ahol a `MyStorageConnection` tárfiók kapcsolati karakterlánca a local.settings.json alkalmazásbeállításában van beállítva.

# <a name="c"></a>[C\#](#tab/csharp)

Frissítse a függvénymetódust, hogy `Run` a következő paramétert adja hozzá a metódusdefinícióhoz:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Ehhez a kódhoz a `using` következő utasítást kell megadnia:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

A `msg` paraméter `ICollector<T>` egy olyan típus, amely a függvény befejezésekor egy kimeneti kötésbe írt üzenetek gyűjteményét jelöli. Egy vagy több üzenetet ad hozzá a gyűjteményhez. Ezeket az üzeneteket a függvény befejezésekor a rendszer elküldi a várólistába.

További információ: [A várólista tárolási kimeneti kötési](functions-bindings-storage-queue-output.md) dokumentációja.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

A Visual Studio Code lehetővé teszi, hogy kötéseket adjon a function.json fájlhoz egy megfelelő kérdéskészlet követésével. Kötés létrehozásához kattintson a jobb gombbal (Ctrl+kattintson a macOS-ra) a **function.json** fájlra a függvénymappában, és válassza a **Kötés hozzáadása parancsot**:

![Kötés hozzáadása meglévő JavaScript-függvényhez ](media/functions-develop-vs-code/function-add-binding.png)

A következőkben egy új tárolókimeneti kötés definiálására vonatkozó utasításokat követők találhatók:

| Kérdés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Kötésirány kiválasztása** | `out` | A kötés kimeneti kötés. |
| **Kötés kiválasztása irányban** | `Azure Queue Storage` | A kötés egy Azure Storage-várólista-kötés. |
| **A kötés azonosítására használt név a kódban** | `msg` | A kódban hivatkozott kötési paramétert azonosító név. |
| **Az a várólista, amelyre az üzenetet küldi** | `outqueue` | Annak a várólistának a neve, amelybe a kötés ír. Ha a *queueName* nem létezik, a kötés létrehozza azt az első használatkor. |
| **A beállítás kiválasztása a "local.settings.json" területen** | `MyStorageConnection` | A tárfiók kapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás a függvényalkalmazással létrehozott tárfiók kapcsolati karakterláncát tartalmazza. |

Ebben a példában a következő `bindings` kötés kerül a function.json fájl tömbjéhez:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

Ugyanazt a kötésdefiníciót közvetlenül a function.jsonhoz is hozzáadhatja.

A függvénykódban `msg` a kötés a `context`ból érhető el, mint ebben a példában:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

További információ: [A várólista tárolási kimeneti kötési](functions-bindings-storage-queue-output.md) hivatkozás.

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Visual Studio Code lehetővé teszi, hogy a Functions projektet közvetlenül az Azure-ban tegye közzé. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe.

Amikor a Visual Studio-kódból egy új függvényalkalmazásba teszi közzé az Azure-ban, a rendszer felajánlja egy gyorsfüggvényalkalmazás létrehozási útvonalát és egy speciális elérési utat. 

Amikor a Visual Studio-kódból tesz közzé, kihasználja a [Zip üzembe helyezési](functions-deployment-technologies.md#zip-deploy) technológia előnyeit. 

### <a name="quick-function-app-create"></a>Gyorsfüggvény alkalmazás létrehozása

Ha a + Új függvényalkalmazás létrehozása lehetőséget választja az **Azure-ban...** a bővítmény automatikusan létrehozza a függvényalkalmazás által igényelt Azure-erőforrások értékeit. Ezek az értékek a kiválasztott függvényalkalmazás-névalapján alapulnak. Ha például az alapértelmezett értékek használatával teszi közzé a projektet egy új függvényalkalmazásban az Azure-ban, tekintse meg a [Visual Studio-kód rövid útmutató ról szóló cikket.](functions-create-first-function-vs-code.md#publish-the-project-to-azure)

Ha explicit neveket szeretne megadni a létrehozott erőforrásoknak, ki kell választania a speciális létrehozási útvonalat.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Projekt közzététele egy új függvényalkalmazásban az Azure-ban speciális beállításokkal

A következő lépések teszik közzé a projektet egy speciális létrehozási beállításokkal létrehozott új függvényalkalmazásban:

1. Az **Azure: Functions** területen válassza ki a **Telepítés a függvényalkalmazáshoz** ikont.

    ![A függvényalkalmazás beállításai](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Ha nincs bejelentkezve, a rendszer kéri, hogy **jelentkezzen be az Azure-ba.** **Ingyenes Azure-fiókot**is létrehozhat. A böngészőből való bejelentkezés után lépjen vissza a Visual Studio-kódhoz.

1. Ha több előfizetéssel rendelkezik, **válasszon egy előfizetést** a függvényalkalmazáshoz, majd válassza a **+ Új függvényalkalmazás létrehozása az Azure-ban lehetőséget... _Speciális_**. _Ez a Speciális_ beállítás lehetővé teszi, hogy jobban szabályozhatja az Azure-ban létrehozott erőforrásokat. 

1. Az utasításokat követően adja meg ezt az információt:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Válassza ki a függvényalkalmazást az Azure-ban | Új függvényalkalmazás létrehozása az Azure-ban | A következő kérdésre írjon be egy globálisan egyedi nevet, amely azonosítja az új függvényalkalmazást, majd válassza az Enter lehetőséget. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`. |
    | Operációs rendszer kiválasztása | Windows | A függvényalkalmazás Windows rendszeren fut. |
    | Hostingcsomag kiválasztása | Használatalapú csomag | A kiszolgáló nélküli [fogyasztási terv hosting](functions-scale.md#consumption-plan) használják. |
    | Futásidő kiválasztása az új alkalmazáshoz | A projekt nyelve | A futásidőnek meg kell egyeznie a közzétenni kívánt projekttel. |
    | Erőforráscsoport kiválasztása új erőforrásokhoz | Új erőforráscsoport létrehozása | A következő kérdésnél írja be az `myResourceGroup`erőforráscsoport nevét(például a, a) lehetőséget, majd válassza a Belépés lehetőséget. Meglévő erőforráscsoportot is kijelölhet. |
    | Tárfiók kiválasztása | Új tárfiók létrehozása | A következő kérdés, írja be a globálisan egyedi nevet az új tárfiók által használt függvényalkalmazás, és válassza az Enter. A tárfióknevek nek 3 és 24 karakter közöttieknek kell lenniük, és csak számokat és kisbetűket tartalmazhatnak. Meglévő fiókot is kiválaszthat. |
    | Új erőforrások helyének kiválasztása | régió | Válasszon ki egy helyet egy önhöz közeli [régióban](https://azure.microsoft.com/regions/) vagy más szolgáltatások közelében, amelyekhez a funkciók hozzáférhetnek. |

    Egy értesítés jelenik meg a függvényalkalmazás létrehozása és a központi telepítési csomag alkalmazása után. Válassza a **Kimenet megtekintése** ebben az értesítésben a létrehozási és üzembe helyezési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat.

## <a name="republish-project-files"></a>Projektfájlok újbóli közzététele

A folyamatos [üzembe helyezés](functions-continuous-deployment.md)beállításakor a függvényalkalmazás az Azure-ban frissül, amikor a forrásfájlok frissülnek a csatlakoztatott forrás helyen. Javasoljuk a folyamatos telepítést, de a Visual Studio-kódból is közzéteheti a projektfájl frissítéseit.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

1. A Visual Studio-kódban válassza az F1 lehetőséget a parancspaletta megnyitásához. A parancspalettán keresse meg és válassza ki az **Azure Functions: Deploy to function app**lehetőséget.

1. Ha nincs bejelentkezve, a rendszer kéri, hogy **jelentkezzen be az Azure-ba.** Miután bejelentkezett a böngészőből, lépjen vissza a Visual Studio-kódhoz. Ha több előfizetéssel rendelkezik, **válasszon egy előfizetést,** amely tartalmazza a függvényalkalmazást.

1. Válassza ki a meglévő függvényalkalmazást az Azure-ban. Amikor a rendszer figyelmezteti a függvényalkalmazás összes fájljának felülírására, válassza a **Telepítés** lehetőséget a figyelmeztetés nyugtázásához és folytatásához.

A projekt et újraépítik, újracsomagolják és feltöltik az Azure-ba. A meglévő projektet az új csomag váltja fel, és a függvényalkalmazás újraindul.

## <a name="get-the-url-of-the-deployed-function"></a>Az üzembe helyezett függvény URL-címének beszereznie

Http-aktivált függvény hívásához a függvény URL-címére van szükség, amikor telepítve van a függvényalkalmazásban. Ez az URL tartalmazza a szükséges [funkciókulcsokat.](functions-bindings-http-webhook-trigger.md#authorization-keys) A bővítmény segítségével lekéri ezeket az URL-címeket az üzembe helyezett függvényekhez.

1. Válassza az F1 lehetőséget a parancspaletta megnyitásához, majd keresse meg és futtassa az **Azure Functions: Copy Function URL parancsot.**

1. Kövesse a utasításokat a függvényalkalmazás kiválasztásához az Azure-ban, majd az adott HTTP-eseményindító, amely meg szeretne hívni.

A függvény URL-címét a program a vágólapra másolja, a `code` lekérdezési paraméter által megadott szükséges kulcsokkal együtt. Http-eszközzel küldjön POST-kérelmeket, vagy egy böngészőt a get kérelmekhez a távoli függvénynek.  

## <a name="run-functions-locally"></a>Függvények helyi futtatása

Az Azure Functions bővítmény lehetővé teszi a Functions projekt futtatását a helyi fejlesztői számítógépen. A helyi futásidejű ugyanaz a futásidejű, amely a függvényalkalmazást üzemelteti az Azure-ban. A helyi beállításokat a [local.settings.json fájlból](#local-settings-file)olvassa be a program.

### <a name="additional-requirements-for-running-a-project-locally"></a>A projekt helyi futtatásával kapcsolatos további követelmények

A Functions projekt helyi futtatásához meg kell felelnie az alábbi további követelményeknek:

* Telepítse az Azure Functions [Core Tools](functions-run-local.md#v2)2.x-es vagy újabb verzióját. A Core Tools csomag letöltése és automatikusan telepítése a projekt helyi indításakor történik. A Core Tools tartalmazza a teljes Azure Functions futásidőt, így a letöltés és a telepítés eltarthat egy ideig.

* Telepítse az Ön által választott nyelvhez tartozó követelményeket:

    | Nyelv | Követelmény |
    | -------- | --------- |
    | **C #** | [C# kiterjesztés](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI-eszközök](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Hibakereső Java-bővítményhez](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 vagy újabb](https://maven.apache.org/) |
    | **Javascript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) ajánlott|

    <sup>*</sup>Aktív LTS és Karbantartási LTS verziók (8.11.1 és 10.14.1 ajánlott).

### <a name="configure-the-project-to-run-locally"></a>A projekt helyi futtatásának beállítása

A Functions futásidejű egy Azure Storage-fiókot használ belsőleg a HTTP-től és a webhookoktól eltérő összes eseményindító típushoz. Ezért be kell állítania a **Values.AzureWebJobsStorage** kulcsot egy érvényes Azure Storage-fiók kapcsolati karakterláncra.

Ez a szakasz az Azure Storage-bővítményt használja a [Visual Studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) az [Azure Storage Explorerrel](https://storageexplorer.com/) a tárolási kapcsolati karakterlánchoz való csatlakozáshoz, és lekérheti.

A tárfiók kapcsolati karakterláncának beállítása:

1. A Visual Studio-ban nyissa meg a **Cloud Explorer**alkalmazást, **bontsa** > ki a Tárfiók**a tárfiók csomópontot,** majd válassza a **Tulajdonságok** lehetőséget, és másolja az **elsődleges kapcsolati karakterlánc** értékét.

2. A projektben nyissa meg a local.settings.json fájlt, és állítsa be az **AzureWebJobsStorage** kulcs értékét a másolt kapcsolati karakterlánchoz.

3. Ismételje meg az előző lépést, ha egyedi kulcsokat szeretne hozzáadni a **Függvények** által igényelt egyéb kapcsolatok értékek tömbjéhez.

További információt a [Helyi beállítások fájl című témakörben](#local-settings-file)talál.

### <a name="debugging-functions-locally"></a>Helyi hibakeresési függvények  

A függvények hibakereséséhez válassza az F5 lehetőséget. Ha még nem töltötte le [a Core Tools]Azure Functions Core Tools[eszközt,]a rendszer erre kéri. A Core Tools telepítésekor és futtatásakor a kimenet megjelenik a terminálon. Ez megegyezik a `func host start` Core Tools parancs terminálról történő futtatásával, de további buildfeladatokkal és egy csatolt hibakeresővel.  

Amikor a projekt fut, a függvények, mint akkor, ha a projekt üzembe helyezése az Azure-ban. Ha a projekt hibakeresési módban fut, a program a várt módon lenyomja a töréspontokat a Visual Studio-kódban.

A HTTP-eseményindítók kérelem URL-címe megjelenik a terminál kimenetében. A HTTP-eseményindítók funkciókulcsai nem használatosak, ha a projekt helyileg fut. További információ: [Strategies for testing your code in Azure Functions](functions-test-a-function.md).  

További információ: [Az Azure Functions core eszközeinek használata][Az Azure Functions Core Tools .]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem kerülnek automatikusan áttelepítésre, amikor a projekt et közzéteszik az Azure-ban. A közzététel befejezése után lehetősége van a beállítások közzétételére a local.settings.json ról az Azure-beli függvényalkalmazásba. További információ: [Alkalmazásbeállítások közzététele](#publish-application-settings).

A **ConnectionStrings értékei** soha nem kerülnek közzétételre.

A függvény alkalmazás beállításaiértékek is olvasható a kódot, mint a környezeti változók. További információt a nyelvi referenciacikkek Környezeti változók című szakaszában talál:

* [C# előre lefordítva](functions-dotnet-class-library.md#environment-variables)
* [C#-szkript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Alkalmazásbeállítások az Azure-ban

A beállításokat a local.settings.json fájlban a projekt ben meg kell egyeznie az alkalmazás beállításait a függvényalkalmazás az Azure-ban. A local.settings.json hoz hozzáadott beállításokat is hozzá kell adni a függvényalkalmazáshoz az Azure-ban. Ezek a beállítások nem töltődnek fel automatikusan a projekt közzétételekor. Hasonlóképpen a függvényalkalmazásban [a portálon](functions-how-to-use-azure-function-app-settings.md#settings) létrehozott beállításokat is le kell tölteni a helyi projektbe.

### <a name="publish-application-settings"></a>Alkalmazásbeállítások közzététele

A szükséges beállítások közzétételének legegyszerűbb módja a függvényalkalmazásban az Azure-ban, ha a projekt közzététele után megjelenő **Feltöltési beállítások** hivatkozást használja:

![Alkalmazásbeállításainak feltöltése](./media/functions-develop-vs-code/upload-app-settings.png)

A beállításokat az **Azure Functions: Upload Local Setting** paranccsal is közzéteheti a parancspalettán. Az **Azure-funkciók: Új beállítás hozzáadása** paranccsal egyéni beállításokat adhat hozzá az Azure-beli alkalmazásbeállításokhoz.

> [!TIP]
> A közzététel előtt mentse a local.settings.json fájlt.

Ha a helyi fájl titkosítva van, akkor a rendszer visszafejti, közzéteszi és újra titkosítja. Ha vannak olyan beállítások, amelyek ütköző értékeket tartalmaznak a két helyen, a program kéri a folytatás módját.

Tekintse meg a meglévő alkalmazásbeállításokat az **Azure:Functions** területen az előfizetés, a függvényalkalmazás és az **alkalmazásbeállítások**bővítésével.

![A függvényalkalmazás beállításainak megtekintése a Visual Studio-kódban](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Beállítások letöltése az Azure-ból

Ha már létrehozott alkalmazásbeállításokat az Azure-ban, letöltheti őket a local.settings.json fájlba az **Azure Functions: Remote Settings letöltése** paranccsal.

A feltöltéshez hasonlóan, ha a helyi fájl titkosítva van, a rendszer visszafejti, frissíti és újra titkosítja. Ha vannak olyan beállítások, amelyek ütköző értékeket tartalmaznak a két helyen, a program kéri a folytatás módját.

## <a name="monitoring-functions"></a>Felügyeleti funkciók

Ha [helyileg futtatja a függvényeket,](#run-functions-locally)a naplóadatok a terminálkonzolra adatolnak. Naplóadatokat is beszerezhet, ha a Functions projekt egy függvényalkalmazásban fut az Azure-ban. Csatlakozhat a streamelési naplókhoz az Azure-ban a közel valós idejű naplóadatok megtekintéséhez, vagy engedélyezheti az Application Insights számára, hogy jobban megértse, hogyan viselkedik a függvényalkalmazás.

### <a name="streaming-logs"></a>Streamelési naplók

Amikor egy alkalmazást fejleszt, gyakran hasznos, ha közel valós időben látja a naplózási adatokat. Megtekintheti a függvények által létrehozott naplófájlok adatfolyamát. Ez a kimenet egy http-aktivált függvényhez intézett kérelem streamelési naplóinak példáját adja:

![Streamelési naplók kimenete HTTP-eseményindítóhoz](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

További információ: [Streamelési naplók](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> A streamelési naplók a Functions-állomásnak csak egy példányát támogatják. Ha a függvény több példányra van méretezve, a más példányokból származó adatok nem jelennek meg a naplófolyamban. Az Application Insights élő [metrikák streamelése](../azure-monitor/app/live-stream.md) több példányt is támogat. Bár közel valós időben is, a streamelési elemzés [a mintavételezett adatokon](functions-monitoring.md#configure-sampling)alapul.

### <a name="application-insights"></a>Application Insights

Azt javasoljuk, hogy a függvények végrehajtásának figyelése a függvényalkalmazás integrálásával az Application Insights. Amikor létrehoz egy függvényalkalmazást az Azure Portalon, ez az integráció alapértelmezés szerint történik. Amikor a Visual Studio közzététele során létrehozza a függvényalkalmazást, saját kezűleg kell integrálnia az Application Insightsot.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

További információ: [Monitor Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# parancsfájl-projektek

Alapértelmezés szerint az összes C# projekt [C# összeállított osztálykönyvtár-projektekként](functions-dotnet-class-library.md)jön létre. Ha inkább C# parancsfájl-projektekkel szeretne dolgozni, az Azure Functions bővítmény beállításaiban a C# parancsfájlt kell alapértelmezett nyelvként választania:

1. Válassza a > **Fájlbeállítások** > **beállításai lehetőséget.** **File**

1. Nyissa meg a **Felhasználói beállítások** > **bővítményei** > **Azure-függvényeket.**

1. Válassza a **C#Script lehetőséget** az **Azure-függvényből: A projekt nyelve**.

A lépések elvégzése után az alapul szolgáló Core `--csx` Tools-hívások tartalmazzák a C# parancsfájlok (.csx) projektfájljait generáló és közzémagamió lehetőséget. Ha ez az alapértelmezett nyelv van megadva, az összes létrehozott projekt c# parancsfájl-projektek. A program nem kéri, hogy az alapértelmezett beállítás esetén válasszon projektnyelvet. Ha más nyelvű projekteket szeretne létrehozni, meg kell változtatnia ezt a beállítást, vagy el kell távolítania a felhasználó settings.json fájljából. A beállítás eltávolítása után a rendszer ismét kéri, hogy a projekt létrehozásakor válassza ki a kívánt nyelvet.

## <a name="command-palette-reference"></a>Parancspaletta hivatkozása

Az Azure Functions bővítmény hasznos grafikus felületet biztosít a területen a függvényalkalmazások azure-beli együttműködéséhez. Ugyanez a funkció a parancspaletta (F1) parancsaiként is elérhető. Ezek az Azure Functions parancsok érhetők el:

|Az Azure Functions parancs  | Leírás  |
|---------|---------|
|**Új beállítások hozzáadása**  |  Új alkalmazásbeállítást hoz létre az Azure-ban. További információ: [Alkalmazásbeállítások közzététele](#publish-application-settings). Előfordulhat, hogy [ezt a beállítást a helyi beállításokra](#download-settings-from-azure)is le kell töltenie. |
| **Telepítési forrás konfigurálása** | Az Azure-beli függvényalkalmazást egy helyi Git-tárházhoz csatlakoztatja. További információ: [Folyamatos üzembe helyezés az Azure Functions számára.](functions-continuous-deployment.md) |
| **Csatlakozás a GitHub-tárházhoz** | A függvényalkalmazást egy GitHub-tárházhoz csatlakoztatja. |
| **Függvény URL-címének másolása** | Leváltja az Azure-ban futó HTTP-aktivált függvény távoli URL-címét. További információ: [Az üzembe helyezett függvény URL-címének beolvassa.](#get-the-url-of-the-deployed-function) |
| **Függvényalkalmazás létrehozása az Azure-ban** | Új függvényalkalmazást hoz létre az Azure-beli előfizetésében. További információ: az [Azure-beli új függvényalkalmazásokban való közzétételről](#publish-to-azure)szóló szakaszban olvashat.        |
| **Beállítások visszafejtése** | Visszafejti az Azure Functions által titkosított [helyi beállításokat:](#local-settings-file) **Beállítások titkosítása**.  |
| **Függvényalkalmazás törlése** | Eltávolítja a függvényalkalmazást az Azure-beli előfizetéséből. Ha nincs más alkalmazás az App Service-csomagban, akkor azt is törölheti. Más erőforrások, például a tárfiókok és az erőforráscsoportok nem törlődnek. Az összes erőforrás eltávolításához törölje [az erőforráscsoportot.](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources) A helyi projektet ez nem érinti. |
|**Törlés függvény**  | Egy meglévő függvény eltávolítása egy függvényalkalmazásból az Azure-ban. Mivel ez a törlés nincs hatással a helyi projektre, fontolja meg a függvény helyi eltávolítását, majd [a projekt újbóli közzétételét.](#republish-project-files) |
| **Proxy törlése** | Eltávolítja az Azure Functions-proxyt az Azure-beli függvényalkalmazásból. A proxykról az [Azure Functions proxyk kalkulátorai](functions-proxies.md)ban olvashat bővebben. |
| **Törlési beállítás** | Egy függvényalkalmazás-beállítás törlése az Azure-ban. Ez a törlés nincs hatással a local.settings.json fájl beállításaira. |
| **Kapcsolat bontása az átólahivatkozóval**  | Eltávolítja a [folyamatos üzembe helyezési](functions-continuous-deployment.md) kapcsolatot egy függvényalkalmazás az Azure-ban és a forrás-ellenőrzési tárház között. |
| **Távoli beállítások letöltése** | Letölti a beállításokat a kiválasztott függvényalkalmazásból az Azure-ban a local.settings.json fájlba. Ha a helyi fájl titkosítva van, akkor a rendszer visszafejti, frissíti és újra titkosítja. Ha vannak olyan beállítások, amelyek ütköző értékeket tartalmaznak a két helyen, a program kéri a folytatás módját. A parancs futtatása előtt mentse a local.settings.json fájl módosításait. |
| **Beállítások szerkesztése** | Módosítja egy meglévő függvényalkalmazás-beállítás értékét az Azure-ban. Ez a parancs nincs hatással a local.settings.json fájl beállításaira.  |
| **Beállítások titkosítása** | A [helyi beállításokban](#local-settings-file) `Values` a tömb egyes elemeit titkosítja. Ebben a `IsEncrypted` fájlban is `true`van beállítva , amely meghatározza, hogy a helyi futásidejű visszafejteni a beállításokat, mielőtt azokat. Titkosítsa a helyi beállításokat, hogy csökkentse az értékes információk kiszivárgásának kockázatát. Az Azure-ban az alkalmazásbeállítások mindig titkosítottan tárolódnak. |
| **Függvény végrehajtása most** | Manuálisan elindítja az időzítő által aktivált függvényt az [Azure-ban.](functions-bindings-timer.md) Ez a parancs tesztelésre szolgál. Ha többet szeretne tudni anem HTTP-függvények azure-beli aktiválásáról, olvassa [el a Nem HTTP-aktivált függvény manuális futtatása.](functions-manually-run-non-http.md) |
| **A project inicializálása vs kóddal való használatra** | Hozzáadja a szükséges Visual Studio Code projektfájlokat egy meglévő Functions projekthez. Ezzel a paranccsal a Core Tools eszközzel létrehozott projekttel dolgozhat. |
| **Az Azure Functions alapvető eszközeinek telepítése vagy frissítése** | Telepíti vagy frissíti az [Azure Functions Core Tools]eszközt, amely a függvények helyi futtatására szolgál. |
| **Ismételt üzembe helyezés**  | Lehetővé teszi a projektfájlok újratelepítését egy csatlakoztatott Git-tárházból egy adott azure-beli üzembe helyezésre. A Visual Studio-kód helyi frissítéseinek újbóli közzétételéhez [tegye közzé újra a projektet.](#republish-project-files) |
| **Beállítások átnevezése** | Módosítja egy meglévő függvényalkalmazás-beállítás kulcsnevét az Azure-ban. Ez a parancs nincs hatással a local.settings.json fájl beállításaira. Miután átnevezte a beállításokat az Azure-ban, le kell [töltenie ezeket a módosításokat a helyi projektbe.](#download-settings-from-azure) |
| **Indítsa újra** | Újraindítja a függvényalkalmazást az Azure-ban. A frissítések telepítése is újraindítja a függvényalkalmazást. |
| **AzureWebJobsStorage beállítása**| Beállítja az `AzureWebJobsStorage` alkalmazásbeállítás értékét. Ezt a beállítást az Azure Functions nek kell megadnia. Akkor van beállítva, amikor egy függvényalkalmazás jön létre az Azure-ban. |
| **Kezdés** | Elindítja a leállított függvényalkalmazást az Azure-ban. |
| **Streamelési naplók indítása** | Elindítja a függvényalkalmazás streamelési naplóit az Azure-ban. A streamelési naplókat az Azure távoli hibaelhárítása során használhatja, ha közel valós időben szeretné látni a naplózási adatokat. További információ: [Streamelési naplók](#streaming-logs). |
| **Leállítás** | Leállít egy függvényalkalmazást, amely az Azure-ban fut. |
| **Streamelési naplók leállítása** | Leállítja a függvényalkalmazás streamelési naplóit az Azure-ban. |
| **Váltás bővítőhely-beállításként** | Ha engedélyezve van, biztosítja, hogy egy alkalmazásbeállítás megmarad egy adott központi telepítési helyhez. |
| **Az Azure Functions alapvető eszközeinek eltávolítása** | Eltávolítja az Azure Functions core eszközöket, amelyek a bővítmény által megkövetelt. |
| **Helyi beállítások feltöltése** | Feltölti a beállításokat a local.settings.json fájlból a kiválasztott függvényalkalmazásba az Azure-ban. Ha a helyi fájl titkosítva van, akkor a rendszer visszafejti, feltölti és újra titkosítja. Ha vannak olyan beállítások, amelyek ütköző értékeket tartalmaznak a két helyen, a program kéri a folytatás módját. A parancs futtatása előtt mentse a local.settings.json fájl módosításait. |
| **Véglegesítés megtekintése a GitHubon** | Megmutatja a legújabb véglegesítést egy adott központi telepítésben, amikor a függvényalkalmazás egy tárházhoz csatlakozik. |
| **Telepítési naplók megtekintése** | Megmutatja a naplókat egy adott központi telepítés a függvényalkalmazás az Azure-ban. |

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Functions alapvető eszközeiről, olvassa el az [Azure Functions alapvető eszközeinek használata.](functions-run-local.md)

Ha többet szeretne tudni a .NET osztálytárak ként való függvények fejlesztéséről, olvassa el az [Azure Functions C# fejlesztői útmutató című témakört.](functions-dotnet-class-library.md) Ez a cikk az Azure Functions által támogatott különböző típusú kötések deklarálásához attribútumok használatára mutató példákra mutató hivatkozásokat is tartalmaz.

[Azure Functions-bővítmény a Visual Studio Code-hoz]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Az Azure Functions alapvető eszközei]: functions-run-local.md
