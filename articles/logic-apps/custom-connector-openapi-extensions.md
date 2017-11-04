---
title: "Egyéni összekötők - Azure Logic Apps OpenAPI kiterjesztéseinek |} Microsoft Docs"
description: "Terjesztheti ki az egyéni összekötő OpenAPI összetettebb funkciók"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: e8565019bd4631043485224c73c2fe60e633951c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="extend-your-custom-connectors-openapi-with-advanced-functionality"></a>Terjesztheti ki az egyéni összekötő OpenAPI összetettebb funkciók

Az Azure Logic Apps, a Microsoft Flow vagy a Microsoft PowerApps egyéni összekötők létrehozásához meg kell adnia egy OpenAPI csomagdefiníciós fájlt, amely egy nyelvtől független géppel olvasható dokumentumok, amelyeket az API-műveleteket és paramétereket ismerteti. OpenAPI tartozó out-of-az-box funkciót, együtt is használható ezen OpenAPI bővítmények egyéni összekötők Logic Apps és a folyamat létrehozásakor:

* [`summary`](#summary)
* [`x-ms-summary`](#x-ms-summary)
* [`description`](#description)
* [`x-ms-visibility`](#x-ms-visibility)
* [`x-ms-dynamic-values`](#x-ms-dynamic-values)
* [`x-ms-dynamic-schema`](#x-ms-dynamic-schema)

Az alábbiakban további információt ezekről a bővítményekről:

<a name="summary"></a>

## <a name="summary"></a>Összefoglalás

Megadja a cím a művelethez (művelet). </br>
A következőkre vonatkozik: műveletek </br>
Ajánlott: Használja *Mondatkezdő* a `summary`. </br>
Példa: "Ha egy esemény jelenik meg a naptár" vagy "E-mail küldési"

!["összefoglaló" minden művelethez](./media/custom-connector-openapi-extensions/summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "summary": "Send an email",
    /// Other action properties here...
  }
},
```

<a name="x-ms-summary"></a>

## <a name="x-ms-summary"></a>x-ms-összegzés

Egy entitás címét adja meg. </br>
A következőkre vonatkozik: paraméterek, a válasz séma </br>
Ajánlott: Használja *eset title* a `x-ms-summary`. </br>
Példa: "Naptár ID", "Tárgy", "Esemény leírása", és így tovább

!["x-ms-összefoglaló" Minden entitáshoz](./media/custom-connector-openapi-extensions/x-ms-summary.png)

``` json
"actions" {
  "Send_an_email": {
    /// Other action properties here...
    "parameters": [ 
      {
        /// Other parameters here...
        "x-ms-summary": "Subject",
        /// Other parameters here...
      }
    ]
  }
},
```

<a name="description"></a>

## <a name="description"></a>leírás

Meghatározza a művelet funkció vagy egy entitás formátum és függvény részletes leírását. </br>
A következőkre vonatkozik: műveletek, paraméterek, válasz séma </br>
Ajánlott: Használja *Mondatkezdő* a `description`. </br>
Példa: "Ez a művelet váltja ki, ha új esemény kerül a naptár", "Adja meg az e-mail tárgya", és így tovább

!["description", művelet vagy entitás](./media/custom-connector-openapi-extensions/description.png)

``` json
"actions" {
  "Send_an_email": {
     "description": "Specify the subject of the mail",
     /// Other action properties here...
  }
},
```

<a name="visibility"></a>

## <a name="x-ms-visibility"></a>x-ms-látható

Adja meg a felhasználók számára is elérhető látható entitás. </br>
A lehetséges értékek: `important`, `advanced`, és`internal` </br>
A következőkre vonatkozik: műveletek, paraméterek, sémák

* `important`műveletek és a paraméterek számára mindig jelennek meg a felhasználó első.
* `advanced`műveletek és a paraméterek egy további menü rejtett.
* `internal`műveletek és a paraméterek rejtve maradnak az a felhasználó.

> [!NOTE] 
> A paraméterek `internal` és `required`, akkor **kell** ezek a paraméterek alapértelmezett értékeinek megadása.

Példa: A **további** menü és **speciális beállítások megjelenítése** menü elrejtése esetén `advanced` műveleteket és paramétereket.

!["x-ms-és könnyebben" megjelenítése vagy elrejtése, műveletek és a paraméterek](./media/custom-connector-openapi-extensions/x-ms-visibility.png)

``` json
"actions" {
  "Send_an_email": {
     /// Other action properties here...
     "parameters": [
         {
           "name": "Subject",
           "type": "string",
           "description": "Specify the subject of the mail",
           "x-ms-summary": "Subject",
           "x-ms-visibility": "important",
           /// Other parameter properties here...
         }
     ]
     /// Other action properties here...
  }
},
```

<a name="x-ms-dynamic-values"></a>

## <a name="x-ms-dynamic-values"></a>az x-ms-dinamikus-értékek

A felhasználó ki van töltve listáját jeleníti meg, így is választhatnak művelet bemeneti paraméterek. </br>
A következőkre vonatkozik: paraméterek </br>
Használata: vegye fel a `x-ms-dynamic-values` objektumot a paraméterek definícióját. Ez például látható [OpenAPI minta](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

!["x-ms-dinamikus-értékek" megjelenítő listája](./media/custom-connector-openapi-extensions/x-ms-dynamic-values.png)

### <a name="properties-for-x-ms-dynamic-values"></a>Az x-ms-dinamikus-értékek tulajdonságai

| Név | Kötelező vagy választható | Leírás | 
| ---- | -------------------- | ----------- | 
| **OperationID azonosítójú** | Szükséges | A művelet hívása a lista feltöltésekor. | 
| **érték-elérési út** | Szükséges | Egy elérési út karakterláncát, az objektum belül `value-collection` , amely hivatkozik a paraméter értékét. </br>Ha `value-collection` nincs megadva, a válasz egy tömb történik. | 
| **címe** | Optional | Egy elérési út karakterláncát, az objektum belül `value-collection` , amely hivatkozik a érték leírás. </br>Ha `value-collection` nincs megadva, a válasz egy tömb történik. | 
| **érték-gyűjtemény** | Optional | Elérési út karakterlánc, amely a válasz tartalomban objektumokból álló tömb kiértékelésének eredménye | 
| **Paraméterek** | Optional | Az objektum, amelynek a tulajdonságait egy dinamikus-értékek művelet meghívásához szükséges bemeneti paraméterek megadása | 
|||| 

Íme egy példa a tulajdonságokat megjelenítő `x-ms-dynamic-values`:

``` json
"x-ms-dynamic-values": {
  "operationId": "PopulateDropdown",
  "value-path": "name",
  "value-title": "properties/displayName",
  "value-collection": "value",
  "parameters": {
     "staticParameter": "<value>",
     "dynamicParameter": {
        "parameter": "<value-to-pass-to-dynamicParameter>"
     }
  }
}
```

## <a name="example-all-the-openapi-extensions-up-to-this-point"></a>Példa: Az összes a OpenAPI bővítmény a pontig

``` json
"/api/lists/{listID-dynamic}": {
    "get": {
        "description": "Get items from a single list - uses dynamic values and outputs dynamic schema",
        "summary": "Gets items from the selected list",
        "operationID": "GetListItems",
        "parameters": [
           {
             "name": "listID-dynamic",
             "type": "string",
             "in": "path",
             "description": "Select the list from where you want outputs",
             "required": true,
             "x-ms-summary": "Select List",
             "x-ms-dynamic-values": {
                "operationID": "GetLists",
                "value-path": "id",
                "value-title": "name"
             }
           }
        ]
    }
}
```

<a name="x-ms-dynamic schema"></a>

## <a name="x-ms-dynamic-schema"></a>x-ms-dinamikus-séma

Azt jelzi, hogy az aktuális paraméter vagy válasz séma dinamikus. Ez az objektum is ez a mező értéke által meghatározott művelet meghívása, dinamikusan felderítése a séma, és felhasználói bevitelek gyűjtéséhez megfelelő felhasználói felületet vagy elérhető mezők megjelenítése. 

A következőkre vonatkozik: paraméterek, a válasz

Használata: vegye fel a `x-ms-dynamic-schema` kérelem paraméter vagy válasz törzsében definíciója objektumot. Egy vonatkozó példáért lásd: a [OpenAPI minta](https://procsi.blob.core.windows.net/blog-images/sampleDynamicSwagger.json).

A példa bemutatja, hogyan változik a bemeneti űrlap, az elem, amely a felhasználó választja ki a legördülő lista alapján:

!["x-ms-dinamikus-séma" dinamikus paraméterek](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema.png)

És ez a példa bemutatja, hogyan módosítja a kimenetek, az elem, amely a felhasználó választja ki a legördülő lista alapján. Ebben a verzióban a felhasználó kiválasztja a "Autók":

!["x-ms-dinamikus-séma-válasz" a kijelölt elem "Autók"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output1.png)

Ebben a verzióban a felhasználó kiválasztja a "Étele":

!["x-ms-dinamikus-séma-válasz" a kijelölt elem "Étele"](./media/custom-connector-openapi-extensions/x-ms-dynamic-schema-output2.png)

### <a name="properties-for-x-ms-dynamic-schema"></a>Az x-ms-dinamikus-séma tulajdonságai

| Név | Kötelező vagy választható | Leírás | 
| ---- | -------------------- | ----------- | 
| **OperationID azonosítójú** | Szükséges | A művelet hívása a séma beolvasása | 
| **Paraméterek** | Szükséges | Az objektum, amelynek a tulajdonságait egy dinamikus-séma művelet meghívásához szükséges bemeneti paraméterek megadása | 
| **érték-elérési út** |Optional | Elérési út karakterlánc, amely tulajdonságára hivatkozik, amely rendelkezik a séma. </br>Ha nincs megadva, a válasz feltételezett, hogy tartalmazza a legfelső szintű objektum tulajdonságai a sémát. | 
|||| 

Íme egy példa a dinamikus paraméter:

``` json
{
  "name": "dynamicListSchema",
  "in": "body",
  "description": "Dynamic schema for items in the selected list",
  "schema": {
    "type": "object",
    "x-ms-dynamic-schema": {
        "operationID": "GetListSchema",
        "parameters": {
          "listID": {
            "parameter": "listID-dynamic"
          }
        },
        "value-path": "items"
    }
  }
}
```

Íme egy példa a dinamikus választ:

``` json
"DynamicResponseGetListSchema": {
   "type": "object",
   "x-ms-dynamic-schema": {
      "operationID": "GetListSchema",
      "parameters": {
         "listID": {
            "parameter": "listID-dynamic"
         }
      },
      "value-path": "items"
    }
}
```

## <a name="next-steps"></a>Következő lépések

* [Az egyéni API-k és összekötők leírása](../logic-apps/custom-connector-api-postman-collection.md)
* [A Logic Apps: Az összekötő regisztrálása](../logic-apps/logic-apps-custom-connector-register.md)
* [Folyamat: Az összekötő regisztrálása](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)