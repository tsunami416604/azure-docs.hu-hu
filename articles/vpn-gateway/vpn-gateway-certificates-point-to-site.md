---
title: 'P2S tanúsítványok előállítása és exportálása: PowerShell'
titleSuffix: Azure VPN Gateway
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványt a PowerShell használatával Windows 10 vagy Windows Server 2016 rendszeren.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393565"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Tanúsítványok előállítása és exportálása pont – hely kapcsolatokhoz a PowerShell használatával

A pont – hely kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ebből a cikkből megtudhatja, hogyan hozhat létre önaláírt főtanúsítványokat, és hogyan hozhat létre ügyféltanúsítványt a PowerShell használatával Windows 10 vagy Windows Server 2016 rendszeren. Ha más tanúsítványokra vonatkozó utasításokat keres, tekintse meg a [tanúsítványok – Linux](vpn-gateway-certificates-point-to-site-linux.md) vagy [tanúsítványok – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)című témakört.

A cikkben ismertetett lépéseket Windows 10 vagy Windows Server 2016 rendszert futtató számítógépen kell végrehajtani. A tanúsítványok létrehozásához használt PowerShell-parancsmagok az operációs rendszer részét képezik, és nem működnek a Windows más verzióin. A Windows 10 vagy Windows Server 2016 rendszerű számítógépeknek csak a tanúsítványok létrehozásához van szükségük. A tanúsítványok létrehozása után feltöltheti őket, vagy telepítheti azokat bármely támogatott ügyféloldali operációs rendszeren.

Ha nem fér hozzá a Windows 10 vagy a Windows Server 2016 rendszerű számítógéphez, a [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) használatával tanúsítványokat állíthat elő. A bármelyik módszerrel létrehozott tanúsítványok a [támogatott](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) ügyfél operációs rendszerekre telepíthetők.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install"></a>Exportált ügyféltanúsítvány telepítése

A P2S-kapcsolaton keresztül a VNet csatlakozó összes ügyfélnek helyileg telepítenie kell egy ügyféltanúsítványt.

Az ügyféltanúsítvány telepítéséhez tekintse meg [az Ügyféltanúsítványok telepítése pont – hely kapcsolatokhoz](point-to-site-how-to-vpn-client-install-azure-cert.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Folytassa a pont – hely konfigurációval.

* A **Resource Manager** -alapú üzemi modell lépéseivel kapcsolatban lásd: [P2S konfigurálása natív Azure tanúsítványalapú hitelesítés használatával](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* A **klasszikus** üzemi modell lépéseivel kapcsolatban lásd: [pont – hely VPN-kapcsolat konfigurálása VNet (klasszikus)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).