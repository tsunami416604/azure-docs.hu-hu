---
title: Logikai alkalmazások létrehozása az Azure Resource Manager-sablonok – Azure Logic Apps |} A Microsoft Docs
description: Létrehozása és üzembe helyezése a logikai alkalmazások munkafolyamataiba az Azure Logic Apps Azure Resource Manager-sablonokkal
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 274fa3153f6a354b121b7fd8338d8e0d2d94f4f4
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230812"
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Logikai alkalmazások az Azure Resource Manager-sablonok létrehozását és telepítését

Az Azure Logic Apps biztosít az Azure Resource Manager-sablonok, amelyet használhat, nem csak automatizálhatja a munkafolyamatokat logikai alkalmazásokat hozhat létre, hanem az erőforrásokat és üzembe helyezéshez használt paraméterek meghatározására. Ez a sablon használata a saját üzleti forgatókönyvek esetén, vagy testre szabhatja a sablont az igényeknek. Tudjon meg többet a [Resource Manager-sablon a logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) és [Azure Resource Manager-sablonok szerkezetének és szintaxisának](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Adja meg a logikai alkalmazás

Ebben a példában logic app-definíciójának óránként egyszer futtatja, és Pingeli a megadott helyen a `testUri` paraméter.
A sablon paraméter értékét használja a logikai alkalmazás nevét (```logicAppName```) és a teszteléshez ping helye (```testUri```). Tudjon meg többet [ezek a paraméterek meghatározása a sablonban](#define-parameters). A sablon ugyanarra a helyre, az Azure-erőforráscsoportot, a logikai alkalmazás helyét is beállítja. 

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
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
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

Az alábbiakban a sablonban szereplő paraméterekkel leírása:

| Paraméter | Leírás | Példa JSON-definíció | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Meghatározza a nevét, hogy a sablon létrehoz a logikai alkalmazás. | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | Tesztelési pingelni a hely határozza meg. | "testUri": {"type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

Tudjon meg többet [REST API a Logic Apps munkafolyamat-definíció és tulajdonságok](https://docs.microsoft.com/rest/api/logic/workflows) és [logikaialkalmazás-definíciók JSON-ra épülő](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Logikai alkalmazások automatikus telepítése

Hozzon létre, és automatikusan egy logikai alkalmazás üzembe helyezése az Azure-ba, válassza a **üzembe helyezés az Azure** itt:

[![Üzembe helyezés az Azure-ban](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ez a művelet bejelentkezik, az Azure Portalt, ahol adja meg a logikai alkalmazás részleteit, és végezze el a módosításokat a sablon és paraméterek. Például az Azure Portalon kéri az alábbi részleteket:

* Azure-előfizetés neve
* Használni kívánt erőforráscsoportot
* Logikai alkalmazás helye
* A logikai alkalmazás nevét
* Egy teszt URI
* A megadott feltételek és kikötések elfogadása

## <a name="deploy-logic-apps-with-commands"></a>A logic apps-parancsokkal üzembe helyezése

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
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)