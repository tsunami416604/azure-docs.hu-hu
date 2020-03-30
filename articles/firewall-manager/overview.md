---
title: Mi az Azure Firewall Manager előzetes verzió?
description: További információ az Azure Firewall Manager szolgáltatásairól
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79366274"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Mi az Azure Firewall Manager előzetes verzió?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Az Azure Firewall Manager Preview egy biztonsági felügyeleti szolgáltatás, amely központi biztonsági házirendet és útvonalkezelést biztosít a felhőalapú biztonsági peremterülethez. 

A Firewall Manager két hálózati architektúratípus hoz biztosít biztonsági felügyeletet:

- **biztonságos virtuális elosztó**

   Az [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) a Microsoft által felügyelt erőforrás, amely lehetővé teszi a központi és küllős architektúrák egyszerű létrehozását. Ha ilyen hubhoz biztonsági és útválasztási házirendek vannak társítva, azt *[biztonságos virtuális elosztónak nevezzük.](secured-virtual-hub.md)* 
- **központi virtuális hálózat**

   Ez egy szabványos Azure virtuális hálózat, amelyet saját maga hoz létre és kezel. Ha ilyen hubhoz biztonsági házirendek vannak társítva, azt *központi virtuális hálózatnak nevezzük.* Jelenleg csak az Azure tűzfalszabályzat támogatott. Társklazon virtuális hálózatok, amelyek tartalmazzák a számítási feladatok kiszolgálók és szolgáltatások. A tűzfalakat olyan önálló virtuális hálózatokban is kezelheti, amelyek nem küllőkre néznek.

A *biztonságos virtuális központ* és a központi virtuális *hálózati* architektúrák részletes összehasonlítását [a Mik az Azure Firewall Manager architektúra beállításai.](vhubs-and-vnets.md)

![tűzfal-kezelő](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Az Azure Firewall Manager előzetes verziófunkciói

Az Azure Firewall Manager előzetes verziója a következő funkciókat kínálja:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Központi Azure tűzfal üzembe helyezése és konfigurálása

Központilag üzembe helyezhet és konfigurálhat több Azure Firewall-példányt, amelyek különböző Azure-régiókra és előfizetésekre terjednek ki. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarchikus házirendek (globális és helyi)

Az Azure Firewall Manager előzetes verziójával központilag kezelheti az Azure tűzfal-házirendeket több biztonságos virtuális központban. A központi informatikai csapatok globális tűzfalházirendeket hozhatnak, hogy kikényszerítsék a csoportok közötti, a szervezeti szintű tűzfalházirendeket. A helyileg készített tűzfalházirendek lehetővé teszik a DevOps önkiszolgáló modell ta- jobb agilitás érdekében.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrálva a külső biztonsági szolgáltatással a fejlett biztonság érdekében

Az Azure Firewall mellett külső szolgáltatások (SECaaS) szolgáltatóként integrálhatja a külső biztonsági szolgáltatókat, hogy további hálózati védelmet nyújtson a virtuális hálózat és a fiókinternet-kapcsolatok számára.

Ez a funkció csak biztonságos virtuális központi központi telepítésekkel érhető el.

- Virtuális hálózat –internet (V2I) forgalomszűrés

   - Szűrje a kimenő virtuális hálózati forgalmat az előnyben részesített külső biztonsági szolgáltatóval.
   - Használja ki a fejlett, felhasználóbarát internetvédelmet az Azure-ban futó felhőbeli számítási feladatokhoz.

- Ág-internet (B2I) forgalom szűrés

   Használja ki az Azure-kapcsolatot és a globális terjesztést, és egyszerűen hozzáadhat külső szűrést az internetes forgatókönyvekhez.

A megbízható biztonsági szolgáltatókról a [Mik azok az Azure Firewall Manager megbízható biztonsági partnerek (előzetes verzió)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Központosított útvonalkezelés

Egyszerűen irányíthatja a forgalmat a biztonságos hubra szűréshez és naplózáshoz anélkül, hogy manuálisan kellene beállítania a felhasználó által definiált útvonalakat (UDR) a küllős virtuális hálózatokon. 

Ez a funkció csak biztonságos virtuális központi központi telepítésekkel érhető el.

Használhatja a külső szolgáltatók ág-internet (B2I) forgalom szűrés, egymás mellett az Azure Firewall for Branch a VNet (B2V), vnet a vnet (V2V) és a virtuális hálózat az internetre (V2I). A V2I-forgalom szűréséhez külső szolgáltatókat is használhat, feltéve, hogy az Azure Firewall nem szükséges a B2V vagy a V2V esetén. 

## <a name="region-availability"></a>Régiónkénti elérhetőség

Az Azure tűzfalszabályzatok régiók között használható. Létrehozhat például egy szabályzatot az USA nyugati részén, és használhatja az USA keleti részén. 

## <a name="known-issues"></a>Ismert problémák

Az Azure Firewall Manager előzetes verziója a következő ismert problémákat tartalmaz:

|Probléma  |Leírás  |Kezelés  |
|---------|---------|---------|
|Külső szűrési korlátozások.|V2I forgalom szűrés külső szolgáltatók kal nem támogatott az Azure Firewall B2V és V2V.|Kivizsgálás|
|A forgalom felosztása jelenleg nem támogatott.|Az Office 365 és az Azure Nyilvános PaaS-forgalom felosztása jelenleg nem támogatott. Így a V2I vagy a B2I külső szolgáltató kiválasztása az összes Azure Nyilvános PaaS- és Office 365-forgalmat is elküldi a partnerszolgáltatáson keresztül.|A forgalom felosztásának vizsgálata a központban.
|Régiónként egy biztonságos virtuális elosztó.|Régiónként nem lehet egynél több biztonságos virtuális hub.|Hozzon létre több virtuális WAN-t egy régióban.|
|Az alapházirendeknek ugyanabban a régióban kell lenniük, mint a helyi házirendeknek.|Hozza létre az összes helyi házirendet ugyanabban a régióban, mint az alapházirend. Továbbra is alkalmazhat egy házirendet, amely et egy régióban hoztak létre egy másik régióból származó biztonságos hubon.|Kivizsgálás|
|A csomópontok közötti kommunikáció nem működik a biztonságos virtuális központtal|A biztonságos virtuális központ biztonságos virtuális központ kommunikációja még nem támogatott.|Kivizsgálás|
|Az azonos virtuális WAN-on osztozó összes biztonságos virtuális hubnak ugyanabban az erőforráscsoportban kell lennie.|Ez a viselkedés ma a Virtual WAN Hubs-hoz igazodik.|Hozzon létre több virtuális WAN-t, hogy biztonságos virtuális elosztók jönnek létre a különböző erőforráscsoportokban.|
|Az IP-csoportok nem támogatottak a tűzfalházirendben.|Az IP-csoportok nyilvános előzetes verzióban vannak, és jelenleg csak a hagyományos tűzfalszabályok támogatják.|Fix folyamatban.
|A felhőszolgáltató (CSP) előfizetései nem támogatottak.|A [csp-előfizetések](https://azure.microsoft.com/offers/ms-azr-0145p/) jelenleg nem támogatottak.|Kivizsgálás

## <a name="next-steps"></a>További lépések

- Tekintse át [az Azure Firewall Manager előzetes telepítésének áttekintését – áttekintés](deployment-overview.md)
- További információ a [biztonságos virtuális központokról.](secured-virtual-hub.md)