---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67178979"
---
Használjon egy nagyvállalati megoldással (ajánlott) létrehozott főtanúsítványt, vagy állítson be önaláírt tanúsítványt. A főtanúsítvány létrehozása után exportálja a nyilvános tanúsítvány (nem a titkos kulcs) adatkészletét Base64 kódolású X. 509. cer fájlként. Ezután töltse fel a nyilvános tanúsítványokat az Azure-kiszolgálóra.

* **Vállalati tanúsítvány:** Ha vállalati megoldást használ, használhatja a meglévő tanúsítványláncot. Szerezze be a használni kívánt főtanúsítványhoz tartozó. cer fájlt.
* **Önaláírt főtanúsítvány:** Ha nem vállalati Tanúsítványos megoldást használ, hozzon létre egy önaláírt főtanúsítványt. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és az ügyfelek csatlakozási hibaüzenetet kapnak, amikor megpróbálnak csatlakozni. Használhatja az Azure PowerShellt, a MakeCertet vagy az OpenSSL-t. Az alábbi cikkekben ismertetett lépések bemutatják, hogyan hozhatja ki a kompatibilis önaláírt főtanúsítványokat:

  * [A Windows 10 PowerShellre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Ezekhez az utasításokhoz Windows 10 és PowerShell szükséges a tanúsítványok létrehozásához. A főtanúsítványból létrehozott ügyféltanúsítványok bármely támogatott P2S-ügyfélen telepíthetőek.
  * [A MakeCertre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Ha nem rendelkezik hozzáféréssel Windows 10 rendszerű számítógéphez, a Makecert használatával is létrehozhat tanúsítványokat. Bár a MakeCert elavult, továbbra is használhatja tanúsítvány létrehozásához. A főtanúsítványból létrehozott Ügyféltanúsítványok bármelyik támogatott P2S-ügyfélen telepíthetők.
  * [Linuxra vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
