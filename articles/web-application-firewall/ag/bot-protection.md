---
title: Botvédelem konfigurálása az Azure Web Application Firewall (WAF) számára
description: Ismerje meg, hogyan konfigurálhatja a webalkalmazás-tűzfal (WAF) robotvédelmét az Azure Application Gateway-en.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516863"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Bot-védelem konfigurálása a webalkalmazás-tűzfalhoz az Azure Application Gateway-en (előzetes verzió)

Ez a cikk bemutatja, hogyan konfigurálhat egy bot védelmi szabályt az Azure Web Application Firewall (WAF) alkalmazásátjáró hoz az Azure Portalon. 

Engedélyezheti a felügyelt robotvédelmi szabálykészletet, hogy a WAF letiltsa vagy naplózza az ismert rosszindulatú IP-címekről érkező kérelmeket. Az IP-címek a Microsoft Threat Intelligence hírcsatornából származnak. Az Intelligent Security Graph a Microsoft fenyegetésekkel kapcsolatos intelligenciáját működteti, és több szolgáltatás, köztük az Azure Security Center is használja.

> [!NOTE]
> A robotvédelemi szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és egy előzetes szolgáltatásiszint-szerződéssel van ellátva. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A [részleteket](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) a Microsoft Azure előzetes verziók kiegészítő használati feltételei ben találja.

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy alapvető WAF-házirendet az Application Gateway alkalmazásátjáróhoz a [Webalkalmazás tűzfalházirendek létrehozása alkalmazásátjáróhoz](create-waf-policy-ag.md)című utasításait követve.

## <a name="enable-bot-protection-rule-set"></a>Botvédelmi szabálykészlet engedélyezése

1. A korábban létrehozott **Alapházirend** lapon a **Beállítások**csoportban válassza a **Szabályok**lehetőséget.  

2. A részletek lap **Szabályok** kezelése szakaszában, a legördülő menüben jelölje be a robotvédelem szabály jelölőnégyzetét, majd kattintson a **Mentés gombra.**

> [!div class="mx-imgBorder"]
> ![Robotvédelem](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>További lépések

Az egyéni szabályokról további információt az [Azure Application Gateway webalkalmazás-tűzfal 2-es webhelyének egyéni szabályai című témakörben talál.](custom-waf-rules-overview.md)