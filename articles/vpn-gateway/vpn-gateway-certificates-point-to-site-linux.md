---
title: 'Tanúsítványok létrehozása és exportálása pontról webhelyre: Linux: CLI'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványokat a Linux (strongSwan) CLI használatával.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779756"
---
# <a name="generate-and-export-certificates"></a>Tanúsítványok létrehozása és exportálása

A helyek közötti kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ez a cikk bemutatja, hogyan hozhat létre egy önaláírt főtanúsítványt, és hozzon létre ügyféltanúsítványokat a Linux CLI és strongSwan használatával. Ha különböző tanúsítványutasításokat keres, olvassa el a [Powershell](vpn-gateway-certificates-point-to-site.md) vagy a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) cikkeket. A strongSwan cli helyett a gui használatával történő telepítéséről az [Ügyfél konfigurációs](point-to-site-vpn-client-configuration-azure-cert.md#install) cikkében található lépésekben olvashat.

## <a name="install-strongswan"></a>Telepítse strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Tanúsítványok létrehozása és exportálása

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>További lépések

Folytassa a pont-hely konfigurációval a [VPN-ügyfél konfigurációs fájljainak létrehozásához és telepítéséhez.](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)
