---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 2ffd8b42bf43658ba488f4c1c92bb7af5b88339e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755788"
---
Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/) paranccsal, és kövesse a képernyőn látható utasításokat. További információt a bejelentkezésről az [Azure CLI használatának első lépéseit](/cli/azure/get-started-with-azure-cli) ismertető cikkben talál.

```azurecli
az login
```

Ha több Azure-előfizetéssel rendelkezik, sorolja fel a fiókhoz tartozó előfizetéseket.

```azurecli
az account list --all
```

Válassza ki a használni kívánt előfizetést.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
