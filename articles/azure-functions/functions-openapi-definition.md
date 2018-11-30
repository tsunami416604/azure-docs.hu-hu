---
title: OpenAPI definíció létrehozása egy függvényhez | Microsoft Docs
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
ms.openlocfilehash: 2d50e4c2352444d29bdb090bc9a2a7947ecc6a50
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52496034"
---
# <a name="create-an-openapi-definition-for-a-function"></a>OpenAPI definíció létrehozása egy függvényhez

A REST API-k leírása gyakran egy OpenAPI-definíció (korábbi nevén [Swagger](http://swagger.io/)-fájl) használatával történik. Ez a definíció tartalmazza az API-ban elérhető műveletekkel kapcsolatos információkat, illetve az API kérés- és válaszadatainak felépítését.

Ebben az oktatóanyagban létrehoz egy függvényt, amely megállapítja, hogy egy szélturbina sürgősségi javítása költséghatékony-e. Ezután létrehoz egy OpenAPI-definíciót a függvényalkalmazáshoz, hogy a függvényt más alkalmazások és szolgáltatások is meghívhassák.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Függvény létrehozása az Azure-ban
> * OpenAPI-definíció létrehozása az OpenAPI eszközeivel
> * A definíció módosítása további metaadatok megadása céljából
> * A definíció tesztelése a függvény meghívásával

> [!IMPORTANT]
> Az OpenAPI-funkció jelenleg előzetes verzióban és csak verzió érhető el az Azure Functions futtatókörnyezet 1.x.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, telepítése, méretezése és megosztása érdekében. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="set-the-functions-runtime-version"></a>Functions futtatókörnyezet verziójának beállítása

Alapértelmezés szerint a függvényalkalmazást hoz létre használt verzió 2.x verziójú futtatókörnyezet. Vissza a 1.x kell futtatókörnyezet verziójának beállítása, a függvény létrehozása előtt.

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

## <a name="create-the-function"></a>A függvény létrehozása

Az oktatóanyag egy HTTP-vel aktivált függvényt használ, amelynek két bemeneti paramétere van: egy turbina javításához szükséges becsült idő (órákban) és a turbina kapacitása (kilowattban). A függvény kiszámolja, hogy mennyibe kerül a javítás, és hogy a turbina 24 óra alatt mennyi bevételt tudna termelni.

1. Bontsa ki a függvényalkalmazást, és kattintson a **Függvények** elem melletti **+** gombra. Ha ez az első függvény a függvényalkalmazásban, jelölje ki az **Egyéni függvény** lehetőséget. Ez megjeleníti a függvénysablonok teljes készletét. 

    ![Függvények gyors létrehozásának oldala az Azure Portalon](media/functions-openapi-definition/add-first-function.png)

1. A keresés mezőbe írja be a `http` kifejezést, majd válassza ki a **C#** nyelvet a HTTP-eseményindító sablonjához. 

    ![HTTP-eseményindító kiválasztása](./media/functions-openapi-definition/select-http-trigger-portal.png)

1. A függvénynek adja a `TurbineRepair` **nevet**, válassza ki a `Function` **[Hitelesítési szintet](functions-bindings-http-webhook.md#http-auth)**, majd kattintson a **Létrehozás** lehetőségre.  

    ![A HTTP által aktivált függvény létrehozása](./media/functions-openapi-definition/select-http-trigger-portal-2.png)

1. Cserélje le a run.csx fájl tartalmát a következő kódra, majd kattintson a **Mentés** parancsra:

    ```csharp
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

Most már van egy olyan függvénye, amely megállapítja a sürgősségi javítások költséghatékonyságát. Ezután hozza létre és módosítsa a függvényalkalmazás OpenAPI-definícióját.

## <a name="generate-the-openapi-definition"></a>Az OpenAPI-definíció létrehozása

Most már készen áll arra, hogy létrehozza az OpenAPI-definíciót. Ezt a Microsoft egyéb technológiái is használhatják, például az API Apps, a [PowerApps](functions-powerapps-scenario.md) és a [Microsoft Flow](../azure-functions/app-service-export-api-to-powerapps-and-flow.md), valamint külső gyártóktól származó fejlesztői eszközök is, mint a [Postman](https://www.getpostman.com/docs/importing_swagger) és [még sok más csomag](http://swagger.io/tools/).

1. Csak azokat a *műveleteket* válassza ki, amelyeket az API támogat (jelen esetben a POST). Ezáltal átláthatóbb lesz a létrehozott API-definíció.

    1. Az új HTTP-eseményindító függvény **Integrálás** lapján módosítsa az **Engedélyezett HTTP-metódusok** beállítást a **Kiválasztott metódusok** értékre.

    1. A **Kiválasztott HTTP-metódusok** között a **POST** kivételével törölje az összes beállítás jelölését, majd kattintson a **Mentés** lehetőségre.

        ![Kiválasztott HTTP-metódusok](media/functions-openapi-definition/selected-http-methods.png)

1. Kattintson a függvényalkalmazás nevére (pl. **function-demo-energy**), majd a **Platformfunkciók** > **API-definíció** lehetőségre.

    ![API-definíció](media/functions-openapi-definition/api-definition.png)

1. Az **API-definíció** lapon kattintson a**Függvény** elemre.

    ![API-definíció forrása](media/functions-openapi-definition/api-definition-source.png)

    Ez a lépés számos OpenAPI-beállítást engedélyez a függvényalkalmazás számára, köztük egy végpontot, amely a függvényalkalmazás tartományából futtat egy OpenAPI-fájlt, valamint az [OpenAPI-szerkesztő](http://editor.swagger.io) egy beágyazott példányát és egy API-definíciósablon generátort.

1. Kattintson az **API-definíciósablon létrehozása** > **Mentés** lehetőségre.

    ![API-definíciósablon létrehozása](media/functions-openapi-definition/generate-template.png)

    Az Azure kikeresi a függvényalkalmazásból a HTTP-eseményindító függvényeket. és a functions.json-ben található adatok alapján létrehoz egy OpenAPI-definíciót. A létrehozott definíció a következő:

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

    Ez a definíció egy _sablonként_ van leírva, mivel további metaadatokat igényel ahhoz, hogy teljes OpenAPI-definíció lehessen. A definíciót a következő lépésben módosíthatja.

## <a name="modify-the-openapi-definition"></a>Az OpenAPI-definíció módosítása

Most, hogy van egy definíciósablonja, módosítania kell azt az API műveleteire és adatstruktúráira vonatkozó metaadatok. Törölje az **API-definícióban** a létrehozott definíciót a `post` résztől a definíció végéig, illessze be az alábbi tartalmat, majd kattintson a **Mentés** lehetőségre.

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

Ebben az esetben elég lenne egyszerűen beilleszteni a frissített metaadatokat, de fontos tisztában lenni azzal, hogy milyen típusú módosításokat végeztünk el az alapértelmezett sablonon:

* Megadtuk, hogy az API JSON-formátumú adatokat hoz létre és használ fel.

* Megadtuk a szükséges paramétereket a nevükkel és adattípusukkal együtt.

* Megadtuk a sikeres válasz által visszaadott értékeket a nevükkel és adattípusukkal együtt.

* Világos összegzéseket és leírásokat adtunk meg az API-hoz a műveleteire és paramétereire vonatkozóan. Ez a függvényt használók számára lesz fontos.

* Hozzáadtuk az x-ms-summary és x-ms-visibility elemeket, amelyeket a Microsoft Flow és a Logic Apps felhasználói felületei használnak. További információ: [Egyéni API-k OpenAPI-bővítményei a Microsoft Flowban](https://preview.flow.microsoft.com/documentation/customapi-how-to-swagger/).

> [!NOTE]
> A biztonsági definíciónál meghagytuk az alapértelmezett hitelesítési metódust, az API-kulcs használatát. Másfajta hitelesítés használata esetén módosítani kell a definíció ezen szakaszát.

További információk az API-műveletek definiálásáról: [ OpenAPI-specifikációk](https://swagger.io/specification/#operationObject).

## <a name="test-the-openapi-definition"></a>Az OpenAPI-definíció tesztelése

Az API-definíciót a használat előtt érdemes tesztelni az Azure Functions felületén.

1. A függvény **Kezelés** lapján másolja ki az **Gazdakulcsok** területen található **alapértelmezett** kulcsot.

    ![API-kulcs másolása](media/functions-openapi-definition/copy-api-key.png)

    > [!NOTE]
    >Ezt a kulcsot használhatja a teszteléshez, valamint akkor is, ha az API-t egy alkalmazásból vagy szolgáltatásból szeretné meghívni.

1. Lépjen vissza az API-definícióhoz: **function-demo-energy** > **Platformfunkciók** > **API-definíció**.

1. A jobb oldali panelen kattintson a **Hitelesítés** lehetőségre, illessze be a kimásolt API-kulcsot, majd kattintson a **Hitelesítés** gombra.

    ![Hitelesítés API-kulccsal](media/functions-openapi-definition/authenticate-api-key.png)

1. Görgessen lefelé, és kattintson a **Művelet kipróbálása** lehetőségre.

    ![Művelet kipróbálása](media/functions-openapi-definition/try-operation.png)

1. Adja meg az **óraszám** és a **kapacitás** paraméterek értékeit.

    ![Paraméterek megadása](media/functions-openapi-definition/parameters.png)

    Figyelje meg, hogy a felhasználói felület az API-definíció leírásait használja.

1. Kattintson a **Kérés küldése** gombra, majd a **Rendezett** lapra a kimenet megtekintéséhez.

    ![Kérés küldése](media/functions-openapi-definition/send-request.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Függvény létrehozása az Azure-ban
> * OpenAPI-definíció létrehozása az OpenAPI eszközeivel
> * A definíció módosítása további metaadatok megadása céljából
> * A definíció tesztelése a függvény meghívásával

Lépjen tovább a következő témakörre, amelyből megtudhatja, hogyan hozhat létre egy PowerApps-alkalmazást, amely az imént létrehozott OpenAPI-definíciót használja.

> [!div class="nextstepaction"]
> [Függvény hívása a PowerAppsből](functions-powerapps-scenario.md)
