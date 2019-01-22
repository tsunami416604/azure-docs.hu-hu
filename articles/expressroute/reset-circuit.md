---
title: 'Sikertelen kapcsolatcsoport - ExpressRoute visszaállítása: PowerShell: Azure | Microsoft Docs'
description: Ez a cikk segít a sikertelen állapotú ExpressRoute-kapcsolatcsoport alaphelyzetbe állítása.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: e94ba2f7f6981355f4736683b891fbdced001d03
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425339"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Sikertelen az ExpressRoute-kapcsolatcsoport alaphelyzetbe állítása

Egy ExpressRoute-kapcsolatcsoport művelet sikeres, ha a kapcsolatcsoport "sikertelen" állapotba előfordulhat, hogy lépjen. Ez a cikk segít sikertelen Azure ExpressRoute-kapcsolatcsoport alaphelyzetbe állítása.

## <a name="reset-a-circuit"></a>Kapcsolatcsoport alaphelyzetbe állítása

1. Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/azurerm/install-azurerm-ps).

2. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```
3. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
4. Válassza ki a használni kívánt előfizetést.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Futtassa a következő parancsokat, amelyek állapota sikertelen kapcsolatcsoport alaphelyzetbe állítása:

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

A kapcsolatcsoport most kifogástalan állapotban kell lennie. Nyisson meg egy támogatási jegyet a [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , ha a kapcsolatcsoport még mindig sikertelen állapotban van.

## <a name="next-steps"></a>További lépések

Nyisson meg egy támogatási jegyet a [a Microsoft támogatási](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha továbbra is problémákat tapasztal.
