---
title: Oktatóanyag - erőforrás-bevezetés
description: Az egyéni szolgáltatókon keresztül történő erőforrás-bevezetés lehetővé teszi a meglévő Azure-erőforrások manipulálását és bővítését.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649938"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Oktatóanyag: Erőforrás-bevezetés az Azure egyéni szolgáltatókkal

Ebben az oktatóanyagban egy egyéni erőforrás-szolgáltatót telepít az Azure-ba, amely kiterjeszti az Azure Resource Manager API-t a Microsoft.CustomProviders/associations erőforrástípussal. Az oktatóanyag bemutatja, hogyan bővítheti a meglévő erőforrásokat, amelyek kívül esnek az egyéni szolgáltatópéldány t. Ebben az oktatóanyagban az egyéni erőforrás-szolgáltató egy Azure-logikai alkalmazás, de bármilyen nyilvános API-végponthasználható.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez tudnia kell:

* Az Azure [egyéni szolgáltatók](overview.md)képességei.
* Alapvető információk [az egyéni szolgáltatókkal való erőforrás-bevezetésről.](concepts-resource-onboarding.md)

## <a name="get-started-with-resource-onboarding"></a>Első lépések az erőforrások bevezetésével

Ebben az oktatóanyagban két darabot kell telepíteni: az egyéni szolgáltatót és a társítást. A folyamat megkönnyítése érdekében tetszés szerint használhat egyetlen sablont, amely mindkettőt telepíti.

A sablon a következő erőforrásokat fogja használni:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/munkafolyamatok
* Microsoft.CustomProviders/associations

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

A sablon első része az egyéni szolgáltatói infrastruktúrát telepíti. Ez az infrastruktúra határozza meg a társítások erőforrás hatását. Ha nem ismeri az egyéni szolgáltatókat, olvassa el az Egyéni szolgáltató alapjai című [témakört.](overview.md)

Telepítsük az egyéni szolgáltatói infrastruktúrát. Másolja, mentse és telepítse az előző sablont, vagy kövesse az infrastruktúrát az Azure Portal használatával.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)

2. **Sablonok** keresése a **Minden szolgáltatásban** vagy a fő keresőmező használatával:

   ![Sablonok keresése](media/tutorial-resource-onboarding/templates.png)

3. A **Sablonok** ablaktáblán válassza a **Hozzáadás** lehetőséget:

   ![Válassza a Hozzáadás lehetőséget](media/tutorial-resource-onboarding/templatesadd.png)

4. Az **Általános**csoportban adja meg az új sablon **nevét** és **leírását:**

   ![A sablon neve és leírása](media/tutorial-resource-onboarding/templatesdescription.png)

5. Hozza létre az Erőforrás-kezelő sablont a JSON-sablonban a cikk "Az erőforrás-bevezetés megkezdése" című részéből történő másolással:

   ![Resource Manager-sablon létrehozása](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. A sablon létrehozásához válassza a **Hozzáadás** lehetőséget. Ha az új sablon nem jelenik meg, válassza a **Frissítés**lehetőséget.

7. Jelölje ki az újonnan létrehozott sablont, majd válassza **a Telepítés**lehetőséget:

   ![Jelölje ki az új sablont, majd válassza a Telepítés lehetőséget](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Adja meg a szükséges mezők beállításait, majd válassza ki az előfizetést és az erőforráscsoportot. Az egyéni **erőforrás-szolgáltató azonosítója mezőt** üresen hagyhatja.

   | Beállítás neve | Kötelező? | Leírás |
   | ------------ | -------- | ----------- |
   | Hely | Igen | A sablonban lévő erőforrások helye. |
   | Logikai alkalmazás neve | Nem | A logikai alkalmazás neve. |
   | Egyéni erőforrásszolgáltató neve | Nem | Az egyéni erőforrás-szolgáltató neve. |
   | Egyéni erőforrásszolgáltató azonosítója | Nem | A társítási erőforrást támogató meglévő egyéni erőforrás-szolgáltató. Ha itt megad egy értéket, a logikai alkalmazás és az egyéni szolgáltató központi telepítése kimarad. |
   | Társítás neve | Nem | A társítási erőforrás neve. |

   Mintaparaméterek:

   ![Sablonparaméterek megadása](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Lépjen a központi telepítésre, és várja meg, amíg befejeződik. Meg kell jelennie valami hasonló a következő screenshot. Az új társítási erőforrásnak kimenetként kell látnia:

   ![Sikeres üzembe helyezés](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Az erőforráscsoport a **Rejtett típusok megjelenítése** beállítással:

   ![Egyéni szolgáltató telepítése](media/tutorial-resource-onboarding/showhidden.png)

10. Fedezze fel a logikai **alkalmazást: Az Előzmények** lap futtatása a társítás létrehozásához használt hívások megtekintéséhez:

    ![Logikai alkalmazás Előzmények futtatása](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>További társítások telepítése

Az egyéni szolgáltatói infrastruktúra beállítása után egyszerűen telepíthet további társításokat. A további társítások erőforráscsoportjának nem kell megegyeznie azzal az erőforráscsoporttal, amelyben az egyéni szolgáltató infrastruktúráját telepítette. Társítás létrehozásához microsoft.CustomProviders/resourceproviders/write engedéllyel kell rendelkeznie a megadott egyéni erőforrás-szolgáltató azonosítójához.

1. Nyissa meg az egyéni **szolgáltatóT, a Microsoft.CustomProviders/resourceProviders** erőforrást az előző központi telepítés erőforráscsoportjában. Be kell jelölnie a **Rejtett típusok megjelenítése** jelölőnégyzetet:

   ![Ugrás az erőforrásra](media/tutorial-resource-onboarding/showhidden.png)

2. Másolja az egyéni szolgáltató Erőforrás-azonosító tulajdonságát.

3. **Sablonok** keresése a **Minden szolgáltatásban** vagy a fő keresőmező használatával:

   ![Sablonok keresése](media/tutorial-resource-onboarding/templates.png)

4. Jelölje ki a korábban létrehozott sablont, majd válassza **a Telepítés**lehetőséget:

   ![Jelölje ki a korábban létrehozott sablont, majd válassza a Telepítés lehetőséget](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Adja meg a szükséges mezők beállításait, majd válassza ki az előfizetést és egy másik erőforráscsoportot. Az **egyéni erőforrás-szolgáltató azonosító** beállításához adja meg azt az erőforrás-azonosítót, amelyet a korábban telepített egyéni szolgáltatótól másolt.

6. Lépjen a központi telepítésre, és várja meg, amíg befejeződik. Most már csak az új társítások erőforrást kell telepítenie:

   ![Új társítások erőforrás](media/tutorial-resource-onboarding/createdassociationresource.png)

Ha szeretné, visszatérhet a logikai alkalmazás **futtatási előzmények,** és látni, hogy egy másik hívás történt a logikai alkalmazás. Frissítheti a logikai alkalmazást, hogy bővítse a további funkciók minden létrehozott társítás.

## <a name="getting-help"></a>Segítség kérése

Ha kérdése van az Azure egyéni szolgáltatókkal kapcsolatban, próbálja meg feltenni őket a [Veremtúlcsordulás.](https://stackoverflow.com/questions/tagged/azure-custom-providers) Lehet, hogy egy hasonló kérdésre már válaszoltak, ezért először a feladás előtt ellenőrizze. Add hozzá `azure-custom-providers` a címkét, hogy gyors választ kapj!

