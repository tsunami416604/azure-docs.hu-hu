---
title: 'Azure VPN Gateway OpenVPN konfigurálása: PowerShell |} A Microsoft Docs'
description: Az Azure VPN Gateway OpenVPN konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: cherylmc
ms.openlocfilehash: 958f4f46ec6ba407df7c739b7c62aa1489458485
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408276"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>OpenVPN konfigurálása az Azure pont – hely VPN Gateway (előzetes verzió)

Ez a cikk segít az Azure VPN Gateway OpenVPN beállítása. A cikk feltételezi, hogy már a pont – hely munkakörnyezet. Ha nem, kövesse az utasításokat az 1. lépésben a pont – hely VPN létrehozása.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register"></a>Ez a funkció regisztrálása

Kattintson a **TryIt** az ezeket a lépéseket Azure Cloud Shell használatával könnyedén Ez a funkció regisztrálása.

>[!NOTE]
>Ha ez a funkció nem regisztrál, nem kell tudni használni azt.
>

Kattintás után **TryIt** az Azure Cloud Shell megnyitásához másolja és illessze be a következő parancsokat:

```azurepowershell-interactive
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```
 
```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

Miután regisztrált, regisztrálja újra az előfizetést a Microsoft.Network névtérbe állapota a szolgáltatást.

```azurepowershell-interactive
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. Pont – hely VPN létrehozása

Ha még nem rendelkezik egy működő pont – hely környezetben, útmutatást követve hozzon létre egyet. Lásd: [hozzon létre egy pont – hely VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) létrehozása és pont – hely VPN-átjáró konfigurálása az Azure natív tanúsítványalapú hitelesítésének.

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