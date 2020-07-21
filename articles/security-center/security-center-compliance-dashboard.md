---
title: 'Oktatóanyag: jogszabályi megfelelőségi ellenőrzések – Azure Security Center'
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
ms.date: 11/12/2019
ms.author: memildin
ms.openlocfilehash: a636acab42d14925f507568e8a9fff4693c8f71c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519643"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: a szabályozás megfelelőségének javítása
---

Azure Security Center megkönnyíti a szabályozási megfelelőségi követelmények teljesítésének folyamatát a **szabályozási megfelelőségi irányítópult**használatával. Az irányítópulton a Security Center az Azure-környezet folyamatos felmérése alapján betekintést nyújt a megfelelőségi helyzetbe. A Security Center az ajánlott biztonsági eljárásoknak megfelelően elemzi a hibrid felhőalapú környezet kockázati tényezőit. Ezek az értékelések a megfelelőségi vezérlőkre vannak leképezve a szabványok támogatott készletében. A szabályozási megfelelőségi irányítópulton egy adott szabvány vagy szabályozás kontextusában tekintheti meg az összes értékelés állapotát a környezetében. A javaslatok és a környezet kockázati tényezőinek csökkentése érdekében a megfelelőségi testhelyzet javul.

Az oktatóanyag során a következőket fogja elsajátítani:

-   A szabályozás megfelelőségének kiértékelése a szabályzatoknak való megfelelőségi irányítópult használatával

-   A megfelelőségi helyzet javítása a javaslatok alapján

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő funkciók átlépéséhez Security Center Standard díjszabási szinttel kell rendelkeznie. Security Center Standard díjmentesen is kipróbálható.
További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](https://docs.microsoft.com/azure/security-center/security-center-get-started) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

##  <a name="assess-your-regulatory-compliance"></a>A szabályozás megfelelőségének felmérése

Security Center folyamatosan elemzi az erőforrások konfigurációját a biztonsági problémák és a biztonsági rések azonosítása érdekében. Ezeket az értékeléseket javaslatokként mutatjuk be, amelyek a biztonsági higiénia javítására összpontosítanak. A szabályozási megfelelőségi irányítópulton megtekintheti a megfelelőségi szabványokat az összes követelményével együtt, ahol a támogatott követelmények a megfelelő biztonsági értékelésekre vannak leképezve. Ez lehetővé teszi, hogy megtekintse a megfelelőségi helyzetet a standardra vonatkozóan, az értékelések állapota alapján.

A megfelelőségi irányítópult nézete segítséget nyújt az Ön számára fontos standard vagy rendeleti hiányosságok betartásában. Ez a célzott nézet azt is lehetővé teszi, hogy a dinamikus Felhőbeli és hibrid környezetekben is folyamatosan figyelje a megfelelőségi pontokat.

>[!NOTE]
> Alapértelmezés szerint a Security Center a következő szabályozási szabványokat támogatja: Azure CIS, PCI DSS 3,2, ISO 27001 és SOC TSP. 
>
> A [dinamikus megfelelőségi csomagok (előzetes verzió)](update-regulatory-compliance-packages.md) funkció lehetővé teszi a szabályozási megfelelőségi irányítópulton látható szabványok frissítését az új *dinamikus* csomagokra. Ugyanezt az előzetes verziójú szolgáltatást használhatja új megfelelőségi csomagok hozzáadására és a megfelelőség további szabványokkal való figyelésére. 

1.  A Security Center főmenüben, a **házirend & megfelelőség** területen válassza a **jogszabályi megfelelőség**lehetőséget. <br>
A képernyő felső részén megjelenik egy irányítópult, amely áttekintést nyújt a megfelelőségi állapotáról a támogatott megfelelőségi szabályozások készletével együtt. Megtekintheti a teljes megfelelőségi pontszámot, valamint az egyes standardokhoz kapcsolódó elvégzési és sikertelen értékelések számát.

    ![számítógép leírása – magas megbízhatóság](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Válassza ki az Önnek megfelelő megfelelési szabványhoz tartozó fület. Ekkor megjelenik az adott szabvány összes vezérlőjét tartalmazó lista. Az alkalmazható vezérlőknél megnézheti a hozzájuk kapcsolódó korábbi és sikertelen felméréseket. Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nem tartozik Security Center Értékelés. Vizsgálja meg ezeket a követelményeket, és mérje fel azokat a saját környezetében. Ezek némelyike feldolgozható és nem technikai jellegű lehet.

    ![megfelelőség lap](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Egy adott szabvány aktuális megfelelőségi állapotát összefoglaló PDF-jelentés létrehozásához és letöltéséhez kattintson a **jelentés letöltése**elemre.

    A jelentés magas szintű összefoglalást biztosít a kiválasztott standard megfelelőségi állapotáról Security Center felmérési adatok alapján, és az adott szabvány vezérlőelemeinek megfelelően van rendszerezve. A jelentés megosztható az érintett felekkel, és a belső és külső könyvvizsgálók számára is biztosíthatja a bizonyítékokat.

    ![letöltés](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>A megfelelőségi testhelyzet javítása

A szabályozás megfelelőségi irányítópultján található információk alapján a megfelelőségi állapotot a közvetlenül az irányítópulton belüli javaslatok megoldásával javíthatja.

1.  Az adott javaslat részleteinek megtekintéséhez kattintson az irányítópulton megjelenő sikertelen értékelések bármelyikére. Mindegyik javaslat olyan szervizelési lépéseket tartalmaz, amelyeket követni kell a probléma megoldásához.

1.  Kiválaszthat egy adott erőforrást a további részletek megtekintéséhez és az adott erőforráshoz tartozó javaslat megoldásához. <br>Az **Azure CIS standard** lapon például a javaslatra kattintva **biztonságos átvitelt igényelhet a Storage-fiókra**.

    ![megfelelőségi javaslat](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Amikor rákattint az ajánlási információkra, és kijelöl egy nem megfelelő állapotú erőforrást, közvetlenül a Azure Portalon belül a **Biztonságos tár-átvitel** engedélyezésének élményét eredményezi.

    A javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).

    ![megfelelőségi javaslat](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Miután lépéseket tett a javaslatok megoldására, látni fogja a megfelelőségi irányítópult jelentésének hatását, mivel a megfelelőségi pontszám javul.

    > [!NOTE]
    > A felmérések körülbelül 12 óránként futnak le, tehát a megfelelési adatai csak a felmérések futtatása után tükrözik majd a változásokat.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Security Center szabályozási megfelelőségi irányítópultját a következőre:

-   Megtekintheti és figyelheti a megfelelőségi testtartást az Ön számára fontos szabványokhoz és előírásokhoz képest.

-   Javítsa a megfelelőségi állapotot a kapcsolódó javaslatok feloldásával és a megfelelőségi pontszám javításával.

A szabályozási megfelelőségi irányítópult nagy mértékben leegyszerűsítheti a megfelelőségi folyamatot, és jelentős mértékben csökkentheti az Azure-és hibrid környezetek megfelelőségi igazolásának begyűjtéséhez szükséges időt.

További információ:

-   [Frissítés dinamikus megfelelőségi csomagokra a szabályozási megfelelőségi irányítópulton (előzetes verzió)](update-regulatory-compliance-packages.md) – Ismerje meg ezt az előzetes funkciót, amely lehetővé teszi, hogy frissítse a szabályozási megfelelőségi irányítópulton látható szabványokat az új *dinamikus* csomagokra. Ugyanezt az előzetes verziójú szolgáltatást is használhatja új megfelelőségi csomagok hozzáadásához és a megfelelőség további szabványokkal való monitorozásához. 

-   A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.

-   [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan használhatók a javaslatok a Azure Security Centerban az Azure-erőforrások védelme érdekében.

-   [Javítsa a Azure Security Center biztonságos pontszámát](secure-score-security-controls.md) – Ismerje meg, hogyan rangsorolhatja a biztonsági réseket és a biztonsági javaslatokat, hogy a leghatékonyabban javítsa a biztonságot.