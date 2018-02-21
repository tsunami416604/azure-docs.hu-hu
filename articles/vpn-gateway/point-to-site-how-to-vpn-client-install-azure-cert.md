---
title: "P2S ügyféltanúsítvány telepítése |} Azure"
description: "P2S tanúsítványhitelesítés Mac vagy Windows ügyfél tanúsítvány telepítéséhez."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: de98201b65f5531f334aded1056f622cecb6e190
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
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

## <a name="next-steps"></a>További lépések

Folytassa a pont-hely konfigurációs lépéseket.

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)