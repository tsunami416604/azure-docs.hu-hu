---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 11/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fab00e5281bb91bce10228b3bc2e9cfd503d5d5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219817"
---
Létre kell hoznia egy virtuális hálózat és átjáró-alhálózatot, először a következő tevékenységek használata előtt.

> [!NOTE]
> Ezek a példák nem vonatkoznak a S2S/ExpressRoute párhuzamos telephelyközi konfigurációkat.
> Átjárók coexist konfigurációban használatáról további információkért lásd: [egyidejű kapcsolatok konfigurálása](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Átjáró hozzáadása

Az átjáró létrehozásához használja az alábbi parancsot. Mindenképpen cserélje le a saját értékeket.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Az átjáró létrehozásának ellenőrzéséhez

Az alábbi parancs segítségével győződjön meg arról, hogy az átjáró létrehozása. A parancs segítségével lekérdezhető az átjáró-azonosító szükséges egyéb műveleteket is.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Az átjáró átméretezése

A következő számú [átjáró-termékváltozatok](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Az alábbi parancs segítségével bármikor módosíthatja az átjáró-Termékváltozat.

> [!IMPORTANT]
> Ez a parancs az UltraPerformance átjáró nem működik. Ha módosítani szeretné az átjárót az UltraPerformance átjáró, először távolítsa el a meglévő ExpressRoute-átjárót, és hozzon létre egy új UltraPerformance átjáró. Alacsonyabb szolgáltatásszintre váltásához az átjáró az UltraPerformance átjáró, először távolítsa el az UltraPerformance átjáró, és hozzon létre egy új átjárót. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása

Használja az alábbi parancsot az átjáró eltávolítása

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
