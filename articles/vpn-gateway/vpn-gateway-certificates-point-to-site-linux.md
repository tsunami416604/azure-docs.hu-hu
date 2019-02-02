---
title: 'Tanúsítványok létrehozása és exportálása pont – hely számára: Linux: CLI: Azure | Microsoft Docs'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot és a Linux (strongSwan) parancssori felület használatával ügyféltanúsítványokat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: cherylmc
ms.openlocfilehash: b673be47d4951adab08f04efc56410095f549356
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564790"
---
# <a name="generate-and-export-certificates"></a>Tanúsítványok létrehozása és exportálása

Pont – hely kapcsolatok tanúsítványok segítségével hitelesíti. Ez a cikk bemutatja, hogyan hozhat létre egy önaláírt főtanúsítványt, és ügyféltanúsítványokat strongSwan és Linux parancssori felület használatával. Ha ugyanazt a tanúsítványt utasításokat keres, tekintse meg a [Powershell](vpn-gateway-certificates-point-to-site.md) vagy [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) cikkeket. StrongSwan a grafikus felhasználói felület helyett a CLI telepítésével kapcsolatos információkért lásd: a lépések a [ügyfél-konfiguráció](point-to-site-vpn-client-configuration-azure-cert.md#install) cikk.

## <a name="generate-and-export"></a>Létrehozása és exportálása
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>További lépések

A pont – hely konfigurációt, folytassa [létrehozása és telepítése VPN-ügyfél konfigurációs fájljainak](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
