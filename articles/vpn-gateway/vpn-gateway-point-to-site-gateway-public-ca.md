---
title: Nyilvános hitelesítésszolgáltató tanúsítványokat P2S átjárók önaláírt való váltás |} Az Azure VPN Gateway |} A Microsoft Docs
description: Ez a cikk segít sikeres átállás alapjai P2S átjárók új nyilvános hitelesítésszolgáltató tanúsítványokat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: ac1ae4125418a9c0b3e9587cd03a44e752ac8f82
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236957"
---
# <a name="transition-from-self-signed-to-public-ca-certificates-for-p2s-gateways"></a>Áttérés a önaláírt a P2S-átjárók nyilvános hitelesítésszolgáltató tanúsítványai

Az Azure VPN Gateway már nem átjárók P2S-kapcsolatok önaláírt tanúsítványokat állít. Kiállított tanúsítványok most már aláírt szerint egy nyilvános hitelesítésszolgáltató (CA). Azonban régebbi átjárók előfordulhat, hogy továbbra is az önaláírt tanúsítványokat. Önaláírt tanúsítványok közelében a lejárati dátumnak és nyilvános hitelesítésszolgáltató tanúsítványokat kell átmenet.

Korábban az önaláírt tanúsítvány az átjáró szükséges minden 18 hónapban frissíteni kell. VPN-ügyfél konfigurációs fájljainak majd kellett jön létre, és áttelepült a P2S-ügyfelekhez. Áthelyezése a CA-tanúsítványok használata esetén nem ezt a korlátozást. A tanúsítványok váltást Ez a változás is nyújt platform fejlesztései jobb metrikák és továbbfejlesztett stabilitását.

Ez a változás által érintett csak a régebbi átjárók. Ha az átjáró tanúsítványt állítjuk át, kommunikációs vagy bejelentési kap az Azure Portalon. Ellenőrizheti, hogy ha az átjáró érinti ez a cikk lépéseit használva talál.

>[!IMPORTANT]
>Az átállás van ütemezve. március 12,2019 kezdőár: 18:00 (UTC). Ha inkább egy másik időtartomány hozhat létre egy támogatási esetet. Győződjön meg arról, és a kérés véglegesítése legalább 24 óra.  A következő windows kérhet:
>
>* A 25 február 06:00 (UTC)
>* A 25 február 18:00 (UTC)
>* Az 1-gyel 06:00 (UTC)
>* Az 1-gyel 18:00 (UTC)
>
>**Az összes többi átjáró veszi át a március 12 2019 18:00 UTC díjtól**.
>

## <a name="1-verify-your-certificate"></a>1. A tanúsítvány

1. Ellenőrizze, hogy ha a frissítés által érintett. Töltse le az aktuális VPN-ügyfél konfigurációja a lépések használatával [Ez a cikk](point-to-site-vpn-client-configuration-azure-cert.md).

2. Nyissa meg, vagy bontsa ki a zip-fájlt, és keresse meg az "Általános" mappában. Az általános mappában, látni fogja a két fájlt, amelyek közül az egyik *VPNSettings.xml*.
3. Nyissa meg *VPNSettings.xml* bármilyen xml-megjelenítő/szerkesztőben. Az xml-fájlt keressen a következő mezőket:

  * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
  * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Ha *ServerCertRotCn* és *ServerCertIssuerCn* "DigiCert globális legfelső szintű hitelesítésszolgáltató", a frissítés nem érinti, és folytassa a jelen cikkben ismertetett lépések nem szükséges. Azonban más mutatnak, ha az átjáró tanúsítványa a frissítés részeként és lép érvénybe.

## <a name="2-check-certificate-transition-schedule"></a>2. Tanúsítvány átmenet ütemezés ellenőrzése

Ha a tanúsítvány frissítésekor, az átjáró tanúsítványa lép érvénybe. Tekintse meg a **fontos** Megjegyzés az átmenet időpontokat. A frissítés után a P2S-ügyfelekhez nem lehet kapcsolódni a régi profil segítségével. Kell új ügyfél VPN-profilok létrehozásához és telepíteni az ügyfeleken.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. VPN-ügyfél konfigurációs profil létrehozása

Miután a tanúsítvány átvitel töltse le az új VPN-profil (VPN-ügyfél konfigurációs fájljainak) az Azure Portalról. Útmutató: [létrehozása és telepítése VPN-ügyfél konfigurációs fájljainak](point-to-site-vpn-client-configuration-azure-cert.md). Nem kell új ügyféltanúsítványokat.

## <a name="4-deploy-vpn-client-profile"></a>4. Ügyfél VPN-profil központi telepítése

Minden pont – hely VPN-ügyfelek az új profil központi telepítése. A VPN-ügyfelek megszakad a kapcsolat addig, amíg az új VPN-profilt a pont – hely kapcsolatokhoz letöltése és ügyféleszközökre. A VPN-ügyfélszámítógépekhez már telepített ügyféltanúsítványok nem kell újból ki kell.

## <a name="5-connect-the-vpn-client"></a>5. Csatlakozás a VPN-ügyfél

Csatlakozás az Azure-bA a VPN-ügyfél a megszokott módon.
