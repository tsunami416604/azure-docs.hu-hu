---
title: "A logic apps létrehozása az Azure Resource Manager-sablonok alapján |} Microsoft Docs"
description: "Létrehozhat és telepíthet a logic app munkafolyamatok Azure Resource Manager-sablonok"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9e696f6e4614052456cf2b55123d98d61b8b3b9c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Logikai alkalmazások az Azure Resource Manager-sablonok létrehozása és telepítése

Az Azure Logic Apps biztosít az Azure Resource Manager sablonokban használható, nem csak a logic apps automatizálhatja a munkafolyamatok létrehozásához, hanem az erőforrások és a központi telepítéshez használt paraméterek megadása. Ez a sablon a saját üzleti helyzetekben használhatja, vagy állítsa be a sablon az igényeinek megfelelő. További információ a [a logic apps Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) és [Azure Resource Manager sablon struktúra és szintaxis](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Adja meg a logikai alkalmazás

A példa logic app-definíciót óránként egyszer fut, és Pingeli a megadott helyre a `testUri` paraméter.
Használja a sablon paraméterértékeket logic app nevét (```logicAppName```) és a hely tesztelési pingelése (```testUri```). További információ [ezeket a paramétereket adjon meg a sablon a](#define-parameters). A sablon is állítja be a logikai alkalmazás helyét és az Azure erőforráscsoport ugyanazon a helyen. 

``` json
{
    "type": "Microsoft.Logic/workflows",
    "apiVersion": "2016-06-01",
    "name": "[parameters('logicAppName')]",
    "location": "[resourceGroup().location]",
    "tags": {
        "displayName": "LogicApp"
    },
    "properties": {
        "definition": {
            "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "testURI": {
                "type": "string",
                "defaultValue": "[parameters('testUri')]"
                }
            },
            "triggers": {
                "Recurrence": {
                    "type": "Recurrence",
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            },
            "actions": {
                "Http": {
                    "type": "Http",
                    "inputs": {
                        "method": "GET",
                        "uri": "@parameters('testUri')"
                    },
                    "runAfter": {}
                }
            },
            "outputs": {}
        },
        "parameters": {}
    }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>Paraméterek megadása

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Az alábbiakban a sablon paramétereihez tartozó leírások:

| Paraméter | Leírás | JSON-definíció példa | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | A nevét adja meg a logikai alkalmazást az, hogy a sablon hoz létre. | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | Meghatározza a tesztelési ping helyét. | "testUri": {"type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

További információ [REST API-t a Logic Apps munkafolyamat-definíciót és tulajdonságok](https://docs.microsoft.com/rest/api/logic/workflows) és [logikai alkalmazás definícióiról rendelkező JSON kialakításának](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>A logic apps automatikus központi telepítés

Hozzon létre, és egy logikai alkalmazás automatikus telepítése az Azure-ba, válassza a **az Azure telepítéséhez** itt:

[![Üzembe helyezés az Azure-ban](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ez a művelet bejelentkezik, az Azure portálra, ahol adja meg a logikai alkalmazás adatait, és végezze el a módosításokat a sablonból vagy a paraméterek. Például az Azure-portálon kérni fogja ezeket az adatokat:

* Azure-előfizetés neve
* Használni kívánt erőforráscsoportot
* Logic app helye
* A logikai alkalmazás nevét
* Egy teszt URI
* A megadott feltételek és kikötések elfogadása

## <a name="deploy-logic-apps-with-commands"></a>A logic apps-parancsokkal telepítése

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Azure CLI

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)