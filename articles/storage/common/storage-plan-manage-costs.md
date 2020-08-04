---
title: Az Azure Blob Storage költségeinek megtervezése és kezelése
description: Megtudhatja, hogyan tervezheti meg és kezelheti az Azure Blob Storage költségeit a Azure Portalban található Cost Analysis használatával.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 90aa2b9504008783649662019179a5998d534746
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543095"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Az Azure Blob Storage költségeinek megtervezése és kezelése

Ez a cikk segítséget nyújt az Azure Blob Storage költségeinek megtervezéséhez és kezeléséhez. Először is becsülje meg a költségeket az Azure díjszabási kalkulátor használatával. A Storage-fiók létrehozása után optimalizálja a fiókot, így csak azért kell fizetnie, amire szüksége van. A Cost Management-funkciók használatával megadhatja a költségvetéseket és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és figyelemmel kísérheti a kiadási trendeket, hogy azonosítsa azokat a területeket, ahol érdemes lehet eljárni.

Ne feledje, hogy az Azure Storage szolgáltatás költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk azt ismerteti, hogyan lehet megbecsülni és kezelni az Azure Storage költségeit, az Azure-előfizetéshez használt összes Azure-szolgáltatás és erőforrás díja, beleértve a külső szolgáltatásokat is. Miután megismerte az Azure Storage költségeinek kezelését, hasonló módszerekkel kezelheti az előfizetésben használt összes Azure-szolgáltatás költségeit.

## <a name="estimate-costs"></a>A költségek megbecslése

Az [Azure díjszabási számológépével](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a költségeket az Azure Storage-fiókba való adatátvitel előtt.

1. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) lapon válassza a **Storage-fiókok** csempét.

2. Görgessen le az oldalra, és keresse meg a becsült **Storage accounts (tárolási fiókok** ) szakaszt.

3. A legördülő listában válassza a beállítások lehetőséget. 

   A legördülő lista értékének módosításakor a becsült költségbecslés módosul. Ez a becslés a felső sarokban, valamint a becslés alján jelenik meg. 
    
   ![Költségek figyelése a Cost Analysis ablaktáblával](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   A **típus** legördülő lista értékének módosításakor a munkalapon megjelenő egyéb beállítások is módosulnak. A **további információkkal** foglalkozó szakaszban található hivatkozásokat követve további információkat tudhat meg arról, hogy az egyes lehetőségek mit jelentenek, és hogy ezek a beállítások milyen hatással vannak a tárolással kapcsolatos műveletek árára. 

4. Módosítsa a hátralévő beállításokat, hogy azok hatással legyenek a becsült értékekre.

## <a name="optimize-costs"></a>Költségek optimalizálása

Ezeket a lehetőségeket érdemes használni a költségek csökkentése érdekében. 

- Tárterület-kapacitás foglalása

- Adatszervezés hozzáférési rétegekbe

- Adatátviteli rétegek közötti automatikus áthelyezés

Ez a szakasz részletesebben ismerteti az egyes lehetőségeket. 

#### <a name="reserve-storage-capacity"></a>Tárterület-kapacitás foglalása

Az Azure Storage szolgáltatás számára fenntartott kapacitással pénzt takaríthat meg a blob-adattárolási költségekkel kapcsolatban. Az Azure Storage szolgáltatás számára fenntartott kapacitás kedvezményt biztosít a blokkos blobok és a standard szintű Storage-fiókok Azure Data Lake Storage Gen2 adatai számára, ha egy vagy három évre szóló foglalást végez. A foglalás rögzített tárolási kapacitást biztosít a foglalás feltételeihez. Az Azure Storage szolgáltatás számára fenntartott kapacitás jelentős mértékben csökkentheti a blokk-blobok és Azure Data Lake Storage Gen2-adatmennyiségek kapacitásának költségeit. 

További információ: a [blob Storage költségeinek optimalizálása a fenntartott kapacitással](https://docs.microsoft.com/azure/storage/blobs/storage-blob-reserved-capacity).

#### <a name="organize-data-into-access-tiers"></a>Adatszervezés hozzáférési rétegekbe

A költségek csökkentése érdekében a blob-adatmennyiséget a legköltséghatékonyabb hozzáférési rétegekbe helyezheti el. Három olyan csomag közül választhat, amelyek az adatfelhasználással kapcsolatos költségek optimalizálására szolgálnak. Például a *gyors* elérési szint nagyobb tárolási kapacitással jár, de alacsonyabb a hozzáférési díj. Ezért ha gyakran szeretné elérni az adatokhoz való hozzáférést, a gyors elérésű szint lehet a leghatékonyabb megoldás. Ha ritkábban szeretné elérni az adatokhoz való hozzáférést, a *hideg* vagy az *archiválási* szint a legalkalmasabb lehet, mert az adatokhoz való hozzáférés költségeit csökkenti az adattárolás költségeinek csökkentése érdekében.    

További információ [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal).

#### <a name="automatically-move-data-between-access-tiers"></a>Adatátviteli rétegek közötti automatikus áthelyezés

Az életciklus-kezelési házirendek segítségével rendszeres időközönként helyezheti át az adatszinteket a legtöbb pénz megtakarítása érdekében. Ezek a szabályzatok az Ön által megadott szabályok használatával helyezhetik át az adataikat. Létrehozhat például egy olyan szabályt, amely blobokat helyez át az archív szintre, ha a blobot 90 napon belül nem módosították. Az adatelérési szintet beállító szabályzatok létrehozásával megtervezheti az igényeinek leginkább megfelelő tárolási lehetőségeket.

További információ: [Az Azure Blob Storage életciklusának kezelése](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../../cost-management-billing/costs/tutorial-acm-create-budgets.md) és riasztásokat hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. Előfordulhat azonban, hogy az egyes Azure-szolgáltatásokhoz hasonló költségekkel járnak, mint például az Azure Storage költségei, mert a költségek magasabb szinten való nyomon követésére szolgálnak.

## <a name="monitor-costs"></a>Költségek figyelése

Amikor Azure-erőforrásokat használ az Azure Storage-ban, költségek merülnek fel. Az erőforrás-használati egység költségei időintervallumok (másodperc, perc, óra és nap), vagy egységenkénti használat (bájt, megabájt stb.) szerint változnak. A költségek az Azure Storage használatának megkezdésekor azonnal felmerülnek. A költségeket a Azure Portal [költség elemzése](../../cost-management-billing/costs/quick-acm-cost-analysis.md) ablaktábláján tekintheti meg.

A Cost Analysis használatakor különböző időintervallumok esetén megtekintheti az Azure Storage-költségeket gráfokban és táblázatokban. Néhány példa: nap, aktuális és előző hónap, év. A költségeket a költségvetések és az előre jelzett költségek között is megtekintheti. Ha a hosszabb nézetekre vált, az idő múlásával azonosíthatja a kiadási trendeket, és megtekintheti, hogy hol történt a túltöltés. Ha költségvetéseket hozott létre, azt is megteheti, hogy a megadottak hol vannak túllépve.

>[!NOTE]
> A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../../cost-management-billing/costs/understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../../cost-management-billing/costs/assign-access-acm-data.md).

Az Azure Storage költségeinek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Nyissa meg a **Cost Management + számlázás** ablakot, válassza ki a menüből a **Cost Management** elemet, majd válassza a **Cost Analysis**lehetőséget. Ezután módosíthatja egy adott előfizetés hatókörét a **hatókör** legördülő menüből.

   ![Költségek figyelése a Cost Analysis ablaktáblával](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Ha csak az Azure Storage költségeit szeretné megtekinteni, válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév**lehetőséget. Ezután válassza a **tároló** elemet a listából. 

   Az alábbi példa az Azure Storage szolgáltatással kapcsolatos költségeket mutatja be:

   ![Tárolási költségek figyelése a Cost Analysis ablaktáblával](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

Az előző példában a szolgáltatás aktuális díja látható. Az Azure-régiók (helyszínek) és az erőforráscsoport költségei is megjelennek. További szűrőket is hozzáadhat (például egy szűrőt az adott Storage-fiókok költségeinek megjelenítéséhez).

## <a name="next-steps"></a>További lépések

További információ a költségek a [Cost Analysis](../../cost-management-billing/costs/quick-acm-cost-analysis.md)szolgáltatással történő kezeléséről.

Az alábbi cikkekből megtudhatja, hogyan működik az árképzés az Azure Storage-ban:

- [Az Azure Storage áttekintése – díjszabás](https://azure.microsoft.com/pricing/details/storage/)
- [A fenntartott kapacitású Blob Storage költségeinek optimalizálása](../blobs/storage-blob-reserved-capacity.md)
