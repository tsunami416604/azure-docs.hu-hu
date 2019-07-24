---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444117"
---
## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

A Functions szolgáltatás egy általános célú Azure Storage-fiókot használ a függvények állapotának és egyéb adatainak kezeléséhez. Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy általános célú tárfiókot a létrehozott erőforráscsoportban.

A következő parancsban a `<storage_name>` helyőrző helyett írjon be egy globálisan egyedi tárfióknevét. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
