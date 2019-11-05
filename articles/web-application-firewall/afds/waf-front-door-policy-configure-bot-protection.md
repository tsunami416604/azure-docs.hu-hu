---
title: A bot Protection konfigurálása a webalkalmazási tűzfalhoz az Azure bejárati ajtaján (előzetes verzió)
description: A webalkalmazási tűzfal (WAF) megismerése.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512469"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>A webalkalmazási tűzfal robot-védelmének konfigurálása (előzetes verzió)
Ez a cikk bemutatja, hogyan konfigurálhatja a bot Protection-szabályt az Azure Web Application Firewall (WAF) szolgáltatásban az Azure CLI, Azure PowerShell vagy Azure Resource Manager sablon használatával.

A felügyelt robot védelmi szabálykészlet beállítható úgy, hogy a WAF egyéni műveleteket hajtson végre az ismert kártékony IP-címekről érkező kérésekre. Az IP-címek forrása a Microsoft Threat Intelligence-hírcsatorna. A [intelligens biztonsági gráf](https://www.microsoft.com/security/operations/intelligence) a Microsoft fenyegetésekkel kapcsolatos intelligenciát, és több szolgáltatás, például Azure Security Center használatát is használja.

> [!IMPORTANT]
> A robot Protection-szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és az előzetes verziójú szolgáltatói szerződéssel van ellátva. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy alapszintű WAF-szabályzatot a bejárati ajtóhoz a [Azure Portal használatával](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>Robot Protection-szabály beállításának engedélyezése

1. A fenti szakaszban létrehozott alapszintű szabályzat lapon kattintson a **Beállítások**elemre, majd a **szabályok**elemre.
2. A Részletek lapon, a **szabályok kezelése** szakaszban, a legördülő menüben jelölje be a **BotProtection-Preview-0,1**szabály előtt található jelölőnégyzetet, majd kattintson a fenti **Mentés** gombra.
    
   ![Robot védelmi szabály](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [figyelheti a WAF](waf-front-door-monitor.md).
