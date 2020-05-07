---
title: A dinamikus szabályozási megfelelőség monitorozásának frissítése a Azure Security Center szabályozási megfelelőségi irányítópulton | Microsoft Docs
description: A szabályozási megfelelőségi csomagok frissítése
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 828357ef9dcb6a59534ede2a2843fc2cb5eaed5e
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598341"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>Frissítés a dinamikus megfelelőségi csomagokra a szabályozási megfelelőségi irányítópulton

Azure Security Center folyamatosan összehasonlítja az erőforrások konfigurációját az iparági szabványok, rendeletek és referenciaértékek követelményeivel. A **szabályozási megfelelőségi irányítópult** a megfelelőségi ellenőrzés és a követelmények teljesítése alapján nyújt betekintést a megfelelőségi helyzetbe.

A **dinamikus megfelelőségi csomagok** szolgáltatással a Security Center *automatikusan javítja az iparági szabványok lefedettségét az idő múlásával*. 

Egy standard, amely nyomon követheti a megfelelőségi testtartást az [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (a "CIS Microsoft Azure founds benchmark Version 1.1.0"). A megfelelőségi irányítópulton eredetileg megjelenő Azure CIS-beli ábrázolás a Security Centerban található szabályok statikus halmazán alapul.

A megfelelőségi csomagok lényegében a Azure Policyban meghatározott kezdeményezések. Ezek hozzárendelhetők a kiválasztott hatókörhöz (előfizetés, felügyeleti csoport stb.). Ha szeretné megtekinteni a megfelelőségi adatok hozzárendelését az irányítópulton, vegyen fel egy megfelelőségi csomagot a felügyeleti csoportba vagy előfizetésbe a biztonsági szabályzatból. A megfelelőségi csomag hozzáadásával hatékonyan hozzárendelheti a szabályozási megfelelőségi kezdeményezést a kiválasztott hatókörhöz. Így nyomon követheti az újonnan közzétett szabályozási kezdeményezéseket megfelelőségi szabványként az irányítópulton. Ha a Microsoft új tartalmat szabadít fel a kezdeményezéshez (új szabályzatok, amelyek a standardban több vezérlőhöz képezhetők le), a további tartalom automatikusan megjelenik az irányítópulton.

Az Azure CIS benchmark, az **Azure CIS 1.1.0 (új)** dinamikus megfelelőségi csomagja az eredeti *statikus* verziót javítja:

* Több szabályzatot is beleértve
* Automatikus frissítés új lefedettséggel, ahogy hozzá van adva 

Frissítsen az új dinamikus csomagra az alább leírtak szerint.

## <a name="adding-a-dynamic-compliance-package"></a>Dinamikus megfelelőségi csomag hozzáadása

A következő lépések azt ismertetik, hogyan adható hozzá a dinamikus csomag az Azure CIS benchmark v 1.1.0 megfelelőségének figyeléséhez.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Frissítés az Azure CIS 1.1.0 (új) dinamikus megfelelőségi csomagra 

1. A Security Center oldalsávján válassza a **jogszabályi megfelelőség** lehetőséget a megfelelőségi irányítópult megnyitásához. Itt láthatja az aktuálisan kijelölt előfizetésekhez jelenleg hozzárendelt megfelelőségi szabványokat.   

1. A lap tetején válassza a **megfelelőségi szabályzatok kezelése**lehetőséget. Ekkor megnyílik a házirend-kezelés lap.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez felügyelni kívánja a szabályozási megfelelőségi állapotot. 

    > [!TIP]
    > Azt javasoljuk, hogy válassza ki a legmagasabb hatókört, amelyre a standard érvényes, hogy a megfelelőségi adatokat összesítsék és nyomon kövessék az összes beágyazott erőforrás esetében. 

1. Az iparági & szabályozási szabványok szakaszban láthatja, hogy az Azure CIS 1.1.0 új tartalomhoz is frissíthető. Kattintson a **Frissítés most**elemre. 

1. Ha szeretné, kattintson a **további szabványok hozzáadása** lehetőségre a **jogszabályi megfelelőségi szabványok hozzáadása** lap megnyitásához. Itt megkeresheti az **Azure CIS 1.1.0 (új)** és a dinamikus csomagokat az egyéb megfelelőségi szabványokhoz, például a következőkhöz:

    - **Azure biztonsági teljesítményteszt** ([részletek itt](https://docs.microsoft.com/azure/security/benchmarks/introduction))
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF – v2020**
    - **UKO és egyesült királysági NHS**
    - **Kanadai PBMM**
    
    > [!TIP]
    > A megfelelőségi szabványok hozzáadásához csak a tulajdonosi vagy a házirend közreműködő felhasználók rendelkeznek a szükséges engedélyekkel. 

    ![Szabályozási csomagok hozzáadása Azure Security Center szabályozási megfelelőségi irányítópulthoz](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)


1. A Security Center oldalsávján válassza a **szabályozás megfelelősége** ismét lehetőséget a megfelelőségi irányítópultra való visszatéréshez.
    * Az **Azure CIS 1.1.0 (új)** mostantól megjelenik az iparági & szabályozási szabványainak listáján. 
    * Az Azure CIS 1.1.0 megfelelőségének eredeti *statikus* nézete is továbbra is fennáll. Előfordulhat, hogy a későbbiekben automatikusan el lesz távolítva.

    > [!NOTE]
    > A megfelelőségi irányítópulton néhány órát is igénybe vehet, hogy egy újonnan hozzáadott szabvány megjelenjen.


    [![A korábbi és az új Azure CIS-t bemutató szabályozási megfelelőségi irányítópult](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta a következőket:

* A szabályozási megfelelőségi irányítópulton látható **szabványok frissítése** az új *dinamikus* csomagokra
* **Megfelelőségi csomagok hozzáadása** a további szabványoknak való megfelelőség monitorozásához. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikkeket: 

- [A Security Center szabályozási megfelelőségi irányítópultja](security-center-compliance-dashboard.md)
- [Biztonsági szabályzatok használata](tutorial-security-policy.md)
- [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan használhatók a javaslatok a Azure Security Centerban az Azure-erőforrások védelme érdekében.