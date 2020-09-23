---
title: További veszélyforrások elleni védelem Azure Security Center
description: Ismerje meg az Azure Defenderen túli Azure Security Center elérhető veszélyforrások elleni védelmet
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 0f4a849af2be9f02187dc3cda526c9c4727cab1b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935770"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>További veszélyforrások elleni védelem Azure Security Center
A beépített [Azure Defender-védelem](azure-defender.md)mellett a Azure Security Center a következő veszélyforrások elleni védelmi képességeket is biztosítja.

> [!TIP]
> Az Security Center veszélyforrások elleni védelmi képességeinek engedélyezéséhez engedélyeznie kell az Azure Defender számára a megfelelő munkaterheléseket tartalmazó előfizetést.
>
> Az **Azure Database for MariaDB/MySQL/PostgreSQL** veszélyforrások elleni védelmét csak az erőforrás szintjén engedélyezheti.


## <a name="threat-protection-for-azure-network-layer"></a>Veszélyforrások elleni védelem az Azure hálózati rétegben <a name="network-layer"></a>
Security Center a hálózati rétegbeli elemzések a minta [IPFIX adatokon](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)alapulnak, amelyek az Azure Core-útválasztók által gyűjtött csomagok fejlécei. Ezen adatcsatorna alapján a Security Center gépi tanulási modelleket használ a kártékony forgalmi tevékenységek azonosítására és megjelölésére. A Security Center a Microsoft Threat Intelligence-adatbázist is használja az IP-címek dúsítására.

Bizonyos hálózati konfigurációk korlátozhatják Security Center a gyanús hálózati tevékenységekre vonatkozó riasztások generálását. A hálózati riasztások létrehozásához Security Center a következőket:
- A virtuális gép nyilvános IP-címmel rendelkezik (vagy egy nyilvános IP-címmel rendelkező terheléselosztó).
- A virtuális gép hálózati kimenő forgalmát nem blokkolja külső azonosító megoldás.
- A virtuális gép ugyanazzal az IP-címmel lett hozzárendelve, mint az a teljes óra, amelyben a gyanús kommunikáció történt. Ez a felügyelt szolgáltatás részeként létrehozott virtuális gépekre is vonatkozik (például: AK, Databricks).

Az Azure hálózati réteggel kapcsolatos riasztások listáját a [riasztások hivatkozási táblázata](alerts-reference.md#alerts-azurenetlayer)tartalmazza.


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Veszélyforrások elleni védelem Azure Resource Manager (előzetes verzió)<a name ="management-layer"></a>
A Azure Resource Manager alapján Security Center védelmi réteg jelenleg előzetes verzióban érhető el.

A Security Center egy további védelmi réteget biztosít Azure Resource Manager események használatával, amely az Azure-beli vezérlési síkon tekinthető. A Azure Resource Manager rekordok elemzésével Security Center észleli a szokatlan vagy potenciálisan ártalmas műveleteket az Azure-előfizetési környezetben.

A Azure Resource Manager (előzetes verzió) riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureresourceman)tekintheti meg.


>[!NOTE]
> Az előző elemzések közül több Microsoft Cloud App Security van. Ezen elemzések kihasználása érdekében aktiválni kell egy Cloud App Security licencet. Ha Cloud App Security licenccel rendelkezik, ezek a riasztások alapértelmezés szerint engedélyezve vannak. A riasztások letiltása:
>
> 1. A Security Center menüjében válassza a **díjszabás & beállítások**lehetőséget.
> 1. Válassza ki a módosítani kívánt előfizetést.
> 1. Válassza a **veszélyforrások észlelése**lehetőséget.
> 1. Törölje a **Microsoft Cloud app Security az adataim elérésének engedélyezése**jelölőnégyzet jelölését, majd válassza a **Mentés**lehetőséget.


>[!NOTE]
>Security Center a biztonsággal kapcsolatos ügyféladatokat ugyanabban a földrajzi régióban tárolja, mint az erőforrása. Ha a Microsoft még nem telepített Security Center az erőforrás geo-ban, akkor az a Egyesült Államok tárolja azokat. Ha Cloud App Security engedélyezve van, a rendszer ezeket az adatokat a Cloud App Security földrajzi hely szabályainak megfelelően tárolja. További információkért lásd: [adattároló a nem regionális szolgáltatásokhoz](https://azuredatacentermap.azurewebsites.net/).

1. Állítsa be azt a munkaterületet, amelyre telepíteni kívánja az ügynököt. Győződjön meg arról, hogy a munkaterület ugyanahhoz az előfizetéshez tartozik, amelyet Security Center használ, és hogy rendelkezik írási/olvasási engedéllyel a munkaterületen.

1. Engedélyezze az **Azure Defendert**, majd válassza a **Mentés**lehetőséget.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Veszélyforrások elleni védelem Azure Cosmos DB (előzetes verzió)<a name="cosmos-db"></a>

A Azure Cosmos DB riasztások szokatlan és potenciálisan ártalmas kísérletekkel jönnek létre Azure Cosmos DB fiókok eléréséhez vagy kiaknázásához.

További információkért lásd:

* [A Azure Cosmos DB komplex veszélyforrások elleni védelme (előzetes verzió)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A veszélyforrások elleni védelmi riasztások listája Azure Cosmos DB (előzetes verzió)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Veszélyforrások elleni védelem más Microsoft-szolgáltatások esetében <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Veszélyforrások elleni védelem az Azure WAF <a name="azure-waf"></a>

Az Azure Application Gateway egy olyan webalkalmazási tűzfalat (WAF) nyújt, amely központi védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen.

A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. A Application Gateway WAF a 3,0-es alapszintű szabálykészlet alapján, vagy az Open Web Application biztonsági projekt 2.2.9 alapul. A WAF automatikusan frissül az új biztonsági rések elleni védelem érdekében. 

Ha rendelkezik Azure WAF-licenccel, a WAF-riasztások továbbítása a Security Center, és nincs szükség további konfigurálásra. A WAF által generált riasztásokkal kapcsolatos további információkért lásd: a [webalkalmazási TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Veszélyforrások elleni védelem Azure DDoS Protection <a name="azure-ddos"></a>

Az elosztott szolgáltatásmegtagadási (DDoS) támadások könnyen végrehajthatók. Nagyszerű biztonsági szempontot jelentenek, különösen akkor, ha az alkalmazásokat a felhőbe helyezi át. 

A DDoS-támadás megpróbál kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások az interneten keresztül elérhető végpontokat is megcélozhatja.

A DDoS-támadások elleni védelemhez vásároljon Azure DDoS Protection-licencet, és győződjön meg róla, hogy az alkalmazás kialakításának ajánlott eljárásait követi. A DDoS Protection különböző szolgáltatási szinteket biztosít. További információ: [Azure DDoS Protection Overview (áttekintés](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)).

A Azure DDoS Protection riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureddos)tekintheti meg.


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a veszélyforrások elleni védelmi funkciókkal kapcsolatos biztonsági riasztásokról, tekintse meg a következő cikkeket:

* [Az összes Azure Security Center-riasztás hivatkozási táblázata](alerts-reference.md)
* [Biztonsági riasztások az Azure Security Centerben](security-center-alerts-overview.md)
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Biztonsági riasztások és javaslatok exportálása (előzetes verzió)](continuous-export.md)