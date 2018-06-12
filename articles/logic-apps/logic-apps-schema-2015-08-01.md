---
title: Séma frissíti 1 – 2015. augusztus előzetes verzió – Azure Logic Apps |} Microsoft Docs
description: Az Azure Logic Apps JSON-meghatározások létrehozása a séma verziója 2015-08-01. dátumú előnézeti
author: stepsic-microsoft-com
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic; LADocs
ms.openlocfilehash: 736a7cf03c7fe1e9fe976c3bcc80393bff2bada5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299868"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Az Azure Logic Apps - 2015. augusztus 1 preview sémafrissítések

A séma- és API-t az Azure Logic Apps verzió magában foglalja a fontos fejlesztést tartalmaz, amelyek a logic apps megbízhatóbb és könnyebben használható:

* A **APIApp** helyett művelet típusa [ **APIConnection**](#api-connections).
* A **ismételje meg a** helyett művelet [ **Foreach**](#foreach).
* A [ **HTTP-figyelő** API-alkalmazás](#http-listener) már nincs szükség.
* Gyermek munkafolyamatok hívása használ egy [új séma](#child-workflows).

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>API-kapcsolatok áthelyezése

A legnagyobb módosítása, hogy már nem kell API-alkalmazások üzembe helyezés az Azure-előfizetéssel, így használhat API-k. Az alábbiakban a módszereket, hogy API-kat:

* Felügyelt API-k
* Az egyéni webes API-k

Mindkét lehetőség némileg eltérően kell kezelni, mert a felügyelet és az üzemeltetési modellek nem egyezik. Ez a modell egyik előnye, még az Azure erőforráscsoportban már nem korlátozódik telepített erőforrások esetén. 

### <a name="managed-apis"></a>Felügyelt API-k

A Microsoft kezeli az egyes API-k a nevünkben, például az Office 365, a Salesforce, a Twitter és az FTP. Használhatja az egyes felügyelt API-k-, például a Bing fordítására, míg más konfigurációs, néven is ismert egy *kapcsolat*.

Például Office 365 használatakor kell létrehoznia, amely tartalmazza az Office 365 bejelentkezési token kapcsolatot. A token biztonságosan tárolhassa és frissítése, hogy a Logic Apps alkalmazást mindig meghívhatja az Office 365 API-t. Ha azt szeretné, az SQL- vagy FTP-kiszolgálóhoz való csatlakozáshoz, létre kell hoznia egy kapcsolatot, amely kapcsolati karakterláncot. 

Ez a definíció az e műveletek végrehajtása nevezzük `APIConnection`. Itt látható egy példa, amely behívja az Office 365 e-mailt küldhet kapcsolatot:

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

A `host` objektum része egy a bemenetek, amely egyedi API-kapcsolatokat, és ezek a részek tartalmazza: `api` és `connection`. A `api` objektumot adja meg a futtatókörnyezet, ahol, amely felügyeli API URL-címe van-e tárolva. Megtekintheti az összes elérhető felügyelt API-k hívásával `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

API-t használ, amikor előfordulhat, hogy adott API, vagy előfordulhat, hogy nincsenek megadva a *kapcsolódási paraméterek*. Igen az API nem adja meg ezeket a paramétereket, ha nincs kapcsolat szükséges. Az API-t adja meg ezeket a paramétereket, ha egy adott nevű kapcsolatot kell létrehoznia.  
Ilyen nevű majd hivatkozik a `connection` objektum belül a `host` objektum. VPN-kapcsolat létrehozásához egy erőforráscsoport, ez a metódus meghívható:

```
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

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Felügyelt API-k az Azure Resource Manager sablon telepítése

Létrehozhat egy teljes app az Azure Resource Manager-sablonok, mindaddig, amíg az interaktív bejelentkezés nem szükséges.
Bejelentkezési szükség, ha minden, az Azure Resource Manager sablon állíthat be, de továbbra is meg kell látogasson el az Azure-portálon a kapcsolatok engedélyezéséhez. 

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

Ebben a példában, hogy a kapcsolatok csak olyan erőforrásokat, amelyek az erőforráscsoportban élő látható. A felügyelt API-k az előfizetésben elérhető hivatkozó.

### <a name="your-custom-web-apis"></a>Az egyéni webes API-k

Ha a saját API-k, nem Microsoft által felügyelt is, használja a beépített **HTTP** művelet hívására őket. Ideális élményt akkor a Swagger-végpont az API-érdemes felfedi. Ez a végpont lehetővé teszi, hogy a Logic App Designer lehet megjeleníteni az adatokat, és kiírja az API. Swagger a Tervező is csak jeleníti meg a be- és kimenetekkel nem átlátszó JSON-objektumként.

Az új bemutató példa `metadata.apiDefinitionUrl` tulajdonság:

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

Ha az Azure App Service Web API működteti, a Web API automatikusan megjelenik a Tervező elérhető műveletek listáját. Ha nem, illessze be az URL-címet közvetlenül kell. A Swagger-végpont kell lennie a hitelesítés nélküli is használható a Logic App Designer, habár maga a API bármilyen módszerrel, amely támogatja a Swagger biztonságossá teheti.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Hívás a 2015-08-01. dátumú előnézeti API-alkalmazások telepítése

Ha korábban telepítette az API-alkalmazások, hívása az alkalmazást a **HTTP** művelet.
Ha a Dropbox segítségével tulajdonságlista-fájlok, például a **2014 12-01. dátumú előnézeti** verzió sémadefiníciót előfordulhat, hogy hasonlót:

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

Most most összeállíthatja a megfelelő HTTP-művelet az alábbi példához hasonló ugyanakkor változatlanul a logic app-definíciót a Paraméterek szakaszban:

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

Útmutató alapján egyenként-ezeket a tulajdonságokat:

| A művelet tulajdonság | Leírás |
| --- | --- |
| `type` | `Http` ahelyett, hogy `APIapp` |
| `metadata.apiDefinitionUrl` | Ez a művelet a Logic App Designer használatához a metaadat-végpontjához, amely értékekből összeállított a következők: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Értékekből összeállított: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Mindig `POST` |
| `inputs.body` | Az API-alkalmazás paraméterek azonos |
| `inputs.authentication` | Az API-alkalmazás hitelesítési azonos |

Ez a módszer minden API-alkalmazás műveletre kell működnie. Ne feledje azonban, hogy a korábbi API-alkalmazások támogatása megszűnt. Ezért át kell helyezni a két előző lehetőségeket, egy felügyelt API-t vagy az egyéni webes API-t üzemeltető.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>Átnevezni a "repeat", "foreach"

A séma korábbi verziójához sok visszajelzései érkezett, amely a **ismételje meg a** műveletnév volt egyértelmű, és nem megfelelően rögzítésére, amely **ismételje meg a** volt, a-each ciklus. Igen, azt átnevezték `repeat` való `foreach`. Korábban kellene írni például ebben a példában ez a művelet:

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

Most ebben a verzióban helyette kellene írni:

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

Emellett a `repeatItem()` funkció, amely a hivatkozott elem a hurok végrehajtja az aktuális iteráció során, át lett nevezve `item()`. 

### <a name="reference-outputs-from-foreach"></a>Hivatkozás kimeneteinek "foreach"

Az egyszerűség kedvéért a kimeneteinek `foreach` műveletek már nem csomagolni nevű objektum `repeatItems`. Ezek a változások az is, a `repeatItem()`, `repeatBody()`, és `repeatOutputs()` funkciók törlődnek.

Igen, használja az előző `repeat` példában kap ezek kimenetek:

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

Most kap ezek kimenetek helyette:

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

Korábban az beszerzése a `body` való hivatkozáskor ezek kimenetek műveletből:

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

Most már használhatja ebben a verzióban helyette:

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

A HTTP-figyelő képességek most beépített. Így már nem kell HTTP Listener API-alkalmazások telepítéséhez. Lásd: [hogyan végezheti el a Logic app végpont hívható itt teljes részleteit](../logic-apps/logic-apps-http-endpoint.md). 

A módosítások eltávolítása a `@accessKeys()` függvénynek, amely azt írni a a `@listCallbackURL()` függvény az első a végpont, amikor erre szükség van. Emellett most definiálnia kell legalább egy eseményindító a Logic Apps alkalmazást. Ha azt szeretné, hogy `/run` a munkafolyamat, ezek az eseményindítók egyikét kell: `manual`, `apiConnectionWebhook`, vagy `httpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Gyermek-munkafolyamatok hívása

Korábban gyermek munkafolyamatok hívása szükség lesz a munkafolyamat, a hozzáférési jogkivonat beolvasása és a token beilleszteni a logic app-definíciót hívható meg, hogy alárendelt munkafolyamat helyét a. Az új sémával a Logic Apps-motor automatikusan létrehozza a gyermek munkafolyamat futásidőben SAS-kód, nem kell a titkos kulcsok illessze be a definíciót. Például:

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

Egy második fokozása azt hozzáadásakor jogosultságot ad a gyermek-munkafolyamatok teljes hozzáférés a bejövő kérelemre. Ez azt jelenti, hogy a paraméterek átadhatók a *lekérdezések* szakasz és a a *fejlécek* objektum és, hogy a teljes szervezet teljesen adhat meg.

Végezetül a gyermek munkafolyamat szükséges módosítások vannak. Amíg korábban hívhatja az alárendelt munkafolyamat közvetlenül, most meg kell adnia egy eseményindító végpont a szülő hívni a munkafolyamatban. Általában szeretné beállítani, amely rendelkezik egy eseményindító `manual` írja be, és a szülő-definícióban, hogy az eseményindító használja. Megjegyzés: a `host` kifejezetten a tulajdonságnak egy `triggerName` mert mindig meg kell adnia amelynek eseményindítója meghívott.

## <a name="other-changes"></a>Az egyéb módosítások

### <a name="new-queries-property"></a>Új "lekérdezések" tulajdonság

Minden művelettípusok mostantól egy új bemeneti nevű `queries`. A bemeneti strukturált objektum ahelyett, hogy át kellene kézzel állítsa össze a karakterlánc lehet.

### <a name="renamed-parse-function-to-json"></a>Átnevezett "parse()" függvény "json()"

Azt ad hozzá több tartalomtípus hamarosan, így azt átnevezték a `parse()` függvény `json()`.

## <a name="coming-soon-enterprise-integration-apis"></a>Hamarosan: vállalati integrációs API-k

Felügyelt verziói még a vállalati integrációs API-k, például a AS2 jelenleg nem áll. Ugyanakkor a meglévő üzembe helyezett BizTalk API-k használatával a HTTP-művelet is használhatja. További információkért lásd "A már telepített API-alkalmazások használata" a a [integrációs terv](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/). 
