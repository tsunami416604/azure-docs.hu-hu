---
title: A Security Center-integráció az Azure biztonsági termékek |} A Microsoft Docs
description: Ez a témakör bemutatja, amely rendelkezik az Azure Security Center integrált Azure biztonsági termékek.
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
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571737"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>A Security Center az Azure biztonsági termékeket az ASC-integráció

A Security Center biztosít előkészíteni a további Microsoft-licencekkel rendelkező ügyfelek a Security centerhez incidensre, és megtekintheti őket egy konszolidált módon.

* [Azure WAF](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Az Azure WAF <a name="azure-waf"></a>

Az Azure Application Gateway egy olyan webalkalmazási tűzfalat (WAF) nyújt, amely központi védelmet biztosít a webalkalmazások számára a biztonsági rések és az azokat kihasználó támadások ellen.

Webalkalmazások egyre inkább rosszindulatú támadásoknak, amelyek gyakran ismert biztonsági rések kihasználására vonatkozik. Az Application Gateway WAF az alapvető szabály beállítása (CRS) 3.0-s vagy 2.2.9-es verzióinak a a nyílt Web Application Security Project (OWASP) alapul. A WAF automatikusan frissül új biztonsági résekről, ellen, amelyeken nincs szükség további konfigurálásra. WAF által létrehozott riasztásokat a rendszer streamként továbbítja a Security Center. További információ a WAF által létrehozott riasztásokról, ez látható [cikk](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS <a name="azure-ddos"></a>

Az elosztott szolgáltatásmegtagadási (DDoS-) támadások ismert, hogy könnyen hajtható végre. Ezért azok váltak ügyfeleink számára, akik saját alkalmazásait a felhőbe áthelyezni kiváló biztonsági szempont. 

A DDoS-támadások megpróbálják kimeríteni az alkalmazás erőforrásait, elérhetetlenné téve az alkalmazást a jogosult felhasználók számára. DDoS-támadások célba bármely végpont, amely az interneten keresztül érhető el.

Az Azure DDoS protection alkalmazás tervezés – ajánlott eljárások, kombinálva a DDoS-támadásokkal szembeni védelmet nyújtson. Az Azure DDoS protection a különböző szolgáltatásszintek biztosít. További információkért lásd: [Azure DDoS Protection áttekintése](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

A DDoS Protection Standard csökkentheti a következő típusú támadások:

> [!div class="mx-tableFixed"]

|Riasztás|Leírás|
|---|---|
|**-Es támadás észlelve**|A támadás célja, hogy a hálózati réteg látszólag jogos forgalom jelentős mennyiségű kéréssekkel túlterhelheti. UDP árvizek, erősítési árvíz és más megtévesztésre csomag árvíz tartalmazza. A DDoS Protection Standard csökkenti a ezek több gigabájt potenciális támadások elemzésével és tisztítási őket, az Azure globális hálózati skálával automatikusan.|
|**Protokoll támadás észlelve**|Ezeket a támadásokat jelennek meg a cél nem érhető el, egy a 3. rétegbeli a gyengeségét és a réteg 4 protokollvermet kiaknázásával. Ez magában foglalja, külön elárasztó támadások, támadások és más protokollt támadásoknak. A DDoS Protection Standard csökkenti ezeket a támadásokat, rosszindulatú és jogszerű forgalom által az ügyfél használatához, és blokkolja a rosszindulatú forgalom megkülönböztetése.|
|**Erőforrás (alkalmazás) réteg támadás észlelve**|Ezeket a támadásokat a céloznia webes alkalmazás csomagok, a gazdagépek közötti adatátvitel akadályozza. A támadások közé tartozik a HTTP protokoll megsértése, az SQL injektálási, webhelyek közötti szkriptelést és egyéb 7. rétegbeli támadásokat. Az Azure Application Gateway webalkalmazási tűzfal, használja a DDoS Protection Standard, ezek a támadások elleni védelemre. Nincsenek is külső webes alkalmazás tűzfal ajánlatok elérhető az Azure piactéren.|
