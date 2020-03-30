---
title: Alkalmazásátjáró-integráció az Azure Security Centerrel | Microsoft dokumentumok
description: Ez a lap arról nyújt tájékoztatást, hogy az Application Gateway hogyan integrálódik az Azure Security Centerbe.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277193"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Az Application Gateway és az Azure Security Center közötti integráció áttekintése

Ismerje meg, hogy az Application Gateway és a Security Center hogyan segíti a webalkalmazás-erőforrások védelmét. Az alkalmazásátjáró webalkalmazás-tűzfala (WAF) integrálható a [Security Centerrel,](../security-center/security-center-intro.md) így zökkenőmentes képet biztosít a környezetben lévő nem védett webalkalmazások fenyegetései megelőzéséhez, észleléséhez és az azokra való reagáláshoz.

## <a name="overview"></a>Áttekintés

Az Application Gateway WAF a Security Center egyik javaslata a webes alkalmazások biztonsági rések elleni védelmére. A WAF által nem védett webes erőforrások a biztonsági központban magas súlyosságú javaslatokként jelennek meg. A webalkalmazás-tűzfalakra vonatkozó javaslatok az **Áttekintés** lap **Alkalmazások**csoportban jelennek meg.

![integráció a biztonsági központtal][1]

A webalkalmazás tűzfalával kapcsolatos javaslatokra kattintva megnyílik egy új lap, amely bemutatja a javaslat részleteit.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Webalkalmazás tűzfalának hozzáadása meglévő erőforráshoz

Keresse meg a Minden szolgáltatás biztonsági + identitásbiztonsági központ és a **Biztonsági központ – Áttekintés menüpontban**az Alkalmazások (Összes **szolgáltatás** > **biztonsága + identitásbiztonsági** > **központ)** **menüt.** A **Security Center – Applications**alkalmazásban a táblázat tartalmazza a Security Center által az előfizetésben észlelt alkalmazások listáját.

![webes alkalmazások][3]

Ha egy kritikus fontosságú webalkalmazásra kattint, megkapja az **Alkalmazás biztonsági állapotlapját.** Az alábbi képen a webalkalmazás, amely nem védi a webalkalmazás tűzfal. 

![webes erőforrások nem védettek][2]

A **Javaslatok csoportban** a **Webalkalmazás tűzfalhozzáadása** **lap** megnyitásához kattintson a Webalkalmazás tűzfal hozzáadása csoportban.

Ha nem rendelkezik meglévő Alkalmazásátjáróval, vagy újat szeretne létrehozni, kattintson az **Új létrehozása gombra,** majd az **Új webalkalmazás-tűzfal létrehozása**elemre, majd a Microsoft – Application **Gateway**parancsra. Ezzel végigvezeti az alkalmazásátjáró létrehozásának lépésein. Ezen a ponton a webalkalmazás védett erőforrásként kerül hozzáadásra, a Security Center most antól nyomon követi, hogy ezt az erőforrást egy webalkalmazás tűzfala védi. Ez nem adja hozzá háttérkészlet-tagként.

Ha már van alkalmazásátjárója, kiválaszthatja a **Meglévő megoldás használata csoportban**

![Weblap webalkalmazás-tűzfal hozzáadásához][4]

Webalkalmazás hozzáadása egy alkalmazásátjáróhoz a Biztonsági központon keresztül nem adja hozzá az erőforrást háttérkészlet-tagként. Ezt közvetlenül az alkalmazásátjáró-erőforráson kell elvégezni.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Erőforrás hozzáadása meglévő webalkalmazás-tűzfalhoz

Keresse meg a **Minden szolgáltatás** > **biztonsági + identitásbiztonsági** > **központját** és a **Biztonsági központ – Áttekintés menüpont,** kattintson a **Partnermegoldások**elemre. A Meglévő Biztonsági központ által tudó alkalmazásátjárók a **Partnermegoldások** lapon jelennek meg.

![partnermegoldások][7]

Kattintson **a Link app** gombra a Link **Applications**megnyitásához, itt lehetősége van a meglévő alkalmazások kiválasztására. Válassza ki a védeni kívánt alkalmazásokat, és kattintson az **OK gombra.** Ez nem adja hozzá a webalkalmazást az alkalmazásátjáró háttérkészletéhez. Ez az erőforrásokat védett erőforrásként állítja be, hogy a Biztonsági központ nyomon követhesse azokat. Ha az erőforrást háttérkészlet-tagként szeretné hozzáadni, ezt az alkalmazásátjárón kell elvégezni, az aktuális lapról a **Megoldás konzolra** kattintva az alkalmazásátjáró-erőforráshoz, ahol hozzáadhatja a webalkalmazást a háttérkészlethez.

![partnermegoldások alkalmazásai][6]

## <a name="finalize-configuration"></a>Konfiguráció véglegesítése

A Security Center nyomon követi az alkalmazásátjáróhoz védett erőforrásként hozzáadott alkalmazásokat.  Figyeli az erőforrás állapotát, és biztosítja, hogy egy alkalmazásátjáró védi. A következő lépés a virtuális gép privát IP-címének, nyilvános IP-címének vagy nic-jének hozzáadása az alkalmazásátjáró háttérkészletéhez. Amíg ez nem történik meg, egy további javaslat a **Véglegesítése alkalmazásvédelem** jelenik meg, amíg az erőforrás hozzáadása.

![Weblap webalkalmazás-tűzfal hozzáadásához][5]

## <a name="security-alerts"></a>Biztonsági riasztások

A Biztonsági központban keresse meg az **ÉSZLELÉSI** > **biztonsági riasztások at.**  Itt található waf riasztások az alkalmazásátjárók. A riasztások WAF-szabály szerint vannak lebontva.

![biztonsági riasztások][8]

Egy szabály kiválasztása az adott WAF-szabályra vonatkozó riasztások listáját tartalmazza. Minden riasztás további részleteket jelenít meg a megállapításról. A részletek az alkalmazásátjáróra mutató hivatkozást biztosítanak.
 
![riasztás részletei][9]

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan engedélyezheti a webalkalmazás-tűzfalat egy meglévő alkalmazásátjárón, olvassa el [az Azure Application Gateway létrehozása vagy frissítése webalkalmazás-tűzfallal](application-gateway-web-application-firewall-portal.md)című webhelyet.

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png