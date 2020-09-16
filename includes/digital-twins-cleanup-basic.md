---
author: baanders
description: fájl belefoglalása az alapszintű Azure Digital Twins-példányok és az alkalmazások regisztrálásának megtisztításához
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: e8f167555434338416381aaf8978a3d551686fb2
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606729"
---
Ha már nincs szüksége az oktatóanyagban létrehozott erőforrásokra, a következő lépésekkel törölheti őket.

A [Azure Cloud Shell](https://shell.azure.com)használatával törölheti az erőforráscsoport összes Azure-erőforrását az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) paranccsal. Ezzel eltávolítja az erőforráscsoportot és az Azure Digital Twins-példányt.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

Nyisson meg egy Azure Cloud Shell, és futtassa a következő parancsot az erőforráscsoport és a benne található összes elem törléséhez.

```azurecli
az group delete --name <your-resource-group>
```

Ezután törölje az ügyfélalkalmazás számára a következő paranccsal létrehozott Azure Active Directory-alkalmazás regisztrációját:

```azurecli
az ad app delete --id <your-application-ID>
```