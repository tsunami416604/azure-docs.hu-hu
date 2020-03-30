---
title: Sablon biztonságos üzembe helyezése SAS-jogkivonattal
description: Erőforrások üzembe helyezése az Azure-ba egy SAS-jogkivonattal védett Azure Resource Manager-sablonnal. Az Azure PowerShell és az Azure CLI megjelenítése.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156395"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Privát ARM-sablon telepítése SAS-jogkivonattal

Ha az Azure Resource Manager (ARM) sablon egy tárfiókban található, korlátozhatja a sablonhoz való hozzáférést, hogy elkerülje a nyilvános felfedést. A védett sablonhoz úgy érhet el, hogy létrehoz egy közös hozzáférésű aláírási (SAS) jogkivonatot a sablonhoz, és biztosítja a jogkivonatot a központi telepítés során. Ez a cikk bemutatja, hogyan használhatja az Azure PowerShell vagy az Azure CLI egy sas-jogkivonattal egy sablon üzembe helyezéséhez.

## <a name="create-storage-account-with-secured-container"></a>Tárfiók létrehozása biztonságos tárolóval

A következő parancsfájl létrehoz egy tárfiókot és tárolót, amelynek nyilvános hozzáférése ki van kapcsolva.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Sablon feltöltése a tárfiókba

Most már készen áll a sablon feltöltésére a tárfiókba. Adja meg a használni kívánt sablon elérési útját.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>SAS-jogkivonat biztosítása az üzembe helyezés során

Privát sablon központi telepítése egy tárfiókban, hozzon létre egy SAS-jogkivonatot, és vegye fel a sablon URI-ba. Állítsa be a lejárati időt, hogy elegendő idő a központi telepítés befejezéséhez.

> [!IMPORTANT]
> A sablont tartalmazó blob csak a fiók tulajdonosa számára érhető el. Azonban, ha létrehoz egy SAS-jogkivonatot a blobhoz, a blob mindenki számára elérhető az adott URI-val. Ha egy másik felhasználó elfogja az URI-t, a felhasználó hozzáférhet a sablonhoz. A SAS-jogkivonat jó módszer a sablonokhoz való hozzáférés korlátozására, de ne tartalmazzon bizalmas adatokat, például jelszavakat közvetlenül a sablonban.
>

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

A SAS-jogkivonat csatolt sablonokkal való használatáról a [Csatolt sablonok használata az Azure Resource Manager rel](linked-templates.md)című témakörben olvashat.


## <a name="next-steps"></a>További lépések
* A sablonok üzembe helyezésének bemutatása az [ERŐFORRÁSOK üzembe helyezése ARM-sablonokkal és az Azure PowerShell használatával című témakörben olvashat.](deploy-powershell.md)
* Paraméterek sablonban történő meghatározásáról a [Sablonok készítése](template-syntax.md#parameters)című témakörben olvashat.
