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
ms.openlocfilehash: f8b09c71e9ad55528788f97fb986606f21e8b0ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84769775"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben

Ebből a témakörből megtudhatja, hogyan tekintheti meg és dolgozza fel a kapott riasztásokat az erőforrások megóvása érdekében. 

* A különböző típusú riasztásokról a [biztonsági riasztások típusai](alerts-reference.md)című témakörben olvashat bővebben.
* A Security Center riasztások létrehozási módjának áttekintését lásd: [how Azure Security Center észleli és reagál a fenyegetésekre](security-center-alerts-overview.md).

> [!NOTE]
> A speciális észlelések eléréséhez frissítsen az Azure Security Center Standard verzióra. Ennek létezik egy ingyenesen elérhető próbaverziója. A frissítéshez válassza az árképzési szintet a [biztonsági házirendben](tutorial-security-policy.md). További információkért lásd: [Az Azure Security Center díjszabása](security-center-pricing.md).

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

    A biztonsági riasztás oldal bal oldali ablaktáblája a biztonsági riasztással kapcsolatos magas szintű információkat jeleníti meg: cím, súlyosság, állapot, tevékenység ideje, a gyanús tevékenység leírása és az érintett erőforrás. Az érintett erőforrás mellett az erőforráshoz tartozó Azure-címkék is szerepelnek. Ezek használatával következtetheti ki az erőforrás szervezeti környezetét a riasztás kivizsgálásakor.

    A jobb oldali ablaktábla tartalmazza a riasztás **részletei** lapot, amely a riasztás további részleteit tartalmazza a probléma kivizsgálásához: IP-címek, fájlok, folyamatok és sok más.
     
    ![Javaslatok a biztonsági riasztásokkal kapcsolatos teendők](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    A jobb oldali ablaktáblán is a **Take Action (művelet** ) lap jelenik meg. ezen a lapon további műveleteket végezhet a biztonsági riasztással kapcsolatban. Műveletek, például:
    - *A fenyegetés enyhítése* – a biztonsági riasztás manuális szervizelési lépéseit nyújtja
    - A *jövőbeli támadások megelőzése* – biztonsági ajánlásokat biztosít a támadási felület csökkentéséhez, a biztonsági helyzet növeléséhez, és ezáltal a jövőbeli támadások megelőzése érdekében
    - *Automatizált válasz indítása* – lehetővé teszi, hogy a logikai alkalmazás a biztonsági riasztásra adott válaszként legyen aktiválva
    - *Hasonló riasztások letiltása* – lehetővé teszi a hasonló tulajdonságokkal rendelkező jövőbeli riasztások mellőzését, ha a riasztás nem releváns a szervezet számára

    ![Művelet elvégzése lap](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Lásd még

Ebből a dokumentumból megtudhatta, hogyan tekintheti meg a biztonsági riasztásokat. A kapcsolódó anyagokkal kapcsolatban lásd a következő lapokat:

- [Riasztás-elnyomási szabályok konfigurálása](alerts-suppression-rules.md)
- [A riasztásokra és javaslatokra adott válaszok automatizálása a munkafolyamat-automatizálással](workflow-automation.md)
