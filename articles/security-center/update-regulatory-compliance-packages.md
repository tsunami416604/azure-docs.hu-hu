---
title: A dinamikus szabályozási megfelelőség monitorozásának frissítése a Azure Security Center szabályozási megfelelőségi irányítópulton | Microsoft Docs
description: A szabályozási megfelelőségi csomagok frissítése (előzetes verzió)
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
ms.openlocfilehash: f76b5443d6c1e3fd2cebf87cba39ba0a6bbdeaee
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521786"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>Frissítés dinamikus megfelelőségi csomagokra a szabályozási megfelelőségi irányítópulton (előzetes verzió)

Azure Security Center folyamatosan összehasonlítja az erőforrások konfigurációját az iparági szabványok, rendeletek és referenciaértékek követelményeivel. A **szabályozási megfelelőségi irányítópult** a megfelelőségi ellenőrzés és a követelmények teljesítése alapján nyújt betekintést a megfelelőségi helyzetbe.

Egy standard, amely nyomon követheti a megfelelőségi testtartást az [Azure CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) (a "CIS Microsoft Azure founds benchmark Version 1.1.0"). 

A megfelelőségi irányítópulton eredetileg megjelenő Azure CIS-beli ábrázolás a Security Centerban található szabályok statikus halmazán alapul.

A **dinamikus megfelelőségi csomagok (előzetes verzió)** szolgáltatással a Security Center automatikusan javítja az iparági szabványok lefedettségét az idő múlásával. A megfelelőségi csomagok lényegében a Azure Policyban meghatározott kezdeményezések. Ezek hozzárendelhetők a kiválasztott hatókörhöz (előfizetés, felügyeleti csoport stb.). Ha szeretné megtekinteni a megfelelőségi adatok hozzárendelését az irányítópulton, vegyen fel egy megfelelőségi csomagot a felügyeleti csoportba vagy előfizetésbe a biztonsági szabályzatból. A megfelelőségi csomag hozzáadásával hatékonyan hozzárendelheti a szabályozási megfelelőségi kezdeményezést a kiválasztott hatókörhöz. Így nyomon követheti az újonnan közzétett szabályozási kezdeményezéseket megfelelőségi szabványként az irányítópulton. Ha a Microsoft új tartalmat szabadít fel a kezdeményezéshez (új szabályzatok, amelyek a standardban több vezérlőhöz képezhetők le), a további tartalom automatikusan megjelenik az irányítópulton.

Az Azure CIS benchmark, az **Azure CIS 1.1.0 (új)** dinamikus megfelelőségi csomagja az eredeti *statikus* verziót javítja:

* Több szabályzatot is beleértve
* Automatikus frissítés új lefedettséggel, ahogy hozzá van adva 

Frissítsen az új dinamikus csomagra az alább leírtak szerint.

## <a name="adding-a-dynamic-compliance-package"></a>Dinamikus megfelelőségi csomag hozzáadása

A következő lépések azt ismertetik, hogyan adható hozzá a dinamikus csomag az Azure CIS benchmark v 1.1.0 megfelelőségének figyeléséhez.   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Frissítés az Azure CIS 1.1.0 (új) dinamikus megfelelőségi csomagra 

1. Nyissa meg a **biztonsági házirend** lapot. Ez az oldal a felügyeleti csoportok, előfizetések, munkaterületek és a felügyeleti csoport struktúrájának számát jeleníti meg.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez felügyelni kívánja a szabályozási megfelelőségi állapotot. Azt javasoljuk, hogy válassza ki a legmagasabb hatókört, amelyre a standard érvényes, hogy a megfelelőségi adatokat összesítsék és nyomon kövessék az összes beágyazott erőforrás esetében. 

1. Az iparági & szabályozási szabványok (előzetes verzió) szakaszban láthatja, hogy az Azure CIS 1.1.0 új tartalomhoz is frissíthető. Kattintson a **Frissítés most**elemre. 

1. Ha szeretné, kattintson a **további szabványok hozzáadása** lehetőségre a **jogszabályi megfelelőségi szabványok hozzáadása** lap megnyitásához. Itt megkeresheti az **Azure CIS 1.1.0 (új)** és a dinamikus csomagokat az egyéb megfelelőségi szabványokhoz, mint például a **NIST SP 800-53 R4**, a **Swift CSP CSCF-v2020**, a **UKO és az Egyesült királyságbeli NHS**és a **Canada PBMM**.
    
    ![Szabályozási csomagok hozzáadása Azure Security Center szabályozási megfelelőségi irányítópulthoz](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. A Security Center oldalsávján válassza a **jogszabályi megfelelőség** lehetőséget a megfelelőségi irányítópult megnyitásához. 
    * Az Azure CIS 1.1.0 (új) mostantól megjelenik az iparági & szabályozási szabványainak listáján. 
    * Az Azure CIS 1.1.0 megfelelőségének eredeti *statikus* nézete is továbbra is fennáll. Előfordulhat, hogy a későbbiekben automatikusan el lesz távolítva.

    > [!NOTE]
    > A megfelelőségi irányítópulton néhány órát is igénybe vehet, hogy egy újonnan hozzáadott szabvány megjelenjen.


    [![szabályozási megfelelőségi irányítópult a régi és az új Azure CIS-t ábrázolva](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta a következőket:

* A szabályozási megfelelőségi irányítópulton látható **szabványok frissítése** az új *dinamikus* csomagokra
* **Megfelelőségi csomagok hozzáadása** a további szabványoknak való megfelelőség monitorozásához. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikkeket: 

- [A Security Center szabályozási megfelelőségi irányítópultja](security-center-compliance-dashboard.md)
- [A biztonsági szabályzatok használata](tutorial-security-policy.md)
- [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan használhatók a javaslatok a Azure Security Centerban az Azure-erőforrások védelme érdekében.
- [Azure Security Center GYIK](security-center-faq.md) – választ kaphat a Security Center használatával kapcsolatos gyakori kérdésekre.