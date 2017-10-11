---
title: "Séma frissíti 1 – 2015. augusztus előzetes verzió – Azure Logic Apps |} Microsoft Docs"
description: "Az Azure Logic Apps JSON-meghatározások létrehozása a séma verziója 2015-08-01. dátumú előnézeti"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: LADocs; stepsic
ms.openlocfilehash: 35d7a56d5607dcc18a4407c65b92962d3d0dcd1d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Az Azure Logic Apps - 2015. augusztus 1 preview sémafrissítések

Az új séma- és API-t az Azure Logic Apps verzió magában foglalja a fontos fejlesztést tartalmaz, amelyek a logic apps megbízhatóbb és könnyebben használható:

*   A **APIApp** művelettípus frissítése, hogy egy új [ **APIConnection** ](#api-connections) művelet típusa.
*   **Ismételje meg a** neve módosult az [ **Foreach**](#foreach).
*   A [ **HTTP-figyelő** API-alkalmazás](#http-listener) már nincs szükség.
*   Gyermek munkafolyamatok hívása használ egy [új séma](#child-workflows).

<a name="api-connections"></a>
## <a name="move-to-api-connections"></a>API-kapcsolatok áthelyezése

A legnagyobb módosítása, hogy már nem kell API-alkalmazások üzembe helyezés az Azure-előfizetéssel, így API-kat is használhatja. Az alábbiakban a módszereket, hogy API-kat:

* Felügyelt API-k
* Az egyéni webes API-k

Mindkét lehetőség némileg eltérően kell kezelni, mert a felügyelet és az üzemeltetési modellek nem egyezik. Ez a modell egyik előnye, még az Azure erőforráscsoportban már nem korlátozódik telepített erőforrások esetén. 

### <a name="managed-apis"></a>Felügyelt API-k

A Microsoft kezeli az egyes API-k a nevünkben, például az Office 365, a Salesforce, a Twitter és az FTP. Használhatja az egyes felügyelt API-k-van, például a Bing fordítására, míg más konfigurációs. Ez a konfiguráció elnevezése egy *kapcsolat*.

Például Office 365 használatakor kell létrehoznia az Office 365 bejelentkezési jogkivonatot tartalmaz egy kapcsolatot. Ez a token biztonságosan tárolhassa és frissítése, hogy a Logic Apps alkalmazást mindig meghívhatja az Office 365 API-t. Azt is megteheti Ha azt szeretné, az SQL- vagy FTP-kiszolgálóhoz való csatlakozáshoz, kell létrehoznia, amely rendelkezik a kapcsolati karakterlánc kapcsolatot. 

Ez a definíció az e műveletek végrehajtása nevezzük `APIConnection`. Itt látható egy példa, amely behívja az Office 365 e-mailt küldhet kapcsolatot:

```
{
    "actions": {
        "Send_Email": {
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
                "method": "post",
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

A `host` célja a bemeneti adatok része, amely egyedi API kapcsolatok, és fonókábel részeket tartalmazza: `api` és `connection`.

A `api` a futtatókörnyezet URL-CÍMÉT, amely a felügyeli, ahol API van-e tárolva van. Megtekintheti az összes elérhető felügyelt API-k hívásával `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview`.

API-t használ, amikor előfordulhat, hogy az API-t, vagy előfordulhat, hogy nem rendelkezik ilyennel *kapcsolódási paraméterek* definiálva. Ha nem az API-t, nem *kapcsolat* szükséges. Ha az API-t, létre kell hoznia egy kapcsolatot. A kapcsolat létrehozása a nevet, amely meg van. Majd hivatkozzon a neve a `connection` objektum belül a `host` objektum. Kapcsolat létrehozása egy erőforráscsoport, hívja meg:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

A következő szervezethez:

```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues": {
        "accountName": "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location": "{Logic app's location}"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Felügyelt API-k az Azure Resource Manager sablon telepítése

Létrehozhat egy teljes alkalmazás az Azure Resource Manager-sablonok, mindaddig, amíg az interaktív bejelentkezés nem szükséges.
Bejelentkezési szükség, ha minden, az Azure Resource Manager sablon állíthat be, de továbbra is meg kell keresse fel a portálra, ahol a kapcsolatok engedélyezéséhez. 

```
    "resources": [{
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
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
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
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
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
            }
        }
    }]
```

Ebben a példában, hogy a kapcsolatok csak olyan erőforrásokat, amelyek az erőforráscsoportban élő látható. A felügyelt API-k az előfizetésben elérhető hivatkozó.

### <a name="your-custom-web-apis"></a>Az egyéni webes API-k

Ha a saját API-k, nem Microsoft által felügyelt is, használja a beépített **HTTP** művelet hívására őket. Ideális élményt akkor a Swagger-végpont az API-érdemes felfedi. Ez a végpont lehetővé teszi, hogy a Logic App Designer lehet megjeleníteni az adatokat, és kiírja az API. Swagger a Tervező is csak jeleníti meg a be- és kimenetekkel nem átlátszó JSON-objektumként.

Az új bemutató példa `metadata.apiDefinitionUrl` tulajdonság:

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata": {
              "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method": "GET"
            }
        }
    }
}
```

Ha az Azure App Service Web API működteti, a Web API automatikusan megjelenik a Tervező elérhető műveletek listáját. Ha nem, illessze be az URL-címet közvetlenül kell. A Swagger-végpont kell lennie a hitelesítés nélküli is használható a Logic App Designer, habár maga a API bármilyen módszerrel, amely támogatja a Swagger biztonságossá teheti.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Hívás a 2015-08-01. dátumú előnézeti API-alkalmazások telepítése

Ha korábban telepítette az API-alkalmazások, hívása az alkalmazás és a **HTTP** művelet.

Ha a Dropbox segítségével tulajdonságlista-fájlok, például a **2014 12-01. dátumú előnézeti** verzió sémadefiníciót előfordulhat, hogy hasonlót:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
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
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Ehhez a példához hasonló egyenértékű HTTP-művelet hogyan hozhat létre, amíg változatlan marad a Logic app-definíciót a Paraméterek szakaszban:

```
{
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
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Útmutató alapján egyenként-ezeket a tulajdonságokat:

| A művelet tulajdonság | Leírás |
| --- | --- |
| `type` |`Http`ahelyett, hogy`APIapp` |
| `metadata.apiDefinitionUrl` |Ez a művelet a Logic App Designer használatához a metaadat-végpontjához, amely értékekből összeállított a következők:`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` |Értékekből összeállított:`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |Mindig`POST` |
| `inputs.body` |Az API-alkalmazás paraméterek azonos |
| `inputs.authentication` |Az API-alkalmazás hitelesítési azonos |

Ez a módszer minden API-alkalmazás műveletre kell működnie. Ne feledje azonban, hogy a korábbi API-alkalmazások támogatása megszűnt. Ezért át kell helyezni a két előző lehetőségeket, egy felügyelt API-t vagy az egyéni webes API-t üzemeltető.

<a name="foreach"></a>
## <a name="renamed-repeat-to-foreach"></a>Átnevezni a "repeat", "foreach"

A séma korábbi verziójához sok visszajelzései érkezett, amely **ismételje meg a** zavaró, és nem megfelelő rögzíti, amelyek **ismételje meg a** volt, a-each ciklus. Ennek eredményeképpen azt átnevezett `repeat` való `foreach`. Például korábban meg kellene írni:

```
{
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
}
```

Szeretné most a írni:

```
{
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
}
```

A függvény `@repeatItem()` korábban használt hivatkozhasson rá az az aktuális elem alatt többször is keresztül. Ez a funkció most egyszerűsítve `@item()`. 

### <a name="reference-outputs-from-foreach"></a>Hivatkozás kimeneteinek "foreach"

Az egyszerűség kedvéért a kimeneteinek `foreach` műveletek az objektum nincs burkolójuk `repeatItems`. Miközben az előző kimenetek `repeat` példa volt:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Most ezeket kimenetének létrehozása a következők:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Korábban való hivatkozáskor ezek kimenetek a szervezet művelet eléréséhez:

```
{
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
}
```

Most helyette teheti:

```
{
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
}
```

A módosítások a funkciók `@repeatItem()`, `@repeatBody()`, és `@repeatOutputs()` törlődnek.

<a name="http-listener"></a>
## <a name="native-http-listener"></a>Natív HTTP-figyelő

A HTTP-figyelő képességek most beépített. Így már nem kell HTTP Listener API-alkalmazások telepítéséhez. Lásd: [hogyan végezheti el a Logic app végpont hívható itt teljes részleteit](../logic-apps/logic-apps-http-endpoint.md). 

A módosítások eltávolítása a `@accessKeys()` függvénynek, amely azt írni a a `@listCallbackURL()` függvény az első a végpont, amikor erre szükség van. Emellett most definiálnia kell legalább egy eseményindító a Logic Apps alkalmazást. Ha azt szeretné, hogy `/run` a munkafolyamat, ezek az eseményindítók egyikét kell: `manual`, `apiConnectionWebhook`, vagy `httpWebhook`.

<a name="child-workflows"></a>
## <a name="call-child-workflows"></a>Gyermek-munkafolyamatok hívása

Korábban gyermek munkafolyamatok hívása szükség lesz a munkafolyamat, a hozzáférési jogkivonat beolvasása és a token beilleszteni a logic app-definíciót hívható meg, hogy alárendelt munkafolyamat helyét a. Az új sémával a Logic Apps-motor automatikusan létrehozza a gyermek munkafolyamat futásidőben SAS-kód, nem kell a titkos kulcsok illessze be a definíciót. Például:

```
"mynestedwf": {
    "type": "workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "myendpointtrigger"
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
