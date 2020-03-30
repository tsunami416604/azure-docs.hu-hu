---
title: Riasztási példányok kezelése az Azure Monitorban
description: Riasztási példányok kezelése az Azure-ban
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667618"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Riasztási példányok kezelése egységes riasztásokkal

Az Azure Monitor [egységes riasztások felületével](https://aka.ms/azure-alerts-overview) megtekintheti az Azure-beli riasztások összes típusát. Ez több előfizetésre terjed ki egyetlen ablaktáblában. Ez a cikk bemutatja, hogyan tekintheti meg a riasztási példányokat, és hogyan kereshet konkrét riasztási példányokat hibaelhárításhoz.

> [!NOTE]
> Csak az elmúlt 30 napban létrehozott riasztásokhoz férhet hozzá.

## <a name="go-to-the-alerts-page"></a>Ugrás a riasztások oldalra

A riasztások oldalon az alábbi módokon léphet meg:

- Az [Azure Portalon](https://portal.azure.com/)válassza a **Riasztások figyelése** > **Alerts**lehetőséget.  

     ![Képernyőkép: Figyelőértesítések](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Egy adott erőforrás környezetének használata. Nyisson meg egy erőforrást, nyissa meg a **Figyelés** **szakaszt,** és válassza a Riasztások lehetőséget. A céloldal előre szűrve van az adott erőforrásra vonatkozó riasztásokhoz.

     ![Képernyőkép az erőforrás-figyelési riasztásokról](media/alerts-managing-alert-instances/alert-resource.JPG)

- Egy adott erőforráscsoport környezetének használata. Nyisson meg egy erőforráscsoportot, nyissa meg a **Figyelés** **szakaszt,** és válassza a Riasztások lehetőséget. A céloldal előre szűrve van az adott erőforráscsoportra vonatkozó riasztásokhoz.    

     ![Képernyőkép az erőforráscsoport figyelési értesítéseiről](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Riasztási példányok keresése

A **Riasztások összegzése** lap áttekintést nyújt az Azure-beli riasztási példányokról. Az összesítő nézetet **több előfizetés** kiválasztásával (legfeljebb 5) vagy **erőforráscsoportok**, adott **erőforrások**vagy időtartományok közötti szűréssel **módosíthatja.** Válassza **az Összes riasztás**lehetőséget, vagy a súlyossági sávok bármelyikét, ha meg szeretné tekinteni az értesítések listanézetét.     

![Képernyőkép: A Riasztások összegzése lap](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Az **Összes riasztás** lapon az Azure-ban található összes riasztási példány szerepel. Ha egy riasztási értesítésből érkezik a portálra, a rendelkezésre álló szűrők segítségével szűkítheti az adott riasztási példányt.

> [!NOTE]
> Ha a súlyossági sávok bármelyikének kiválasztásával érkezett az oldalra, a lista előre szűrve van az adott súlyosság érdekében.

Az előző oldalon elérhető szűrőkön kívül szűrhet a figyelőszolgáltatás (például a mérőszámok platformja), a figyelőállapot (kilövés vagy megoldás), a súlyosság, a riasztási állapot (új/nyugtázott/lezárt) vagy az intelligens csoport azonosítója alapján is.

![Képernyőkép az Összes értesítés lapról](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Ha a súlyossági sávok bármelyikének kiválasztásával érkezett az oldalra, a lista előre szűrve van az adott súlyosság érdekében.

Bármely riasztási példány kiválasztása megnyitja a **Riasztás részletei** lapot, így további részleteket láthat az adott riasztási példányról.   

![Képernyőkép a Riasztás részletei lapról](media/alerts-managing-alert-instances/alert-details.jpg)  

