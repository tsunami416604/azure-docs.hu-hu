---
title: "A központi telepítési sablonok létrehozása az Azure Logic Apps |} Microsoft Docs"
description: "A logic Apps alkalmazások telepítése Azure Resource Manager-sablonok létrehozása"
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: SyntaxC4
editor: 
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: LADocs; estfan
ms.openlocfilehash: 91d93a02bb9bf48c5bda0304c9d3d52c22e30209
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>A logic Apps alkalmazások telepítése Azure Resource Manager-sablonok létrehozása

A logikai alkalmazás létrehozása után érdemes az Azure Resource Manager sablon létrehozásához.
Így könnyen telepíthet a logikai alkalmazást a környezetben vagy az erőforráscsoport lehet szükség.
A Resource Manager-sablonok kapcsolatban bővebben lásd: [Azure Resource Manager sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md) és [erőforrások telepítése Azure Resource Manager-sablonok segítségével](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Logikai alkalmazás központi telepítési sablont

A logikai alkalmazás három alapvető részből áll:

* **Logic app erőforrás**: többek között a terv, helyét és a munkafolyamat-definíció díjszabási információkat tartalmaz.
* **Munkafolyamat-definíciót**: a Logic Apps alkalmazást munkafolyamat lépéseket, és hogyan hajtható végre a Logic Apps motor kell a munkafolyamatot ismerteti.
Ez a definíció meg tudja tekinteni a logikai alkalmazás **kódnézetben** ablak.
A logic app erőforrás, ez a definíció megtalálhatja a `definition` tulajdonság.
* **Kapcsolatok**: biztonságosan tárolni a összekötő-kapcsolatokat, például egy kapcsolati karakterláncot és a hozzáférési token metaadatainak erőforrásokat külön hivatkozik.
A logic app erőforrás, a logikai alkalmazás erőforrásainak hivatkozik a `parameters` szakasz.

Egy eszköz, például a meglévő logic Apps alkalmazások az összes adatot megtekintheti [Azure erőforrás-kezelő](http://resources.azure.com).

Ahhoz, hogy az erőforrás-csoport központi telepítésével használandó logikai alkalmazás sablonját, adja meg az erőforrások és parametrizálja igény szerint kell.
Például ha egy fejlesztési, tesztelési és éles környezetben telepít, valószínűleg használni kívánt SQL-adatbázis különböző kapcsolati karakterláncai minden környezetben.
Vagy előfordulhat, hogy szeretné belül különböző előfizetésekhez vagy erőforráscsoportok telepítéséhez.  

## <a name="create-a-logic-app-deployment-template"></a>Hozzon létre egy logic app központi telepítési sablont

A legegyszerűbb módja annak, hogy egy érvényes logikai alkalmazás központi telepítési sablont, hogy használja a [Visual Studio eszközök a Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
A Visual Studio eszközök, amelyek segítségével bármely előfizetés vagy a hely érvényes központi telepítési sablont hoz létre.

Néhány más eszközök is segítséget nyújt logic app központi telepítési sablont hoz létre.
Hozhat létre kézzel, ez azt jelenti, hogy a paraméterek létrehozásához, igény szerint már Microsofttól erőforrások használatával.
Egy másik lehetőség egy [logic app sablon létrehozó](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell-modult. A nyílt forráskódú modul először kiértékeli a logikai alkalmazást és a kapcsolatokat, hogy azt használja, és akkor hoz létre a telepítéshez szükséges paraméterekkel rendelkező sablon erőforrásokat.
Például ha egy logikai alkalmazás, amely egy üzenetet kapott egy Azure Service Bus-üzenetsorba, és adatokat ad hozzá Azure SQL-adatbázis, az eszköz megőrzi az orchestration logikai és az SQL és a Service Bus kapcsolati karakterláncok parameterizes, így azok állíthat be központi telepítési.

> [!NOTE]
> Kapcsolatok ugyanabban az erőforráscsoportban, a logikai alkalmazás tartományba kell esnie.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>A logic app sablon PowerShell moduljának telepítése
A modul telepítése a legegyszerűbb módja keresztül a [PowerShell-galériában](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), a parancs segítségével `Install-Module -Name LogicAppTemplate`.  

Is telepítheti a PowerShell modul manuálisan:

1. Töltse le a legújabb kiadása a [logic app sablon létrehozó](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Bontsa ki a mappát a PowerShell modul mappába (általában `%UserProfile%\Documents\WindowsPowerShell\Modules`).

A modul a bérlők és az előfizetés elérését együttműködve token, azt javasoljuk, hogy együtt használja a [ARMClient](https://github.com/projectkudu/ARMClient) parancssori eszközt.  Ez [blogbejegyzés](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ARMClient ismerteti részletesen.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>A logic app-sablon létrehozása a PowerShell használatával
PowerShell telepítése után létrehozhat egy sablont a következő paranccsal:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` az Azure-előfizetés-azonosító. Először lekérdezi egy hozzáférési jogkivonat segítségével ARMClient, majd kiszolgálókészletéhez keresztül, a PowerShell-parancsfájlba, és létrehozza a sablon egy JSON-fájlban.

## <a name="add-parameters-to-a-logic-app-template"></a>Paraméterek hozzáadása a logic app-sablon
A logic app-sablon létrehozása után továbbra is fel vagy módosít a paramétereket, amelyek hasznosak lehetnek. Például ha a definíciója tartalmaz egy erőforrás-azonosító egy Azure függvényt vagy beágyazott munkafolyamatot, amely egy központi telepítésben telepítését tervezi, hozzáadhat további erőforrások a sablonhoz és parametrizálja azonosítók, igény szerint. Ugyanez érvényes egyéni API-k és Swagger mutató hivatkozásokat az egyes erőforráscsoportokban telepítendő várt végpontok.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Hivatkozások a tőle függő erőforrások hozzáadása a Visual Studio központi telepítési sablonok

Ha azt szeretné, hogy a logikai alkalmazás függő erőforrásokra kell hivatkoznia, [Azure Resource Manager sablonfüggvényei](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) logic app központi telepítési sablonba. Például érdemes hivatkozhasson rá az Azure-funkció vagy integrációs fiókkal, amely mellett a Logic Apps alkalmazást telepíteni szeretné a Logic Apps alkalmazást. Kövesse az alábbi irányelveket a központi telepítési sablont a paraméterek használatáról, úgy, hogy a Logic App Designer Renderelés megfelelően. 

Eseményindítók és műveletek az ilyen típusú logic app paramétereket használhatja:

*   Alárendelt munkafolyamat
*   Függvényalkalmazás
*   APIM hívás
*   API kapcsolat futásidejű URL-címe
*   API-kapcsolati útvonal

És használhatja a sablont funkciókat, például a paraméterek, változókat, resourceId, concat, stb. Például ez hogyan lecserélheti az Azure-függvény erőforrás-azonosító:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

És ahol paraméterek szeretné használni:

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
Másik példaként a Service Bus küldési üzenet művelet is parametrizálja:

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
> A Logic App Designer működéséhez paraméterek használatakor meg kell adni alapértelmezett értéket, például:
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

Ha egy meglévő erőforráscsoport-projekt, adhat hozzá a logikai alkalmazás erre a projektre a JSON-vázlat ablak. Egy másik logikai alkalmazást a korábban létrehozott alkalmazás mellett azt is megteheti.

1. Nyissa meg az `<template>.json` fájlt.

2. A JSON-vázlat ablak megnyitásához lépjen **nézet** > **más Windows** > **JSON-vázlat**.

3. A sablonfájl egy erőforrás hozzáadásához kattintson **erőforrás hozzáadása** a JSON-vázlat ablak tetején. A JSON-vázlat ablak, kattintson a jobb gombbal vagy **erőforrások**, és válassza ki **új erőforrás hozzáadása**.

    ![JSON-vázlat ablak](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. Az a **erőforrás hozzáadása** párbeszédpanel, keresése és kijelölése **logikai alkalmazás**. A logikai alkalmazás neve, és válassza a **Hozzáadás**.

    ![Erőforrás felvétele](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>A logic app sablon üzembe helyezése

A sablon olyan eszközöket, például a PowerShell, a REST API segítségével telepíthet [Visual Studio Team Services Kiadáskezelés](#team-services), és a sablon-üzembehelyezés az Azure portálon keresztül.
Is, a paraméterek értékeit tárolására, azt javasoljuk, hogy hozzon létre egy [paraméterfájl](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Megtudhatja, hogyan [erőforrások az Azure Resource Manager-sablonok és a PowerShell telepítése](../azure-resource-manager/resource-group-template-deploy.md) vagy [erőforrások az Azure Resource Manager-sablonok és az Azure-portál telepítése](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>OAuth-kapcsolatok engedélyezése

A központi telepítést követően a logic app-végpontok közötti érvényes paraméterekkel működik.
Azonban továbbra is engedélyeznie kell egy érvényes jogkivonat létrehozásához OAuth-kapcsolatokat.
OAuth-kapcsolatok engedélyezésére, nyissa meg a logikai alkalmazást a Logic Apps-tervezőben, és ezek a kapcsolatok engedélyezéséhez. Vagy az automatikus üzembe helyezés esetén az parancsfájl segítségével járul hozzá az egyes OAuth-kapcsolatok.
Nincs a githubon példa parancsfájl a [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projekt.

<a name="team-services"></a>
## <a name="visual-studio-team-services-release-management"></a>A Visual Studio Team Services Kiadáskezelés

Egy általános forgatókönyv központi telepítésére és felügyeletére egy olyan környezetben, hogy egy eszköz, például a Kiadáskezelés logic app a központi telepítési sablont a Visual Studio Team Services használata. A Visual Studio Team Services tartalmaz egy [telepítése Azure erőforráscsoport](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) , hogy minden build hozzáadhatja vagy csővezeték kiadási tevékenység. Telepíteni kell egy [egyszerű](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) az engedélyezési központi telepítéséhez, és ezt a kiadási definition hozhat létre.

1. A Kiadáskezelés, válassza ki **üres** , hogy egy üres definíció létrehozása.

    ![Üres definíció létrehozása][1]

2. Válassza ki a van szüksége, nagy valószínűséggel többek között a logic app-sablon manuálisan, vagy az összeállítási folyamat részeként létrehozott erőforrásokat.
3. Adja hozzá egy **Azure erőforrás-csoport központi telepítésének** feladat.
4. Állítson be egy [egyszerű](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), és a sablon és a sablon paramétereinek fájlok hivatkozik.
5. Maradnak meg a környezetben, automatikus teszt vagy igény szerint jóváhagyó kiadás folyamat lépéseit.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
