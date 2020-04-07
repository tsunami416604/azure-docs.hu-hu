---
title: Oktatóanyag – Csatolt sablon telepítése
description: Csatolt sablon központi telepítése
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 177a994450b6ffe5489a8c95c3b484521fd9b77b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672919"
---
# <a name="tutorial-deploy-a-linked-template"></a>Oktatóanyag: Csatolt sablon telepítése

Az [előző oktatóanyagokból](./deployment-tutorial-local-template.md)megtanulta, hogyan telepíthet a helyi számítógépen tárolt sablont. Összetett megoldások üzembe helyezéséhez a sablonokat számos sablonra bonthatja, és ezeket a sablonokat egy fő sablonon keresztül telepítheti. Ebben az oktatóanyagban megtudhatja, hogyan telepíthet egy fő sablont, amely egy csatolt sablonra való hivatkozást tartalmaz. Amikor a fő sablon üzembe kerül, elindítja a csatolt sablon üzembe helyezését. Azt is megtudhatja, hogyan tárolhatja és biztonságossá a csatolt sablon segítségével SAS-jogkivonat. Körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Azt javasoljuk, hogy töltse ki az előző oktatóanyag, de ez nem szükséges.

## <a name="review-template"></a>Véleményezési sablon

Az előző oktatóanyagokban egy olyan sablont telepít, amely létrehoz egy tárfiókot, az App Service-csomagot és a webalkalmazást. A használt sablon a következő volt:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Csatolt sablon létrehozása

A tárfiók-erőforrást csatolt sablonra bonthatja:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

A következő sablon a fő sablon.  A kiemelt **Microsoft.Resources/deployments** objektum bemutatja, hogyan hívhato meg a csatolt sablont. A csatolt sablon nem tárolható helyi fájlként vagy olyan fájlként, amely csak a helyi hálózaton érhető el. Csak *http-t* vagy *https-t*tartalmazó URI-értéket adhat meg. Az Erőforrás-kezelőnek képesnek kell lennie a sablon elérésére. Az egyik lehetőség az, hogy helyezze el a csatolt sablont egy tárfiókban, és használja az adott elem URI-ját. Az URI egy paraméter használatával kerül átadásra a sablonnak. Tekintse meg a kiemelt paraméterdefiníciót.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Mentse a fő sablon egy példányát a helyi számítógépre a .json kiterjesztéssel, például az azuredeploy.json. A csatolt sablon egy példányát nem kell mentenie.  A csatolt sablon takara lesz a GitHub-tárházból egy tárfiókba.

## <a name="store-the-linked-template"></a>Csatolt sablon tárolása

A következő PowerShell-parancsfájl létrehoz egy tárfiókot, létrehoz egy tárolót, és másolja a csatolt sablont egy GitHub-tárházból a tárolóba. A csatolt sablon egy példánya a [GitHubon](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json)tárolódik.

Válassza **a Try-it** lehetőséget a Cloud Shell megnyitásához, a **Másolás parancsot** a PowerShell-parancsfájl másolásához, és kattintson a jobb gombbal a rendszerhéj ablaktáblájára a parancsfájl beillesztéséhez:

> [!IMPORTANT]
> A tárfióknevek nek 3 és 24 karakter közötti hosszúságúnak kell lenniük, és csak számokat és kisbetűket használhatnak. A névnek egyedinek kell lennie. A sablonban a tárfiók neve a projekt neve az "store" csatolt, és a projekt neve 3 és 11 karakter között kell lennie. Így a projekt nevének meg kell felelnie a tárfiók nevére vonatkozó követelményeknek, és kevesebb, mint 11 karakterből áll.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Sablon üzembe helyezése

Privát sablon központi telepítése egy tárfiókban, hozzon létre egy SAS-jogkivonatot, és vegye fel a sablon URI-ba. Állítsa be a lejárati időt, hogy elegendő idő a központi telepítés befejezéséhez. A sablont tartalmazó blob csak a fiók tulajdonosa számára érhető el. Azonban, ha létrehoz egy SAS-jogkivonatot a blobhoz, a blob mindenki számára elérhető az adott URI-val. Ha egy másik felhasználó elfogja az URI-t, a felhasználó hozzáférhet a sablonhoz. A SAS-jogkivonat jó módszer a sablonokhoz való hozzáférés korlátozására, de ne tartalmazzon bizalmas adatokat, például jelszavakat közvetlenül a sablonban.

Ha még nem hozta létre az erőforráscsoportot, olvassa el az Erőforráscsoport létrehozása című [témakört.](./deployment-tutorial-local-template.md#create-resource-group)

> [!NOTE]
> Az alábbi Azure CLI-kódban a -d dátumparaméter érvénytelen argumentum lenne a macOS rendszerben. Tehát a macOS felhasználók, hogy 2 óra legyen az aktuális időhöz a macOS terminálján, a -v+2H-t kell használnia.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport törlésével törölje a telepített erőforrásokat.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan kell telepíteni egy csatolt sablont. A következő oktatóanyagban megtudhatja, hogyan hozhat létre egy DevOp-folyamatot egy sablon üzembe helyezéséhez.

> [!div class="nextstepaction"]
> [Folyamat létrehozása](./deployment-tutorial-pipeline.md)
