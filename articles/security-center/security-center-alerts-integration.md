---
title: Veszélyforrások észlelésével kapcsolatos riasztások az Azure Security-termékekből Azure Security Center
description: Ez a témakör azokat az Azure-beli biztonsági termékeket mutatja be, amelyekről a Azure Security Center fenyegetésekkel kapcsolatos riasztásokat jeleníthet meg
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: memildin
ms.openlocfilehash: 16cae819b1714c2b410cfa311a3602e0f4ed968a
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913038"
---
# <a name="threat-detection-alerts-from-azure-waf-and-azure-ddos-protection"></a>Veszélyforrások észlelésével kapcsolatos riasztások az Azure WAF és Azure DDoS Protection

Azure Security Center a következő Azure biztonsági termékek által generált veszélyforrás-észlelési riasztásokat jelenítheti meg és gyűjtheti össze (az egyes termékekhez külön licenc szükséges):

* [Azure-WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure-WAF<a name="azure-waf"></a>

Az Azure Application Gateway egy olyan webalkalmazási tűzfalat (WAF) nyújt, amely központi védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen.

A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. A Application Gateway WAF a 3,0-es alapszintű szabálykészlet alapján, vagy az Open Web Application biztonsági projekt 2.2.9 alapul. A WAF automatikusan frissül az új biztonsági rések elleni védelem érdekében, és nincs szükség további konfigurálásra. A WAF-riasztások adatfolyamként való továbbítása Security Center. A WAF által generált riasztásokkal kapcsolatos további információkért lásd: a [webalkalmazási TŰZFAL CRS-szabályait tartalmazó csoportok és szabályok](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Azure DDoS Protection<a name="azure-ddos"></a>

Az elosztott szolgáltatásmegtagadási (DDoS) támadások könnyen végrehajthatók. Nagyszerű biztonsági szempontot jelentenek, különösen akkor, ha az alkalmazásokat a felhőbe helyezi át. 

A DDoS-támadások megpróbálják kimeríteni az alkalmazás erőforrásait, elérhetetlenné téve az alkalmazást a jogosult felhasználók számára. A DDoS-támadások az interneten keresztül elérhető végpontokat is megcélozhatja.

Azure DDoS Protection az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva védelmet nyújt a DDoS-támadásokkal szemben. A DDoS Protection különböző szolgáltatási szinteket biztosít. További információ: [Azure DDoS Protection Overview (áttekintés](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)).

A Azure DDoS Protection riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureddos)tekintheti meg.