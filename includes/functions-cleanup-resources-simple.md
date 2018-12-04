---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f953c7ae1bba774a69bffddb148c93609c9a85c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856658"
---
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ebben a cikkben létrehozott erőforrásokat használatának befejezése után a következő paranccsal törölheti őket az előfizetésből:

```azurecli-interactive
az group delete --name myResourceGroup
```

Amikor a rendszer kéri, írja be a `y` parancsot. Ezzel a paranccsal eltávolítható az `myResourceGroup` erőforráscsoportot és az ahhoz tartozó erőforrásokat.