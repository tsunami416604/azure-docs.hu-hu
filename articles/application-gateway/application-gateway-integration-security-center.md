---
title: "Alkalmazások átjáró integrálása az Azure Security Center |} Microsoft Docs"
description: "Ezen a lapon bemutatja, hogyan Alkalmazásátjáró integrálva van-e az Azure Security Center."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: 68d4f9cb5fc9c9f15a355d9fdade922889d2aa30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Alkalmazásátjáró és az Azure Security Center közötti integráció áttekintése

Ismerje meg, hogyan Application Gateway és a Security Center védelmét a webes alkalmazás erőforrásaihoz. Alkalmazás átjáró webalkalmazási tűzfal (WAF) integrálható [Security Center](../security-center/security-center-intro.md) megjelenítésére szolgáló zökkenőmentes megelőzése, észlelésében és kezelésében a környezetben nem védett webalkalmazásokhoz fenyegetések ellen.

## <a name="overview"></a>Áttekintés

Alkalmazás átjáró WAF várja, hogy egy érték, a Security Center webalkalmazások biztonsági réseket és biztonsági rések elleni védelme. Magas súlyosságú javaslatok, a security center engedélyezve van a webes erőforrásokat WAF által nem védett megjelenítése. Webalkalmazási tűzfalak javaslatok jelenjenek meg a **áttekintése** lap **alkalmazások**.

![a security Center Integration][1]

Kattintson a javaslatokkal vonatkozó webalkalmazási tűzfal egy új, az ajánlás részleteit megjelenítő lapon nyílik meg.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Webalkalmazási tűzfal hozzáadása a meglévő erőforrás

Navigáljon a **minden szolgáltatás** > **biztonság + identitás szakaszában** > **Security Center** és a **Security Center -áttekintése**, kattintson a **alkalmazások**. A **Security Center - alkalmazások**, a tábla a Security Center az előfizetésében észlelt alkalmazások listáját tartalmazza.

![webalkalmazás][3]

Kattintva kritikus problémát webalkalmazás, azzal a **biztonsági állapotának** lap. Az alábbi képen, a webes alkalmazás webalkalmazási tűzfal által nem védett. 

![nem védett webes erőforrások][2]

Kattintson a **webalkalmazási tűzfal hozzáadása** alatt **javaslatok** megnyitásához a **webalkalmazási tűzfal hozzáadása** lap.

Ha nem rendelkezik meglévő Alkalmazásátjáró, vagy hozzon létre egy újat szeretne, kattintson a **hozzon létre új** és a **hozzon létre egy új webalkalmazási tűzfal**, és kattintson a **Microsoft - alkalmazás átjáró** . Ez végigvezeti Alkalmazásátjáró létrehozásához szükséges lépéseket. Ezen a ponton a webes alkalmazás meg van adva egy védett erőforrásokhoz, most már a Security Center követi nyomon, hogy ehhez az erőforráshoz webalkalmazási tűzfal védi. Ez nem adja hozzá azt egy háttér címkészletet tag.

Ha egy meglévő Alkalmazásátjáró, kiválaszthatja azt a **meglévő megoldással**

![webalkalmazási tűzfal-weblap hozzáadása][4]

A Security Center keresztül Alkalmazásátjáró webalkalmazások hozzáadása nem adja az erőforrást háttér címkészletet tagja. Ez a kell elvégezni az átjáró-erőforráshoz közvetlenül.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Erőforrás hozzáadása egy meglévő webalkalmazási tűzfal

Navigáljon a **minden szolgáltatás** > **biztonság + identitás szakaszában** > **Security Center** és a **Security Center -áttekintése**, kattintson a **partneri megoldások**. Meglévő Security Center felismerésre képes alkalmazást átjárók megjelenítése a **partneri megoldások** lap.

![Partneri megoldások][7]

Kattintson a **összekapcsolás** megnyitásához **összekapcsolás**, itt lehetősége van a beállítások, jelölje be a meglévő alkalmazásokat. Válassza ki azokat a védelmét, majd **OK**. Ez nem bővíti ki a webalkalmazás a háttérkészletbe az Alkalmazásátjáró. Ez beállítja az erőforrások védett erőforrásként, a Security Center nyomon követheti. Az erőforrás hozzáadása a háttér-készlet tagja, ez a kell elvégezni az alkalmazás-átjárót, kattintson az aktuális oldalon **megoldáskonzol** kell fordítani az alkalmazás átjáró-erőforráshoz, ahol felveheti a webes alkalmazás a háttérkészlet.

![Partneri megoldások alkalmazások][6]

## <a name="finalize-configuration"></a>Konfigurálás lezárásaképpen

A Security Center nyomon követi az alkalmazások Alkalmazásátjáró védett erőforrásként hozzáadni.  Ez az erőforrás állapotát figyeli, és biztosítja, hogy egy alkalmazás-átjáró által védett. A következő lépés a magánhálózati IP-címe, nyilvános IP-cím vagy a virtuális gép hálózati Adapterének hozzáadása az Alkalmazásátjáró háttérkészlet. Amíg ez történik, egy további ajánlása **alkalmazás védelem véglegesítése** látható, amíg az erőforrás nincs hozzáadva.

![webalkalmazási tűzfal-weblap hozzáadása][5]

## <a name="security-alerts"></a>Biztonsági riasztások

A Security Center Ugrás **ÉSZLELÉSI** > **biztonsági riasztások**.  Itt megtalálja az Ön WAF riasztások az alkalmazás átjárók. Riasztások WAF szabály bontásban.

![biztonsági riasztások][8]

Kattintson egy szabály nyújtják adott WAF szabályhoz tartozó riasztások listája. Minden riasztás további részleteket a megállapítás mutatja be. A részleteket az Alkalmazásátjáró mutató hivatkozás biztosítása.
 
![riasztás részletei][9]

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy a meglévő Alkalmazásátjáró webalkalmazási tűzfal engedélyezése, látogasson el a [létrehozás vagy frissítés webalkalmazási tűzfal az Azure-Alkalmazásátjáró](application-gateway-web-application-firewall-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png