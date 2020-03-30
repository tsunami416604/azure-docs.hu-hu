---
title: 'Hibás kapcsolat alaphelyzetbe állítása – ExpressRoute: PowerShell: Azure | Microsoft dokumentumok'
description: Ez a cikk segít alaphelyzetbe állítani egy sikertelen állapotú ExpressRoute-áramkört.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: deeb1c65cae7e3a5b42230dbda1ad8efa717ba0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748110"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Állítsa alaphelyzetbe a hibás ExpressRoute-kapcsolatcsoportot

Ha egy ExpressRoute-kapcsolaton végzett művelet nem fejeződik be sikeresen, az áramkör "sikertelen" állapotba léphet. Ez a cikk segít a sikertelen Azure ExpressRoute-kapcsolat alaphelyzetbe állításához.

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
5. A következő parancsokkal állítsa alaphelyzetbe a meghibásodott áramkört:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Az áramkörnek most már egészségesnek kell lennie. Nyisson meg egy támogatási jegyet a [Microsoft támogatásával,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ha az áramkör még mindig sikertelen állapotban van.

## <a name="next-steps"></a>További lépések

Nyisson meg egy támogatási jegyet a [Microsoft támogatási szolgálatával,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ha továbbra is problémákat tapasztal.
