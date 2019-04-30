---
title: 'Pont – hely ügyfél-tanúsítvány telepítése: Azure | Microsoft Docs'
description: Telepítse az ügyféltanúsítványt a tanúsítvány P2S - hitelesítéséhez Windows, Mac, Linux.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: c278c1c85961fbeb0779cad98f8ac16d4961ba75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679952"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>P2S-tanúsítvánnyal hitelesített kapcsolatokhoz szükséges ügyféltanúsítványok telepítése

Minden virtuális hálózathoz pont – hely Azure tanúsítványalapú hitelesítésének használatával csatlakozó ügyfelek ügyféltanúsítvány szükséges. Ez a cikk segítséget nyújt a P2S használatával virtuális hálózathoz való csatlakozáskor a hitelesítéshez használt ügyféltanúsítvány telepítéséhez.

## <a name="generate"></a>Ügyfél-tanúsítvány beszerzése

Függetlenül attól, milyen ügyfél operációs rendszer szeretne csatlakozni mindig egy ügyfél-tanúsítványt kell rendelkeznie. Létrehozhat egy ügyfél-tanúsítványt egy vállalati Hitelesítésszolgáltatói megoldás használatával létrehozott főtanúsítványt vagy egy önaláírt főtanúsítványt. Tekintse meg a [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md), vagy [Linux](vpn-gateway-certificates-point-to-site-linux.md) lépések ügyféltanúsítvány létrehozásához. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN-ügyfelek csak a Resource Manager üzemi modell esetében támogatottak. Ezek nem támogatottak a klasszikus üzemi modellhez.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

A Linux ügyféltanúsítványt az ügyfél az ügyfél-konfiguráció részeként van telepítve. Lásd: [ügyfél-konfiguráció – Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) útmutatást.

## <a name="next-steps"></a>További lépések

A pont – hely konfiguráció lépésekkel [létrehozása és telepítése VPN-ügyfél konfigurációs fájljainak](point-to-site-vpn-client-configuration-azure-cert.md).