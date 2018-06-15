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
ms.openlocfilehash: a72f5f5a1d8c64b347185827906f15d3cb9f55ad
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197856"
---
1. Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/#login) paranccsal, és kövesse a képernyőn látható utasításokat. További információt a bejelentkezésről az [Azure CLI 2.0-s verzió használatának első lépéseit](/cli/azure/get-started-with-azure-cli) ismertető cikkben talál.

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