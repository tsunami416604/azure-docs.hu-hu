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
ms.openlocfilehash: 28b6a70297efb8d8237f085e1f5b08dbb6a09072
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522606"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>A szabályzatok megfelelőségi irányítópultján lévő szabványok testreszabása

Azure Security Center folyamatosan összehasonlítja az erőforrások konfigurációját az iparági szabványok, rendeletek és referenciaértékek követelményeivel. A **szabályozási megfelelőségi irányítópult** a megfelelőségi ellenőrzés és a követelmények teljesítése alapján nyújt betekintést a megfelelőségi helyzetbe.


## <a name="overview-of-compliance-packages"></a>A megfelelőségi csomagok áttekintése

Az iparági szabványok, a szabályozási szabványok és a referenciaértékek a Security Center *megfelelőségi csomagokként*jelennek meg.  Minden csomag egy Azure Policyban definiált kezdeményezés. Ha szeretné megtekinteni a megfelelőségi adatok hozzárendelését az irányítópulton, vegyen fel egy megfelelőségi csomagot a felügyeleti csoportba vagy előfizetésbe a **biztonsági házirend** lapon. (További információ a [biztonsági házirendek](tutorial-security-policy.md)használatáról Azure Policy és kezdeményezésekről.)

Ha a kiválasztott hatókörhöz standard vagy teljesítménytesztet állított be, akkor az a hatókörhöz rendeli a kezdeményezést, és a szabvány a megfelelőségi irányítópulton jelenik meg, amely az értékelésként hozzárendelt összes megfelelőségi adattal rendelkezik. Az összegző jelentéseket az előkészített szabványok bármelyikére is letöltheti.

A Microsoft emellett saját maga is nyomon követi a szabályozási szabványokat, és az egyes csomagokban az idő múlásával automatikusan javítja annak lefedettségét. Ha a Microsoft új tartalmat szabadít fel a kezdeményezéshez (új szabályzatok, amelyek a standardban több vezérlőhöz képezhetők le), a további tartalom automatikusan megjelenik az irányítópulton.

> [!TIP]
> Az egyik standard, amely az idő múlásával javul, mivel a Microsoft új tartalmat szabadít fel az **Azure CIS 1.1.0 (új)** (a [CIS Microsoft Azure founds benchmark Version 1.1.0](https://www.cisecurity.org/benchmark/azure/)). Ezt hozzá kell adnia az irányítópulthoz az "Azure CIS 1.1.0" mellett, amely az Azure CIS-t mutatja be, amely alapértelmezés szerint minden Security Center környezetben konfigurálva van. Ez a csomag a szabályok statikus készletére támaszkodik. Az újabb csomag több szabályzatot is tartalmaz, és az idő múlásával automatikusan frissül. Frissítsen az új dinamikus csomagra az alább leírtak szerint.


## <a name="available-packages"></a>Elérhető csomagok

Olyan szabványokat adhat hozzá, mint például a NIST SP 800-53 R4, a SWIFT CSP CSCF-v2020, az Egyesült Királyság hivatalos és az Egyesült királyságbeli NHS, Kanada szövetségi PBMM és az Azure CIS 1.1.0 (új) – az Azure CIS 1.1.0 teljesebb körű bemutatása. 

Emellett a közös megfelelőségi keretrendszereken alapuló, a Microsoft által készített, az Azure-ra vonatkozó biztonsági és megfelelőségi irányelvek is hozzáadhatók az **Azure**-hoz. (További[információ az Azure biztonsági teljesítménytesztről](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

Az irányítópulton további szabványok is támogatottak lesznek, amint azok elérhetővé válnak. 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>Szabályozási szabvány hozzáadása az irányítópulthoz

A következő lépések azt ismertetik, hogyan adhat hozzá egy csomagot a megfelelőség monitorozásához a támogatott szabályozási szabványok egyikével.

> [!NOTE]
> A megfelelőségi szabványok hozzáadásához csak a tulajdonosi vagy a házirend közreműködő felhasználók rendelkeznek a szükséges engedélyekkel. 

1. A Security Center oldalsávján válassza a **jogszabályi megfelelőség** lehetőséget a megfelelőségi irányítópult megnyitásához. Itt láthatja az aktuálisan kijelölt előfizetésekhez jelenleg hozzárendelt megfelelőségi szabványokat.   

1. A lap tetején válassza a **megfelelőségi szabályzatok kezelése**lehetőséget. Megjelenik a házirend-kezelés lap.

1. Válassza ki azt az előfizetést vagy felügyeleti csoportot, amelyhez felügyelni kívánja a szabályozási megfelelőségi állapotot. 

    > [!TIP]
    > Azt javasoljuk, hogy válassza ki a legmagasabb hatókört, amelyre a standard érvényes, hogy a megfelelőségi adatokat összesítsék és nyomon kövessék az összes beágyazott erőforrás esetében. 

1. A szervezethez kapcsolódó szabványok hozzáadásához kattintson a **további szabványok hozzáadása**lehetőségre. 

1. A **jogszabályi megfelelőségi szabványok hozzáadása** lapon bármely elérhető szabványhoz kereshet csomagokat. A rendelkezésre álló szabványok némelyike:

    - **Azure-biztonsági teljesítményteszt**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF – v2020**
    - **UKO és egyesült királysági NHS**
    - **Kanadai PBMM**
    
    ![Szabályozási csomagok hozzáadása Azure Security Center szabályozási megfelelőségi irányítópulthoz](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Válassza a **Hozzáadás** lehetőséget, és adja meg az adott kezdeményezéshez szükséges összes adatot, például a hatókört, a paramétereket és a szervizelést.

1. A Security Center oldalsávján válassza a **szabályozás megfelelősége** ismét lehetőséget a megfelelőségi irányítópultra való visszatéréshez.
    * Az új szabvány megjelenik az iparági & szabályozási szabványainak listájában. 
    * Ha hozzáadta az **Azure CIS 1.1.0-t (új)**, az Azure CIS 1.1.0 megfelelőségének eredeti *statikus* nézete is továbbra is fennáll. Előfordulhat, hogy a későbbiekben automatikusan el lesz távolítva.

    > [!NOTE]
    > A megfelelőségi irányítópulton néhány órát is igénybe vehet, hogy egy újonnan hozzáadott szabvány megjelenjen.

    [![A korábbi és az új Azure CIS-t bemutató szabályozási megfelelőségi irányítópult](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan **adhat hozzá megfelelőségi csomagokat** a megfelelőség további szabványokkal való figyeléséhez. 

Más kapcsolódó anyagok esetében tekintse meg a következő cikkeket: 

- [Azure-biztonsági teljesítményteszt](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [A Security Center szabályozási megfelelőségi irányítópultja](security-center-compliance-dashboard.md)
- [Biztonsági szabályzatok használata](tutorial-security-policy.md)