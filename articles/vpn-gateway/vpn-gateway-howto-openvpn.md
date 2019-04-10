---
title: 'Hogyan OpenVPN konfigurálása Azure VPN Gateway átjárón: PowerShell| Microsoft Docs'
description: Az Azure VPN Gateway OpenVPN konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8cc2a6d4ad06bf4a55d4ef078970823df1e0d910
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281964"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>OpenVPN konfigurálása az Azure pont – hely VPN Gateway (előzetes verzió)

Ez a cikk segít beállítani **OpenVPN® protokoll** Azure VPN gatewayen. A cikk feltételezi, hogy már a pont – hely munkakörnyezet. Ha nem, kövesse az utasításokat az 1. lépésben a pont – hely VPN létrehozása.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="register"></a>A funkció regisztrálása

Kattintson a **TryIt** az ezeket a lépéseket Azure Cloud Shell használatával könnyedén Ez a funkció regisztrálása.

>[!NOTE]
>Ha ez a funkció nem regisztrál, nem kell tudni használni azt.
>

Miután a **Kipróbálás** gombra kattintva megnyílik az Azure Cloud Shell, másolja és illessze be a következő parancsokat:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Amikor a funkció már regisztráltként jelenik meg, regisztrálja újra az előfizetést a Microsoft.Network névtérben.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Pont – hely VPN létrehozása

Ha még nem rendelkezik egy működő pont – hely környezetben, útmutatást követve hozzon létre egyet. Lásd: [hozzon létre egy pont – hely VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) létrehozása és pont – hely VPN-átjáró konfigurálása az Azure natív tanúsítványalapú hitelesítésének. 

> [!IMPORTANT]
> Az alapszintű Termékváltozathoz nem támogatott OpenVPN.

## <a name="enable"></a>2. Az átjáró OpenVPN engedélyezése

Az átjáró OpenVPN engedélyezése. Győződjön meg arról, hogy az átjáró már konfigurálva van a pont – hely (IKEv2- vagy SSTP) a következő parancsok futtatása előtt:

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>További lépések

OpenVPN ügyfelek konfigurálása, lásd: [OpenVPN konfigurálása az ügyfelek](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" a OpenVPN Inc. védjegye.**