---
title: 'Tanúsítványok létrehozása és exportálása a P2S-hez: PowerShell'
titleSuffix: Azure VPN Gateway
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványokat a Windows 10 vagy Windows Server 2016 PowerShell használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279337"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Tanúsítványok létrehozása és exportálása a Point-to-Site alkalmazáshoz a PowerShell használatával

A helyek közötti kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ez a cikk bemutatja, hogyan hozhat létre önaláírt főtanúsítványt, és hogyan hozhat létre ügyféltanúsítványokat a Windows 10 vagy Windows Server 2016 PowerShell használatával. Ha különböző tanúsítványutasításokat keres, olvassa el [a Tanúsítványok - Linux](vpn-gateway-certificates-point-to-site-linux.md) vagy Tanúsítványok - [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

A cikkben leírt lépéseket Windows 10 vagy Windows Server 2016 rendszert futtató számítógépen kell végrehajtania. A tanúsítványok létrehozásához használt PowerShell-parancsmagok az operációs rendszer részét képezik, és nem működnek a Windows más verzióin. A Windows 10 vagy Windows Server 2016 rendszerű számítógépek csak a tanúsítványok létrehozásához szükségesek. A tanúsítványok létrehozása után feltöltheti őket, vagy telepítheti őket bármely támogatott ügyféloperációs rendszerre.

Ha nem rendelkezik hozzáféréssel windows 10-es vagy Windows Server 2016-os számítógéphez, a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) segítségével tanúsítványokat hozhat létre. A bármelyik módszerrel létrehozott tanúsítványok bármely [támogatott](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) ügyféloperációs rendszerre telepíthetők.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Exportált ügyféltanúsítvány telepítése

Minden olyan ügyfél, amely P2S-kapcsolaton keresztül csatlakozik a virtuális hálózathoz, helyileg kell telepíteni egy ügyféltanúsítványt.

Ügyféltanúsítvány telepítéséhez olvassa [el A Pont–hely kapcsolatok ügyféltanúsítványának telepítése című témakört.](point-to-site-how-to-vpn-client-install-azure-cert.md)

## <a name="next-steps"></a>További lépések

Folytassa a pont-hely konfigurációval.

* Az **Erőforrás-kezelő** telepítési modelljének [lépéseiről a P2S konfigurálása natív Azure-tanúsítványhitelesítéssel](vpn-gateway-howto-point-to-site-resource-manager-portal.md)című témakörben talál.
* A **klasszikus** telepítési modell lépéseit a [Point-to-Site VPN-kapcsolat konfigurálása virtuális hálózattal (klasszikus) című](vpn-gateway-howto-point-to-site-classic-azure-portal.md)témakörben talál.