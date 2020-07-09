---
title: Erőforrások üzembe helyezése a PowerShell és a sablon használatával
description: Erőforrások üzembe helyezése az Azure-ban Azure Resource Manager és Azure PowerShell használatával. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: af255e0248c029f42c9c2999ae7c0389d60c58fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84431838"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell

Ismerje meg, hogyan helyezhet üzembe erőforrásokat az Azure-ban a Azure PowerShell és Azure Resource Manager (ARM) sablonok használatával. További információ az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmakról: [sablonok üzembe helyezésének áttekintése](overview.md).

## <a name="deployment-scope"></a>Központi telepítés hatóköre

Az üzembe helyezést egy erőforráscsoport, egy előfizetés, egy felügyeleti csoport vagy egy bérlő számára is megcélozhatja. A legtöbb esetben az üzembe helyezést egy erőforráscsoporthoz kell megcélozni. A házirendek és a szerepkör-hozzárendelések nagyobb hatókörön való alkalmazásához használja az előfizetést, a felügyeleti csoportot vagy a bérlő központi telepítését. Az előfizetések telepítésekor létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet üzembe a szolgáltatásban.

A központi telepítés hatókörének függvényében különböző parancsokat kell használnia.

* Egy **erőforráscsoporthoz**való üzembe helyezéshez használja a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* Az **előfizetéshez**való üzembe helyezéshez használja a New-AzSubscriptionDeployment:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Az előfizetési szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md).

* Ha **felügyeleti csoportba**kíván üzembe helyezni, használja a [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  További információ a felügyeleti csoport szintű központi telepítésekről: [erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md).

* Egy **bérlőn**való üzembe helyezéshez használja a [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  A bérlői szintű központi telepítésekkel kapcsolatos további információkért lásd: [erőforrások létrehozása a bérlő szintjén](deploy-to-tenant.md).

A cikkben szereplő példák az erőforráscsoportok központi telepítését használják.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz egy telepítendő sablonra. Ha még nem rendelkezik ilyennel, töltsön le és mentsen egy [példaként szolgáló sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) az Azure Gyorsindítás sablonok tárházból. A cikkben használt helyi fájl neve **c:\MyTemplates\azuredeploy.js**.

Ha nem használja a Azure Cloud Shell a sablonok üzembe helyezéséhez, telepítenie kell az Azure PowerShellt, és kapcsolódnia kell az Azure-hoz:

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

Az ARM-sablonok helyi gépen való tárolása helyett érdemes lehet őket külső helyen tárolni. A sablonok a verziókövetés adattárában (például a GitHubon) is tárolhatók. Egy Azure Storage-fiókban is tárolhatja őket a szervezet megosztott hozzáféréséhez.

Külső sablon üzembe helyezéséhez használja a **TemplateUri** paramétert. A példában szereplő URI használatával telepítse a minta sablont a GitHubról.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Az előző példa egy nyilvánosan elérhető URI-t igényel a sablonhoz, amely a legtöbb forgatókönyv esetében működik, mert a sablon nem tartalmazhat bizalmas adatokat. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja át ezt az értéket biztonságos paraméterként. Ha azonban nem szeretné, hogy a sablon nyilvánosan elérhető legyen, akkor azt egy privát tárolóban tárolhatja. A közös hozzáférésű aláírási (SAS-) tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon telepítése sas-tokenrel](secure-template-with-sas-token.md). Az oktatóanyag lépéseinek megismeréséhez tekintse meg [az oktatóanyag: integráció Azure Key Vault az ARM-sablonok üzembe helyezése](template-tutorial-use-key-vault.md)című témakört.

## <a name="preview-changes"></a>Előnézeti változások

A sablon üzembe helyezése előtt megtekintheti, hogy a sablon milyen módosításokat hajt végre a környezetben. A [mi-if művelettel](template-deploy-what-if.md) ellenőrizheti, hogy a sablon elvégzi-e a várt módosításokat. Mi a teendő, ha a hibát is ellenőrzi a sablonban.

## <a name="deploy-from-azure-cloud-shell"></a>Üzembe helyezés Azure Cloud Shell

A [Azure Cloud Shell](https://shell.azure.com) a sablon üzembe helyezéséhez használható. Külső sablon üzembe helyezéséhez adja meg a sablon URI-JÁT. Helyi sablon üzembe helyezéséhez először be kell töltenie a sablont a Cloud Shell Storage-fiókjába. Fájlok a rendszerhéjba való feltöltéséhez válassza a **fájlok feltöltése/letöltése** menü ikont a rendszerhéj ablakában.

A Cloud Shell megnyitásához tallózással keresse meg a [https://shell.azure.com](https://shell.azure.com) következő kódrészletet, vagy válassza a **TRY-IT** elemet:

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

Egy paraméter értékének beolvasása a fájlból hasznos lehet, ha konfigurációs értékeket kell megadnia. Megadhat például [egy Linux rendszerű virtuális gép számára a Cloud-init értékeket](../../virtual-machines/linux/using-cloud-init.md).

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

További információ a paraméter fájlról: [Resource Manager-paraméter fájljának létrehozása](parameter-files.md).

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

## <a name="next-steps"></a>További lépések

- Ha hibát tapasztal a sikeres üzembe helyezéshez, olvassa el a [hiba visszaállítása a sikeres központi telepítéshez](rollback-on-error.md)című témakört.
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoport meglévő erőforrásait, de a sablonban nincs definiálva, tekintse meg a [Azure Resource Manager üzembe helyezési módokat](deployment-modes.md).
- Ha meg szeretné tudni, hogyan határozhat meg paramétereket a sablonban, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című részt.
- A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).
