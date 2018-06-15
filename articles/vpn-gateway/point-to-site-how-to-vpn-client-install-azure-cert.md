---
title: P2S ügyféltanúsítvány telepítése |} Azure
description: P2S tanúsítványhitelesítés Mac vagy Windows ügyfél tanúsítvány telepítéséhez.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: bf2788fff64ab8b3a5ccf75b8a80f2bd5aba5151
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30317993"
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Pont-pont Azure tanúsítvány hitelesítési kapcsolatok ügyfél tanúsítvány telepítése

Pont-pont Azure-alapú hitelesítést használó virtuális hálózathoz csatlakozó összes ügyfelek ügyfél-tanúsítvány kérésére. Ez a cikk segítséget nyújt a hitelesítéshez egy Vnetet P2S használatával történő csatlakozás során használt ügyfél-tanúsítvány telepítéséhez.

## <a name="generate"></a>Létrehozása és ügyfél-tanúsítvány exportálása

Ügyfél-tanúsítvány generálása egy legfelső szintű tanúsítványt egy vállalati hitelesítésszolgáltató megoldás használatával lett létrehozva, vagy önaláírt legfelső szintű tanúsítvány. Tekintse meg a [PowerShell](vpn-gateway-certificates-point-to-site.md) vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) lépések. Ügyféltanúsítványok generálása, után exportálhatja őket .pfx-fájlként. Győződjön meg arról, hogy tartalmazza a teljes tanúsítványlánc exportálásakor.

## <a name="installwin"></a>Tanúsítvány - Windows telepítése

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Tanúsítvány - Mac telepítése

A Resource Manager üzembe helyezési modellel csak Mac VPN-ügyfelek esetén támogatottak. A klasszikus üzembe helyezési modell azok nem támogatottak.

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>További lépések

Folytassa a pont-hely konfigurációs lépéseket.

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [(Klasszikus) Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
