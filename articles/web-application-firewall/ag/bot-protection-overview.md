---
title: WAF az Azure Application Gateway robotvédelem – áttekintés
titleSuffix: Azure Web Application Firewall
description: Ez a cikk áttekintést nyújt az Application Gateway robotvédelmével foglalkozó webalkalmazás-tűzfalról (WAF)
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027094"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Az Azure Web Application Firewall az Azure Application Gateway robotvédelemen – áttekintés

Nagyjából 20%-a az összes internetes forgalom származik rossz botok. Ők csinál dolog szeret kaparás, átkésés, és látszó részére sebezhetőség -ban -a pókháló alkalmazás. Amikor ezek a botok leállnak a webalkalmazás-tűzfalon (WAF), nem támadhatják meg Önt. Emellett nem használhatják fel az erőforrásokat és a szolgáltatásokat, például a háttérrendszereket és más mögöttes infrastruktúrát.

Engedélyezheti a felügyelt robotvédelmi szabálykészletet, hogy a WAF letiltsa vagy naplózza az ismert rosszindulatú IP-címekről érkező kérelmeket. Az IP-címek a Microsoft Threat Intelligence hírcsatornából származnak. Az Intelligent Security Graph a Microsoft fenyegetésekkel kapcsolatos intelligenciáját működteti, és több szolgáltatás, köztük az Azure Security Center is használja.

> [!IMPORTANT]
> A robotvédelemi szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és egy előzetes szolgáltatásiszint-szerződéssel van ellátva. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A [részleteket](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) a Microsoft Azure előzetes verziók kiegészítő használati feltételei ben találja.

## <a name="use-with-owasp-rulesets"></a>OWASP szabályrendszerekkel való használat

A Bot Protection szabálykészlet az OWASP szabálykészlet (2.2.9, 3.0 és 3.1) mellett használható. Egy oWASP szabálykészlet egy adott időpontban használható. A robotvédelmi szabálykészlet tartalmaz egy további szabályt, amely megjelenik a saját szabálykészletében. A címe **Microsoft_BotManagerRuleSet_0.1**, és a többi OWASP-szabályhoz hasonlóan engedélyezheti vagy letilthatja azt.

![Bot szabályrendszer](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Szabálykészlet frissítése

Az ismert rossz IP-címek robotkockázat-kockázatcsökkentési szabálykészlet-listája naponta többször frissül a Microsoft Threat Intelligence hírcsatornából, hogy szinkronban maradjon a robotokkal. A webes alkalmazások folyamatosan védettek, még akkor is, ha a bot támadásvektorok változnak.

## <a name="next-steps"></a>További lépések

- [Bot-védelem konfigurálása a webalkalmazás-tűzfalhoz az Azure Application Gateway-en (előzetes verzió)](bot-protection.md)
