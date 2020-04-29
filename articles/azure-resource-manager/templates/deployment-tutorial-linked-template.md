---
title: Oktatóanyag – csatolt sablon üzembe helyezése
description: Útmutató csatolt sablon üzembe helyezéséhez
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 177a994450b6ffe5489a8c95c3b484521fd9b77b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80672919"
---
# <a name="tutorial-deploy-a-linked-template"></a>Oktatóanyag: csatolt sablon üzembe helyezése

Az [előző oktatóanyagokban](./deployment-tutorial-local-template.md)megtanulta, hogyan helyezhet üzembe egy helyi számítógépen tárolt sablont. Összetett megoldások üzembe helyezéséhez megszakíthat egy sablont számos sablonba, és egy fő sablon segítségével telepítheti ezeket a sablonokat. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy olyan fő sablont, amely egy csatolt sablonra mutató hivatkozást tartalmaz. A fő sablon üzembe helyezése után elindítja a csatolt sablon központi telepítését. Azt is megtudhatja, hogyan tárolhat és biztonságossá teheti a csatolt sablont SAS-token használatával. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be az előző oktatóanyagot, de ez nem kötelező.

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyagokban egy olyan sablont telepít, amely létrehoz egy Storage-fiókot, App Service-csomagot és egy webalkalmazást. A sablon a következőket használta:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Csatolt sablon létrehozása

A Storage-fiók erőforrását egy csatolt sablonba is elkülönítheti:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

A fő sablon a következő sablon.  A Kiemelt **Microsoft. Resources/Deployments** objektum egy csatolt sablon meghívását mutatja be. A csatolt sablon nem tárolható helyi fájlként vagy olyan fájlként, amely csak a helyi hálózaton érhető el. Csak olyan URI-értéket adhat meg, amely *http* vagy *HTTPS protokollt*is tartalmaz. A Resource Managernek képesnek kell lennie hozzáférni a sablonhoz. Az egyik lehetőség, hogy a csatolt sablont egy Storage-fiókba helyezi, és az adott elemhez tartozó URI-t használja. Az URI-t egy paraméterrel kell átadni a sablonnak. Tekintse meg a kijelölt paraméter definícióját.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Mentse a fő sablon egy példányát a helyi számítógépre a. JSON kiterjesztéssel, például: azuredeploy. JSON. Nem kell mentenie a csatolt sablon másolatát.  A csatolt sablon a GitHub-adattárból egy Storage-fiókba lesz átmásolva.

## <a name="store-the-linked-template"></a>A csatolt sablon tárolása

A következő PowerShell-szkript létrehoz egy Storage-fiókot, létrehoz egy tárolót, és átmásolja a csatolt sablont egy GitHub-adattárból a tárolóba. A csatolt sablon másolatát a [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json)tárolja.

Válassza a **kipróbálás-ez** lehetőséget a Cloud Shell megnyitásához, válassza a **Másolás** lehetőséget a PowerShell-parancsfájl másolásához, majd kattintson a jobb gombbal a rendszerhéj ablaktáblára a parancsfájl beillesztéséhez:

> [!IMPORTANT]
> A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon. A névnek egyedinek kell lennie. A sablonban a Storage-fiók neve a projekt neve a "Store" hozzáfűzéssel, a projekt nevének pedig 3 – 11 karakterből kell állnia. Így a projekt nevének meg kell felelnie a Storage-fiók nevének, és kevesebb, mint 11 karakterből áll.

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

Ha privát sablont szeretne üzembe helyezni egy Storage-fiókban, állítson be egy SAS-tokent, és vegye fel azt a sablon URI-kódjába. Állítsa be a lejárati időt, hogy elegendő idő legyen a telepítés befejezésére. A sablont tartalmazó blob csak a fiók tulajdonosa számára érhető el. Ha azonban SAS-jogkivonatot hoz létre a blobhoz, a blob mindenki számára elérhető lesz az adott URI-val. Ha egy másik felhasználó elfogja az URI-t, a felhasználó hozzáférhet a sablonhoz. Az SAS-token jó módszer a sablonokhoz való hozzáférés korlátozására, de nem tartalmazhat bizalmas adatokat, például jelszavakat közvetlenül a sablonban.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](./deployment-tutorial-local-template.md#create-resource-group)című témakört.

> [!NOTE]
> Az alábbi Azure CLI-kódban a-d dátum paraméter nem lehet a macOS-ben Érvénytelen argumentum. Így a macOS-felhasználók számára a-v + 2H-t kell használnia a macOS-es terminálon, hogy az aktuális idő 2 óra legyen.

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

Az erőforráscsoport törlésével törölje az üzembe helyezett erőforrásokat.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan helyezhet üzembe egy csatolt sablont. A következő oktatóanyagban megtudhatja, hogyan hozhat létre egy DevOp folyamatot egy sablon üzembe helyezéséhez.

> [!div class="nextstepaction"]
> [Folyamat létrehozása](./deployment-tutorial-pipeline.md)
