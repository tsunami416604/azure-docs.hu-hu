---
title: Az Azure DevTest Labs infrastruktúrájának szélesítése
description: Ez a cikk útmutatást nyújt az Azure DevTest Labs-infrastruktúra bővítéséhez.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 3a48cef2210721bf7116b1c4ad1169779288f47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644834"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Az Azure DevTest Labs infrastruktúrájának szélesítése
A DevTest Labs vállalati szintű megvalósítása előtt számos kulcsfontosságú döntési pont létezik. Ezeknek a döntési pontoknak a magas szintű megértése segít a szervezetnek a tervezési döntések meghozatalában a jövőben. Ezek a pontok azonban nem akadályozhatják meg a szervezetet a koncepció igazolásának elindításában. A kezdeti felskálázási tervezés első három területe a következő:

- Hálózatépítés és biztonság
- Előfizetési topológia
- Szerepek és felelősségek

## <a name="networking-and-security"></a>Hálózatépítés és biztonság
A hálózatépítés és a biztonság minden szervezet számára sarokkövei. Bár egy vállalati szintű üzembe helyezés sokkal mélyebb elemzést igényel, a koncepció igazolásának sikeres elvégzéséhez kevesebb követelmény szükséges. Néhány kulcsfontosságú terület a következők:

- **Azure-előfizetés** – A DevTest Labs üzembe helyezéséhez hozzá kell férnie egy Azure-előfizetéshez, amely megfelelő jogokkal rendelkezik az erőforrások létrehozásához. Az Azure-előfizetésekhez számos módon férhet hozzá, beleértve a nagyvállalati szerződést és az első fizetési díjat. Az Azure-előfizetéshez való hozzáférésről további információt [a nagyvállalati licencelési Azure](https://azure.microsoft.com/pricing/enterprise-agreement/)című témakörben talál.
- **Helyszíni erőforrásokhoz való hozzáférés** – Egyes szervezetek a DevTest Labs erőforrások hoz való hozzáférés a helyszíni erőforrásokhoz. A helyszíni környezetből az Azure-hoz biztonságos kapcsolatra van szükség. Ezért fontos, hogy az első lépések előtt vpn- vagy expressz útvonal-kapcsolatot állítson be/konfiguráljon. További információt a [Virtuális hálózatok – áttekintés című témakörben talál.](../virtual-network/virtual-networks-overview.md)
- **További biztonsági követelmények** – Egyéb biztonsági követelmények, például a gépházirendek, a nyilvános IP-címekhez való hozzáférés, az internethez való csatlakozás olyan forgatókönyvek, amelyeket a koncepció igazolása előtt esetleg felül kell vizsgálni. 

## <a name="subscription-topology"></a>Előfizetési topológia
Az előfizetéses topológia kritikus tervezési szempont a DevTest Labs vállalati üzembe helyezésekor. A koncepció bizonyításának befejezéséig azonban nem szükséges minden határozatot megszilárdulni. A vállalati implementációhoz szükséges előfizetések számának kiértékelésekor két végletek közül lehet: 

- Egy előfizetés az egész szervezet számára
- Előfizetés felhasználónként

Ezután kiemeljük az egyes megközelítések profiit.

### <a name="one-subscription"></a>Egy előfizetés
Gyakran a megközelítés egy előfizetés nem kezelhető egy nagyvállalat. Az előfizetések számának korlátozása azonban a következő előnyöket biztosítja:

- **Vállalati előrejelzési** költségek.  A költségvetés-behaszkadás sokkal könnyebb lesz egyetlen előfizetésben, mivel minden erőforrás egyetlen készletben van. Ez a megközelítés egyszerűbb döntéshozatalt tesz lehetővé aszámlázási ciklus bármely adott időpontjában történő költségellenőrzési intézkedések letételéről.
- **A** virtuális gépek, az összetevők, a képletek, a hálózati konfiguráció, az engedélyek, a házirendek stb.
- **A hálózati** munkamennyiség jelentősen leegyszerűsödik egyetlen előfizetésben olyan vállalatok számára, ahol a helyszíni kapcsolat követelmény. Virtuális hálózatok előfizetések közötti csatlakoztatása (hub-küllős modell) további előfizetések, amely további konfigurációt igényel, kezelése, IP-címterek, stb.
- **A csapategyüttműködés** könnyebb, ha mindenki ugyanabban az előfizetésben dolgozik – például egyszerűbb újrahozzárendelni egy virtuális gépegy munkatársa, ossza meg a csapat erőforrásait stb.

### <a name="subscription-per-user"></a>Előfizetés felhasználónként
A felhasználónkénti külön előfizetés egyenlő lehetőségeket biztosít az alternatív spektrum számára. Számos előfizetés előnyei a következők:

- **Az Azure skálázási kvóták** nem akadályozzák a bevezetést. Például, mint az írás azure lehetővé teszi, hogy 200 tárfiókok előfizetésenként. Vannak működési kvóták a legtöbb szolgáltatás az Azure-ban (sok testreszabható, néhány nem). Ebben a modellben egy előfizetés felhasználónként, nagyon valószínűtlen, hogy a legtöbb kvóták elérése. Az aktuális Azure-skálázási kvótákról az [Azure előfizetési és szolgáltatáskorlátai, kvótái és korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál további információt.
- A csoportoknak vagy az egyéni fejlesztőknek **történő visszaterhelések** sokkal könnyebbé válnak, lehetővé téve a szervezetek számára, hogy a jelenlegi modelljük használatával számolják el a költségeket.
- A DevTest Labs-környezetek **tulajdonjogi & engedélyei** egyszerűek. Az előfizetési szintű hozzáférést biztosít a fejlesztőknek, és 100%-ban felelősek mindenért, beleértve a hálózati konfigurációt, a laborházirendeket és a virtuális gépek kezelését.

Az Enterprise-on elég korlátozás lehet a spektrum szélsőségein. Ezért előfordulhat, hogy az előfizetéseket úgy kell beállítania, hogy az a szélsőségek közepébe essen. Ajánlott eljárásként a szervezet célja a lehető legkevesebb előfizetés használata, szem előtt tartva az előfizetések teljes számát növelő kényszerítési funkciókat. Megismételni, előfizetés-topológia kritikus fontosságú a DevTest Labs vállalati üzembe helyezéséhez, de nem késleltetheti a koncepció igazolását. További részletek a [cégirányítási](devtest-lab-guidance-governance-policy-compliance.md) cikkben arról, hogyan dönt az előfizetés és a labor részletessége a szervezetben.

## <a name="roles-and-responsibilities"></a>Szerepek és felelősségek
A DevTest Labs koncepcióigazolása három fő szerepkörrel rendelkezik, meghatározott felelősségi körökkel – Előfizetés-tulajdonos, DevTest Labs-tulajdonos, DevTest Labs-felhasználó és adott esetben közreműködő.

- **Előfizetés tulajdonosa** – Az előfizetés tulajdonosa jogosult az Azure-előfizetés felügyeletére, beleértve a felhasználók hozzárendelését, a szabályzatok kezelését, a hálózati topológia & kezelésének létrehozását, a kvótaemelések igénylését stb. További információt [ebben a cikkben](../role-based-access-control/rbac-and-directory-admin-roles.md)talál.
- **DevTest Labs tulajdonosa** – A DevTest Labs tulajdonosa teljes körű felügyeleti hozzáféréssel rendelkezik a laborhoz. Ez a személy felelős a felhasználók hozzáadásáért/eltávolításáért, a költségbeállítások kezeléséért, az általános laborbeállításokért és más virtuális gép-/műtermék-alapú feladatokért. A labor tulajdonosa is rendelkezik a DevTest Labs-felhasználó összes jogával.
- **DevTest Labs felhasználó** – A DevTest Labs felhasználó létrehozhatja és felhasználhatja a virtuális gépek a laborban. Ezek a személyek rendelkeznek néhány minimális felügyeleti képességekkel az általuk létrehozott virtuális gépeken (start/stop/delete/configure a virtuális gépek). A felhasználók nem kezelhetik más felhasználók virtuális gépeit.

## <a name="next-steps"></a>További lépések
Tekintse meg a sorozat következő cikkét: [Az Azure DevTest Labs megvalósításának koordinálása](devtest-lab-guidance-orchestrate-implementation.md)