---
title: A bot Protection konfigurálása az Azure webalkalmazási tűzfal (WAF) számára
description: Ismerje meg, hogyan konfigurálhatja a bot Protectiont a webalkalmazási tűzfal (WAF) számára az Azure Application Gatewayon.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73516863"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>A robot Protection konfigurálása a webalkalmazási tűzfalhoz az Azure Application Gateway (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy robot Protection-szabályt az Azure webalkalmazási tűzfal (WAF) szolgáltatásban Application Gateway a Azure Portal használatával. 

Engedélyezheti a WAF felügyelt bot Protection-szabálykészlet számára az ismert kártékony IP-címekről érkező kérések blokkolását vagy naplózását. Az IP-címek forrása a Microsoft Threat Intelligence-hírcsatorna. A Intelligens biztonsági gráf a Microsoft fenyegetésekkel kapcsolatos intelligenciát, és több szolgáltatás, például Azure Security Center használatát is használja.

> [!NOTE]
> A robot Protection-szabálykészlet jelenleg nyilvános előzetes verzióban érhető el, és az előzetes verziójú szolgáltatói szerződéssel rendelkezik. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részletekért tekintse meg a [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)   .

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy alapszintű WAF szabályzatot a Application Gateway számára a [webalkalmazási tűzfal házirendjeinek létrehozása a Application Gatewayhoz](create-waf-policy-ag.md)című témakörben leírtak szerint.

## <a name="enable-bot-protection-rule-set"></a>Robot Protection-szabály beállításának engedélyezése

1. A korábban létrehozott **alapszintű** szabályzat lapon a **Beállítások**területen válassza a **szabályok**elemet.  

2. A Részletek lapon, a **szabályok kezelése**   szakaszban, a legördülő menüben jelölje be a bot Protection-szabály jelölőnégyzetét, majd kattintson a **Mentés**gombra.

> [!div class="mx-imgBorder"]
> ![Robotvédelem](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>További lépések

Az egyéni szabályokkal kapcsolatos további információkért lásd: [webalkalmazási tűzfal v2 egyéni szabályai az Azure Application Gateway](custom-waf-rules-overview.md).