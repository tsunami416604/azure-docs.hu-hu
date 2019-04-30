---
title: Az SAS-jogkivonat és az Azure CLI az Azure-sablon üzembe helyezése |} A Microsoft Docs
description: Azure Resource Manager és az Azure CLI használatával helyezheti üzembe az erőforrásokat az Azure-ban, amely a SAS-jogkivonat használatával védett sablon alapján.
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
ms.openlocfilehash: c869b76a0d1ba10bc27aefa60cbe4ed5b8d8201a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061358"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Az SAS-jogkivonat és az Azure CLI saját Resource Manager-sablon üzembe helyezése

Ha a sablon a storage-fiókban található, korlátozza a hozzáférést a sablont, és adjon meg egy közös hozzáférésű jogosultságkód (SAS) üzembe helyezése során. Ez a témakör ismerteti az Azure PowerShell használata a Resource Manager-sablonok az üzembe helyezés során adja meg a SAS-jogkivonatát. 

## <a name="add-private-template-to-storage-account"></a>Saját sablon hozzáadása a storage-fiókba

A sablonok hozzáadása egy tárfiók és a egy SAS-jogkivonat használatával üzembe helyezés során őket mutató hivatkozást.

> [!IMPORTANT]
> Az alábbi lépéseket követve a blob, a sablont tartalmazó könyvtárban elérhető, csak a fiók tulajdonosa. Viszont a BLOB SAS-token létrehozásakor a blob, bárki hozzáférhet az URI-ra. Ha egy másik felhasználó elfogja az URI-t, a felhasználó hozzáférjen a sablon. Egy SAS-jogkivonat használatával korlátozza a hozzáférést a sablonok egy jó módszer, de nem tartalmazhatja bizalmas adatokat – például jelszavakat közvetlenül a sablonban.
> 
> 

Az alábbi példa állít be egy személyes fiók tárolót, és feltölti egy sablont:
   
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

### <a name="provide-sas-token-during-deployment"></a>Adja meg a SAS-jogkivonat üzembe helyezés során
A storage-fiókokban privát sablon üzembe helyezéséhez hozzon létre egy SAS-token és a sablon URI. Hagyjon elegendő időt a telepítés befejezéséhez a lejárati idő beállítása.
   
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

Az SAS-jogkivonatot használó kapcsolt sablonokkal rendelkező egy példa: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>További lépések
* Sablonok üzembe helyezése bevezetésért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](resource-group-template-deploy-cli.md).
* Teljes minta parancsfájl, amely üzembe helyezi a sablont, tekintse meg a [parancsfájl üzembe helyezése Resource Manager-sablon](resource-manager-samples-cli-deploy.md)
* A paraméterek meghatározása sablonban, lásd: [sablonok készítése](resource-group-authoring-templates.md#parameters).
