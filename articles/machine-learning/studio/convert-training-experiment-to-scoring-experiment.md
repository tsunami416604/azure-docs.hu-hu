---
title: Modell előkészítése a telepítéshez
titleSuffix: ML Studio (classic) - Azure
description: Hogyan készítse elő a betanított modellt webszolgáltatásként való üzembe helyezéshez a Machine Learning Studio (klasszikus) betanítási kísérlet ének prediktív kísérletté alakításával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204511"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>A modell előkészítése az Azure Machine Learning Studióban való üzembe helyezéshez (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Az Azure Machine Learning Studio (klasszikus) biztosítja a prediktív elemzési modell kifejlesztéséhez szükséges eszközöket, majd üzembe helyezésével azt Egy Azure webszolgáltatásként.

Ehhez a Studio (klasszikus) segítségével hozzon létre egy kísérletet - úgynevezett *betanítási kísérlet* - ahol a modell betanítása, pontszám, és szerkesztheti a modellt. Miután elégedett, készen áll a modell üzembe helyezésére a betanítási kísérlet átalakításával egy *prediktív kísérlet,* amely úgy van beállítva, hogy a felhasználói adatok pontozására.

Erre a folyamatra egy példát láthat az [1.](tutorial-part1-credit-risk.md)

Ez a cikk részletesen bemutatja, hogyan alakul át egy betanítási kísérlet egy prediktív kísérletté, és hogyan telepíti ezt a prediktív kísérletet. Ezeket a részleteket ismerve megtudhatja, hogyan konfigurálhatja az üzembe helyezett modellt, hogy hatékonyabbá tegye.



## <a name="overview"></a>Áttekintés 

A betanítási kísérlet prediktív kísérletté történő átalakításának folyamata három lépésből áll:

1. Cserélje le a gépi tanulási algoritmus modulok a betanított modell.
2. Vágja le a kísérletet, hogy csak azokat a modulokat, amelyek szükségesek a pontozáshoz. A betanítási kísérlet számos modult tartalmaz, amelyek szükségesek a betanításhoz, de nincs szükség, ha a modell be van tanítva.
3. Adja meg, hogy a modell hogyan fogadja el a webszolgáltatás felhasználójától származó adatokat, és milyen adatokat ad vissza.

> [!TIP]
> A betanítási kísérlet, már érintett betanítása és pontozási a modell segítségével a saját adatait. De üzembe helyezése után a felhasználók új adatokat küldenek a modellnek, és előrejelzési eredményeket adnak vissza. Így a betanítási kísérlet átalakítása egy prediktív kísérlet, hogy készen áll a telepítésre, ne feledje, hogy a modell fogja használni mások által.
> 
> 

## <a name="set-up-web-service-button"></a>Webszolgáltatás beállítása gomb
A kísérlet futtatása után (kattintson a **futtatás** gombra a kísérlet vászon alján), kattintson a **Webszolgáltatás beállítása** gombra (válassza a **Prediktív webszolgáltatás** lehetőséget). **A Webszolgáltatás beállítása** a betanítási kísérlet prediktív kísérletté alakításának három lépése:

1. Ez menti a betanított modell a modul paletta **Betanított modellek** szakaszában (a kísérlet vászon bal oldalán). Ezután lecseréli a gépi tanulási algoritmusés [a modell betanítási][train-model] modulok a mentett betanított modell.
2. Elemzi a kísérletet, és eltávolítja azokat a modulokat, amelyeket egyértelműen csak a képzéshez használtak, és amelyekre már nincs szükség.
3. Webszolgáltatás _bemeneti_ és _kimeneti_ modulokat illeszt be a kísérlet alapértelmezett helyeibe (ezek a modulok elfogadják és visszaadják a felhasználói adatokat).

Például a következő kísérlet egy kétosztályos kiemelt döntési famodellt képez ki a népszámlálási adatok alapján:

![Képzési kísérlet](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

A modul ebben a kísérletben alapvetően négy különböző funkciót lát el:

![Modulfunkciók](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Amikor ezt a betanítási kísérletet prediktív kísérletté alakítja, néhány ilyen modulra már nincs szükség, vagy most más célt szolgálnak:

* **Adatok** – A minta adatkészletben lévő adatok nem használatosak a pontozás során – a webszolgáltatás felhasználója adja meg a pontozandó adatokat. Az adatkészlet metaadatait azonban, például az adattípusokat, a betanított modell használja. Ezért meg kell tartania az adatkészletet a prediktív kísérletben, hogy azok biztosíthassák ezeket a metaadatokat.

* **Prep** - Attól függően, hogy a felhasználói adatokat, hogy a beküldendő pontozási, ezek a modulok lehet, hogy nem szükséges a bejövő adatok feldolgozásához. A **Webszolgáltatás beállítása** gomb nem érinti ezeket – el kell döntenie, hogyan szeretné kezelni őket.
  
    Ebben a példában például előfordulhat, hogy a mintaadatkészletből hiányoznak az értékek, ezért egy [hiányzó adatok tisztítási][clean-missing-data] modult is tartalmazott azok kezeléséhez. A minta adatkészlet olyan oszlopokat is tartalmaz, amelyek nem szükségesek a modell betanításához. Így az [Adatkészlet modul oszlopok kiválasztása][select-columns] modult tartalmazott, hogy kizárja ezeket az extra oszlopokat az adatfolyamból. Ha tudja, hogy a webszolgáltatáson keresztül pontozásra elküldött adatok nem lesznek hiányzó értékek, majd eltávolíthatja a [Hiányzó adatok tisztítása modult.][clean-missing-data] Mivel azonban az Oszlopok kijelölése az [adatkészletmodulban][select-columns] segít meghatározni a betanított modell által várt adatoszlopokat, a modulnak meg kell maradnia.

* **Train** - Ezek a modulok a modell betanítására szolgálnak. Ha a **Webszolgáltatás beállítása gombra**kattint, ezeket a modulokat a modul egyetlen modulra cseréli, amely a betanított modellt tartalmazza. Ez az új modul a modulpaletta **Betanított modellek** szakaszábakerül.

* **Pontszám** – Ebben a példában a [Split Data][split] modul az adatfolyam felosztására szolgál a tesztadatok és a betanítási adatok. A prediktív kísérletben már nem vagyunk betanítás, így [a Split Data][split] eltávolítható. Hasonlóképpen a második [Score Model][score-model] modul és a [Modell kiértékelése][evaluate-model] modul a tesztadatok eredményeinek összehasonlítására szolgál, így ezek a modulok nem szükségesek a prediktív kísérletben. A fennmaradó [score modell][score-model] modul, azonban van szükség, hogy adja vissza a pontszám eredménye a webszolgáltatáson keresztül.

Így néz ki a példa, miután a **Webszolgáltatás beállítása**gombra kattintott:

![Konvertált prediktív kísérlet](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

**A Webszolgáltatás beállítása** által végzett munka elegendő lehet a kísérlet webszolgáltatásként való üzembe helyezéséhez. Előfordulhat azonban, hogy a kísérlethez tartozó további munkát is el szeretne végezni.

### <a name="adjust-input-and-output-modules"></a>Bemeneti és kimeneti modulok beállítása
A betanítási kísérletben egy betanítási adatokat használt, majd némi feldolgozást végzett az adatok leküzdéséhez egy olyan formában, amelyre a gépi tanulási algoritmusnak szüksége van. Ha a webszolgáltatáson keresztül várhatóan fogadott adatoknak nem lesz szükségük erre a feldolgozásra, megkerülheti azt: csatlakoztassa a **webszolgáltatás bemeneti moduljának** kimenetét egy másik modulhoz a kísérletben. A felhasználó adatai most érkeznek meg a modell ezen a helyen.

Alapértelmezés szerint például **a Webszolgáltatás beállítása** alapértelmezés szerint a **webszolgáltatás beviteli** modulja az adatfolyam tetejére kerül, ahogy az a fenti ábrán is látható. De manuálisan eltudjuk helyezni a **webszolgáltatás bemenetét** az adatfeldolgozó modulokon túl:

![A webszolgáltatás bemenetének áthelyezése](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

A webszolgáltatáson keresztül megadott bemeneti adatok mostantól közvetlenül a Score Model modul előzetes feldolgozás nélkül.

Hasonlóképpen, alapértelmezés szerint **a Webszolgáltatás beállítása** a webszolgáltatás kimeneti modulját az adatfolyam aljára helyezi. Ebben a példában a webszolgáltatás visszatér a felhasználónak a [Score Model][score-model] modul kimenetét, amely tartalmazza a teljes bemeneti adatok vektor, valamint a pontozási eredmények.
Ha azonban valami mást szeretne visszaadni, akkor további modulokat is hozzáadhat a **webszolgáltatás kimeneti modulja** előtt. 

Ha például csak a pontozási eredményeket szeretné visszaadni, és nem a bemeneti adatok teljes vektorát, adjon hozzá egy [Oszlopok kijelölése az Adatkészlet modulban,][select-columns] hogy a pontozási eredmények kivételével az összes oszlopot kizárja. Ezután helyezze át a **webszolgáltatás kimeneti** modulját az Oszlopok kijelölése az [Adatkészlet modulban][select-columns] kimenetére. A kísérlet így néz ki:

![A webszolgáltatás kimenetének áthelyezése](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>További adatfeldolgozási modulok hozzáadása vagy eltávolítása
Ha több modul van a kísérletben, amelyről tudja, hogy nem lesz szükség a pontozás során, ezek eltávolíthatók. Például azért, mert a **webszolgáltatás bemeneti** modulját az adatfeldolgozási modulok után egy pontra helyeztük át, eltávolíthatjuk a [Tiszta hiányzó adatok modult][clean-missing-data] a prediktív kísérletből.

A prediktív kísérletünk most így néz ki:

![További modul eltávolítása](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Választható webszolgáltatás-paraméterek hozzáadása
Bizonyos esetekben előfordulhat, hogy engedélyezni szeretné a webszolgáltatás felhasználójának a modulok viselkedését a szolgáltatás elérésekor. *A webszolgáltatás paraméterei* lehetővé teszik ezt.

Gyakori példa egy [Adatok importálása][import-data] modul beállítása, így a telepített webszolgáltatás felhasználója másik adatforrást adhat meg a webszolgáltatás elérésekor. Vagy konfigurálása [Az adatok exportálása][export-data] modul, hogy egy másik cél lehet megadni.

Megadhatja a webszolgáltatás paramétereit, és társíthatja őket egy vagy több modulparaméterhez, és megadhatja, hogy kötelezőek-e vagy sem. A webszolgáltatás felhasználója értékeket ad ezekhez a paraméterekhez a szolgáltatás elérésekor, és a modulműveletek ennek megfelelően módosulnak.

A webszolgáltatás paramétereinek használatáról és használatáról az [Azure Machine Learning webszolgáltatás paramétereinek használata][webserviceparameters]című témakörben talál további információt.

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>A prediktív kísérlet telepítése webszolgáltatásként
Most, hogy a prediktív kísérlet megfelelően előkészített, telepítheti azt egy Azure-webszolgáltatás. A webszolgáltatás használatával a felhasználók adatokat küldhetnek a modellnek, és a modell visszaadja az előrejelzéseket.

A teljes telepítési folyamatról további információt az [Azure Machine Learning webszolgáltatás üzembe helyezése című][deploy] témakörben talál.

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
