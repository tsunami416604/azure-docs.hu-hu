---
title: Logikai alkalmazássablonok létrehozása központi telepítéshez
description: Megtudhatja, hogy miként hozhat létre Azure Resource Manager-sablonokat az Azure Logic Apps-alkalmazásokban való üzembe helyezés automatizálásához
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972634"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Azure Resource Manager-sablonok létrehozása az Azure Logic Apps üzembe helyezésének automatizálásához

A logikai alkalmazás létrehozásának és üzembe helyezésének automatizálása érdekében ez a cikk ismerteti, hogyan hozhat létre [egy Azure Resource Manager-sablont](../azure-resource-manager/management/overview.md) a logikai alkalmazáshoz. A munkafolyamat-definíciót és a telepítéshez szükséges egyéb erőforrásokat tartalmazó sablonok szerkezetének és szintaxisának áttekintését [az Áttekintés: A logikai alkalmazások üzembe helyezésének automatizálása az Azure Resource Manager-sablonokkal című](logic-apps-azure-resource-manager-templates-overview.md)témakörben találja.

Az Azure Logic Apps egy [előre összeállított logikai alkalmazást biztosít az Azure Resource Manager-sablonhoz,](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) amelyet nem csak logikai alkalmazások létrehozásához használhat fel, hanem a központi telepítéshez használandó erőforrások és paraméterek meghatározásához is. Ezt a sablont használhatja saját üzleti forgatókönyveihez, vagy testreszabhatja a sablont, hogy megfeleljen az igényeinek.

> [!IMPORTANT]
> Győződjön meg arról, hogy a sablonban lévő kapcsolatok ugyanazt az Azure-erőforráscsoportot és -helyet használják, mint a logikai alkalmazás.

Az Azure Resource Manager-sablonokról az alábbi témakörökben olvashat bővebben:

* [Az Azure Resource Manager sablonszerkezete és szintaxisa](../azure-resource-manager/templates/template-syntax.md)
* [Az Azure Resource Manager sablonjainak szerzője](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager-sablonok fejlesztése felhőkonzisztenciához](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Sablonok létrehozása a Visual Studio segítségével

A legegyszerűbb módja annak, hogy érvényes paraméteres logikai alkalmazássablonokat hozzon létre, amelyek többnyire készen állnak a telepítésre, használja a Visual Studio (ingyenes közösségi kiadás vagy újabb) és az Azure Logic Apps Tools for Visual Studio. Ezután [létrehozhatja a logikai alkalmazást a Visual Studióban,](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) vagy [megkeresheti és letöltheti a meglévő logikai alkalmazást az Azure Portalról a Visual Studióba.](../logic-apps/manage-logic-apps-with-visual-studio.md)

A logikai alkalmazás letöltésével kap egy sablont, amely tartalmazza a logikai alkalmazás és más erőforrások, például a kapcsolatok definícióit. A sablon *paraméterezi*a , vagy meghatározza a logikai alkalmazás és más erőforrások üzembe helyezéséhez használt értékek paraméterezi, vagy paramétereket határoz meg. Ezeknek a paramétereknek az értékeit külön paraméterfájlban adja meg. Így könnyebben módosíthatja ezeket az értékeket a központi telepítési igények alapján. További információt az alábbi témakörökben talál:

* [Logikai alkalmazások létrehozása a Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Logikai alkalmazások kezelése a Visual Studióval](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Sablonok létrehozása az Azure PowerShell használatával

Erőforrás-kezelő sablonokat hozhat létre az Azure PowerShell használatával a [LogicAppTemplate modullal.](https://github.com/jeffhollan/LogicAppTemplateCreator) Ez a nyílt forráskódú modul először kiértékeli a logikai alkalmazást és a logikai alkalmazás által használt kapcsolatokat. A modul ezután sablonerőforrásokat hoz létre a telepítéshez szükséges paraméterekkel.

Tegyük fel például, hogy rendelkezik egy logikai alkalmazás, amely üzenetet kap egy Azure Service Bus-várólistából, és adatokat tölt fel egy Azure SQL-adatbázisba. A modul megőrzi az összes vezénylési logikát, és paraméterezi az SQL és a Service Bus kapcsolati karakterláncokat, így a telepítési igények alapján biztosíthatja és módosíthatja ezeket az értékeket.

Ezek a minták bemutatják, hogyan hozhat létre és helyezhet üzembe logikai alkalmazásokat az Azure Resource Manager-sablonok, az Azure DevOps Azure DevOps-beli Azure-folyamatok és az Azure PowerShell használatával:

* [Példa: Csatlakozás az Azure Service Bus várólistáihoz az Azure Logic Apps alkalmazásból](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Példa: Csatlakozás Azure Storage-fiókokhoz az Azure Logic Apps alkalmazásból](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Példa: Függvényalkalmazás-művelet beállítása az Azure Logic Apps alkalmazáshoz](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Csatlakozás integrációs fiókhoz az Azure Logic Apps alkalmazásból](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>PowerShell-modulok telepítése

1. Ha még nem tette meg, telepítse az [Azure PowerShellt.](https://docs.microsoft.com/powershell/azure/install-az-ps)

1. A LogicAppTemplate modul [PowerShell-galériából](https://www.powershellgallery.com/packages/LogicAppTemplate)történő telepítésének legegyszerűbb módja a következő parancs futtatásához:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   A legújabb verzióra való frissítéshez futtassa a következő parancsot:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Manuális telepítéshez kövesse a GitHub for [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator)című témakör lépéseit.

### <a name="install-azure-resource-manager-client"></a>Az Azure Resource Manager-ügyfél telepítése

A LogicAppTemplate modul bármely Azure-bérlői és előfizetés-hozzáférési jogkivonattal való együttműködésre telepítse az [Azure Resource Manager ügyféleszközt,](https://github.com/projectkudu/ARMClient)amely egy egyszerű parancssori eszköz, amely meghívja az Azure Resource Manager API-t.

Amikor ezzel `Get-LogicAppTemplate` az eszközzel futtatja a parancsot, a parancs először kap egy hozzáférési jogkivonatot az ARMClient eszközön keresztül, lecsövezi a jogkivonatot a PowerShell-parancsfájlba, és a sablont JSON-fájlként hozza létre. Az eszközről az Azure [Resource Manager ügyféleszközről szóló cikkben olvashat bővebben.](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)

### <a name="generate-template-with-powershell"></a>Sablon létrehozása a PowerShell használatával

A sablon létrehozásához a LogicAppTemplate modul és az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)telepítése után futtassa ezt a PowerShell-parancsot:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Az [Azure Resource Manager ügyféleszközből](https://github.com/projectkudu/ARMClient)származó jogkivonatban történő pipálásra vonatkozó javaslat követéséhez futtassa ezt a parancsot, hanem azt, hogy hol `$SubscriptionId` található az Azure-előfizetésazonosítója:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

A kivonás után a következő paranccsal hozhat létre paraméterfájlt a sablonból:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

Az Azure Key Vault-hivatkozásokkal való kinyeréshez (csak statikus) futtassa ezt a parancsot:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Paraméterek | Kötelező | Leírás |
|------------|----------|-------------|
| Sablonfájl | Igen | A sablonfájl elérési útja |
| KeyVault | Nem | Egy felsorítás, amely leírja, hogyan kell kezelni a lehetséges key vault értékeket. A mező alapértelmezett értéke: `None`. |
||||

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logikaialkalmazás-sablonok üzembe helyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
