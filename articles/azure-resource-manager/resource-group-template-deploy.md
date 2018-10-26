---
title: A PowerShell és a sablon erőforrások üzembe helyezése |} A Microsoft Docs
description: Azure Resource Manager és az Azure PowerShell használatával helyezze üzembe az Azure-erőforrásokat. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2018
ms.author: tomfitz
ms.openlocfilehash: 083a318f008799713f4d8d9aeacfe2e27f6ad195
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085934"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel

Ez a cikk ismerteti az Azure PowerShell használata a Resource Manager-sablonok, az erőforrások üzembe helyezése az Azure. Ha nem ismeri a fogalmait, üzembe helyezése és kezelése az Azure-megoldások, lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).

A Resource Manager-sablon telepít egy helyi fájlt a gépén vagy egy külső amilyen a GitHub-tárházban található fájl lehet. A sablon üzembe helyezi az ebben a cikkben érhető el, [GitHub storage-fiók sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

Ha szükséges, telepítse az Azure PowerShell-modult az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd futtassa a `Connect-AzureRmAccount` parancsot az Azure-hoz való csatlakozáshoz.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>A helyi gépen-sablon üzembe helyezése

Erőforrások üzembe helyezésekor az Azure-ba, hogy:

1. Jelentkezzen be az Azure-fiókjába
2. Hozzon létre egy erőforráscsoportot, amely a telepített erőforrások tárolójaként szolgál. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójelet tartalmazhat. Legfeljebb 90 karakter lehet. Nem végződhet ponttal.
3. Az erőforráscsoport üzembe a sablont, amely meghatározza az erőforrások létrehozása

A sablon paramétereit, amelyek lehetővé teszik az üzembe helyezés testreszabása tartalmazhat. Például megadhat értékeket, amelyek egy adott környezetben (például fejlesztési, tesztelési és éles környezetben). A mintasablon a tárfiók SKU paraméter határozza meg.

Az alábbi példa létrehoz egy erőforráscsoportot, és üzembe helyezi a sablont a helyi gépen:

```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Az üzembe helyezés eltarthat néhány percig. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Egy külső forrásból-sablon üzembe helyezése

Resource Manager-sablonok tárolása a helyi gépén, helyett érdemesebb lehet külső helyen tárolja őket. Sablonok verziókövetési adattár (például a GitHub) tárolhatja. Vagy tárolhatja őket az Azure storage-fiók, a közös hozzáférésű a szervezetben.

Egy külső sablon üzembe helyezéséhez használja a **TemplateUri** paraméter. A példában az URI használatával helyezhet üzembe a mintául szolgáló sablont a Githubból.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

Az előző példában a sablon, amely a legtöbb esetben működik, mivel a sablon ne tartalmazza a bizalmas adatokat egy nyilvánosan elérhető-e URI-t igényel. Adja meg a bizalmas adatok (például egy rendszergazdai jelszót) van szüksége, ha biztonságos paraméterként adja át ezt az értéket. Azonban ha nem szeretné a sablon nyilvánosan hozzáférhető, segítségével megvédheti azokat a személyes tárolót tárolja őket. Egy közös hozzáférésű jogosultságkód (SAS) igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

A Cloud shellben használja az alábbi parancsokat:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Több erőforráscsoport vagy előfizetés üzembe helyezése

Általában végzi az üzembe helyezést összes erőforrást a sablonhoz, amelyekkel egyetlen erőforráscsoportra. Vannak azonban forgatókönyvek, ahol szeretné erőforráscsoport telepítsen együtt, de különböző erőforráscsoport vagy előfizetés helyezze el őket. Telepíthet egy központi telepítésben csak öt erőforráscsoportokhoz. További információkért lásd: [üzembe helyezése Azure-erőforrásokat az egynél több előfizetésen vagy erőforráscsoporton](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameters"></a>Paraméterek

Paraméterértékek átadni, vagy a beágyazott paraméterek, vagy egy paraméterfájl használható. Az előző példák ebben a cikkben a beágyazott paraméterek megjelenítése.

### <a name="inline-parameters"></a>A beágyazott paraméterek

A beágyazott paraméterek átadni, adja meg a paraméter nevét a `New-AzureRmResourceGroupDeployment` parancsot. Ha például egy karakterláncot és egy tömb átadása egy sablont, használja:

```powershell
$arrayParam = "value1", "value2"
New-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Fájl tartalmának beolvasása, és adja meg a beágyazott paraméterként tartalom is.

```powershell
$arrayParam = "value1", "value2"
New-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

A paraméter értékének lekérése egy fájl akkor hasznos, ha meg kell adnia a konfigurációs értékeket. Megadhat például [Linux rendszerű virtuális gép értékeit a cloud-init](../virtual-machines/linux/using-cloud-init.md).

### <a name="parameter-files"></a>Alkalmazásparaméter-fájlok

Ahelyett, hogy a paraméterek átadása a parancsfájlban beágyazott értékekként, akkor előfordulhat, hogy egyszerűbb paraméter értékét tartalmazó JSON-fájl használata. A paraméterfájl lehet egy helyi fájlból vagy egy külső fájl egy elérhető URI-t.

A paraméterfájl a következő formátumban kell lennie:

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

Figyelje meg, hogy a paraméterek szakasz tartalmazza-e a paraméter neve, amely megegyezik a paraméter definiálva a sablonban (Tárfióktípus). A paraméterfájl a paraméter értékét tartalmazza. Ezt az értéket automatikusan kerülnek a sablon üzembe helyezése során. Hozzon létre több alkalmazásparaméter-fájlt, és akkor továbbítja a forgatókönyvnek megfelelő paraméterfájlban. 

Másolja ki az előző példában, és mentse a fájlt `storage.parameters.json`.

Adja át a helyi alkalmazásparaméter-fájlt, használja a **TemplateParameterFile** paramétert:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Átadni egy külső alkalmazásparaméter-fájlt, használja a **TemplateParameterUri** paramétert:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>A paraméter sorrend

A beágyazott paraméterek és a egy helyi paraméterfájl azonos központi telepítési művelet is használhatja. Például néhány értéket adja meg a helyi alkalmazásparaméter-fájlt, és adja hozzá az egyéb értékek beágyazott üzembe helyezés során. Ha a paraméter a helyi alkalmazásparaméter-fájlt és a beágyazott értékeket ad meg, a beágyazott elsőbbséget.

Azonban ha egy külső alkalmazásparaméter-fájlt használ, nem adhatók át további értékek vagy beágyazott vagy egy helyi fájlból. A paraméterfájl megadása a **TemplateParameterUri** paraméter, minden beágyazott paraméterek a rendszer figyelmen kívül hagyja. Adja meg a külső fájl összes paraméter értéke. Ha a sablont, amely nem adhat meg a paraméterfájlt kényes értéket tartalmaz, adja hozzá a key vault ezt az értéket, vagy dinamikusan adjon meg minden paramétert értékek beágyazott.

### <a name="parameter-name-conflicts"></a>A paraméter neve ütközik

A sablon tartalmazza a PowerShell-parancs olyan paraméterre, amelynek neve megegyezik a paraméterek egyikét, ha PowerShell be az a paraméter a sablonból a utótag **FromTemplate**. Például nevű paraméter **ResourceGroupName** a sablon ütközik a **ResourceGroupName** paramétert a [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) a parancsmag. Adjon meg egy értéket a kéri **ResourceGroupNameFromTemplate**. Általában ez zavart ne által nem elnevezési paraméterek az üzembe helyezési műveleteihez használt paraméterek azonos néven.

## <a name="test-a-template-deployment"></a>Egy sablon üzemelő példány tesztelése

A sablonnal és paraméterfájlokkal értékek teszteléséhez ténylegesen az olyan erőforrások telepítés nélkül használhatja a [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Ha nincsenek hibák észlelhetők, a parancs befejeződik, a válaszra. Ha hibát észlel, a parancs hibaüzenetet ad vissza. Például a tárfiók SKU, helytelen értéket átadja adja vissza a következő hibával:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Ha a sablon szintaktikai hibát tartalmaz, a parancs visszaadja egy hibaüzenet, nem lehetett elemezni a sablont. Az üzenet azt jelzi, hogy a sor száma és az elemzési hiba pozícióját.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>További lépések
* Ebben a cikkben szereplő példák erőforrások üzembe helyezése az erőforráscsoporthoz, az alapértelmezett előfizetést. Használjon másik előfizetést, lásd: [több Azure-előfizetések kezelése](/powershell/azure/manage-subscriptions-azureps).
* Adja meg, hogyan legyen kezelve az erőforrásokat, az erőforráscsoportban létezik, de nincsenek definiálva a sablonban, lásd: [Azure Resource Manager üzembe helyezési mód](deployment-modes.md).
* A sablonban szereplő paraméterekkel definiálása ismertetése: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
* Gyakori üzembehelyezési hibák elhárítása a tippek: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* SAS-token igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-powershell-sas-token.md).
* Biztonságosan terjed ki a szolgáltatás több régióban, lásd: [Azure Deployment Manager](deployment-manager-overview.md).
