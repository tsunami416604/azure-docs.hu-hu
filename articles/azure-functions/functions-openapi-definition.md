---
title: A függvények felfedése az OpenAPI segítségével az Azure API Management használatával
description: Hozzon létre egy OpenAPI definíciót, amely lehetővé teszi más alkalmazások és szolgáltatások számára, hogy meghívják a függvényt az Azure-ban.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9465209467c83f7de075d16e724459c307d55bd3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77210208"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>OpenAPI-definíció létrehozása kiszolgáló nélküli API-hoz az Azure API Management használatával

A REST API-kat gyakran egy OpenAPI-definíció használatával írják le. Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését.

Ebben az oktatóanyagban létrehoz egy függvényt, amely megállapítja, hogy egy szélturbina sürgősségi javítása költséghatékony-e. Ezután hozzon létre egy OpenAPI-definíciót a függvényalkalmazáshoz az [Azure API Management](../api-management/api-management-key-concepts.md) használatával, hogy a függvény más alkalmazásokból és szolgáltatásokból hívható legyen.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Függvény létrehozása az Azure-ban
> * OpenAPI-definíció létrehozása az Azure API Management használatával
> * A definíció tesztelése a függvény meghívásával
> * Az OpenAPI-definíció letöltése

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi a függvények logikai egységként történő csoportosítását az erőforrások egyszerűbb kezelése, üzembe helyezése, méretezése és megosztása érdekében.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>A függvény létrehozása

Ez az oktatóanyag egy HTTP-aktivált függvényt használ, amely két paramétert vesz igénybe:

* A becsült idő, hogy a turbina javítás, órákban.
* A turbina kapacitása kilowattban. 

A függvény kiszámolja, hogy mennyibe kerül a javítás, és hogy a turbina 24 óra alatt mennyi bevételt tudna termelni. A HTTP-aktivált függvény létrehozása az [Azure Portalon:](https://portal.azure.com)

1. Bontsa ki a **+** függvényalkalmazást, és jelölje ki a **Funkciók**gomb elemet. Válassza **a Portálon** > lévő**Folytatás**lehetőséget.

1. Válassza a **További sablonok lehetőséget...** majd válassza **a Befejezés és megtekintés sablonok lehetőséget.**

1. Válassza a HTTP-eseményindító lehetőséget, írja `TurbineRepair` be a függvény **nevét,** `Function` válassza a **[Hitelesítési szint](functions-bindings-http-webhook-trigger.md#http-auth)** lehetőséget, majd válassza a **Létrehozás lehetőséget.**  

    ![HTTP-függvény létrehozása az OpenAPI-hoz](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Cserélje le a run.csx C# parancsfájl tartalmát a következő kódra, majd válassza a **Mentés gombot:**

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Ez a függvény a `Yes` vagy `No` üzenetet adja vissza, ezzel jelezve, hogy a turbina sürgősségi javítása költséghatékony-e. Emellett megjeleníti a turbina által képviselt bevételi lehetőséget és a javítási költséget.

1. A funkció teszteléséhez kattintson a **Teszt** a jobb szélen a **Request body**tesztlap kibontásához. **Run**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![A függvény tesztelése az Azure Portalon](media/functions-openapi-definition/test-function.png)

    A válasz törzse a következő értéket adja vissza.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Most már van egy olyan függvénye, amely megállapítja a sürgősségi javítások költséghatékonyságát. Ezután létrehoz egy OpenAPI-definíciót a függvényalkalmazáshoz.

## <a name="generate-the-openapi-definition"></a>Az OpenAPI-definíció létrehozása

Most már készen áll arra, hogy létrehozza az OpenAPI-definíciót.

1. Jelölje ki a függvényalkalmazást, majd a **Platform funkciói**ban válassza az **API Management** lehetőséget, és válassza az Új **létrehozása lehetőséget** az **API Management**csoportban.

    ![API-kezelés kiválasztása a platformfunkciókban](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Használja az API Management beállításait a kép alatti táblázatban megadottak szerint.

    ![Új API Management szolgáltatás létrehozása](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Név** | Globálisan egyedi név | A függvényalkalmazás neve alapján létrejön egy név. |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amely alatt az új erőforrás létrejön. |  
    | **[Erőforráscsoport](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Ugyanaz az erőforrás, mint a függvényalkalmazás, amelynek be kell állítania az Ön számára. |
    | **Helyen** | USA nyugati régiója | Válassza ki az USA nyugati telephelyét. |
    | **Szervezet neve** | Contoso | A fejlesztői portálon és az e-mailes értesítésekben használt szervezet neve. |
    | **Rendszergazdai e-mail** | az e-mail címe | Az API Management rendszerértesítéseit kapott e-mail. |
    | **Tarifacsomag** | Felhasználás (előzetes verzió) | A felhasználási szint előzetes verzióban érhető el, és nem érhető el minden régióban. A teljes díjszabási részleteket az [API Management díjszabási oldalán](https://azure.microsoft.com/pricing/details/api-management/) találja. |

1. A **Létrehozás** lehetőséget választva hozza létre az API Management-példányt. Ez több percet is igénybe vehet.

1. Válassza **az Application Insights engedélyezése** lehetőséget, ha a naplókat ugyanarra a helyre szeretné küldeni, mint a függvényalkalmazást, majd fogadja el a fennmaradó alapértelmezett értékeket, és válassza az API **csatolása**lehetőséget.

1. Az **Azure Functions importálása** a **kiemelve turbinajavítás** i. Válassza a **Kijelölés** elemet a folytatáshoz.

    ![Azure-függvények importálása az API Management szolgáltatásba](media/functions-openapi-definition/import-function-openapi.png)

1. A **Létrehozás függvényalkalmazásból** lapon fogadja el az alapértelmezett értékeket, és válassza a **Létrehozás lehetőséget.**

    ![Létrehozás függvényalkalmazásból](media/functions-openapi-definition/create-function-openapi.png)

Az API most jön létre a függvényhez.

## <a name="test-the-api"></a>Az API tesztelése

Az OpenAPI-definíció használata előtt ellenőrizze, hogy az API működik-e.

1. A funkció **Teszt** lapján válassza a **POST** művelet lehetőséget.

1. **Órák** és **kapacitás** értékeinek megadása

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Kattintson a **Küldés**gombra, majd tekintse meg a HTTP-választ.

    ![TESZT függvény API](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Az OpenAPI-definíció letöltése

Ha az API a várt módon működik, letöltheti az OpenAPI-definíciót.

1. A lap tetején válassza az **OpenAPI-definíció letöltése** lehetőséget.
   
   ![OpenAPI-definíció letöltése](media/functions-openapi-definition/download-definition.png)

2. Nyissa meg a letöltött JSON-fájlt, és tekintse át a definíciót.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>További lépések

Az API Management-integrációval létrehozta a függvények OpenAPI-definícióját. Most már szerkesztheti a definíciót az API Management a portálon. Az API Management szolgáltatásról is [többet tudhat meg.](../api-management/api-management-key-concepts.md)

> [!div class="nextstepaction"]
> [Az OpenAPI-definíció szerkesztése az API Management ben](../api-management/edit-api.md)
