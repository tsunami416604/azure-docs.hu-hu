---
author: baanders
description: fájl belefoglalása egy alapszintű Azure Digital Twins-példány tisztításához
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 29a07ffa917153c0cb062d34e2807d43d039a373
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494699"
---
Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, a következő lépésekkel törölheti őket.

A [Azure Cloud Shell](https://shell.azure.com)használatával törölheti az erőforráscsoport összes Azure-erőforrását az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) paranccsal. Ezzel eltávolítja az erőforráscsoportot és az Azure Digital Twins-példányt.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

Nyisson meg egy Azure Cloud Shell, és futtassa a következő parancsot az erőforráscsoport és a benne található összes elem törléséhez.

```azurecli-interactive
az group delete --name <your-resource-group>
```