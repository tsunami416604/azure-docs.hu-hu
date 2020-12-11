---
title: 'Oktatóanyag: modell tanítása automatizált ML használatával'
description: Útmutató a gépi tanulási modellek programkód nélküli betanításához az Azure Szinapszisban Apache Spark és automatizált ML használatával.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4967d5305b4b438f3baa6fca078d7b3169612590
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093400"
---
# <a name="tutorial-train-a-machine-learning-model-code-free-in-azure-synapse-with-apache-spark-and-automated-ml"></a>Oktatóanyag: gépi tanulási modell kódjának betanítása – ingyenes az Azure Szinapszisban Apache Spark és automatizált ML-vel

Megtudhatja, hogyan gazdagíthatja adatait a Spark-táblázatokban olyan új gépi tanulási modellekkel, amelyeket a [Azure Machine learning AUTOMATIZÁLT ml-](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)t használ.  A szinapszis egyik felhasználója egyszerűen kiválaszthatja az Azure szinapszis munkaterületen egy Spark-táblázatot, amely képzési adatkészletként használható a gépi tanulási modellek kód nélküli felhasználói felületének létrehozásához.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> - Gépi tanulási modellek betanítása az Azure szinapszis Studióban, amely automatizált ML-t használ a Azure Machine Learningban. A betanított modell típusa a megoldani kívánt problémától függ.

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

## <a name="launch-automated-ml-wizard-to-train-a-model"></a>Automatizált ML varázsló elindítása modell betanításához

Kattintson a jobb gombbal az előző lépésben létrehozott Spark-táblára. A varázsló megnyitásához válassza a "Machine Learning-> gazdagítás új modellel" lehetőséget.
![Automatikus ML varázsló indítása](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Megjelenik egy konfigurációs panel, és a rendszer arra kéri, hogy adja meg a konfigurációs adatokat a Azure Machine Learning-ben futtatott automatikus ML-kísérlet létrehozásához. Ez a Futtatás a több modellt is betanítja, és a sikeres futtatásból származó legjobb modell regisztrálva lesz a Azure Machine Learning modell beállításjegyzékében:

![Futtatási 1. lépés konfigurálása](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Azure Machine learning munkaterület**: az automatikus ml-kísérlet futtatásához szükség van egy Azure Machine learning-munkaterületre. Az Azure szinapszis-munkaterületet társított [szolgáltatás](quickstart-integrate-azure-machine-learning.md)használatával is össze kell kapcsolni az Azure Machine learning munkaterülettel. Az összes előzetes = követelmény megadása után megadhatja az automatikus ML-futtatáshoz használni kívánt Azure Machine Learning munkaterületet.

- **Kísérlet neve**: adja meg a kísérlet nevét. Amikor automatikus ML-t küld el, a kísérlet nevét adja meg. A futtatással kapcsolatos információkat a rendszer a Azure Machine Learning munkaterületen található kísérlet alatt tárolja. Ez a felhasználói élmény alapértelmezés szerint új kísérletet hoz létre, és egy javasolt nevet generál, de egy meglévő kísérlet nevét is megadhatja.

- **Legjobb modell**: adja meg a legjobb modell nevét az automatikus ml futtatásból. A legjobb modell ezt a nevet adja meg, és a Futtatás után automatikusan a Azure Machine Learning modell beállításjegyzékében lesz mentve. Az automatizált ML-futtatások számos gépi tanulási modellt hoznak létre. Egy későbbi lépésben kiválasztott elsődleges metrika alapján ezeket a modelleket össze lehet hasonlítani, és a legjobb modellt ki lehet választani.

- **Cél oszlop**: ez az a modell, amelyet előre kell tanítani. Válassza ki az előre jelezni kívánt oszlopot.

- **Spark-készlet**: az automatikus ml kísérlet futtatásához használni kívánt Spark-készlet. A számítások a megadott készleten lesznek végrehajtva.

- **Spark-konfiguráció részletei**: a Spark-készleten kívül lehetősége van a munkamenet-konfiguráció részleteinek megadására is.

Ebben az oktatóanyagban kiválasztjuk a numerikus oszlopot `fareAmount` a cél oszlopként.

Kattintson a "Folytatás" gombra.

## <a name="choose-task-type"></a>Feladat típusának kiválasztása

Válassza ki a gépi tanulási modell típusát a kísérlethez a válaszolni kívánt kérdés alapján. Mivel `fareAmount` a cél oszlopként van kiválasztva, és numerikus érték, a *regressziót* fogjuk kiválasztani.

Kattintson a "Folytatás" gombra a további beállítások konfigurálásához.

![Feladattípus kiválasztása](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>További konfigurációk

Ha a *besorolás* vagy a *regresszió* típusa lehetőséget választja, a további konfigurációk a következők:

- **Elsődleges metrika**: a modell működésének méréséhez használt metrika. Ez az a metrika, amelyet a rendszer az automatikus ML-futtatás során létrehozott különböző modellek összehasonlítására fog használni, és meghatározza, hogy melyik modell lett a legjobban elvégezve.

- **Betanítási idő (óra)**: az a maximális időtartam (óra), amely a kísérlet futtatására és a modellek betanítására használható. Vegye figyelembe, hogy az 1 értéknél kisebb értékeket is megadhat. Például: `0.5`.

- **Egyidejű ismétlések** maximális száma: a párhuzamosan végrehajtandó ismétlések maximális számát jelöli.

- **ONNX-modell kompatibilitása**: Ha engedélyezve van, az automatikus ml által betanított modellek a ONNX formátumba lesznek konvertálva. Ez különösen akkor fontos, ha a modellt szeretné használni az Azure szinapszis SQL-készletekben való pontozáshoz.

Ezek a beállítások mindegyike alapértelmezett értékkel rendelkezik, amelyet testreszabhat.
![További konfigurációk](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Vegye figyelembe, hogy ha a "Time Series előrejelzés" lehetőséget választja, több konfigurációra van szükség. Az előrejelzés emellett nem támogatja a ONNX-modell kompatibilitását.

Ha elkészült az összes szükséges konfigurációval, elindíthatja az automatikus ML-futtatást.

Az Azure Azure Szinapszisban kétféle automatizált ML-futtatást indíthat el. A kód nélküli használat érdekében dönthet úgy, hogy közvetlenül **futtatja a futtatást** . Ha inkább a kód lehetőséget választja, a **Megnyitás jegyzetfüzetben** lehetőségre kattintva megtekintheti a futtatást létrehozó kódot, és futtathatja a jegyzetfüzetet.

### <a name="create-run-directly"></a>Közvetlen Futtatás létrehozása

Kattintson a "Futtatás indítása" lehetőségre az automatikus ML közvetlen futtatásának elindításához. Ekkor megjelenik egy értesítés, amely jelzi, hogy az automatikus ML Futtatás elindul.

Az automatikus ML-Futtatás sikeres elindítása után egy másik sikeres értesítés jelenik meg. Az értesítés gombra kattintva megtekintheti a futtatási beküldési állapotot is.
Azure Machine Learning a sikeres értesítésben szereplő hivatkozásra kattintva.
![Sikeres értesítés](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Futtatás jegyzetfüzettel

Jegyzetfüzet létrehozásához válassza *a Megnyitás jegyzetfüzetből* lehetőséget. Kattintson az *összes futtatása* elemre a jegyzetfüzet végrehajtásához.
Ez lehetőséget biztosít további beállítások hozzáadására is az automatikus ML-futtatáshoz.

![Jegyzetfüzet megnyitása](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

A jegyzetfüzetből való futtatás sikeres elküldését követően a rendszer a jegyzetfüzet kimenetének Azure Machine Learning munkaterületén található kísérletre mutató hivatkozást tartalmaz. A hivatkozásra kattintva figyelheti az automatikus ML-t Azure Machine Learning.
![A jegyzetfüzet összes futtatása ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: gépi tanulási modellek pontozása az Azure szinapszis DEDIKÁLT SQL-készletekben](tutorial-sql-pool-model-scoring-wizard.md).
- [Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása az Azure Szinapszisban](quickstart-integrate-azure-machine-learning.md)
- [Az Azure szinapszis Analytics Machine Learning képességei](what-is-machine-learning.md)
