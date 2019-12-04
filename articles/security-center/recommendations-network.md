---
title: Azure Security Center javaslatok a hálózatkezeléshez
description: Ez a cikk Azure Security Center biztonsági javaslatait sorolja fel, amelyek segítenek a hálózati erőforrások védelmében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2019
ms.author: memildin
ms.openlocfilehash: 1b8d84286d14949c007d1ece3d089a7606464aaf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781968"
---
# <a name="network-recommendations---reference-guide"></a>Hálózati javaslatok – útmutató

Ez a cikk a hálózat topológiájának és az internet felé néző végpontoknak a Azure Security Centerban esetlegesen megjelenő javaslatainak teljes listáját tartalmazza.

A [következő témakörből megtudhatja](security-center-network-recommendations.md), hogyan keresheti meg ezeket, és hogyan oldhatja meg őket.

## <a name="network-recommendations"></a>Hálózattal kapcsolatos ajánlások

|Javaslat neve|Leírás|Súlyosság|Biztonsági pontszám|Erőforrás típusa|
|----|----|----|----|----|----|
|Az alhálózat szintjén engedélyezni kell a hálózati biztonsági csoportokat.|Engedélyezze a hálózati biztonsági csoportok számára az alhálózatokban üzembe helyezett erőforrások hálózati hozzáférésének szabályozását.|Magas/közepes|30|Alhálózat|
|A virtuális gépeket hálózati biztonsági csoporttal kell társítani|Engedélyezze a hálózati biztonsági csoportok számára a virtuális gépek hálózati hozzáférésének szabályozását.|Magas/közepes|30|Virtuális gép|
|A hozzáférésnek korlátozva kell lennie az internetre irányuló virtuális gépekkel rendelkező, engedékeny hálózati biztonsági csoportoknak|A meglévő engedélyezési szabályok elérésének korlátozásával megerősítheti az internetre irányuló virtuális gépek hálózati biztonsági csoportjait.|Magas|20|Virtuális gép|
|A IaaS-NSG lévő webalkalmazások szabályait meg kell erősíteni|Megerősítheti a webalkalmazásokat futtató virtuális gépek hálózati biztonsági csoportját (NSG) olyan NSG-szabályokkal, amelyek túlzottan engedékenyek a webalkalmazási portok tekintetében.|Magas|20|Virtuális gép|
|A App Serviceshoz való hozzáférést korlátozni kell|Korlátozza a hozzáférést a App Services a hálózati konfiguráció módosításával, hogy megtagadja a bejövő forgalmat a túl széles tartományokból.|Magas|10|App Service|
|A felügyeleti portokat be kell zárni a virtuális gépeken|A felügyeleti portokhoz való hozzáférés korlátozása érdekében a virtuális gépek hálózati biztonsági csoportjának megerősítése.|Magas|10|Virtuális gép|
DDoS Protection a standardot engedélyezni kell|A DDoS Protection szolgáltatás szabványának engedélyezésével biztosíthatja a nyilvános IP-címekkel rendelkező alkalmazásokat tartalmazó virtuális hálózatok védelmét. A DDoS elleni védelem lehetővé teszi a hálózati térfogatos és a protokollok elleni támadások enyhítését.|Magas|10|Virtuális hálózat|
|A virtuális gépen lévő IP-továbbítást le kell tiltani|Tiltsa le az IP-továbbítást. Ha az IP-továbbítás engedélyezve van egy virtuális gép hálózati adapterén, a gép más célhelyekre irányuló forgalmat is fogadhat. Az IP-továbbítást ritkán kell végrehajtani (például ha a virtuális GÉPET hálózati virtuális készülékként használja), ezért ezt a hálózati biztonsági csapatnak kell áttekintenie.|Közepes|10|Virtuális gép|
|A webalkalmazás csak HTTPS protokollon keresztül érhető el|A "csak HTTPS" hozzáférés engedélyezése a webes alkalmazásokhoz. A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban.|Közepes|20|Webalkalmazás|
|Igény szerinti hálózati hozzáférés-vezérlést kell alkalmazni a virtuális gépeken|Alkalmazzon igény szerinti (JIT) virtuálisgép-hozzáférés-vezérlést a kiválasztott portok elérésének végleges zárolásához, és engedélyezze, hogy a jogosult felhasználók csak korlátozott ideig nyissák meg őket a JIT használatával.|Magas|20|Virtuális gép|
|A Function apps csak HTTPS-kapcsolaton keresztül érhető el|A "csak HTTPS" hozzáférés engedélyezése a Function apps alkalmazásokhoz. A HTTPS használata biztosítja a kiszolgálók/szolgáltatások hitelesítését, és védelmet biztosít a hálózati réteg-lehallgatási támadásoktól érkező adatforgalomban.|Közepes|20|Függvényalkalmazás|
|A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell|Biztonságos átvitel engedélyezése a Storage-fiókokba. A biztonságos átvitel olyan lehetőség, amely arra kényszeríti a Storage-fiókot, hogy csak biztonságos kapcsolatokból (HTTPS) fogadja a kéréseket. A HTTPS használata biztosítja a hitelesítést a kiszolgáló és a szolgáltatás között, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől.|Magas|20|Tárfiók|


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következőket:

* [Identitás és hozzáférés figyelése](security-center-identity-access.md)
* [A gépek és alkalmazások védelme](security-center-virtual-machine-protection.md)
* [Az Azure SQL-szolgáltatás védelme](security-center-sql-service-recommendations.md)

