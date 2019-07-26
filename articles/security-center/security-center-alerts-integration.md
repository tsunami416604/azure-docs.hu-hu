---
title: Az Azure biztonsági termékek integrálása a Azure Security Centerban | Microsoft Docs
description: Ez a témakör a Azure Security Center integrált Azure biztonsági termékeket mutatja be.
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
ms.openlocfilehash: 847748d1c56221119d8f74a2aee716ee08448e28
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335816"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Az Azure biztonsági termékek integrálása a Azure Security Centerban

A Security Center további Microsoft-licencekkel rendelkező ügyfeleket kínál a megállapításaik bevezetéséhez Security Center és megtekintheti őket konszolidált módon.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure-WAF<a name="azure-waf"></a>

Az Azure Application Gateway egy olyan webalkalmazási tűzfalat (WAF) nyújt, amely központi védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen.

A webalkalmazások egyre inkább a gyakran ismert biztonsági réseket kihasználó rosszindulatú támadásokra irányulnak. A Application Gateway WAF az Open Web Application Security Project (OWASP) alapszintű szabálykészlet (CRS) 3,0-es vagy 2.2.9 alapul. A WAF automatikusan frissül az új biztonsági rések elleni védelem érdekében, és nincs szükség további konfigurálásra. A WAF által generált riasztások a Security Center felé áramlanak. A WAF által generált riasztásokkal kapcsolatos további információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS<a name="azure-ddos"></a>

Az elosztott szolgáltatásmegtagadási (DDoS) támadások könnyen végrehajthatók. Ezért nagy biztonságot jelentenek azok az ügyfelek, akik az alkalmazásaikat a felhőbe helyezik át. 

A DDoS-támadások megpróbálják kimeríteni az alkalmazás erőforrásait, elérhetetlenné téve az alkalmazást a jogosult felhasználók számára. A DDoS-támadások az interneten keresztül elérhető végpontokat is megcélozhatja.

Az Azure DDoS Protection az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva védelmet nyújt a DDoS-támadások ellen. Az Azure DDoS Protection különböző szolgáltatási szinteket biztosít. További információ: [Azure DDoS Protection Overview (áttekintés](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)).

DDoS Protection a standard a következő típusú támadásokat csökkentheti:

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**Térfogatos támadás észlelhető**|Ennek a támadásnak a célja, hogy elárasztsa a hálózati réteget, amely jelentős mennyiségű látszólag megbízható adatforgalommal rendelkezik. Ide tartozik az UDP-árvizek, az erősítési árvizek és a hamis csomagokra vonatkozó egyéb áradások. DDoS Protection standard az Azure globális hálózati skálázásával automatikusan felhasználhatja ezeket a lehetséges több gigabájtos támadásokat.|
|**A protokoll támadása észlelve**|Ezek a támadások nem hozzáférhetővé teszik a célt, mert a 3. rétegbeli és a 4. rétegbeli protokollbeli verem gyengeségét kihasználva. Ez magában foglalja a SYN FLOOD támadásokat, a reflexiós támadásokat és más protokollok elleni támadásokat. A DDoS Protection standard csökkenti ezeket a támadásokat, megkülönbözteti a kártékony és a legitim forgalmat, az ügyféllel való interakcióval és a rosszindulatú forgalom blokkolásával.|
|**Az erőforrás (alkalmazás) rétegbeli támadás észlelhető**|Ezek a támadások a webalkalmazási csomagokat célozzák meg a gazdagépek közötti adatátvitel megzavarásához. A támadások közé tartoznak a HTTP-protokollok megsértése, az SQL-injektálás, a helyközi parancsfájlkezelés és a 7. rétegbeli támadások. Használja az Azure Application Gateway webalkalmazási tűzfalat DDoS Protection standard szintű használatával a támadások elleni védelemhez. Az Azure Marketplace-en a harmadik féltől származó webalkalmazási tűzfalak is elérhetők.|
