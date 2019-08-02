---
title: Azure Functions létrehozása és üzembe helyezése a Pythonban a Visual Studio Code-ban
description: A Visual Studio Code bővítmény használata a Azure Functionshoz kiszolgáló nélküli függvények létrehozása a Pythonban és üzembe helyezése az Azure-ban.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: f5591a3e0ca73649b1ffc51c75aa95e86e286768
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639086"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>A Python üzembe helyezése a Visual Studio Code-Azure Functions

Ebben az oktatóanyagban a Visual Studio Code és a Azure Functions bővítmény használatával hozzon létre egy kiszolgáló nélküli HTTP-végpontot a Python segítségével, és adjon hozzá egy kapcsolatot (vagy "kötést") a tárolóhoz. Azure Functions futtatja a kódot egy kiszolgáló nélküli környezetben anélkül, hogy virtuális gépet kellene kiépíteni vagy webalkalmazást közzétennie. A Visual Studio Code Azure Functions-bővítménye jelentősen leegyszerűsíti a függvények használatának folyamatát azáltal, hogy automatikusan kezeli a számos konfigurációs szempontot.

Ha problémákat tapasztal az oktatóanyag lépéseivel kapcsolatban, szeretnénk hallani a részleteket. Az egyes szakaszok végén található **probléma** gombra kattintva részletes visszajelzéseket küldhet.

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure-előfizetés](#azure-subscription).
- [A Visual Studio Code Azure functions](#visual-studio-code-python-and-the-azure-functions-extension) bővítménnyel.
- A [Azure functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Azure-előfizetés

Ha nem rendelkezik Azure-előfizetéssel, [Regisztráljon most](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) egy 30 napos ingyenes, $200 értékű Azure-Kredittel a szolgáltatások bármilyen kombinációjának kipróbálásához.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>A Visual Studio Code, a Python és a Azure Functions bővítmény

Telepítse a következő szoftvereket:

- A Python 3.6. x a Azure Functions által megkövetelt módon. A [Python 3.6.8](https://www.python.org/downloads/release/python-368/) a legújabb 3.6. x verzió.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [Python bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a [Visual Studio Code Python-oktatóanyagban leírt módon – előfeltételek](https://code.visualstudio.com/docs/python/python-tutorial).
- A [Azure functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Általános információkat a [vscode-Azurefunctions GitHub-adattárban](https://github.com/Microsoft/vscode-azurefunctions)találhat.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Kövesse az operációs rendszerének útmutatását a [Azure functions Core Toolssal végzett munkához](functions-run-local.md#v2). Maguk az eszközök a .NET Core-ban vannak írva, és az alapszintű eszközcsomag a legjobb, ha a Node. js csomagkezelő, a NPM, ezért a .NET Core-t és a Node. js-t is telepíteni kell, még a Python-kód esetében is. A .NET Core-követelmény azonban a "bővítmény-csomagok" használatával kerülhető el a fenti dokumentációban leírtak szerint. Bármilyen esetben csak egyszer kell telepítenie ezeket az összetevőket, amely után a Visual Studio Code automatikusan rákérdez a frissítések telepítésére.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Miután telepítette a functions bővítményt, jelentkezzen be az Azure-fiókjába **az Azure-ba való navigálással: Functions** Explorer, válassza a **Bejelentkezés az Azure**-ba lehetőséget, majd kövesse az utasításokat.

![Bejelentkezés az Azure-ba a Visual Studio Code használatával](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

A bejelentkezést követően ellenőrizze, hogy az Azure-előfizetés e-mail fiókja megjelenik-e az állapotsorban:

![A Visual Studio Code állapotjelző sáv, amely az Azure-fiókot mutatja](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

Az előfizetéshez hozzárendelt név is megjelenik az **Azure-ban: Functions** Explorer ("elsődleges" az alábbi képen):

![Az előfizetéseket megjelenítő Visual Studio Code Azure App Service Explorer](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Ha a **"nem találja a (z) [előfizetés-azonosító] nevű**előfizetést" hibaüzenet jelenik meg, ennek az lehet az oka, hogy a proxy mögött van, és nem érhető el az Azure API. Konfigurálja `HTTP_PROXY` és`HTTPS_PROXY` környezeti változókat a proxy adataival a terminálon:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

Annak ellenőrzéséhez, hogy az összes Azure functions-eszköz telepítve van-e, nyissa meg a Visual Studio Code Command **paletta (F1) parancsot, majd válassza ki a terminált: Hozzon létre új** integrált terminál-parancsot, és a terminál megnyitása után futtassa `func`a parancsot:

![Azure Functions alapvető eszközök előfeltételeinek ellenőrzése](media/tutorial-vs-code-serverless-python/check-prereqs.png)

A Azure Functions emblémával kezdődő kimenet (a kimenetet felfelé kell görgetni) jelzi, hogy a Azure Functions Core Tools jelen vannak.

Ha a `func` parancs nem ismerhető fel, ellenőrizze, hogy a Azure functions Core Tools telepítési mappája tartalmazza-e a PATH környezeti változót.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>A függvény létrehozása

1. A Azure Functions kódja a functions projektbenvan kezelve, amelyet először a kód létrehozása előtt hoz létre. Az **Azure-ban: Functions** Explorer (a bal oldalon az Azure ikon használatával nyílik meg), válassza az **új projekt** parancs ikont, vagy nyissa meg a Command paletta ( **F1) parancsot, és válassza a Azure functions: Új projekt**létrehozása.

    ![Új projekt létrehozása gomb a functions Explorerben](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. Az alábbi utasításokban:

    | Kérdés | Érték | Leírás | 
    | --- | --- | --- |
    | A projekthez tartozó mappa megadása | Aktuális megnyitott mappa | Az a mappa, amelyben létre kívánja hozni a projektet. Előfordulhat, hogy létre szeretné hozni a projektet egy almappában. |
    | Válasszon nyelvet a Function app-projekthez | **Python** | A függvényhez használandó nyelv, amely meghatározza a kódhoz használt sablont. |
    | Válasszon sablont a projekt első függvényéhez | **HTTP-trigger** | A HTTP-triggert használó függvények akkor futnak le, amikor HTTP-kérelem történik a függvény végpontján. (A Azure Functions számos más eseményindítóval rendelkezik. További információért lásd: [Mire használhatom a functions szolgáltatást?](functions-overview.md#what-can-i-do-with-functions) |
    | Adja meg a függvény nevét | HttpExample | A név olyan almappában használatos, amely tartalmazza a függvény kódját a konfigurációs adat mellett, valamint meghatározza a HTTP-végpont nevét is. Használja az "HttpExample" értéket ahelyett, hogy elfogadja az alapértelmezett "HTTPTrigger" elemet, hogy megkülönböztesse magát a függvényt az triggerből. |
    | Engedélyszint | **Névtelen** | A névtelen hitelesítés mindenki számára nyilvánosan elérhetővé teszi a funkciót. |
    | Válassza ki, hogyan szeretné megnyitni a projektet? | **Megnyitás az aktuális ablakban** | Megnyitja a projektet az aktuális Visual Studio Code-ablakban. |

1. Rövid idő elteltével egy üzenet jelzi, hogy az új projekt létrejött. A **Explorerben**megjelenik a függvényhez létrehozott almappa, a Visual Studio Code pedig megnyitja az  *\_ \_\_init\_.* a (z) alapértelmezett függvény kódját tartalmazó fájlt:

    [![Új Python functions-projekt létrehozásának eredménye](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Ha a Visual Studio Code azt jelzi, hogy nincs **kiválasztva  *\_ \_Python-tolmács az init\_\_.* a parancssori felület megnyitásakor, nyissa meg a parancssort (F1), majd válassza a Python: Válassza a értelmező** parancs lehetőséget, majd válassza ki a virtuális környezetet a `.env` helyi mappában (amely a projekt részeként lett létrehozva). A környezetnek a Python 3.6 x-alapúnak kell lennie, amelyet az [Előfeltételek](#prerequisites)között korábban feljegyzett.
    >
    > ![A projekttel létrehozott virtuális környezet kiválasztása](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> Ha ugyanabban a projektben egy másik függvényt szeretne létrehozni, használja az **Azure-ban a **create Function** parancsot: Functions** Explorer, vagy nyissa meg a Command paletta (F1) **, és válassza ki a Azure functions: Függvény** létrehozása parancs. Mindkét parancs rákérdez a függvény nevére (amely a végpont neve), majd létrehoz egy almappát az alapértelmezett fájlokkal.
>
> ![Új Function parancs az Azure-ban: Functions Explorer](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>A kód fájljainak vizsgálata

Az újonnan létrehozott függvény almappájában három fájl található  *\_:\_ \_az init\_.* a (z) függvény kódja, a *function. JSON* a függvényt írja le Azure functions és *sample. dat* egy minta adatfájl. Ha szeretné, törölheti a *sample. dat* fájlt, mivel az csak azt mutatja, hogy más fájlokat is hozzáadhat az almappához.

Nézzük meg először a *function. JSON* fájlt, majd a kódot az  *\_ \_\_init\_.* a......

### <a name="functionjson"></a>function.json

A function. JSON fájl biztosítja a Azure Functions végponthoz szükséges konfigurációs információkat:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

A `scriptFile` tulajdonság azonosítja a kód indítási fájlját, és a kódnak tartalmaznia kell egy nevű `main`Python-függvényt. A kódot több fájlba is felhasználhatja, amíg az itt megadott fájl `main` függvényt tartalmaz.

A `bindings` elem két objektumot tartalmaz, egyet a beérkező kérések leírására, a másikat pedig a http-válasz leírására. A bejövő kérelmek (`"direction": "in"`) esetében a függvény válaszol a HTTP Get vagy post kérelmekre, és nem igényel hitelesítést. A válasz (`"direction": "out"`) egy olyan http-válasz, amely a `main` Python függvényből származó bármilyen értéket ad vissza.

### <a name="initpy"></a>\_\_init.py\_\_

Új függvény létrehozásakor Azure functions alapértelmezett Python-kódot biztosít az  *\_init\_\_. \_* a:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

A kód fontos részei a következők:

- `func` Importálnia`azure.functions`kell a alkalmazást; a naplózási modul importálása nem kötelező, de ajánlott.
- A kötelező `main` Python-függvény fogad `func.request` egy nevű `req`objektumot, és egy típusú `func.HttpResponse`értéket ad vissza. Az objektumok képességeiről a funkcióban olvashat bővebben [. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) és a függvény [. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python) -referenciák.
- `main` Ezután a törzs feldolgozza a kérést, és létrehoz egy választ. Ebben az esetben a kód egy `name` paramétert keres az URL-ben. Ha ezt elmulasztja, ellenőrzi, hogy a kérelem törzse JSON `func.HttpRequest.get_json`-t (using) tartalmaz- `name` e, és hogy `get` a JSON tartalmaz-e értéket ( `get_json`a által visszaadott JSON-objektum metódusának használatával).
- Ha a rendszer megtalál egy nevet, a kód a "Hello" karakterláncot adja vissza a hozzáfűzött névvel. Ellenkező esetben hibaüzenetet ad vissza.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Helyi hibakeresés

1. A functions projekt létrehozásakor a Visual Studio Code bővítmény létrehoz egy indítási konfigurációt `.vscode/launch.json` is, amely egyetlen, a **Python-függvényekhez csatolt**nevű konfigurációt tartalmaz. Ez a konfiguráció azt jelenti, hogy az F5 billentyű lenyomásával vagy a projekt elindításához használhatja a hibakeresési Explorert:

    ![A functions indítási konfigurációját bemutató hibakereső Explorer](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. A hibakereső indításakor megnyílik egy terminál, amely a Azure Functions kimenetét mutatja, beleértve az elérhető végpontok összegzését is. Az URL-cím eltérő lehet, ha a "HttpExample" névvel nem rendelkező nevet használ:

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. A **CTRL + kattintás** vagy a **cmd + kattintson** az URL-címre a Visual Studio Code **kimenet** ablakban egy böngésző megnyitásához az adott címre, vagy indítsa el a böngészőt, és illessze be ugyanezt az URL-címet. Mindkét esetben a végpont `api/<function_name>`, ebben az esetben. `api/HttpExample` Mivel azonban ez az URL-cím nem tartalmazza a name paramétert, a böngészőablakban csak az "adja meg a nevet a lekérdezési karakterláncban vagy a kérelem törzsében", a kódban megadott elérési útnak megfelelően.

1. Most próbálja meg hozzáadni a name paramétert a használathoz, `http://localhost:7071/api/HttpExample?name=VS%20Code`például a (z) és a böngészőablakban a "Hello Visual Studio code!" üzenetet, amely azt mutatja, hogy futtatta a kód elérési útját.

1. Ha a name értéket egy JSON-kérelem törzsében szeretné átadni, olyan eszközt használhat, mint például a curl a JSON inline használatával:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    Másik lehetőségként hozzon létre egy fájlt `{"name":"Visual Studio Code"}` *, például a* következőt:, `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`és használja az parancsot.

1. A függvény hibakeresésének teszteléséhez állítson be egy töréspontot a sorban `name = req.params.get('name')` , amely újra beolvassa az URL-címre, és egy kérést küld újra. A Visual Studio Code debuggernek le kell állnia az adott sorban, így megvizsgálhatja a változókat, és áttekintheti a kódot. (Az alapszintű hibakeresés rövid áttekintését lásd: [Visual Studio Code oktatóanyag – a hibakereső konfigurálása és futtatása](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger).)

1. Ha meggyőződött arról, hogy a funkciót helyileg tesztelte, állítsa le a hibakeresőt **(a** > hibakeresés**leállítása** vagy a hibakeresési eszköztáron a leválasztási parancs leállítása paranccsal).

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Üzembe helyezés az Azure Functionsben

Ezekben a lépésekben a functions bővítmény használatával hozzon létre egy Function alkalmazást az Azure-ban, valamint más szükséges Azure-erőforrásokat. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, telepítése és megosztása érdekében. Emellett Azure Storage-fiókra is szükség van az adatkezeléshez és a [üzemeltetési tervekhez](functions-scale.md#hosting-plan-support). Minden ilyen erőforrás egyetlen erőforráscsoport keretében van rendszerezve.

1. Az **Azure-ban: Functions** Explorer, válassza ki a **központi telepítés függvényalkalmazás** parancsot, vagy nyissa meg a parancssort (F1 **), és válassza ki a Azure functions: Függvényalkalmazás** parancs üzembe helyezése. A Function alkalmazás az Azure-ban futtatja a Python-projektet.

    ![Üzembe helyezés függvényalkalmazás parancsban](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. Ha a rendszer kéri, válassza az **új Függvényalkalmazás létrehozása az Azure-ban**lehetőséget, és adjon meg egy egyedi nevet az Azure-ban (általában a személyes vagy a cég nevét használja más egyedi azonosítókkal együtt). a betűket, számokat és kötőjeleket is használhat. Ha korábban létrehozott egy függvényalkalmazás, a neve megjelenik a beállítások listájában.

1. A bővítmény a következő műveleteket hajtja végre, amelyeket megfigyelheti a Visual Studio Code felugró üzeneteiben és a **kimeneti** ablakban (a folyamat eltarthat néhány percig):

    - Hozzon létre egy erőforráscsoportot az adott név használatával (kötőjelek eltávolítása).
    - Ebben az erőforráscsoportban hozza létre a Storage-fiókot, a üzemeltetési tervet és a Function alkalmazást. Alapértelmezés szerint a rendszer létrehoz egy [felhasználási tervet](functions-scale.md#consumption-plan) . A függvények dedikált csomagban való futtatásához engedélyeznie kell a [speciális létrehozási beállításokkal](functions-develop-vs-code.md)történő közzétételt.
    - Telepítse a kódot a Function alkalmazásba.

    Az **Azure: A** functions Explorer az előrehaladást is megjeleníti:

    ![Üzembe helyezési állapotjelző az Azure-ban: Functions Explorer](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. Az üzembe helyezés befejezése után az Azure Functions-bővítmény három további művelethez tartozó gombokkal rendelkező üzenetet jelenít meg:

    ![A sikeres telepítést jelző üzenet további műveletekkel](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    A **stream-naplók** és a **feltöltési beállítások**a következő részekben találhatók. A **kimenet megtekintéséhez**tekintse meg az alábbi 5. lépést.

1. Az üzembe helyezést követően a **kimeneti** ablak az Azure nyilvános végpontját is megjeleníti:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Ezt a végpontot használhatja a helyileg végzett tesztek futtatására, URL-paraméterek és/vagy JSON-adatok kérésével a kérelem törzsében. A nyilvános végpont eredményeinek meg kell egyezniük a korábban tesztelt helyi végpont eredményeivel.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Naplók streamelése

A log streaming támogatása jelenleg fejlesztés alatt áll, ahogy az a [589](https://github.com/microsoft/vscode-azurefunctions/issues/589) -es számú, a Azure functions-bővítményre vonatkozó probléma. Az üzembe helyezési üzenet előugró ablakának **stream-naplók** gombja végül az Azure-ban a Visual Studio Code-hoz csatlakozik a log kimenethez. A log streamet a **Azure functions** Explorerben is elindíthatja és leállíthatja, ha a jobb gombbal a functions projektre kattint, és kiválasztja a **streaming naplók indítása** vagy a **folyamatos átviteli naplók leállítása**lehetőséget.

Jelenleg azonban ezek a parancsok még nem működnek. A log streaming egy böngészőben, a következő parancs futtatásával, a functions `<app_name>` alkalmazás Azure-beli nevével való lecserélésekor érhető el:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Helyi beállítások szinkronizálása az Azure-ba

A telepítési üzenet felugró **beállításai** gombja a *helyi. Settings. JSON* fájlon végrehajtott módosításokat alkalmazza az Azure-ba. A parancs a **Azure functions** Explorerben is meghívható a functions projekt csomópontjának kibontásával, a jobb gombbal az **Alkalmazásbeállítások**elemre, majd a **helyi beállítások feltöltése**lehetőségre. A **Azure functions kiválasztásához használhatja a parancs palettáját is: Töltse fel a** helyi beállítások parancsot.

A beállítások feltöltése frissíti a meglévő beállításokat, és hozzáadja a *Local. Settings. JSON*fájlban definiált új beállításokat. A feltöltés nem távolítja el az Azure-ból származó olyan beállításokat, amelyek nem szerepelnek a helyi fájlban. A beállítások eltávolításához bontsa ki az **alkalmazások beállítások** csomópontot a **Azure functions** Explorerben, kattintson a jobb gombbal a beállításra, majd válassza a **beállítás törlése..** . lehetőséget. A beállításokat közvetlenül a Azure Portal szerkesztheti is.

A portálon vagy az **Azure Explorerben** a *Local. Settings. JSON* fájlon elvégzett módosítások alkalmazásához kattintson a jobb gombbal az **Alkalmazásbeállítások** csomópontra, és válassza a **Távoli beállítások letöltése...** parancsot. A **Azure functions kiválasztásához használhatja a parancs palettáját is: Töltse le a** távoli beállítások parancsot.

## <a name="add-a-second-function"></a>Második függvény hozzáadása

Az első üzembe helyezés után módosításokat végezhet a kódban, például további függvények hozzáadásával és az ugyanazon functions alkalmazásba való újbóli üzembe helyezéssel.

1. Az **Azure-ban: Functions** Explorer, válassza a **create Function** parancsot, **vagy használja a Azure functions: Függvény** létrehozása a parancs palettáján. A függvény következő részleteinek megadása:

    - Sablon: HTTP-eseményindító
    - Név: "DigitsOfPi"
    - Engedélyezési szint: Névtelen

1. A Visual Studio Code fájlkezelőben egy olyan almappát tartalmaz, amely ismét tartalmazza az  *\_init\_\_. \_* , a *function. JSON*és a *sample. dat*nevű fájlokat.

1. Cserélje le az  *\_ \_init\_. másolása fájl tartalmát, hogy az megfeleljen a következő kódnak, amely létrehoz egy sztringet, amely a pi értékét az URL-címben megadott számú számjegyre (ez a kód csak URL-paramétert használ)\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Mivel a kód csak a HTTP Get-t támogatja, módosítsa a *function. JSON* fájlt úgy `"get"` , hogy a `"methods"` gyűjtemény csak `"post"`(azaz az eltávolítást) tartalmazza. A teljes fájlnak a következőképpen kell megjelennie:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. Indítsa el a hibakeresőt az F5 billentyű lenyomásával, **vagy a** > hibakeresés megkezdése parancs bejelölésével. A **kimeneti** ablakban most mindkét végpontot meg kell jeleníteni a projektben:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Egy böngészőben, vagy a curl-ból hajtson végre egy `http://localhost:7071/api/DigitsOfPi?digits=125` kérést, és figyelje meg a kimenetet. (Észreveheti, hogy a kód algoritmusa nem teljesen pontos, de a tökéletesítéseket elhagyjuk Önnek!) Ha elkészült, állítsa le a hibakeresőt.

1. Telepítse újra a kódot az **Azure-beli **üzembe helyezés függvényalkalmazás** használatával: Functions** Explorer. Ha a rendszer kéri, válassza ki a korábban létrehozott függvényalkalmazás.

1. Az üzembe helyezés befejeződése után (néhány percet vesz igénybe), a **kimeneti** ablak megjeleníti azokat a nyilvános végpontokat, amelyekkel megismételheti a teszteket.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Kötés hozzáadása az Azure Storage-hoz írt üzenetek írásához

A _kötés_ lehetővé teszi, hogy az adatelérési kód írása nélkül összekösse a függvény kódját az erőforrásokkal, például az Azure Storage szolgáltatással. Egy kötés definiálva van a *function. JSON* fájlban, és képes a bemeneti és a kimeneti adatok ábrázolására is. A függvények több bemeneti és kimeneti kötést is használhatnak, de csak egy triggert. További információ: [Azure functions triggerek és kötések fogalmai](functions-triggers-bindings.md).

Ebben a szakaszban egy tárolási kötést ad hozzá az oktatóanyag korábbi részében létrehozott HttpExample függvényhez. A függvény ezt a kötést használja az üzenetek tárolásához az egyes kérelmekkel. A szóban forgó tároló ugyanazt az alapértelmezett Storage-fiókot használja, amelyet a Function alkalmazás használ. Ha azt tervezi, hogy nagy mennyiségű tárterületet használ, azonban érdemes lehet külön fiókot létrehoznia.

1. Szinkronizálja a Azure functions-projekt távoli beállításait a *Local. Settings. JSON* fájlba a Command paletta megnyitásával és a **Azure functions kiválasztásával: Távoli beállítások**letöltése. Nyissa meg a *Local. Settings. JSON* fájlt, és győződjön `AzureWebJobsStorage`meg arról, hogy a tartalmaz egy értéket. Ez az érték a Storage-fiókhoz tartozó kapcsolatok karakterlánca.

1. A `HttpExample` mappában kattintson a jobb gombbal a *function. JSON*fájlra, válassza a **kötés hozzáadása**elemet:

    ![Kötési parancs hozzáadása a Visual Studio Code Explorerben](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. A Visual Studio Code-ban követett utasításokban válassza ki vagy adja meg a következő értékeket:

    | Kérdés | Megadható érték |
    | --- | --- |
    | Kötési irány beállítása | ki |
    | Kötés kiválasztása a kifelé | Azure Queue Storage |
    | Ez a név szolgál a kötés azonosítására a kódban. | msg |
    | Az az üzenetsor, amelybe az üzenet el lesz küldve | outqueue |
    | Válassza a beállítás lehetőséget a *Local. Settings. JSON* fájlban (a tárolási kapcsolatok megkérdezése) | AzureWebJobsStorage |

1. A beállítások megadása után ellenőrizze, hogy a következő kötés hozzá van-e adva a *function. JSON* fájlhoz:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Most, hogy konfigurálta a kötést, használhatja azt a függvény kódjában. Az újonnan definiált kötés újra megjelenik a kódban argumentumként `main` az  *\_ \_\_init\_.* a................. Például módosíthatja az `msg`  *\_ \_init.HttpExamplefájlt\_a következővel, hogy az megfeleljen az alábbi feltételeknek, ami azt mutatja, hogy az argumentum használatával egy időbélyeggel ellátott üzenetet ír a\_* kérelem. A megjegyzések a konkrét módosításokat ismertetik:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. A módosítások helyi teszteléséhez indítsa el újra a hibakeresőt a Visual Studio Code-ban az F5 billentyű lenyomásával, **vagy a** > hibakeresés megkezdése menüparancs kiválasztásával. A **kimeneti** ablaknak a projektben lévő végpontok megjelenítéséhez hasonlóan kell megjelennie.

1. Egy böngészőben nyissa meg az URL `http://localhost:7071/api/HttpExample?name=VS%20Code` -címet, és hozzon létre egy kérelmet a HttpExample-végpontnak, amely szintén üzenetet ír a várólistára.

1. Annak ellenőrzéséhez, hogy az üzenet az "üzenetsor" várólistára lett-e írva (a kötésben leírtak szerint), a következő három módszer egyikét használhatja:

    1. Jelentkezzen be [](https://portal.azure.com)a Azure Portalba, és navigáljon a functions projektet tartalmazó erőforráscsoporthoz. Az adott erőforráscsoporthoz a helyi területen navigáljon a projekthez tartozó Storage-fiókba, majd navigáljon a **várólisták**között. Ezen az oldalon navigáljon az "üzenetsor" kifejezésre, amely megjeleníti az összes naplózott üzenetet.

    1. Navigáljon és vizsgálja meg a várólistát a Azure Storage Explorerával, amely integrálható a Visual Studióval, ahogy az az [Azure Storage-hoz való kapcsolódás a Visual Studio Code használatával](functions-add-output-binding-storage-queue-vs-code.md)című témakörben leírtak szerint, különösen a [kimeneti várólista vizsgálata](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) szakaszban.

    1. Az Azure CLI-vel kérdezheti le a tárolási várólistát a [tárolási üzenetsor lekérdezése](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue)című cikkben leírtak szerint.
    
1. A felhőben való teszteléshez telepítse újra a kódot az **Azure-beli **üzembe helyezés függvényalkalmazás** használatával: Functions** Explorer. Ha a rendszer kéri, válassza ki a korábban létrehozott függvényalkalmazás. A telepítés befejeződése után (néhány percet vesz igénybe), a **kimeneti** ablak ismét megjeleníti azokat a nyilvános végpontokat, amelyekkel megismételheti a teszteket.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott függvényalkalmazás olyan erőforrásokat tartalmaz, amelyek minimális költségekkel járnak (lásd a [függvények díjszabását](https://azure.microsoft.com/pricing/details/functions/)). Az erőforrások tisztításához kattintson a jobb gombbal a függvényalkalmazás az **Azure-ban: Functions** Explorer és válassza a **függvényalkalmazás törlése**lehetőséget. Látogasson el a [Azure Portalra](https://portal.azure.com)is, válassza az **erőforráscsoportok** lehetőséget a bal oldali navigációs ablaktáblán, válassza ki az oktatóanyag folyamatában létrehozott erőforráscsoportot, majd használja az **erőforráscsoport törlése** parancsot.

## <a name="next-steps"></a>További lépések

Gratulálunk a Python-kód üzembe helyezésének Azure Functions! Most már készen áll arra, hogy több kiszolgáló nélküli függvényt hozzon létre.

Amint azt korábban említettük, további információt a functions bővítménnyel kapcsolatban a GitHub-adattár, a [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions)webhelyén olvashat. A problémák és a hozzájárulások is üdvözlendők.

Olvassa el a [Azure functions áttekintése című témakört](functions-overview.md) , amelyből megismerheti a különböző használható eseményindítókat.

Ha többet szeretne megtudni a Pythonból használható Azure-szolgáltatásokról, beleértve az adattárolást az AI-és Machine Learning-szolgáltatásokkal együtt, látogasson el az [Azure Python fejlesztői](/azure/python/?view=azure-python)központba.

Más Azure-bővítmények is hasznosak lehetnek a Visual Studio Code-hoz. Csak keressen rá az "Azure" kifejezésre a bővítmények Explorerben:

![Azure-bővítmények a Visual Studio Code-hoz](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Néhány népszerű bővítmény a következők:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Eszközök Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
