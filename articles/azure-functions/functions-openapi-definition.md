---
title: OpenAPI-definíció létrehozása kiszolgáló nélküli API-hoz az Azure API Management használatával
description: Hozzon létre egy OpenAPI definíciót, amely lehetővé teszi más alkalmazások és szolgáltatások számára, hogy meghívják a függvényt az Azure-ban.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 659c05b3d31f5673e95cb27f10eaa8bd872e4be6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226821"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>OpenAPI-definíció létrehozása kiszolgáló nélküli API-hoz az Azure API Management használatával

A REST API-kat gyakran egy OpenAPI-definíció használatával írják le. Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését.

Ebben az oktatóanyagban létrehoz egy függvényt, amely megállapítja, hogy egy szélturbina sürgősségi javítása költséghatékony-e. Ezután létrehoz egy OpenAPI-definíciót a Function alkalmazáshoz az [Azure API Management](../api-management/api-management-key-concepts.md) használatával, hogy a függvény más alkalmazásokból és szolgáltatásokból is meghívható legyen.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Függvény létrehozása az Azure-ban
> * OpenAPI-definíció létrehozása az Azure API Management használatával
> * A definíció tesztelése a függvény meghívásával
> * A OpenAPI-definíció letöltése

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>A függvény létrehozása

Ez az oktatóanyag egy HTTP által aktivált függvényt használ, amely két paramétert vesz igénybe:

* A turbina javításának várható ideje (óra).
* A turbina kapacitása kilowattban. 

A függvény kiszámolja, hogy mennyibe kerül a javítás, és hogy a turbina 24 óra alatt mennyi bevételt tudna termelni. A HTTP által aktivált függvény létrehozása a [Azure Portalban](https://portal.azure.com).

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények+ elem melletti**  gombra. Válassza **a portálon belüli** > **Folytatás**lehetőséget.

1. Válassza a **további sablonok...** lehetőséget, majd kattintson a **Befejezés és a sablonok megtekintése** elemre.

1. Válassza a HTTP-trigger lehetőséget, írja be a `TurbineRepair` nevet a függvény **neveként**, válassza a `Function` lehetőséget a **[hitelesítési szint](functions-bindings-http-webhook.md#http-auth)** beállításnál, majd válassza a **Létrehozás**lehetőséget.  

    ![HTTP-függvény létrehozása a OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Cserélje le a Run. CSX C# parancsfájl tartalmát a következő kódra, majd válassza a **Mentés**lehetőséget:

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

1. A függvény teszteléséhez kattintson a jobb szélen a **tesztelés** gombra a teszt lap kibontásához. adja meg a **kérelem törzsének**következő értékét, majd kattintson a **Futtatás**gombra.

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

Most már van egy olyan függvénye, amely megállapítja a sürgősségi javítások költséghatékonyságát. Ezután létrehoz egy OpenAPI-definíciót a Function alkalmazáshoz.

## <a name="generate-the-openapi-definition"></a>Az OpenAPI-definíció létrehozása

Most már készen áll arra, hogy létrehozza az OpenAPI-definíciót.

1. Válassza ki a Function alkalmazást, majd a **platform szolgáltatásainál**válassza a **API Management** lehetőséget, majd válassza az **új létrehozása** lehetőséget **API Management**alatt.

    ![API Management kiválasztása a platform szolgáltatásaiban](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Használja a rendszerkép alatti táblázatban megadott API Management beállításokat.

    ![Új API Management szolgáltatás létrehozása](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name (Név)** | Globálisan egyedi név | A rendszer a függvény alkalmazásának neve alapján hozza létre a nevet. |
    | **Előfizetés** | Az Ön előfizetése | Az az előfizetés, amely alatt az új erőforrást létrehozták. |  
    | **[Erőforráscsoport](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | A Function alkalmazással megegyező erőforrás, amelyet be kell állítani. |
    | **Hely** | USA nyugati régiója | Válassza ki az USA nyugati régióját. |
    | **Szervezet neve** | Contoso | A fejlesztői portálon használt szervezet neve és e-mail-értesítések. |
    | **Rendszergazdai e-mail** | e-mail-címe | A API Managementtól kapott rendszerértesítéseket tartalmazó e-mailek. |
    | **Tarifacsomag** | Felhasználás (előzetes verzió) | A használati szintek előzetes verzióban érhetők el, és nem érhető el minden régióban. A díjszabással kapcsolatos részletekért tekintse meg a [API Management díjszabási oldalát](https://azure.microsoft.com/pricing/details/api-management/) . |

1. Válassza a **Létrehozás** lehetőséget a API Management példány létrehozásához, amely több percet is igénybe vehet.

1. Válassza az **engedélyezés Application Insights** lehetőséget, hogy a függvény alkalmazással megegyező helyre küldje a naplókat, majd fogadja el a fennmaradó alapértékeket, és válassza az **API csatolása**lehetőséget.

1. Megnyílik az **importálási Azure functions** a **TurbineRepair** függvénnyel kiemelve. A folytatáshoz válassza a **Kiválasztás lehetőséget** .

    ![Azure Functions importálása a API Managementba](media/functions-openapi-definition/import-function-openapi.png)

1. A **Létrehozás a függvényalkalmazás** lapon fogadja el az alapértelmezett értékeket, és válassza a **Létrehozás** lehetőséget.

    ![Létrehozás függvényalkalmazás](media/functions-openapi-definition/create-function-openapi.png)

Ekkor létrejön az API a függvényhez.

## <a name="test-the-api"></a>Az API tesztelése

A OpenAPI-definíció használata előtt ellenőrizze, hogy az API működik-e.

1. A függvény **teszt** lapján válassza a művelet **küldése** lehetőséget.

1. Adja meg az **órák** és a **kapacitás** értékét

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Kattintson a **Küldés**gombra, majd tekintse meg a http-választ.

    ![Function API tesztelése](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>A OpenAPI-definíció letöltése

Ha az API a várt módon működik, letöltheti a OpenAPI-definíciót.

1. A lap tetején válassza a **OpenAPI-definíció letöltése** lehetőséget.
   
   ![OpenAPI-definíció letöltése](media/functions-openapi-definition/download-definition.png)

2. Nyissa meg a letöltött JSON-fájlt, és tekintse át a definíciót.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

API Management integrációt használt a függvények OpenAPI-definíciójának létrehozásához. Most már szerkesztheti a definíciót API Management a portálon. [További tudnivalókat a API Managementról](../api-management/api-management-key-concepts.md)is kaphat.

> [!div class="nextstepaction"]
> [A OpenAPI-definíció szerkesztése a API Managementban](../api-management/edit-api.md)
