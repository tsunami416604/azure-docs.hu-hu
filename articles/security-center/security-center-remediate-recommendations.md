---
title: Javaslatok szervizelése Azure Security Centerban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan javíthatja a javaslatokat a Azure Security Centerban az erőforrások védelme és a biztonsági szabályzatok betartása érdekében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 3d4b968d0d5e091d72cf718d58b11ade68ddf8c6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637088"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Helyreállítási javaslatok az Azure Security Centerben

A javaslatok segítséget nyújtanak az erőforrások jobb biztonságossá tételéhez. A javaslatot a javaslatban megadott szervizelési lépések követésével hajthatja végre.

## <a name="remediation-steps"></a>Szervizelési lépések <a name="remediation-steps"></a>

Az összes javaslat áttekintése után döntse el, hogy melyiket kell elsőként kijavítani. Javasoljuk, hogy a [biztonságos pontszám hatása](security-center-recommendations.md#monitor-recommendations) alapján rangsorolja, hogy mi az első.

1. A listában kattintson a javaslatra.

1. Kövesse a **szervizelési lépések** szakaszban található utasításokat. Minden javaslathoz saját utasítások vannak megadva. Az alábbi képernyőképen az alkalmazások konfigurálásához szükséges szervizelési lépések láthatók a HTTPS protokollon keresztüli adatforgalom engedélyezéséhez.

    ![Javaslat részletei](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, ha a szervizelés sikeres volt.

## <a name="quick-fix-remediation"></a>Gyors javítás szervizelése<a name="one-click"></a>

A gyors javítás leegyszerűsíti a szervizelést, és lehetővé teszi a biztonsági pontszám gyors növelését, a környezet biztonságának javítását.

A gyors javítás lehetővé teszi, hogy gyorsan javítsa a több erőforrásra vonatkozó javaslatait. 

> [!TIP]
> A gyors javítás csak bizonyos javaslatok esetében érhető el. A gyors javítási lehetőséggel rendelkező javaslatok megkereséséhez használja a javaslatok listájának tetején található dedikált szűrőt:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="A javaslatok lista fölötti szűrők használatával megtalálhatja a gyors javítás lehetőséget tartalmazó javaslatokat":::

Gyors javítási szervizelés megvalósítása:

1. A **gyors javítást** tartalmazó javaslatok listájáról címkére, kattintson a javaslatra.

    [![Válassza a gyors javítás lehetőséget.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. A nem kifogástalan **erőforrások** lapon válassza ki azokat az erőforrásokat, amelyekhez javaslatot kíván végrehajtani, majd kattintson a **szervizelés** elemre.

    > [!NOTE]
    > Előfordulhat, hogy a felsorolt erőforrások némelyike le van tiltva, mert nem rendelkezik a megfelelő engedélyekkel a módosításhoz.

1. A megerősítés mezőben olvassa el a szervizelés részleteit és a következményeket.

    ![Gyors javítás](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > A következmények a javítás után megnyíló **erőforrások szervizelése** ablak szürke mezőjében jelennek **meg.** Felsorolja, hogy milyen változások történnek a gyors javítás szervizelésének folytatásakor.

1. Szükség esetén szúrja be a megfelelő paramétereket, majd hagyja jóvá a szervizelést.

    > [!NOTE]
    > A szervizelés befejezése után több percet is igénybe vehet, hogy az erőforrások megjelenjenek az **egészséges erőforrások** lapon. A Szervizelési műveletek megtekintéséhez tekintse meg a [tevékenység naplóját](#activity-log).

1. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, ha a szervizelés sikeres volt.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Szervizelési napló gyors javítása a műveletnapló esetében <a name="activity-log"></a>

A Szervizelési művelet sablon-telepítési vagy REST-javító API-hívást használ a konfiguráció alkalmazásához az erőforráson. Ezeket a műveleteket az [Azure-tevékenység naplójában](../azure-resource-manager/management/view-activity-logs.md)naplózza a rendszer.


## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan javíthatja az ajánlásokat a Security Centerban. Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő témaköröket:

* [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md) – megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és-erőforráscsoportok számára.
* A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.
