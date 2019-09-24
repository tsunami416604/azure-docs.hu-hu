---
title: A szabályozás megfelelőségének javítása a Azure Security Center használatával | Microsoft Docs
description: 'Oktatóanyag: Ismerje meg, hogyan javíthatja a szabályozás megfelelőségét Azure Security Center használatával.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/30/2019
ms.author: memildin
ms.openlocfilehash: 20842997c5df81835024a6f458cd863b4e4d78b0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202428"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: Előírásoknak való megfelelés javítása
---

Azure Security Center megkönnyíti a szabályozási megfelelőségi követelmények teljesítésének folyamatát a szabályozási megfelelőségi irányítópult használatával. Az irányítópulton a Security Center az Azure-környezet folyamatos felmérése alapján betekintést nyújt a megfelelőségi helyzetbe. A Security Center által elvégzett értékelések az ajánlott biztonsági eljárásoknak megfelelően elemzik a hibrid felhőalapú környezet kockázati tényezőit. Ezek az értékelések a megfelelőségi vezérlőkre vannak leképezve a szabványok támogatott készletében. A szabályozási megfelelőségi irányítópulton egy adott szabvány vagy szabályozás kontextusában világosan áttekintheti a környezetében található összes értékelés állapotát. A javaslatok és a kockázati tényezők csökkentése érdekében a környezetében megtekintheti a megfelelőségi testhelyzetet.

Az oktatóanyag során a következőket fogja elsajátítani:

-   A szabályozás megfelelőségének kiértékelése a szabályzatoknak való megfelelőségi irányítópult használatával

-   A megfelelőségi helyzet javítása a javaslatok alapján

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő funkciók átlépéséhez Security Center Standard díjszabási szinttel kell rendelkeznie. Security Center Standard díjmentesen is kipróbálható.
További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](https://docs.microsoft.com/azure/security-center/security-center-get-started) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

##  <a name="assess-your-regulatory-compliance"></a>A szabályozás megfelelőségének felmérése

Security Center folyamatosan elemzi az erőforrások konfigurációját a biztonsági problémák és a biztonsági rések azonosítása érdekében. Ezeket az értékeléseket javaslatokként mutatjuk be, amelyek a biztonsági higiénia javítására összpontosítanak. A szabályozási megfelelőségi irányítópulton megtekintheti a megfelelőségi szabványokat az összes követelményével együtt, ahol a támogatott követelmények a megfelelő biztonsági értékelésekre vannak leképezve. Ez lehetővé teszi, hogy megtekintse a megfelelőségi helyzetet a standardra vonatkozóan, az értékelések állapota alapján.

A megfelelőségi irányítópult nézete segítséget nyújt az Ön számára fontos standard vagy rendeleti hiányosságok betartásában. Ez a célzott nézet azt is lehetővé teszi, hogy a dinamikus Felhőbeli és hibrid környezetekben is folyamatosan figyelje a megfelelőségi pontokat.

>[!NOTE]
> A jelenleg támogatott szabályozási szabványok a következők: Azure CIS, PCI DSS 3,2, ISO 27001 és SOC TSP. A fejlesztésekkel érkező további szabványok az irányítópulton fognak megjelenni.
1.  A Security Center főmenüben, a **házirend &AMP; megfelelőség** területen válassza a **jogszabályi megfelelőség**lehetőséget. <br>
A képernyő felső részén megjelenik egy irányítópult, amely áttekintést nyújt a megfelelőségi állapotáról a támogatott megfelelőségi szabályozások készletével együtt. Megtekintheti a teljes megfelelőségi pontszámot, valamint az egyes standardokhoz kapcsolódó elvégzési és sikertelen értékelések számát.

    ![számítógép leírása – magas megbízhatóság](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  Válassza ki az Önnek megfelelő megfelelési szabványhoz tartozó fület. Ekkor megjelenik az adott szabvány összes vezérlőjét tartalmazó lista. Az alkalmazható vezérlőknél megnézheti a hozzájuk kapcsolódó korábbi és sikertelen felméréseket. Egyes vezérlők kiszürkítve jelennek meg. Ezeknek a vezérlőknek nincs kapcsolódó Security Center-felmérése. Elemezni kell ezeket a követelményeket, és a saját környezetében kell értékelni őket. Ezek némelyike feldolgozható és nem technikai jellegű lehet.

    ![megfelelőség lap](./media/security-center-compliance-dashboard/compliance-pci.png)

3. A **minden** lapra kattintva megtekintheti az összes releváns Security Center javaslatot és a hozzájuk kapcsolódó szabványokat. Ez a nézet hasznos lehet az adott javaslat által érintett különböző szabványok azonosításához. <br> Ezt a nézetet használhatja a megoldáshoz szükséges javaslatok rangsorolására. Ha például azt látja, hogy az adott **előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező fiókok esetében az MFA engedélyezése** nem több erőforrásra vonatkozik, és több szabványhoz van társítva, akkor a javaslat feloldása nagy hatással lesz a következőre: a teljes megfelelőségi pontszám.

    ![megfelelőségi pontszám hatása](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

1. Egy adott szabvány aktuális megfelelőségi állapotát összefoglaló PDF-jelentés létrehozásához és letöltéséhez kattintson a **jelentés letöltése**elemre.

    A jelentés magas szintű összefoglalást biztosít a kiválasztott standard megfelelőségi állapotáról Security Center felmérési adatok alapján, és az adott szabvány vezérlőelemeinek megfelelően van rendszerezve. A jelentés megosztható az érintett felekkel, és a belső és külső könyvvizsgálók számára is biztosíthatja a bizonyítékokat.

    ![Letöltés](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>A megfelelőségi testhelyzet javítása

A szabályozás megfelelőségi irányítópultján található információk alapján a megfelelőségi állapotot a közvetlenül az irányítópulton belüli javaslatok megoldásával javíthatja.

1.  Az adott javaslat részleteinek megtekintéséhez kattintson az irányítópulton megjelenő sikertelen értékelések bármelyikére. Mindegyik javaslat olyan szervizelési lépéseket tartalmaz, amelyeket követni kell a probléma megoldásához.

2.  Kiválaszthat egy adott erőforrást a további részletek megtekintéséhez és az adott erőforráshoz tartozó javaslat megoldásához. <br>Az **Azure CIS standard** lapon például a javaslatra kattintva **biztonságos átvitelt igényelhet a Storage-fiókra**.

    ![megfelelőségi javaslat](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. Amikor rákattint az ajánlási információkra, és kijelöl egy nem megfelelő állapotú erőforrást, közvetlenül a Azure Portalon belül a **Biztonságos tár-átvitel** engedélyezésének élményét eredményezi.<br>Javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok alkalmazása az Azure Security Center](security-center-recommendations.md).

    ![megfelelőségi javaslat](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  Miután lépéseket tett a javaslatok megoldására, látni fogja a megfelelőségi irányítópult jelentésének hatását, mivel a megfelelőségi pontszám javul.

    > [!NOTE]
    > A felmérések körülbelül 12 óránként futnak le, tehát a megfelelési adatai csak a felmérések futtatása után tükrözik majd a változásokat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Security Center szabályozási megfelelőségi irányítópultját a következőre:

-   Megtekintheti és figyelheti a megfelelőségi testtartást az Ön számára fontos szabványokhoz és előírásokhoz képest.

-   Javítsa a megfelelőségi állapotot a kapcsolódó javaslatok feloldásával és a megfelelőségi pontszám javításával.

A szabályozási megfelelőségi irányítópult nagy mértékben leegyszerűsítheti a megfelelőségi folyamatot, és jelentős mértékben csökkentheti az Azure-és hibrid környezetek megfelelőségi igazolásának begyűjtéséhez szükséges időt.

A Security Centerról további információt a következő témakörben talál:

-   [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.

-   [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md)– megtudhatja, hogyan használhatók a javaslatok a Azure Security Centerban az Azure-erőforrások védelme érdekében.

-   [Javítsa a biztonságos pontszámát Azure Security Centerban](security-center-secure-score.md)– megtudhatja, hogyan rangsorolhatja a biztonsági réseket és a biztonsági javaslatokat a lehető leghatékonyabban.

-   [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
