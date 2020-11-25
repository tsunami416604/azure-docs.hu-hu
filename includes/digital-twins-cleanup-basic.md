---
author: baanders
description: fájl belefoglalása egy alapszintű Azure Digital Twins-példány tisztításához
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 4c03ef942896dda63f678018cdd257024cfbb6d4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011299"
---
Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, a következő lépésekkel törölheti őket.

A [Azure Cloud Shell](https://shell.azure.com)használatával az erőforráscsoport összes Azure-erőforrását törölheti az az [Group delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) paranccsal. Ez a parancs eltávolítja az erőforráscsoportot és az Azure Digital Twins-példányt.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön.

Nyissa meg Azure Cloud Shell, és futtassa a következő parancsot az erőforráscsoport és a benne található összes elem törléséhez.

```azurecli-interactive
az group delete --name <your-resource-group>
```