---
title: Adja meg a JSON - Azure Logic Apps munkafolyamatok |} Microsoft Docs
description: "Munkafolyamat-definícióhoz írásával a JSON-ban a logic Apps alkalmazások"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7f9e5a10066df8a464c285273e77a85c0d562ebb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>A logic apps segítségével JSON munkafolyamat-meghatározások létrehozása

A munkafolyamat-definícióhoz hozhat létre [Azure Logic Apps](logic-apps-what-are-logic-apps.md) egyszerű, deklaratív JSON nyelv. Ha még nem tette meg, olvassa el [az első logikai alkalmazás létrehozása a Logic App tervezővel](logic-apps-create-a-logic-app.md). További tájékoztatás a [hivatkozás a Munkafolyamatdefiníciós nyelve a teljes](http://aka.ms/logicappsdocs).

## <a name="repeat-steps-over-a-list"></a>Ismételje meg a során

Olyan tömb, amely legfeljebb 10 000 elem iterációt, és minden elem egy műveletet, használja a [foreach típus](logic-apps-loops-and-scopes.md).

## <a name="handle-failures-if-something-goes-wrong"></a>Kijavíthassa a hibákat, ha valamilyen hiba

Általában olyan szeretne hozzáadni egy *szervizelési lépés* – néhány logika, amely végrehajtja a *csak, ha* egy vagy több, a hívás sikertelen. Ebben a példában adatok lekérése a különböző helyek, de a hívás sikertelen lesz, ha azt szeretné-e valahol utáni egy üzenetet, így azt követheti nyomon, hogy hiba le később:  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

Annak meghatározása, hogy `postToErrorMessageQueue` csak futtat `readData` rendelkezik `Failed`, használja a `runAfter` tulajdonságot, például a lehetséges értékek listáját adja meg, hogy `runAfter` lehet `["Succeeded", "Failed"]`.

Végül, ez a példa most kezeli a hiba, mert azt már nem jelölje meg a Futtatás mint `Failed`. Mivel azt adja meg a lépés kezelése ebben a példában ez a hiba, rendelkezik-e a Futtatás `Succeeded` bár egy lépésben `Failed`.

## <a name="execute-two-or-more-steps-in-parallel"></a>Két vagy több lépést végre párhuzamosan

Ezzel párhuzamosan több műveletek futtatására a `runAfter` tulajdonságot meg kell futásidőben. 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

Ebben a példában is `branch1` és `branch2` futtatása után értékre van beállítva `readData`. Ennek eredményeképpen a mindkét ágak párhuzamosan futnak. Mindkét ágak időbélyegzője megegyezik.

![Párhuzamos](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>Csatlakozás két párhuzamos ág

Két műveletek elemek hozzáadásával párhuzamosan futó beállított csatlakozhat a `runAfter` tulajdonság az előző példában látható módon.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![Párhuzamos](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>Egy másik konfigurációs listaelemek leképezése

Ezt követően tegyük fel, hogy azt szeretné, hogy a tulajdonság értékének alapján különböző tartalom. Paraméterként létrehozhatunk olyan értékek célhelyekre térképet:  

```
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

Ebben az esetben azt először kapnak a cikkek listáját. A paraméterként megadott kategória alapján, a második lépésben használja a térkép kereséséhez a tartalom első URL-CÍMÉT.

Néhány eset Itt figyelembe venni: 

*   A [ `intersection()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) függvény ellenőrzi, hogy a kategória megegyezik-e ismert meghatározott kategóriák közül.

*   Miután azt lekérése a kategóriát, azt is lekéréses szögletes zárójelbe használatával leképezés elem:`parameters[...]`

## <a name="process-strings"></a>Folyamat karakterláncok

Különböző funkciókat karakterláncok módosítására használhatja. Tegyük fel például, a karakterlánc, amely azt szeretnénk, hogy egy system van, de jelenleg nem megfelelő kezelését a karakteres kódolási kapcsolatos benne. Egy elem Base64 kódolással. Ez a karakterlánc kódolása. Azonban egy URL-címben escape-karaktersorozat elkerüléséhez fogjuk néhány karakterek. 

Szeretnénk továbbá rendelés nevének egy részét, mert az első öt karakterek nem használhatók.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Munka a belül a kívül:

1. Beolvasása a [ `length()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) a megrendelő megadásához, így azt vissza karakterek száma.

2. Kivonás 5, mert azt szeretnénk, ha egy rövidebb karakterláncot.

3. Ténylegesen, igénybe vehet a [ `substring()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring). Először indexnél `5` és nyissa meg a fennmaradó karakterlánc.

4. A keresendő karakterláncrészletet átalakítani egy [ `base64()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) karakterlánc.

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)minden a `+` karakterből álló `-` karaktereket.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace)minden a `/` karakterből álló `_` karaktereket.

## <a name="work-with-date-times"></a>Időpontok használata

Időpontok hasznos lehet, különösen olyan adatforrást, amelynek a természetes nem támogatja olvasnak be adatokat próbál *eseményindítók*. Használhatja a időpontok mennyi ideig számos lépés végzése kereséséhez.

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
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

Ez a példa azt bontsa ki a `startTime` az előző lépésben. Ezután azt beolvasni az aktuális idejét, és egy második kivonása:

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

Például használhatja más időegységekkel `minutes` vagy `hours`. E két érték végül azt is összehasonlíthatja. Ha az első érték kisebb, mint a második érték, akkor több mint egy második megbízást először óta eltelt.

Dátumok formázásához karakterlánc is használhatja azt. Például a RFC1123 beszerzéséhez használjuk [ `utcnow('r')` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow). Dátum formázás, lásd: [Munkafolyamatdefiníciós nyelve](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## <a name="deployment-parameters-for-different-environments"></a>A különböző környezetek üzembe helyezéshez megadott paraméterek

Gyakran központi telepítési életciklusának rendelkezik, a környezet, egy átmeneti és éles környezetben. Például előfordulhat, hogy ugyanazon definíció ezekben a környezetekben használható, de különböző adatbázist használja. Hasonlóképpen érdemes ugyanazon definíció különböző régiókban használja a magas rendelkezésre állású, de szeretné, hogy felvegye a adott régióban adatbázis minden logic app-példány.
Ebben a forgatókönyvben eltér paraméterek véve *futásidejű* ahol Ehelyett használjon a `trigger()` az előző példában szemléltetett működéséhez.

Kezdésként használhatja az ebben a példában például egy alapszintű definíciója:

```
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

A tényleges `PUT` kérhetnek a logic apps, megadhatja, hogy a paraméter `uri`. Mivel az alapértelmezett értéke már nem létezik, a logic app forgalma kell ezt a paramétert:

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
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

Minden környezetben, adjon meg más értéket a a `connection` paraméter. 

Az összes lehetséges, hogy rendelkezik, és logic Apps alkalmazásokat kezeléséhez, tekintse meg a [REST API-dokumentáció](https://msdn.microsoft.com/library/azure/mt643787.aspx). 
