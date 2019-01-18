---
title: 'Hogyan OpenVPN konfigurálása Azure VPN Gateway átjárón: PowerShell| Microsoft Docs'
description: Az Azure VPN Gateway OpenVPN konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: cherylmc
ms.openlocfilehash: 07dae60d1d4ab43194f88f44bde498d9fa19ce81
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388105"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>OpenVPN konfigurálása az Azure pont – hely VPN Gateway (előzetes verzió)

Ez a cikk segít az Azure VPN Gateway OpenVPN beállítása. A cikk feltételezi, hogy már a pont – hely munkakörnyezet. Ha nem, kövesse az utasításokat az 1. lépésben a pont – hely VPN létrehozása.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register"></a>A funkció regisztrálása

Kattintson a **TryIt** az ezeket a lépéseket Azure Cloud Shell használatával könnyedén Ez a funkció regisztrálása.

>[!NOTE]
>Ha ez a funkció nem regisztrál, nem kell tudni használni azt.
>

Miután a **Kipróbálás** gombra kattintva megnyílik az Azure Cloud Shell, másolja és illessze be a következő parancsokat:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Amikor a funkció már regisztráltként jelenik meg, regisztrálja újra az előfizetést a Microsoft.Network névtérben.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Pont – hely VPN létrehozása

Ha még nem rendelkezik egy működő pont – hely környezetben, útmutatást követve hozzon létre egyet. Lásd: [hozzon létre egy pont – hely VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) létrehozása és pont – hely VPN-átjáró konfigurálása az Azure natív tanúsítványalapú hitelesítésének. 

> [!IMPORTANT]
> Az alapszintű Termékváltozathoz nem támogatott OpenVPN.

## <a name="cmdlets"></a>2. PowerShell-parancsmagok telepítése

Telepítse a Resource Manager PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítéséről további információt a [How to install and configure Azure PowerShell](/powershell/azure/overview) (Az Azure PowerShell telepítése és konfigurálása) című témakörben talál. Ez azért fontos, mert a parancsmagok korábbi verziói nem tartalmazzák a feladatok elvégzéséhez szükséges aktuális értékeket.

## <a name="enable"></a>3. Az átjáró OpenVPN engedélyezése

Az átjáró OpenVPN engedélyezése. Győződjön meg arról, hogy az átjáró már konfigurálva van a pont – hely (IKEv2- vagy SSTP) a következő parancsok futtatása előtt:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>További lépések

OpenVPN ügyfelek konfigurálása, lásd: [OpenVPN konfigurálása az ügyfelek](vpn-gateway-howto-openvpn-clients.md).
