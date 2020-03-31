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
ms.openlocfilehash: f322803d3484b4ec2d5449e19d67d75b35d6d92f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75752083"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Mi a teendő, ha tanúsítványeltérést kapok a tanúsítványhitelesítéssel való csatlakozáskor?

Törölje a jelet **a "Kiszolgáló identitásának ellenőrzése a tanúsítvány ellenőrzésével" jelölőnégyzetből,** vagy adja hozzá a kiszolgáló teljes tartománynát a profil manuális létrehozásakor a **tanúsítvánnyal együtt.** Ezt úgy teheti meg, **hogy a rasphone-ot** egy parancssorból futtatja, és kiválasztja a profilt a legördülő listából.

A kiszolgálóidentitás-érvényesítés megkerülése általában nem ajánlott, de az Azure-tanúsítvány hitelesítése esetén ugyanaz a tanúsítvány használatos a kiszolgáló érvényesítéséhez a VPN-bújtatási protokollban (IKEv2/SSTP) és az EAP protokollban. Mivel a VPN-bújtatási protokoll már érvényesítette a kiszolgálói tanúsítványt és az FQDN-t, felesleges újra ellenőrizni az EAP-ban.

![pontról helyre](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Kiszolgálói tanúsítvány")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Használhatom a saját belső PKI gyökérhitelesítési hitelesítésszolgáltatómat a pont-hely kapcsolat tanúsítványainak létrehozásához?

Igen. Korábban csak önaláírt főtanúsítványt lehetett használni. Továbbra is 20 főtanúsítvány tölthető fel.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Használhatom az Azure Key Vault tanúsítványait?

Nem.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Milyen eszközökkel hozhatok létre tanúsítványokat?

Használhatja a Vállalati nyilvános kulcsú infrastruktúra megoldást (belső nyilvános kulcsú infrastruktúrát), az Azure PowerShellt, a MakeCertet vagy az OpenSSL-t.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Elérhető útmutató a tanúsítvány beállításaihoz és paramétereihez?

* **Belső/vállalati nyilvános kulcsú infrastruktúra:** a lépéseket a [Tanúsítványok előállítása](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert) pontban találja.

* **Azure PowerShell:** a lépéseket az [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) cikkében találja.

* **MakeCert:** a lépéseket a [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) cikkében találja.

* **OpenSSL:** 

    * A tanúsítványok exportálása során ügyeljen arra, hogy a főtanúsítványt Base64 formátumba konvertálja.

    * Ügyféltanúsítvány esetén:

      * Amikor létrehozza a titkos kulcsot, 4096 bites hosszt adjon meg.
      * Amikor létrehozza a tanúsítványt, az *-extensions* paraméter értéke *usr_cert* legyen.
