---
title: 'Egyéni IPsec-házirendek létrehozása és beállítása pont – hely kapcsolatokhoz: PowerShell'
titleSuffix: Azure VPN Gateway
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és állíthat be egyéni IPSec-házirendeket VPN Gateway P2S-konfigurációkhoz
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: 852ff5e6ad847ff33f8e32b4c3d9f8b325b80716
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664517"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site"></a>Egyéni IPsec-házirendek létrehozása és beállítása pont – hely kapcsolatokhoz

Ha a környezethez Egyéni IPsec-házirend szükséges a titkosításhoz, egyszerűen konfigurálhat egy házirend-objektumot a szükséges beállításokkal. Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni házirend-objektumot, majd hogyan állíthatja be a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

### <a name="prerequisites"></a>Előfeltételek

Ellenőrizze, hogy a környezet megfelel-e az alábbi előfeltételeknek:

* Már konfigurálva van egy működő pont – hely típusú VPN. Ha nem, konfigurálja az egyiket a **pont – hely típusú VPN létrehozása**  a [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)vagy a [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)használatával című témakör lépéseit követve.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. változók beállítása

Deklarálja a használni kívánt változókat. Használja az alábbi mintát, és szükség esetén cserélje le a saját értékeit. Ha a gyakorlat során bármikor lezárta a PowerShell-vagy Cloud Shell-munkamenetet, csak másolja ki és illessze be újra az értékeket a változók újbóli bejelentéséhez.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. házirend-objektum létrehozása

Hozzon létre egy egyéni IPsec-házirend objektumot. Az értékeket beállíthatja úgy, hogy megfeleljenek a szükséges feltételeknek.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. az átjáró frissítése és a házirend beállítása

Ebben a lépésben frissítse a meglévő P2S VPN-átjárót, és állítsa be az IPsec-házirendet.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>Következő lépések

További információ a P2S-konfigurációkról: [Tudnivalók a pont – hely VPN-ről](point-to-site-about.md).