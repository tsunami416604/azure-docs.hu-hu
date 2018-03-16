---
title: "Létrehozása, szerkesztése vagy JSON kiterjesztése a logikai alkalmazás definícióiról - Azure Logic Apps |} Microsoft Docs"
description: "Szerzői és testre szabhatja a logikai alkalmazás definícióiról a JSON-ban"
author: ecfan
manager: SyntaxC4
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2018
ms.author: estfan; LADocs
ms.openlocfilehash: bde275eb75c97da2a99109484b46b599a5b2f871
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="create-edit-or-customize-json-for-logic-app-definitions"></a>Létrehozása, szerkesztése vagy JSON logic app-definíciók testreszabása

A munkafolyamatok létrehozásakor vállalati integrációs megoldásokat az automatikus [Azure Logic Apps](../logic-apps/logic-apps-overview.md), az alapul szolgáló logikai alkalmazás definícióiról egyszerű és deklaratív JavaScript Object Notation (JSON) együtt használja a [ Munkafolyamat Definition Language (WDL) séma](../logic-apps/logic-apps-workflow-definition-language.md) leírás és érvényesítése. Ezek a formátumok könnyebben logikai alkalmazás definícióiról Elolvastam és megértettem nagy kód ismerete nélkül. Ha automatizálni szeretné a logikai alkalmazások létrehozása és telepítése, megadhatja a logikai alkalmazás definícióiról mint [Azure-erőforrások](../azure-resource-manager/resource-group-overview.md) belül [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-overview.md#template-deployment). Létrehozására, kezelésére és központi telepítése a logic apps, ezután [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md), vagy a [Azure Logic Apps REST API-k](https://docs.microsoft.com/rest/api/logic/).

Dolgozunk a logikai alkalmazás definícióiról a JSON-ban, nyissa meg a kód nézetre szerkesztőt, az Azure portálon vagy a Visual Studio használatakor, vagy másolja a definíció bármilyen kívánt szerkesztő. Ha most ismerkedik a logic apps, tekintse át a [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Bizonyos Azure Logic Apps képességeit, például a paraméterek és több eseményindítók meghatározása a logikai alkalmazás definícióiról, csak a JSON-ban, nem a Logic Apps-Tervező érhetők el. Ezeket a feladatokat a kód nézetre, vagy egy másik szerkesztőt kell működni.

## <a name="edit-json---azure-portal"></a>Szerkessze a JSON - Azure-portálon

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

2. A bal oldali menüből **minden szolgáltatás**. A keresési mezőbe "logic apps" keresse meg, és az eredmények közül jelölje a Logic Apps alkalmazást.

3. A logikai alkalmazás menü alatti **Fejlesztőeszközök**, jelölje be **Logic App kód nézetre**.

   A kód nézetre-szerkesztő megnyitása, és JSON formátumban jeleníti meg a logic app-definíciót.

## <a name="edit-json---visual-studio"></a>Szerkessze a JSON - Visual Studio

Mielőtt a logic app-definíciót a Visual Studio is dolgozhatnak, győződjön meg arról, hogy [telepítette a szükséges eszközöket](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Logikai alkalmazás létrehozása a Visual Studio, tekintse át [gyors üzembe helyezés: feladatok és az Azure Logic Apps - Visual Studio eljárások automatizálására](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

A Visual Studio nyissa meg a logic apps létrehozott és telepített vagy közvetlenül az Azure-portálon, vagy Azure Resource Manager projekteket a Visual Studio eszközből.

1. Nyissa meg a Visual Studio megoldás vagy [Azure erőforráscsoport](../azure-resource-manager/resource-group-overview.md) projekt, amely tartalmazza a Logic Apps alkalmazást.

2. Keresse meg, és nyissa meg a logic app-definíciót, amely alapértelmezés szerint megjelenik egy [Resource Manager-sablon](../azure-resource-manager/resource-group-overview.md#template-deployment)nevű **LogicApp.json**. Használjon, és a különböző környezetekben való központi telepítési sablon testreszabása.

3. Nyissa meg a logic app-definíciót és a sablon helyi menüje. Válassza ki **nyissa meg a Logic App tervezővel**.

   ![Nyissa meg logikai alkalmazást a Visual Studio megoldás](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. A designer alján válassza **kódnézetben**. 

   A kód nézetre-szerkesztő megnyitása, és JSON formátumban jeleníti meg a logic app-definíciót.

5. Térjen vissza a Tervező nézetre, a kód nézetre szerkesztő alján válassza **tervezési**.

## <a name="parameters"></a>Paraméterek

Paraméterek is felhasználhatja a Logic Apps alkalmazást értékét, és jó helyettesítésére értékeket, amelyeket gyakran érdemes lehet módosítani. Például ha egy e-mail címet, amelyet több helyen is használja, meg kell határozni, hogy e-mail cím paraméterként. 

Paraméterek is akkor hasznosak, ha különböző környezetekben paraméterének, további információ kell [telepítési paramétereinek](#deployment-parameters) és a [REST API-t az Azure Logic Apps dokumentációs](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Paraméterek kód nézetben csak érhetők el.

Az a [első példa logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md), a munkafolyamat által küldött e-mailek amikor új bejegyzések megjelennek a webhely RSS-hírcsatorna a létrehozott. A hírcsatorna URL-cím szoftveresen kötött, így ez a példa bemutatja, hogyan cserélje le a lekérdezés értékét egy paraméterrel, így könnyebben hírcsatorna URL-címet módosíthatja.

1. A kód nézetre, keresse a `parameters : {}` objektumot, és adjon hozzá egy `currentFeedUrl` objektum:

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. Az a `When_a_feed-item_is_published` művelet található a `queries` szakaszt, és cserélje le a lekérdezés értékét `"feedUrl": "#@{parameters('currentFeedUrl')}"`. 

   **Before**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **Után**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   Két vagy több karakterlánc csatlakozni is használhatja a `concat` függvény. 
   Például `"@concat('#',parameters('currentFeedUrl'))"` ugyanúgy működik, mint az előző példát.

3.  Ha elkészült, kattintson a **Mentés** gombra. 

Most a webhely RSS-hírcsatorna úgy, hogy egy másik URL-címet keresztül bármikor módosíthatja a `currentFeedURL` objektum.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>A különböző környezetek üzembe helyezéshez megadott paraméterek

Központi telepítés életciklusának általában, fejlesztési, átmeneti és üzemi környezetekben rendelkezik. Például előfordulhat, hogy ezekben a környezetekben az azonos logic app-definíciót használatára, de különböző adatbázist használja. Hasonlóképpen érdemes ugyanazon definíció különböző régiókban használja a magas rendelkezésre állású, de szeretné, hogy minden egyes logic app-példány adott régióban adatbázis használatára. 

> [!NOTE] 
> Ebben a forgatókönyvben eltér paraméterek véve *futásidejű* hol kell használnia a `trigger()` helyette működik.

Íme egy alapszintű definíciója:

``` json
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
A tényleges `PUT` kérhetnek a logic apps, megadhatja, hogy a paraméter `uri`. Minden környezetben, adjon meg más értéket a a `connection` paraméter. Mivel az alapértelmezett értéke már nem létezik, a logic app forgalma kell ezt a paramétert:

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

További tudnivalókért tekintse meg a [REST API-t az Azure Logic Apps dokumentációs](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>Folyamat karakterláncok funkciók

A Logic Apps rendelkezik a különböző funkciók karakterláncok használata. Tegyük fel például, sorrendben vállalatnevet átadása egy másik rendszer szeretné. Azonban nem biztos megfelelő kezelését a karakteres kódolási kapcsolatban. Ez a karakterlánc az alkalmazás base64 kódolást elvégezni, de Kilépés az URL-címben elkerüléséhez lecserélheti több karaktert helyette. Is csak kell egy substring a vállalat neve, mert az első öt karakterek nem használhatók. 

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Ezeket a lépéseket ismerteti, hogyan dolgozza fel a ebben a példában ez a karakterlánc, belső kívülre dolgozik:

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Beolvasása a [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) a vállalat nevét, az így kapott karakterek száma.

2. Ahhoz, hogy rövidebb karakterláncnak, kivonás `5`.

3. Most már egy [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Indítsa el a következő indexnél `5`, és nyissa meg a karakterlánc hátralévő részére.

4. A keresendő karakterláncrészletet átalakítani egy [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) karakterlánc.

5. Most [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) minden a `+` karakterből álló `-` karaktereket.

6. Végezetül [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) minden a `/` karakterből álló `_` karaktereket.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Listaelemek megfeleltetése tulajdonságok értékeit, majd használja a maps paraméter

Más eredményt ad-alapú egy tulajdonság értéke, a megfelelő tulajdonságértékekhez eredményeként a térkép létrehozásához, majd a térkép használata paramétereként. 

Ez a munkafolyamat például néhány kategóriák meghatározása szerint a paraméterek és, hogy az ezekben a kategóriákban egy adott URL-cím megegyezik. Először a munkafolyamat lekérdezi a cikkek listáját. Ezt követően a munkafolyamat a térkép az egyes cikkekhez tartozó kategória megfelelő URL-címét használja.

*   A [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) függvény ellenőrzi, hogy a kategória sem felel meg egy ismert meghatározott kategóriát.

*   Után a megfelelő kategóriát kap, a választ az elem a térkép szögletes zárójelbe használatával: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>A dátum funkciók adatok beolvasása

Nem natív módon támogató adatforrásból származó adatok eléréséhez *eseményindítók*, használhat dátum alkalommal való munkához működik, és ehelyett dátumok. Például ebben a kifejezésben megkeresi, mennyi ideig a munkafolyamat lépéseket végzése, kívülre belülről működik:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Az a `order` kivonat, a művelet a `startTime`. 
2. Az aktuális idő az beszerzése `utcNow()`.
3. Egy második kivonása:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Például használhatja más időegységekkel `minutes` vagy `hours`. 

3. Most amelyeket összehasonlíthat e két érték. 

   Ha az első érték kisebb, mint a második érték, akkor több mint egy második megbízást először óta eltelt.

A dátumok formázásához karakterlánc is használhatja. Például a RFC1123, amelyet [ `utcnow('r')` ](../logic-apps/logic-apps-workflow-definition-language.md). További információ [formázás dátum](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```


## <a name="next-steps"></a>További lépések

* [A lépéseket (feltételes utasítások) feltétel alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Futtatás (kapcsoló utasítások) eltérő értékek alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Futtassa a csoportosított (hatókör) állapota alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* További információ a [Azure Logic Apps Munkafolyamatdefiníciós nyelve sémája](../logic-apps/logic-apps-workflow-definition-language.md)
* További információ [munkafolyamat-műveleteket, és az Azure Logic Apps eseményindítók](../logic-apps/logic-apps-workflow-actions-triggers.md)