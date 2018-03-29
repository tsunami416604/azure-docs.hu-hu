---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b2a96aad793d956b3ea3de06fba74bcf68e50786
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
Használhat egy vállalati megoldás használatával létrehozott főtanúsítványt (ez az ajánlott módszer), de egy önaláírt tanúsítványt is létrehozhat. A főtanúsítvány létrehozása után exportálja a nyilvános tanúsítványadatokat (ne a titkos kulcsot) egy Base-64 kódolású X.509 .cer fájlba, és töltse fel a nyilvános tanúsítvány adatait az Azure-ba.

* **Vállalati tanúsítvány:** Nagyvállalati megoldás esetén használhatja meglévő tanúsítványláncát. Szerezze be a használni kívánt főtanúsítványhoz tartozó .cer fájlt.
* **Önaláírt főtanúsítvány:** Ha nem vállalati tanúsítványmegoldást használ, létre kell hoznia egy önaláírt főtanúsítványt. Fontos, hogy lépésről lépésre betartsa a P2S-tanúsítványokkal foglalkozó valamelyik alábbi cikk utasításait. Ellenkező esetben a létrehozott tanúsítványok nem lesznek kompatibilisek a P2S-kapcsolatokkal, és az ügyfelek csatlakozási hibákat kapnak majd a kapcsolódás során. Használhatja az Azure PowerShellt, a MakeCertet vagy az OpenSSL-t. A cikkekben ismertetett lépésekkel kompatibilis tanúsítványok hozhatóak létre:

  * [A Windows 10 PowerShellre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Ezekhez az utasításokhoz Windows 10 és PowerShell szükséges a tanúsítványok létrehozásához. A főtanúsítványból létrehozott ügyféltanúsítványok bármely támogatott P2S-ügyfélen telepíthetőek.
  * [A MakeCertre vonatkozó utasítások](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Ha nem rendelkezik hozzáféréssel Windows 10 rendszerű számítógéphez, a Makecert használatával is létrehozhat tanúsítványokat. A MakeCert ugyan elavult, azonban továbbra is hozhat létre vele tanúsítványokat. A főtanúsítványból létrehozott ügyféltanúsítványok bármely támogatott P2S-ügyfélen telepíthetőek.
