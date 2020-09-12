---
title: 'Sikertelen áramkör alaphelyzetbe állítása – ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Ez a cikk segítséget nyújt egy hibás állapotú ExpressRoute-áramkör alaphelyzetbe állításához.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/28/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7df96f34ee408c0a6d26b27adbac7351c9ab937f
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393088"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Állítsa alaphelyzetbe a hibás ExpressRoute-kapcsolatcsoportot

Ha egy ExpressRoute-áramkörön végzett művelet nem fejeződik be sikeresen, az áramkör "sikertelen" állapotba kerülhet. Ez a cikk segítséget nyújt egy sikertelen Azure ExpressRoute-áramkör alaphelyzetbe állításához.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Áramkör alaphelyzetbe állítása

1. Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps).

2. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Válassza ki a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Futtassa a következő parancsokat egy hibás állapotú áramkör alaphelyzetbe állításához:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Az áramkörnek most kifogástalannak kell lennie. Nyisson meg egy támogatási jegyet a [Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) szolgálatával, ha az áramkör még sikertelen állapotban van.

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákat tapasztal, nyisson meg egy támogatási jegyet a [Microsoft ügyfélszolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
