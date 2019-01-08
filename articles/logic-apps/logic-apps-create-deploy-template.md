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
ms.openlocfilehash: 8714c91079d087b16a62af4af8b381edf6871f8b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065732"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Logic Apps-alkalmazások üzembe helyezéséhez Azure Resource Manager-sablonok létrehozása

Logic Apps-alkalmazás létrehozása után érdemes az Azure Resource Manager-sablonnal hozza létre.
Így egyszerűen telepítheti a logikai alkalmazás bármilyen környezetben vagy az erőforráscsoport lehet szükség.
Resource Manager-sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md) és [üzembe erőforrásokat az Azure Resource Manager-sablonok használatával](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Logikai alkalmazás központi telepítési sablont

Logic Apps-alkalmazás három alapvető részből áll:

* **Logikai alkalmazás erőforrásához**: Többek között a tervet, helyét és a munkafolyamat-definíció díjszabási információkat tartalmaz.
* **Munkafolyamat-definíció**: A logikai alkalmazás munkafolyamat-lépéseit, és hogyan hajtsa végre a Logic Apps-motor kell a munkafolyamatot ismerteti.
Ez a definíció tekintheti meg a logikai alkalmazás **Kódnézet** ablak.
Ez a definíció az annak az a logikai alkalmazás erőforrásához a `definition` tulajdonság.
* **Kapcsolatok**: A különálló erőforrásokat, amelyek biztonságos tárolása a metaadatokat, például egy kapcsolati karakterláncot és egy hozzáférési tokent minden olyan összekötő kapcsolatok hivatkozik.
A logikai alkalmazás erőforrásához, az a logikai alkalmazás erőforrásainak hivatkozik a `parameters` szakaszban.

Egy hasonló eszköz használatával megtekintheti a meglévő logic apps összes megtalálhatja [Azure erőforrás-kezelő](http://resources.azure.com). JSON-szintaxist és a Tulajdonságok [Microsoft.Logic erőforrás-típus](/azure/templates/microsoft.logic/allversions).

Ahhoz, hogy egy logikai alkalmazást az erőforráscsoportok üzemelő példányainak használandó sablont, kell erőforrásokat határozzák meg, és szükség szerint paraméterezni.
Például ha telepít egy fejlesztési, tesztelési és éles környezetben való, valószínűleg használni kívánt SQL-adatbázis különböző kapcsolati karakterláncokkal minden környezetben.
Vagy előfordulhat, hogy szeretne belül különböző előfizetések vagy erőforráscsoportok üzembe helyezése.  

## <a name="create-a-logic-app-deployment-template"></a>Hozzon létre egy logikai alkalmazás központi telepítési sablont

Rendelkezik egy érvényes logikai alkalmazás központi telepítési sablont a legegyszerűbb módja az, hogy használja a [a Logic Apps Visual Studio-eszközök](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
A Visual Studio-eszközök, bármely előfizetéssel vagy hellyel is használható érvényes központi telepítési sablont létrehozni.

Néhány egyéb eszközökkel is segítséget nyújt hoz létre egy logikai alkalmazás központi telepítési sablont.
Hozhat létre kézzel, azaz már tárgyalt Itt hozhat létre a paramétereket szükség szerint források segítségével.
Egy másik lehetőség egy [logic app sablon készítője](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell-modult. A nyílt forráskódú modul először kiértékeli a logikai alkalmazás és a kapcsolatokat, hogy azt használja, és akkor hoz létre a sablon erőforrásainak üzembe helyezéshez szükséges paraméterekkel.
Például ha egy logikai alkalmazást, amely egy üzenetet fogad egy Azure Service Bus-üzenetsorba, és adatokat ad hozzá egy Azure SQL database, az eszköz megőrzi a vezénylési-logika és felparaméterezi az SQL és a Service Bus kapcsolati karakterláncokat, hogy azok beállíthatja üzembe helyezése köz.

> [!NOTE]
> Kapcsolatok ugyanazt az erőforráscsoportot, a logikai alkalmazás belül kell lennie.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>A logikai alkalmazás sablon PowerShell modul telepítése
A modul telepítése a legegyszerűbb módja, keresztül a [PowerShell-galériából](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), a parancs segítségével `Install-Module -Name LogicAppTemplate`.  

Is telepítheti a PowerShell-modult manuálisan:

1. Töltse le a legújabb kiadását a [logic app sablon készítője](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Csomagolja ki a mappát a PowerShell modul mappába (általában `%UserProfile%\Documents\WindowsPowerShell\Modules`).

A modul működéséhez minden bérlői és az előfizetés hozzáférés-token, azt javasoljuk, hogy együtt használja, a [ARMClient](https://github.com/projectkudu/ARMClient) parancssori eszköz.  Ez [blogbejegyzés](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ARMClient ismerteti részletesebben.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>A logikaialkalmazás-sablon létrehozása PowerShell használatával
PowerShell telepítése után a sablon a következő parancs használatával is létrehozhat:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` az Azure-előfizetés azonosítója. Ezt a sort először lekérése hozzáférési token via ARMClient, majd átadja azt a a PowerShell-parancsfájlt, és majd a sablont hoz létre egy JSON-fájlban.

## <a name="add-parameters-to-a-logic-app-template"></a>Paraméterek hozzáadása a logikaialkalmazás-sablon
A logic app-sablon létrehozása után folytathatja felvenni vagy módosítani a paraméterek, amelyeket érdemes lehet. Például ha a definíciója tartalmaz egy erőforrás-azonosító egy Azure-függvényt vagy beágyazott munkafolyamatot, amely tervez üzembe helyezni egy központi telepítésben, rendeljen több erőforrást a sablonhoz, szabadon paraméterezni azonosítók, igény szerint. Ugyanez vonatkozik az egyéni API-k és a Swagger mutató hivatkozásokat végpontok tervez telepíteni az egyes erőforráscsoportokban.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Függő erőforrások mutató hivatkozások hozzáadása a Visual Studio a központi telepítési sablonok

Ha azt szeretné, hogy a logikai alkalmazás függő erőforrásokra kell hivatkoznia, [Azure Resource Manager-sablonfüggvények](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) az a logikai alkalmazás központi telepítési sablont. Például érdemes lehet a logikai alkalmazás egy Azure-funkció vagy integrációs fiók mellett a logikai alkalmazás üzembe helyezése kívánt hivatkozni. Kövesse ezeket az irányelveket kapcsolatos paraméterek használata a központi telepítési sablont úgy, hogy a Logic App Designerben az megfelelően jelenik meg. 

Az ilyen típusú eseményindítók és műveletek logic app paraméterek használhatók:

*   Gyermek munkafolyamat
*   Függvényalkalmazás
*   APIM-hívás
*   API-kapcsolat futásidejű URL-címe
*   API-kapcsolat elérési útja

És használhatja a sablonokban használható függvények például a paraméterek, változókat, erőforrás-azonosító, concat, stb. Ha például a következő hogyan lecserélheti az Azure-függvény erőforrás-azonosító:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

És ahol paramétereket használja:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Másik példaként, a Service Bus küldési üzenet művelet lehet paraméterezni:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl nem kötelező, és eltávolíthatja a sablon ha van ilyen.
> 


> [!NOTE] 
> A Logic App Designerben működéséhez paraméterek használatakor például az alapértelmezett értékeket, kell adnia:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>A logikai alkalmazás hozzáadása egy meglévő erőforráscsoport-projekt

Ha rendelkezik egy meglévő erőforráscsoport-projektet, a logikai alkalmazás erre a projektre a JSON-vázlat ablak is hozzáadhat. Azt is megteheti a korábban létrehozott app együtt egy másik logikai alkalmazást.

1. Nyissa meg az `<template>.json` fájlt.

2. A JSON-vázlat ablak megnyitásához nyissa meg **nézet** > **Other Windows** > **JSON-vázlat**.

3. Adjon hozzá egy erőforrást a sablonfájlt, kattintson a **erőforrás hozzáadása** a JSON-vázlat ablak tetején. A JSON-vázlat ablak, kattintson a jobb gombbal, vagy **erőforrások**, és válassza ki **új erőforrás hozzáadása**.

    ![JSON-vázlat ablak](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. Az a **erőforrás hozzáadása** párbeszédpanelen keresse meg és válassza ki **logikai alkalmazás**. Nevezze el a logikai alkalmazást, és válassza a **Hozzáadás**.

    ![Erőforrás hozzáadása](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Logic app-sablon üzembe helyezése

A sablon olyan eszközöket, mint például a PowerShell, REST API segítségével telepíthet [Azure DevOps Azure folyamatok](#team-services), és a sablon telepítése az Azure Portalon keresztül.
Emellett szeretné tárolni a paraméterek értékeit, javasoljuk, hogy hozzon létre egy [paraméterfájl](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Ismerje meg, hogyan [erőforrások üzembe helyezése Azure Resource Manager-sablonok és PowerShell](../azure-resource-manager/resource-group-template-deploy.md) vagy [erőforrások üzembe helyezése Azure Resource Manager-sablonokkal és az Azure Portalon](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>OAuth-kapcsolatok engedélyezése

Az üzembe helyezést követően az a logikai alkalmazás – teljes körű érvényes paraméterekkel működik.
Azonban továbbra is engedélyeznie kell egy érvényes hozzáférési jogkivonatot az OAuth-kapcsolatok.
OAuth-kapcsolatok hitelesítéséhez, nyissa meg a logikai alkalmazás a Logic Apps Designerben, és ezek a kapcsolatok hitelesítéséhez. Vagy az automatikus telepítési parancsfájl hogy engedélyt adjanak az egyes OAuth-kapcsolat.
Példa parancsfájl van a githubon a [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projekt.

<a name="team-services"></a>
## <a name="azure-devops-azure-pipelines"></a>Az Azure DevOps Azure folyamatok

Egy általános forgatókönyv üzembe helyezéséhez és felügyeletéhez egy környezetet, hogy egy eszköz, például az Azure-folyamatok az Azure DevOps, a logikai alkalmazás központi telepítési sablont. Az Azure DevOps tartalmaz egy [üzembe helyezése Azure-erőforráscsoport](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) feladat, amelyet minden build ad hozzá vagy kibocsátásában. Rendelkeznie kell egy [szolgáltatásnév](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) az engedélyezési üzembe helyezéséhez, és engedélyezi a kiadási folyamathoz hozhat létre.

1. Válassza ki az Azure-folyamatok, **üres** úgy, hogy létrehoz egy üres folyamatot.

    ![Üres folyamat létrehozása][1]

2. Válassza ki bármelyik van szüksége ennek, nagy valószínűséggel többek között a logikaialkalmazás-sablon manuálisan, vagy a létrehozási folyamat részeként létrehozott erőforrásokat.
3. Adjon hozzá egy **Azure erőforráscsoport-telepítés** feladat.
4. Állítson be egy [egyszerű szolgáltatás](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), és a sablon és paraméterek sablon fájlok hivatkoznak.
5. Továbbra is hozhatja létre a környezet, automatizált tesztelési vagy igény szerint a jóváhagyók a kibocsátási folyamat lépéseit.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
