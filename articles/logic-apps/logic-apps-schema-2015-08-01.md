---
title: Sémafrissítések az augusztus 1-2015 előzetes verzióhoz
description: Frissített séma verzió 2015-08-01-preview logikai alkalmazásdefiníciók az Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792846"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Sémafrissítések az Azure Logic Apps alkalmazáshoz – 2015.

Ez a séma- és API-verzió az Azure Logic Apps hez olyan kulcsfontosságú fejlesztéseket tartalmaz, amelyek megbízhatóbbá és könnyebben használhatóvá teszik a logikai alkalmazásokat:

* Az **APIApp-művelettípus** neve [**APIConnection**](#api-connections)lett.
* Az **Ismétlés** művelet neve Most [**Antól Foreach.**](#foreach)
* A [ **HTTP Listener** API-alkalmazás](#http-listener) már nincs szükség.
* Az alárendelt munkafolyamatok hívása [új sémát](#child-workflows)használ.

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Áthelyezés API-kapcsolatokra

A legnagyobb változás az, hogy már nem kell telepítenie az API-alkalmazásokat az Azure-előfizetésében, hogy API-kat használhasson. Az API-k használatával a következőképpen használhatja a következőket:

* Felügyelt API-k
* Az egyéni webes API-k

Minden út kezeli kissé másképp, mert a menedzsment és hosting modellek eltérőek. A modell egyik előnye, hogy már nem korlátozza az Azure-erőforráscsoportban üzembe helyezett erőforrásokat. 

### <a name="managed-apis"></a>Felügyelt API-k

A Microsoft az Ön nevében kezel bizonyos API-kat, például az Office 365-öt, a Salesforce-ot, a Twittert és az FTP-t. Egyes felügyelt API-kat ugyanúgy is használhat, mint például a Bing Fordító, míg mások nak konfigurációra van szükségük, más néven *kapcsolatra.*

Az Office 365 használataesetén például létre kell hoznia egy olyan kapcsolatot, amely tartalmazza az Office 365 bejelentkezési jogkivonatát. A jogkivonat biztonságosan van tárolva és frissítve, így a logikai alkalmazás mindig hívhatja az Office 365 API-t. Ha csatlakozni szeretne az SQL- vagy FTP-kiszolgálóhoz, létre kell hoznia egy olyan kapcsolatot, amely rendelkezik a kapcsolati karakterláncmal. 

Ebben a definícióban ezeket a műveleteket nevezik `APIConnection`. Íme egy példa egy olyan kapcsolatra, amely e-mail küldésére hívja az Office 365-öt:

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

Az `host` objektum az API-kapcsolatokra vonatkozó adatok része, és a `api` `connection`következő részeket tartalmazza: és . Az `api` objektum megadja a futásidejű URL-címet, ahol a felügyelt API található. Az összes elérhető felügyelt API-t a következő módszer hívásával láthatja:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

API használatakor előfordulhat, hogy az API-nak nincs *kapcsolati paramétere.* Így ha az API nem határozza meg ezeket a paramétereket, nincs kapcsolat szükséges. Ha az API határozza meg ezeket a paramétereket, létre kell hoznia egy kapcsolatot egy megadott névvel.  
Ezután hivatkozik erre `connection` a `host` névre az objektumon belüli objektumban. Ha erőforráscsoportban szeretne kapcsolatot létrehozni, hívja meg ezt a módszert:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

A következő testtel:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Felügyelt API-k telepítése Azure Resource Manager-sablonban

Ha nincs szükség interaktív bejelentkezésre, létrehozhat egy teljes alkalmazást egy Erőforrás-kezelő sablon használatával.
Ha bejelentkezésre van szükség, továbbra is használhatja a Resource Manager-sablont, de engedélyeznie kell a kapcsolatokat az Azure Portalon keresztül. 

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

Ebben a példában látható, hogy a kapcsolatok csak az erőforráscsoportban élő erőforrások. Hivatkoznak az előfizetésben elérhető felügyelt API-kra.

### <a name="your-custom-web-apis"></a>Az egyéni webes API-k

Ha a Microsoft által felügyelt ek helyett saját API-kat **HTTP** használ, a beépített HTTP-művelettel hívja meg az API-kat. Ideális esetben meg kell adnia egy Swagger-végpontot az API-hoz. Ez a végpont segít a Logic App Designer az API bemenetek és kimenetek megjelenítése. Swagger-végpont nélkül a tervező csak a bemenetek és kimenetek átlátszatlan JSON-objektumokként jeleníthető meg.

Íme egy példa, `metadata.apiDefinitionUrl` amely bemutatja az új tulajdonságot:

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

Ha az Azure App Service-ben üzemelteti a webes API-t, a webes API automatikusan megjelenik a tervezőben elérhető műveletek listájában. Ha nem, akkor közvetlenül be kell illesztenie az URL-t. A Swagger-végpont nem hitelesíthető, hogy használható legyen a Logic App Designer, bár az API-t is biztosíthatja bármilyen módszerekkel, amelyeket swagger támogatja.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Üzembe helyezett API-alkalmazások hívása a 2015-08-01-preview-val

Ha korábban telepített egy API-alkalmazást, meghívhatja az alkalmazást a **HTTP-művelettel.**
Ha például a Dropbox segítségével listázfájlokat, a **2014-12-01-preview séma** verziódefiníciója a következőhöz hasonló lehet:

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

Most már létrehozhat egy hasonló HTTP-műveletet, és `parameters` változatlanul hagyhatja a logikai alkalmazás definíciójának szakaszát, például:

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

Séta ezeken a tulajdonságokon keresztül egyenként:

| Művelet tulajdonság | Leírás |
| --- | --- |
| `type` | `Http`ahelyett, hogy`APIapp` |
| `metadata.apiDefinitionUrl` | Ha ezt a műveletet a Logic App Designer, adja meg a metaadat-végpont, amely épül:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | A következő ből készült:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Mindig`POST` |
| `inputs.body` | Megegyezik az API-alkalmazás paramétereivel |
| `inputs.authentication` | Megegyezik az API-alkalmazás hitelesítésének |

Ez a megközelítés az összes API-alkalmazás-művelethez működnie kell. Ne feledje azonban, hogy ezek a korábbi API-alkalmazások már nem támogatottak. Ezért lépjen át a két másik korábbi lehetőség, egy felügyelt API vagy az egyéni webes API üzemeltetésére.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Átnevezve "ismétlés" -re "foreach"

Az előző sémaverzióhoz sok vásárlói visszajelzést kaptunk arról, hogy a Művelet **ismétlése** név zavaró volt, és nem megfelelően rögzítette, hogy **az Ismétlés** valóban minden ciklusra szólt. Szóval, `repeat` átneveztük `foreach`a . Korábban ezt a műveletet a következő példához hasonlóan írta:

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

Most azt írja ezt a verziót helyett:

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

Továbbá, `repeatItem()` a függvény, amely hivatkozott a tétel, hogy a hurok feldolgozása `item()`során az aktuális iteráció, most átlett nevezve . 

### <a name="reference-outputs-from-foreach"></a>Referencia-outputok a "foreach"-ből

Az egyszerűsítés érdekében `foreach` a műveletek kimenetei `repeatItems`már nem kerülnek a neve súgás objektumba. Ezen a módosításnál `repeatItem()`a `repeatBody()`, `repeatOutputs()` a és a függvények is törlődnek.

Tehát az előző `repeat` példában ezeket a kimeneteket kapja:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Most kap ezeket a kimeneteket helyett:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Korábban, hogy `body` a műveletből, amikor ezekre a kimenetek:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Most már használhatja ezt a verziót helyette:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Natív HTTP-figyelő

A HTTP-figyelő funkciók mostmár beépítettek, így nem kell telepítenie egy HTTP Listener API-alkalmazást. További információ: a [logikai alkalmazás végpontjának hívássá tehető.](../logic-apps/logic-apps-http-endpoint.md) 

Ezekkel a módosításokkal a `@accessKeys()` Logic `@listCallbackURL()` Apps lecseréli a függvényt a függvényre, amely szükség esetén leváltja a végpontot. Emellett most meg kell határoznia legalább egy eseményindítót a logikai alkalmazásban. Ha a `/run` munkafolyamatot szeretné használni, az alábbi eseményindítótípusok egyikét kell használnia: `Manual`, `ApiConnectionWebhook`vagy`HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Gyermekmunkafolyamatok hívása

Korábban az alárendelt munkafolyamatok hívása szükséges a munkafolyamathoz való hozzáféréshez, a hozzáférési jogkivonat beszerzéséhez és a jogkivonat beillesztéséhez a logikai alkalmazás definíciójába, ahol meg szeretné hívni az adott alárendelt munkafolyamatot. Ezzel a sémával a Logic Apps motor automatikusan létrehoz egy SAS futásidőben a gyermek munkafolyamat, így nem kell beilleszteni a titkos kulcsokat a definícióba. Például:

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

Emellett az alárendelt munkafolyamatok teljes hozzáférést kapnak a bejövő kérelemhez. Tehát paramétereket adhat át `queries` a szakaszban `headers` és az objektumban. Teljes mértékben meghatározhatja `body` a teljes szakaszt is.

Végül az alárendelt munkafolyamatok rendelkeznek ezekkel a szükséges módosításokkal. Bár korábban és közvetlenül meghívhat egy alárendelt munkafolyamatot, most meg kell határoznia egy eseményindító végpontot a munkafolyamatban, hogy a szülő hívhassa. Általában hozzá kell adnia `Manual` egy eseményindítót, amely nek típusa van, majd használja ezt az eseményindítót a szülődefinícióban. A `host` tulajdonságnak kifejezetten van egy, `triggerName` mert mindig meg kell adnia a hívott eseményindítót.

## <a name="other-changes"></a>További változások

### <a name="new-queries-property"></a>Új "lekérdezések" tulajdonság

Mostantól minden művelettípus támogatja `queries`az új bemenetet, amelyet . Ez a bemenet lehet strukturált objektum, ahelyett, hogy kézzel kellene összeállítania a karakterláncot.

### <a name="renamed-parse-function-to-json"></a>Átnevezett "parse()" függvény "json()" névre.

A `parse()` függvény most átnevezi a függvényt a `json()` jövőbeli tartalomtípusokhoz.

## <a name="enterprise-integration-apis"></a>Vállalati integrációs API-k

Ez a séma még nem támogatja a vállalati integrációs API-k, például az AS2 felügyelt verzióit. A meglévő üzembe helyezett BizTalk API-k at azonban használhatja a HTTP-műveleten keresztül. További információt az [integrációs ütemterv](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)"A már telepített API-alkalmazások használata" című témakörben talál. 
