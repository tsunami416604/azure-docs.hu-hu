---
title: Adatok elemzése Azure Machine Learning segítségével
description: Az Azure Machine Learning használatával prediktív gépi tanulási modellt hozhat létre az Azure Synapse-ban tárolt adatok alapján.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 74a6d1aecfc83ea68b9e30453056d231f4bf3e65
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416172"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Adatok elemzése Azure Machine Learning segítségével
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Ez az oktatóanyag az Azure Machine Learning segítségével hozhat létre egy prediktív gépi tanulási modellt az Azure Synapse-ban tárolt adatok alapján. Egy célzott marketingkampányt hozunk létre az Adventure Works kerékpáráruház számára, annak előre jelzése alapján, hogy egy vásárló valószínűleg megvesz-e egy kerékpárt vagy nem.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Az AdventureWorksDW mintaadatokkal előre betöltött SQL-készlet. Ennek létrehozásához [lásd: SQL-készlet létrehozása,](create-data-warehouse-portal.md) és válassza ki a mintaadatok betöltése. Ha már rendelkezik egy adatraktárral, de nincsenek mintaadatai, [a mintaadatokat manuálisan is betöltheti](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Szerezd meg az adatokat
Az adatok az AdventureWorksDW adatbázis dbo.vTargetMail nézetében találhatók. Az adatok olvasása:

1. Jelentkezzen be az [Azure Machine Learning Studio](https://studio.azureml.net/) szolgáltatásba, majd kattintson a Saját kísérletek elemre.
2. Kattintson a képernyő bal alsó sarkában a **+ÚJ** gombra, és válassza az **Üres kísérlet**lehetőséget .
3. Adjon nevet a Célzott marketing kísérletnek.
4. Húzza az **Adatok importálása modult** az **Adatbevitel és kimenet** csoportban a modulok ablaktáblájáról a vászonra.
5. Adja meg az SQL-készlet részleteit a Tulajdonságok ablaktáblán.
6. Adja meg az adatbázishoz a **lekérdezést** az Önt érdeklő adatok olvasásához.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Futtassa a kísérletet a kísérlet vászon alatt található**Futtatás** opcióra kattintva.

![Kísérlet futtatása](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

A kísérlet futtatásának sikeres befejezése után kattintson az Olvasó modul alján található kimeneti portra, és válassza a **Képi megjelenítés** opciót az importált adatok megtekintéséhez.

![Importált adatok megtekintése](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. Tisztítsa meg az adatokat
Az adatok megtisztításához el kell vetni néhány, a modell szempontjából érdektelen oszlopot. Ehhez tegye a következőket:

1. Húzza az **Adatkészlet oszlopban oszlopok kijelölése** modult **az Adatátalakítás < a manipuláció csoportban** a vászonra. Csatlakoztassa ezt a modult az **Adatok importálása** modulhoz.
2. Kattintson a Tulajdonságok panelen az **Oszlopválasztás indítása** opcióra az elvetni kívánt oszlopok megadásához.

   ![Projektoszlopok](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Két oszlop kizárása: CustomerAlternateKey és GeographyKey.

   ![Felesleges oszlopok eltávolítása](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Építsd meg a modellt
80:20 arányban osztjuk fel az adatokat: az adatok 80 % -át a gépi tanulási modell tanításához, 20 %-át a modell teszteléséhez használjuk. A bináris osztályozási probléma "Kétosztályos" algoritmusait fogjuk használni.

1. Húzza a **Felosztás** modult a vászonra.
2. A tulajdonságok ablaktáblán írja be a 0,8 értéket az első kimeneti adatkészlet sorok töredéke mezőbe.

   ![Adatok felosztása tanítási és tesztelési adatkészletre](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Húzza a **Két osztályú súlyozott döntési fa** modult a vászonra.
4. Húzza a **Betanítási modell** modult a vászonra, és adja meg a bemeneteket a **kétosztályos súlyozott döntési fához** (ML algoritmus) és a **Split** (adatok az algoritmus betanításához) modulokhoz csatlakoztatva. 

     ![Csatlakozás a Tanítási modell modulhoz](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Majd kattintson a Tulajdonságok panelen az **Oszlopválasztás indítása** opcióra. Válassza ki a **BikeBuyer** oszlopot előrejelzési oszlopként.

   ![Előrejelzési oszlop kiválasztása](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Pontszám a modell
Most teszteljük, hogyan kezeli a modell a tesztadatokat. Az általunk választott algoritmust összehasonlítjuk egy másik algoritmussal, hogy megtudjuk, melyik teljesít jobban.

1. Húzza **a Score Model** modult a vászonra, és csatlakoztassa a Modell **betanításához** és **az adatok felosztása** modulokhoz.

   ![A modell pontozása](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Húzza a **két osztályú Bayes pontozó gépet** a kísérlet vászonra. Összehasonlítjuk ennek az algoritmusnak a teljesítményét a Két osztályú súlyozott döntési fa teljesítményével.
3. Másolja és illessze be a vászonra a Tanítási és Pontszám modelleket.
4. Húzza a **Modell kiértékelése** modult a vászonra a két algoritmus összehasonlításához.
5. **Futtassa** le a kísérletet.

   ![Kísérlet futtatása](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Kattintson a Modell kiértékelése modul alsó részén található kimeneti portra, majd kattintson a Képi megjelenítés opcióra.

   ![Kiértékelés eredményeinek képi megjelenítése](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

A megadott mérőszámok a ROC-görbe, a pontosság-visszahívási diagram és a felemelésegörbe. A metrikák azt mutatják, hogy az első modell jobban teljesített, mint a második. Ha meg szeretné tekinteni, hogy az első modell mit jósolt, kattintson a score modell kimeneti portjára, és kattintson a Visualize gombra.

![Pontszám modell eredményeinek képi megjelenítése](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

Látni fogja, hogy a tesztelési adatkészletnél megjelent két új oszlop.

* Pontozott valószínűség: annak valószínűsége, hogy az ügyfél kerékpárvásárló.
* Pontozott címkék: a modell által elvégzett osztályozás – kerékpárvásárló (1) vagy sem (0). A címkézés valószínűségi küszöbértéke 50 százalékra van beállítva és módosítható.

A Kerékpárvásárló (tényleges) és a Pontozott címkék (előrejelzés) oszlopok összehasonlításával láthatja, milyen jól teljesített a modell. Ezután használhatja ezt a modellt, hogy előrejelzéseket az új ügyfelek számára, és tegye közzé ezt a modellt webszolgáltatásként, vagy írja az eredményeket vissza az Azure Synapse.

## <a name="next-steps"></a>További lépések
A prediktív gépi tanulási modellek létrehozásával kapcsolatos további tudnivalókért olvassa el [Az Azure Machine Learning bemutatása](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/) című részt.