---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77133602"
---
Emelt szintű jogosultságokkal nyissa meg a PowerShell-konzolt.

Ha Azure PowerShell helyileg futtatja, kapcsolódjon az Azure-fiókjához. A *AzAccount* parancsmag hitelesítő adatokat kér. A hitelesítés után letölti a fiók beállításait, hogy elérhetők legyenek Azure PowerShell számára. Ha ehelyett Azure Cloud Shell használ, nem kell futtatnia a *AzAccount*. Azure Cloud Shell automatikusan csatlakozik Azure-fiókjához.

```azurepowershell
Connect-AzAccount
```

Ha egynél több előfizetéssel rendelkezik, szerezze be az Azure-előfizetések listáját.

```azurepowershell-interactive
Get-AzSubscription
```

Válassza ki a használni kívánt előfizetést.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```