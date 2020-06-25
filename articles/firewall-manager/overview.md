---
title: Mi az Azure Firewall Manager előzetes verziója?
description: A Azure Firewall Manager funkcióinak megismerése
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 06/11/2020
ms.author: victorh
ms.openlocfilehash: bef948def487e2b60764641e6cf38a3e122e2f87
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84792157"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Mi az Azure Firewall Manager előzetes verziója?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

A Azure Firewall Manager előzetes verziója egy biztonsági felügyeleti szolgáltatás, amely központi biztonsági házirendet és útválasztási felügyeletet biztosít a felhőalapú biztonsági körzetek számára. 

A Firewall Manager két hálózati architektúra-típushoz biztosít biztonsági felügyeletet:

- **Biztonságos virtuális központ**

   Az [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources) egy Microsoft által felügyelt erőforrás, amellyel könnyedén hozhat létre sugaras és küllős architektúrákat. Ha a biztonsági és útválasztási házirendek egy ilyen hubhoz vannak társítva, akkor azt a rendszer *[biztonságos virtuális központként](secured-virtual-hub.md)* említi. 
- **Hub virtuális hálózata**

   Ez egy szabványos Azure-beli virtuális hálózat, amelyet saját maga hozhat létre és kezelhet. Ha a biztonsági szabályzatok egy ilyen hubhoz vannak társítva, akkor azt *hub virtuális hálózatnak*nevezzük. Jelenleg csak Azure Firewall házirend támogatott. A munkaterhelési kiszolgálókat és szolgáltatásokat tartalmazó társ-küllős virtuális hálózatokat is használhatja. Az önálló virtuális hálózatokban olyan tűzfalak is kezelhetők, amelyek nem kapcsolódnak egyetlen küllőhöz sem.

A *biztonságos virtuális központ* és a *hub virtuális hálózati* architektúráinak részletes összehasonlítását lásd: [Mik a Azure Firewall Manager architektúra lehetőségei?](vhubs-and-vnets.md).

![tűzfal – kezelő](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-preview-features"></a>Azure Firewall Manager előzetes verziójának funkciói

A Azure Firewall Manager előzetes verziója a következő funkciókat kínálja:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Központi Azure Firewall üzembe helyezése és konfigurálása

Központilag telepíthet és konfigurálhat több Azure Firewall példányt, amely különböző Azure-régiókat és-előfizetéseket foglal magába. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarchikus házirendek (globális és helyi)

A Azure Firewall Manager előzetes verziójának használatával központilag kezelheti a Azure Firewall szabályzatokat több biztonságos virtuális hubok között. A központi informatikai csapatok globális tűzfal-házirendeket hozhatnak létre a szervezeti szintű tűzfalszabályok a csapatok közötti kikényszerített szabályozásához. A helyileg létrehozott tűzfalszabályok lehetővé teszik a DevOps önkiszolgáló modell használatát a jobb mozgékonyság érdekében.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrált a fokozott biztonságú külső biztonsági szolgáltatásokkal

A Azure Firewallon kívül a külső gyártótól származó biztonsági szolgáltatásokat (SECaaS) is integrálhatja, így további hálózati védelmet biztosíthat a VNet és az ág internetes kapcsolatai számára.

Ez a szolgáltatás csak biztonságos virtuális központú üzemelő példányokkal érhető el.

- VNet az internetre (V2I) forgalom szűrése

   - A kimenő virtuális hálózati forgalom szűrése az Ön által előnyben részesített külső biztonsági szolgáltatóval.
   - Az Azure-on futó Felhőbeli számítási feladatokhoz speciális, felhasználó által kompatibilis internetes védelmet használhat.

- Ág – Internet (B2I) forgalom szűrése

   Használja ki az Azure-kapcsolatot és a globális disztribúciót, hogy könnyen hozzá lehessen adni a harmadik féltől származó szűrést a fiókirodához az internetes forgatókönyvekhez.

További információ a megbízható biztonsági szolgáltatókról: [Mi a Azure Firewall Manager megbízható biztonsági partnerei (előzetes verzió)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Központosított útválasztás kezelése

A biztonságos hubhoz irányíthatja át a forgalmat a szűréshez és a naplózáshoz anélkül, hogy manuálisan be kellene állítania a felhasználó által megadott útvonalakat (UDR) a küllős virtuális hálózatokon. 

Ez a szolgáltatás csak biztonságos virtuális központú üzemelő példányokkal érhető el.

Harmadik féltől származó szolgáltatókat is használhat a fiókirodák internetes (B2I) adatforgalmának szűrésére, egymás mellett Azure Firewall a ágat a VNet (B2V), a VNet VNet (V2V) és az internetre (VNET). Külső szolgáltatókat is használhat a V2I, ha a B2V vagy a V2V esetében nem szükséges a Azure Firewall. 

## <a name="region-availability"></a>Régiónkénti elérhetőség

A Azure Firewall házirendek a régiók között is használhatók. Létrehozhat például egy szabályzatot az USA nyugati régiójában, és használhatja azt az USA keleti régiójában. 

## <a name="known-issues"></a>Ismert problémák

A Azure Firewall Manager előzetes verziója a következő ismert problémákkal rendelkezik:

|Probléma  |Leírás  |Kezelés  |
|---------|---------|---------|
|Harmadik féltől származó szűrési korlátozások.|A V2I külső szolgáltatókkal való kiszűrése nem támogatott a Azure Firewall B2V és a V2V.|Kivizsgálás|
|A forgalom felosztása jelenleg nem támogatott.|Az Office 365 és az Azure nyilvános Pásti forgalom felosztása jelenleg nem támogatott. Így például a V2I vagy a B2I harmadik féltől származó szolgáltatójának kiválasztásával az összes Azure nyilvános és az Office 365-alapú forgalmat a partneri szolgáltatáson keresztül küldi el.|A forgalom felosztásának kivizsgálása a központban.
|Egy biztonságos virtuális központ régiónként.|Régiónként legfeljebb egy biztonságos virtuális központ lehet.|Több virtuális WAN-t hozhat létre egy régióban.|
|Az alapházirendeknek ugyanabban a régióban kell lenniük, mint a helyi házirendnek.|Hozza létre az összes helyi házirendet az alapházirenddel azonos régióban. Továbbra is alkalmazhat egy olyan házirendet, amely egy másik régióban található biztonságos központ egyik régiójában lett létrehozva.|Kivizsgálás|
|A csomópontok közötti kommunikáció nem működik a biztonságos virtuális hubhoz|A biztonságos virtuális központ védett virtuális hubhoz való kommunikációja még nem támogatott.|Kivizsgálás|
|Az azonos virtuális WAN-t megosztó biztonságos virtuális huboknak ugyanabban az erőforráscsoporthoz kell tartoznia.|Ez a viselkedés a virtuális WAN-Hubokkal van összhangban ma.|Hozzon létre több virtuális WAN-t, amely lehetővé teszi, hogy a biztonságos virtuális hubok különböző erőforráscsoportok jöjjenek létre.|
|Az IP-csoportok nem támogatottak a tűzfal házirendjében.|Az IP-csoportok nyilvános előzetes verzióban érhetők el, és jelenleg csak a hagyományos tűzfalszabályok támogatottak.|Javítás folyamatban.
|A Cloud Solution Provider (CSP) előfizetések nem támogatottak.|A [CSP-előfizetések](https://azure.microsoft.com/offers/ms-azr-0145p/) jelenleg nem támogatottak.|Kivizsgálás

## <a name="next-steps"></a>További lépések

- Tekintse át a [Azure Firewall Manager előzetes telepítésének áttekintését](deployment-overview.md)
- A [biztonságos virtuális hubok](secured-virtual-hub.md)megismerése.