---
title: 'Oktatóanyag: Szabályozási megfelelőségi ellenőrzések – Azure Security Center'
description: 'Oktatóanyag: Ismerje meg, hogyan javíthatja a szabályozási megfelelőséget az Azure Security Center használatával.'
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
ms.openlocfilehash: 1a6999c05c0b3dbaf572b376412f666c50c23df7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77604453"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Oktatóanyag: A jogszabályi megfelelőség javítása
---

Az Azure Security Center a **jogszabályi megfelelőségi irányítópult**használatával egyszerűsíti a szabályozási megfelelőségi követelmények teljesítésének folyamatát. Az irányítópulton a Security Center az Azure-környezet folyamatos felmérései alapján betekintést nyújt a megfelelőségi állapotba. A Security Center a hibrid felhőkörnyezetben lévő kockázati tényezőket a biztonsági ajánlott eljárásoknak megfelelően elemzi. Ezek az értékelések a támogatott szabványok megfelelőségi vezérlőihez vannak rendelve. A szabályozási megfelelőségi irányítópulton láthatja a környezetén belüli összes értékelés állapotát egy adott szabvány vagy szabályozás összefüggésében. Ahogy a javaslatok szerint cselekszik, és csökkenti a környezeti kockázati tényezőket, javítja a megfelelőségi pozíciót.

Az oktatóanyag során a következőket fogja elsajátítani:

-   A jogszabályi megfelelőség értékelése a Szabályozási megfelelőségirányítópult használatával

-   Javítsa a megfelelőségi pozíciót az ajánlásokra vonatkozó intézkedések nek megfelelően

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban szereplő funkciók on-át, a Security Center standard tarifacsomagjával kell rendelkeznie. Kipróbálhatja a Security Center Standard-ot díjmentesen.
További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](https://docs.microsoft.com/azure/security-center/security-center-get-started) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

##  <a name="assess-your-regulatory-compliance"></a>A jogszabályi megfelelőség felmérése

A Security Center folyamatosan felméri az erőforrások konfigurációját a biztonsági problémák és biztonsági rések azonosítására. Ezek az értékelések ajánlásokként jelennek meg, amelyek a biztonsági higiénia javítására összpontosítanak. A Szabályozási megfelelőség irányítópultján megtekintheti a megfelelőségi szabványok készletét az összes követelményüknek megfelelően, ahol a támogatott követelmények le vannak képezve a vonatkozó biztonsági értékelésekhez. Ez lehetővé teszi, hogy megtekintse a megfelelőségi pozíciót a szabványhoz képest, ezen értékelések állapota alapján.

A jogszabályi megfelelőségi irányítópult-nézet segítségével az Ön számára fontos szabványnak vagy szabályozásnak megfelelő hiányosságokra összpontosíthatja figyelmét. Ez a fókuszált nézet azt is lehetővé teszi, hogy folyamatosan figyelje a megfelelőségi pontszám az idő múlásával a dinamikus felhőbeli és hibrid környezetekben.

>[!NOTE]
> Alapértelmezés szerint a Security Center a következő szabályozási szabványokat támogatja: Azure CIS, PCI DSS 3.2, ISO 27001 és SOC TSP. 
>
> A [dinamikus megfelelőségi csomagok (előzetes verzió)](update-regulatory-compliance-packages.md) funkció lehetővé teszi, hogy frissítse a szabványok at a jogszabályi megfelelőségi irányítópulton az új *dinamikus* csomagok. Ugyanazzal az előzetes verzióval új megfelelőségi csomagokat is hozzáadhat, és figyelheti, hogy megfelel-e a további szabványoknak. 

1.  A Biztonsági központ főmenüjében, **a POLICY & COMPLIANCE** csoportban válassza a **Jogszabályi megfelelőség**lehetőséget. <br>
A képernyő tetején egy irányítópult látható, amely áttekintést nyújt a támogatott megfelelőségi előírásoknak való megfelelési állapotáról. Láthatja az általános megfelelőségi pontszám, és a tompított és sikertelen értékelések az egyes szabványokhoz társított száma.

    ![számítógép leírása nagy bizalommal](./media/security-center-compliance-dashboard/compliance-dashboard.png)

2.  Válassza ki az Önnek megfelelő megfelelési szabványhoz tartozó fület. Ekkor megjelenik az adott szabvány összes vezérlőjét tartalmazó lista. Az alkalmazható vezérlőknél megnézheti a hozzájuk kapcsolódó korábbi és sikertelen felméréseket. Egyes vezérlők szürkén jelennek meg. Ezekhez a vezérlőkhöz nincs biztonsági központ-értékelés társítva. Ellenőrizze ezek követelményeit, és saját környezetében mérsékelje őket. Ezek közül néhány folyamattal kapcsolatos lehet, és nem technikai jellegű.

    ![megfelelőség lap](./media/security-center-compliance-dashboard/compliance-pci.png)

1. Ha egy adott szabvány aktuális megfelelőségi állapotát összegző PDF-jelentést szeretne létrehozni és letölteni, kattintson a **Jelentés letöltése**gombra.

    A jelentés a Security Center értékelési adatai alapján magas szintű összegzést ad a kiválasztott szabvány megfelelőségi állapotáról, és az adott szabvány vezérlőinek megfelelően van rendszerezve. A jelentés megosztható az érintett érdekelt felekkel, és szolgálhat arra, hogy bizonyítékokat szolgáltasson a belső és külső ellenőröknek.

    ![letöltés](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>Javítja a megfelelőségi pozíciót

Tekintettel a szabályozási megfelelőségi irányítópulton található információkra, javíthatja a megfelelőségi állapotot azáltal, hogy közvetlenül az irányítópulton belül oldja meg a javaslatokat.

1.  Kattintson az irányítópulton megjelenő sikertelen értékelések bármelyikére az adott javaslat részleteinek megtekintéséhez. Minden javaslat tartalmaz egy sor javítási lépéseket, amelyeket követni kell a probléma megoldásához.

1.  Kiválaszthat egy adott erőforrást a további részletek megtekintéséhez és az adott erőforrásra vonatkozó javaslat feloldásához. <br>Például az **Azure CIS standard** lapon kattintson a javaslat **biztonságos átvitele a tárfiókba.**

    ![megfelelőségi ajánlás](./media/security-center-compliance-dashboard/compliance-recommendation.png)

1. Ahogy átkattint a javaslat információk és válasszon ki egy nem megfelelő állapotú erőforrás, ez közvetlenül az Azure Portalon belüli **biztonságos tárolás átvitelének** engedélyezéséhez vezet.

    A javaslatok alkalmazásáról a [Biztonsági javaslatok megvalósítása az Azure Security Centerben](security-center-recommendations.md)című témakörben talál további információt.

    ![megfelelőségi ajánlás](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

1.  Miután lépéseket tett a javaslatok feloldásához, látni fogja a megfelelőségi irányítópult-jelentésben a hatás hatását, mivel a megfelelőségi pontszám javul.

    > [!NOTE]
    > A felmérések körülbelül 12 óránként futnak le, tehát a megfelelési adatai csak a felmérések futtatása után tükrözik majd a változásokat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a rról, hogy miként használja a Security Center szabályozási megfelelőségi irányítópultját a következőkhöz:

-   Tekintse meg és figyelje a megfelelőségi állapotát az Ön számára fontos szabványokhoz és előírásokhoz viszonyítva.

-   Javítsa a megfelelőségi állapotot a vonatkozó javaslatok megoldásával és a megfelelőségi pontszám javításának figyelésével.

A szabályozási megfelelőségi irányítópult nagymértékben leegyszerűsítheti a megfelelőségi folyamatot, és jelentősen csökkentheti az Azure-hoz és a hibrid környezethez szükséges megfelelőségi bizonyítékok összegyűjtéséhez szükséges időt.

További információ: Lásd:

-   [Frissítsen dinamikus megfelelőségi csomagokra a szabályozási megfelelőségi irányítópulton (Előzetes verzió)](update-regulatory-compliance-packages.md) – Ismerje meg ezt az előzetes verziófunkciót, amely lehetővé teszi a jogszabályi megfelelőségi irányítópulton megjelenő szabványok frissítését az új *dinamikus* csomagokra. Ugyanezt az előzetes verziójú funkciót is hozzáadhatja új megfelelőségi csomagok hozzáadásához, és figyelheti, hogy megfelel-e a további szabványoknak. 

-   [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.

-   [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Ismerje meg, hogyan használhatja az Azure Security Center javaslatait az Azure-erőforrások védelme érdekében.

-   [Javítsa a biztonságos pontszámot az Azure Security Centerben](security-center-secure-score.md) – Ismerje meg, hogyan rangsorolhatja a biztonsági réseket és a biztonsági javaslatokat a biztonsági állapot javítása érdekében.