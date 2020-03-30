---
title: Tanúsítványok létrehozása és exportálása felhasználói VPN-kapcsolatokhoz | Azure Virtual WAN
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványokat a Windows 10 vagy Windows Server 2016 PowerShell használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059914"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Tanúsítványok létrehozása és exportálása felhasználói VPN-kapcsolatokhoz

A felhasználói VPN -kapcsolatok (pont-hely) kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ez a cikk bemutatja, hogyan hozhat létre önaláírt főtanúsítványt, és hogyan hozhat létre ügyféltanúsítványokat a Windows 10 vagy Windows Server 2016 PowerShell használatával.

A cikkben leírt lépéseket Windows 10 vagy Windows Server 2016 rendszert futtató számítógépen kell végrehajtania. A tanúsítványok létrehozásához használt PowerShell-parancsmagok az operációs rendszer részét képezik, és nem működnek a Windows más verzióin. A Windows 10 vagy Windows Server 2016 rendszerű számítógépek csak a tanúsítványok létrehozásához szükségesek. A tanúsítványok létrehozása után feltöltheti őket, vagy telepítheti őket bármely támogatott ügyféloperációs rendszerre.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>További lépések

A [felhasználói VPN-kapcsolat virtuális WAN-lépéseinek folytatása](virtual-wan-about.md)
