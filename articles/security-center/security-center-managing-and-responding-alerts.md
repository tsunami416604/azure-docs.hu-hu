---
title: Biztonsági riasztások kezelése az Azure Security Centerben | Microsoft Docs
description: Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 6ea951b542d893b8fef3cdf19a964ce2ef8a034d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415715"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben

Ez a témakör bemutatja, hogyan tekintheti meg és dolgozhatja fel az erőforrások védelme érdekében kapott riasztásokat. 

* A különböző típusú riasztásokról a [Biztonsági riasztástípusok](alerts-reference.md)című témakörben olvashat.
* A Security Center riasztásainak áttekintését a [Hogyan észleli az Azure Security Center a fenyegetések észlelése és az azokra való reagálás című témakörben.](security-center-alerts-overview.md)

> [!NOTE]
> A speciális észlelések eléréséhez frissítsen az Azure Security Center Standard verzióra. Ennek létezik egy ingyenesen elérhető próbaverziója. A frissítéshez válassza a Biztonsági [házirend](tutorial-security-policy.md)díjszabási szint lehetőséget. További információkért lásd: [Az Azure Security Center díjszabása](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.

> [!NOTE]
> A Security Center észlelési képességeinek működéséről a Hogyan észleli az Azure Security Center a [fenyegetéseket, és hogyan reagál azokra.](security-center-alerts-overview.md#detect-threats)

## <a name="manage-your-security-alerts"></a>A biztonsági riasztások kezelése

1. A Security Center irányítópultján tekintse meg a **Veszélyforrások elleni védelem** csempét a riasztások megtekintéséhez és áttekintéséhez.

    ![Biztonsági riasztások csempe a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Ha további részleteket szeretne látni a riasztásokkal kapcsolatban, kattintson a csempére.

   ![A Biztonsági riasztások oldal a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. A megjelenített riasztások szűréséhez kattintson a **Szűrő**gombra, majd a megnyíló **Szűrő** panelen adja meg az alkalmazni kívánt szűrőbeállításokat. A lista a kijelölt szűrőnek megfelelően frissül. Szűrés nagyon hasznos lehet. Például olyankor, ha az elmúlt 24 órában történt biztonsági riasztásokat szeretné kezelni, mert egy, a rendszerbe történő lehetséges behatolást vizsgál.

    ![A riasztások szűrése a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Válaszadás a biztonsági riasztásokra

1. A **Biztonsági riasztások** listában kattintson egy biztonsági riasztásra. Az érintett erőforrások és a támadások elhárításához szükséges lépések láthatók.

    ![Válaszadás a biztonsági riasztásokra](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Az információk áttekintése után kattintson egy megtámadott erőforrásra.

    ![Javaslatok a biztonsági riasztásokhoz](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Az **Általános információk** szakasz betekintést nyújthat abba, hogy mi váltotta ki a biztonsági riasztást. Olyan információkat jelenít meg, mint a célerőforrás, a forrás IP-címe (ha van), ha a riasztás még aktív, és javaslatokat tesz a javításra.  

    > [!NOTE]
    >Bizonyos esetekben a forrás IP-címe nem érhető el, egyes Windows biztonsági események naplói nem tartalmazzák az IP-címet.

1. A Security Center által javasolt elhárítási lépések a biztonsági riasztástól függően eltérők lehetnek. Kövesse őket minden riasztáshoz. 

    Bizonyos esetekben a biztonsági riasztás csökkentése érdekében előfordulhat, hogy más Azure-vezérlők et vagy szolgáltatásokat kell használnia az ajánlott szervizelés megvalósításához. 

## <a name="see-also"></a>Lásd még

Ebből a dokumentumból megismerte a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági riasztások az Azure Security Centerben.](security-center-alerts-overview.md)
* [Biztonsági események kezelése](security-center-incident.md)