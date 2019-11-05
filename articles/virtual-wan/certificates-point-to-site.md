---
title: Tanúsítványok létrehozása és exportálása Azure virtuális WAN-felhasználók VPN-kapcsolataihoz | Microsoft Docs
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványt a PowerShell használatával Windows 10 vagy Windows Server 2016 rendszeren.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514913"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>Tanúsítványok létrehozása és exportálása virtuális WAN-felhasználók VPN-kapcsolataihoz

A felhasználói VPN-kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ebből a cikkből megtudhatja, hogyan hozhat létre önaláírt főtanúsítványokat, és hogyan hozhat létre ügyféltanúsítványt a PowerShell használatával Windows 10 vagy Windows Server 2016 rendszeren.

A cikkben ismertetett lépéseket Windows 10 vagy Windows Server 2016 rendszert futtató számítógépen kell végrehajtani. A tanúsítványok létrehozásához használt PowerShell-parancsmagok az operációs rendszer részét képezik, és nem működnek a Windows más verzióin. A Windows 10 vagy Windows Server 2016 rendszerű számítógépeknek csak a tanúsítványok létrehozásához van szükségük. A tanúsítványok létrehozása után feltöltheti őket, vagy telepítheti azokat bármely támogatott ügyféloldali operációs rendszeren.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>További lépések

Folytassa a [VPN-kapcsolat virtuális WAN-kapcsolatainak lépéseit](virtual-wan-about.md)
