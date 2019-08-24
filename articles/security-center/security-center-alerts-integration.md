---
title: Az Azure biztonsági termékek integrálása a Azure Security Centerban | Microsoft Docs
description: Ez a témakör a Azure Security Center integrált Azure biztonsági termékeket ismerteti.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: beac7d08a973dd8dc7e450840669bfd5687e76ed
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013308"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Az Azure biztonsági termékek integrálása a Azure Security Centerban

A Azure Security Center további Microsoft-licenceket biztosít a következő biztonsági termékekkel való együttműködéshez:

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure-WAF<a name="azure-waf"></a>

Az Azure Application Gateway egy olyan webalkalmazási tűzfalat (WAF) nyújt, amely központi védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen.

A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. A Application Gateway WAF a 3,0-es alapszintű szabálykészlet alapján, vagy az Open Web Application biztonsági projekt 2.2.9 alapul. A WAF automatikusan frissül az új biztonsági rések elleni védelem érdekében, és nincs szükség további konfigurálásra. A WAF-riasztások adatfolyamként való továbbítása Security Center. A WAF által generált riasztásokkal kapcsolatos további információkért lásd: a webalkalmazási [TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS Protection<a name="azure-ddos"></a>

Az elosztott szolgáltatásmegtagadási (DDoS) támadások könnyen végrehajthatók. Nagyszerű biztonsági szempontot jelentenek, különösen akkor, ha az alkalmazásokat a felhőbe helyezi át. 

A DDoS-támadások megpróbálják kimeríteni az alkalmazás erőforrásait, elérhetetlenné téve az alkalmazást a jogosult felhasználók számára. A DDoS-támadások az interneten keresztül elérhető végpontokat is megcélozhatja.

Azure DDoS Protection az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva védelmet nyújt a DDoS-támadásokkal szemben. A DDoS Protection különböző szolgáltatási szinteket biztosít. További információ: [Azure DDoS Protection Overview (áttekintés](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)).

DDoS Protection a standard a következő típusú támadásokat csökkentheti:

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Térfogatos támadás észlelhető**|Ennek a támadásnak a célja, hogy elárasztsa a hálózati réteget, amely jelentős mennyiségű látszólag megbízható adatforgalommal rendelkezik. Ide tartozik az UDP-árvizek, az erősítési árvizek és a hamis csomagokra vonatkozó egyéb áradások. A DDoS Protection standard csökkenti ezeket a lehetséges többgigabájtos támadásokat azáltal, hogy a globális hálózati méretezéssel automatikusan befogadja és súrolja őket.|
|**A protokoll támadása észlelve**|Ezek a támadások nem hozzáférhetővé teszik a célt, ha kihasználják a 3. és a 4. rétegbeli protokollok gyenge pontjait. A szolgáltatás magában foglalja az SYN FLOOD támadásokat, a reflexiós támadásokat és más protokollok elleni támadásokat. A DDoS Protection standard csökkenti ezeket a támadásokat, megkülönbözteti a kártékony és a legitim forgalmat, az ügyféllel való interakcióval és a rosszindulatú forgalom blokkolásával.|
|**Az erőforrás (alkalmazás) rétegbeli támadás észlelhető**|Ezek a támadások a webalkalmazási csomagokat célozzák meg a gazdagépek közötti adatátvitel megzavarásához. A támadások közé tartoznak a HTTP-protokollok megsértése, az SQL-injektálás, a helyközi parancsfájlkezelés és a 7. rétegbeli támadások. Az Azure Application Gateway WAF DDoS Protection standard szintű használatával védekezhet a támadásokkal szemben. Az Azure Marketplace-en elérhetők a harmadik féltől származó WAF ajánlatok is.|
