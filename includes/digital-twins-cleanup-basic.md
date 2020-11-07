---
author: baanders
description: fájl belefoglalása egy alapszintű Azure Digital Twins-példány tisztításához
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: ab179c5f0c56a7d6fd2ebd41e13d27a832512b54
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358098"
---
Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, a következő lépésekkel törölheti őket.

A [Azure Cloud Shell](https://shell.azure.com)használatával az erőforráscsoport összes Azure-erőforrását törölheti az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) paranccsal. Ez a parancs eltávolítja az erőforráscsoportot és az Azure Digital Twins-példányt.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön.

Nyissa meg Azure Cloud Shell, és futtassa a következő parancsot az erőforráscsoport és a benne található összes elem törléséhez.

```azurecli-interactive
az group delete --name <your-resource-group>
```