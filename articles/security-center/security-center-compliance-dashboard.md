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
ms.date: 10/11/2020
ms.author: memildin
ms.openlocfilehash: bbc36dbb2a17d379d31a9a235898500aea36247d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533910"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: Jogszabályi megfelelés javítása

Azure Security Center megkönnyíti a szabályozási megfelelőségi követelmények teljesítésének folyamatát a **szabályozási megfelelőségi irányítópult** használatával. Az irányítópulton a Security Center az Azure-környezet folyamatos felmérése alapján betekintést nyújt a megfelelőségi helyzetbe. A Security Center az ajánlott biztonsági eljárásoknak megfelelően elemzi a hibrid felhőalapú környezet kockázati tényezőit. Ezek az értékelések a megfelelőségi vezérlőkre vannak leképezve a szabványok támogatott készletében. A szabályozási megfelelőségi irányítópulton egy adott szabvány vagy szabályozás kontextusában tekintheti meg az összes értékelés állapotát a környezetében. A javaslatok és a környezet kockázati tényezőinek csökkentése érdekében a megfelelőségi testhelyzet javul.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A szabályozás megfelelőségének kiértékelése a szabályzatoknak való megfelelőségi irányítópult használatával
> * A megfelelőségi helyzet javítása a javaslatok alapján

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban tárgyalt funkciók átlépéséhez:

- Az [Azure Defender](azure-defender.md) szolgáltatást engedélyezni kell. 30 napig ingyenesen kipróbálhatja az Azure Defendert.
- Be kell jelentkeznie egy olyan fiókkal, amely rendelkezik olvasói hozzáféréssel a szabályzat megfelelőségi információhoz (a **biztonsági olvasó** nem elegendő). Az előfizetés **globális olvasójának** szerepe fog működni. Legalább az **erőforrás-házirend közreműködői** és a **biztonsági rendszergazdai** szerepkörök hozzárendelésére van szükség.

##  <a name="assess-your-regulatory-compliance"></a>A szabályozás megfelelőségének felmérése

Security Center folyamatosan elemzi az erőforrások konfigurációját a biztonsági problémák és a biztonsági rések azonosítása érdekében. Ezeket az értékeléseket javaslatokként mutatjuk be, amelyek a biztonsági higiénia javítására összpontosítanak. A szabályozási megfelelőségi irányítópulton megtekintheti a megfelelőségi szabványokat az összes követelményével együtt, ahol a támogatott követelmények a megfelelő biztonsági értékelésekre vannak leképezve. Ez lehetővé teszi, hogy megtekintse a megfelelőségi helyzetet a standardra vonatkozóan, az értékelések állapota alapján.

A megfelelőségi irányítópult nézete segítséget nyújt az Ön számára fontos standard vagy rendeleti hiányosságok betartásában. Ez a célzott nézet azt is lehetővé teszi, hogy a dinamikus Felhőbeli és hibrid környezetekben is folyamatosan figyelje a megfelelőségi pontokat.

>[!NOTE]
> Alapértelmezés szerint a Security Center a következő szabályozási szabványokat támogatja: Azure CIS, PCI DSS 3,2, ISO 27001 és SOC TSP. 
>
> A [dinamikus megfelelőségi csomagok (előzetes verzió)](update-regulatory-compliance-packages.md) funkció lehetővé teszi a szabályozási megfelelőségi irányítópulton látható szabványok frissítését az új *dinamikus* csomagokra. Ugyanezt az előzetes verziójú szolgáltatást használhatja új megfelelőségi csomagok hozzáadására és a megfelelőség további szabványokkal való figyelésére. 

1. A Security Center menüjében válassza a **jogszabályi megfelelőség** lehetőséget. <br>
A képernyő felső részén megjelenik egy irányítópult, amely áttekintést nyújt a megfelelőségi állapotáról a támogatott megfelelőségi szabályozások készletével együtt. Megtekintheti a teljes megfelelőségi pontszámot, valamint az egyes standardokhoz kapcsolódó elvégzési és sikertelen értékelések számát.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Szabályozási megfelelőségi irányítópult":::

1. Válassza ki az Önnek megfelelő megfelelőségi szabványhoz tartozó fület (1). Láthatja, hogy mely előfizetések lesznek alkalmazva a standard alkalmazásra (2), valamint az adott standardhoz tartozó összes vezérlő (3) listáját. A megfelelő vezérlők esetében megtekintheti a vezérlőhöz társított átadási és sikertelen értékelések részleteit (4), valamint az érintett erőforrások számát (5). Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nem tartozik Security Center Értékelés. Vizsgálja meg ezeket a követelményeket, és mérje fel azokat a saját környezetében. Ezek némelyike feldolgozható és nem technikai jellegű lehet.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Egy adott szabványnak való megfelelés részleteinek feltárása":::

1. Egy adott szabvány aktuális megfelelőségi állapotát összefoglaló PDF-jelentés létrehozásához és letöltéséhez kattintson a **jelentés letöltése** elemre.

    A jelentés magas szintű összefoglalást biztosít a kiválasztott standard megfelelőségi állapotáról Security Center felmérési adatok alapján, és az adott szabvány vezérlőelemeinek megfelelően van rendszerezve. A jelentés megosztható az érintett felekkel, és a belső és külső könyvvizsgálók számára is igazolhatja.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Megfelelőségi jelentés letöltése":::

## <a name="improve-your-compliance-posture"></a>A megfelelőségi testhelyzet javítása

A szabályozás megfelelőségi irányítópultján található információk alapján a megfelelőségi állapotot a közvetlenül az irányítópulton belüli javaslatok megoldásával javíthatja.

1.  Az adott javaslat részleteinek megtekintéséhez kattintson az irányítópulton megjelenő sikertelen értékelések bármelyikére. Mindegyik javaslat olyan szervizelési lépéseket tartalmaz, amelyeket követni kell a probléma megoldásához.

1.  Kiválaszthat egy adott erőforrást a további részletek megtekintéséhez és az adott erőforráshoz tartozó javaslat megoldásához. <br>Az **Azure CIS 1.1.0 (új) szabványban** például kiválaszthatja, hogy a rendszer hogyan **alkalmazza a lemez titkosítását a virtuális gépekre**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="A standard érdeklődők javaslatának kiválasztása közvetlenül a javaslat részleteit tartalmazó oldalra":::

1. Ebben a példában, ha a javaslat részletei lapon a **művelet elvégzése** lehetőséget választja, akkor a Azure Portal Azure-beli virtuális gépek oldalain érkeznek, ahol megnyithatja a **Biztonság** lapot, és engedélyezheti a titkosítást:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="A javaslat részletei lapon található művelet gomb a Szervizelési beállításokhoz vezet":::

    A javaslatok alkalmazásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).

1.  Miután lépéseket tett a javaslatok megoldására, látni fogja a megfelelőségi irányítópult jelentésének hatását, mivel a megfelelőségi pontszám javul.

    > [!NOTE]
    > Az értékelések körülbelül 12 óránként futnak, így a megfelelőségi adatokat csak a megfelelő értékelés következő futtatása után fogja látni.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Security Center szabályozási megfelelőségi irányítópultját a következőre:

-   Megtekintheti és figyelheti a megfelelőségi testtartást az Ön számára fontos szabványokhoz és előírásokhoz képest.
-   Javítsa a megfelelőségi állapotot a kapcsolódó javaslatok feloldásával és a megfelelőségi pontszám javításával.

A szabályozási megfelelőségi irányítópult nagy mértékben leegyszerűsítheti a megfelelőségi folyamatot, és jelentős mértékben csökkentheti az Azure-és hibrid környezetek megfelelőségi igazolásának begyűjtéséhez szükséges időt.

További információt a következő kapcsolódó cikkekben talál:

-   [Frissítés dinamikus megfelelőségi csomagokra a szabályozási megfelelőségi irányítópulton (előzetes verzió)](update-regulatory-compliance-packages.md) – Ismerje meg ezt az előzetes funkciót, amely lehetővé teszi, hogy frissítse a szabályozási megfelelőségi irányítópulton látható szabványokat az új *dinamikus* csomagokra. Ugyanezt az előzetes verziójú szolgáltatást is használhatja új megfelelőségi csomagok hozzáadásához és a megfelelőség további szabványokkal való monitorozásához. 
-   A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.
-   [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan használhatók a javaslatok a Azure Security Centerban az Azure-erőforrások védelme érdekében.
-   [Javítsa a Azure Security Center biztonságos pontszámát](secure-score-security-controls.md) – Ismerje meg, hogyan rangsorolhatja a biztonsági réseket és a biztonsági javaslatokat, hogy a leghatékonyabban javítsa a biztonságot.
