---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323753"
---
Használhat egy legfelső szintű tanúsítványt, amely egy vállalati megoldás (ajánlott) lett létrehozva, vagy létrehozhat egy önaláírt tanúsítványt. Miután létrehozta a főtanúsítványt, exportálja a nyilvános tanúsítványadatokat (ne a titkos kulcsot), a Base64 kódolású X.509 .cer fájlba. Ezután töltse fel a nyilvános tanúsítványadatokat az Azure-kiszolgálóhoz.

* **Vállalati tanúsítvány:** Ha egy vállalati megoldás használata esetén használhatja meglévő tanúsítványláncát. Szerzik be a használni kívánt főtanúsítványhoz tartozó .cer fájlt.
* **Önaláírt főtanúsítvány:** Ha nem vállalati tanúsítványmegoldást használ, hozzon létre egy önaláírt főtanúsítványt. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és az ügyfelek egy kapcsolat hibaüzenetet fog kapni, amikor megpróbálnak csatlakozni. Használhatja az Azure PowerShellt, a MakeCertet vagy az OpenSSL-t. A ezekben a cikkekben ismertetett lépések bemutatják, hogyan kompatibilis önaláírt tanúsítványt létrehozni:

  * [A Windows 10 Powershellre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Ezekhez az utasításokhoz Windows 10-es és a PowerShell is létrehozhat tanúsítványokat. A főtanúsítványból létrehozott ügyféltanúsítványok bármely támogatott P2S-ügyfélen telepíthetőek.
  * [Útmutatás a MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Ha nem rendelkezik hozzáféréssel Windows 10 rendszerű számítógéphez használatával is létrehozhat tanúsítványokat, használja a Makecertet. Bár a MakeCert elavult, továbbra is használhatja azt is létrehozhat tanúsítványokat. A főtanúsítványból létrehozott ügyféltanúsítványok bármely támogatott P2S-ügyfélen is telepíthető.
  * [Linuxra vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
