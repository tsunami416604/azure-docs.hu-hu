---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77133602"
---
Nyissa meg a PowerShell-konzolemelt jogosultságokkal.

Ha az Azure PowerShellt helyileg futtatja, csatlakozzon az Azure-fiókjához. A *Connect-AzAccount* parancsmag hitelesítő adatokat kér. A hitelesítés után letölti a fiókbeállításokat, hogy azok elérhetők legyenek az Azure PowerShell számára. Ha ehelyett az Azure Cloud Shellt használja, nem kell futtatnia a *Connect-AzAccount szolgáltatást.* Az Azure Cloud Shell automatikusan csatlakozik az Azure-fiókjához.

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