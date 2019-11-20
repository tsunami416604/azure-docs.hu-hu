---
title: A bot Protection beállítása a WAF az Azure bejárati ajtóval (előzetes verzió)
description: A webalkalmazási tűzfal (WAF) megismerése.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 95660f764d28172ecb55a4952b785fea5f2aa4bb
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186715"
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

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [figyelheti a WAF](waf-front-door-monitor.md).
