---
title: "Azure gyors üzembe helyezés – hozzon létre egy tárfiókot, az Azure parancssori felülettel |} Microsoft Docs"
description: "Gyorsan bemutatják, hogyan hozzon létre egy új tárfiókot az Azure parancssori felület használatával."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/28/2017
ms.author: marsma
ms.openlocfilehash: b1fb2da4acf6e06219d790f2354cada4f1e34285
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Hozzon létre egy tárfiókot, az Azure parancssori felület használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. A gyors üzembe helyezés adatokat egy Azure Storage-fiók létrehozása az Azure parancssori felület használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ez a példa létrehoz egy erőforráscsoportot *myResourceGroup* a a *eastus* régióban.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Ha Ön nem tudja, hogy melyik régióban szeretné megadni a `--location` paraméter, kérheti le a támogatott régiók listáját az előfizetés a [az fiók lista-helyek](/cli/azure/account#list) parancs.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Általános célú szabványos storage-fiók létrehozása

Többféle tárfiókok különböző használati forgatókönyvek, amelyek mindegyike támogatja legalább egy, a tároló szolgáltatást (BLOB, fájlok, táblák vagy várólisták) megfelelő. Az alábbi táblázat részletezi a rendelkezésre álló tár fióktípus.

|**Tárfiók típusa**|**Általános célú standard**|**Általános célú prémium**|**Blob Storage, a gyakran és a ritkán használt adatok tárolási rétegei**|
|-----|-----|-----|-----|
|**Támogatott szolgáltatások**| A BLOB, a fájl, Table, Queue szolgáltatások | Blob service | Blob service|
|**Támogatott blobtípusok**|Blokkblobokat, lapblobokat, hozzáfűző blobokat | Lapblobok | Blokkblobok és hozzáfűző blobok|

Az [az storage account create](/cli/azure/storage/account#create) paranccsal hozzon létre egy általános célú standard szintű tárfiókot.

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforrásokat az erőforráscsoportban, beleértve a gyors üzembe helyezés létrehozott tárfiók törlése az erőforráscsoport a [az csoport törlése](/cli/azure/group#delete) parancsot.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, a létrehozott erőforráscsoport és az általános célú standard szintű tárfiókot. Megtudhatja, hogyan viheti át a korábbi tárfiókban lévő adatokat, továbbra is a Blob storage gyors üzembe helyezés.

> [!div class="nextstepaction"]
> [Átviteli objektumok és az Azure Blob Storage tárolóban az Azure parancssori felület használatával](../blobs/storage-quickstart-blobs-cli.md)