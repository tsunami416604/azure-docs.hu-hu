---
title: Létrehozásához, szerkesztéséhez vagy JSON kiterjesztheti a logic app-definíciók – Azure Logic Apps |} A Microsoft Docs
description: Hozhat létre, és JSON kiterjesztése a logikai alkalmazás definícióiról az Azure Logic Appsben
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: daeb900abc3f24a408fc1b5f6e989e5181f2a463
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862743"
---
# <a name="create-edit-or-extend-json-for-logic-app-definitions-in-azure-logic-apps"></a>Létrehozásához, szerkesztéséhez vagy JSON kiterjesztése a logikai alkalmazás definícióiról az Azure Logic Appsben

A munkafolyamatok létrehozásakor enterprise integration-megoldások automatikus [Azure Logic Apps](../logic-apps/logic-apps-overview.md), az alapul szolgáló logikaialkalmazás-definíciók használata egyszerű, deklaratív JavaScript Object Notation (JSON) és a [ A munkafolyamat Definition Language (WDL) séma](../logic-apps/logic-apps-workflow-definition-language.md) azok leírását és az érvényesítés. Ezek a formátumok egyszerűbben logikai alkalmazás definícióiról Elolvastam és megértettem ismerné a kód nélkül. Ha szeretné automatizálni a létrehozása és telepítése a logic apps, hozzáadhatja a logikaialkalmazás-definíciók, [Azure-erőforrások](../azure-resource-manager/resource-group-overview.md) belül [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-overview.md#template-deployment). Létrehozása, kezelése és üzembe helyezése a logic apps esetében használhatja [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/az.logicapp), [Azure CLI-vel](../azure-resource-manager/resource-group-template-deploy-cli.md), vagy a [Azure Logic Apps – REST API-k](https://docs.microsoft.com/rest/api/logic/).

Logikaialkalmazás-definíciók JSON dolgozni, nyissa meg a Kódnézet szerkesztőt, ha az Azure Portalon vagy a Visual Studióban, vagy másolja a definíció bármilyen szerkesztőt, amelyeket szeretne. Ha most ismerkedik a logic apps, tekintse át [az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

> [!NOTE]
> Azure Logic Apps bizonyos funkciók, például a paraméterek és több eseményindító meghatározása a logikaialkalmazás-definíciók, csak a JSON-ban, nem a Logic Apps Designerben érhetők el.
> Így a feladatokhoz Kódnézet vagy más szerkesztővel kell működniük.

## <a name="edit-json---azure-portal"></a>Szerkesztés JSON - Azure-portálon

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>.

2. A bal oldali menüben válassza **minden szolgáltatás**. A keresőmezőbe keresse meg a "logic apps", és az eredmények közül válassza ki a logikai alkalmazás.

3. A logikai alkalmazás menüjében alatt **Fejlesztőeszközök**válassza **logikai alkalmazás Kódnézete**.

   A kód nézet szerkesztő partnerről tekinthet meg a logikai alkalmazás definíciójának JSON formátumban.

## <a name="edit-json---visual-studio"></a>JSON - szerkesztése a Visual Studio

Előtt is dolgozhatnak a logikai alkalmazás definíciójának a Visual Studióban, győződjön meg arról, hogy [telepítve van a szükséges eszközöket](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites). Logikai alkalmazás létrehozása a Visual Studióval, tekintse át [a rövid útmutató: Automatizálhatja a feladatokat és folyamatokat az Azure Logic Apps – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

A Visual Studióban nyissa meg a logic apps létrehozott és telepített, vagy közvetlenül az Azure Portalról vagy a Visual Studióból az Azure Resource Manager-projektként.

1. Nyissa meg a Visual Studio-megoldást, vagy [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) projekt legyen, amely tartalmazza a logikai alkalmazást.

2. Keresse meg és nyissa meg a logikai alkalmazás definíciót, amely alapértelmezés szerint megjelenik egy [Resource Manager-sablon](../azure-resource-manager/resource-group-overview.md#template-deployment)nevű **LogicApp.json**. Ez a sablon a különböző környezetekben való üzembe helyezéshez testreszabása és használhatja.

3. A logic app-definíciójának és a sablon a helyi menü megnyitásához. Válassza a lehetőséget az elem **megnyitására a Logikaialkalmazás-tervezővel**.

   ![Nyissa meg logikai alkalmazás a Visual Studio-megoldásban](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. A tervező alján válassza **Kódnézet**. 

   A kód nézet szerkesztő partnerről tekinthet meg a logikai alkalmazás definíciójának JSON formátumban.

5. Térjen vissza tervezőnézetben, a kód nézet szerkesztő alján válassza **tervezési**.

## <a name="parameters"></a>Paraméterek

Paramétereket is felhasználhatja a logikai alkalmazás teljes értékeket, és jók, amelyek gyakran változhatnak értékeket cserélje le a. Például ha egy e-mail címet, amelyet több helyen is használja, meg kell határozni e-mail-címmel paraméterként.

Paramétereket is akkor hasznos, ha a paramétereket a különböző környezetek felülírása, tudjon meg többet kell [üzembe helyezési paraméterek](#deployment-parameters) és a [REST API-t az Azure Logic Apps-dokumentáció](https://docs.microsoft.com/rest/api/logic).

> [!NOTE]
> Paraméterek csak Kódnézet érhető el.

Az a [első példa logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md), létrehozott egy munkafolyamatot, amely e-maileket küld, amikor új bejegyzés jelenik meg egy webhely RSS-hírcsatornában. Az adatcsatorna URL-címe nem változtatható, így ez a példa bemutatja, hogyan cserélje le a lekérdezés értéket egy paraméterrel, így könnyebben módosíthatók hírcsatorna URL-cím.

1. Kódnézet, keresse meg a `parameters : {}` objektumot, és adjon hozzá egy `currentFeedUrl` objektum:

   ``` json
   "currentFeedUrl" : {
      "type" : "string",
      "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. Az a `When_a_feed-item_is_published` művelet, keresse meg a `queries` szakaszt, és cserélje le a lekérdezés értékét `"feedUrl": "#@{parameters('currentFeedUrl')}"`.

   **Mielőtt**
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

   Két vagy több karakterláncok csatlakozni is használhatja a `concat` függvény. 
   Ha például `"@concat('#',parameters('currentFeedUrl'))"` ugyanúgy működik, mint az előző példában.

3.  Ha elkészült, kattintson a **Mentés** gombra.

Most módosíthatja a webhely RSS-hírcsatorna egy másik URL-címet keresztül adja át a `currentFeedURL` objektum.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>Különböző környezetek üzembe helyezéshez megadott paraméterek

Általában a központi telepítési életciklusának fejlesztési, átmeneti és éles környezetek rendelkezik. Például előfordulhat, hogy az azonos logic app-definíció használata ezekben a környezetekben, de különböző adatbázist használja. Hasonlóképpen érdemes a különböző régiók között ugyanazon definíció használata magas rendelkezésre állást, de használhatja az adott régióban database minden egyes logikaialkalmazás-példányt szeretné.

> [!NOTE]
> Ebben a forgatókönyvben eltér a paraméterekhez véve *futásidejű* hol kell használnia a `trigger()` inkább működik.

Íme egy alapszintű definíciója:

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
A tényleges `PUT` kérhetnek a logic apps esetében megadhatja a paraméterrel `uri`. Minden környezetben, adjon meg másik értéket a `connection` paraméter. Alapértelmezett érték már nem létezik, mert a logikai alkalmazás hasznos szükséges ezt a paramétert:

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

További tudnivalókért tekintse meg a [REST API-t az Azure Logic Apps-dokumentáció](https://docs.microsoft.com/rest/api/logic/).

## <a name="process-strings-with-functions"></a>A functions folyamat karakterláncok

A Logic Apps rendelkezik a különböző funkciók karakterláncokkal való munka. Tegyük fel, írjon be egy céges nevet egy másik rendszerre szeretne. Azonban nem biztos kapcsolatos forráskarakter-kódolás a megfelelő kezeléséhez. Elvégezni ezt a karakterláncot a base64 kódolást, de az URL-címben Kilépés elkerüléséhez lecserélheti több karaktert helyette. Emellett csak kell egy karakterláncrészletet a vállalat neve, mert az első öt karakterek nem használhatók.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Ezek a lépések bemutatják, miként dolgozza fel a ebben a példában ez a karakterlánc, a belső dolgozunk azon, hogy a külső:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. Első a [ `length()` ](../logic-apps/logic-apps-workflow-definition-language.md) a vállalat neve, így kap karakterek száma.

2. Egy rövidebb karakterlánc lekéréséhez kivonása `5`.

3. Mostantól egy [ `substring()` ](../logic-apps/logic-apps-workflow-definition-language.md). Indítsa el a következő indexnél `5`, és nyissa meg a karakterlánc a további részében.

4. A keresendő karakterláncrészletet átalakítani egy [ `base64()` ](../logic-apps/logic-apps-workflow-definition-language.md) karakterlánc.

5. Most már [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) minden a `+` karakterből álló `-` karakter.

6. Végül [ `replace()` ](../logic-apps/logic-apps-workflow-definition-language.md) minden a `/` karakterből álló `_` karakter.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Listaelemek leképezése tulajdonságértékek, majd paraméterként térképek használata

Eltérő eredményt ad-alapú egy tulajdonság értéke, hozzon létre egy térképet, amely minden eredményt tulajdonság értéke megegyezik, akkor azt használja paraméterként.

Például a munkafolyamat paramétereket és a egy térképet, amely megfelel egy adott URL-cím ezekben a kategóriákban bizonyos kategóriák határozza meg. A munkafolyamat először cikkek listáját kéri le. Ezt követően a munkafolyamat használja a térkép az URL-egyeztetési az egyes cikkeket a kategóriát.

*   A [ `intersection()` ](../logic-apps/logic-apps-workflow-definition-language.md) függvény ellenőrzi, hogy kategória megegyezik-e ismert meghatározott kategória.

*   Egy megfelelő kategóriát, ha a példa lekéri a szögletes zárójelek segítségével a térkép elemet: `parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "science": "https://www.nasa.gov",
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
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
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

## <a name="get-data-with-date-functions"></a>Dátumfüggvények adatokat

Adatok beolvasása egy adatforrásból, amely nem natív módon támogatja a *eseményindítók*, használható dátum idő használata a functions, és a dátumokat helyette. Például ez a kifejezés megkeresi, mennyi e munkafolyamat lépései tart, a belső dolgozunk azon, hogy a külső:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Az a `order` művelet, kinyerési a `startTime`.
2. Az aktuális idő az első `utcNow()`.
3. Egy második kivonása:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   Használhat más időegység, például `minutes` vagy `hours`.

3. Most ezt a két értéket összehasonlíthatja. 

   Ha az első érték kisebb, mint a második értéket, majd több, mint egy másodperc megbízást először óta eltelt.

Formázhatja a dátumokat, karakterlánc formázót is használhatja. Ha például a RFC1123, amelyet [ `utcnow('r')` ](../logic-apps/logic-apps-workflow-definition-language.md). Tudjon meg többet [dátum formázása](../logic-apps/logic-apps-workflow-definition-language.md).

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
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
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
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

* [Hajtsa végre a lépéseket, egy feltételt (feltételes kifejezések) alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Hajtsa végre a lépéseket, a különböző értékek (switch-utasítások) alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ág)](../logic-apps/logic-apps-control-flow-branches.md)
* [Hajtsa végre a lépéseket, a csoportosított műveleti állapota (hatóköröket) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* Tudjon meg többet a [Azure Logic Apps munkafolyamat-definíciós nyelv sémája](../logic-apps/logic-apps-workflow-definition-language.md)
* Tudjon meg többet [munkafolyamat-műveletek és eseményindítók az Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md)
