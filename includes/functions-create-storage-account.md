---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203184"
---
## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

A Functions szolgáltatás egy általános célú Azure Storage-fiókot használ a függvények állapotának és egyéb adatainak kezeléséhez. Az [az storage account create](/cli/azure/storage/account#az-storage-account-create) paranccsal hozzon létre egy általános célú tárfiókot a létrehozott erőforráscsoportban.

A következő parancsban a `<storage_name>` helyőrző helyett írjon be egy globálisan egyedi tárfióknevét. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
