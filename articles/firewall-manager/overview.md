---
title: Mi az Azure Firewall Manager előzetes verziója?
description: A Azure Firewall Manager funkcióinak megismerése
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 11/05/2019
ms.author: victorh
ms.openlocfilehash: df649eab1f0e2946078f8efd3cdd6ab68c3b7938
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580189"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Mi az Azure Firewall Manager előzetes verziója?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A Azure Firewall Manager előzetes verziója egy biztonsági felügyeleti szolgáltatás, amely központi biztonsági házirendet és útválasztási felügyeletet biztosít a felhőalapú biztonsági körzetek számára. Az [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources), egy Microsoft által felügyelt erőforrás, amellyel könnyedén hozhat létre hub-és küllős architektúrákat. Ha a biztonsági és útválasztási házirendek egy ilyen hubhoz vannak társítva, akkor azt a rendszer *[biztonságos virtuális központként](secured-virtual-hub.md)* említi. 

![tűzfal – kezelő](media/overview/firewallmanagerv3.png)

## <a name="azure-firewall-manager-preview-features"></a>Azure Firewall Manager előzetes verziójának funkciói

A Azure Firewall Manager előzetes verziója a következő funkciókat kínálja:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Központi Azure Firewall üzembe helyezése és konfigurálása

Központilag telepíthet és konfigurálhat több Azure Firewall példányt, amely különböző Azure-régiókat és-előfizetéseket foglal magába. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarchikus házirendek (globális és helyi)

A Azure Firewall Manager előzetes verziójának használatával központilag kezelheti a Azure Firewall szabályzatokat több biztonságos virtuális hubok között. A központi informatikai csapatok globális tűzfal-házirendeket hozhatnak létre a szervezeti szintű tűzfalszabályok a csapatok közötti kikényszerített szabályozásához. A helyileg létrehozott tűzfalszabályok lehetővé teszik a DevOps önkiszolgáló modell használatát a jobb mozgékonyság érdekében.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrált a fokozott biztonságú külső biztonsági szolgáltatásokkal

A Azure Firewallon kívül a külső gyártótól származó biztonsági szolgáltatásokat (SECaaS) is integrálhatja, így további hálózati védelmet biztosíthat a VNet és az ág internetes kapcsolatai számára.

- VNet az internetre (V2I) forgalom szűrése

   - A kimenő virtuális hálózati forgalom szűrése az Ön által előnyben részesített külső biztonsági szolgáltatóval.
   - Az Azure-on futó Felhőbeli számítási feladatokhoz speciális, felhasználó által kompatibilis internetes védelmet használhat.

- Ág – Internet (B2I) forgalom szűrése

   Használja ki az Azure-kapcsolatot és a globális disztribúciót, hogy könnyen hozzá lehessen adni a harmadik féltől származó szűrést a fiókirodához az internetes forgatókönyvekhez.

További információ a megbízható biztonsági szolgáltatókról: [Mi a Azure Firewall Manager megbízható biztonsági partnerei (előzetes verzió)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Központosított útválasztás kezelése

A biztonságos hubhoz irányíthatja át a forgalmat a szűréshez és a naplózáshoz anélkül, hogy manuálisan be kellene állítania a felhasználó által megadott útvonalakat (UDR) a küllős virtuális hálózatokon. Harmadik féltől származó szolgáltatókat is használhat a fiókirodák internetes (B2I) adatforgalmának szűrésére, egymás mellett Azure Firewall a ágat a VNet (B2V), a VNet VNet (V2V) és az internetre (VNET). Külső szolgáltatókat is használhat a V2I, ha a B2V vagy a V2V esetében nem szükséges a Azure Firewall. 

## <a name="region-availability"></a>Régiónkénti elérhetőség

A nyilvános előzetes verzióban a következő régiók támogatottak:

- Nyugat-Európa, Észak-Európa, Közép-Franciaország, Dél-Franciaország, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója
- Kelet-Ausztrália, Ausztrália középső régiója, Ausztrália középső régiója 2, Délkelet-Ausztrália
- Közép-Kanada
- USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA déli középső régiója, USA 2. nyugati régiója, USA középső régiója, USA északi középső régiója

Azure Firewall szabályzatok csak ezekben a régiókban hozhatók létre, de régiónként is használhatók. Létrehozhat például egy szabályzatot az USA nyugati régiójában, és használhatja azt az USA keleti régiójában. 

## <a name="known-issues"></a>Ismert problémák

A Azure Firewall Manager előzetes verziója a következő ismert problémákkal rendelkezik:

|Probléma  |Leírás  |Kezelés  |
|---------|---------|---------|
|A manuálisan létrehozott központi virtuális hálózatok nem támogatottak|A Azure Firewall Manager jelenleg a virtuális Hubokkal létrehozott hálózatokat támogatja. A saját manuálisan létrehozott hub-VNet használata még nem támogatott.|Egyelőre használja a Azure Firewall Managert a virtuális Hubokkal létrehozott, központi és küllős hálózatokkal.<br>Jelenleg vizsgálja.
|Harmadik féltől származó szűrési korlátozások|A V2I külső szolgáltatókkal való kiszűrése nem támogatott a Azure Firewall B2V és a V2V.|Jelenleg vizsgálja.|
|A forgalom felosztása jelenleg nem támogatott|Az Office 365 és az Azure nyilvános Pásti forgalom felosztása jelenleg nem támogatott. Így például a V2I vagy a B2I harmadik féltől származó szolgáltatójának kiválasztásával az összes Azure nyilvános és az Office 365-alapú forgalmat a partneri szolgáltatáson keresztül küldi el.|Jelenleg vizsgálja a forgalom felosztását a központban.
|Régiónként egy hub|Régiónként legfeljebb egy hub lehet|Több virtuális WAN-t hozhat létre egy régióban.|
|Az alapházirendeknek ugyanabban a régióban kell lenniük, mint a helyi házirendnek.|Hozza létre az összes helyi házirendet az alapházirenddel azonos régióban. Továbbra is alkalmazhat egy olyan házirendet, amely egy másik régióban található biztonságos központ egyik régiójában lett létrehozva.|Jelenleg vizsgálja.|

## <a name="next-steps"></a>További lépések

- Tekintse át a [Azure Firewall Manager előzetes telepítésének áttekintését](deployment-overview.md)
- A [biztonságos virtuális hubok](secured-virtual-hub.md)megismerése.