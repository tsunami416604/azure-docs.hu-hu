---
title: 'Tanúsítványok előállítása és exportálása pont – hely kapcsolatokhoz: Linux: CLI: Azure | Microsoft Docs'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványt a Linux (alapú strongswan) parancssori felület használatával.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: alzam
ms.openlocfilehash: 79840010561746f2885909123012ffb4efbea5f6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990316"
---
# <a name="generate-and-export-certificates"></a>Tanúsítványok előállítása és exportálása

A pont – hely kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ebből a cikkből megtudhatja, hogyan hozhat létre önaláírt főtanúsítványokat, és hogyan hozhat létre ügyféltanúsítványt a linuxos parancssori felület és a alapú strongswan használatával. Ha más tanúsítványokra vonatkozó utasításokat keres, tekintse meg a [PowerShell](vpn-gateway-certificates-point-to-site.md) -vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) -cikkeket. További információ a alapú strongswan a parancssori felület helyett a GUI használatával történő telepítéséről: az [ügyfél](point-to-site-vpn-client-configuration-azure-cert.md#install) -konfigurációval kapcsolatos cikk lépései.

## <a name="generate-and-export"></a>Készítés és exportálás
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>További lépések

Folytassa a pont – hely konfigurációval a VPN- [ügyfél konfigurációs fájljainak létrehozásához és telepítéséhez](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
