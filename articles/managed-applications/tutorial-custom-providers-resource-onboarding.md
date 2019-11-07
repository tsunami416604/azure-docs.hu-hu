---
title: Erőforrás-előkészítés egyéni Azure-szolgáltatókkal
description: Az egyéni szolgáltatókon keresztüli erőforrás-előkészítés lehetővé teszi a meglévő Azure-erőforrások kezelését és kiterjesztését.
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: c722b4dc3219f76f8c7c571af3613996fec9e69c
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73608679"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Oktatóanyag: erőforrás-előkészítés az Azure egyéni szolgáltatókkal

Ez az oktatóanyag egy egyéni erőforrás-szolgáltatót helyez üzembe az Azure-ban, amely kiterjeszti a Azure Resource Manager API-t a "Microsoft. CustomProviders/Associations" erőforrás-típussal. Ez a minta azt mutatja be, hogyan terjeszthető ki az erőforráscsoport azon erőforrásain kívüli meglévő erőforrások, amelyeken az egyéni szolgáltató példánya él. Ebben a példában az egyéni erőforrás-szolgáltatót egy Azure logikai alkalmazás működteti, de a nyilvános API-végpontok is használhatók.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez ismernie kell a következőket:

* Az [Azure egyéni szolgáltatói](custom-providers-overview.md) képességei.
* [Erőforrás-előkészítés egyéni szolgáltatókkal](concepts-custom-providers-resourceonboarding.md).

## <a name="getting-started-with-resource-onboarding"></a>Az erőforrás-előkészítés első lépései

Ebben a példában két darabot kell telepíteni: az egyéni szolgáltatót és a társítást. A minta leegyszerűsítése érdekében egyetlen sablon is van, amely egyszerre telepíthető.

A sablon a következő erőforrások használatát teszi elérhetővé:

* Microsoft. CustomProviders/resourceProviders
* Microsoft. Logic/munkafolyamatok
* Microsoft. CustomProviders/társítások

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Az egyéni szolgáltatói infrastruktúra üzembe helyezése

A sablon első része az egyéni szolgáltatói infrastruktúra üzembe helyezése. Ez az infrastruktúra határozza meg a "társítások" erőforrás hatását. Ha nem ismeri az egyéni szolgáltatókat, tekintse meg az [egyéni szolgáltató alapjai](custom-providers-overview.md)című témakört.

Üzembe helyezheti az egyéni szolgáltatói infrastruktúrát, vagy másolhatja, mentheti és telepítheti a fenti sablont, vagy követheti és telepítheti a Azure Portal.

1. Nyissa meg a Azure Portalt https://portal.azure.com.

2. Keressen rá a "sablonok" kifejezésre `All Services` vagy a középső keresősáv. 

![Sablon keresése](media/custom-providers-resource-onboarding/templates.png)

3. A "sablonok" panel bal felső részén kattintson a `+ Add` gombra.

![Sablon hozzáadása új](media/custom-providers-resource-onboarding/templatesadd.png)

4. Töltse ki a "Name" és a "Description" mezőket az "általános" területen az új sablonhoz.

![Sablon neve és leírása](media/custom-providers-resource-onboarding/templatesdescription.png)

5. A Resource Manager-sablon kitöltéséhez másolja a JSON-sablont az "első lépések az erőforrás-előkészítéssel" című részből.

![Töltse ki a Resource Manager-sablont](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. Új sablon létrehozásához nyomja meg a `Add` gombot. Ha az új sablon nem jelenik meg, nyomja meg `Refresh`.

7. Válassza ki az újonnan létrehozott sablont, és nyomja le a `Deploy` gombot.

![Az újonnan létrehozott sablon kiválasztása és üzembe helyezése](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. Adja meg a kötelező mezők beállítási paramétereit, és válassza ki az előfizetést és az erőforráscsoportot. Az "egyéni erőforrás-szolgáltató azonosítója" nem maradhat üresen.

| Beállítás neve | Kötelező | Leírás |
| ------------ | -------- | ----------- |
| Hely | *igen* | A sablon erőforrásainak helye. |
| Logikai alkalmazás neve | *nem* | A logikai alkalmazás neve. |
| Egyéni erőforrás-szolgáltató neve | *nem* | Az egyéni erőforrás-szolgáltató neve. |
| Egyéni erőforrás-szolgáltató azonosítója | *nem* | Egy meglévő egyéni erőforrás-szolgáltató, amely támogatja a "társítás" erőforrást. A beállítás megadásával kihagyja a logikai alkalmazást és az egyéni szolgáltató telepítését. |
| Társítás neve | *nem* | A társítási erőforrás neve. |

Minta paramétereinek:

![Bemeneti sablon paraméterei](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. Navigáljon a központi telepítéshez, és várjon, amíg befejeződik. A sikeres megjelenítésnek és az új "társítás" erőforrás kimenetének kell megjelennie.

Sikeres üzembe helyezés:

![Egyéni szolgáltató üzembe helyezése](media/custom-providers-resource-onboarding/customproviderdeployment.png)

Erőforráscsoport "rejtett típusok megjelenítése":

![Egyéni szolgáltató üzembe helyezése](media/custom-providers-resource-onboarding/showhidden.png)

10. A "társítás" létrehozásával kapcsolatban tekintse meg a logikai alkalmazás "futtatási előzmények" lapját.

![Logikai alkalmazás futtatási előzményei](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>További társítások telepítése

Az egyéni szolgáltatói infrastruktúra beállítása után egyszerűen üzembe helyezhetők a további "társítások". A további "társítások" erőforráscsoport esetében nem kell megegyeznie az egyéni szolgáltatói infrastruktúra üzembe helyezéséhez használt erőforrás-csoporttal. Társítás létrehozásához a "Microsoft. CustomProviders/resourceproviders/Write" engedélyekre van szükség a megadott "egyéni erőforrás-szolgáltató azonosítója" esetén.

1. Navigáljon a "Microsoft. CustomProviders/resourceProviders" egyéni szolgáltatói erőforráshoz az előző üzemelő példány erőforráscsoporthoz. A "rejtett típusok megjelenítése" jelölőnégyzetet ki kell jelölni.

![Egyéni szolgáltató üzembe helyezése](media/custom-providers-resource-onboarding/showhidden.png)

2. Másolja az egyéni szolgáltató "erőforrás-azonosító" tulajdonságát.

3. Keressen rá a "sablonok" kifejezésre `All Services` vagy a középső keresősáv. 

![Sablon keresése](media/custom-providers-resource-onboarding/templates.png)

4. Válassza ki a korábban létrehozott sablont, és nyomja le a `Deploy` gombot.

![A korábban létrehozott sablon kiválasztása és üzembe helyezése](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. Adja meg a kötelező mezők beállítási paramétereit, és válassza ki az előfizetést és egy másik erőforráscsoportot. Az "egyéni erőforrás-szolgáltató azonosítója" beállításnál adja meg a korábban telepített egyéni szolgáltató "erőforrás-AZONOSÍTÓját".

6. Navigáljon a központi telepítéshez, és várjon, amíg befejeződik. Most csak az új "társítások" erőforrást kell üzembe helyezni.

![Létrehozott társítás](media/custom-providers-resource-onboarding/createdassociationresource.png)

Igény szerint visszatérhet a logikai alkalmazás "futtatási előzmények" lapjára, és láthatja, hogy a logikai alkalmazásnak egy másik hívása történt. A logikai alkalmazás frissíthető az egyes létrehozott társítások további funkcióinak bővítéséhez.

## <a name="looking-for-help"></a>Segítség keresése

Ha kérdése van az egyéni Azure-szolgáltatókkal kapcsolatban, próbálja meg megkérdezni [stack overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers). Előfordulhat, hogy egy hasonló kérdést már megtettek és megválaszoltak, ezért először A feladás előtt érdemes megnézni. A címke `azure-custom-providers` hozzáadásával gyorsan választ kaphat!

