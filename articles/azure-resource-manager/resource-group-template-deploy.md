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
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 18dc82880830b6f8d14a7fc01930f75e9e61e5b0
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300550"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel

Útmutató az Azure PowerShell használatával a Resource Manager-sablonokkal helyezheti üzembe az erőforrásokat az Azure-bA. Az üzembe helyezése és kezelése az Azure-megoldások kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](resource-group-overview.md).

A sablon üzembe helyezéséhez két lépést általában kell:

1. Hozzon létre egy erőforráscsoportot. A tároló üzembe helyezett erőforrások erőforráscsoport szolgál. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójelet tartalmazhat. Legfeljebb 90 karakter lehet. Nem végződhet ponttal.
2. Sablon üzembe helyezése. A sablon határozza meg az erőforrás létrehozásához.  Az üzemelő példány a megadott erőforráscsoportban létrehozza az erőforrásokat.

Ez a cikk a kétlépéses módszer használatos.  A másik lehetőség, hogy egyszerre egy erőforráscsoportot és az erőforrások üzembe.  További információkért lásd: [hozzon létre és helyezhet üzembe erőforrásokat](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="prerequisites"></a>Előfeltételek

Ha nem használ a [Azure Cloud shell](#deploy-templates-from-azure-cloud-shell) sablonok üzembe helyezése az Azure PowerShell telepítése és csatlakozás az Azure szüksége:
- **Azure PowerShell-parancsmagjainak telepítése a helyi számítógépen.** További információért lásd [az Azure PowerShell használatának első lépéseit](/powershell/azure/get-started-azureps).
- **Csatlakozás az Azure használatával [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount.md)**. Ha több Azure-előfizetéssel rendelkezik, szükség lehet futtatni [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext.md). További információkért lásd: [használata több Azure-előfizetéssel](/powershell/azure/manage-subscriptions-azureps).
- * Töltse le és mentse egy [gyorsindítási sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) . A cikk ezt használja a helyi Fájlnév **c:\MyTemplates\azuredeploy.json**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-templates-stored-locally"></a>Helyileg tárolt sablonok üzembe helyezése

Az alábbi példa létrehoz egy erőforráscsoportot, és üzembe helyezi a sablont a helyi gépen:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Megjegyzés: *c:\MyTemplates\azuredeploy.json* gyorsindítási sablon van.  Lásd: [Előfeltételek](#prerequisites).

Az üzembe helyezés eltarthat néhány percig.

## <a name="deploy-templates-stored-externally"></a>Külsőleg tárolt sablonok üzembe helyezése

Resource Manager-sablonok tárolása a helyi gépén, helyett érdemesebb lehet külső helyen tárolja őket. Sablonok verziókövetési adattár (például a GitHub) tárolhatja. Vagy tárolhatja őket az Azure storage-fiók, a közös hozzáférésű a szervezetben.

Egy külső sablon üzembe helyezéséhez használja a **TemplateUri** paraméter. A példában az URI használatával helyezhet üzembe a mintául szolgáló sablont a Githubból.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Az előző példában a sablon, amely a legtöbb esetben működik, mivel a sablon ne tartalmazza a bizalmas adatokat egy nyilvánosan elérhető-e URI-t igényel. Adja meg a bizalmas adatok (például egy rendszergazdai jelszót) van szüksége, ha biztonságos paraméterként adja át ezt az értéket. Azonban ha nem szeretné a sablon nyilvánosan hozzáférhető, segítségével megvédheti azokat a személyes tárolót tárolja őket. Egy közös hozzáférésű jogosultságkód (SAS) igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-powershell-sas-token.md). Go-oktatóanyagot, tekintse meg [oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-templates-from-azure-cloud-shell"></a>Az Azure Cloud shell sablonok üzembe helyezése

Használhatja a [Azure Cloud Shell](https://shell.azure.com) a sablon üzembe helyezéséhez. Egy külső sablon üzembe helyezéséhez, adja meg a sablon URI azonosítója. Egy helyi sablon üzembe helyezéséhez, először be kell töltenie a sablont a storage-fiókra a Cloud Shell. Fájlok feltöltése a rendszerhéj, válassza ki a **fájlok feltöltése/letöltése** menüikonra felület ablakából.

A Cloud shell megnyitásához keresse meg a [ https://shell.azure.com ](https://shell.azure.com), vagy válasszon **Try-It** az alábbi kód szakaszban:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Illessze be a kódot a rendszerhéjba van beépítve, kattintson a jobb gombbal a rendszerhéj belül, majd **illessze be**.

## <a name="deploy-to-multiple-resource-groups-or-subscriptions"></a>Üzembe helyezés több erőforráscsoporton vagy előfizetésben

Általában végzi az üzembe helyezést összes erőforrást a sablonhoz, amelyekkel egyetlen erőforráscsoportra. Vannak azonban forgatókönyvek, ahol szeretné erőforráscsoport telepítsen együtt, de különböző erőforráscsoport vagy előfizetés helyezze el őket. Telepíthet egy központi telepítésben csak öt erőforráscsoportokhoz. További információkért lásd: [üzembe helyezése Azure-erőforrásokat az erőforráscsoportok és -előfizetések több](resource-manager-cross-resource-group-deployment.md).

## <a name="redeploy-when-deployment-fails"></a>Telepítse újra a központi telepítésének hibája esetén

Ha egy központi telepítés nem sikerül, automatikusan is újratelepítése egy korábbi, a sikeres telepítés az üzembe helyezési előzményekből. Újbóli üzembe helyezés megadásához használja vagy a `-RollbackToLastDeployment` vagy `-RollBackDeploymentName` paramétert a üzembe helyezés parancsba.

Ez a beállítás használatához az üzemelő példányok egyedi névvel kell rendelkezniük az előzményekben található ellenőrizhető, hogy. Ha nem rendelkezik egyedi nevét, az aktuális telepítése sikertelen volt. a korábban sikeresen végrehajtott központi telepítés előzményei esetleg felülírhatja. Használhatja ezt a beállítást csak a legfelső szintű telepítések. Beágyazott sablonból üzemelő példányok nem újbóli üzembe helyezés érhetők el.

Telepítse újra a legutóbbi sikeres üzembe helyezés, adja hozzá a `-RollbackToLastDeployment` paramétert, azt a jelzőt.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Az újratelepítés egy adott, használja a `-RollBackDeploymentName` paramétert, és adja meg a központi telepítés nevét.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

A megadott központi telepítés sikeres volt kell rendelkeznie.

## <a name="pass-parameter-values"></a>Paraméterértékeket

Paraméterértékek átadni, vagy a beágyazott paraméterek, vagy egy paraméterfájl használható. Az előző példák ebben a cikkben a beágyazott paraméterek megjelenítése.

### <a name="inline-parameters"></a>A beágyazott paraméterek

A beágyazott paraméterek átadni, adja meg a paraméter nevét a `New-AzResourceGroupDeployment` parancsot. Ha például egy karakterláncot és egy tömb átadása egy sablont, használja:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Fájl tartalmának beolvasása, és adja meg a beágyazott paraméterként tartalom is.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
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
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Átadni egy külső alkalmazásparaméter-fájlt, használja a **TemplateParameterUri** paramétert:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>A paraméter sorrend

A beágyazott paraméterek és a egy helyi paraméterfájl azonos központi telepítési művelet is használhatja. Például néhány értéket adja meg a helyi alkalmazásparaméter-fájlt, és adja hozzá az egyéb értékek beágyazott üzembe helyezés során. Ha a paraméter a helyi alkalmazásparaméter-fájlt és a beágyazott értékeket ad meg, a beágyazott elsőbbséget.

Azonban ha egy külső alkalmazásparaméter-fájlt használ, nem adhatók át további értékek vagy beágyazott vagy egy helyi fájlból. A paraméterfájl megadása a **TemplateParameterUri** paraméter, minden beágyazott paraméterek a rendszer figyelmen kívül hagyja. Adja meg a külső fájl összes paraméter értéke. Ha a sablont, amely nem adhat meg a paraméterfájlt kényes értéket tartalmaz, adja hozzá a key vault ezt az értéket, vagy dinamikusan adjon meg minden paramétert értékek beágyazott.

### <a name="parameter-name-conflicts"></a>A paraméter neve ütközik

A sablon tartalmazza a PowerShell-parancs olyan paraméterre, amelynek neve megegyezik a paraméterek egyikét, ha PowerShell be az a paraméter a sablonból a utótag **FromTemplate**. Például nevű paraméter **ResourceGroupName** a sablon ütközik a **ResourceGroupName** paramétert a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) a parancsmag. Adjon meg egy értéket a kéri **ResourceGroupNameFromTemplate**. Általában ez zavart ne által nem elnevezési paraméterek az üzembe helyezési műveleteihez használt paraméterek azonos néven.

## <a name="test-template-deployments"></a>Sablon-üzembehelyezések tesztelése

A sablonnal és paraméterfájlokkal értékek teszteléséhez ténylegesen az olyan erőforrások telepítés nélkül használhatja a [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Ha nincsenek hibák észlelhetők, a parancs befejeződik, a válaszra. Ha hibát észlel, a parancs hibaüzenetet ad vissza. Például a tárfiók SKU, helytelen értéket átadja adja vissza a következő hibával:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Ha a sablon szintaktikai hibát tartalmaz, a parancs visszaadja egy hibaüzenet, nem lehetett elemezni a sablont. Az üzenet azt jelzi, hogy a sor száma és az elemzési hiba pozícióját.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>További lépések

- Biztonságosan terjed ki a szolgáltatás több régióban, lásd: [Azure Deployment Manager](deployment-manager-overview.md).
- Adja meg, hogyan legyen kezelve az erőforrásokat, az erőforráscsoportban létezik, de nincsenek definiálva a sablonban, lásd: [Azure Resource Manager üzembe helyezési mód](deployment-modes.md).
- A sablonban szereplő paraméterekkel definiálása ismertetése: [struktúra és az Azure Resource Manager-sablonok szintaxisát](resource-group-authoring-templates.md).
- SAS-token igénylő sablonok telepítésével kapcsolatos információkért lásd: [saját sablon üzembe helyezése SAS-jogkivonat használatával](resource-manager-powershell-sas-token.md).
