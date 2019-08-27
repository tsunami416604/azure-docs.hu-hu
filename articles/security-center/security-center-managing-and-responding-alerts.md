---
title: Biztonsági riasztások kezelése az Azure Security Centerben | Microsoft Docs
description: Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/27/2019
ms.author: v-mohabe
ms.openlocfilehash: a31b6006ff3345947fc0c64b9427d79e3870a2b6
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70052054"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Biztonsági riasztások kezelése és reagálás Azure Security Center

Ebből a témakörből megtudhatja, hogyan tekintheti meg és dolgozza fel a kapott riasztásokat az erőforrások megóvása érdekében. 

* A különböző típusú riasztásokról a [biztonsági riasztások típusai](security-center-alerts-overview.md#security-alert-types)című témakörben olvashat bővebben.
* A Security Center riasztások létrehozási módjának áttekintését lásd: [how Azure Security Center észleli és reagál a fenyegetésekre](security-center-alerts-overview.md#detect-threats).

> [!NOTE]
> A speciális észlelések eléréséhez frissítsen az Azure Security Center Standard verzióra. Ennek létezik egy ingyenesen elérhető próbaverziója. A frissítéshez a [Biztonsági szabályzat](tutorial-security-policy.md) beállításnál válassza ki a kívánt tarifacsomagot. További információkért lásd: [Az Azure Security Center díjszabása](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.

> [!NOTE]
> További információ a Security Center észlelési képességeinek működéséről: [how Azure Security Center észleli és reagál a fenyegetésekre](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Biztonsági riasztások kezelése

1. A Security Center irányítópulton tekintse meg a **fenyegetések elleni védelem** csempéjét a riasztások megtekintéséhez és áttekintéséhez.

    ![Biztonsági riasztások csempe a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. A riasztásokkal kapcsolatos további részletek megtekintéséhez kattintson a csempére.

   ![A Biztonsági riasztások oldal a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. A megjelenített riasztások szűréséhez kattintson a **szűrő**lehetőségre, és a megnyíló **szűrő** panelen válassza ki az alkalmazni kívánt szűrési beállításokat. A lista a kiválasztott szűrőnek megfelelően frissíti a frissítéseket. A szűrés nagyon hasznos lehet. Például olyankor, ha az elmúlt 24 órában történt biztonsági riasztásokat szeretné kezelni, mert egy, a rendszerbe történő lehetséges behatolást vizsgál.

    ![A riasztások szűrése a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Válaszadás a biztonsági riasztásokra

1. A **biztonsági riasztások** listából válassza ki a biztonsági riasztást. Megjelenik az érintett erőforrások és a támadások elhárításához szükséges lépések.

    ![Válaszadás a biztonsági riasztásokra](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Az információk áttekintése után kattintson a megtámadott erőforrásra.

    ![Javaslatok a biztonsági riasztásokkal kapcsolatos teendők](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Az **általános információ** szakasz betekintést nyújt a biztonsági riasztás kiváltott részébe. Olyan információkat jelenít meg, mint például a cél erőforrás, a forrás IP-címe (ha van ilyen), ha a riasztás még aktív, és javaslatokat tesz a megoldására.  

    > [!NOTE]
    >Bizonyos esetekben a forrás IP-címe nem érhető el, néhány Windows biztonsági eseménynapló nem tartalmazza az IP-címet.

1. A Security Center által javasolt szervizelési lépések a biztonsági riasztástól függően változnak. Mindegyik riasztásnál kövesse ezeket. Bizonyos esetekben a fenyegetések észlelésével kapcsolatos riasztások enyhítése érdekében előfordulhat, hogy más Azure-vezérlőket vagy-szolgáltatásokat kell használnia az ajánlott szervizelés megvalósításához. 

    Az alábbi témakörök a különböző riasztásokat ismertetik az erőforrástípusok szerint:
    
    * [IaaS virtuális gépek és kiszolgálók riasztásai](security-center-alerts-iaas.md)
    * [Natív számítási riasztások](security-center-alerts-compute.md)
    * [Adatszolgáltatási riasztások](security-center-alerts-data-services.md)
    
    A következő témakörök azt ismertetik, hogyan használják a Security Center az Azure-infrastruktúrával való integrálás során gyűjtött különböző telemetria, hogy az Azure-ban üzembe helyezett erőforrások további védelmi rétegeit is alkalmazni lehessen:
    
    * [Szolgáltatási réteggel kapcsolatos riasztások](security-center-alerts-service-layer.md)
    * [Integráció az Azure Security-termékekkel](security-center-alerts-integration.md)
    
## <a name="see-also"></a>Lásd még

Ebből a dokumentumból megismerte a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center biztonsági riasztások](security-center-alerts-overview.md).
* [Biztonsági incidensek kezelésével](security-center-incident.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
