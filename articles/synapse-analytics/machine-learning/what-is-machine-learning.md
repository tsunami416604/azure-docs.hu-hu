---
title: Machine Learning az Azure szinapszis Analyticsben
description: Az Azure szinapszis Analytics gépi tanulási képességeinek áttekintése.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: b72c20cd67aa4792b5e2a2f96dc39e78301c9219
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91543466"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics-workspaces-preview"></a>Az Azure szinapszis Analytics Machine Learning képességei (munkaterületek előzetes verzió)

[!INCLUDE [preview](../includes/note-preview.md)]

Az Azure szinapszis Analytics különböző gépi tanulási funkciókat kínál. Ez a cikk áttekintést nyújt arról, hogyan alkalmazhat Machine Learning az Azure szinapszis környezetében.

Ez az Áttekintés az adatelemzési folyamat perspektívájában a gépi tanulással kapcsolatos különböző képességeket ismerteti.

Lehet, hogy ismeri a tipikus adatelemzési folyamat megjelenését. Ez egy jól ismert folyamat, amelyet a legtöbb gépi tanulási projekt követ.

A folyamat magas szinten a következő lépéseket tartalmazza:
* (Üzleti ismeretek)
* Adatgyűjtés és adatértelmezés
* Modellezés
* Modell üzembe helyezése és pontozása

Ez a cikk az adatelemzési folyamat perspektívájában ismerteti az Azure szinapszis gépi tanulási képességeit különböző analitikai motorokban. Az adatelemzési folyamat minden egyes lépéseként az Azure szinapszis olyan képességeit összegzi, amelyek segíthetnek az összefoglalásban.

## <a name="azure-synapse-machine-learning-capabilities"></a>Azure szinapszis Machine learning-képességek

### <a name="data-acquisition-and-understanding"></a>Adatgyűjtés és adatértelmezés

A legtöbb gépi tanulási projekt jól kialakított lépéseket tesz elérhetővé, és az egyik lépés az, hogy hozzáférjen és megértse az adatmennyiséget.

#### <a name="data-source-and-pipelines"></a>Adatforrás és folyamatok

A [Azure Data Factorynak](/azure/data-factory/introduction)köszönhetően az Azure szinapszis natívan integrált része, amely az adatfeldolgozási és adatelőkészítési folyamatok számára is hatékony eszközkészlettel rendelkezik. Ez lehetővé teszi az adatfolyamatok egyszerű kiépítését, amelyekkel a gépi tanulás számára felhasználható formátumhoz férhet hozzá és alakíthatja át azokat. További információ a Szinapszisban található [adatfolyamatokról](/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) . 

#### <a name="data-preparation-and-explorationvisualization"></a>Az adatelőkészítés és a feltárás/vizualizáció

A gépi tanulási folyamat fontos része az, hogy megértse az adatelemzést és vizualizációkat.

Az adattárolás helyétől függően a szinapszis különféle eszközöket kínál az elemzéshez és a gépi tanuláshoz való felkészüléshez és előkészítéséhez. Az adatfeltárás első lépéseinek egyik leggyorsabb módja Apache Spark vagy szinapszis SQL Server nélküli készleteket használ közvetlenül a adat-Lake-ben lévő adaton keresztül.

* A [Apache Spark for Azure szinapszis](../spark/apache-spark-overview.md) olyan képességeket kínál, amelyek nagy mennyiségű adatátalakítást, előkészítést és feltárást biztosítanak. Ezek a Spark-készletek olyan eszközöket kínálnak, mint a PySpark/Python, a Scala és a .NET az adatfeldolgozáshoz. A hatékony vizualizációs kódtárak használatával az adatfeltárási élmény jobban megismerheti az adatelemzést. [Ismerje meg, hogyan derítheti fel és jelenítheti meg a szinapszis adatait a Spark használatával](../get-started-analyze-spark.md).

* A [SZINAPSZIS SQL Server nélküli készletek](../sql/on-demand-workspace-overview.md) lehetővé teszik az adatelemzést közvetlenül a TSQL használatával. A szinapszis SQL Server nélküli készletek is kínálnak néhány beépített vizualizációt a szinapszis Studióban. [További információ a SZINAPSZIS SQL Server nélküli készletekkel kapcsolatos](../get-started-analyze-sql-on-demand.md)információk megismeréséről.

### <a name="modeling"></a>Modellezés

Az Azure Szinapszisban a gépi tanulási modelleket a Apache Spark készleteken végezheti el, például a PySpark/Python, a Scala vagy a .NET eszközökkel.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Modellek betanítása a Spark-készleteken a MLlib

A gépi tanulási modelleket különböző algoritmusok és könyvtárak segítségével lehet betanítani. A [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) méretezhető gépi tanulási algoritmusokat kínál, amelyek segítenek a legtöbb klasszikus gépi tanulási probléma megoldásában. [Ez az oktatóanyag](../spark/apache-spark-machine-learning-mllib-notebook.md) bemutatja, hogyan taníthat modelleket a MLlib használatával a szinapszisban.

A MLlib mellett az olyan népszerű kódtárak is használhatók, mint a [Scikit-Learn](https://scikit-learn.org/stable/) modellek. A könyvtárak a szinapszis Spark-készleteken való telepítésével kapcsolatos részletekért lásd: az [Azure szinapszis Analytics Apache Spark könyvtárainak kezelése](../spark/apache-spark-azure-portal-add-libraries.md) .

#### <a name="train-models-with-azure-machine-learning-automl"></a>Modellek betanítása Azure Machine Learning AutoML

A gépi tanulási modellek betanításának egy másik módja, amely nem igényli a gépi tanuláshoz szükséges jóval korábbi ismereteket, hogy a AutoML használja. A [AutoML](/azure/machine-learning/concept-automated-mls) egy olyan szolgáltatás, amely automatikusan betanítja a gépi tanulási modelleket, és lehetővé teszi, hogy a felhasználó kiválassza a legjobb modellt adott mérőszámok alapján. Az Azure szinapszis-jegyzetfüzetek Azure Machine Learningával való zökkenőmentes integrációnak köszönhetően a felhasználók könnyedén kihasználhatják a AutoML a Szinapszisban Azure Active Directory hitelesítéssel.  Ez azt jelenti, hogy csak a Azure Machine Learning munkaterületre kell mutatnia, és nem kell megadnia a hitelesítő adatokat. Itt talál egy [AutoML-oktatóanyagot](../spark/apache-spark-azure-machine-learning-tutorial.md) , amely leírja, hogyan taníthat modelleket Azure Machine learning AutoML használatával a szinapszis Spark-készleteken.

### <a name="model-deployment-and-scoring"></a>Modell üzembe helyezése és pontozása

Az Azure Szinapszisban vagy az Azure szinapszison kívül már betanított modellek könnyen használhatók a Batch-pontozáshoz. Jelenleg a Szinapszisban kétféle módon futtatható a Batch-pontozási szolgáltatás.

* A szinapszis SQL-készletekben a [TSQL prediktív függvény](../sql-data-warehouse/sql-data-warehouse-predict.md) használatával a jóslatokat a megfelelő módon futtathatja. Ez a hatékony és skálázható funkció lehetővé teszi, hogy az adattárházból kifelé irányuló összes adat áthelyezése nélkül gazdagítsa az adatait. A [szinapszis Studióban egy új, interaktív gépi tanulási modellt](https://aka.ms/synapse-ml-ui) ismertetünk, amelyen üzembe HELYEZHET egy ONNX modellt a Azure Machine learning modell beállításjegyzékében a szinapszis SQL-készletekben a Batch-pontozáshoz az előrejelzés használatával.

* Az Azure Szinapszisban a gépi tanulási modellek egy másik lehetősége az Azure szinapszis Apache Spark készletének kihasználása. A modellek betanításához használt könyvtáraktól függően a Batch-pontozási futtatásához használhat programkódot.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Azure szinapszis Analytics szolgáltatással](../get-started.md)
* [Munkaterület létrehozása](../get-started-create-workspace.md)
* [Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása a Szinapszisban](quickstart-integrate-azure-machine-learning.md)
* [Oktatóanyag: Machine learning Model pontozási varázsló – SQL-készlet](tutorial-sql-pool-model-scoring-wizard.md)
