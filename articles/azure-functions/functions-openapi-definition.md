---
title: Az Azure API Management-függvény OpenAPI definíció létrehozása
description: Hozzon létre egy OpenAPI definíciót, amely lehetővé teszi más alkalmazások és szolgáltatások számára, hogy meghívják a függvényt az Azure-ban.
services: functions
keywords: OpenAPI, Swagger, felhőalkalmazás, felhőszolgáltatások,
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 3ad304bc8f038d4009352dae72d70079828c26ba
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141574"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>Az Azure API Management-függvény OpenAPI definíció létrehozása

REST API-k leírása gyakran OpenAPI-definícióval. Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését.

Ebben az oktatóanyagban létrehoz egy függvényt, amely megállapítja, hogy egy szélturbina sürgősségi javítása költséghatékony-e. Ezután létrehoz egy OpenAPI-definícióját a függvény használatával [Azure API Management](../api-management/api-management-key-concepts.md) úgy, hogy a függvényt más alkalmazások és szolgáltatások is meghívhassák.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Függvény létrehozása az Azure-ban
> * Az Azure API Management segítségével a OpenAPI-definíció létrehozása
> * A definíció tesztelése a függvény meghívásával

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi, csoportosítsa a függvényeket egy logikai egységet az egyszerűbb felügyelete, telepítése, méretezése és az erőforrások megosztása.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>A függvény létrehozása

Ebben az oktatóanyagban egy HTTP által aktivált függvényt, amely a két paraméter szükséges:

* A becsült ideje, hogy a turbina javításához (óra).
* A kapacitás a turbina kilowattban. 

A függvény kiszámolja, hogy mennyibe kerül a javítás, és hogy a turbina 24 óra alatt mennyi bevételt tudna termelni. HOZHAT létre a HTTP által aktivált függvény a [az Azure portal](https://portal.azure.com).

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Válassza ki **a portálon** > **továbbra is**.

1. Válassza ki **további sablonok...** , majd **Befejezés és nézet sablonok**

1. Írja be a HTTP-eseményindító kiválasztása `TurbineRepair` a függvény **neve**, válassza a `Function` a  **[hitelesítési szint](functions-bindings-http-webhook.md#http-auth)**, majd válassza ki  **Hozzon létre**.  

    ![Az OpenAPI HTTP-függvény létrehozása](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Cserélje le a run.csx tartalmát C# parancsfájlt a következő kódra, majd kattintson a **mentése**:

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

1. A függvény teszteléséhez kattintson a jobb szélen a **Tesztelés** gombra, amely kibontja a tesztelési lapot. A **Kérelem törzse** mezőben adja meg a következő értéket, majd kattintson a **Futtatás** parancsra.

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

Most már van egy olyan függvénye, amely megállapítja a sürgősségi javítások költséghatékonyságát. Következő lépésként hozza létre a függvényalkalmazás OpenAPI-definíció.

## <a name="generate-the-openapi-definition"></a>Az OpenAPI-definíció létrehozása

Most már készen áll arra, hogy létrehozza az OpenAPI-definíciót.

1. Válassza ki a függvényalkalmazást, és válassza ki **platformfunkciók**, **minden beállítás**

    ![A függvény tesztelése az Azure Portalon](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Görgessen le, majd kattintson a **az API Management** > **új létrehozása** egy új API Management szolgáltatáspéldány létrehozása.

    ![Hivatkozás függvény](media/functions-openapi-definition/link-apim-openapi.png)

1. Az API Management-beállításokat használja az ábra alatti táblázatban megadott.

    ![Új API Management szolgáltatás létrehozása](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name (Név)** | Globálisan egyedi név | Egy nevet a függvényalkalmazás neve alapján generált. |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben az új erőforrás jön létre. |  
    | **[Erőforráscsoport](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Ugyanazt az erőforrást a függvényalkalmazás, amely kell első beállítva az Ön számára. |
    | **Hely** | USA nyugati régiója | Válassza ki az USA nyugati régiójában |
    | **Szervezet neve** | Contoso | A fejlesztői portálon, és az e-mail értesítések a szervezet neve. |
    | **Rendszergazdai e-mail** | az e-mailben | E-mailt, amely az API Management rendszer értesítést kapott. |
    | **Tarifacsomag** | Használat (előzetes verzió) | Teljes körű díjszabási részletek: a [az API Management-díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/api-management/) |
    | **Application Insights** | A példány | A függvényalkalmazás által használt azonos Application Insights használatához. |

1. Válasszon **létrehozás** hozhat létre az API Management-példány, ami eltarthat néhány percig.

1. Válassza ki **engedélyezze az Application Insights** elküldeni a naplókat a függvény alkalmazás ugyanezen a helyen, majd fogadja el a fennmaradó alapértelmezett beállításokat, és válasszon **hivatkozás API**.

1. A **importálása az Azure Functions** megnyílik a **TurbineRepair** függvény vannak kiemelve. Válasszon **kiválasztása** folytatásához.

    ![Az Azure Functions importálni az API Management](media/functions-openapi-definition/import-function-openapi.png)

1. Az a **létrehozás függvényalkalmazásból** lapon elfogadhatja az alapértelmezett beállításokat, és válassza **létrehozása**

    ![Létrehozás függvényalkalmazásból](media/functions-openapi-definition/create-function-openapi.png)

Az API-t a függvény megtörtént.

## <a name="test-the-openapi-definition"></a>Az OpenAPI-definíció tesztelése

Ahhoz, hogy az API-definíció, ellenőrizze, hogy működik.

1. Az a **teszt** fülre a select függvény **POST** művelet

1. Adjon meg értéket a **óra** és **kapacitás**

```json
{
"hours": "6",
"capacity": "2500"
}
```

1. Kattintson a **küldése**, majd tekintse meg a HTTP-válasz.

    ![Függvény API tesztelése](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Függvény létrehozása az Azure-ban
> * Az Azure API Management segítségével a OpenAPI-definíció létrehozása
> * A definíció tesztelése a függvény meghívásával

Folytassa a következő témakörben az API Management ismertetése.

> [!div class="nextstepaction"]
> [API Management](../api-management/api-management-key-concepts.md)
