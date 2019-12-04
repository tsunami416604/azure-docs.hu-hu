---
title: A séma frissítései augusztus-1-2015 előzetes verzióra
description: 'Frissített séma-verzió: 2015-08-01 – előzetes verzió a Logic app-definíciók számára Azure Logic Apps'
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: b6746baaede777eb8c2afcae9eb3fe80b669c468
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792846"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Séma frissítései Azure Logic Apps – augusztus 1., 2015 előzetes verzió

A Azure Logic Apps sémája és API-verziója olyan kulcsfontosságú funkciókat tartalmaz, amelyek megbízhatóbb és könnyebben használható logikai alkalmazásokat tesznek elérhetővé:

* A **APIApp** művelet típusa mostantól [**APIConnection**](#api-connections).
* Az **Ismétlési** művelet már [**foreach**](#foreach)néven szerepel.
* A [ **http-figyelő** API-alkalmazásra](#http-listener) már nincs szükség.
* A gyermek munkafolyamatok hívása egy [új sémát](#child-workflows)használ.

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Áttérés az API-kapcsolatokra

A legnagyobb változás az, hogy már nem kell API Apps telepíteni az Azure-előfizetésbe, hogy az API-kat használhassa. Az API-k az alábbi módokon használhatók:

* Felügyelt API-k
* Egyéni webes API-k

Az egyes módokat némileg másképp kezelik, mert a felügyeleti és üzemeltetési modelljeik eltérőek. A modell egyik előnye, hogy már nem korlátozza az Azure-erőforráscsoporthoz üzembe helyezett erőforrásokat. 

### <a name="managed-apis"></a>Felügyelt API-k

A Microsoft felügyel néhány API-t az Ön nevében, például az Office 365, a Salesforce, a Twitter és az FTP szolgáltatáshoz. Bizonyos felügyelt API-kat használhat, például a Bing fordítását, míg mások konfigurációt igényelnek, más néven a *kapcsolódást*.

Ha például az Office 365-et használja, létre kell hoznia egy olyan kapcsolatokat, amely tartalmazza az Office 365 bejelentkezési jogkivonatát. A token tárolása és frissítése biztonságos, így a logikai alkalmazás mindig meghívhatja az Office 365 API-t. Ha az SQL-vagy FTP-kiszolgálóhoz szeretne csatlakozni, létre kell hoznia egy olyan kapcsolatot, amely rendelkezik a kapcsolati karakterlánccal. 

Ebben a definícióban ezeket a műveleteket `APIConnection`nevezzük. Az alábbi példa egy olyan kapcsolódási példát mutat be, amely az Office 365 e-mail küldését hívja meg:

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

A `host` objektum az API-kapcsolatokhoz egyedi bemenetek részét képezi, és a következő részeket tartalmazza: `api` és `connection`. A `api` objektum azt a futásidejű URL-címet adja meg, ahol a felügyelt API üzemeltetve van. Az összes elérhető felügyelt API-t megtekintheti a következő módszer meghívásával:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Ha API-t használ, az API lehet, hogy nincs megadva *kapcsolódási paraméter*. Tehát ha az API nem határozza meg ezeket a paramétereket, nincs szükség kapcsolatok megadására. Ha az API definiálja ezeket a paramétereket, létre kell hoznia egy, a megadott névvel rendelkező kapcsolatokat.  
Ezt követően a `connection` objektumban hivatkozhat erre a névre a `host` objektumon belül. Ha egy erőforráscsoportot szeretne létrehozni, hívja meg a következő metódust:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

A következő törzstel:

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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Felügyelt API-k központi telepítése Azure Resource Manager sablonban

Ha nincs szükség interaktív bejelentkezésre, létrehozhat egy teljes alkalmazást egy Resource Manager-sablon használatával.
Ha be kell jelentkeznie, továbbra is használhatja a Resource Manager-sablonokat, de a Azure Portalon keresztül engedélyeznie kell a kapcsolatokat. 

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

Ebben a példában láthatja, hogy a kapcsolatok csak az erőforráscsoporthoz tartozó erőforrások. Az előfizetésben elérhető felügyelt API-kra hivatkoznak.

### <a name="your-custom-web-apis"></a>Egyéni webes API-k

Ha a Microsoft által felügyelt helyett saját API-kat használ, használja a beépített **http** -műveletet az API-k meghívásához. Ideális esetben meg kell adnia egy hencegő végpontot az API-hoz. Ez a végpont segít a Logic app Designerben az API bemeneteit és kimeneteit megjeleníteni. Hencegő végpont nélkül a tervező csak átlátszatlan JSON-objektumokként jeleníti meg a bemeneteket és kimeneteket.

Az alábbi példa az új `metadata.apiDefinitionUrl` tulajdonságot mutatja be:

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

Ha Azure App Serviceon futtatja a webes API-t, a webes API automatikusan megjelenik a tervezőben elérhető műveletek listájában. Ha nem, akkor közvetlenül be kell illesztenie az URL-címet. A hencegő végpontnak nem hitelesítettnek kell lennie ahhoz, hogy használható legyen a Logic app Designerben, de saját maga is biztonságossá teheti az API-t bármilyen, a hencegés által támogatott módszerrel.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Meghívásos üzembe helyezett API-alkalmazások a 2015-08-01-es verzióban – előzetes verzió

Ha korábban üzembe helyezett egy API-alkalmazást, meghívhatja az alkalmazást a **http** -művelettel.
Ha például a Dropbox használatával kívánja kilistázni a fájlokat, a **2014-12-01-** es verziójú sémájának definíciója a következőhöz hasonló lehet:

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

Most már létrehozhat egy hasonló HTTP-műveletet, és a Logic app-definíció `parameters` szakaszát változatlanul hagyhatja, például:

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

Ezeket a tulajdonságokat egy-az-egyhez:

| Művelet tulajdonsága | Leírás |
| --- | --- |
| `type` | `APIapp` helyett `Http` |
| `metadata.apiDefinitionUrl` | Ha ezt a műveletet a Logic app Designerben szeretné használni, foglalja bele a metaadatok végpontját, amely a következőkből áll: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Kiépítés forrása: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Mindig `POST` |
| `inputs.body` | Ugyanaz, mint az API-alkalmazás paramétereinek |
| `inputs.authentication` | Ugyanaz, mint az API-alkalmazás hitelesítése |

Ennek a megközelítésnek működnie kell az összes API app-művelet esetében. Ne feledje azonban, hogy ezek az előző API Appsek már nem támogatottak. Ezért érdemes áttérnie a két másik korábbi lehetőségre, egy felügyelt API-ra vagy az egyéni webes API-k üzemeltetésére.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Átnevezve: "REPEAT" – "foreach"

A séma korábbi verziójában sok vásárlói visszajelzést kaptunk, hogy az **Ismétlési** művelet neve zavaros volt, és nem volt megfelelően rögzítve **, hogy az ismétlés valóban** a-minden hurokhoz tartozik. Ezért a `repeat` átnevezve `foreach`re. Korábban a következő példához hasonló műveletet kell megírnia:

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

Most Ehelyett ezt a verziót kell megírnia:

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

Továbbá a `repeatItem()` függvény, amely arra hivatkozott, hogy az aktuális iteráció során a hurok feldolgozására használt elemre hivatkozik, a rendszer most átnevezi `item()`. 

### <a name="reference-outputs-from-foreach"></a>A "foreach" kimenetének hivatkozási eredményei

Az egyszerűsítés érdekében a `foreach` műveletekből származó kimenetek már nincsenek becsomagolva egy `repeatItems`nevű objektumba. Ezekkel a módosításokkal a `repeatItem()`, `repeatBody()`és `repeatOutputs()` függvények is törlődnek.

Tehát az előző `repeat` példa használatával ezeket a kimeneteket kapja:

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

Most ezeket a kimeneteket kapja:

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

Korábban a következő kimenetekre való hivatkozáskor a művelet `body`ának beszerzéséhez:

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

Most már használhatja ezt a verziót:

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

A HTTP-figyelő funkciói már beépítettek, így nem kell telepítenie egy HTTP-figyelő API-alkalmazást. További információért olvassa el, hogyan [teheti meg a logikai alkalmazás végpontjának meghívását](../logic-apps/logic-apps-http-endpoint.md). 

Ezekkel a módosításokkal a Logic Apps lecseréli a `@accessKeys()` függvényt a `@listCallbackURL()` függvénnyel, amely szükség esetén lekéri a végpontot. Emellett meg kell adnia legalább egy triggert a logikai alkalmazásban. Ha `/run` a munkafolyamatot, a következő trigger-típusok egyikét kell használnia: `Manual`, `ApiConnectionWebhook`vagy `HttpWebhook`

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Alárendelt munkafolyamatok hívása

Korábban a gyermek munkafolyamatok hívása szükséges a munkafolyamathoz, a hozzáférési token beszerzése és a token beillesztése a logikai alkalmazás definíciójában, ahol a gyermek munkafolyamatot hívni kívánja. Ezzel a sémával a Logic Apps motor automatikusan létrehoz egy SAS-t futásidőben a gyermek munkafolyamathoz, így nem kell semmilyen titkot beillesztenie a definícióba. Például:

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

Emellett a gyermek munkafolyamatok teljes hozzáférést kapnak a bejövő kérelemhez. Így a paramétereket át lehet adni a `queries` szakaszban és a `headers` objektumban. A teljes `body` szakaszt teljes egészében is meghatározhatja.

Végül a gyermek munkafolyamatok rendelkeznek a szükséges módosításokkal. Egy alárendelt munkafolyamatot korábban és közvetlenül hívhat meg, ezért meg kell határoznia egy trigger-végpontot a szülő által hívni kívánt munkafolyamatban. Általában olyan triggert kell hozzáadnia, amely `Manual` típust tartalmaz, majd ezt az triggert használja a szülő definíciójában. A `host` tulajdonságnak külön `triggerName` kell lennie, mert mindig meg kell adnia a meghívott triggert.

## <a name="other-changes"></a>Egyéb változások

### <a name="new-queries-property"></a>Új "lekérdezések" tulajdonság

Az összes Művelettípus mostantól támogatja a `queries`nevű új bemenetet. Ez a bemenet lehet strukturált objektum, nem pedig kézzel kell összeállítani a sztringet.

### <a name="renamed-parse-function-to-json"></a>A "parse ()" függvény átnevezve a következőre: "JSON ()"

A `parse()` függvény most átnevezte a `json()` függvényt a jövőbeli tartalomtípusokra.

## <a name="enterprise-integration-apis"></a>Vállalati integráció API-k

Ez a séma még nem támogatja a felügyelt verziókat Vállalati integráció API-k, például az AS2 számára. A meglévő telepített BizTalk API-kat azonban a HTTP-művelettel is használhatja. További információ: "a már telepített API-alkalmazások használata" az [integrációs ütemtervben](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
