---
title: "P2S ügyféltanúsítvány telepítése |} Azure"
description: "Ez a cikk segítséget nyújt egy ügyfél-tanúsítvány P2S-tanúsítvány hitelesítése a telepítéséhez."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2017
ms.author: cherylmc
ms.openlocfilehash: fc0cc37794ef291c9d27b094211b38cec90da55c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Pont-pont Azure tanúsítvány hitelesítési kapcsolatok ügyfél tanúsítvány telepítése

Pont-pont Azure-alapú hitelesítést használó virtuális hálózathoz csatlakozó összes ügyfelek ügyfél-tanúsítvány kérésére. Ez a cikk segítséget nyújt a hitelesítéshez egy Vnetet P2S használatával történő csatlakozás során használt ügyfél-tanúsítvány telepítéséhez.

## <a name="generate"></a>Létrehozása és ügyfél-tanúsítvány exportálása

Ügyfél-tanúsítvány generálása egy legfelső szintű tanúsítványt egy vállalati hitelesítésszolgáltató megoldás használatával lett létrehozva, vagy önaláírt legfelső szintű tanúsítvány. Tekintse meg a [PowerShell](vpn-gateway-certificates-point-to-site.md) vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) lépések. Ügyféltanúsítványok generálása, után exportálhatja őket .pfx-fájlként. Győződjön meg arról, hogy tartalmazza a teljes tanúsítványlánc exportálásakor.

## <a name="installwin"></a>Tanúsítvány telepítése Windows ügyfelek

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Tanúsítvány telepítése Mac rendszerű ügyfelek

A Resource Manager üzembe helyezési modellel csak Mac VPN-ügyfelek esetén támogatottak. A klasszikus üzembe helyezési modell azok nem támogatottak.

> [!NOTE]
>  Az IKEv2 jelenleg előzetes verzióban érhető el.
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Következő lépések

Folytassa a pont-hely konfigurációs lépéseket.

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)