---
title: Átjáró integrációja az Azure Security Centerrel |} A Microsoft Docs
description: Ez az oldal nyújt tájékoztatást hogyan Application Gateway integrálva van az Azure Security Center.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: victorh
ms.openlocfilehash: 10f115b64f0bd3f7e557da2bedbf3327d0ef483d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122307"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Az Application Gateway és az Azure Security Center közötti integráció áttekintése

Megtudhatja, hogyan az Application Gateway és a Security Center a webalkalmazás-erőforrások védelme. Application gateway webalkalmazási tűzfal (WAF) integrálható [a Security Center](../security-center/security-center-intro.md) adja meg, hogy áttekinthetőségét, észlelése és háríthatja el a fenyegetéseket a környezetben nem védett webalkalmazások számára.

## <a name="overview"></a>Áttekintés

Az Application Gateway WAF egy javaslatot a Security Center olyan webes alkalmazások védelme a biztonsági rések ellen. A security center magas súlyossági szintű ajánlásként megjelenítése engedélyezve webes erőforrások, amelyek a WAF által nem védett. A webalkalmazás-tűzfalak javaslatok láthatók a **áttekintése** lap **alkalmazások**.

![a security center-integráció][1]

Kattintson a kapcsolódó webalkalmazási tűzfal megnyílik egy új oldalt, az ajánlás részleteit megjelenítő javaslatokkal szolgálni.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Webalkalmazási tűzfal hozzáadása egy meglévő erőforrás

Navigáljon a **minden szolgáltatás** > **biztonság + identitás** > **a Security Center** és a **Security Center – áttekintés**, kattintson a **alkalmazások**. A **a Security Center - alkalmazások**, a tábla tartalmazza, amelyeket a Security Center észleli az előfizetésében.

![webalkalmazás][3]

A webalkalmazás egy kritikus probléma kattintva a **alkalmazás biztonsági állapota** lapot. Az alábbi képen, a webalkalmazási tűzfal által nem védett webalkalmazás. 

![webes erőforrások nem védett][2]

Kattintson a **webalkalmazási tűzfal hozzáadása** alatt **javaslatok** megnyitásához a **webalkalmazási tűzfal hozzáadása** lapot.

Ha nem rendelkezik meglévő Application Gateway, vagy hozzon létre egy újat szeretne, kattintson a **hozzon létre új** és a **hozzon létre egy új webalkalmazási tűzfal**, és kattintson a **Microsoft – az Application Gateway** . Ez végigvezeti az application gateway létrehozásához szükséges lépéseken. Ezen a ponton a webes alkalmazások hozzáadásakor, védett erőforrások, a Security Centert most követi nyomon, hogy ezt az erőforrást a webalkalmazási tűzfal védi. Ez nem ad hozzá, háttérbeli címkészlet tagként.

Ha rendelkezik meglévő application gateway, kiválaszthatja a **meglévő megoldás használata**

![oldal a webalkalmazási tűzfal hozzáadása][4]

Egy application gateway a Security Center segítségével webes alkalmazások adása nem adja hozzá az erőforrás háttérbeli címkészlet tagként. Ezt kell elvégezni, az application gateway erőforráshoz a közvetlenül.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adjon hozzá egy erőforrást egy meglévő webalkalmazási tűzfal

Navigáljon a **minden szolgáltatás** > **biztonság + identitás** > **a Security Center** és a **Security Center – áttekintés**, kattintson a **partneri megoldások**. A Security Center képes alkalmazást meglévő átjárók jelennek meg a **Partnermegoldások** lapot.

![Partnermegoldások][7]

Kattintson a **alkalmazás összekapcsolása** megnyitásához **alkalmazások összekapcsolása**, itt a meglévő alkalmazások választható lehetőségeket vannak megadva. Válassza ki az alkalmazások védelme, és kattintson a **OK**. Ez nem adja hozzá az application Gateway háttérkészlet a webalkalmazást. Ezzel beállítja az erőforrások egy védett erőforrás, a Security Center képes nyomon követni. Az erőforrás hozzáadása a háttérbeli címkészlet tagjaként, ezt kell elvégezni, az application gatewayen az aktuális lapon, rákattinthat a **megoldáskonzol** kattintva megtekinthető az application gateway erőforráshoz, melyben hozzáadhatja a webes alkalmazásba a háttérkészlet.

![Partneri megoldások alkalmazások][6]

## <a name="finalize-configuration"></a>Konfiguráció véglegesítése

A Security Center az application gateway egy védett erőforrásként hozzáadott alkalmazások követi nyomon.  Ez az erőforrás állapotát figyeli, és biztosítja, hogy az application gateway által védett. A következő lépés, hogy az application Gateway háttérkészlet hozzáadása a privát IP, nyilvános IP-cím vagy a virtuális gép hálózati Adapterén. Amíg ez nem egy további ajánlása **alkalmazásvédelem véglegesítése** jelenik meg, amíg az erőforrás nincs hozzáadva.

![oldal a webalkalmazási tűzfal hozzáadása][5]

## <a name="security-alerts"></a>Biztonsági riasztások

A Security Center Ugrás **ÉSZLELÉSI** > **biztonsági riasztások**.  Itt találja a WAF-riasztások az application Gateway átjárók esetében. Riasztások WAF szabály bontásban.

![Biztonsági riasztások][8]

Kattintson egy szabályt az adott WAF-szabályokat a riasztások listája biztosítja. Minden riasztás a megkereséséről további részleteket jeleníti meg. A részletek adjon meg egy hivatkozást az application gateway.
 
![Riasztás részletei][9]

## <a name="next-steps"></a>További lépések

A meglévő application gateway webalkalmazási tűzfal engedélyezése talál [létrehozás vagy frissítés az Azure Application Gateway webalkalmazási tűzfallal rendelkező](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png