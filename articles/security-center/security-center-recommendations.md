---
title: Biztonsági javaslatok az Azure Security Centerben
description: Ez a dokumentum bemutatja, hogy az Azure Security Center ben található javaslatok hogyan segítenek megvédeni az Azure-erőforrásokat, és hogyan felelnek meg a biztonsági szabályzatoknak.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245303"
---
# <a name="security-recommendations-in-azure-security-center"></a>Biztonsági javaslatok az Azure Security Centerben 
Ez a témakör bemutatja, hogyan tekintheti meg és értheti meg az Azure Security Center ben található javaslatokat az Azure-erőforrások védelme érdekében.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem részletes útmutató.
>

## <a name="what-are-security-recommendations"></a>Mik a biztonsági javaslatok?

A javaslatok olyan műveletek, amelyeket az erőforrások védelme érdekében meg kell tennie.

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát a potenciális biztonsági rések azonosítása érdekében. Ezután ajánlásokat ad arra vonatkozóan, hogyan távolíthatja el őket.

Minden ajánlás a következőket biztosítja:

- Az ajánlott intézkedések rövid leírása.
- Az ajánlás végrehajtása érdekében végrehajtandó kármentesítési lépések. <!-- In some cases, Quick Fix remediation is available. -->
- Mely erőforrásokra van szükség az ajánlott művelet végrehajtásához.
- A **biztonságos pontszám hatása**, amely az az összeg, amely a biztonságos pontszám megy fel, ha végrehajtja ezt a javaslatot.

## <a name="monitor-recommendations"></a>Javaslatok figyelése<a name="monitor-recommendations"></a>

A Security Center elemzi az erőforrások biztonsági állapotát a lehetséges biztonsági rések azonosítása érdekében. Az Áttekintés **csoport** **ban található Javaslatok** csempe a Security Center által azonosított javaslatok teljes számát jeleníti meg.

![Biztonsági központ – áttekintés](./media/security-center-recommendations/asc-overview.png)

1. Válassza a **Javaslatok csempét** **az Áttekintés csoportban.** Megnyílik **a Javaslatok** lista.

      ![Javaslatok megtekintése](./media/security-center-recommendations/view-recommendations.png)

    A javaslatok szűrhetők. A javaslatok szűréséhez válassza a **Szűrő lehetőséget** a **Javaslatok** panelen. Megnyílik **a Szűrő** panel, és kiválaszthatja a látni kívánt súlyossági és állapotértékeket.

   * **AJÁNLÁSOK**: Az ajánlás.
   * **SECURE SCORE IMPACT:** A Biztonsági központ által létrehozott pontszám a biztonsági javaslatok használatával, és speciális algoritmusok alkalmazásával határozza meg, hogy mennyire fontos az egyes javaslatok. További információ: [Secure Score calculation](security-center-secure-score.md#secure-score-calculation).
   * **ERŐFORRÁS**: Azokat az erőforrásokat sorolja fel, amelyekre ez a javaslat vonatkozik.
   * **ÁLLAPOTSÁVOK**: Az adott ajánlás súlyosságát írja le:
       * **Magas (piros)**: A biztonsági rés értelmes erőforrással (például alkalmazással, virtuális géptel vagy hálózati biztonsági csoporttal) rendelkezik, és figyelmet igényel.
       * **Közepes (narancssárga)**: Biztonsági rés létezik, és nem kritikus vagy további lépésekre van szükség annak megszüntetéséhez vagy a folyamat befejezéséhez.
       * **Alacsony (kék)**: Olyan biztonsági rés áll fenn, amelyet meg kell oldani, de nem igényel azonnali figyelmet. (Alapértelmezés szerint az alacsony javaslatok nem jelennek meg, de szűrhet az alacsony javaslatokra, ha meg szeretné tekinteni őket.) 
       * **Egészséges (zöld)**:
       * **Nem érhető el (szürke)**:

1. Az egyes ajánlások részleteinek megtekintéséhez kattintson az ajánlásra.

    ![Ajánlás részletei](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Tekintse meg [a klasszikus és a Resource Manager üzembe helyezési modellek](../azure-classic-rm.md) az Azure-erőforrások.
 
## <a name="next-steps"></a>További lépések

Ebben a dokumentumban a Security Center biztonsági javaslatai kerültek bevezetésre. Az ajánlások kijavításának megismerése:

* [Javaslatok elhárítása](security-center-remediate-recommendations.md) – Ismerje meg, hogyan konfigurálhatja a biztonsági szabályzatokat az Azure-előfizetésekhez és erőforráscsoportokhoz.
