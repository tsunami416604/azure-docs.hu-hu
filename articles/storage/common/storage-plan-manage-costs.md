---
title: Az Azure Storage költségeinek megtervezése és kezelése
description: Megtudhatja, hogyan tervezheti meg és kezelheti az Azure Storage szolgáltatás költségeit a Azure Portalban található Cost Analysis használatával.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304523"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Az Azure Storage költségeinek megtervezése és kezelése

Ez a cikk bemutatja, hogyan tervezheti meg és kezelheti az Azure Storage költségeit. Első lépésként az Azure díjszabási kalkulátor segítségével tervezze meg a tárolási költségeket, mielőtt erőforrásokat hozna létre. Az Azure Storage-erőforrások használatának megkezdése után a Cost Management szolgáltatással állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és figyelheti a kiadási trendeket, hogy azonosítsa azokat a területeket, ahol érdemes lehet eljárni.

Ne feledje, hogy az Azure Storage szolgáltatás költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti az Azure Storage-t, az Azure-előfizetéshez használt összes Azure-szolgáltatás és erőforrás díja, beleértve a külső szolgáltatásokat is. Miután megismerte az Azure Storage költségeinek kezelését, hasonló módszerekkel kezelheti az előfizetésben használt összes Azure-szolgáltatás költségeit.

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../../cost-management-billing/costs/understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Az Azure Storage-fiók létrehozása előtti költségek becslése

Az [Azure díjszabási számológépével](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a költségeket az Azure Storage-fiókba való adatátvitel előtt.

1. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) lapon válassza a **Storage-fiókok** csempét.

2. Görgessen le az oldalra, és keresse meg a becsült **Storage accounts (tárolási fiókok** ) szakaszt.

3. A legördülő listában válassza a beállítások lehetőséget. 

   A legördülő lista értékének módosításakor a becsült költségbecslés módosul. Ez a becslés a felső sarokban, valamint a becslés alján jelenik meg. 
    
   ![Költségek figyelése a Cost Analysis ablaktáblával](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   A **típus** legördülő lista értékének módosításakor a munkalapon megjelenő egyéb beállítások is módosulnak. A **további információkkal** foglalkozó szakaszban található hivatkozásokat követve további információkat tudhat meg arról, hogy az egyes lehetőségek mit jelentenek, és hogy ezek a beállítások milyen hatással vannak a tárolással kapcsolatos műveletek árára. 

4. Módosítsa a hátralévő beállításokat, hogy azok hatással legyenek a becsült értékekre.

## <a name="use-budgets-and-cost-alerts"></a>Költségvetések és költségriasztások használata

A költségek kezeléséhez [költségvetéseket](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) és riasztásokat hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. Előfordulhat azonban, hogy az egyes Azure-szolgáltatásokhoz hasonló költségekkel járnak, mint például az Azure Storage költségei, mert a költségek magasabb szinten való nyomon követésére szolgálnak.

## <a name="monitor-costs"></a>Költségek figyelése

Amikor Azure-erőforrásokat használ az Azure Storage-ban, költségek merülnek fel. Az erőforrás-használati egység költségei időintervallumok (másodperc, perc, óra és nap), vagy egységenkénti használat (bájt, megabájt stb.) szerint változnak. A költségek az Azure Storage használatának megkezdésekor azonnal felmerülnek. A költségeket a Azure Portal [költség elemzése](../../cost-management-billing/costs/quick-acm-cost-analysis.md) ablaktábláján tekintheti meg.

A Cost Analysis használatakor különböző időintervallumok esetén megtekintheti az Azure Storage-költségeket gráfokban és táblázatokban. Néhány példa: nap, aktuális és előző hónap, év. A költségeket a költségvetések és az előre jelzett költségek között is megtekintheti. Ha a hosszabb nézetekre vált, az idő múlásával azonosíthatja a kiadási trendeket, és megtekintheti, hogy hol történt a túltöltés. Ha költségvetéseket hozott létre, azt is megteheti, hogy a megadottak hol vannak túllépve.

Az Azure Storage költségeinek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com).

2. Nyissa meg a **Cost Management + számlázás** ablakot, válassza ki a menüből a **Cost Management** elemet, majd válassza a **Cost Analysis**lehetőséget. Ezután módosíthatja egy adott előfizetés hatókörét a **hatókör** legördülő menüből.

   ![Költségek figyelése a Cost Analysis ablaktáblával](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Ha csak az Azure Storage költségeit szeretné megtekinteni, válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév**lehetőséget. Ezután válassza a **tároló** elemet a listából. 

   Az alábbi példa az Azure Storage szolgáltatással kapcsolatos költségeket mutatja be:

   ![Tárolási költségek figyelése a Cost Analysis ablaktáblával](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

Az előző példában a szolgáltatás aktuális díja látható. Az Azure-régiók (helyszínek) és az erőforráscsoport költségei is megjelennek.  

## <a name="next-steps"></a>További lépések

További információ a költségek a [Cost Analysis](../../cost-management-billing/costs/quick-acm-cost-analysis.md)szolgáltatással történő kezeléséről.

Az alábbi cikkekből megtudhatja, hogyan működik az árképzés az Azure Storage-ban:

- [Az Azure Storage áttekintése – díjszabás](https://azure.microsoft.com/pricing/details/storage/)
- [A fenntartott kapacitású Blob Storage költségeinek optimalizálása](../blobs/storage-blob-reserved-capacity.md)
