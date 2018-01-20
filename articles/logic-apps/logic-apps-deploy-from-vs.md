---
title: "Hozzon létre, elkészítéséhez és központi telepítése a Visual Studio - Azure Logic Apps a logic apps |} Microsoft Docs"
description: "Visual Studio-projektek, tervezése, elkészítéséhez és központi telepítése az Azure Logic Apps létrehozása"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 614e07ad9fcbe9ad3684ed977f7ffa36e727dddc
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>Tervezési, elkészítéséhez és központi telepítése az Azure Logic Apps a Visual Studióban

Bár a [Azure-portálon](https://portal.azure.com/) létrehozása és kezelése az Azure Logic Apps remek mód kínál, tervezési, kiépítési és a logic Apps alkalmazások telepítése a Visual Studio is használhatja. A Visual Studio hasonlóan ahhoz, hogy a logic Apps alkalmazások létrehozása, telepítése és az automatizálás sablonok konfigurálása és telepítése bármilyen környezethez a Logic App Designer hatékony eszközöket biztosít. 

Ismerkedés az Azure Logic Apps, ismerje meg [az első logikai alkalmazás létrehozása az Azure portálon](quickstart-create-first-logic-app-workflow.md).

## <a name="installation-steps"></a>Telepítés lépései

Telepítse és konfigurálja a Visual Studio eszközök az Azure Logic Apps, kövesse az alábbi lépéseket.

### <a name="prerequisites"></a>Előfeltételek

* [A Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) vagy a Visual Studio 2015-höz
* [Legfrissebb Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 vagy újabb)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Ha a beágyazott designer segítségével Internet-hozzáféréssel

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Telepítheti a Visual Studio tools for Azure Logic Apps

Miután telepítette az Előfeltételek:

1. Nyissa meg a Visual Studiót. Az a **eszközök** menü **bővítmények és frissítések**.
2. Bontsa ki a **Online** kategória online kereséséhez.
3. Keresse meg **Logic Apps** amíg meg nem látja **Azure Logic Apps Tools for Visual Studio**.
4. Töltse le és telepítse a bővítmény kattintson **letöltése**.
5. Indítsa újra a Visual Studio telepítése után.

> [!NOTE]
> Emellett letöltheti [Azure Logic Apps-eszközök Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) és a [Azure Logic Apps eszközök a Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio) közvetlenül a Visual Studio piactérről.

A telepítés befejezése után a Logic App-tervezővel is használhatja az Azure erőforráscsoport-projekt.

## <a name="create-your-project"></a>A projekt létrehozása

1. Az a **fájl** menüben keresse fel **új**, és válassza ki **projekt**. Vagy egy meglévő megoldás projektjéhez adásához **Hozzáadás**, és válassza ki **új projekt**.

    ![Fájl menü](./media/logic-apps-deploy-from-vs/filemenu.png)

2. Az a **új projekt** ablakban található **felhő**, és válassza ki **Azure erőforráscsoport**. Nevezze el a projektet, és kattintson a **OK**.

    ![Új projekt hozzáadása](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. Válassza ki a **logikai alkalmazás** sablont, amely létrehoz egy üres logikai alkalmazás központi telepítési sablont használatára. A sablon megadása után kattintson az **OK**.

    ![Válassza ki a Logic App-sablon](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    Most már hozzáadott a logic app projektet a megoldásban. 
    A Megoldáskezelőben a telepítési fájlt meg kell jelennie.

    ![Telepítési fájl](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>A Logic App tervezővel a logikai alkalmazás létrehozása

Ha egy Azure erőforráscsoport-projekt, amely egy logikai alkalmazást tartalmaz, a Logic App Designer nyithatja meg a Visual Studio, a munkafolyamat létrehozásához. 

> [!NOTE]
> A Tervező lekérdezés összekötőkre internetkapcsolat szükséges a rendelkezésre álló tulajdonságok és az adatok. Például ha a Dynamics CRM Online-összekötő használata esetén a Tervező lekérdezi a CRM példányát a rendelkezésre álló egyéni és az alapértelmezett tulajdonságok megjelenítése.

1. Kattintson a jobb gombbal a `<template>.json` fájlt, és válassza ki **nyissa meg a Logic App tervezővel**. (`Ctrl+L`)

2. Válassza ki az Azure-előfizetés, erőforráscsoportot és helyet a központi telepítési sablon.

    > [!NOTE]
    > Logikai alkalmazás kialakítása API-kapcsolat erőforrások azokhoz a tulajdonságokhoz lekérdezés során létrehoz Tervező. A Visual Studio a kiválasztott erőforráscsoporthoz alapján hozza létre ezeket a kapcsolatokat tervezés során. Megtekintéséhez, vagy módosítsa a API-kapcsolatokat, nyissa meg az Azure portálra, és tallózással keresse meg **API kapcsolatok**.

    ![Előfizetés kiválasztása](./media/logic-apps-deploy-from-vs/designer_picker.png)

    A designer használja a definíció a `<template>.json` fájl a megjelenítéshez.

4. Hozzon létre, és a logikai alkalmazás kialakítása. A módosításokat a központi telepítési sablont frissül.

    ![A Visual Studio programot Tervező](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

A Visual Studio hozzáadja `Microsoft.Web/connections` a kapcsolatokat erőforrásfájljából erőforrásokat a Logic Apps alkalmazást kell működni. A kapcsolati tulajdonságok kell telepítésekor, és állítsa a telepítése után felügyelt **API kapcsolatok** az Azure portálon.

### <a name="switch-to-json-code-view"></a>JSON-kód nézetre váltáshoz

A logikai alkalmazásnak a JSON-megjelenítés megjelenítéséhez jelölje ki a **kódnézetben** lapon a designer alján.

Váltson vissza a teljes körű erőforrást JSON, kattintson a jobb gombbal a `<template>.json` fájlt, és válassza ki **nyitott**.

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

### <a name="save-your-logic-app"></a>A logikai alkalmazás mentése

A Logic Apps alkalmazást bármikor mentéséhez navigáljon **fájl** > **mentése**. (`Ctrl+S`) 

Ha a Logic Apps alkalmazást hibák az alkalmazás mentésekor, azok megjelennek a Visual Studio **kimenetek** ablak.

## <a name="deploy-your-logic-app-from-visual-studio"></a>A Logic Apps alkalmazást a Visual Studio telepítése

Az alkalmazás konfigurálását, telepítheti közvetlenül a Visual Studio csak néhány lépésben. 

1. A Megoldáskezelőben kattintson jobb gombbal a projektre, és navigáljon a **telepítés** > **új központi telepítési...**

    ![Új központi telepítés](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. Amikor a rendszer kéri, jelentkezzen be az Azure-előfizetéshez. 

3. Most már ki kell választania az erőforráscsoportot, ahol a Logic Apps alkalmazást telepíteni szeretné a részletes. Amikor elkészült, válassza ki a **telepítés**.

    > [!NOTE]
    > Győződjön meg arról, hogy bejelöli-e a megfelelő sablon és a paraméterek fájlt ahhoz az erőforráscsoporthoz. Ha szeretné telepíteni az éles környezetben való használatra, például éles paraméterek fájl kiválasztásához.

    ![Erőforráscsoport telepítése](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    A telepítés állapota megjelenik a **kimeneti** ablak. 
    Ki kell választania **Azure kiépítés** a a **megjelenítése kimenetét** listája.

    ![Központi telepítési állapot kimeneti](./media/logic-apps-deploy-from-vs/output.png)

A jövőben a Logic Apps alkalmazást az adatforrás-vezérlő szerkesztése, és új verziók helyezhetők üzembe a Visual Studio használatával.

> [!NOTE]
> Az Azure portálon definition közvetlenül módosítja, ezeket a módosításokat a rendszer felülírja központi telepítésekor a Visual Studio eszközből legközelebb. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>A logikai alkalmazás hozzáadása egy meglévő erőforráscsoport-projekt

Ha egy meglévő erőforráscsoport-projekt, adhat hozzá a logikai alkalmazás erre a projektre a JSON-vázlat ablak. Egy másik logikai alkalmazást a korábban létrehozott alkalmazás mellett azt is megteheti.

1. Nyissa meg az `<template>.json` fájlt.

2. A JSON-vázlat ablak megnyitásához lépjen **nézet** > **más Windows** > **JSON-vázlat**.

3. A sablonfájl egy erőforrás hozzáadásához kattintson **erőforrás hozzáadása** a JSON-vázlat ablak tetején. A JSON-vázlat ablak, kattintson a jobb gombbal vagy **erőforrások**, és válassza ki **új erőforrás hozzáadása**.

    ![JSON-vázlat ablak](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. Az a **erőforrás hozzáadása** párbeszédpanel, keresése és kijelölése **logikai alkalmazás**. A logikai alkalmazás neve, és válassza a **Hozzáadás**.

    ![Erőforrás felvétele](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>További lépések

* [A Visual Studio Cloud Explorer logic Apps-alkalmazások kezelése](logic-apps-manage-from-vs.md)
* [Gyakori példák és felhasználási helyzetek megtekintése](logic-apps-examples-and-scenarios.md)
* [Az Azure Logic Apps üzleti folyamatok automatizálása](http://channel9.msdn.com/Events/Build/2016/T694)
* [Útmutató: az Azure Logic Apps rendszerintegráció](http://channel9.msdn.com/Events/Build/2016/P462)
