---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520845"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Használhatom a saját belső PKI legfelső szintű HITELESÍTÉSSZOLGÁLTATÓját a pont – hely kapcsolatokhoz tartozó tanúsítványok létrehozásához?

Igen. Korábban csak önaláírt főtanúsítványt lehetett használni. Továbbra is 20 főtanúsítvány tölthető fel.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Használhatok Azure Key Vault tanúsítványokat?

Nem.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Milyen eszközökkel hozhatok létre tanúsítványokat?

Használhatja a Vállalati nyilvános kulcsú infrastruktúra megoldást (belső nyilvános kulcsú infrastruktúrát), az Azure PowerShellt, a MakeCertet vagy az OpenSSL-t.

### <a name="certsettings"></a>Elérhető útmutató a tanúsítvány beállításaihoz és paramétereihez?

* **Belső PKI/nagyvállalati PKI-megoldás:** Lásd a [tanúsítványok létrehozásához](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert)szükséges lépéseket.

* **Azure PowerShell:** A lépésekért tekintse meg a [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) cikket.

* **MakeCert** A lépésekért tekintse meg a [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) című cikket.

* **OpenSSL:** 

    * A tanúsítványok exportálása során ügyeljen arra, hogy a főtanúsítványt Base64 formátumba konvertálja.

    * Ügyféltanúsítvány esetén:

      * Amikor létrehozza a titkos kulcsot, 4096 bites hosszt adjon meg.
      * Amikor létrehozza a tanúsítványt, az *-extensions* paraméter értéke *usr_cert* legyen.
