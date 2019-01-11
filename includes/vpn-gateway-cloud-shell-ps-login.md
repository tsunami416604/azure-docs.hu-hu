---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201482"
---
Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal.



Ha helyileg futtatja az Azure PowerShell, csatlakozzon az Azure-fiókjával. A *Connect-AzureRmAccount* parancsmag hitelesítő adatokat kér. Miután hitelesítése, letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell-lel. Ha helyileg nem futtatja a Powershellt, és helyette használja az Azure Cloud Shell "próbálja ki: a böngészőben, hagyja ki az első lépéshez. Fog csatlakozni az Azure-fiókja automatikusan.

```azurepowershell
Connect-AzureRmAccount
```

Ha több előfizetéssel rendelkezik, az Azure-előfizetések listájának beolvasása.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Válassza ki a használni kívánt előfizetést.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```