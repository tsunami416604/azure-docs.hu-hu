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
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410673"
---
 Az Azure Cloud Shellt használja, ha bejelentkezik az Azure-fiókhoz automatikusan "próbálja ki: kattintás után. Jelentkezzen be a helyi, nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és futtassa a parancsmagot való csatlakozáshoz.

```azurepowershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, az Azure-előfizetések listájának beolvasása.

```azurepowershell-interactive
Get-AzSubscription
```

Válassza ki a használni kívánt előfizetést.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```