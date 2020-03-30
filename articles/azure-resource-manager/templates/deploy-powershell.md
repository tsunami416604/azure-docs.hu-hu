---
title: Erőforrások üzembe helyezése PowerShell és sablon használatával
description: Az Azure Resource Manager és az Azure PowerShell segítségével erőforrásokat helyezhet üzembe az Azure-ba. Az erőforrások egy Resource Manager-sablonban vannak meghatározva.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153267"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell-sablonokkal

Ismerje meg, hogyan használhatja az Azure PowerShellt az Azure Resource Manager (ARM) sablonokkal az erőforrások Azure-ba való üzembe helyezéséhez. Az Azure-megoldások üzembe helyezésének és kezelésének koncepcióiról a [sablon üzembe helyezésének áttekintése című témakörben olvashat bővebben.](overview.md)

## <a name="deployment-scope"></a>Központi telepítési hatókör

A központi telepítést egy erőforráscsoportra, előfizetésre, felügyeleti csoportra vagy bérlőre célozhatja. A legtöbb esetben egy erőforráscsoportra fogja célozni a központi telepítést. A szabályzatok és a szerepkör-hozzárendelések alkalmazása egy nagyobb hatókörben, használja előfizetés, felügyeleti csoport vagy bérlői központi telepítések. Előfizetésre való üzembe helyezéskor létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet üzembe.

A központi telepítés hatókörétől függően különböző parancsokat kell használnia.

Ha **erőforráscsoportra**szeretné telepíteni, használja a [New-AzResourceGroupDeployment (Új-AzResourceGroupDeployment) ( Új-AzResourceGroupDeployment ) használatát:](/powershell/module/az.resources/new-azresourcegroupdeployment)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Előfizetésre való üzembe helyezéshez használja **a**New-AzSubscriptionDeployment használatát:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Az előfizetési szintű központi telepítésekről további információt [az Erőforráscsoportok és -erőforrások létrehozása előfizetési szinten](deploy-to-subscription.md)című témakörben talál.

Ha egy **felügyeleti csoportba**szeretné telepíteni, használja a [New-AzManagementGroupDeployment .](/powershell/module/az.resources/New-AzManagementGroupDeployment)

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

A felügyeleticsoport szintű központi telepítésekről további információt [az Erőforrások létrehozása a felügyeleti csoport szintjén című témakörben](deploy-to-management-group.md)talál.

A **bérlőre**való üzembe helyezéshez használja a [New-AzTenantDeployment .](/powershell/module/az.resources/new-aztenantdeployment)

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

A bérlői szintű központi telepítésekről további információt [az Erőforrások létrehozása bérlői szinten](deploy-to-tenant.md)című témakörben talál.

Ebben a cikkben szereplő példák erőforráscsoport-telepítések használatát használja.

## <a name="prerequisites"></a>Előfeltételek

A telepítéshez sablonra van szükség. Ha még nem rendelkezik ilyen, töltse le és mentse na az Azure Gyorsútmutató sablonok tártárából egy [példasablont.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) A cikkben használt helyi **fájlnév: c:\MyTemplates\azuredeploy.json**.

Ha nem használja az Azure Cloud Shellt a sablonok üzembe helyezéséhez, telepítenie kell az Azure PowerShellt, és csatlakoznia kell az Azure-hoz:

- **Telepítse az Azure PowerShell-parancsmagokat a helyi számítógépre.** További információért lásd [az Azure PowerShell használatának első lépéseit](/powershell/azure/get-started-azureps).
- **Csatlakozzon az [Azure-hoz a Connect-AZAccount segítségével.](/powershell/module/az.accounts/connect-azaccount)** Ha több Azure-előfizetéssel rendelkezik, előfordulhat, hogy a [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)szolgáltatást is futtatnia kell. További információ: [Több Azure-előfizetés használata.](/powershell/azure/manage-subscriptions-azureps)

## <a name="deploy-local-template"></a>Helyi sablon telepítése

A következő példa létrehoz egy erőforráscsoportot, és telepíti a sablont a helyi gépről. Az erőforráscsoport neve csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójeleket tartalmazhat. Legfeljebb 90 karakter lehet. Nem végződhet egy időszakban.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Az üzembe helyezés eltarthat néhány percig.

## <a name="deploy-remote-template"></a>Távoli sablon telepítése

Ahelyett, hogy arm sablonokat tárolna a helyi számítógépen, célszerű lehet azokat egy külső helyen tárolni. A sablonokat egy forrásvezérlő tárházban (például a GitHubon) tárolhatja. Vagy tárolhatja őket egy Azure storage-fiókban a szervezeten belüli megosztott hozzáféréshez.

Külső sablon üzembe helyezéséhez használja a **TemplateUri** paramétert. Használja az URI-t a példában a mintasablon üzembe helyezéséhez a GitHubról.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Az előző példa nyilvánosan elérhető URI-t igényel a sablonhoz, amely a legtöbb esetben működik, mert a sablon nem tartalmazhat bizalmas adatokat. Ha bizalmas adatokat (például rendszergazdai jelszót) kell megadnia, adja át ezt az értéket biztonságos paraméterként. Ha azonban nem szeretné, hogy a sablon nyilvánosan elérhető legyen, megvédheti azt egy privát tárolótárolóban való tárolásával. A megosztott hozzáférésű aláírási (SAS) jogkivonatot igénylő sablonok központi telepítéséről a [Privát sablon telepítése SAS-jogkivonattal](secure-template-with-sas-token.md)című témakörben olvashat. Az oktatóanyag megtekintéséhez tekintse meg [az Oktatóanyag: Az Azure Key Vault integrálása az ARM-sablon üzembe helyezésében.](template-tutorial-use-key-vault.md)

## <a name="deploy-from-azure-cloud-shell"></a>Üzembe helyezés az Azure Cloud Shellből

Az Azure [Cloud Shell](https://shell.azure.com) segítségével telepítheti a sablont. Külső sablon üzembe helyezéséhez adja meg a sablon URI-ját. Helyi sablon üzembe helyezéséhez először be kell töltenie a sablont a Cloud Shell tárfiókjába. Ha fájlokat szeretne feltölteni a rendszerhéjba, válassza a **Fájlok feltöltése/letöltése** menüikont a rendszerhéj ablakából.

A Felhőrendszerhéj megnyitásához [https://shell.azure.com](https://shell.azure.com)keresse meg a tallózással a következő kódszakaszban a **Try-It** elemet:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

A kód shellbe való beillesztéséhez kattintson a jobb gombbal a rendszerhéjon belülre, majd válassza a **Beillesztés parancsot.**

## <a name="pass-parameter-values"></a>Paraméterértékek áthaladása

A paraméterértékek átadására használhat szövegközi paramétereket vagy paraméterfájlt.

### <a name="inline-parameters"></a>Szövegközi paraméterek

A szövegközi paraméterek átadására adja meg `New-AzResourceGroupDeployment` a paraméter nevét a paranccsal. Ha például egy karakterláncot és egy tömböt szeretne átadni egy sablonnak, használja a következőket:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

A fájl tartalmát is beszerezheti, és a tartalmat szövegközi paraméterként is megadhatja.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

A paraméterérték fájlból való beszerzése akkor hasznos, ha konfigurációs értékeket kell megadnia. Például felhőalapú init értékeket adhat meg [egy Linux-alapú virtuális géphez.](../../virtual-machines/linux/using-cloud-init.md)

Ha objektumok tömbjében kell átadnia, hozzon létre kivonatoló táblákat a PowerShellben, és adja hozzá őket egy tömbhöz. Adja át a tömböt paraméterként az üzembe helyezés során.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Paraméterfájlok

Ahelyett, hogy a paramétereket szövegközi értékként adná át a parancsfájlban, egyszerűbben használhatja a paraméterértékeket tartalmazó JSON-fájlt. A paraméterfájl lehet helyi vagy külső fájl hozzáférhető URI-val.

A paraméterfájlról az [Erőforrás-kezelő paraméterfájl létrehozása](parameter-files.md)című témakörben talál további információt.

Helyi paraméterfájl átadására használja a **TemplateParameterFile** paramétert:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Külső paraméterfájl átadására használja a **TemplateParameterUri** paramétert:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Tesztsablon-telepítések

Ha a sablont és a paraméterértékeket erőforrások tényleges üzembe helyezése nélkül szeretné tesztelni, használja a [Test-AzResourceGroupDeployment parancsot.](/powershell/module/az.resources/test-azresourcegroupdeployment) 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Ha a rendszer nem észlel hibát, a parancs válasz nélkül fejeződik be. Hiba észlelése esetén a parancs hibaüzenetet ad vissza. Ha például helytelen értéket ad meg a tárfiók Termékváltozatának, a következő hibát adja vissza:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Ha a sablon szintaktikai hiba miatt a parancs hibát ad vissza, amely azt jelzi, hogy nem tudta elemezni a sablont. Az üzenet az elemzési hiba sorszámát és pozícióját jelzi.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>További lépések

- Ha hiba esetén szeretne visszaáll egy sikeres központi telepítésre, olvassa el [a Hiba visszaállítása a sikeres üzembe helyezéshez.](rollback-on-error.md)
- Ha meg szeretné adni, hogyan kezelje az erőforráscsoportban létező, de a sablonban nem definiált erőforrásokat, olvassa el az Azure Resource Manager telepítési módjai című [témakört.](deployment-modes.md)
- A paraméterek sablonban való definiálásának megértéséhez [olvassa el Az ARM-sablonok szerkezetének és szintaxisának megismerése című témakört.](template-syntax.md)
- A SAS-jogkivonatot igénylő sablonok üzembe helyezéséről a [Privát sablon telepítése SAS-jogkivonattal](secure-template-with-sas-token.md)című témakörben olvashat.
