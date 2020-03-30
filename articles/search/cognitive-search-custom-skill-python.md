---
title: Példa egyéni szakértelemre (Python)
titleSuffix: Azure Cognitive Search
description: A Python-fejlesztők számára ismerje meg az Azure Functions és a Visual Studio egyéni szakértelem készítéséhez szükséges eszközöket és technikákat. Az egyéni képességek olyan felhasználó által definiált modelleket vagy logikát tartalmaznak, amelyeket hozzáadhat egy AI-dúsított indexelési folyamathoz az Azure Cognitive Search szolgáltatásban.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: fc69761a05ea381d39d58d5ebf0046e0d9874961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210465"
---
# <a name="example-create-a-custom-skill-using-python"></a>Példa: Egyéni szakértelem létrehozása python használatával

Ebben az Azure Cognitive Search skillset példában megtudhatja, hogyan hozhat létre egy webes API egyéni szakértelem python és visual studio kód használatával. A példa egy [Azure-függvényt](https://azure.microsoft.com/services/functions/) használ, amely megvalósítja az [egyéni szakértelem-felületet.](cognitive-search-custom-skill-interface.md)

Az egyéni szakértelem egyszerű a tervezés (ez összefűzkét karakterláncok), így összpontosítani az egyéni szakértelem fejlesztése a Pythonban. Miután sikerült egy egyszerű készség, akkor ág ki bonyolultabb forgatókönyvek.

## <a name="prerequisites"></a>Előfeltételek

+ Tekintse át az [egyéni szakértelem felület](cognitive-search-custom-skill-interface.md) egy bevezetés a bemeneti/kimeneti felület, amely egy egyéni szakértelem kell végrehajtania.

+ Állítsa be a környezetet. Ezt [az oktatóanyagot követtük,](https://docs.microsoft.com/azure/python/tutorial-vs-code-serverless-python-01) hogy a Visual Studio Code és a Python-bővítmények használatával kiszolgáló nélküli Azure-függvényt állítsunk be. Az oktatóanyag a következő eszközök és összetevők telepítésén keresztül vezet: 

  + [Python 3,75](https://www.python.org/downloads/release/python-375/)
  + [Visual Studio kód](https://code.visualstudio.com/)
  + [Python-bővítmény a Visual Studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  + [Az Azure Functions alapvető eszközei](https://docs.microsoft.com/azure/azure-functions/functions-run-local#v2)
  + [Azure Functions-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Ez a példa egy Azure-függvényt használ a webes API üzemeltetésének fogalmának bemutatásához, de más megközelítések is lehetségesek. Mindaddig, amíg megfelel a [felület követelményeinek a kognitív készség,](cognitive-search-custom-skill-interface.md)a megközelítés szedése lényegtelen. Az Azure Functions azonban megkönnyíti az egyéni szakértelem létrehozását.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A Visual Studio Code Azure Functions projektsablonja egy olyan projektet hoz létre, amely közzétehető egy Azure-függvényalkalmazásban. A függvényalkalmazás lehetővé teszi, hogy logikai egységbe csoportosítsa a függvényeket az erőforrások felügyelete, üzembe helyezése és megosztása érdekében.

1. A Visual Studio Code alkalmazásban nyomja le az F1 billentyűt a parancspaletta megnyitásához. A parancspalettán keresse `Azure Functions: Create new project...`meg és válassza a lehetőséget.

1. Válassza ki a projektmunkaterület könyvtárhelyét, és válassza a **Kijelölés gombot.**

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül hajtsák végre őket. Ezért ne jelöljön ki munkaterület részét szolgáló projektmappát.

1. Válassza ki a függvényalkalmazás-projekt nyelvét. Ehhez az oktatóanyaghoz válassza a **Python**lehetőséget.
1. Válassza ki a Python-verziót (a 3.7.5-ös verziót az Azure Functions támogatja)
1. Válasszon sablont a projekt első függvényéhez. Válassza a **HTTP-eseményindító lehetőséget** egy HTTP-aktivált függvény létrehozásához az új függvényalkalmazásban.
1. Adja meg a függvény nevét. Ebben az esetben használjuk a **Concatenator** 
1. Válassza a **Függvény** engedélyezési szintként lehetőséget. Ez azt jelenti, hogy egy [függvénykulcsot](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) biztosítunk a függvény HTTP-végpontjának hívásához. 
1. Adja meg, hogyan szeretné megnyitni a projektet. Ebben a lépésben válassza a **Hozzáadás munkaterülethez** lehetőséget a függvényalkalmazás létrehozásához az aktuális munkaterületen.

A Visual Studio Code létrehozza a függvényalkalmazást egy új munkaterületen. Ez a projekt a [host.json](../azure-functions/functions-host-json.md) és a [local.settings.json](../azure-functions/functions-run-local.md#local-settings-file) konfigurációs fájlokat tartalmazza, valamint az esetleges nyelvspecifikus projektfájlokat is. 

Egy új HTTP-aktivált függvény is létrejön a függvényalkalmazás-projekt **Concatenator** mappájában. Benne lesz egy "init__.py"\_\_nevű fájl, amelynek tartalma:

```py
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

Most nézzük módosítani, hogy a kódot, hogy kövesse az [egyéni készség felület](cognitive-search-custom-skill-interface.md)). Módosítsa a kódot a következő tartalommal:

```py
import logging
import azure.functions as func
import json

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    try:
        body = json.dumps(req.get_json())
    except ValueError:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )
    
    if body:
        result = compose_response(body)
        return func.HttpResponse(result, mimetype="application/json")
    else:
        return func.HttpResponse(
             "Invalid body",
             status_code=400
        )


def compose_response(json_data):
    values = json.loads(json_data)['values']
    
    # Prepare the Output before the loop
    results = {}
    results["values"] = []
    
    for value in values:
        output_record = transform_value(value)
        if output_record != None:
            results["values"].append(output_record)
    return json.dumps(results, ensure_ascii=False)

## Perform an operation on a record
def transform_value(value):
    try:
        recordId = value['recordId']
    except AssertionError  as error:
        return None

    # Validate the inputs
    try:         
        assert ('data' in value), "'data' field is required."
        data = value['data']        
        assert ('text1' in data), "'text1' field is required in 'data' object."
        assert ('text2' in data), "'text2' field is required in 'data' object."
    except AssertionError  as error:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Error:" + error.args[0] }   ]       
            })

    try:                
        concatenated_string = value['data']['text1'] + " " + value['data']['text2']  
        # Here you could do something more interesting with the inputs

    except:
        return (
            {
            "recordId": recordId,
            "errors": [ { "message": "Could not complete operation for record." }   ]       
            })

    return ({
            "recordId": recordId,
            "data": {
                "text": concatenated_string
                    }
            })
```

A **transform_value** metódus egyetlen rekordon hajt végre műveletet. Módosíthatja a módszert, hogy megfeleljen az Ön egyedi igényeinek. Ne felejtse el elvégezni a szükséges bemeneti érvényesítést, és adja vissza az esetlegesen létrehozott hibákat és figyelmeztetéseket, ha a művelet nem hajtható végre a rekordhoz.

### <a name="debug-your-code-locally"></a>Hibakeresés a kód helyileg

A Visual Studio Code megkönnyíti a kód hibakeresését. Nyomja le az "F5" billentyűt, vagy lépjen a **Hibakeresés** menüre, és válassza a **Hibakeresés indítása parancsot.**

Beállíthatja a töréspontokat a kódon, ha az "F9" gombra nyomódik az érdeklődési vonalon.

A hibakeresés megkezdése után a függvény helyileg fog futni. Használhatja az eszköz, mint a Postman vagy Hegedűs, hogy kiadja a kérelmet localhost. Jegyezze fel a helyi végpont helyét a Terminál ablakban. 

## <a name="publish-your-function"></a>A függvény közzététele

Ha elégedett a funkció viselkedésével, közzéteheti azt.

1. A Visual Studio Code alkalmazásban nyomja le az F1 billentyűt a parancspaletta megnyitásához. A parancspalettán keresse meg és válassza **a Telepítés a függvényalkalmazásba lehetőséget...**. 

1. Válassza ki azt az Azure-előfizetést, amelyben telepíteni szeretné az alkalmazást.

1. Válassza **a + Új függvényalkalmazás létrehozása lehetőséget az Azure-ban**

1. Adjon meg egy globálisan egyedi nevet a függvényalkalmazásnak.

1. Válassza ki a Python-verziót (a Python 3.7.x működik ehhez a funkcióhoz).

1. Válassza ki az új erőforrás helyét (például US2 nyugati telephelyét).

Ezen a ponton a szükséges erőforrásokjönnek létre az Azure-előfizetésben az új Azure-függvény azure-beli üzemeltetéséhez. Várjon, amíg az üzembe helyezés befejeződik. A kimeneti ablak ban megjelenik a telepítési folyamat állapota.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a **Minden erőforrás,** és keresse meg a nevet közzétett függvény. Ha **concatenator névre keresztelte,** válassza ki az erőforrást.

1. Kattintson a **</> Funkció URL-címének bekerülése** gombra. Ez lehetővé teszi, hogy másolja az URL-t, hogy hívja a funkciót.

## <a name="test-the-function-in-azure"></a>A funkció tesztelése az Azure-ban

Most, hogy rendelkezik az alapértelmezett állomáskulccsal, tesztelje a függvényt az alábbiak szerint:

```http
POST [Function URL you copied above]
```

### <a name="request-body"></a>A kérelem törzse
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "text1":  "Hello",
                "text2":  "World"
            }
        },
        {
            "recordId": "e2",
            "data": "This is an invalid input"
        }
    ]
}
```

Ebben a példában kell létrehoznia ugyanazt az eredményt, amit korábban látott, amikor a függvény futtatása a helyi környezetben.

## <a name="connect-to-your-pipeline"></a>Csatlakozás a folyamathoz

Most, hogy új egyéni szakértelemmel rendelkezik, hozzáadhatja azt a skillsethez. Az alábbi példa bemutatja, hogyan hívhatja meg a készséget a dokumentum címének és szerzőjének összefűzéséhez egyetlen mezőbe, amelyet merged_title_author nevezünk. Cserélje `[your-function-url-here]` le az új Azure-függvény URL-címét.

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new search custom skill",
        "uri": "https://[your-function-url-here]",        
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "text1",
              "source": "/document/metadata_title"
            },
            {
              "name": "text2",
              "source": "/document/metadata_author"
            },
          ],
          "outputs": [
            {
              "name": "text",
              "targetName": "merged_title_author"
            }
          ]
      }
  ]
}
```

## <a name="next-steps"></a>További lépések
Gratulálunk! Létrehozta az első egyéni képzettségét. Most ugyanazt a mintát követheti a saját egyéni funkciók hozzáadásához. További információért kattintson az alábbi hivatkozásokra.

+ [Power Skills: egyéni készségek tárháza](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Egyéni szakértelem hozzáadása a ai-dúsítási folyamathoz](cognitive-search-custom-skill-interface.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Szakértelemkészlet létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Bővített mezők leképezése](cognitive-search-output-field-mapping.md)
