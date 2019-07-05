---
title: Az Azure Functions Visual Studio Code használatával fejlesztése |} A Microsoft Docs
description: Ismerje meg, hogyan fejlesztheti és tesztelheti az Azure Functions az Azure Functions bővítmény a Visual Studio Code használatával.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: glenga
ms.openlocfilehash: 17550e148ea61eb69a20fc6a3215dfb63b65f18e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452695"
---
# <a name="develop-azure-functions-using-visual-studio-code"></a>Fejlesztés az Azure Functions Visual Studio Code használatával

A [Azure Functions-bővítmény a Visual Studio Code-hoz] lehetővé teszi a helyi fejlesztés és a functions üzembe helyezése az Azure-bA. Ha ez az első és az Azure Functions, többet is megtudhat a [Bevezetés az Azure Functions](functions-overview.md).

Az Azure Functions bővítmény az alábbi előnyöket nyújtja: 

* Szerkesztés, elkészítéséhez és függvények a helyi fejlesztői számítógépen való futtatását. 
* Az Azure Functions-projekt közzététele közvetlenül az Azure-bA. 
* A függvények írási különféle nyelveken a Visual Studio Code előnyei mindegyikével közben. 

A bővítmény a következő nyelveket támogatja az Azure Functions verzió 2.x verziójú futtatókörnyezet használható: 

* [C#lefordított](functions-dotnet-class-library.md) 
* [C#-szkript](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Szükséges, amikor [beállítása C# szkript az alapértelmezett projekt nyelve](#c-script-projects).

Ez a cikk példákat jelenleg csak a Javascripthez (Node.js) és C# függvények osztály.  

Ez a cikk részletesen ismerteti az Azure Functions bővítmény használatával funkciók fejlesztése, és közzéteheti őket az Azure-bA. Ez a cikk előtt kell [Visual Studio Code használatával az első függvény létrehozása](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Ne keverje a helyi fejlesztési ugyanaz a függvényalkalmazás a portál fejlesztési. Amikor közzétesz egy helyi projektből a függvényalkalmazáshoz, a telepítési folyamat felülírja a portálon fejlesztett funkciók.

## <a name="prerequisites"></a>Előfeltételek

Telepítése és futtatása előtt a [Azure Functions bővítmény][azure functions-bővítmény a visual studio code-hoz], az alábbi követelményeknek kell megfelelnie:

* [A Visual Studio Code](https://code.visualstudio.com/) telepítve van az egyik a [által támogatott platformok](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Az előfizetésben jönnek létre más erőforrások, amelyek van szüksége, például az Azure Storage-fiókot, amikor, [közzététele a Visual Studio Code használatával](#publish-to-azure).

> [!IMPORTANT]
> A függvények helyi fejlesztheti és közzététele az Azure-bA indítása és helyi futtatása nélkül. Nincsenek további követelmények: a működési helyben fut, beleértve az Azure Functions Core Tools-automatikus letöltését. További tudnivalókért lásd: [további követelmények helyi futtatásához](#additional-requirements-to-run-locally). 

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

A Functions bővítmény egy függvényalkalmazás projektjét, valamint az első függvény létrehozását teszi lehetővé. A következő lépések bemutatják egy HTTP által aktivált függvény létrehozása az új functions-projekt. [HTTP-eseményindító](functions-bindings-http-webhook.md) legegyszerűbb függvény eseményindító a sablon használatával mutatja be.

1. A **Azure: Függvények**, kattintson a Create Function ikonra.

    ![Függvény létrehozása](./media/functions-develop-vs-code/create-function.png)

1. Válassza ki a függvényalkalmazás projektjét, az a mappát, majd **válasszon egy nyelvet, a függvény projekt**. 

1. Válassza ki a **HTTP-eseményindító** függvénysablon, vagy dönthet úgy, **Kihagyás** függvény nélkül projekt létrehozásához. Bármikor [függvény hozzáadása a projekthez](#add-a-function-to-your-project) egy későbbi időpontban. 

    ![A HTTP-eseményindító sablon kiválasztása](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Típus `HTTPTrigger` a függvény neve és nyomja le az Enter billentyűt, majd válassza ki **függvény** engedélyezési. Ez a jogosultsági szintet kell adnia egy [függvénykulcs](functions-bindings-http-webhook.md#authorization-keys) a függvény végpont hívásakor.

    ![Válassza ki a függvény hitelesítés](./media/functions-develop-vs-code/create-function-auth.png)

    A rendszer létrehoz egy függvényt a választott nyelven a HTTP által indított függvények sablonjának használatával.

    ![A HTTP által indított függvények sablonja a Visual Studio Code-ban](./media/functions-develop-vs-code/new-function-full.png)

A projekt sablont hoz létre egy projektet a választott nyelven, telepíti a szükséges függőségeket. Bármely nyelven az új projekt tartalmaz a következő fájlokat:

* **host.json**: A Functions gazdagép konfigurálását teszi lehetővé. Ezek a beállítások is alkalmazható, ha helyileg és az Azure-ban futó is. További információkért lásd: [host.json referencia](functions-host-json.md).

* **local.settings.json**: Kezeli a függvények helyi futtatás során használt beállításokat. Ezek a beállítások csak akkor használja, amikor helyileg. További információkért lásd: [helyi beállításfájl](#local-settings-file).

    >[!IMPORTANT]
    >Mivel a local.settings.json fájlban a titkos kulcsokat is tartalmazhatnak, a projekt forrásvezérlőből kell zárni.

Ezen a ponton adja hozzá a bemeneti és kimeneti kötés által a függvényt [a function.json fájlban](#javascript-2), vagy [egy paraméter hozzáadása egy C# függvénye osztály](#c-class-library-2).

Emellett [új függvény hozzáadása a projekthez](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Kötési bővítmények telepítése

HTTP- és időzítő eseményindító, kivéve a kötések bővítmény csomagokban vannak megvalósítva. Telepítenie kell a bővítmény csomagjai eseményindítók és kötések, amelyek azt igénylik. Kötési bővítményeket telepíteni módja a projekt nyelvet függ.

### <a name="javascript"></a>JavaScript

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

### <a name="c-class-library"></a>C\# osztálytár

Futtassa a [dotnet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) paranccsal a terminálablakban a projektben van szüksége a bővítmény-csomagok telepítéséhez. A következő példa az Azure Storage-bővítmény, kötéseit Blob, Queue és Table storage, amely telepíti.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

## <a name="add-a-function-to-your-project"></a>Függvény hozzáadása a projekthez

Egy új funkció a Functions az eseményindító előre definiált sablonok használatával hozzáadhat egy meglévő projekt. Adjon hozzá egy eseményindító függvényt, nyomja le az F1 billentyűt a parancskatalógus megnyitásához, majd keresse meg és futtassa a parancsot **Azure Functions: Függvény létrehozása...** . Kövesse az utasításokat az eseményindító-típust választhat, és a szükséges attribútumokkal az eseményindító meghatározása. Ha az eseményindító-hozzáférési kulcsa vagy kapcsolati karakterláncot a szolgáltatáshoz való csatlakozáshoz van szüksége, hogy az készen álljon a függvény az eseményindító létrehozása előtt. 

Ez a művelet eredményei a projekt nyelvet függ:

### <a name="javascript"></a>JavaScript

Új mappa jön létre a projektben egy új function.json fájlt és a JavaScript-kód fájlt tartalmazza.

### <a name="c-class-library"></a>C\# osztálytár

Egy új C# class library (.cs) fájlt adnak hozzá a projekthez.

## <a name="add-input-and-output-bindings"></a>Adja hozzá a bemeneti és kimeneti kötések

Bemeneti és kimeneti kötései hozzáadásával kibontásával, a függvény. Ez a módszer attól függ, hogy a projekt nyelvet. Kötések kapcsolatos további információkért lásd: [Azure Functions eseményindítók és kötések fogalmak](functions-triggers-bindings.md). 

Az alábbi példák connect nevű üzenetsor-tárolóba `outqueue`, ahol a kapcsolati karakterlánc a tárfiók van megadva a `MyStorageConnection` local.settings.json Alkalmazásbeállítás. 

### <a name="javascript"></a>JavaScript

A Visual Studio Code egy kényelmes készletét utasításokat követve adhat hozzá kötéseket a function.json fájlt teszi lehetővé. A kötés létrehozásához kattintson a jobb gombbal (Ctrl + kattintás macOS rendszeren) a `function.json` a függvény-mappában fájlt, és válassza a **kötés hozzáadása...** . 

![Kötés hozzáadása egy meglévő JavaScript-függvény ](media/functions-develop-vs-code/function-add-binding.png)

A következők példa utasításokat követve adja meg az új tároló kimeneti kötést:

| Kérdés | Érték | Leírás |
| -------- | ----- | ----------- |
| **Válassza ki a kötés iránya** | `out` | A kötés egy kimeneti kötés. |
| **Kötés irányú kiválasztása...** | `Azure Queue Storage` | A kötés egy Azure Storage-üzenetsor kötést. |
| **Ebben a kódban a kötés azonosítására használt név** | `msg` | A kötési paraméter a kódban hivatkozott azonosító név. |
| **Az üzenetet küld az üzenetsorba** | `outqueue` | Az üzenetsorba író a kötés neve. Ha a *queueName* nem létezik, a kötést hoz létre, azt először használ. |
| **Válassza ki a "local.setting.json" beállítás** | `MyStorageConnection` | Egy tárfiók kapcsolati karakterláncát tartalmazó alkalmazásbeállítás neve. A `AzureWebJobsStorage` beállítás tartalmazza a tárfiók, függvényalkalmazás létrehozott kapcsolati karakterláncára. |

Ebben a példában a következő kötés adnak hozzá a `bindings` tömb a function.json fájlban:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

A kötés ugyanazon definíció közvetlenül a function.json a is megteheti.

A függvény kódját a `msg` kötés érhető el a `context`, ahogy az alábbi példában:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

További tudnivalókért tekintse meg a [a Queue storage kimeneti kötésének](functions-bindings-storage-queue.md#output---javascript-example) hivatkozást.

### <a name="c-class-library"></a>C\# osztálytár

Frissítse a függvény módot, a következő paraméter hozzáadása a `Run` metódusdefiníciót:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Ez a kód szükséges, hogy adja hozzá a következő `using` utasítást:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

A `msg` paraméter egy `ICollector<T>` típusát, amely egy kimeneti kötés, ha a függvény írt üzenetek gyűjteményét befejeződik. Vegyen fel egy vagy több üzenet a gyűjteményhez, a függvény befejezésekor az üzenetsorba küldött.

További tudnivalókért tekintse meg a [a Queue storage kimeneti kötésének](functions-bindings-storage-queue.md#output---c-example) hivatkozást.

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="publish-to-azure"></a>Közzététel az Azure platformon

A Visual Studio Code lehetővé teszi a függvényprojekt közzétételét közvetlenül az Azure-ba. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe.

Közzététel a Visual Studio Code-ból, ha két üzembe helyezési módszer egyikét használják:

* [Futtatás-a-Package engedélyezve van az üzembe helyezés zip](functions-deployment-technologies.md#zip-deploy): a legtöbb Azure Functions-telepítéshez használt.
* [Külső csomag URL-cím](functions-deployment-technologies.md#external-package-url): Linux-alkalmazások való üzembe helyezéshez használt egy [Használatalapú csomag](functions-scale.md#consumption-plan).

### <a name="quick-function-app-creation"></a>Gyors függvény létrehozása

Alapértelmezés szerint a Visual Studio Code automatikusan előállítja az Azure-erőforrások a függvényalkalmazás számára szükséges értékeket. Ezeket az értékeket a függvényalkalmazás nevét, válassza ki a alapulnak. A projekt közzététele egy új függvényalkalmazáshoz az Azure-ban az alapértelmezett érték használatának példájáért lásd a [a rövid útmutató a cikk a Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Ha szeretne biztosítani a létrehozott erőforrások explicit nevét, engedélyeznie kell közzététele a speciális beállításokkal.

### <a name="enabled-publishing-with-advanced-create-options"></a>Az irányított engedélyezett közzétételi beállítások létrehozása

Hogy a beállítások felett társított létrehozása az Azure Functions alkalmazások, az Azure Functions bővítmény engedélyezése a Speciális beállítások frissítése.

1. Kattintson a **fájl > Beállítások > Beállítások**

1. Haladjon végig **felhasználói beállítások > bővítmények > az Azure Functions**

1. Jelölje be a jelölőnégyzetet a **Azure-függvény: Speciális létrehozása**

### <a name="publish-to-a-new-function-app-in-azure-with-advanced-creation"></a>Egy új függvényalkalmazáshoz az Azure-ban speciális létrehozása és közzététele

Az alábbi lépéseket a projekt közzététele a létrehozott új függvényalkalmazás használ a Speciális beállítások létrehozása.

1. Az a **Azure: Függvények** területen válassza ki az üzembe helyezés, a Függvényalkalmazás ikon.

    ![Függvény-Alkalmazásbeállítások](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Ha nincs bejelentkezve, kéri **jelentkezzen be Azure**. Emellett **hozzon létre egy ingyenes Azure-fiókkal**. Sikeres bejelentkezés után a böngészőből lépjen vissza a Visual Studio Code-ot.

1. Ha több előfizetéssel rendelkezik **válasszon ki egy előfizetést** a függvényalkalmazás, majd válassza ki **+ új Függvényalkalmazás létrehozása az Azure-ban**.

1. Kövesse az utasításokat a következő információkat biztosítja:

    | Kérdés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Válassza ki a függvényalkalmazást az Azure-ban | + Új Függvényalkalmazás létrehozása az Azure-ban | Adjon meg egy globálisan egyedi nevet az új függvényalkalmazást azonosító a következő parancssort, és nyomja le az Enter billentyűt. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`. |
    | Válassza ki az operációs rendszer | Windows | Függvényalkalmazás futtat Windows |
    | Szolgáltatási csomag kiválasztása | Használatalapú csomag | Kiszolgáló nélküli [Használatalapú csomaghoz kötődő üzemeltetés](functions-scale.md#consumption-plan) szolgál. |
    | Válassza ki az új alkalmazás egy modult | A projekt nyelv | A futtatókörnyezet meg kell egyeznie a projekt közzéteszi. |
    | Válasszon ki egy erőforráscsoportot az új erőforrások | Hozzon létre új erőforráscsoportot | A következő parancssorba írja be egy erőforráscsoport-nevet, például `myResourceGroup`, és nyomja le az Entert. Választhat egy meglévő erőforráscsoportot is. |
    | Tárfiók kiválasztása | Új tárfiók létrehozása | A következő rendszer, a típus egy globálisan egyedi nevet az új tárfiók a függvényalkalmazást, és nyomja le az Enter által használt. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Választhat egy meglévő fiókot is. |
    | Válasszon egy helyet új erőforrások | régió | Válasszon ki egy helyet egy, a közelben, vagy a függvények által elért más szolgáltatások közelében található [régióban](https://azure.microsoft.com/regions/). |

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. Válassza ki **nézet kimeneti** erre az értesítésre, létrehozása és üzembe helyezés eredményei megtekintése, beleértve a létrehozott Azure-erőforrások.

## <a name="republish-project-files"></a>Soubory projektu újbóli közzététele

Miután beállította [folyamatos üzembe helyezés](functions-continuous-deployment.md), a függvényalkalmazás Azure-ban mindig frissül, amikor a csatlakoztatott forráshelyen forrásfájljainak frissítésekor. Azt javasoljuk, hogy a fejlesztési eljárás, amíg ismét közzéteszi a projekt fájlok frissítései a Visual Studio Code-ból. 

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

1. A Visual Studio Code-nyomja le az F1 billentyűt a parancskatalógus megnyitásához. A parancskatalógus keresése és kiválasztása `Azure Functions: Deploy to function app...`.

1. Ha nincs bejelentkezve, kéri **jelentkezzen be Azure**. Sikeres bejelentkezés után a böngészőből lépjen vissza a Visual Studio Code-ot. Ha több előfizetéssel rendelkezik **válasszon ki egy előfizetést** , amely tartalmazza a függvényalkalmazást.

1. Az Azure-ban válassza ki a meglévő függvényalkalmazásában. Ha figyelmeztetést kap, a függvényalkalmazás található összes fájl felülírására, válassza ki a **telepítés** nyugtázza a figyelmeztetést, és továbbra is. 

A projekt az újonnan létrehozott, újracsomagolják, és az Azure-bA feltöltve. A meglévő projekt váltotta fel az új csomagot, és a függvényalkalmazás újraindul.

## <a name="get-deployed-function-url"></a>Üzembe helyezett függvény URL-Címének lekérése

Meghívhat egy HTTP által aktivált függvényt, hogy, URL-címét a függvényt, amikor a függvényalkalmazás üzembe kell. Az URL-cím tartalmazza a szükséges [funkcióbillentyűket](functions-bindings-http-webhook.md#authorization-keys). A bővítmény használatával az üzembe helyezett függvényeket ezen URL-címek lekérése.

1. Nyomja le az F1 billentyűt a parancskatalógus megnyitásához, majd keresse meg és futtassa a parancsot **Azure Functions: Függvény URL-Címének másolása**.

1. Kövesse az utasításokat követve válassza ki a függvényalkalmazást az Azure és az adott HTTP-eseményindító szeretne meghívni. 

A függvény URL-cím másolja a vágólapra, bármilyen szükséges kulcsok használatával átadott együtt a `code` lekérdezési paraméter. Egy HTTP-eszköz használatával küldjön el POST kéréseket, vagy egy böngészőben a GET-kérésekhez a távoli függvény.  

## <a name="run-functions-locally"></a>Függvény helyi futtatása

Az Azure Functions bővítmény lehetővé teszi, hogy a functions-projekt helyi fejlesztői számítógépen való futtatását. A helyi modul az ugyanazzal a futtatókörnyezettel, amely a függvényalkalmazás Azure-ban futtatja. A helyi beállítások olvassa a rendszer a [local.settings.json fájljában](#local-settings-file).

### <a name="additional-requirements-to-run-locally"></a>További követelmények helyi futtatásához

Tudják a Functions-projekt futtatása helyileg, akkor is meg kell felelniük ezek a követelmények:

* Verzió telepítése 2.x verzióját a [Azure Functions Core Tools](functions-run-local.md#v2). A Core Tools csomag letöltését és telepítését az az automatikusan a projekt helyi indításakor. A Core Tools tartalmazza a teljes Azure Functions runtime, így a letöltés és telepítés eltarthat egy ideig.

* Telepítse az Ön által választott nyelvhez tartozó követelményeket:

    | Nyelv | Követelmény |
    | -------- | --------- |
    | **C#** | [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[.NET core Parancssorifelület-eszközök](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Java-bővítmény a hibakeresőt](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6-os +](https://www.python.org/downloads/)|

    <sup>*</sup>Aktív LTS és karbantartási LTS verzió (8.11.1 és ajánlott 10.14.1).

### <a name="configure-the-project-to-run-locally"></a>A projekt helyi futtatását konfigurálása

A Functions futtatókörnyezete egy Azure Storage-fiókot az összes trigger esetében eltérő HTTP és webhookok belső célokra használja. Ez azt jelenti, hogy be kell a **Values.AzureWebJobsStorage** egy érvényes Azure-Tárfiók kapcsolati sztringje kulcs.

Ez a szakasz a [Azure Storage-bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) a [Microsoft Azure Storage Explorer](https://storageexplorer.com/) való csatlakozáshoz, és a tárolási kapcsolati karakterlánc lekéréséhez.   

A tárfiók kapcsolati sztringje beállítása:

1. A Visual Studióban nyissa meg a **Cloud Explorer**, bontsa ki a **Tárfiók** > **a Storage-fiók**, majd **tulajdonságok**, és másolja a **elsődleges kapcsolati karakterlánc** értéket.

2. A projektben nyissa meg a local.settings.json fájlban, és az értékét állítsa be a **AzureWebJobsStorage** kulcs a kapcsolati karakterlánc másolta.

3. Ismételje meg az előző lépésben az egyedi kulcs hozzáadása a **értékek** bármilyen más kapcsolatot, a functions által igényelt az tömb.

További információkért lásd: [helyi beállításfájl](#local-settings-file).

### <a name="debugging-functions-locally"></a>A függvények helyi hibakeresés  

A függvények hibakeresése, nyomja le az F5. Ha még nem töltötte le a [Core Tools][az azure functions core tools], erre kéri. Amikor Core Tools telepítve van és fut, kimeneti megjelennek a terminálon. Ez megegyezik a futó `func host start` Core Tools parancsot a terminálon, de a további hozhat létre, feladatok és a egy csatolt hibakereső.  

A projekt fut a függvények is indíthat, mint az Azure-ba való telepítésekor. Hibakeresési módban futtatja, töréspontokat a kiválasztott az elvárt módon vannak találati a Visual Studio Code-ban.

A kérelem URL-CÍMÉT a HTTP-eseményindítók nem jelenik meg a kimenetet a terminálon. Funkcióbillentyűk HTTP-eseményindítók nem használ a helyi futtatás során. További információkért lásd: [stratégiát a kódot tesztelés az Azure Functions](functions-test-a-function.md).  

További tudnivalókért lásd: [együttműködik az Azure Functions Core Tools][az azure functions core tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem települnek át automatikusan az Azure-bA a projekt közzétételekor. Közzététel befejezése után a beállítást, a függvényalkalmazás local.settings.json található beállításokat közzététele az Azure-ban adják meg. További tudnivalókért lásd: [alkalmazás nastavení Publikování](#publish-application-settings).

Az értékek **kapcsolati Sztringjei** soha nem kerülnek közzétételre.

A függvény alkalmazások beállításokat értékeket is elolvashatja a kódban környezeti változókként. További információkért tekintse meg a környezeti változók szakaszban az alábbi nyelvspecifikus referencia cikkek:

* [C# előre lefordított](functions-dotnet-class-library.md#environment-variables)
* [C# script (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Az Azure-ban nastavení aplikace

A beállítások a projektben a local.settings.json fájlban lehet ugyanaz, mint az alkalmazásbeállításokat a függvényalkalmazáshoz az Azure-ban. A local.settings.json ad hozzá minden beállítást is meg kell adni a függvényalkalmazáshoz az Azure-ban. Ezek a beállítások nem lesznek feltöltve automatikusan, a projekt közzétételekor. Hasonlóképpen, azokat a beállításokat, a függvényalkalmazást hoz létre [a portálon](functions-how-to-use-azure-function-app-settings.md#settings) le kell tölteni a helyi projekthez.

### <a name="publish-application-settings"></a>Alkalmazásbeállítások közzététele

A szükséges beállításokat a függvényalkalmazáshoz az Azure-ban közzéteendő legegyszerűbb módja a használja a **feltöltési beállítások** hivatkozás, amely a projekt sikeres közzététel után jelenik meg.

![Üzembe helyezés kész feltöltési nastavení aplikace](./media/functions-develop-vs-code/upload-app-settings.png)

Beállítások használatával is közzéteheti a `Azure Functions: Upload Local Setting` a parancskatalógus parancsot. Egyes beállítások lettek hozzáadva az Azure-ban alkalmazásbeállításokhoz használatával a `Azure Functions: Add New Setting...` parancsot. 

> [!TIP]
> Mindenképpen mentse a local.settings.json fájllal közzététel előtt.

Ha a helyi fájl titkosítva van, visszafejtése, közzé, és újból titkosítja. Ha beállítások létezik eltérő értékek mindkét helyen, a rendszer megkéri, válassza ki a folytatásról.

A meglévő alkalmazás beállítások megtekintéséhez a **Azure: Függvények** az előfizetéséhez, a függvényalkalmazás kibontásával terület és **Alkalmazásbeállítások**.

![Függvény Alkalmazásbeállítás megtekintése a Visual Studio Code-ban](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Letöltési beállítások az Azure-ból

Ha az alkalmazás beállításai az Azure-ban létrehozott, letöltheti azokat a local.settings.json fájlban. használatával a `Azure Functions: Download Remote Settings...` parancsot. 

Csakúgy, mint ha a helyi fájl titkosítva van feltöltésével, visszafejtése, frissítve, és újból titkosítja. Ha beállítások létezik eltérő értékek mindkét helyen, a rendszer megkéri, válassza ki a folytatásról.

## <a name="monitoring-functions"></a>Figyelési funkciók

Ha Ön [helyileg történő futtatása](#run-functions-locally), naplóadatok adatfolyamként történő a terminálkonzolt. A functions-projektet egy függvényalkalmazást az Azure-ban történő futtatásakor naplóadatok is kaphat. Akár csatlakozik folyamatos átviteli naplók az Azure-ban, közel valós idejű-naplóadatok megtekintéséhez, vagy engedélyezheti az Application Insights egy teljes körű ismeretekkel módját a függvényalkalmazás működése.

### <a name="streaming-logs"></a>Folyamatos átviteli naplók

Az alkalmazások fejlesztése során hasznos gyakran közel valós idejű naplóinformációkat tekinthet. A stream a függvények által létrehozott naplófájlok megtekintéséhez. Az alábbi kimeneti példa folyamatos átviteli naplók a kérés egy HTTP által aktivált függvény a következő:

![Folyamatos átviteli naplók HTTP-trigger kimenete](media/functions-develop-vs-code/streaming-logs-vscode-console.png) 

További tudnivalókért lásd: [folyamatos átviteli naplók](functions-monitoring.md#streaming-logs). 

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Folyamatos átviteli naplók csak egyetlen példány az a funkciók gazdagép támogatja. A függvény átméretezi több példányra, ha más a példányok adatait nem láthatók a naplózási adatfolyam. A [élő metrikák Stream](../azure-monitor/app/live-stream.md) Application Insights több példányt támogat. Bár még a közel valós idejű Stream analytics is alapján [változásához](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Az ajánlott úgy a függvények végrehajtásának figyelése, hogy a függvényalkalmazás integrálása az Azure Application Insights. Ha függvényalkalmazást hoz létre az Azure Portalon, az integráció, készen áll alapértelmezés szerint. Azonban amikor a Visual Studio közzététel során a függvényalkalmazást hoz létre, az integráció az Azure-ban a függvényalkalmazásban nem fejeződött.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

További tudnivalókért lásd: [figyelése az Azure Functions](functions-monitoring.md).

## <a name="c-script-projects"></a>C\# parancsfájl-projektek

Alapértelmezés szerint minden C# projektek jönnek létre [ C# osztály kódtárprojektek lefordított](functions-dotnet-class-library.md). Ha ehelyett inkább együttműködve C# parancsfájl-projektek, ki kell választania C# szkript az alapértelmezett nyelve az Azure Functions bővítmény között.

1. Kattintson a **fájl > Beállítások > Beállítások**.

1. Haladjon végig **felhasználói beállítások > bővítmények > az Azure Functions**.

1. Válasszon **C #Script** a **Azure-függvény: A projekt nyelvi**.

Ezen a ponton az alapul szolgáló Core Tools hívások közé tartozik a `--csx` beállítás, amely állít elő, és közzéteszi a C# szkript (.csx) soubory projektu. A megadott alapértelmezett nyelv, projektek alapértelmezés szerint az összes létrehozott C# parancsfájl-projektek. Nem kell megadnia a projekt nyelv megadása alapértelmezés szerint be van állítva. Más nyelvű projektek létrehozása, módosítsa ezt a beállítást, vagy távolítsa el a felhasználói settings.json fájlból. Miután eltávolította ezt a beállítást, a rendszer újra megkéri válassza ki a nyelvet, ha projektet hoz létre.

## <a name="command-palette-reference"></a>Paletta parancsdokumentációja

Az Azure Functions bővítmény az Azure területen hasznos grafikus felületet biztosít a függvényalkalmazásokat az Azure-ban való interakcióhoz. Ugyanazokat a funkciókat is az as-parancsok a parancskatalógus (F1) érhető el. A következő Azure Functions-specifikus parancsok használhatók:

|Azure Functions-parancs  | Leírás  |
|---------|---------|
|**Új beállítások hozzáadása...**  |  Létrehoz egy új alkalmazás-beállítás az Azure-ban. További tudnivalókért lásd: [alkalmazás nastavení Publikování](#publish-application-settings). Emellett szükség lehet [töltse le ezt a beállítást, hogy a helyi beállításokban](#download-settings-from-azure). |
| **Központi telepítés forrásának beállítása...** | A függvényalkalmazás Azure-ban csatlakozzon egy helyi Git-tárház. További tudnivalókért lásd: [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md). |
| **Kapcsolódás GitHub-adattár...** | Csatlakozás a függvényalkalmazás egy GitHub-adattárból. |
| **Függvény URL-Címének másolása** | Lekérdezi az távoli URL-címét egy HTTP által aktivált Azure-ban futó függvény. További tudnivalókért lásd: hogyan [üzembe helyezett függvény URL-címére](#get-deployed-function-url). |
| **Függvényalkalmazás létrehozása az Azure-ban...** | Létrehoz egy új függvényalkalmazást az előfizetésében, az Azure-ban. További tudnivalókért lásd: hogyan [közzététele egy új függvényalkalmazáshoz az Azure-ban](#publish-to-azure).        |
| **Visszafejtés beállításai** | Visszafejtéséhez szükséges [helyi beállítások](#local-settings-file) használatával titkosítja `Azure Functions: Encrypt Settings`.  |
| **Függvényalkalmazás törlése...** | Meglévő függvényalkalmazással eltávolítja az előfizetés az Azure-ban. Ha más alkalmazás nem található az App Service-csomag, felhőszolgáltatására, amely túl törlésének lehetőségét. További erőforrások, például a storage-fiókok és -erőforráscsoportok, a rendszer nem törli. Összes erőforrás eltávolítására, akkor ehelyett [törölje az erőforráscsoportot](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). A folyamatot nem befolyásolja a helyi projektet. |
|**Függvény törlése...**  | Eltávolít egy meglévő függvényt egy függvényalkalmazást az Azure-ban. A törlés nem befolyásolja a helyi projekthez, mert, Ehelyett érdemes eltávolítani, a függvény helyben, majd [újbóli közzétételét a projekt](#republish-project-files). |
| **Delete Proxy...** | Az Azure Functions proxy eltávolítja a függvényalkalmazás Azure-ban. Proxyk kapcsolatos további információkért lásd: [használata az Azure Functions-proxyk](functions-proxies.md). |
| **Beállítás törlése...** | Töröl egy függvény Alkalmazásbeállítás az Azure-ban. Nincs hatással a local.settings.json fájlban található beállításokat. |
| **Tárház leválasztása...**  | Távolítsa el a [folyamatos üzembe helyezés](functions-continuous-deployment.md) egy függvényalkalmazást az Azure-ban és a egy verziókövetési tárházat közötti kapcsolat. |
| **Töltse le a távoli beállítások...** | Beállítások tölt le a kiválasztott függvényalkalmazáshoz az Azure-ban a local.settings.json fájlban. Ha a helyi fájl titkosítva van, visszafejtése, frissítve, és újból titkosítja. Ha beállítások létezik eltérő értékek mindkét helyen, a rendszer megkéri, válassza ki a folytatásról. Győződjön meg arról, hogy mentett módosítások a local.settings.json fájllal a parancs futtatása előtt. |
| **Beállítások szerkesztése...** | Módosítja az Azure-ban egy meglévő függvény Alkalmazásbeállítás értékét. Nincs hatással a local.settings.json fájlban található beállításokat.  |
| **Beállítások titkosítása** | Az egyes elemek titkosítja a `Values` Pole a [helyi beállítások](#local-settings-file). Ebben a fájlban `IsEncrypted` értéke is `true`, amely tájékoztatja, hogy a helyi futásidejű beállításokat visszafejteni a használatuk előtt. A titkosítást értékes információkat kiszivárgását kockázatának csökkentése érdekében a helyi beállításokat. Az Azure-ban alkalmazás-beállítások mindig tárolt titkosított. |
| **Most már a függvény végrehajtása** | Elindítja egy [időzítő által aktivált függvény](functions-bindings-timer.md) az Azure-ban manuálisan tesztelési célokra. -HTTP funkciók az Azure-ban elindítása kapcsolatos további információkért lásd: [manuális futtatása egy nem HTTP által aktivált függvény](functions-manually-run-non-http.md). |
| **A VS Code segítségével inicializálni a projekt...** | A Visual Studio Code projekt szükséges fájlokat ad hozzá egy meglévő Functions-projektet. Ez a parancs segítségével dolgozhat a Core Tools használatával létrehozott projekt. |
| **Frissítés az Azure Functions Core Tools telepítése** | Telepíti és frissíti a [Az Azure Functions Core Tools] használt helyi futtatásához. |
| **Ismételt üzembe helyezése**  | Lehetővé teszi az ismételt üzembe helyezése a projektfájlokat csatlakoztatott Git-adattárból adott üzembe helyezés az Azure-ban. Helyi frissítéseket, a Visual Studio Code-ban közzé [tegye közzé újra a projekt](#republish-project-files). |
| **Nevezze át a beállításokat...** | Az Azure-ban egy meglévő függvény alkalmazásbeállítási kulcs neve megváltozik. Nincs hatással a local.settings.json fájlban található beállításokat. Átnevezés beállítások az Azure-ban, után kell [töltse le változások a helyi projektet](#download-settings-from-azure). |
| **Indítsa újra a** | A függvényalkalmazás újraindítja az Azure-ban. Frissítések letöltésére is a függvényalkalmazás indul újra. |
| **AzureWebJobStorage beállítása...**| Értékét a `AzureWebJobStorage` nastavení aplikace. Ez a beállítás az Azure functions által igényelt, és van beállítva, ha a függvényalkalmazás létrehozása az Azure-ban. |
| **Start** | Elindít egy leállított függvényalkalmazást az Azure-ban. | 
| **Indítsa el a folyamatos átviteli naplók** | Elindítja a függvényalkalmazás a naplók streamelése az Azure-ban. Használja a folyamatos átviteli naplók távoli hibaelhárítás az Azure-ban, ha meg kell tekintenie ezt az információt közel valós idejű során. További tudnivalókért lásd: [folyamatos átviteli naplók](#streaming-logs). |
| **állj** | Lefelé leáll egy Azure-ban futó függvényalkalmazást. |
| **Állítsa le a folyamatos átviteli naplók** | A naplók streamelése az Azure-ban a függvényalkalmazás leáll. |
| **Tárhelybeállítás, be-vagy kikapcsolása** | Ha engedélyezve van, biztosítja, hogy, hogy alkalmazás-beállítás továbbra is fennáll a megadott üzembe helyezési ponthoz. |
| **Távolítsa el az Azure Functions Core Tools** | Eltávolítja az Azure Functions Core Tools, amelyek szükségesek a bővítményt. |
| **Töltse fel a helyi beállításokban...** | Tölt fel a kiválasztott függvényalkalmazáshoz az Azure-ban a local.settings.json fájlban található beállításokat. Ha a helyi fájl titkosítva van, visszafejtése, feltöltött, és újból titkosítja. Ha beállítások létezik eltérő értékek mindkét helyen, a rendszer megkéri, válassza ki a folytatásról. Győződjön meg arról, hogy mentett módosítások a local.settings.json fájllal a parancs futtatása előtt. |
| **Nézet véglegesítése a Githubban** | Megjeleníti a legutóbbi véglegesítést adott üzembe helyezés a függvényalkalmazás a tárházba való csatlakozáskor. |
| **Telepítési naplók megtekintése** | Bemutatja, a naplókban az adott üzembe helyezés a függvényalkalmazáshoz az Azure-ban. |

## <a name="next-steps"></a>További lépések

Az Azure Functions Core Tools kapcsolatos további információkért lásd: [kódot és az Azure functions helyi tesztelése](functions-run-local.md).

Is, mint a .NET-osztálytárak fejlesztésével kapcsolatos további tudnivalókért lásd: [Azure Functions C# – fejlesztői referencia](functions-dotnet-class-library.md). Ez a cikk példákat deklarálásához a kötéseket az Azure Functions támogatja a különböző típusú attribútumok használata is hivatkozik.    

[Azure Functions-bővítmény a Visual Studio Code-hoz]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Az Azure Functions Core Tools]: functions-run-local.md