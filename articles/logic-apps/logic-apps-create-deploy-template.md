---
title: Üzembehelyezési sablonok létrehozása az Azure Logic Apps |} A Microsoft Docs
description: Logic Apps-alkalmazások üzembe helyezéséhez Azure Resource Manager-sablonok létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894234"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Logic Apps-alkalmazások üzembe helyezéséhez Azure Resource Manager-sablonok létrehozása

Amikor létrehoz egy logikai alkalmazást, a logikai alkalmazás definícióját is bővítheti az [Azure Resource Manager-sablon](../azure-resource-manager/resource-group-overview.md). Ezután használhatja ezt a sablont az erőforrások meghatározásával központi telepítések automatizálásához és paramétereket szeretne használt központi telepítési és a paraméterértékek keresztül egy [paraméterfájl](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Ezzel a módszerrel telepítheti a logic apps könnyebben bármilyen környezetben vagy az Azure-erőforráscsoportot, és szeretne. 

Az Azure Logic Apps biztosít egy [előre létrehozott logikai alkalmazások az Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) , hogy újból felhasználhatja, nem csak a logikai alkalmazások létrehozásának, hanem az erőforrások és a paraméterek a telepítéshez meghatározásához. Ez a sablon használata a saját üzleti forgatókönyvek esetén, vagy testre szabhatja a sablont az igényeknek. Tudjon meg többet [Resource Manager-sablonok szerkezetének és szintaxisának](../azure-resource-manager/resource-group-authoring-templates.md). JSON-szintaxist és a Tulajdonságok [Microsoft.Logic erőforrás-típus](/azure/templates/microsoft.logic/allversions).

Az Azure Resource Manager-sablonokkal kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [Szerzői Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md)
* [Fejlesztés a felhőben konzisztencia az Azure Resource Manager-sablonokkal](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Logic app-struktúra

A logikai alkalmazás definíciójának rendelkezik ezen alapvető szakaszokat, és megtekintheti váltásával "Tervező nézetben", "Kódnézet", vagy egy eszköz használatával [Azure erőforrás-kezelő](http://resources.azure.com). Logikaialkalmazás-definíciók Javascript Object Notation (JSON) használja, ezért JSON-szintaxist és tulajdonságaival kapcsolatos további információkért lásd: [Microsoft.Logic erőforrás-típus](/azure/templates/microsoft.logic/allversions).

* **Logikai alkalmazás erőforrásához**: Ismerteti az adatokat, például a logikai alkalmazás hely vagy régió, árképzési csomag és munkafolyamat-definíció.

* **Munkafolyamat-definíció**: A logikai alkalmazás eseményindítók és műveletek, és hogyan az Azure Logic Apps szolgáltatás fut-e a munkafolyamatot ismerteti. A kód nézetben, a munkafolyamat-definíció található az `definition` szakaszban.

* **Kapcsolatok**: Ha használja a felügyelt összekötők a logikai alkalmazást, a `$connections` szakasz más erőforrások, amelyek ezeket a kapcsolatokat, a logikai alkalmazás és más rendszerek vagy a szolgáltatások, például kapcsolati karakterláncok és a hozzáférési jogkivonatok közötti metaadatait tárolja biztonságosan hivatkozik. Belül a logikai alkalmazás definíciójának ezeket a kapcsolatokat mutató hivatkozások jelennek meg a logikai alkalmazás definíciójának belül `parameters` szakaszban.

Létrehoz egy logikaialkalmazás-sablon használható az Azure-erőforráscsoportok üzemelő példányainak, erőforrásokat határozzák meg, és szükség szerint paraméterezni. Például ha telepít egy fejlesztési, tesztelési és éles környezetben való, valószínűleg használni kívánt SQL-adatbázis különböző kapcsolati karakterláncokkal minden környezetben.
Vagy előfordulhat, hogy szeretne belül különböző előfizetések vagy erőforráscsoportok üzembe helyezése.

## <a name="create-logic-app-deployment-templates"></a>Logikai alkalmazás üzembehelyezési sablonok létrehozása

A legegyszerűbben úgy, hogy egy érvényes logikai alkalmazás központi telepítési sablont létrehozni használja a Visual Studio és az Azure Logic Apps Tools for Visual Studio-bővítmény. Töltse le az Azure Portalról a logikai alkalmazás a Visual studióba, kap egy érvényes központi telepítési sablont, amely bármely Azure-előfizetést és helyet használhat. Emellett a logikai alkalmazás automatikusan letöltése felparaméterezi a logikai alkalmazás definíciójában a sablonban beágyazott.
Létrehozásával és a logic apps a Visual Studióban kezelésével kapcsolatos további információkért lásd: [logikai alkalmazások létrehozása a Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) és [logikai alkalmazások Visual studióval kezelése](../logic-apps/manage-logic-apps-with-visual-studio.md).

A Visual Studio és a manuális létrehozásához a sablon és a szükséges paraméterek ebben a témakörben található útmutatást követve is használhatja a [logic app-sablonok létrehozása a PowerShell-modul](https://github.com/jeffhollan/LogicAppTemplateCreator). A nyílt forráskódú modul először kiértékeli a logikai alkalmazás és a logikai alkalmazás által használt kapcsolatokat. A modul akkor hoz létre a sablon erőforrásainak üzembe helyezéshez szükséges paraméterekkel. Tegyük fel például, egy logikai alkalmazást, amely egy üzenetet fogad egy Azure Service Bus-üzenetsorba, és adatokat ad hozzá egy Azure SQL database. A modul eszköz megőrzi a vezénylési összes logikát, és az SQL és a Service Bus kapcsolati karakterláncok felparaméterezi, hogy ezeket az értékeket is megadhatja a központi telepítéskor.

> [!IMPORTANT]
> Kapcsolatok léteznie kell az Azure-erőforráscsoport a logikai alkalmazással.
> A PowerShell-modul dolgozhat bármely Azure bérlői és az előfizetés hozzáférési jogkivonat, használja a modult a következővel a [Azure Resource Manager-ügyfél eszköz](https://github.com/projectkudu/ARMClient). További információkért lásd: Ez [a cikk az Azure Resource Manager-ügyfél eszközzel kapcsolatos](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ARMClient ismerteti részletesebben.

### <a name="install-powershell-module-for-logic-app-templates"></a>Az a logikai alkalmazások sablonjai PowerShell moduljának telepítése

A legegyszerűbb módja a modul telepítéséhez az [PowerShell-galériából](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), használja a következő parancsot:

`Install-Module -Name LogicAppTemplate`

Manuálisan is telepítheti a PowerShell-modult:

1. Töltse le a legújabb [Logic App sablon készítője](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Csomagolja ki a mappát a PowerShell-modul mappáját, amely általában a `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Hozzon létre logikaialkalmazás-sablon – PowerShell

PowerShell telepítése után a sablon a következő parancs használatával is létrehozhat:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` az Azure-előfizetés azonosítója. Ezt a sort először lekérése hozzáférési token via ARMClient, majd átadja azt a a PowerShell-parancsfájlt, és majd a sablont hoz létre egy JSON-fájlban.

## <a name="parameters-in-logic-app-templates"></a>Paramétereket a logikai alkalmazások sablonjai

A logic app-sablon létrehozása után adja hozzá, és szerkessze a szükséges paramétereket. A sablon egynél több rendelkezik `parameters` részben, például: 

* A logikai alkalmazás munkafolyamat-definíció rendelkezik a saját [ `parameters` szakasz](../logic-apps/logic-apps-workflow-definition-language.md#parameters) ahol megadhatja a paramétereket a logikai alkalmazás által a bemenetek üzembe helyezéskor elfogadásával.

* A Resource Manager-sablon rendelkezik a saját [ `parameters` szakasz](../azure-resource-manager/resource-group-authoring-templates.md#parameters), külön a logikai alkalmazás `parameters` szakaszban. Példa:

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Tegyük fel például, hogy a logikai alkalmazás definíciójának hivatkozik, amely egy Azure-függvényt, vagy a beágyazott logikaialkalmazás-munkafolyamat egy erőforrás-azonosító, és szeretné központilag telepíteni, hogy az erőforrás-azonosító és a logikai alkalmazás egy egyetlen központi telepítési. Hozzáadhatja erőforrásként a sablonban szereplő Azonosítóval és paraméterezni a azonosítója. Ugyanezzel a módszerrel az egyéni API-k vagy OpenAPI végpontok mutató hivatkozásokat is használhat (a korábban "Swagger"), amelyet központilag telepített az egyes Azure-erőforráscsoportot.

Ha paraméterek a központi telepítési sablont használ, ezt az útmutatót követve, így a Logic Apps Designerben megfelelően jeleníti meg ezeket a paramétereket:

* Csak ezek triggereket és műveleteket a paramétereket használja:

  * Az Azure Functions-alkalmazás
  * A beágyazott vagy gyermek logikaialkalmazás-munkafolyamat
  * Az API Management-hívás
  * API-kapcsolat futásidejű URL-címe
  * API-kapcsolat elérési útja

* Amikor a paraméter határozza meg, győződjön meg arról, hogy Ön adja meg az alapértelmezett értékeket a a `defaultValue` tulajdonság értékét, például:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Biztonságos, vagy a sablonok bizalmas adatok elrejtése, tegye biztonságossá a paramétereket. Tudjon meg többet [biztonságos paraméterek használata](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

A következő példa bemutatja, hogyan lehet paraméterezni az Azure Service Bus "Üzenet küldése" művelet:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Hivatkozás a függő erőforrások

Ha a logikai alkalmazásnak szüksége van a tőle függő erőforrások mutató hivatkozásokat, [Azure Resource Manager-sablonfüggvények](../azure-resource-manager/resource-group-template-functions.md) a logikai alkalmazás központi telepítési sablont. Például tegyük fel, hogy a logikai alkalmazás való hivatkozáshoz az Azure-függvényekre vagy -partnerek, szerződések és más összetevőket szeretne ügyfélkódtárat, a logikai alkalmazás definíciókkal rendelkező integrációs fiókban.
Használhatja például a Resource Manager-sablonfüggvények, `parameters`, `variables`, `resourceId`, `concat`, és így tovább.

A következő példa bemutatja, hogyan lecserélheti az erőforrás-azonosító az Azure-függvény paraméterek megadásával:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Itt látható, hogyan használhatja ezeket a paramétereket az Azure-függvény való hivatkozáskor:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Erőforráscsoport-projekt hozzáadása a logikai alkalmazás

Ha rendelkezik egy meglévő Azure erőforráscsoport-projektet, a JSON-vázlat ablak segítségével hozzáadhat a logikai alkalmazás erre a projektre. Azt is megteheti a korábban létrehozott app együtt egy másik logikai alkalmazást.

1. A Megoldáskezelőben nyissa meg a `<template>.json` fájlt.

2. Az a **nézet** menüjében válassza **Other Windows** > **JSON-vázlat**.

3. Adjon hozzá egy erőforrást a sablonfájlt, válassza a **erőforrás hozzáadása** a JSON-vázlat ablak tetején. A JSON-vázlat ablak, kattintson a jobb gombbal, vagy **erőforrások**, és válassza ki **új erőforrás hozzáadása**.

   ![JSON-vázlat ablak](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. Az a **erőforrás hozzáadása** párbeszédpanelen keresse meg és válassza ki **logikai alkalmazás**. Nevezze el a logikai alkalmazást, és válassza a **Hozzáadás**.

   ![Erőforrás felvétele](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Támogatás kérése

A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logic app-sablonok üzembe helyezése](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
