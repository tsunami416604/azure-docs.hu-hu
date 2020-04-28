---
title: Application Gateway integráció a Azure Security Centertal | Microsoft Docs
description: Ez az oldal információt nyújt arról, hogyan integrálható a Application Gateway Azure Security Centerba.
services: application-gateway
author: vhorne
ms.author: victorh
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.openlocfilehash: f5ecd2334ca80f5561c0611239b5bb00d222112a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277193"
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Application Gateway és Azure Security Center közötti integráció áttekintése

Ismerje meg, hogyan segíti a Application Gateway és Security Center a webalkalmazás-erőforrások védelmében. Az Application Gateway webalkalmazási tűzfal (WAF) integrálható [Security Centerekkel](../security-center/security-center-intro.md) , így zökkenőmentesen megtekinthető a környezetében a nem védett webalkalmazásokra irányuló fenyegetések megelőzése, észlelése és elhárítása.

## <a name="overview"></a>Áttekintés

Application Gateway WAF a webalkalmazások biztonsági rések és sebezhetőségek elleni védelméhez Security Center javaslata. A WAF által nem védett webes erőforrások magas súlyossági szintű javaslatként jelennek meg a Security Centerben. A webalkalmazási tűzfalakra vonatkozó javaslatok az **Áttekintés** lapon, az **alkalmazások**területen jelennek meg.

![integráció a Security centerrel][1]

Ha a webalkalmazási tűzfallal kapcsolatos javaslatokra kattint, megnyílik egy új oldal, amely a javaslat részleteit jeleníti meg.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Webalkalmazási tűzfal hozzáadása meglévő erőforráshoz

Navigáljon **az összes szolgáltatás** > **Biztonság és Identitáskezelés** > **Security Center** és a **Security Center – áttekintés**területen kattintson az **alkalmazások**elemre. **Security Center alkalmazásokban**a tábla az előfizetésében Security Center észlelt alkalmazások listáját tartalmazza.

![webalkalmazások][3]

Ha egy kritikus problémával rendelkező webalkalmazásra kattint, megjelenik az **alkalmazás biztonsági állapota** oldal. Az alábbi képen a webalkalmazást, amelyet a webalkalmazási tűzfal nem véd. 

![nem védett webes erőforrások][2]

Kattintson a **webalkalmazási tűzfal hozzáadása** elemre a **javaslatok** területen a **webalkalmazási tűzfal hozzáadása** lap megnyitásához.

Ha nem rendelkezik meglévő Application Gatewayval, vagy újat szeretne létrehozni, kattintson az **új létrehozása** elemre, és **hozzon létre egy új webalkalmazási tűzfalat**, és kattintson a **Microsoft-Application Gateway**elemre. Ez végigvezeti az Application Gateway létrehozásának lépésein. Ezen a ponton a webalkalmazás védett erőforrásként lesz hozzáadva, Security Center most nyomon követi, hogy ezt az erőforrást egy webalkalmazási tűzfal védi. Ez nem adja hozzá háttér-készlet tagként.

Ha már rendelkezik egy meglévő Application Gateway **szolgáltatással, a meglévő megoldás használata** alatt választhatja ki

![Webalkalmazási tűzfal hozzáadására szolgáló oldal][4]

Webalkalmazás hozzáadása egy Application Gateway-hez Security Centeron keresztül nem adja hozzá az erőforrást a háttérbeli készlet tagjaként. Ezt közvetlenül az Application Gateway-erőforráson kell elvégezni.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Erőforrás hozzáadása meglévő webalkalmazási tűzfalhoz

Navigáljon **az összes szolgáltatás** > **Biztonság és Identitáskezelés** > **Security Center** és a **Security Center – áttekintés**területen kattintson a **partneri megoldások**elemre. Meglévő Security Center Aware Application Gateway-átjárók a **partneri megoldások** lapon jelennek meg.

![partneri megoldások][7]

Az **alkalmazás csatolása elemre kattintva** megnyithatja az **alkalmazások hivatkozásait**, Itt választhatja ki a meglévő alkalmazások lehetőséget. Válassza ki a védelemmel ellátni kívánt alkalmazásokat, majd kattintson **az OK**gombra. Ez nem adja hozzá a webalkalmazást az Application Gateway háttér-készletéhez. Ezzel az erőforrást védett erőforrásként állítja be, így Security Center nyomon követheti. Ha hozzá szeretné adni az erőforrást a háttérbeli készlet tagjaként, ezt az Application Gateway-ben kell elvégezni, az aktuális lapról **kattintva az** Application Gateway-erőforráshoz, ahol felveheti a webalkalmazást a háttér-készletbe.

![partneri megoldások alkalmazásai][6]

## <a name="finalize-configuration"></a>Konfiguráció véglegesítése

Security Center nyomon követi az Application Gateway számára védett erőforrásként hozzáadott alkalmazásokat.  Figyeli az erőforrás állapotát, és biztosítja, hogy az egy Application Gateway által védett. A következő lépés a virtuális gép magánhálózati IP-címének, nyilvános IP-címének vagy hálózati adapterének hozzáadása az Application Gateway háttér-készletéhez. Amíg ez nem történik meg, az **alkalmazás védelmének véglegesítésére** vonatkozó további javaslat jelenik meg, amíg hozzá nem adja az erőforrást.

![Webalkalmazási tűzfal hozzáadására szolgáló oldal][5]

## <a name="security-alerts"></a>Biztonsági riasztások

Security Centeron belül navigáljon az **észlelési** > **biztonsági riasztásokhoz**.  Itt talál WAF-riasztásokat az Application Gateway átjárók számára. A riasztások WAF-szabály szerint vannak lebontva.

![biztonsági riasztások][8]

Egy szabály kiválasztásával megadhatja az adott WAF-szabályhoz tartozó riasztások listáját. Minden riasztás további részleteket jelenít meg a keresésről. A részletek az Application gatewayre mutató hivatkozást tartalmaznak.
 
![riasztás részletei][9]

## <a name="next-steps"></a>További lépések

Ha szeretné megtudni, hogyan engedélyezheti a webalkalmazási tűzfalat egy meglévő Application Gateway-példányon, látogasson el [Az Azure-Application Gateway webalkalmazási tűzfallal való létrehozására vagy frissítésére](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png