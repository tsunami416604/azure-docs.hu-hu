---
title: 'Több-bérlős virtuális hálózatok összekötése egy hubhoz: PowerShell'
titleSuffix: Azure Virtual WAN
description: Ez a cikk segítséget nyújt a több-bérlős virtuális hálózatok virtuális hubhoz való összekapcsolásához a PowerShell használatával.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: c49a85c71c9b877be7e143f5caf27dc307fe0c12
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381269"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Több-bérlős virtuális hálózatok összekötése egy virtuális WAN-hubhoz

Ebből a cikkből megtudhatja, hogyan csatlakoztathat egy VNet egy másik bérlőn lévő virtuális hubhoz a virtuális WAN használatával. Ez az architektúra akkor hasznos, ha olyan ügyfél-munkaterhelésekkel rendelkezik, amelyeknek ugyanahhoz a hálózathoz kell csatlakozniuk, de különböző bérlőn vannak. Például az alábbi ábrán látható módon csatlakoztathat egy nem contoso VNet (a távoli bérlőt) egy contoso virtuális hubhoz (a szülő bérlőhöz).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Útválasztási konfiguráció beállítása" :::

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Vegyen fel egy másik bérlőt közreműködőként az Azure-előfizetésében.
* Egy több-bérlős VNet csatlakoztathat egy virtuális hubhoz.

A konfiguráció lépései a Azure Portal és a PowerShell kombinációjának használatával hajthatók végre. A funkció azonban csak a PowerShellben és a CLI-ben érhető el.

## <a name="before-you-begin"></a>Előkészületek

### <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések használatához a következő konfigurációt kell beállítania a környezetben:

* Virtuális WAN és virtuális központ a fölérendelt előfizetésben.
* Egy másik (távoli) bérlőben lévő előfizetésben konfigurált virtuális hálózat.
* Győződjön meg arról, hogy a távoli bérlőben lévő VNet-címtartomány nem fedi át a fölérendelt virtuális hubhoz már csatlakoztatott többi virtuális hálózatok belüli más címtartományt.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Engedélyek hozzárendelése

Ahhoz, hogy a fölérendelt előfizetés a virtuális központtal módosítható és hozzáférhessen a távoli bérlőben lévő virtuális hálózatokhoz, a távoli bérlői előfizetésből hozzá kell rendelnie a fölérendelt előfizetéshez **közreműködő** jogosultságokat.

1. Adja hozzá a **közreműködői** szerepkör-hozzárendelést a fölérendelt fiókhoz (az egyiket a virtuális WAN-hubhoz). Ezt a szerepkört a PowerShell vagy a Azure Portal használatával rendelheti hozzá. A következő lépésekhez tekintse meg a **szerepkör-hozzárendelések hozzáadása és eltávolítása** című cikket:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portál](../role-based-access-control/role-assignments-portal.md)

1. Ezután adja hozzá a távoli bérlői előfizetést és a szülő bérlői előfizetést a PowerShell aktuális munkamenetéhez. Futtassa az alábbi parancsot. Ha bejelentkezett a szülőbe, csak a távoli bérlőhöz tartozó parancsot kell futtatnia.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Ellenőrizze, hogy a szerepkör-hozzárendelés sikeres-e, ha bejelentkezik Azure PowerShell a szülő hitelesítő adataival, és futtatja a következő parancsot:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Ha az engedélyek sikeresen propagálva lettek a szülőnek, és hozzá lettek adva a munkamenethez, a távoli bérlő által birtokolt előfizetés megjelenik a parancs kimenetében.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>A VNet és a hub összekötése

A következő lépésekben váltania kell a két előfizetés kontextusa között, amikor a virtuális hálózatot a virtuális hubhoz kapcsolja. Cserélje le a példában szereplő értékeket a saját környezetének megfelelően.

1. A következő parancs futtatásával győződjön meg arról, hogy a távoli fiók környezetében van:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Hozzon létre egy helyi változót annak a virtuális hálózatnak a metaadatainak tárolására, amelyhez csatlakozni szeretne a központhoz.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Váltson vissza a fölérendelt fiókra.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Csatlakoztatja a VNet a hubhoz.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. Az új kapcsolatokat a PowerShellben vagy a Azure Portal is megtekintheti.

   * **PowerShell:** Ha a kapcsolat sikeresen létrejött, az újonnan létrehozott kapcsolat metaadatai megjelennek a PowerShell-konzolon.
   * **Azure Portal:** Navigáljon a virtuális hubhoz, a **kapcsolat > Virtual Network kapcsolatokhoz**. Megtekintheti a mutatót a kapcsolódási lehetőséghez. A tényleges erőforrás megtekintéséhez szüksége lesz a megfelelő engedélyekre.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Hibaelhárítás

* Ellenőrizze, hogy a metaadatok $remoteban (az előző [szakaszban](#connect)) megfelel-e a Azure Portal információinak.
* Az engedélyeket a távoli bérlői erőforráscsoport IAM beállításaival vagy Azure PowerShell parancsok (Get-AzSubscription) használatával ellenőrizheti.
* Győződjön meg arról, hogy az erőforráscsoportok neve vagy más, a környezetre jellemző változók (például "VirtualHub1" vagy "VirtualNetwork1").

## <a name="next-steps"></a>Következő lépések

A virtuális WAN-ról további információt a következő témakörben talál:

* Virtuális WAN [– Gyakori kérdések](virtual-wan-faq.md)
