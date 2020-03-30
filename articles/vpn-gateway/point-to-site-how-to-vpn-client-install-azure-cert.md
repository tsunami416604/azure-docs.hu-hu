---
title: 'Azure VPN-átjáró: Pont-hely ügyféltanúsítvány telepítése'
description: Telepítse az ügyféltanúsítványt a P2S tanúsítványhitelesítéshez - Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902850"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>A P2S-tanúsítványhitelesítési kapcsolatok ügyféltanúsítványainak telepítése

Minden olyan ügyfél, amely a Point-to-Site Azure tanúsítványhitelesítéshasználatával csatlakozik egy virtuális hálózathoz, ügyféltanúsítványt igényel. Ez a cikk segít a hitelesítéshez használt ügyféltanúsítvány telepítésében, amikor p2S-t használó virtuális hálózathoz csatlakozik.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Ügyféltanúsítvány beszerzése

Függetlenül attól, hogy melyik ügyféloperációs rendszerről szeretne csatlakozni, mindig rendelkeznie kell ügyféltanúsítvánnyal. Az ügyféltanúsítvány t vállalati hitelesítésszolgáltatói megoldással létrehozott főtanúsítványból vagy önaláírt főtanúsítványból hozhat létre. Az ügyféltanúsítvány létrehozásának lépéseit a [PowerShell](vpn-gateway-certificates-point-to-site.md), [a MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)vagy a [Linux](vpn-gateway-certificates-point-to-site-linux.md) utasítások ban találja. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>A Mac VPN-ügyfelek csak az Erőforrás-kezelő telepítési modelljéhez támogatottak. Nem támogatottak a klasszikus üzembe helyezési modellhez.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

A Linux-ügyféltanúsítvány az ügyfélkonfiguráció részeként települ az ügyfélre. Az [utasításokat az Ügyfél konfigurációja - Linux.](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)

## <a name="next-steps"></a>További lépések

Folytassa a [VPN-ügyfél konfigurációs fájlok létrehozásához és telepítéséhez](point-to-site-vpn-client-configuration-azure-cert.md)szükséges pont-hely konfigurációs lépésekkel.
