---
title: 'Távoli munka a P2S-sel: Azure VPN-átjáró'
description: Ez az oldal bemutatja, hogyan használhatja ki az Azure Bastion-t a covid-19-es világjárvány miatt távolról történő munkavégzés engedélyezéséhez.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: alzam
ms.openlocfilehash: b751817467bd263e8b7c64ccc95ec82ef1579836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337108"
---
# <a name="remote-work-using-azure-vpn-gateway-point-to-site"></a>Távoli munka az Azure VPN-átjáró pont-hely használatával

>[!NOTE]
>Ez a cikk bemutatja, hogyan használhatja ki az Azure VPN-átjárót, az Azure-t, a Microsoft-hálózatot és az Azure-partneri ökoszisztémát a távoli munkavégzéshez és a COVID-19-es válság miatt felmerülő hálózati problémák csökkentéséhez.
>

Ez a cikk ismerteti azokat a lehetőségeket, amelyek a szervezetek számára elérhetőak a felhasználók távoli hozzáférésének beállítására vagy meglévő megoldásaik további kapacitással való kiegészítésére a COVID-19 járvány idején.

Az Azure point-to-site megoldás felhőalapú, és gyorsan kiépíthető, hogy kielégítse a felhasználók megnövekedett otthoni munkavégzési igényét. Könnyen és gyorsan felskálázható, és ugyanolyan könnyen és gyorsan kikapcsolható, ha a megnövekedett kapacitásra már nincs szükség.

## <a name="about-point-to-site-vpn"></a>A pont-hely VPN

A pont–hely (P2S) VPN-átjátókapcsolat lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat létesítéséhez a kapcsolatot az ügyfélszámítógépről kell elindítani. Ez a megoldás olyan távmunkát végzők számára hasznos, akik távoli helyről, például otthonról vagy konferenciáról szeretnének csatlakozni az Azure-virtuális hálózatokhoz vagy a helyszíni adatközpontokhoz. Ez a cikk azt ismerteti, hogy miként engedélyezheti a felhasználók számára, hogy különböző forgatókönyvek alapján távolról dolgozzanak.

Az alábbi táblázat az ügyféloperációs rendszereket és a számukra elérhető hitelesítési beállításokat mutatja be. Hasznos lenne a már használatban lévő ügyféloperációsrendszer alapján kiválasztani a hitelesítési módszert. Válassza például az OpenVPN tanúsítványalapú hitelesítést, ha olyan ügyféloperációs rendszerek keverékével rendelkezik, amelyeknek csatlakozniuk kell. Kérjük, vegye figyelembe, hogy a pont-hely VPN csak útvonalalapú VPN-átjárókon támogatott.

![pontról helyre](./media/working-remotely-support/ostable.png "Operációs rendszer")

## <a name="scenario-1---users-need-access-to-resources-in-azure-only"></a>1. forgatókönyv – A felhasználóknak csak az Azure-ban kell hozzáférnie az erőforrásokhoz

Ebben a forgatókönyvben a távoli felhasználók csak az Azure-ban található erőforrásokhoz kell hozzáférni.

![pontról helyre](./media/working-remotely-support/scenario1.png "Scanario, 1.")

Magas szinten a következő lépésekre van szükség ahhoz, hogy a felhasználók biztonságosan csatlakozhassanak az Azure-erőforrásokhoz:

1. Virtuális hálózati átjáró létrehozása (ha nem létezik)
2. Pont-hely VPN konfigurálása az átjárón
    3. [A tanúsítványhitelesítéshez kövesse ezt a hivatkozást.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw)
    2.  [Az OpenVPN esetében kövesse ezt a linket](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn).
    3.  [Az Azure AD-hitelesítéshez kövesse ezt a hivatkozást.](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-tenant)
    4.  A helyek közötti [kapcsolatok hibaelhárításához kövesse ezt a hivatkozást.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)
3. A VPN-ügyfél konfigurációjának letöltése és terjesztése
4. A tanúsítványok terjesztése (ha a tanúsítványhitelesítés be van jelölve) az ügyfelek között
5. Csatlakozás az Azure VPN-hez

## <a name="scenario-2---users-need-access-to-resources-in-azure-andor-on-prem-resources"></a>2. forgatókönyv – A felhasználóknak hozzáférésre van szükségük az Azure-beli és/vagy a helyszíni erőforrásokhoz

Ebben a forgatókönyvben a távoli felhasználóknak hozzá kell férnie az Azure-ban és a helyszíni adatközpont(ok) erőforrásokhoz.

![pontról helyre](./media/working-remotely-support/scenario2.png "2. példa")

Magas szinten a következő lépésekre van szükség ahhoz, hogy a felhasználók biztonságosan csatlakozhassanak az Azure-erőforrásokhoz:

1. Virtuális hálózati átjáró létrehozása (ha nem létezik)
2. Pont-hely VPN konfigurálása az átjárón (lásd a fenti 1. forgatókönyvet)
3. Helyek közötti bújtatás konfigurálása az Azure virtuális hálózati átjárón, ha a BGP engedélyezve van
4. Helyszíni eszköz konfigurálása az Azure Virtuális hálózati átjáróhoz való csatlakozáshoz
5. Töltse le a pont-webhely profilt az Azure Portalról, és ossza el az ügyfelekközött

[Ezen a linken megtudhatja, hogyan állítható be egy helyek közötti VPN-alagút](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Gyakori kérdések a natív Azure-tanúsítványhitelesítéssel kapcsolatban

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Gyakran feltett kérdések a RADIUS-hitelesítéssel kapcsolatban

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Következő lépések

* [P2S-kapcsolat konfigurálása – Azure AD-hitelesítés](openvpn-azure-ad-tenant.md)

* [P2S-kapcsolat konfigurálása – RADIUS-hitelesítés](point-to-site-how-to-radius-ps.md)

* [P2S-kapcsolat konfigurálása – Azure natív tanúsítványhitelesítése](vpn-gateway-howto-point-to-site-rm-ps.md)

**Az "OpenVPN" az OpenVPN Inc. védjegye.**