---
title: Hozzon létre egy Azure Media Services-fiók – Azure CLI-vel |} A Microsoft Docs
description: Az Azure CLI-példaszkript egy Azure Media Services-fiók létrehozásához használja.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 7e668852346de1b48bd34658dea001435ba8a625
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842213"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>CLI-példa: Azure Media Services-fiók létrehozása

A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan hozhat létre Azure Media Services-fiókot. 

A Media Services-fiók és a társított tárfiókok az Azure-előfizetéshez kell lennie. Javasoljuk, hogy a Media Services-fiók ugyanazon a helyen a storage-fiókok használatához.

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Létrehoz egy Media Services-fiókot. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Létrehoz egy jelszóval védett szolgáltatásnevet, és konfigurálja annak hozzáférését az Azure Media Services-fiókhoz. 
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |


## <a name="next-steps"></a>További lépések

További példákért tekintse meg az [Azure CLI-minták](../cli-samples.md) című dokumentumot.
