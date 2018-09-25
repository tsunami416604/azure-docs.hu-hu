---
title: 'Azure VPN Gateway OpenVPN konfigurálása: PowerShell |} A Microsoft Docs'
description: Az Azure VPN Gateway OpenVPN konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: b915a56b14332bfa885eaccf9a151d08c58dc5b1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973694"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway-preview"></a>OpenVPN konfigurálása az Azure pont – hely VPN Gateway (előzetes verzió)

Ez a cikk segít az Azure VPN Gateway OpenVPN beállítása. A cikk feltételezi, hogy már a pont – hely munkakörnyezet. Ha nem, kövesse az utasításokat az 1. lépésben a pont – hely VPN létrehozása.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

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