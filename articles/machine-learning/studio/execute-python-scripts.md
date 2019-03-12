---
title: Python machine learning parancsfájlok végrehajtása
titleSuffix: Azure Machine Learning Studio
description: Megtudhatja, hogyan használható a Python az Azure Machine Learning Studióban.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/05/2019
ms.openlocfilehash: f508d16330bad7044a69ccff2ddf84ece74e78a2
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729425"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>A Python Machine Learning parancsfájlok végrehajtása az Azure Machine Learning Studióban

Python egy olyan értékes eszköz a eszköz mellkasát számos az adatszakértők a. Minden szokásos machine learning munkafolyamatok többek között az adatfeltárás, a szolgáltatás kivonása, modell betanítása és érvényesítés és központi telepítési szakaszában használatban van.

Ez a cikk bemutatja, hogyan használhatja a Python-szkript végrehajtására modul az Azure Machine Learning Studio-kísérletek és webszolgáltatások a Python-kód használatára.

## <a name="using-the-execute-python-script-module"></a>A Python-szkript végrehajtására modul használatával

Az elsődleges Python Studio felületén keresztül történik a [Python-szkript végrehajtására] [ execute-python-script] modul. Legfeljebb három bemenetek fogadja és legfeljebb két kimeneteket, hasonló eredményez a [R-szkript végrehajtása] [ execute-r-script] modul. Python-kód is meg kell adni a paraméter mezőbe egy külön elnevezett-belépési ponttal függvénnyel nevű `azureml_main`.

![Python-szkriptet a modul végrehajtása](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Python-mintakód modul paraméter mezőbe](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Bemeneti paraméterek

A Python modul bemeneti adatok érhetők el, Pandas adatkerettípusokat jelölhet. A `azureml_main` függvény elfogad legfeljebb két választható Pandas adatkerettípusokat jelölhet meg paraméterként.

A leképezést a bemeneti portok és a függvény paraméterei között a következő Helyzetbeállító:

- Az első csatlakoztatott bemeneti portját a függvény első paramétere van leképezve.
- A második bemenet (Ha a kapcsolódó) függvény második paraméterként van leképezve.
- A harmadik bemeneti szolgál [további Python-modulok importálása](#import-modules).

Részletesebb szemantikáját hogyan leképezve a bemeneti portok paramétereit a `azureml_main` függvény alább láthatók.

![Bemeneti portot és az eredményül kapott Python-aláírás](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Kimeneti visszatérési értékek

A `azureml_main` függvény kell visszaadnia egy Python csomagolva egyetlen Pandas DataFrame [feladatütemezési](https://docs.python.org/2/c-api/sequence.html) például egy rekord, lista vagy NumPy tömb. Ez a sorozat első elemeként küld vissza az első kimeneti portjára, a modul. A második kimeneti portjára, a modul használatos [Vizualizációk](#visualizations) , és nem szükséges egy visszatérési értéket. Ez a séma alább látható.

![Leképezési bemeneti paraméterek használt portok és kimeneti portját adja vissza értéket](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Fordítási bemeneti és kimeneti adatok típusa

Studio-adatkészletek nem azonosak, Pandas adatkerettípusokat jelölhet. Ennek eredményeképpen bemeneti adatkészletek a Studióban Pandas DataFrame alakítja, és DataFrames kimeneti vissza Studio adatkészletek alakítani. A konvertálási folyamat során a következő fordításokat is kell végrehajtani:

 **Python-adattípus** | **Studio fordítási eljárás** |
| --- | --- |
| Karakterláncok és numerics| Mivel a lefordított |
| Pandas 'NA' | A lefordított "Hiányzó értéke" |
| Index vektorok | Nem támogatott * |
| A nem karakterlánc oszlopnevek | Hívás `str` az oszlopnevek |
| Ismétlődő oszlopneveket tartalmaz | Numerikus utótag hozzáadása: (1), (2), (3), és így tovább.
**A Python-függvény az összes bemeneti adatkeretek mindig van egy 64 bites numerikus indexet 0 mínusz 1 sorok száma*

## <a id="import-modules"></a>Meglévő Python-szkript modulok importálása

A Python végrehajtásához használt háttérrendszer alapján [Anaconda](https://store.continuum.io/cshop/anaconda/), a tudományos Python elosztási széles körben használt. Közel 200-as, a leggyakoribb Python-csomagokat a adatközpontú számítási használt együtt származik. Azonban előfordulhat, további kódtárak pedig be kell.

Egy gyakori alkalmazási helyzet, hogy Studio kísérletek beépítheti a meglévő Python-szkriptek. A [Python-szkript végrehajtására] [ execute-python-script] modul fogadja el a harmadik bemeneti portját a Python-modulok tartalmazó zip-fájlt. A fájlt futásidőben a végrehajtási keretrendszer által a kicsomagolt, és a tartalmak kerülnek a Python-fordítóra könyvtár elérési útja. A `azureml_main` függvény importálhatja ezeket a modulokat közvetlenül belépési pontot.

Tegyük fel fontolja meg a fájlt egy egyszerű "Hello, World" függvény tartalmazó Hello.py.

![Felhasználó által definiált függvény Hello.py fájlban](./media/execute-python-scripts/figure4.png)

Ezután létrehozunk egy fájlt, amely tartalmazza a Hello.py Hello.zip:

![Felhasználó által definiált Python-kód tartalmazó zip-fájl](./media/execute-python-scripts/figure5.png)

A zip-fájl feltöltése adatkészletként studióba. Ezután hozzon létre és futtassa a kísérletet, amelyek a Python-kód használja a Hello.zip fájlban harmadik bemeneti portjával csatlakoztatásával a **Python-szkript végrehajtására** modul az alábbi képen látható módon.

![Egy Python-szkript végrehajtására modul bemeneteként Hello.zip a mintakísérlet](./media/execute-python-scripts/figure6a.png)

![Felhasználó által definiált Python-kód feltöltött zip-fájlként](./media/execute-python-scripts/figure6b.png)

A modul kimeneti jeleníti meg, hogy a zip-fájl becsomagolatlan lett-e, és hogy a függvény `print_hello` futtatása.

![Felhasználó által definiált függvény megjelenítő modul kimenete](./media/execute-python-scripts/figure7.png)

## <a name="operationalizing-python-scripts"></a>Python-szkriptek modellezést

Bármely [Python-szkript végrehajtására] [ execute-python-script] webszolgáltatásként, amely közzétett modulok a pontozó kísérletben használt nevezzük. Ha például az alábbi képen láthatók egy pontozó kísérletet, amely tartalmazza a kódot Python egyetlen kifejezés kiértékeléséhez.

![Studio-munkaterülethez a webszolgáltatáshoz](./media/execute-python-scripts/figure3a.png)

![Python Pandas expression](./media/execute-python-scripts/python-script-with-python-pandas.png)

Ezzel a kísérlettel létrehozott webszolgáltatások igénybe az alábbi műveleteket:

1. Egy Python-kifejezést is a bemeneti (karakterláncként)
1. A Python-kifejezést a Python-fordítóra küldése
1. a kifejezés és az értékelés eredménye is tartalmazó táblát adja vissza.

## <a id="visualizations"></a>Vizualizációk használata

MatplotLib használatával létrehozott grafikon által visszaadható a [Python-szkript végrehajtására][execute-python-script]. Azonban grafikon nem automatikusan átirányítja az lemezképek úgy, mint amikor az r segítségével Így a felhasználónak explicit módon menteni kell bármely grafikon, PNG-fájlok.

Rendszerképek létrehozásához a MatplotLib, a következő lépések szükségesek:

1. Az alapértelmezett Qt-alapú megjelenítő "AGG" válthat a háttérrendszerhez.
1. Hozzon létre egy új. ábra-objektumot.
1. A tengely első, és hozzon létre minden grafikon bele.
1. Mentse az ábrán egy PNG-fájl.

Ezt a folyamatot mutatja be az alábbi lemezképek, a pontdiagram diagram mátrix scatter_matrix funkció a Pandas használatával hoz létre.

![Kód lemezképek MatplotLib adatok mentése](./media/execute-python-scripts/figure-v1-8.png)

![Kattintson egy Python-szkript végrehajtására modul a adatait a megjelenítése](./media/execute-python-scripts/figure-v2-9a.png)

![Egy Python-kód használatával mintakísérlet a grafikon megjelenítése](./media/execute-python-scripts/figure-v2-9b.png)

Több adatok visszaadandó menti őket a különböző képek lehetőség. A Studio modul az összes rendszerkép és egy naplóbejegyzésbe fűzi össze azokat a vizualizációt.

## <a name="advanced-examples"></a>Speciális példák

A Studio telepített Anaconda-környezet NumPy SciPy és Scikits – ismerje meg, például közös csomagot tartalmaz. Ezeket a csomagokat a machine learning-folyamat az adatfeldolgozás hatékonyan használható.

Például az alábbi kísérlet és parancsfájl ensemble tanulók Scikits további számítási szolgáltatás fontosság pontszámok egy adatkészlethez a használatát mutatják be. A pontszámok felügyelt szolgáltatás kiválasztása előtt folyamatban van egy másik modellbe adatkéréseket végrehajtásához használható.

Íme a Python-függvény fontosság pontszámokat és a szolgáltatásokat a pontszámok alapján kiszámításához használt:

![A pontszámok rangsorolják funkciók függvény](./media/execute-python-scripts/figure8.png)

Az alábbi kísérlet majd kiszámítja, és adja vissza a szolgáltatások fontossági pontszámokat az Azure Machine Learning Studióban "Pima indiai küzdő" adatkészlet:

![Kísérletezzen a Python használatával Pima indiai küzdő adatkészlet rangsorolják szolgáltatásaihoz](./media/execute-python-scripts/figure9a.png)

![Képi megjelenítés, a Python-szkript végrehajtására modul kimenete](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Korlátozások

A [Python-szkript végrehajtására] [ execute-python-script] modul jelenleg a következő korlátozások vonatkoznak:

### <a name="sandboxed-execution"></a>Elkülönített végrehajtása

A Python-futtatókörnyezet jelenleg vendégünk, és nem engedélyezi a hozzáférést a hálózaton vagy a helyi fájlrendszerben állandó módon. Minden fájl mentése helyileg elkülönített és törölni a modul befejeződése után. A Python-kód nem fér hozzá a legtöbb könyvtárak a gépen futtatott, az aktuális könyvtárban, illetve annak alkönyvtáraiba kivétel.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>A kifinomult fejlesztési és a hibakeresés támogatása

A Python modul jelenleg nem támogatja az intellisense és hibakeresés IDE funkciókat. Emellett ha a modul sikertelen futásidőben, a Python teljes híváslánc érhető el. De a modul kimeneti naplóban lehet megtekinteni. Jelenleg javasoljuk, hogy, fejlesztés és hibakeresés Python-szkriptek, például az IPython-környezetben és a kód Ezután importálja a modult.

### <a name="single-data-frame-output"></a>Keret kimeneti egyetlen adatok

A Python belépési pont csak az ad vissza kimenetként egyetlen keret számára engedélyezett. Nincs jelenleg lehetővé tetszőleges Python objektumok, például közvetlenül a betanított modellek térjen vissza a Studio modul. Például [R-szkript végrehajtása][execute-r-script], amely rendelkezik, és ugyanez a helyzet, lehetséges, egy bájttömböt objektumok pickle és visszatér, amely adatkeretek belül sok esetben.

### <a name="inability-to-customize-python-installation"></a>Nem lehet a Python-telepítés testreszabása

Adja hozzá az egyéni Python-modulok csak úgy jelenleg a zip-fájl a korábban ismertetett mechanizmus révén. Bár ez megvalósítható az kis modulok, nagy modulok (különösen a natív DLL-modulok) vagy a modulok nagy számú nehézkes.

## <a name="next-steps"></a>További lépések

További információ: [Python fejlesztői központban](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script