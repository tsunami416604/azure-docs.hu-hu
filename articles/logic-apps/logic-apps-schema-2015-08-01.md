---
title: A-1 – 2015 augusztus előzetes verzió – Azure Logic Apps sémafrissítések |} A Microsoft Docs
description: Frissített séma verziója 2015-08-01-preview a logikaialkalmazás-definíciók az Azure Logic Appsben
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: ec6f98ca0f0260a0d7bed16538f557931cd2e33e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080010"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Az Azure Logic Apps – 2015. augusztus 1-én előzetes sémafrissítések

A séma- és API-t az Azure Logic Apps-verzió magában foglalja a kulcsfontosságú fejlesztések, amelyek a logic apps megbízhatóbb és könnyebben használható:

* A **APIApp** művelettípus helyett [ **APIConnection**](#api-connections).
* A **ismételje meg a** művelet helyett [ **Foreach**](#foreach).
* A [ **HTTP-figyelő** API-alkalmazás](#http-listener) már nem szükséges.
* Gyermek munkafolyamat hívása használ egy [új sémára](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Ugrás az API-kapcsolatok

A legnagyobb módosítás, hogy már nincs helyezheti üzembe az Azure-előfizetés az API Apps, hogy az API-kat is használhat. Itt módja az, hogy API-kat használhatja:

* Felügyelt API-k
* Az egyéni webes API-k

Minden egyes módja kissé különbözően történik, mivel a felügyeleti és az üzemeltetési modellek különböző. Ez a modell egyik előnye, még az Azure-erőforráscsoportban már nem korlátozott üzembe helyezendő erőforrások. 

### <a name="managed-apis"></a>Felügyelt API-k

Az Ön nevében, például az Office 365-höz, a Salesforce, a Twitter és a FTP bizonyos API-k a Microsoft felügyeli. Használhatja az egyes felügyelt API-, például a Bing-fordítás, míg mások a konfigurációt igényelnek, néven is ismert egy *kapcsolat*.

Például ha Office 365-höz használ, létre kell hoznia egy kapcsolatot, amely tartalmazza az Office 365 bejelentkezési token. A token biztonságosan tárolja, és frissítve, hogy a logikai alkalmazás mindig hívhatja meg az Office 365 API-t. Ha azt szeretné, az SQL- vagy FTP-kiszolgálóhoz való csatlakozáshoz, létre kell hoznia egy kapcsolatot, amely rendelkezik a kapcsolati karakterláncot. 

Ez a definíció az műveleteknek nevezzük `APIConnection`. Íme egy példa, amely meghívja az Office 365 e-mail küldése a kapcsolatot:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

A `host` objektum a bemeneti adatok egy részét, amely egyedi API-kapcsolatok, és ezeket a részeket tartalmazza: `api` és `connection`. A `api` objektum adja meg az URL-CÍMÉT, amely a felügyelt, ahol API runtime. A metódus meghívásának hatására minden rendelkezésre álló felügyelt API is látható:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

API-t használ, amikor API előfordulhat, hogy vagy a nem meghatározott bármely *kapcsolódási paraméterek*. Tehát az API nem adja meg ezeket a paramétereket, ha nincs kapcsolat nem szükséges. Ha az API-hoz meg ezeket a paramétereket, létre kell hoznia egy kapcsolatot a megadott néven.  
Ezután ezt a nevet a hivatkozik a `connection` belüli objektum a `host` objektum. Hozzon létre egy kapcsolatot egy erőforráscsoportban, hívja meg ezt a módszert:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

A következő szervezethez:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Felügyelt API-k az Azure Resource Manager-sablon üzembe helyezése

Interaktív bejelentkezési nem szükséges, ha a Resource Manager-sablon használatával létrehozhat egy teljes alkalmazás.
Ha be szükség, továbbra is használhatja a Resource Manager-sablonnal, de rendelkezik az Azure Portalon a kapcsolatok hitelesítéséhez. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

Ebben a példában, hogy a kapcsolatok forrásanyag csak az erőforráscsoportban élő láthatja. A felügyelt API-k az előfizetésben elérhető hivatkozó.

### <a name="your-custom-web-apis"></a>Az egyéni webes API-k

Ha a Microsoft által felügyelt eszközök helyett a saját API-k, használhatja a beépített **HTTP** művelet az API-k meghívására. Ideális esetben egy Swagger-végpont kell adnia az API-hoz. Ez a végpont az API-k bemenetek és kimenetek megjelenítése Logikaialkalmazás-tervező segítségével. Egy Swagger-végpont nélkül a tervező csak megjelenítheti a bemenetek és kimenetek átlátszatlan JSON-objektumként.

Íme egy példa az új `metadata.apiDefinitionUrl` tulajdonság:

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Ha a webes API-t az Azure App Service, a webes API automatikusan megjelenik a tervezőben az elérhető műveletek listáját. Ha nem, illessze be az URL-címet közvetlenül kell. A Swagger-végpont is használható a Logic App Designerben, bár a biztonságossá teheti a saját maga az API bármilyen módon, amely támogatja a Swagger-nem hitelesített kell lennie.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>2015-08-01-preview alkalmazások üzembe helyezett API meghívása

Ha korábban már üzembe helyezett API-alkalmazás, az adott alkalmazás meghívhatja a **HTTP** művelet.
Például, ha a Dropbox használatával fájlok listázása a **2014-12-01-preview** verzió sémadefiníciót előfordulhat, hogy hasonló:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Most, most már hozhat létre egy hasonló HTTP-műveletet, és hagyja a logikai alkalmazás definíciójában `parameters` szakasz változatlan marad, például:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Ezek a Tulajdonságok egyesével-ajánljuk figyelmébe:

| A művelet tulajdonság | Leírás |
| --- | --- |
| `type` | `Http` ahelyett, hogy `APIapp` |
| `metadata.apiDefinitionUrl` | Ez a művelet használata a Logic App Designerben, a metaadatok végpontján, amelyet értékekből összeállított a következők: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Értékekből összeállított: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Mindig `POST` |
| `inputs.body` | Ugyanaz, mint az API-alkalmazás paraméterek |
| `inputs.authentication` | Ugyanaz, mint az API-alkalmazás hitelesítése |

Ez a megközelítés is működnie kell az összes API-alkalmazás műveletekhez. Ne feledje azonban, hogy az előző API-alkalmazás már nem támogatottak. Ezért át kell helyezni a két előző lehetőségekért, egy felügyelt API-t vagy az egyéni webes API-t üzemeltető.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Neve a "repeat" a "foreach"

A korábbi verziójú, sok ügyfélvisszajelzések érkezett, amely a **ismételje meg a** műveletnév zavaros volt, és nem megfelelően rögzítése, amelyek **ismételje meg a** volt, a for-each ciklusban. Tehát, hogy átnevezték `repeat` való `foreach`. Korábban ez a művelet, mint ebben a példában lenne írása:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Most már ebben a verzióban inkább volna írni:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

Emellett a `repeatItem()` függvény, amely a cikk a hurok az aktuális iteráció során feldolgozó hivatkozott, nevezve `item()`. 

### <a name="reference-outputs-from-foreach"></a>A "foreach" hivatkozási kimenetek

Az egyszerűség kedvéért a kimenetei `foreach` műveletek nevű objektum már nem burkolja `repeatItems`. Ezeket a módosításokat az is, a `repeatItem()`, `repeatBody()`, és `repeatOutputs()` függvények el lesznek távolítva.

Így használja az előző `repeat` például kap ezeket a kimeneteket:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Mostantól megkapja ezeket a kimeneteket helyette:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Korábban beolvasni a `body` , a műveletet, amikor ezeket a kimeneteket hivatkozik:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Most már Ehelyett használhatja a verzió:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "http://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Natív HTTP-figyelő

HTTP-figyelő szolgáltatások immár beépített, így nem kell a HTTP Listener API-alkalmazás üzembe helyezéséhez. További információ megtudhatja, hogyan [győződjön meg arról, a logic app-végpont használata hívható](../logic-apps/logic-apps-http-endpoint.md). 

Ezeket a módosításokat, a Logic Apps váltja fel a `@accessKeys()` függvény és a `@listCallbackURL()` függvény, amely lekérdezi a végpontot, amikor erre szükség van. Ezenkívül mostantól definiálni kell legalább egy triggert a logikai alkalmazásban. Ha azt szeretné, hogy `/run` a munkafolyamat kell használni a következő eseményindító-típusok egyikének megfelelő: `Manual`, `ApiConnectionWebhook`, vagy `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Hívja a gyermek munkafolyamat

Korábban a gyermek munkafolyamat hívása szükséges fog a munkafolyamat, bevezetés a hozzáférési jogkivonatot, és a jogkivonat beillesztése a logikai alkalmazás definíciójában, ahol a gyermek munkafolyamat hívni kívánt. Ebben a sémában a Logic Apps-motor automatikusan hoz létre a gyermek munkafolyamat futtatáskor SAS, így nem kell a titkos kulcsok illessze be a definíció. Például:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Gyermek-munkafolyamatok is, a bejövő kérelem teljes hozzáférést kap. Tehát paramétereket adhat át a `queries` szakasz és az a `headers` objektum. Teljesen is megadhatja a teljes `body` szakaszban.

Végül a gyermek munkafolyamat kell ezeket a szükséges módosításokat. Bár korábban volt, és közvetlenül az alárendelt munkafolyamatot hívja, meg kell határoznia egy eseményindító végpont most már a munkafolyamat a szülőhöz meghívásához. Általában a rendelkező eseményindító lenne hozzáadása `Manual` írja be, és használja az eseményindító a szülő-definícióban. A `host` kifejezetten a tulajdonságnak egy `triggerName` mivel mindig meg kell adnia az eseményindító próbáljuk hívni.

## <a name="other-changes"></a>Egyéb módosítások

### <a name="new-queries-property"></a>Új "queries" tulajdonságot

Összes művelettípus mostantól támogatják a néven egy új bemenet `queries`. A bemenet egy strukturált objektum ahelyett, hogy a karakterlánc összeállítása manuálisan kellene is lehet.

### <a name="renamed-parse-function-to-json"></a>Átnevezett "parse()" függvény "json()"

A `parse()` függvény lett nevezve a `json()` függvény a jövőbeli tartalomtípusokat.

## <a name="enterprise-integration-apis"></a>Vállalati integráció API-k

Ez a séma még nem támogatja a felügyelt verziók a vállalati integrációs API-k, például az AS2. Meglévő telepített BizTalk API-k használatával a HTTP-művelet is használhatja. További információkért lásd "A már üzembe helyezett API-alkalmazások használatával" az a [integrációs ütemterv](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
