---
title: Bot védelme webalkalmazási tűzfal konfigurálása az Azure bejárati ajtajának (előzetes verzió)
description: Ismerje meg a webalkalmazási tűzfal (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481624"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>A webalkalmazási tűzfal (előzetes verzió) a robot a védelem konfigurálása
Ez a cikk bemutatja, hogyan konfigurálása bot védelmét szolgáló szabály Azure webalkalmazási tűzfal (WAF) a bejárati ajtajának Azure CLI, Azure PowerShell-lel vagy Azure Resource Manager-sablon használatával.

Egy felügyelt Bot védelmi szabálykészlet a WAF az egyéni műveletek is végezhetők rajtuk az ismert kártékony IP-címekről érkező kérelmek esetén is engedélyezhető. Az IP-címek vannak a Microsoft Fenyegetésfelderítő adatcsatorna forrása. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) Microsoft fenyegetésfelderítő működteti, és több szolgáltatást, többek között az Azure Security Center által használt.

> [!IMPORTANT]
> A robot védelmi sadu pravidel jelenleg nyilvános előzetes verzióban és a egy előzetes szolgáltatói szerződést. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Egy alapszintű WAF-szabályzat létrehozása a bejárati ajtó a leírt utasítások alapján [Azure bejárati ajtajának WAF szabályzat létrehozása az Azure portal használatával](waf-front-door-create-portal.md).

## <a name="enable-bot-protection-rule-set"></a>A robot védelmi sadu pravidel engedélyezése

1. Az alapszintű szabályzat lapján, amelyet az előző szakaszban található **beállítások**, kattintson a **szabályok**.
2. A Részletek lapján alatt a **szabályok kezelése** szakaszban, a legördülő menüben jelölje be a jelölőnégyzetet a szabály elé **BotProtection – előzetes verzió – 0,1**, majd válassza ki **mentése**felett.
    
   ![A robot védelmét szolgáló szabály](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [WAF figyelése](waf-front-door-monitor.md).
