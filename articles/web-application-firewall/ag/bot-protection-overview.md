---
title: WAF az Azure Application Gateway bot Protection áttekintése
titleSuffix: Azure Web Application Firewall
description: Ez a cikk áttekintést nyújt a webalkalmazási tűzfalról (WAF) a Application Gateway bot Protection szolgáltatásban
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027094"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure webalkalmazási tűzfal az Azure-ban Application Gateway bot Protection – áttekintés

Az internetes forgalom körülbelül 20%-a rossz robotoktól származik. Olyan dolgok, mint a kaparás, a vizsgálat és a biztonsági rések keresése a webalkalmazásban. Ha ezek a robotok le vannak állítva a webalkalmazási tűzfalon (WAF), nem tudnak megtámadni. Emellett nem használhatják fel erőforrásait és szolgáltatásait, például a háttérrendszer és más mögöttes infrastruktúra használatát.

Engedélyezheti a WAF felügyelt bot Protection-szabálykészlet számára az ismert kártékony IP-címekről érkező kérések blokkolását vagy naplózását. Az IP-címek forrása a Microsoft Threat Intelligence-hírcsatorna. A Microsoft Threat Intelligence az Intelligens biztonsági gráffal működik, amelyet több szolgáltatás, így az Azure Security Center is használ.

> [!IMPORTANT]
> A robot Protection-szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és az előzetes verziójú szolgáltatói szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További részletekért tekintse meg a [Microsoft Azure előzetesek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="use-with-owasp-rulesets"></a>Használat a OWASP szabályrendszerek

A bot Protection szabályait a OWASP szabályrendszerek (2.2.9, 3,0 és 3,1) együtt használhatja. Egy adott időpontban csak egy OWASP-szabályrendszert lehet használni. A bot Protection szabályrendszert egy további szabályt is tartalmaz, amely a saját szabályzatában jelenik meg. A cím **Microsoft_BotManagerRuleSet_0 1**, és engedélyezheti vagy letilthatja a többi OWASP-szabályhoz hasonlóan.

![Robot-szabályrendszert](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Szabályrendszer frissítése

Az ismert rossz IP-címek robot-elhárítási szabályait naponta többször frissítik a Microsoft Threat Intelligence-hírcsatornában, hogy szinkronban maradjanak a botokkal. A webalkalmazások folyamatosan védve vannak, még akkor is, ha a robot támadási vektorok változnak.

## <a name="next-steps"></a>Következő lépések

- [A robot Protection konfigurálása a webalkalmazási tűzfalhoz az Azure Application Gateway (előzetes verzió)](bot-protection.md)
