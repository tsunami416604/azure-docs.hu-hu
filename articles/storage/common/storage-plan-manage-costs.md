---
title: Az Azure Storage költségeinek megtervezése és kezelése
description: Megtudhatja, hogyan tervezhet és kezelhet költségeket az Azure Storage számára az Azure Portal költségelemzésével.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304523"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Az Azure Storage költségeinek megtervezése és kezelése

Ez a cikk ismerteti, hogyan tervezze meg és kezelje az Azure Storage költségeit. Először használja az Azure díjkalkulátor segítségével megtervezze a tárolási költségeket, mielőtt bármilyen erőforrást hozzá. Miután megkezdte az Azure Storage-erőforrások használatát, költségkezelési funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Megtekintheti az előre jelzett költségeket is, és figyelheti a költési trendeket, hogy azonosíthassa azokat a területeket, ahol esetleg cselekedni szeretne.

Ne feledje, hogy az Azure Storage költségei csak egy részét teszik ki az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti az Azure Storage költségeit, az Azure-előfizetéséhez használt összes Azure-szolgáltatásért és erőforrásért díjat kell fizetnie, beleértve a külső szolgáltatásokat is. Miután már ismeri az Azure Storage költségeinek kezelését, hasonló módszereket alkalmazhat az előfizetésben használt összes Azure-szolgáltatás költségeinek kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../../cost-management-billing/costs/understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Becsült költségek az Azure Storage-fiók létrehozása előtt

Az [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével becsülje meg a költségeket, mielőtt létrehozna és megkezdené az adatok átvitelét egy Azure Storage-fiókba.

1. Az [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) lapon válassza a **Storage-fiókok csempét.**

2. Görgessen le a lapon, és keresse meg a **becslés Tárfiókok** szakaszát.

3. Válasszon a legördülő listából. 

   A legördülő listák értékének módosításakor a költségbecslés megváltozik. Ez a becslés a felső sarokban és a becslés alján is megjelenik. 
    
   ![Költségek figyelése a Költségelemzés ablaktáblával](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   A **Típus** legördülő lista értékének módosításakor a munkalapon megjelenő egyéb beállítások is megváltoznak. A **További információk** szakasz hivatkozásaisegítségével többet tudhat meg arról, hogy mit jelentenek az egyes beállítások, és hogy ezek a beállítások hogyan befolyásolják a tárolással kapcsolatos műveletek árát. 

4. A fennmaradó beállítások módosításával megtekintheti a becslésre gyakorolt hatásukat.

## <a name="use-budgets-and-cost-alerts"></a>Költségvetések és költségriasztások használata

A költségek kezeléséhez [költségvetéseket](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) és riasztásokat hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. A költségvetések és riasztások az Azure-előfizetésekhez és erőforráscsoportokhoz jönnek létre, így hasznosak egy átfogó költségfigyelési stratégia részeként. Előfordulhat azonban, hogy korlátozott funkcionalitással rendelkeznek az egyes Azure-szolgáltatások költségeinek, például az Azure Storage költségeinek kezeléséhez, mivel a költségek magasabb szintű nyomon követésére szolgálnak.

## <a name="monitor-costs"></a>A költségek figyelése

Az Azure-erőforrások Azure Storage-szal való használata során költségek merülnek fel. Az erőforrás-felhasználási egység költségei időintervallumok (másodperc, perc, óra és nap) vagy egységhasználat (bájt, megabájt és így tovább) szerint változnak. A költségek az Azure Storage használatának megkezdésekor merülnek fel. A költségeket az Azure Portal [költségelemzési](../../cost-management-billing/costs/quick-acm-cost-analysis.md) ablaktáblájában láthatja.

Költségelemzés használatakor megtekintheti az Azure Storage-költségeket grafikonokban és táblázatokban a különböző időintervallumokban. Néhány példa a nap, az aktuális és az előző hónap és év szerint. A költségeket a költségvetések és az előre jelzett költségek alapján is megtekintheti. A hosszabb nézetekre való váltás sal segíthet azonosítani a költési trendeket, és megtekinteni, hogy hol történhetett túlköltekezés. Ha létrehozott költségkereteket, azt is könnyen láthatja, hogy hol lépték túl.

Az Azure Storage költségeinek megtekintése a költségelemzésben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Nyissa meg a **Költségkezelés + Számlázás** ablakot, válassza a menü **Költségkezelés** parancsát, majd válassza a **Költségelemzés**lehetőséget. Ezután módosíthatja egy adott előfizetés hatókörét a **Hatókör legördülő menüből.**

   ![Költségek figyelése a Költségelemzés ablaktáblával](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Ha csak az Azure Storage költségeit szeretné megtekinteni, válassza **a Szűrő hozzáadása** lehetőséget, majd a Szolgáltatás név **lehetőséget.** Ezután válassza a **tárolót** a listából. 

   Íme egy példa, amely csak az Azure Storage költségeit mutatja be:

   ![A tárolási költségek figyelése a Költségelemzés ablaktáblával](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

Az előző példában a szolgáltatás aktuális költsége látható. Az Azure-régiók (helyek) és az erőforráscsoport költségei is megjelennek.  

## <a name="next-steps"></a>További lépések

További információ a költségek [költségelemzéssel](../../cost-management-billing/costs/quick-acm-cost-analysis.md)való kezeléséről.

Az alábbi cikkekből megtudhatja, hogyan működik a díjszabás az Azure Storage szolgáltatással:

- [Az Azure Storage – áttekintő díjszabás](https://azure.microsoft.com/pricing/details/storage/)
- [A fenntartott kapacitású Blob Storage költségeinek optimalizálása](../blobs/storage-blob-reserved-capacity.md)
