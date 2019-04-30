---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320148"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Használhatom a saját PKI legfelső szintű hitelesítésszolgáltatómat a pont–hely kapcsolathoz?

Igen. Korábban csak önaláírt főtanúsítványt lehetett használni. Továbbra is 20 főtanúsítvány tölthető fel.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Milyen eszközökkel hozhatok létre tanúsítványokat?

Használhatja a Vállalati nyilvános kulcsú infrastruktúra megoldást (belső nyilvános kulcsú infrastruktúrát), az Azure PowerShellt, a MakeCertet vagy az OpenSSL-t.

### <a name="certsettings"></a>Elérhető útmutató a tanúsítvány beállításaihoz és paramétereihez?

* **Belső PKI/vállalati nyilvános kulcsú infrastruktúra megoldás:** Tekintse meg a lépéseket [tanúsítványok előállítása](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Tekintse meg a [Azure PowerShell-lel](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) ismertető cikket.

* **MakeCert:** Tekintse meg a [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) ismertető cikket.

* **OpenSSL:** 

    * A tanúsítványok exportálása során ügyeljen arra, hogy a főtanúsítványt Base64 formátumba konvertálja.

    * Ügyféltanúsítvány esetén:

      * Amikor létrehozza a titkos kulcsot, 4096 bites hosszt adjon meg.
      * Amikor létrehozza a tanúsítványt, az *-extensions* paraméter értéke *usr_cert* legyen.