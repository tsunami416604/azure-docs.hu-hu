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
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121206"
---
1. Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/) paranccsal, és kövesse a képernyőn látható utasításokat. További információt a bejelentkezésről az [Azure CLI használatának első lépéseit](/cli/azure/get-started-with-azure-cli) ismertető cikkben talál.

   ```azurecli
   az login
   ```
2. Ha több Azure-előfizetéssel rendelkezik, sorolja fel a fiókhoz tartozó előfizetéseket.

   ```azurecli
   az account list --all
   ```
3. Válassza ki a használni kívánt előfizetést.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
