---
title: "Alaphelyzetbe állítja a sikertelen Azure ExpressRoute-kapcsolatcsoportot: PowerShell |} Microsoft Docs"
description: "Ez a cikk segít az ExpressRoute-kapcsolatcsoport hibás állapotban lévő új."
documentationcenter: na
services: expressroute
author: anzaman
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 0e017200193de3e4a02275cec3b09c32f1fa5c31
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2017
---
# <a name="reset-a-failed-expressroute-circuit"></a>Alaphelyzetbe állítása sikertelen ExpressRoute-kapcsolatcsoportot

Ha ExpressRoute-kapcsolatcsoportot művelet befejezése sikertelen, a kapcsolatcsoport "sikertelen" állapotban is kísérhet. Ez a cikk segít alaphelyzetbe a hibás Azure ExpressRoute-kapcsolatcsoportot.

## <a name="reset-a-circuit"></a>A kapcsolat alaphelyzetbe állítása

1. Telepítse az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióját. További információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-azurerm-ps).

2. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

  ```powershell
  Login-AzureRmAccount
  ```
3. Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Válassza ki a használni kívánt előfizetést.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Alaphelyzetbe állítja a kapcsolatcsoport hibás állapotban van, a következő parancsok futtatásával:

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

A kapcsolatcsoport megfelelő kell. A támogatási jegy megnyitása [Microsoft támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha a kapcsolatcsoport hibás állapotban van.

## <a name="next-steps"></a>Következő lépések

A támogatási jegy megnyitása [Microsoft támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) Ha továbbra is problémákat tapasztal.
