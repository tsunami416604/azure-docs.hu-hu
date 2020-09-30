---
title: 'Oktatóanyag: gépi tanulási modell pontozási varázsló SQL-készletekhez'
description: Oktatóanyag a Machine learning Model pontozási varázslójának használatáról a szinapszis SQL-készletekben lévő adatfrissítéshez
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 14358f60b700e80c17b1dd8259f1d0566a854c43
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91543388"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-synapse-sql-pools"></a>Oktatóanyag: a számítógép-tanulási modell pontozási varázslója a szinapszis SQL-készletekhez

Ismerje meg, hogyan gazdagíthatja adatait az SQL-készletekben a prediktív gépi tanulási modellel.  Az adatszakértők által létrehozott modellek mostantól könnyen elérhetők az adatszakemberek számára a prediktív elemzésekhez. A Szinapszisban található adatszakember egyszerűen kiválaszthat egy modellt a Azure Machine Learning modell beállításjegyzékből a szinapszis SQL-készletekbe való üzembe helyezéshez, és előrejelzéseket indíthat az adatok dúsítására.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> - A prediktív gépi tanulási modell betanítása és a modell regisztrálása Azure Machine Learning modell beállításjegyzékében
> - Az SQL pontozás varázsló használata a szinapszis SQL-készletben lévő előrejelzések elindításához

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- A [szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy ADLS Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A ADLS Gen2-fájlrendszer **Storage blob-Adatközreműködőinek** kell lennie.
- Szinapszis SQL-készlet a szinapszis Analytics-munkaterületen. Részletekért lásd: [SZINAPSZIS SQL-készlet létrehozása](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning társított szolgáltatást a szinapszis Analytics-munkaterületen. Részletekért lásd: [Azure Machine learning társított szolgáltatás létrehozása a szinapszisban](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>Modell betanítása Azure Machine Learning

Mielőtt elkezdené, ellenőrizze, hogy a **sklearn** verziója 0.20.3-e.

A jegyzetfüzet összes cellájának futtatása előtt ellenőrizze, hogy fut-e a számítási példány.

![PÉNZMOSÁS-számítás ellenőrzése](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Navigáljon a Azure Machine Learning munkaterületre.

1. Töltse le a [New York-i tipp. ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Indítsa el a Azure Machine Learning munkaterületet a [Azure Machine learning Studioban](https://ml.azure.com).

1. Nyissa meg a **jegyzetfüzeteket** , és kattintson a **fájlok feltöltése**elemre, válassza a "New York taxi tips. ipynb" lehetőséget, hogy letöltötte és feltöltse a fájlt.
   ![Fájl feltöltése](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. A jegyzetfüzet feltöltése és megnyitása után kattintson az **összes cella futtatása**lehetőségre.

   Előfordulhat, hogy az egyik cella meghibásodik, és az Azure-ban történő hitelesítést kéri. Tartsa szem előtt ezt a cella kimenetében, és végezze el a hitelesítést a böngészőben a hivatkozás követésével és a kód megadásával. Ezután futtassa újra a jegyzetfüzetet.

1. A jegyzetfüzet ONNX-modellt fog betanítani, és regisztrálja azt a MLFlow. Nyissa meg a **modelleket** , és ellenőrizze, hogy az új modell regisztrálása megfelelő-e.
   ![Modell a beállításjegyzékben](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. A jegyzetfüzet futtatásakor a rendszer egy CSV-fájlba is exportálja a tesztelési adatait. Töltse le a CSV-fájlt a helyi rendszeren. Később importálja a CSV-fájlt az SQL-készletbe, majd a modell teszteléséhez használja az adattípust.

   A CSV-fájl a jegyzetfüzet-fájllal megegyező mappában jön létre. Ha nem látja azonnal, kattintson a "frissítés" gombra a fájlkezelőben.

   ![CSV-fájl](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>Előrejelzések indítása SQL-pontozási varázslóval

1. Nyissa meg a szinapszis munkaterületet a szinapszis Studióval.

1. Navigáljon az **adatkapcsolattal**rendelkező  ->  **Linked**  ->  **Storage-fiókokhoz**. Töltse fel `test_data.csv` az alapértelmezett Storage-fiókba.

   ![Adatok feltöltése](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Ugrás az SQL-parancsfájlok **fejlesztéséhez**  ->  **SQL scripts**. Hozzon létre egy új SQL-szkriptet az SQL-készletbe való betöltéshez `test_data.csv` .

   > [!NOTE]
   > A szkript futtatása előtt frissítse a fájl URL-címét.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Az SQL-készletbe való betöltés](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Ugrás az **Data**  ->  **munkaterületre**. Az SQL-pontozási varázsló megnyitásához kattintson a jobb gombbal az SQL-készlet táblára. Válassza **Machine Learning**  ->  **a Machine learning a meglévő modellel való**bővítés lehetőséget.

   > [!NOTE]
   > A Machine learning lehetőség nem jelenik meg, ha nem rendelkezik a Azure Machine Learninghoz létrehozott társított szolgáltatással (lásd az oktatóanyag elején található **Előfeltételek** című részt).

   ![Machine Learning lehetőség](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Válassza ki a csatolt Azure Machine Learning munkaterületet a legördülő listából. Ez betölti a gépi tanulási modellek listáját a kiválasztott Azure Machine Learning munkaterület modell-beállításjegyzékében. Jelenleg csak a ONNX modellek támogatottak, így ez csak ONNX-modelleket fog megjeleníteni.

1. Válassza ki az imént betanított modellt, majd kattintson a **Folytatás**gombra.

   ![Azure Machine Learning modell kiválasztása](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Ezután rendelje hozzá a táblázat oszlopait a modell bemenetekhez, és adja meg a modell kimeneteit. Ha a modellt MLFlow formátumban menti, és a modell aláírása fel van töltve, a megfeleltetés automatikusan történik a hasonlóságok alapján. Az illesztőfelület a manuális leképezést is támogatja.

   Kattintson a **Folytatás** gombra.

   ![Táblázat – modell leképezése](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. A generált T-SQL-kód egy tárolt eljárásba van csomagolva. Ezért meg kell adnia egy nevet a tárolt eljárásnak. A modell bináris fájljai, beleértve a metaadatokat (verzió, leírás stb.) fizikailag át lesznek másolva Azure Machine Learningról egy SQL-készlet táblájába. Ezért meg kell adnia, hogy melyik táblát mentse a modellbe. A "meglévő tábla használata" vagy "új tábla létrehozása" lehetőség közül választhat. Ha elkészült, kattintson a **modell telepítése + szerkesztő megnyitása** lehetőségre a modell üzembe helyezéséhez és egy T-SQL-előrejelzési parancsfájl létrehozásához.

   ![Eljárás létrehozása](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. A szkript létrehozása után kattintson a Futtatás gombra a pontozás végrehajtásához és az előrejelzések lekéréséhez.

   ![Előrejelzések futtatása](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>További lépések

- [Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása a Szinapszisban](quickstart-integrate-azure-machine-learning.md)
- [Az Azure szinapszis Analytics Machine Learning képességei (munkaterületek előzetes verzió)](what-is-machine-learning.md)
