---
title: Bérlőközi felügyelet az Azure Security Centerben | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja be a bérlők közötti felügyeletet a Security Center több bérlőbiztonsági állapotának kezelésére az Azure delegált erőforrás-kezelése használatával.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919481"
---
# <a name="cross-tenant-management-in-security-center"></a>Bérlőközi felügyelet a Biztonsági központban

A bérlők közötti felügyelet lehetővé teszi több bérlő biztonsági állapotának megtekintését és kezelését a Security Centerben az [Azure delegált erőforrás-kezelésének](../lighthouse/concepts/azure-delegated-resource-management.md) kihasználásával. Több bérlő hatékony kezelése egyetlen nézetből, anélkül, hogy minden bérlő könyvtárába be kellene jelentkeznie.

- A szolgáltatók kezelhetik az erőforrások biztonsági állapotát több ügyfél számára a saját bérlőjükön belül.

- A több bérlővel rendelkező szervezetek biztonsági csapatai egyetlen helyről tekinthetik meg és kezelhetik biztonsági állapotukat.

## <a name="set-up-cross-tenant-management"></a>Több bérlő közötti felügyelet beállítása

Bérlőközi felügyelet beállítása a felügyelt bérlők erőforrásaihoz való hozzáférés delegálásával a saját bérlőre az [Azure delegált erőforrás-kezelése](../lighthouse/concepts/azure-delegated-resource-management.md)használatával.

> [!NOTE]
> Az Azure delegált erőforrás-kezelése az Azure Lighthouse egyik legfontosabb összetevője.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Hogyan működik a bérlők közötti felügyelet a Security Centerben?

Több bérlő előfizetését is áttekintheti és kezelheti ugyanúgy, ahogyan egyetlen bérlőben több előfizetést is kezelhet.

A felső menüsorban kattintson a szűrő ikonra, és válassza ki az egyes bérlői címtárban megtekinteni kívánt előfizetéseket.

  ![Bérlők szűrése](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

A nézetek és intézkedések alapvetően azonosak. Néhány példa:

- **Biztonsági házirendek kezelése**: Egyetlen nézetben kezelheti számos erőforrás biztonsági állapotát [házirendekkel,](tutorial-security-policy.md)biztonsági javaslatokkal műveleteket hajtson végre, valamint gyűjtsön és kezeljen a biztonsággal kapcsolatos adatokat.
- **A biztonságos pontszám és megfelelőségi állapot javítása:** A bérlők közötti láthatóság lehetővé teszi az összes bérlő általános biztonsági állapotának megtekintését, valamint azt, hogy hol és hogyan javíthatja a [biztonságos pontszámot](security-center-secure-score.md) és [a megfelelőségi állapotot.](security-center-compliance-dashboard.md)
- **Javaslatok javítása:** Figyelheti és kiigazítja a különböző bérlők től származó erőforrások egy időben [számos erőforrásra vonatkozó javaslat.](security-center-recommendations.md) Ezután azonnal kezelni a biztonsági réseket, amelyek a legnagyobb kockázatot az összes bérlő.
- **Riasztások kezelése:** [riasztások](security-center-alerts-overview.md) észlelése a különböző bérlők között. A végrehajtható [javítási lépéseknek](security-center-managing-and-responding-alerts.md)nem megfelelő erőforrásokkal kapcsolatos műveletek et kell végrehajtania.

- **Fejlett felhővédelmi funkciók kezelése és egyebek:** Kezelheti a különböző veszélyforrások elleni védelmi szolgáltatásokat, például [a just-in-time (JIT) virtuális gép-hozzáférést,](security-center-just-in-time.md) [az adaptív hálózati edzést,](security-center-adaptive-network-hardening.md) [az adaptív alkalmazásvezérlőket](security-center-adaptive-application.md)stb.
 
## <a name="next-steps"></a>További lépések
Ez a cikk bemutatja, hogyan működik a bérlők közötti felügyelet a Security Centerben. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Az Azure Security Center rel való biztonsági állapot megerősítése](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.
* [Az Azure Security Center –](faq-general.md) Gyakori kérdések – Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Ismerje meg az Azure Lighthouse vállalati forgatókönyvekben](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) – Fedezze fel, hogyan egyszerűsítheti az Azure Lighthouse a több Azure AD-bérlőt használó vállalaton belüli bérlők közötti felügyeletet.