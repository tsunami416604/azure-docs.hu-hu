---
title: 'Oktatóanyag: gépi tanulási modell betanítása a AutoML használatával'
description: 'Oktatóanyag: gépi tanulási modell betanítása az Azure Szinapszisban az AutoML használatával.'
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8dd99b60a548e3c392bbe468ddde484081e6eb8b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468264"
---
# <a name="tutorial-code-free-machine-learning-model-training-in-azure-synapse-with-automl-preview"></a>Oktatóanyag: ingyenes gépi tanulási modell betanítása az Azure Szinapszisban a AutoML (előzetes verzió)

Megtudhatja, hogyan gazdagíthatja adatait a Spark-táblákban olyan új gépi tanulási modellekkel, amelyeket a [Azure Machine learning AutoML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)használatával tanít.  A Szinapszisban egy felhasználó egyszerűen kiválaszthatja az Azure szinapszis munkaterületen egy Spark-táblázatot, amely képzési adatkészletként használható a gépi tanulási modellek kódjának ingyenes felhasználói felületén történő létrehozásához.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> - Gépi tanulási modellek betanítása az Azure szinapszis Studióban, amely automatizált ML-t használ az Azure ML-ben. A betanított modell típusa a megoldani kívánt problémától függ.

Ha nem rendelkezik Azure-előfizetéssel, [a Kezdés előtt hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- A [szinapszis Analytics-munkaterület](../get-started-create-workspace.md) egy ADLS Gen2 Storage-fiókkal, amely alapértelmezett tárolóként van konfigurálva. A ADLS Gen2-fájlrendszer **Storage blob-Adatközreműködőinek** kell lennie.
- Spark-készlet az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: [Spark-készlet létrehozása az Azure szinapszisban](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning társított szolgáltatás az Azure szinapszis Analytics-munkaterületen. Részletekért lásd: [Azure Machine learning társított szolgáltatás létrehozása az Azure szinapszisban](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="create-a-spark-table-for-training-dataset"></a>Spark-táblázat létrehozása a betanítási adatkészlethez

Ehhez az oktatóanyaghoz szüksége lesz egy Spark-táblázatra. A következő jegyzetfüzet létrehoz egy Spark-táblázatot.

1. Töltse le a notebook [create-Spark-Table-NYCTaxi-adat. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Importálja a jegyzetfüzetet az Azure szinapszis studióba.
![Jegyzetfüzet importálása](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Válassza ki a használni kívánt Spark-készletet, majd kattintson a elemre `Run all` . A jegyzetfüzet futtatásakor a rendszer beolvassa a New York-i taxi adatait a nyitott adatkészletből, és menti az alapértelmezett Spark-adatbázist.
![Az összes futtatása](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. A jegyzetfüzet futtatásának befejeződése után létrejön egy új Spark-tábla az alapértelmezett Spark-adatbázisban. Nyissa meg az adatközpontot, és keresse meg a nevű táblát `nyc_taxi` .
![Spark-táblázat](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automl-wizard-to-train-a-model"></a>A AutoML varázsló elindítása a modell betanításához

Kattintson a jobb gombbal az előző lépésben létrehozott Spark-táblára. A varázsló megnyitásához válassza a "Machine Learning-> gazdagítás új modellel" lehetőséget.
![AutoML elindítása varázsló](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Megjelenik egy konfigurációs panel, és a rendszer arra kéri, hogy adja meg a AutoML-kísérlet létrehozásához szükséges konfigurációs adatokat Azure Machine Learningban. Ez a Futtatás a több modell betanítását és a sikeres Futtatás legjobb modelljét fogja regisztrálni az Azure ML-modell beállításjegyzékében:

![Futtatási 1. lépés konfigurálása](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Azure ml-munkaterület**: szükség van egy Azure ml-munkaterületre a AutoML kísérlet futtatásához. A [társított szolgáltatás](quickstart-integrate-azure-machine-learning.md)használatával az Azure-beli szinapszis-munkaterületet is össze kell kapcsolni az Azure ml-munkaterülettel. Miután megtörtént az összes előzetes = követelmény, megadhatja a AutoML használni kívánt Azure ML-munkaterületet.

- **Kísérlet neve**: adja meg a kísérlet nevét. A AutoML futtatásakor meg kell adnia egy kísérlet nevét. A futtatással kapcsolatos információk tárolása az Azure ML-munkaterületen, az adott kísérlet alatt történik. Ez a felhasználói élmény alapértelmezés szerint új kísérletet hoz létre, és egy javasolt nevet generál, de egy meglévő kísérlet nevét is megadhatja.

- **Legjobb modell**: adja meg a AutoML futtatásához a legjobb modell nevét. A legjobb modell ezt a nevet adja meg, és a Futtatás után automatikusan az Azure ML-modell beállításjegyzékében lesz mentve. Egy AutoML-Futtatás számos gépi tanulási modellt fog létrehozni. Egy későbbi lépésben kiválasztott elsődleges metrika alapján ezeket a modelleket össze lehet hasonlítani, és a legjobb modellt ki lehet választani.

- **Cél oszlop**: ez az a modell, amelyet előre kell tanítani. Válassza ki az előre jelezni kívánt oszlopot.

- **Spark-készlet**: a AutoML kísérlet futtatásához használni kívánt Spark-készlet. A számítások a megadott készleten lesznek végrehajtva.

- **Spark-konfiguráció részletei**: a Spark-készleten kívül lehetősége van a munkamenet-konfiguráció részleteinek megadására is.

Ebben az oktatóanyagban kiválasztjuk a numerikus oszlopot `fareAmount` a cél oszlopként.

Kattintson a "Folytatás" gombra.

## <a name="choose-task-type"></a>Feladat típusának kiválasztása

Válassza ki a gépi tanulási modell típusát a kísérlethez a válaszolni kívánt kérdés alapján. Mivel `fareAmount` a cél oszlopként van kiválasztva, és numerikus érték, a *regressziót* fogjuk kiválasztani.

Kattintson a "Folytatás" gombra a további beállítások konfigurálásához.

![Feladattípus kiválasztása](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>További konfigurációk

Ha a *besorolás* vagy a *regresszió* típusa lehetőséget választja, a további konfigurációk a következők:

- **Elsődleges metrika**: a modell működésének méréséhez használt metrika. Ez az a metrika, amelyet a rendszer a AutoML-futtatás során létrehozott különböző modellek összehasonlítására fog használni, és meghatározhatja, hogy melyik modellt végezte el legjobban.

- **Betanítási idő (óra)**: az a maximális időtartam (óra), amely a kísérlet futtatására és a modellek betanítására használható. Vegye figyelembe, hogy az 1 értéknél kisebb értékeket is megadhat. Például: `0.5`.

- **Egyidejű ismétlések** maximális száma: a párhuzamosan végrehajtandó ismétlések maximális számát jelöli.

- **ONNX-modell kompatibilitása**: Ha engedélyezve van, a AutoML által betanított modellek a ONNX formátumba lesznek konvertálva. Ez különösen akkor fontos, ha a modellt szeretné használni az Azure szinapszis SQL-készletekben való pontozáshoz.

Ezek a beállítások mindegyike alapértelmezett értékkel rendelkezik, amelyet testreszabhat.
![További konfigurációk](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Vegye figyelembe, hogy az "idősorozat-előrejelzés" beállításnál az FF esetében több konfigurációra van szükség. Az előrejelzés emellett nem támogatja a ONNX-modell kompatibilitását.

Miután az összes szükséges konfigurációt elvégezte, elindíthatja a AutoML futtatását.

A AutoML kétféleképpen indítható el az Azure Azure Szinapszisban. A kód ingyenes használata esetén dönthet úgy, hogy közvetlenül **futtatja a futtatást** . Ha inkább a kód lehetőséget választja, a **Megnyitás jegyzetfüzetben** lehetőségre kattintva megtekintheti a futtatást létrehozó kódot, és futtathatja a jegyzetfüzetet.

### <a name="create-run-directly"></a>Közvetlen Futtatás létrehozása

A AutoML futtatásának megkezdéséhez kattintson a "Futtatás elindítása" gombra. Ekkor megjelenik egy értesítés, amely jelzi, hogy a AutoML Futtatás elindul.

A AutoML futtatásának sikeres elindítása után egy másik sikeres értesítés jelenik meg. Az értesítés gombra kattintva megtekintheti a futtatási beküldési állapotot is.
Az Azure ML-t a sikeres értesítésben szereplő hivatkozásra kattintva.
![Sikeres értesítés](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Futtatás jegyzetfüzettel

Jegyzetfüzet létrehozásához válassza *a Megnyitás jegyzetfüzetből* lehetőséget. Kattintson az *összes futtatása* elemre a jegyzetfüzet végrehajtásához.
Ez lehetőséget biztosít további beállítások hozzáadására is a AutoML futtatásához.

![Jegyzetfüzet megnyitása](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

A jegyzetfüzetből való futtatást követően a rendszer a jegyzetfüzet kimenetében található Azure ML munkaterületen futtatja a kísérletre mutató hivatkozást. A hivatkozásra kattintva megfigyelheti a AutoML az Azure ML-ben.
![A jegyzetfüzet összes futtatása ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: gépi tanulási modellek pontozása az Azure szinapszis DEDIKÁLT SQL-készletekben](tutorial-sql-pool-model-scoring-wizard.md).
- [Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása az Azure Szinapszisban](quickstart-integrate-azure-machine-learning.md)
- [Az Azure szinapszis Analytics Machine Learning képességei](what-is-machine-learning.md)