---
title: "Egy OpenAPI definíció függvény létrehozása |} Microsoft Docs"
description: "Hozzon létre egy OpenAPI-definíciót, amely lehetővé teszi, hogy egyéb alkalmazások és szolgáltatások, a függvény hívása az Azure-ban."
services: functions
keywords: "OpenAPI, Swagger, felhőalapú alkalmazások, a felhőalapú szolgáltatások,"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/09/2017
ms.author: mblythe; glenga
ms.custom: mvc
ms.openlocfilehash: 1ad23a098ee0482b3c8c853ab5cee989f752a101
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="create-an-openapi-definition-for-a-function"></a>Egy függvény OpenAPI definíció létrehozása
REST API-k gyakran ismerteti egy OpenAPI definíció használatával (korábbi néven egy [Swagger](http://swagger.io/) fájl). Ez a definíció milyen műveleteket elérhetők az API-k, és hogy a kérés- és az API-adatszerkezet információkat tartalmaz.

Ebben az oktatóanyagban létrehoz egy függvényt, amely meghatározza, hogy a szél turbina egy helyreállítási költséghatékony. A függvény alkalmazás egy OpenAPI definíció majd, hogy a függvény hívása az egyéb alkalmazások és szolgáltatások létrehozása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure-függvény létrehozása
> * Az OpenAPI eszközökkel OpenAPI-definíció létrehozása
> * A további metaadatainak módosítása
> * A definíció a függvény tesztelése

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. Logikai egységet az egyszerűbb felügyelet érdekében a központi telepítés, méretezés és erőforrás-megosztás funkciók csoportosítása függvény alkalmazás segítségével. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]


## <a name="create-the-function"></a>A függvény létrehozása

Ez az oktatóanyag használja egy indított HTTP-függvény, amely két paramétert fogad: becsült ideje a turbina létrehozásához javítása (órákban); és (a kilowattban) turbinás kapacitását. A függvény majd kiszámítja mennyi javítási ára, és mekkora a bevétel a turbinás lehetett létrehozni egy 24 órás időszakban.

1. Bontsa ki a függvény alkalmazást, és válassza ki a  **+**  gombra **funkciók**. Ha ez az első függvény a függvényalkalmazásban, jelölje ki az **Egyéni függvény** lehetőséget. Ez megjeleníti a függvénysablonok teljes készletét. 

    ![Függvények gyors létrehozásának oldala az Azure Portalon](media/functions-openapi-definition/add-first-function.png)

2. Írja be a keresőmezőbe, `http` majd **C#** a HTTP-eseményindító sablon. 
 
    ![A HTTP-eseményindítóval kiválasztása](./media/functions-openapi-definition/select-http-trigger-portal.png)

3. Típus `TurbineRepair` a függvény **neve**, válassza a `Function` a  **[hitelesítési szint](functions-bindings-http-webhook.md#http-auth)**, majd válassza ki **létrehozása**.  

    ![Az indított HTTP-függvény létrehozása](./media/functions-openapi-definition/select-http-trigger-portal-2.png)

1. Cserélje le a run.csx fájl tartalmát a következő kódra, majd kattintson az **mentése**:

    ```c#
    using System.Net;

    const double revenuePerkW = 0.12; 
    const double technicianCost = 250; 
    const double turbineCost = 100;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {   

        //Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();
        int hours = data.hours;
        int capacity = data.capacity;

        //Formulas to calculate revenue and cost
        double revenueOpportunity = capacity * revenuePerkW * 24;  
        double costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;

        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        }

        return req.CreateResponse(HttpStatusCode.OK, new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix         
        }); 
    }
    ```
    Ez a függvény kód üzenetet adja vissza `Yes` vagy `No` költséghatékony,-e egy helyreállítási, valamint a turbinás jelölő bevétel lehetőséget, és a költségeket a turbinás javításához jelzi. 

1. A függvény teszteléséhez kattintson **tesztelése** bontsa ki a teszt lap jobb szélén. A következő értéket adja meg a **Request body**, és kattintson a **futtatása**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![A függvény tesztelése az Azure-portálon](media/functions-openapi-definition/test-function.png)

    A következő értéket adja vissza a válasz törzsében.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Most már rendelkezik egy függvényt, amely meghatározza, hogy a költséghatékonyság vészhelyzeti javítások. A következő Ön hozza létre, és a függvény alkalmazás OpenAPI módosítása.

## <a name="generate-the-openapi-definition"></a>A OpenAPI definíció létrehozása

Most már készen áll a OpenAPI definíciót létrehozni. Ez a definíció használhatja más Microsoft-technológiák, például az API-alkalmazásokat, [PowerApps](functions-powerapps-scenario.md) és [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), valamint harmadik féltől származó fejlesztői eszközök, például az [Postman](https://www.getpostman.com/docs/importing_swagger) és [számos további csomag](http://swagger.io/tools/).

1. Válassza ki a csak a *műveletek* , hogy az API támogatja (Ez az eset BLOGBEJEGYZÉS). Így a létrehozott API-definíció tisztító.

    1. Az a **integráció** lap az új HTTP-eseményindítóval függvény, a módosítás **engedélyezett HTTP-metódus** való **kijelölt módszerek**

    1. A **kijelölt HTTP-metódus**, kapcsolja ki minden beállítást, kivéve **POST**.

        ![A kijelölt HTTP-metódusok](media/functions-openapi-definition/selected-http-methods.png)
        
1. Kattintson a függvény alkalmazás neve (például **függvény-bemutató-energia**) > **Platform funkciói** > **API-definíció**.

    ![API-definíció](media/functions-openapi-definition/api-definition.png)

1. Az a **API-definíció** lapra, majd **függvény**.

    ![API definition forrás](media/functions-openapi-definition/api-definition-source.png)

    Ez a lépés lehetővé teszi, hogy az függvény alkalmazások, beleértve a függvény app tartományból egy beágyazott másolatot készít egy OpenAPI fájl futtatásához a végpont OpenAPI beállítások együttese a [OpenAPI szerkesztő](http://editor.swagger.io), és az API definition sablon generátor.

1. Kattintson a **készítése API definition sablon** > **mentése**.

    ![API definition sablon létrehozása](media/functions-openapi-definition/generate-template.png)

    Azure vizsgálatokat végez a HTTP-eseményindítóval funkciók függvény alkalmazás, és használja a biztonsági adatok functions.json egy OpenAPI definíciója létrehozásához. A-definíciót, amely akkor jön létre, a következő:

    ```yaml
    swagger: '2.0'
    info:
    title: function-demo-energy.azurewebsites.net
    version: 1.0.0
    host: function-demo-energy.azurewebsites.net
    basePath: /
    schemes:
    - https
    - http
    paths:
    /api/TurbineRepair:
        post:
        operationId: /api/TurbineRepair/post
        produces: []
        consumes: []
        parameters: []
        description: >-
            Replace with Operation Object
            #http://swagger.io/specification/#operationObject
        responses:
            '200':
            description: Success operation
        security:
            - apikeyQuery: []
    definitions: {}
    securityDefinitions:
    apikeyQuery:
        type: apiKey
        name: code
        in: query
    ```

    Ez a definíció leírása a _sablon_ , amelyhez szükséges további metaadatokra ahhoz, hogy egy teljes OpenAPI definíciója lehet. A definícióját a következő lépésben fogja módosítani.

## <a name="modify-the-openapi-definition"></a>A OpenAPI módosítása
Most, hogy a sablonleírásnak, módosítása, hogy adja meg az API műveletek és adatstruktúrák kapcsolatos metaadatokat. A **API-definíció**, törölje a létrehozott definíciót `post` a definíció alsó, illessze be az alábbi tartalom, majd kattintson **mentése**.

```yaml
    post:
      operationId: CalculateCosts
      description: Determines if a technician should be sent for repair
      summary: Calculates costs
      x-ms-summary: Calculates costs
      x-ms-visibility: important
      produces:
        - application/json
      consumes:
        - application/json
      parameters:
        - name: body
          in: body
          description: Hours and capacity used to calculate costs
          x-ms-summary: Hours and capacity
          x-ms-visibility: important
          required: true
          schema:
            type: object
            properties:
              hours:
                description: The amount of effort in hours required to conduct repair
                type: number
                x-ms-summary: Hours
                x-ms-visibility: important
              capacity:
                description: The max output of a turbine in kilowatts
                type: number
                x-ms-summary: Capacity
                x-ms-visibility: important
      responses:
        200:
          description: Message with cost and revenue numbers
          x-ms-summary: Message
          schema:
           type: object
           properties:
            message:
              type: string
              description: Returns Yes or No depending on calculations
              x-ms-summary: Message 
            revenueOpportunity:
              type: string
              description: The revenue opportunity cost
              x-ms-summary: RevenueOpportunity 
            costToFix:
              type: string
              description: The cost in $ to fix the turbine
              x-ms-summary: CostToFix
      security:
        - apikeyQuery: []
definitions: {}
securityDefinitions:
  apikeyQuery:
    type: apiKey
    name: code
    in: query
```

Ebben az esetben meg lehetett most illessze be a metaadatok, de fontos ismerni a módosítások azt az alapértelmezett sablont:

+ A megadott, hogy az API-t hoz létre, és adatforrástól JSON formátumban.

+ A nevek és adattípusok meg a szükséges paramétereket.

+ A nevek és adattípusok sikeres választ, a visszaadott értékeket megadni.

+ A megadott felhasználóbarát összegzéseket és az API-t, és a műveletek és a paraméterek leírását. Ez fontos a személyek, akik használni fogják ezt a funkciót.

+ X-ms-összegzés és az x-ms-látható, a Microsoft Flow és a Logic Apps a felhasználói felületen használt fel. További információkért lásd: [OpenAPI bővítmények a Microsoft Flow egyéni API-k](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> Azt a biztonsági definíciójának bal oldali API-kulcs az alapértelmezett hitelesítési módszerrel. Ez a szakasz a definíció különböző típusú hitelesítés használatakor megváltozna.

API-műveleteket meghatározása kapcsolatos további információkért tekintse meg a [nyitott API-specifikációnak](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>A OpenAPI definition tesztelése
Az API-definíció használata előtt célszerű tesztelheti az Azure Functions felhasználói felületén.

1. Az a **kezelése** lapon, a függvény a **Állomáskulcsai**, másolása a **alapértelmezett** kulcs.

    ![Az API-kulcs másolása](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Tesztelési használhatja ezt a kulcsot, és akkor is használhatja az alkalmazás vagy szolgáltatás hívja az API-t.

1. Lépjen vissza az API-definíció: **függvény-bemutató-energia** > **Platform funkciói** > **API-definíció**.

1. Kattintson a jobb oldali ablaktáblában **hitelesítés módosítása**, adja meg az API-kulcsot, amely, másolja, majd kattintson az **hitelesítés**.

    ![A hitelesítést az API-kulcs](media/functions-openapi-definition/authenticate-api-key.png)

1. Görgessen le, majd kattintson a **próbálja elvégezni a műveletet**.

    ![Próbálja elvégezni a műveletet](media/functions-openapi-definition/try-operation.png)

1. Adja meg az értékeket **óra** és **kapacitás**.

    ![Adja meg a paraméterek](media/functions-openapi-definition/parameters.png)

    Figyelje meg, hogyan használja a felhasználói felület a az API-definíció leírása.

1. Kattintson a **kérés küldése**, majd kattintson a **közérthető** lapon, a kimenet megtekintéséhez.

    ![Kérés küldése](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure-függvény létrehozása
> * Az OpenAPI eszközökkel OpenAPI-definíció létrehozása
> * A további metaadatainak módosítása
> * A definíció a függvény tesztelése

Előzetes tudnivalók megtekintéséhez használja a létrehozott OpenAPI definition PowerApps-alkalmazás létrehozása a következő témakörre.
> [!div class="nextstepaction"]
> [Egy függvény hívása a powerapps segítségével](functions-powerapps-scenario.md)
