---
title: Nyilvános hitelesítésszolgáltató tanúsítványokat P2S átjárók való áttérés |} Az Azure VPN Gateway |} A Microsoft Docs
description: Ez a cikk segít sikeres átállás alapjai P2S átjárók új nyilvános hitelesítésszolgáltató tanúsítványokat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: cherylmc
ms.openlocfilehash: 8d5dca65734640dc9e756f9130e6b362178781f2
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453514"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>Váltás nyilvános hitelesítésszolgáltatói átjárótanúsítványra a P2S-kapcsolatok esetében

Az Azure VPN Gateway már nem Azure-szintű önaláírt tanúsítványokat állít a P2S-kapcsolatokkal-átjárókat. Kiállított tanúsítványok most már aláírt szerint egy nyilvános hitelesítésszolgáltató (CA). Azonban a régebbi átjárók némelyike előfordulhat, hogy továbbra is az önaláírt tanúsítványokat. Önaláírt tanúsítványok közelében a lejárati dátumnak és nyilvános hitelesítésszolgáltató tanúsítványokat kell átmenet.

>[!NOTE]
> Az Azure-szintű tanúsítvány módosítása nem érinti a P2S ügyfél-hitelesítéshez használt önaláírt tanúsítványokat. Továbbra is ki, önaláírt tanúsítványokat használni a szokásos módon.
>

Ebben a környezetben a tanúsítványokat egy további Azure-szintű tanúsítványt is. Azok nem a tanúsítványláncok a saját önaláírt legfelső szintű tanúsítványok és az ügyfél-hitelesítési tanúsítványok létrehozásakor használt. Ezeknek a tanúsítványoknak érinti, és le fog járni a időpontokban generált őket, ennek a végrehajtására.

Korábban az önaláírt tanúsítvány az átjáró (kiadott a színfalak mögött az Azure-ban) frissíteni kell minden 18 hónap szükséges. VPN-ügyfél konfigurációs fájljainak majd kellett jön létre, és áttelepült a P2S-ügyfelekhez. Áthelyezése a CA-tanúsítványok használata esetén nem ezt a korlátozást. A tanúsítványok váltást Ez a változás is nyújt platform fejlesztései jobb metrikák és továbbfejlesztett stabilitását.

Ez a változás által érintett csak a régebbi átjárók. Ha az átjáró tanúsítványt állítjuk át, kommunikációs vagy bejelentési kap az Azure Portalon. Ellenőrizheti, hogy ha az átjáró érinti ez a cikk lépéseit használva talál.

> [!IMPORTANT]
> Az átállás. március 12 2019 18:00 (UTC) már van ütemezve. Ha inkább egy másik időtartomány hozhat létre egy támogatási esetet. Győződjön meg arról, és a kérés véglegesítése legalább 24 óra.  A következő windows kérhet:
>
> * A február 25 06:00 (UTC)
> * A február 25 18:00 (UTC)
> * Március 1-06:00 (UTC)
> * Március 1-18:00 (UTC)
>
> **Az összes többi átjáró veszi át a március 12 2019 18:00 UTC díjtól**.
>
> Az átjáró áttérési folyamat akár 2 órát vesz igénybe vesz igénybe. Ügyfelek fog kapni egy e-mailt, az átjáró az áttérési folyamat befejezése után.
> 

## <a name="1-verify-your-certificate"></a>1. A tanúsítvány

### <a name="resource-manager"></a>Resource Manager

1. Ellenőrizze, hogy ha a frissítés által érintett. Töltse le az aktuális VPN-ügyfél konfigurációja a lépések használatával [Ez a cikk](point-to-site-vpn-client-configuration-azure-cert.md).

2. Nyissa meg, vagy bontsa ki a zip-fájlt, és keresse meg az "Általános" mappában. Az általános mappában, látni fogja a két fájlt, amelyek közül az egyik *VPNSettings.xml*.
3. Nyissa meg *VPNSettings.xml* bármilyen xml-megjelenítő/szerkesztőben. Az xml-fájlt keressen a következő mezőket:

  * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
  * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. Ha *ServerCertRotCn* és *ServerCertIssuerCn* "DigiCert globális legfelső szintű hitelesítésszolgáltató", a frissítés nem érinti, és folytassa a jelen cikkben ismertetett lépések nem szükséges. Azonban más mutatnak, ha az átjáró tanúsítványa a frissítés részeként és lép érvénybe.

### <a name="classic"></a>Klasszikus

1. Egy ügyfélszámítógépen, keresse meg elérési utat %appdata%/Microsoft/Network/Connections/Cm/<gatewayID>. Átjáróazonosító mappában megtekintheti a tanúsítványt.
2. Az Általános lapon a tanúsítványt ellenőrizze, hogy a kibocsátó hatóság "DigiCert globális legfelső szintű hitelesítésszolgáltató". Ha a kiállító szervezet nem rendelkezik, az átjáró tanúsítványa a frissítés részeként, és lép érvénybe.

## <a name="2-check-certificate-transition-schedule"></a>2. Tanúsítvány átmenet ütemezés ellenőrzése

Ha a tanúsítvány frissítésekor, az átjáró tanúsítványa lép érvénybe. Tekintse meg a **fontos** Megjegyzés az átmenet időpontokat. A frissítés után a P2S-ügyfelekhez nem lehet kapcsolódni a régi profil segítségével. Kell új ügyfél VPN-profilok létrehozásához és telepíteni az ügyfeleken.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. VPN-ügyfél konfigurációs profil létrehozása

Miután a tanúsítvány átvitel töltse le az új VPN-profil (VPN-ügyfél konfigurációs fájljainak) az Azure Portalról. Útmutató: [létrehozása és telepítése VPN-ügyfél konfigurációs fájljainak](point-to-site-vpn-client-configuration-azure-cert.md). Nem kell új ügyféltanúsítványokat.

## <a name="4-deploy-vpn-client-profile"></a>4. Ügyfél VPN-profil központi telepítése

Minden pont – hely VPN-ügyfelek az új profil központi telepítése. A VPN-ügyfelek megszakad a kapcsolat addig, amíg az új VPN-profilt a pont – hely kapcsolatokhoz letöltése és ügyféleszközökre. A VPN-ügyfélszámítógépekhez már telepített ügyféltanúsítványok nem kell újból ki kell.

## <a name="5-connect-the-vpn-client"></a>5. Csatlakozás a VPN-ügyfél

Csatlakozás az Azure-bA a VPN-ügyfél a megszokott módon.
