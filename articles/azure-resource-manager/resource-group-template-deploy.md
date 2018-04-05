---
title: Erőforrások az PowerShell és a sablon telepítése |} Microsoft Docs
description: Azure Resource Manager és az Azure PowerShell használatával telepítse a erőforrások az Azure. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: tomfitz
ms.openlocfilehash: eb4ebe0b1c0e4799aea6401b068d881e5aa47026
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel

Ez a cikk ismerteti az Azure PowerShell használata a Resource Manager-sablonok az erőforrások telepítése az Azure-bA. Ha nem ismeri a telepítésével kapcsolatos alapfogalmakat és kezelése az Azure megoldások, lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).

A Resource Manager-sablon, azok a helyi fájl a számítógépre telepít, vagy egy külső egy például a GitHub-tárházban található fájl. Ez a cikk központi telepítését a sablon érhető el a [mintasablon](#sample-template) szakasz, vagy mint [tárolási fiók sablon a Githubon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

Szükség esetén telepítse az Azure PowerShell-modult a található utasításokat követve a [Azure PowerShell útmutató](/powershell/azure/overview), majd futtassa `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>A helyi gépről sablon telepítése

Ha erőforrásokat üzembe helyezi az Azure-ba, hogy:

1. Jelentkezzen be az Azure-fiókjába.
2. Hozzon létre egy erőforráscsoportot, amely a telepített erőforrások tárolójaként szolgál. Az erőforráscsoport neve csak tartalmazhatnak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter lehet. Nem végződhet ponttal.
3. Telepítse az erőforráscsoport a sablon, amely meghatározza az erőforrás létrehozásához.

A sablon tartalmazhat, amelyek segítségével testre szabhatja a központi telepítési paramétereit. Biztosíthatja például is lefednek értékeket (például a fejlesztői, tesztelési és éles) egy adott környezetben. A minta sablon meghatározza a tárfiók SKU paraméter.

Az alábbi példa létrehoz egy erőforráscsoport, és egy sablon, a helyi számítógépen telepíti:

```powershell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. A Befejezés után megjelenik egy üzenet, amely tartalmazza az eredmény:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Külső forrásból sablon telepítése

Helyett Resource Manager-sablonok a helyi gépén, célszerű lehet külső helyen tárolja őket. A verziókövetési tárházat (például a Githubon) sablonok tárolhat. Vagy tárolhatja őket egy Azure storage-fiók megosztott eléréséhez a szervezetében.

Egy külső sablon történő üzembe helyezéséhez használjon a **TemplateUri** paraméter. A példában az URI segítségével telepítheti a minta-sablont a Githubból.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

Az előző példában a sablont, amely a legtöbb környezetben működik, mivel a sablon nem tartalmaznia kell a bizalmas adatok nyilvánosan elérhető URI igényel. Meg kell adnia a bizalmas adatok (például egy rendszergazdai jelszó), ha egy biztonságos paraméterben adja át ezt az értéket. Azonban ha nem szeretné, hogy a sablon a nyilvánosan hozzáférhető, megvédheti azokat a személyes tárolót tárolja őket. A sablont, amely közös hozzáférésű jogosultságkód (SAS) jogkivonat szükséges, központi telepítésével kapcsolatos információkért lásd: [telepítés titkos sablont a SAS-jogkivonat](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

A felhő rendszerhéj az alábbi parancsokat használja:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Egynél több erőforráscsoportba vagy előfizetésbe történő központi telepítése

Általában központi telepítését az erőforrásokat a sablonhoz, amelyekkel egyetlen erőforráscsoportként működnek. Vannak azonban forgatókönyvek, ahol szeretne erőforráscsoport telepítsen együtt, de másik erőforráscsoport-sablonok és előfizetések helyezze el őket. Egy központi telepítésnél csak öt erőforráscsoportok telepítene. További információkért lásd: [telepítése Azure erőforrások több mint egy előfizetéshez vagy erőforráscsoporthoz](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameter-files"></a>A paraméter fájlok

Ahelyett, hogy a parancsfájl beágyazott értékeiként paraméterek átadása, előfordulhat, hogy ez egyszerűbbé teszi a paraméterek értékeit tartalmazó JSON-fájl használatára. A paraméterfájl a következő formátumúnak kell lennie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Figyelje meg, hogy a Paraméterek szakaszban tartalmazza-e a paraméter neve, amely megfelel a sablonban (storageAccountType) meghatározott paraméter. A paraméterfájl a paraméter értékét tartalmazza. Ezt az értéket automatikusan kerülnek a sablon üzembe helyezése során. Hozzon létre különböző telepítési forgatókönyvek esetén több paraméter fájlt, és akkor továbbítja a megfelelő paraméter fájlban. 

Másolja át az előző példában, és mentse a fájlt `storage.parameters.json`.

A helyi paraméterfájl továbbítani, használja a **TemplateParameterFile** paraméter:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Egy külső paraméterfájl továbbítani, használja a **TemplateParameterUri** paraméter:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

Beágyazott paraméterek és a helyi paraméterfájl azonos központi telepítési művelet használható. Például a helyi paraméterfájl adja meg az egyes értékeket, és adja hozzá a más értékek beágyazott üzembe helyezése során. Ha az egyik paraméter a helyi paraméterfájl és a beágyazott értékeket ad meg, a beágyazott elsőbbséget.

Azonban egy külső paraméterfájl használata esetén nem adható át más értékek vagy szövegközi vagy egy helyi fájlból. A paraméterfájl a megadása a **TemplateParameterUri** paraméter, minden beágyazott paraméterek figyelmen kívül lesznek hagyva. Adja meg az összes paraméter a külső fájlban. Ha a sablont, amely nem adhat meg a paraméter fájl-és nagybetűket értéket tartalmaz, adja hozzá ezt az értéket a kulcstároló, vagy dinamikusan adjon meg az összes paraméter értékek beágyazott.

Ha a sablon szerepel a PowerShell-parancs olyan paraméterre, amelynek a neve megegyezik a paraméterek egyikét, PowerShell eltéréseit a paraméter a sablonból a utótag **FromTemplate**. Például nevű paraméter **ResourceGroupName** a sablon ütközik a **ResourceGroupName** paramétere a [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) parancsmag. Adjon meg egy értéket a rendszer kéri **ResourceGroupNameFromTemplate**. Ez zavart ne általában a nem paraméterek telepítési műveleteihez használt paraméterek megegyező névvel.

## <a name="test-a-template-deployment"></a>A sablon üzemelő példány tesztelése

Minden olyan erőforrásnál tényleges telepítése nélkül a sablonnal és paraméterfájlokkal értékek teszteléséhez [teszt-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Ha nincsenek hibák, a parancs a válaszra befejeződik. Ha a rendszer hibát észlel, a parancs hibaüzenetet ad vissza. Például a tárfiók SKU, helytelen értéket átadni próbált a következő hibaüzenet:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Ha a sablon szintaktikai hibát tartalmaz, a parancs nem tudta elemezni a sablon jelző hiba adja vissza. Az üzenet azt jelzi, a sor számának megjelenítése és elhelyezése az elemzési hiba.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Teljes módot használja, használja a `Mode` paraméter:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="sample-template"></a>Példasablon

Ebben a cikkben szereplő példák a következő sablon használható. Másolja ki és mentse azt egy storage.json nevű fájlba. Ez a sablon létrehozása ismertetése: [az első Azure Resource Manager-sablon létrehozása](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>További lépések
* Ebben a cikkben szereplő példák erőforrások telepítése az alapértelmezett előfizetésében az erőforráscsoporthoz. Használjon másik előfizetést, lásd: [több Azure-előfizetések kezeléséhez](/powershell/azure/manage-subscriptions-azureps).
* Egy teljes parancsfájlt, amely telepít egy sablon, lásd: [Resource Manager sablon üzembe helyezési parancsfájl](resource-manager-samples-powershell-deploy.md).
* Szeretné megtudni, hogyan adhat meg a paramétereket a sablonban, lásd: [megérteni a felépítését és Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Tippek az általános telepítési hibák feloldására, lásd: [hibaelhárítás általános az Azure-telepítés az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* A sablont, amely a SAS-jogkivonat szükséges, központi telepítésével kapcsolatos információkért lásd: [telepítés titkos sablont a SAS-jogkivonat](resource-manager-powershell-sas-token.md).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.

