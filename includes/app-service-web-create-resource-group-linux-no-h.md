---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fab0258b0c0e2f9b31358075a8f7a5be0228a5e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095700"
---
[!INCLUDE [resource group intro text](resource-group.md)]

A Cloud Shellben hozzon létre egy erőforráscsoportot az [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *West Europe* helyen. A Linuxon futó, **Alapszintű** App Service-t támogató összes hely megtekintéséhez futtassa az [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations) parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. 

A parancs befejeződésekor a JSON-kimenet megjeleníti az erőforráscsoport tulajdonságait.