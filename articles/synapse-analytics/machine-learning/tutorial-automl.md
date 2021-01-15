---
title: 'Oktatóanyag: modell betanítása automatizált gépi tanulás használatával'
description: Útmutató a gépi tanulási modellek kód nélküli betanításához az Azure szinapszis Analyticsben.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: f3b0c5f1487951d05bc83973e5b4b9f3634a694b
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222258"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Oktatóanyag: gépi tanulási modell betanítása kód nélkül

A Spark-táblázatokban lévő adatait az [automatikus gépi tanulás](../../machine-learning/concept-automated-ml.md)használatával betanított új gépi tanulási modellekkel bővítheti. Az Azure szinapszis Analyticsben kiválaszthat egy Spark-táblázatot a munkaterületen, amely képzési adatkészletként használható a gépi tanulási modellek létrehozásához, és ezt kód nélküli felhasználói élményben teheti meg.

Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a gépi tanulási modellek betanítását az Azure szinapszis Analytics Studio kód nélküli felhasználói felületének használatával. Az automatizált gépi tanulást Azure Machine Learning használhatja a felhasználói felület manuális kódolása helyett. A betanított modell típusa a megoldani kívánt problémától függ.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Egy [Azure szinapszis Analytics-munkaterület](../get-started-create-workspace.md). Győződjön meg arról, hogy a következő Storage-fiók van beállítva alapértelmezett tárolóként: Azure Data Lake Storage Gen2. A Data Lake Storage Gen2 használt fájlrendszernél győződjön meg róla, hogy Ön a **Storage blob adatközreműködői**.
- Egy Apache Spark-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: gyors útmutató [: DEDIKÁLT SQL-készlet létrehozása az Azure szinapszis Analytics Studio használatával](../quickstart-create-sql-pool-studio.md).
- Egy Azure Machine Learning társított szolgáltatás az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: gyors útmutató [: új Azure Machine learning társított szolgáltatás létrehozása az Azure szinapszis Analyticsben](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-spark-table-for-training-dataset"></a>Spark-táblázat létrehozása a betanítási adatkészlethez

Ebben az oktatóanyagban egy Spark-táblára van szükség. A következő jegyzetfüzet létrehoz egyet.

1. Töltse le a notebook [create-Spark-Table-NYCTaxi-adat. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Importálja a jegyzetfüzetet az Azure szinapszis Analytics studióba.
![Képernyőkép az Azure szinapszis Analyticsről, az Importálás lehetőség kiemelve.](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Válassza ki a használni kívánt Spark-készletet, majd válassza az **összes futtatása** lehetőséget. Ez beolvassa a New York-i taxi adatait a megnyitott adatkészletből, és elmenti az alapértelmezett Spark-adatbázisba.
![Képernyőfelvétel az Azure szinapszis Analyticsről, az összes és a Spark-adatbázis kiemelésével.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. A jegyzetfüzet futtatásának befejeződése után egy új Spark-tábla jelenik meg az alapértelmezett Spark-adatbázisban. Az **adatok** területen keresse meg **nyc_taxi** nevű táblázatot.
![Képernyőkép az Azure szinapszis Analytics-adatlapokról, új táblázat kiemelve.](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automated-machine-learning-wizard"></a>Automatikus gépi tanulás varázsló indítása

Ezt a következőképpen teheti meg:

1. Kattintson a jobb gombbal az előző lépésben létrehozott Spark-táblára. A varázsló megnyitásához válassza a **Machine learning**  >  **gazdagítás új modellel** lehetőséget.
![Képernyőfelvétel a Spark-táblázatról, Machine Learning és gazdagítva az új modell kiemelésével.](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Ezután megadhatja a konfiguráció részleteit, amelyekkel egy automatizált gépi tanulási kísérletet hozhat létre Azure Machine Learningban. Ez több modellt is futtat, és a sikeres Futtatás legjobb modellje regisztrálva van a Azure Machine Learning modell beállításjegyzékében.

   ![Képernyőkép a gazdagabbá az új modell konfigurációs specifikációkkal.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine learning munkaterület**: az automatikus gépi tanulási kísérlet futtatásához egy Azure Machine learning munkaterület szükséges. Az Azure szinapszis Analytics-munkaterületet társított [szolgáltatás](quickstart-integrate-azure-machine-learning.md)használatával is össze kell kapcsolni az Azure Machine learning munkaterülettel. Miután teljesítette az összes előfeltételt, megadhatja az automatikus futtatáshoz használni kívánt Azure Machine Learning munkaterületet.

    - **Kísérlet neve**: adja meg a kísérlet nevét. Ha automatikus gépi tanulást végez, a kísérlet nevét adja meg. A futtatással kapcsolatos információkat a rendszer a Azure Machine Learning munkaterületen található kísérlet alatt tárolja. Ez a felület alapértelmezés szerint létrehoz egy új kísérletet, és létrehoz egy javasolt nevet, de megadhat egy meglévő kísérlet nevét is.

    - **Legjobb modell**: adja meg a legjobb modell nevét az automatikus futtatásból. A legjobb modell ezt a nevet adja meg, és a Futtatás után automatikusan a Azure Machine Learning modell beállításjegyzékében lesz mentve. A gépi tanulás automatizált futtatása számos gépi tanulási modellt hoz létre. Egy későbbi lépésben kiválasztott elsődleges metrika alapján ezek a modellek összehasonlíthatók, a legjobb modell pedig kiválasztható.

    - **Cél oszlop**: ez az a modell, amelyet előre kell tanítani. Válassza ki az előre jelezni kívánt oszlopot. (Ebben az oktatóanyagban a numerikus oszlopot válassza `fareAmount` a cél oszlopként.)

    - **Spark-készlet**: az automatizált kísérlet futtatásához használni kívánt Spark-készlet. A számítások a megadott készleten futnak.

    - **Spark-konfiguráció részletei**: a Spark-készleten kívül lehetősége van a munkamenet-konfiguráció részleteinek megadására is.

1. Válassza a **Folytatás** lehetőséget.

## <a name="choose-task-type"></a>Feladat típusának kiválasztása

Válassza ki a gépi tanulási modell típusát a kísérlethez a válaszolni kívánt kérdés alapján. Mivel `fareAmount` a cél oszlop, és numerikus érték, válassza a **regresszió** itt lehetőséget. Ezután válassza a **Folytatás** elemet.

![Képernyőkép az új modellel való bővítésről, regressziós kiemelve.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>További konfigurációk

Ha az előző szakaszban a modell típusaként a **regresszió** vagy a **besorolás** lehetőséget választja, a következő konfigurációk érhetők el:

- **Elsődleges metrika**: a modell működésének méréséhez használt metrika. Ez az automatikus futtatás során létrehozott különböző modellek összehasonlításához használt metrika, valamint annak meghatározása, hogy melyik modell lett a legjobban elvégezve.

- **Betanítási idő (óra)**: az a maximális időtartam (óra), amely a kísérlet futtatására és a modellek betanítására használható. Vegye figyelembe, hogy 1 értéknél kisebb értékeket is megadhat (például `0.5` ).

- **Egyidejű ismétlések** maximális száma: a párhuzamosan futó ismétlések maximális számát jelöli.

- **ONNX-modell kompatibilitása**: Ha engedélyezi ezt a beállítást, az automatizált gépi tanulás által betanított modellek a ONNX formátumba lesznek konvertálva. Ez különösen akkor fontos, ha a modellt szeretné használni az Azure szinapszis Analytics SQL-készletekben való pontozáshoz.

Ezek a beállítások mindegyike alapértelmezett értékkel rendelkezik, amelyet testreszabhat.
![Képernyőfelvétel az új modellekkel való gazdagítás további konfigurációkról.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Miután az összes szükséges konfigurációt elvégezte, elkezdheti az automatikus futtatást. Válassza a **Futtatás létrehozása** lehetőséget, amely a futtatást közvetlenül, kód nélkül indítja el. Ha inkább a Code-ot részesíti előnyben, **a Megnyitás jegyzetfüzetben** lehetőség kiválasztásával. Ez a beállítás lehetővé teszi, hogy megtekintse azt a kódot, amely létrehozza a futtatást, és futtatja a jegyzetfüzetet.

>[!NOTE]
>Ha az előző szakaszban a modell típusaként az **idősorozat-előrejelzés** lehetőséget választja, további konfigurációkat kell elvégeznie. Az előrejelzés szintén nem támogatja a ONNX-modell kompatibilitását.

### <a name="create-run-directly"></a>Közvetlen Futtatás létrehozása

Az automatikus gépi tanulás közvetlen futtatásának megkezdéséhez válassza a **Futtatás indítása** lehetőséget. Ekkor megjelenik egy értesítés, amely jelzi, hogy a Futtatás elindul. Ezután megjelenik egy másik értesítés, amely jelzi a sikert. A Azure Machine Learning állapotának ellenőrzéséhez kattintson az értesítésben található hivatkozásra.
![Képernyőkép a sikeres értesítésről.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Futtatás jegyzetfüzettel

Jegyzetfüzet létrehozásához válassza **a Megnyitás jegyzetfüzetben** lehetőséget. Ezután válassza **az összes futtatása** lehetőséget. Ez lehetőséget biztosít további beállítások hozzáadására is az automatizált gépi tanulási futtatáshoz.

![Képernyőkép a jegyzetfüzetről, az összes Kiemelt futtatásával.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Miután sikeresen elküldte a futtatást, megjelenik egy hivatkozás a kísérlet futtatásához a Azure Machine Learning munkaterületen a jegyzetfüzet kimenetében. Válassza ki a hivatkozást a Azure Machine Learning automatikus futtatásának figyeléséhez.
![Képernyőkép az Azure szinapszis Analyticsről a hivatkozás kiemelésével. ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: gépi tanulási modell pontozása varázsló (előzetes verzió) dedikált SQL-készletekhez](tutorial-sql-pool-model-scoring-wizard.md)
- [Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása az Azure szinapszis Analyticsben](quickstart-integrate-azure-machine-learning.md)
- [Gépi tanulási képességek az Azure szinapszis Analyticsben](what-is-machine-learning.md)