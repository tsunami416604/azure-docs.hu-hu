---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc7367ca2162dde9a266341f0d7b3dd1e8b0da81
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313693"
---
Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat. További információt a bejelentkezésről az [Azure CLI 2.0-s verzió használatának első lépéseit](/cli/azure/get-started-with-azure-cli) ismertető cikkben talál.

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