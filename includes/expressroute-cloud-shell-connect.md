---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178852"
---
 Ha az Azure Cloud Shellt használja, automatikusan bejelentkezik az Azure-fiókjába, miután a "Próbálja ki" gombra kattintott. A helyi bejelentkezéshez nyissa meg a PowerShell-konzol emelt szintű jogosultságokkal, és futtassa a parancsmag csatlakozni.

```azurepowershell
Connect-AzAccount
```

Ha egynél több előfizetéssel rendelkezik, az Azure-előfizetések listáját.

```azurepowershell-interactive
Get-AzSubscription
```

Válassza ki a használni kívánt előfizetést.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```