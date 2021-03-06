---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c70e2c166bee14ac58ee88bd18baf0cc61702767
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997925"
---
[!INCLUDE [resource group intro text](resource-group.md)]

A Cloud Shell hozzon létre egy erőforráscsoportot a [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *Nyugat-Európa* helyen. A Linuxon futó, **Alapszintű** App Service-t támogató összes hely megtekintéséhez futtassa az [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) parancsot.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. 

A parancs befejeződésekor a JSON-kimenet megjeleníti az erőforráscsoport tulajdonságait.