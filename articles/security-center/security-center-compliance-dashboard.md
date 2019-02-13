---
title: Azure Security Center használata a szabályozási megfelelőség javítása |} A Microsoft Docs
description: 'Oktatóanyag: Ismerje meg, hogyan javíthatja a szabályozásoknak való megfelelőséget, az Azure Security Center használatával.'
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 2e73cbd1de4336a08e26a609dfe704da0a068860
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106004"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: Előírásoknak való megfelelés javítása
---

Az Azure Security Center leegyszerűsíti a folyamatot jogszabályi követelményeknek, az előírásoknak való megfelelés irányítópulttal. Az irányítópulton a Security Center a megfelelőségi állapotáról, folyamatos értékelésekkel, az Azure-környezet alapján betekintést biztosít. A Security Center által végzett értékelések kockázati tényezők alapján, a hibrid felhőalapú környezetben ajánlott biztonsági eljárások megfelelően elemezheti. Ezek az értékelések szabványok támogatott bizonyos megfelelőségi vezérlők vannak leképezve. A szabályozásoknak való megfelelőséget irányítópultján láthassa az összes értékelés állapotát egy adott standard vagy a rendelet a környezetében a környezeten belül van. Az ajánlásoknak és kockázati tényezők alapján csökkentse a környezetben, megtekintheti a megfelelőségi állapotáról, javíthatja.

Az oktatóanyag során a következőket fogja elsajátítani:

-   Az előírásoknak való megfelelés a szabályozásoknak való megfelelőséget irányítópulttal kiértékelése

-   Javíthatja a megfelelőségi helyzetét művelet végrehajtásával vonatkozó javaslatok

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban tárgyalt funkciók elvégezhető, rendelkeznie kell a Security Center Standard tarifacsomagot. Megpróbálhatja Security Center Standard költségek nélkül.
További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](https://docs.microsoft.com/azure/security-center/security-center-get-started) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

##  <a name="assess-your-regulatory-compliance"></a>A szabályozásoknak való megfelelés

A Security Center folyamatosan értékeli a konfiguráció az erőforrások biztonsági problémák és biztonsági rések azonosítása. Ezek az értékelések javaslatok, amelyek a biztonsági higiéniai fejlesztésére koncentrálhat, jelennek meg. A szabályozásoknak való megfelelőséget irányítópultján megtekintheti az összes rájuk vonatkozó követelményeket, a megfelelőségi követelményeknek készletét ahol alkalmazható a biztonság állapotát támogatott követelményei vannak leképezve. Ez lehetővé teszi, hogy megtekintheti a megfelelőségi állapotáról, a standard szintű csomagja értékelésről állapota alapján.

A szabályozásoknak való megfelelőséget irányítópult nézet segítségével a figyelmet a hiányosságok pótlásában megfelel egy standard vagy a rendelet, amely az Ön számára fontos. A fókuszban lévő nézet segítségével folyamatosan figyelheti a megfelelőségi pontszám idővel belül a dinamikus felhőbeli és hibrid környezetekben is.

>[!NOTE]
> Jelenleg támogatott szabályozási normák terén a következők: Az Azure CIS, PCI DSS 3.2-es, az ISO 27001 és SOC TSP. További szabványoknak megfelelően megjelennek az irányítópulton.

1.  A a Security Center főmenüjében alatt **szabályzat és megfelelőség** kiválasztása **szabályozásoknak való megfelelőséget irányítópult**. <br>
A képernyő tetején megjelenik a megfelelőségi állapotának áttekintése az támogatott megfelelőségi szabályozásokhoz együtt egy irányítópulton. Láthatja, hogy az összesített megfelelőségi pontszám és megadásának és a sikertelen társított minden egyes standard értékelések számát.

    ![Leírás megbízható számítógép](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Válassza ki a lapon egy megfelelőségi szabvány, amely az Ön számára. Látni fogja, hogy standard összes vezérlők listájából. A alkalmazni vezérlők megtekintheti átadásával, valamint a vezérlőhöz társított értékelések sikertelen. Egyes vezérlőelemeket szürkén jelennek meg. Ezek a vezérlők nincs társítva a Security Center értékeléseknek. Ezek a követelmények elemzése, és felmérheti a saját környezetében kell. Ezek közül néhányat a folyamattal kapcsolatos és nem technikai lehetnek.

    ![megfelelőségi lap](./media/security-center-compliance-dashboard/compliance-pci.png)

3.  Válassza ki a **összes** a nézet az összes releváns Security Center javaslatait és azok kapcsolódó szabványok fülre. Ez a nézet egy adott javaslat által érintett összes különböző szabványok azonosítására szolgáló hasznos lehet. <br> Ez a nézet segítségével potenciálisan rangsorolhatja a javaslatok, el kell hárítania. Például ha látni, hogy a javaslat **MFA engedélyezése az előfizetésben tulajdonosi engedélyekkel rendelkező fiókok esetében** sikertelen a több erőforrást és a társított többféle szabvány, majd a Ez az ajánlás feloldása lesz az általános megfelelőségi pontszám a nagy hatással van.

    ![megfelelőségi pontszám gyakorolt hatás](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

## <a name="improve-your-compliance-posture"></a>Javíthatja a megfelelőségi helyzetét


Adja meg az adatokat a szabályozásoknak való megfelelőséget irányítópultján, javíthatja a megfelelőségi állapotáról feloldása ajánlások közvetlenül az irányítópulton belül.

1.  Kattintson végig bármelyik az adott javaslat részletes adatainak megtekintése az irányítópulton megjelenő sikertelen értékeléseket. Javaslatot tartalmaz a szükséges javítási lépések kell követni a probléma megoldásához.

2.  Kiválaszthatja, hogy egy adott erőforrás további részleteket, és végrehajthatja a javaslatokat az adott erőforráshoz. <br>Például a **Azure CIS standard** lapon, rákattinthat a javasolt **tárfiókba biztonságos átvitel megkövetelése**.

    ![Megfelelőségi javaslat](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. Kattintson végig a javaslat információkat és a egy nem megfelelő állapotú erőforrás kiválasztása vezet, közvetlenül a felhasználói élményt, hogy a **biztonságos tárolási átvitelre** az Azure Portalon.<br>Javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md).

    ![Megfelelőségi javaslat](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Miután javaslatok alkalmazásával, mert növeli a megfelelőségi pontszám látni fogja a gyakorolt hatás az eszközmegfelelőségi irányítópult jelentés.

    > [!NOTE]
    > Vizsgálat fut körülbelül 12 óránként, így láthatja a hatás a megfelelőségi adatok csak az értékelések futtatása után.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerhette a Security Center a jogszabályoknak való megfelelőség irányítópulttal:

-   Megtekintheti és figyelheti a megfelelőségi állapotáról, a szabványoknak és előírásoknak, amely fontos viszonyítva.

-   Javítása a megfelelőségi állapot feloldására vonatkozó javaslatok és a megfelelőségi pontszám javítása figyelése.

A szabályozásoknak való megfelelőséget irányítópult jelentősen leegyszerűsítik a megfelelőségi folyamat, és jelentősen kivágása az Azure-ban és a hibrid környezet megfelelőségi bizonyíték összegyűjtéséhez szükséges időt.

A Security Centerrel kapcsolatos további tudnivalókért lásd:

-   [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.

-   [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md)– ismerje meg, hogyan használható a javaslatok az Azure Security Centerben az Azure-erőforrások védelme érdekében.

-   [Az Azure Security Centerben a biztonságos pontszám javítása](security-center-secure-score.md)– ismerje meg, hogyan biztonsági réseket és a biztonsági javaslatok a legtöbb javíthatja biztonsági helyzetét.

-   [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
