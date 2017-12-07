---
title: "Azure gyors üzembe helyezés – Tárfiók létrehozása az Azure parancssori felülettel | Microsoft Docs"
description: "Gyorsan megismerheti az új tárfiókok az Azure parancssori felülettel való létrehozásának módját."
services: storage
documentationcenter: na
author: tamram
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
ms.openlocfilehash: 7186c5e2ce94d06b21d95a557e960b82e268cdce
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-using-the-azure-cli"></a>Tárfiók létrehozása az Azure parancssori felülettel

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató részletesen bemutatja, hogyan lehet az Azure CLI használatával Azure Storage-fiókokat létrehozni.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ebben a példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* régióban.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

Ha nem biztos abban, hogy melyik régiót kell megadnia a `--location` paraméterhez, a támogatott régiók listáját az [az account list-locations](/cli/azure/account#list) paranccsal kérheti le.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

## <a name="create-a-general-purpose-standard-storage-account"></a>Általános célú standard szintű tárfiók létrehozása

Több tárfióktípus is elérhető a különféle alkalmazási forgatókönyveknek megfelelően, amelyek mindegyike egy vagy több tárolószolgáltatást (blobot, fájlt, táblát vagy sort) támogat. Az elérhető tárfióktípusokat az alábbi táblázat ismerteti.

|**Tárfiók típusa**|**Általános célú standard**|**Általános célú prémium**|**Blob Storage, a gyakran és a ritkán használt adatok tárolási rétegei**|
|-----|-----|-----|-----|
|**Támogatott szolgáltatások**| Blob, File, Table és Queue szolgáltatás | Blob szolgáltatás | Blob szolgáltatás|
|**Támogatott blobtípusok**|Blokkblobok, lapblobok és hozzáfűző blobok | Lapblobok | Blokkblobok és hozzáfűző blobok|

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

Ha már nincs szüksége az erőforráscsoportjában lévő egyik erőforrásra sem (beleértve a jelen rövid útmutatóban létrehozott tárfiókot is), törölje az erőforráscsoportot az [az group delete](/cli/azure/group#delete) paranccsal.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy erőforráscsoportot és egy általános célú standard szintű tárfiókot hozott létre. Ha szeretne az adatok a tárfiókról és tárfiókra való továbbításával megismerkedni, folytassa a Blob Storage rövid útmutatójával.

> [!div class="nextstepaction"]
> [Objektumok továbbítása Azure Blob-tárolókra és -tárolókról az Azure CLI-vel](../blobs/storage-quickstart-blobs-cli.md)