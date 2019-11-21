---
title: Create an OpenAPI definition for a serverless API using Azure API Management
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
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Create an OpenAPI definition for a serverless API using Azure API Management

REST APIs are often described using an OpenAPI definition. Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését.

Ebben az oktatóanyagban létrehoz egy függvényt, amely megállapítja, hogy egy szélturbina sürgősségi javítása költséghatékony-e. You then create an OpenAPI definition for the function app using [Azure API Management](../api-management/api-management-key-concepts.md) so that the function can be called from other apps and services.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Függvény létrehozása az Azure-ban
> * Generate an OpenAPI definition using Azure API Management
> * A definíció tesztelése a függvény meghívásával
> * Download the OpenAPI definition

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A function app lets you group functions as a logical unit for easier management, deployment, scaling, and sharing of resources.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>A függvény létrehozása

This tutorial uses an HTTP triggered function that takes two parameters:

* The estimated time to make a turbine repair, in hours.
* The capacity of the turbine, in kilowatts. 

A függvény kiszámolja, hogy mennyibe kerül a javítás, és hogy a turbina 24 óra alatt mennyi bevételt tudna termelni. TO create the HTTP triggered function in the [Azure portal](https://portal.azure.com).

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Select **In-portal** > **Continue**.

1. Select **More templates...** , then select **Finish and view templates**

1. Select HTTP trigger, type `TurbineRepair` for the function **Name**, choose `Function` for **[Authentication level](functions-bindings-http-webhook.md#http-auth)** , and then select **Create**.  

    ![Create HTTP function for OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Replace the contents of the run.csx C# script file with the following code, then choose **Save**:

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

1. To test the function, click **Test** at the far right to expand the test tab. Enter the following value for the **Request body**, and then click **Run**.

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

Most már van egy olyan függvénye, amely megállapítja a sürgősségi javítások költséghatékonyságát. Next, you generate an OpenAPI definition for the function app.

## <a name="generate-the-openapi-definition"></a>Az OpenAPI-definíció létrehozása

Most már készen áll arra, hogy létrehozza az OpenAPI-definíciót.

1. Select the function app, then in **Platform features**, choose **API Management** and select **Create new** under **API Management**.

    ![Choose API Management in Platform Features](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Use the API Management settings as specified in the table below the image.

    ![Create new API Management service](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name (Név)** | Globálisan egyedi név | A name is generated based on the name of your function app. |
    | **Előfizetés** | Az Ön előfizetése | The subscription under which this new resource is created. |  
    | **[Erőforráscsoport](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | The same resource as your function app, which should get set for you. |
    | **Hely** | USA nyugati régiója | Choose the West US location. |
    | **Szervezet neve** | Contoso | The name of the organization used in the developer portal and for email notifications. |
    | **Rendszergazdai e-mail** | your email | Email that received system notifications from API Management. |
    | **Tarifacsomag** | Consumption (preview) | Consumption tier is in preview and isn't available in all regions. For complete pricing details, see the [API Management pricing page](https://azure.microsoft.com/pricing/details/api-management/) |

1. Choose **Create** to create the API Management instance, which may take several minutes.

1. Select **Enable Application Insights** to send logs to the same place as the function application, then accept the remaining defaults and select **Link API**.

1. The **Import Azure Functions** opens with the **TurbineRepair** function highlighted. Choose **Select** to continue.

    ![Import Azure Functions into API Management](media/functions-openapi-definition/import-function-openapi.png)

1. In the **Create from Function App** page, accept the defaults and select **Create**

    ![Create from Function App](media/functions-openapi-definition/create-function-openapi.png)

The API is now created for the function.

## <a name="test-the-api"></a>Test the API

Before you use the OpenAPI definition, you should verify that the API works.

1. On the **Test** tab of your function, select **POST** operation.

1. Enter values for **hours** and **capacity**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Click **Send**, then view the HTTP response.

    ![Test function API](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Download the OpenAPI definition

If your API works as expected, you can download the OpenAPI definition.

1. Select **Download OpenAPI definition** at the top of the page.
   
   ![OpenAPI-definíció letöltése](media/functions-openapi-definition/download-definition.png)

2. Open the downloaded JSON file and review the definition.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Következő lépések

You have used API Management integration to generate an OpenAPI definition of your functions. You can now edit the definition in API Management in the portal. You can also [learn more about API Management](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Edit the OpenAPI definition in API Management](../api-management/edit-api.md)
