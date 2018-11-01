---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035203"
---
[!INCLUDE [resource group intro text](resource-group.md)]

A Cloud Shellben hozzon létre egy erőforráscsoportot az [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *USA déli középső régiója* helyen. Az **Ingyenes** szintű App Service-t támogató összes hely megtekintéséhez futtassa az [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. 

A parancs befejeződésekor a JSON-kimenet megjeleníti az erőforráscsoport tulajdonságait.