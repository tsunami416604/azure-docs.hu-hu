---
title: Az SAS-jogkivonat és az Azure CLI Azure-sablon üzembe helyezése |} Microsoft Docs
description: Azure Resource Manager és az Azure parancssori felület használatával telepítse az erőforrások az Azure által védett SAS-jogkivonat sablonból.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: b97fdea36eab914926ff91e1f27bdab0fb8a3930
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Az SAS-jogkivonat és az Azure parancssori felület titkos Resource Manager-sablon üzembe helyezése

Ha a sablon olyan tárfiókban található, korlátozza a hozzáférést a sablont, és adjon meg egy közös hozzáférésű jogosultságkód (SAS) token üzembe helyezése során. Ez a témakör ismerteti az Azure PowerShell használata a Resource Manager-sablonok központi telepítése során egy SAS-jogkivonat biztosításához. 

## <a name="add-private-template-to-storage-account"></a>A tárfiók saját sablon hozzáadása

A sablonok hozzáadása egy tárfiókot, és hivatkozzon őket egy SAS-jogkivonat a telepítés során.

> [!IMPORTANT]
> Az alábbi lépéseket követve sablont tartalmazó blob elérhető, csak a fiók tulajdonosa. Amikor létrehoz egy SAS-jogkivonat a blob, a blob azonban, hogy az URI-azonosítójú bárki hozzáférhet. Ha egy másik felhasználó elfogja az URI, a felhasználó hozzáférjen a sablon lesz. A SAS-jogkivonat használatával egy jó módszer a sablonok való hozzáférés korlátozása, de kell nem tartalmazhat bizalmas adatok, például jelszavak közvetlenül a sablonban.
> 
> 

Az alábbi példa állít be egy személyes fiók tárolót, és feltölti a sablont:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Adja meg a SAS-jogkivonat központi telepítése során
A tárfiókban lévő titkos sablon telepítése, a SAS-token létrehozásához, és adja hozzá a sablon az URI azonosító. Állítsa be a lejárati időpont hagyjon elegendő időt a telepítés befejezéséhez.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Például egy SAS-jogkivonat használatával a csatolt sablonokkal, [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>További lépések
* Megismerkedhet a sablonok központi telepítése, lásd: [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](resource-group-template-deploy-cli.md).
* Egy teljes parancsfájlt, amely telepít egy sablon, lásd: [sablonparancsfájlt erőforrás-kezelő telepítése](resource-manager-samples-cli-deploy.md)
* Sablon paraméterek megadásához tekintse meg a [sablonok készítése](resource-group-authoring-templates.md#parameters).
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
