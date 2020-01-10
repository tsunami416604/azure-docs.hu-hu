---
title: 'Azure VPN Gateway: pont – hely típusú ügyféltanúsítvány telepítése'
description: Ügyféltanúsítvány-alapú P2S-tanúsítvány telepítése – Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: f6399f52229916c9ab3e55ec55ace092d0b73dba
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778277"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Ügyféltanúsítványok telepítése a P2S-tanúsítvány hitelesítési kapcsolataihoz

Az összes olyan ügyfél, amely pont – hely típusú Azure-tanúsítvány hitelesítése használatával csatlakozik egy virtuális hálózathoz, Ügyféltanúsítvány szükséges. Ez a cikk segítséget nyújt a hitelesítéshez használt ügyféltanúsítvány telepítéséhez a P2S használatával történő VNet való csatlakozáskor.

## <a name="generate"></a>Ügyféltanúsítvány beolvasása

Függetlenül attól, hogy melyik ügyfél operációs rendszerhez szeretne csatlakozni, mindig rendelkeznie kell ügyféltanúsítvány-tanúsítvánnyal. Létrehozhat egy ügyféltanúsítványt egy vállalati HITELESÍTÉSSZOLGÁLTATÓI megoldással létrehozott főtanúsítványból vagy egy önaláírt főtanúsítványból. Az ügyféltanúsítványok létrehozásához szükséges lépésekért lásd a [PowerShell](vpn-gateway-certificates-point-to-site.md), a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)vagy a [Linux](vpn-gateway-certificates-point-to-site-linux.md) útmutatását. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>A Mac VPN-ügyfelek csak a Resource Manager-alapú üzemi modellben támogatottak. A klasszikus üzemi modell esetében nem támogatottak.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

A Linux-ügyféltanúsítvány az ügyfél konfigurációjának részeként van telepítve az ügyfélszámítógépen. Útmutatásért lásd: [ügyfél konfigurációja – Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) .

## <a name="next-steps"></a>Következő lépések

Folytassa a pont – hely konfiguráció lépéseit a VPN- [ügyfél konfigurációs fájljainak létrehozásához és telepítéséhez](point-to-site-vpn-client-configuration-azure-cert.md).
