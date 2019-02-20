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
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418065"
---
Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal.



Ha helyileg futtatja az Azure PowerShell, csatlakozzon az Azure-fiókjával. A *Connect-AzAccount* parancsmag hitelesítő adatokat kér. Miután hitelesítése, letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell-lel. Ha helyileg nem futtatja a Powershellt, és helyette használja az Azure Cloud Shell "próbálja ki: a böngészőben, hagyja ki az első lépéshez. Fog csatlakozni az Azure-fiókja automatikusan.

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