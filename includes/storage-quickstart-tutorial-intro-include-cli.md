---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 82b3349afd10b585a10619229a2bc6d849d71524
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246920"
---
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy általános célú tárfiókot. Az általános célú tárfiók mind a négy szolgáltatással (blobok, fájlok, táblák és üzenetsorok) használható. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Tárfiók hitelesítő adatainak megadása

Az Azure CLI az oktatóanyagban szereplő parancsok legtöbbje esetén elkéri a tárfiók hitelesítő adatait. Ennek végrehajtására több lehetőség is rendelkezésre áll, amelyek közül az egyik legegyszerűbb az `AZURE_STORAGE_ACCOUNT` és az `AZURE_STORAGE_ACCESS_KEY` környezeti változó beállítása.

Elsőként jelenítse meg a tárfiókkulcsokat az [az storage account keys list](/cli/azure/storage/account/keys) parancs segítségével:

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Ez után adja meg az `AZURE_STORAGE_ACCOUNT` és `AZURE_STORAGE_ACCESS_KEY` környezeti változókat. Ezt a Bash-felületen az `export` parancs használatával is megteheti:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_ACCESS_KEY="myStorageAccountKey"
```
