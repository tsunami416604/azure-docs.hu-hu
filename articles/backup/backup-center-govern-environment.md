---
title: Biztonsági mentések szabályozása a Backup Centerrel
description: Ismerje meg, hogyan szabályozhatja az Azure-környezetet annak biztosításához, hogy az összes erőforrás megfelel a Backup Center biztonsági mentési perspektívájának.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 8a16769be827bf34e5be82409d5b8eb014cdcf1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614329"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Biztonsági mentések szabályozása a Backup Centerrel

A Backup Center segítségével szabályozhatja az Azure-környezetet, így biztosítva, hogy minden erőforrás megfelel a biztonsági mentési perspektívának. Az alábbiakban a Backup Center irányítási képességeiből tájékozódhat:

* Azure-szabályzatok megtekintése és kiosztása a biztonsági mentéshez

* Tekintse meg az erőforrások megfelelőségét az összes beépített Azure-szabályzatban a biztonsági mentéshez.

* A biztonsági mentéshez nem konfigurált összes adatforrás megtekintése.

## <a name="supported-scenarios"></a>Támogatott esetek

* A támogatott és nem támogatott forgatókönyvek részletes listáját a [támogatási mátrixban](backup-center-support-matrix.md) találja.

## <a name="azure-policies-for-backup"></a>Azure-szabályzatok biztonsági mentéshez

A biztonsági mentéshez elérhető összes [Azure-házirend](https://docs.microsoft.com/azure/governance/policy/overview) megtekintéséhez válassza ki az **Azure-szabályzatok biztonsági mentéshez** menüpontot. Ez megjeleníti az előfizetések és az erőforráscsoportok hozzárendeléséhez elérhető összes beépített és egyéni [Azure-házirend-definíciót a biztonsági mentéshez](policy-reference.md) .

A definíciók bármelyikének kiválasztásával [hozzárendelheti a szabályzatot](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) egy hatókörhöz.

![Azure Policy definíciók kiválasztása](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Biztonsági mentés megfelelősége

A biztonsági mentés megfelelősége menüpontra kattintva megtekintheti az erőforrások [megfelelőségét](https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data) az Azure-környezethez rendelt különböző beépített szabályzatoknak megfelelően. Megtekintheti az összes szabályzatnak megfelelő erőforrások százalékos arányát, valamint azokat a házirendeket, amelyek egy vagy több nem megfelelő erőforrással rendelkeznek.

![Biztonsági másolatok megfelelőségének megtekintése](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Védhető adatforrások

A **Protected DataSources** menüpont kiválasztásával megtekintheti az összes olyan adatforrást, amely nem lett konfigurálva a biztonsági mentéshez. A listát adatforrás-előfizetés, Erőforráscsoport, hely, típus és címkék alapján szűrheti. Miután azonosította a biztonsági mentésre szoruló adatforrást, kattintson a jobb gombbal a megfelelő rácsvonal elemre, és válassza a **biztonsági** mentés lehetőséget az erőforrás biztonsági mentésének konfigurálásához.

![Védhető adatforrások menü](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>Következő lépések

* [Biztonsági másolatok figyelése és üzemeltetése](backup-center-monitor-operate.md)
* [Műveletek végrehajtása a Backup Center használatával](backup-center-actions.md)
* [Bepillantást nyerhet a biztonsági másolatokra](backup-center-obtain-insights.md)
