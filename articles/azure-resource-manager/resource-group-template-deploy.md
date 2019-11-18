---
title: Erőforrások üzembe helyezése a PowerShell és a sablon használatával
description: Erőforrások üzembe helyezése az Azure-ban Azure Resource Manager és Azure PowerShell használatával. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: e726d1154fca1dbcce244783bf987bea6610cf98
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150707"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel

Ismerje meg, hogyan helyezhet üzembe erőforrásokat az Azure-ban Resource Manager-sablonokkal a Azure PowerShell használatával. További információ az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmakról: [Azure Resource Manager Overview (áttekintés](resource-group-overview.md)).

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy előfizetésen belül egy Azure-előfizetésre vagy egy erőforráscsoporthoz is megcélozhatja. A legtöbb esetben az üzembe helyezést egy erőforráscsoporthoz kell megcélozni. Az előfizetés központi telepítései használatával házirendek és szerepkör-hozzárendelések alkalmazhatók az előfizetések között. Az előfizetések központi telepítését is használhatja az erőforráscsoport létrehozásához és az erőforrások üzembe helyezéséhez. A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

Egy **erőforráscsoporthoz**való üzembe helyezéshez használja a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Az **előfizetéshez**való üzembe helyezéshez használja a [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Az előfizetési szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

Jelenleg a felügyeleti csoport központi telepítései csak a REST APIon keresztül támogatottak. További információ a felügyeleti csoport szintű központi telepítésekről: [erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md).

A cikkben szereplő példák az erőforráscsoportok központi telepítését használják.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy telepítendő sablonra. Ha még nem rendelkezik ilyennel, töltsön le és mentsen egy [példaként szolgáló sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) az Azure Gyorsindítás sablonok tárházból. A cikkben használt helyi fájl neve **c:\MyTemplates\azuredeploy.JSON**.

Ha a sablonok üzembe helyezéséhez az Azure Cloud shellt használja, telepítenie kell a Azure PowerShellt, és kapcsolódnia kell az Azure-hoz:

- **Telepítse Azure PowerShell parancsmagokat a helyi számítógépen.** További információért lásd [az Azure PowerShell használatának első lépéseit](/powershell/azure/get-started-azureps).
- **Kapcsolódjon az Azure [-](/powershell/module/az.accounts/connect-azaccount)hoz a AZAccount használatával**. Ha több Azure-előfizetéssel rendelkezik, előfordulhat, hogy a [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)parancsot is futtatnia kell. További információ: [több Azure-előfizetés használata](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Helyi sablon üzembe helyezése

A következő példában létrehozunk egy erőforráscsoportot, és üzembe helyezünk egy sablont a helyi gépről. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásokat, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter hosszú lehet. Nem végződhet ponttal.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Az üzembe helyezés eltarthat néhány percig.

## <a name="deploy-remote-template"></a>Távoli sablon üzembe helyezése

A Resource Manager-sablonok helyi gépen való tárolása helyett érdemes lehet őket külső helyen tárolni. A sablonok a verziókövetés adattárában (például a GitHubon) is tárolhatók. Egy Azure Storage-fiókban is tárolhatja őket a szervezet megosztott hozzáféréséhez.

Külső sablon üzembe helyezéséhez használja a **TemplateUri** paramétert. A példában szereplő URI használatával telepítse a minta sablont a GitHubról.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Az előző példa egy nyilvánosan elérhető URI-t igényel a sablonhoz, amely a legtöbb forgatókönyv esetében működik, mert a sablon nem tartalmazhat bizalmas adatokat. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja át ezt az értéket biztonságos paraméterként. Ha azonban nem szeretné, hogy a sablon nyilvánosan elérhető legyen, akkor azt egy privát tárolóban tárolhatja. A közös hozzáférésű aláírási (SAS-) tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon telepítése sas-tokenrel](resource-manager-powershell-sas-token.md). Az oktatóanyag lépéseinek megismeréséhez tekintse meg [az oktatóanyag: Azure Key Vault integrálása a Resource Manager template deploymentban](./resource-manager-tutorial-use-key-vault.md)című témakört.

## <a name="deploy-from-azure-cloud-shell"></a>Üzembe helyezés az Azure Cloud shellből

A [Azure Cloud Shell](https://shell.azure.com) a sablon üzembe helyezéséhez használható. Külső sablon üzembe helyezéséhez adja meg a sablon URI-JÁT. Helyi sablon üzembe helyezéséhez először be kell töltenie a sablont a Cloud Shell Storage-fiókjába. Fájlok a rendszerhéjba való feltöltéséhez válassza a **fájlok feltöltése/letöltése** menü ikont a rendszerhéj ablakában.

A Cloud Shell megnyitásához keresse meg a [https://shell.azure.com](https://shell.azure.com), vagy válassza a **TRY-IT** elemet a következő kód szakaszban:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

A kód a rendszerhéjba való beillesztéséhez kattintson a jobb gombbal a rendszerhéjon belül, majd válassza a **Beillesztés**lehetőséget.

## <a name="pass-parameter-values"></a>Paraméter értékeinek továbbítása

A paraméterek értékének átadásához használhat beágyazott paramétereket vagy egy paraméter-fájlt.

### <a name="inline-parameters"></a>Beágyazott paraméterek

A beágyazott paraméterek továbbításához adja meg a paraméter nevét a `New-AzResourceGroupDeployment` paranccsal. Ha például egy karakterláncot és tömböt szeretne átadni egy sablonnak, használja a következőt:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Emellett beolvashatja a fájl tartalmát, és megadhatja a tartalmat beágyazott paraméterként.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Egy paraméter értékének beolvasása a fájlból hasznos lehet, ha konfigurációs értékeket kell megadnia. Megadhat például [egy Linux rendszerű virtuális gép számára a Cloud-init értékeket](../virtual-machines/linux/using-cloud-init.md).

Ha objektumok tömbjét kell átadnia, hozzon létre kivonatoló táblákat a PowerShellben, és adja hozzá őket egy tömbhöz. Adja át ezt a tömböt paraméterként az üzembe helyezés során.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Paraméter fájljai

Ahelyett, hogy a paramétereket a parancsfájlba beágyazott értékként adja át, előfordulhat, hogy könnyebben használható egy JSON-fájl, amely tartalmazza a paraméter értékeit. A paraméter fájl lehet helyi fájl vagy egy elérhető URI-val rendelkező külső fájl is.

További információ a paraméter fájlról: [Resource Manager-paraméter fájljának létrehozása](resource-manager-parameter-files.md).

Helyi paraméter fájljának átadásához használja a **TemplateParameterFile** paramétert:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Külső paraméter fájljának átadásához használja a **TemplateParameterUri** paramétert:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Sablon központi telepítésének tesztelése

A sablon és a paraméterek értékének teszteléséhez az erőforrások tényleges telepítése nélkül használja a [test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Ha a rendszer nem észlel hibát, a parancs válasz nélkül fejeződik be. Ha a rendszer hibát észlel, a parancs hibaüzenetet ad vissza. Ha például helytelen értéket ad meg a Storage-fiók SKU-jának, a a következő hibaüzenetet adja vissza:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Ha a sablon szintaktikai hibát tartalmaz, a parancs egy hibaüzenetet ad vissza, amely azt jelzi, hogy nem tudta elemezni a sablont. Az üzenet a sorszámot és az elemzési hiba pozícióját jelzi.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>További lépések

- Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](resource-group-authoring-templates.md)ismertető témakört.
- A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](resource-manager-powershell-sas-token.md).
