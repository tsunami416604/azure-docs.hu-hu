---
title: Python machine learning parancsfájlok végrehajtása
titleSuffix: Azure Machine Learning Studio
description: A körvonal tervezési elvek, az alapul szolgáló Python-szkriptek az Azure Machine Learning Studio és az alapvető használati forgatókönyvek, képességekre és korlátozásokra támogatása.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: ca1487bea969703fddffd664b899d4e2f47b4277
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824045"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>A Python Machine Learning parancsfájlok végrehajtása az Azure Machine Learning Studióban

Ez a témakör ismerteti a tervezési alapelvek az alapul szolgáló Azure Machine Learning Studio Python-szkriptek jelenlegi támogatása. A megadott fő funkciói is leírása, beleértve:

- hajtsa végre az alapvető használati forgatókönyvek
- egy kísérlet pontszám a web service-ben
- importálja a meglévő kód támogatása
- Vizualizációk exportálása
- hajtsa végre a felügyelt szolgáltatás kiválasztása
- bizonyos korlátozások ismertetése

[Python](https://www.python.org/) elengedhetetlen az eszköz mellkasát számos adatszakértők az eszköz. Rendelkezik:

* egy elegáns és tömör szintaxis 
* Többplatformos támogatás 
* egy hatalmas hatékony kódtárak és 
* megalapozott fejlesztési eszközök. 

Python-munkafolyamat a machine learning modellezési jellemzően használt összes fázisban történik:

- adatok betöltése és feldolgozása 
- a szolgáltatás fejlesztés
- Modell betanítása 
- modell érvényesítése
- üzembe helyezési modellek

Az Azure Machine Learning Studióban be egy machine learning-kísérlet, és zökkenőmentesen is közzé őket a Microsoft Azure-webszolgáltatásként különböző részeivel beágyazási Python-szkriptek támogatja.




## <a name="design-principles-of-python-scripts-in-machine-learning"></a>A Machine Learning Python-szkriptek tervezési alapelvek

Az Azure Machine Learning Studio Python elsődleges felületén keresztül van a [Python-szkript végrehajtására] [ execute-python-script] modul az 1. ábrán látható.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

1. ábra A **Python-szkript végrehajtására** modul.

A [Python-szkript végrehajtására] [ execute-python-script] modul az Azure Machine Learning Studióban legfeljebb három bemenetek fogad, és legfeljebb két kimenetek (a következő szakaszban részletezett), például az R analóg elkészíti a [R végrehajtása Parancsfájl] [ execute-r-script] modul. A Python-kód végrehajtását is meg kell adni a paraméter mezőbe, mint egy külön elnevezett belépési ponttal a hívott függvény `azureml_main`. Ez a modul megvalósításához használt fő tervezési elvek a következők:

1. *Python-felhasználók számára bármilyen kell lennie.* A felhasználók többsége Python kódját kéttényezős funkciókként modulok belül. Ezért üzembe egy legfelső szintű modulban végrehajtható utasítások sok viszonylag ritkán fordul elő. A parancsfájl mező, ezért is utasításokat egy sorozatát ellentétben kifejezetten elnevezett Python függvény vesz igénybe. Az objektumok között szerepelnek a függvényt is Python-kódtár alaptípusok például [Pandas](http://pandas.pydata.org/) adatkeretek és [NumPy](http://www.numpy.org/) tömböket.
2. *Rendelkeznie kell a helyi közötti élethű és végrehajtásainak felhőbeli.* A háttérrendszer a Python-kód végrehajtására szolgáló alapján [Anaconda](https://store.continuum.io/cshop/anaconda/), a széles körben használt többplatformos tudományos Python elosztási. Közel 200-as, a leggyakoribb Python-csomagok együtt származik. Az adatszakértők, ezért hibakeresést, és a kódját az Azure Machine Learning Studio-kompatibilis Anaconda helyi környezetben minősítéséhez. Majd használjon például egy már létező fejlesztési környezetben [IPython](http://ipython.org/) notebook vagy [Python Tools for Visual Studio](https://aka.ms/ptvs), egy Azure Machine Learning-kísérlet keretében futtatásához. A `azureml_main` belépési pontja pedig egy vanília Python-függvény, így x hozhatóak létre az Azure Machine Learning-specifikus kód vagy az SDK telepítése nélkül.
3. *Zökkenőmentesen algyűjteményeinek összefüggő egyéb Azure Machine Learning Studio-modulok kell lennie.* A [Python-szkript végrehajtására] [ execute-python-script] modul fogad, bemeneti és kimeneti, Standard szintű Azure Machine Learning Studio-adatkészletek. Az alapul szolgáló keretrendszere átlátható módon és hatékonyan áthidalja az Azure gépi tanulás és a Python modulok. Ezért a Python meglévő Azure ML munkafolyamatok, például azokkal, amelyek hívásokat indítani olyan R- és SQLite együtt használható. Ennek eredményeként adattudós munkafolyamatok állítsa össze, amelyek:
   * használható a Python és a Pandas adatok előzetesen feldolgozni és tisztítása
   * az adatok SQL átalakítást, több adatkészlet csatlakoztatása a szolgáltatások-hírcsatorna
   * az Azure Machine Learning Studióban az algoritmusok használatával modelleket taníthat be 
   * értékeli, és az eredmények r használatával az utófeldolgozási


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Python-szkriptek a Machine Learning alapvető használati forgatókönyvek

Ebben a szakaszban azt felmérést, néhány alapvető felhasználása a [Python-szkript végrehajtására] [ execute-python-script] modul. A Python modul bemeneti adatokat, Pandas adatkeretek érhetők el. A függvénynek egy csomagolt Python belül egyetlen Pandas adatkeretbe kell [feladatütemezési](https://docs.python.org/2/c-api/sequence.html) például egy rekord, lista vagy NumPy tömb. Ez a sorozat első elemeként majd az első kimeneti portjára, a modul adja vissza. Ez a rendszer a 2. ábrán látható.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

2. ábra Leképezése, bemeneti paraméterek portok, és kimeneti portját adja vissza értéket.

Részletesebb szemantikáját hogyan leképezve a bemeneti portok paramétereit a `azureml_main` függvény jelennek meg az 1. táblázat:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

1. táblázat. Függvény paraméterei a bemeneti portok hozzárendelését.

A leképezést a bemeneti portok és a függvény paraméterei között a következő Helyzetbeállító:

- Az első csatlakoztatott bemeneti portját a függvény első paramétere van leképezve. 
- A második bemenet (Ha a kapcsolódó) függvény második paraméterként van leképezve.

Lásd: *adatelemzés céljából Python* (O'Reilly, 2012) által Nyugat McKinney Python Pandas és hogyan használható adatok kezelésére és gazdaságosabb további információt. 


## <a name="translation-of-input-and-output-types"></a>Fordítási bemeneti és kimeneti típusai 
Az Azure ML bemeneti adatkészletek a Pandas adatkeretek konvertálja. Kimeneti adatkeretek vissza az Azure Machine Learning adatkészletek konvertálja. A következő átalakítás történik:

1. Konvertálja a karakterlánc és a numerikus oszlopok-rendszer és a egy adatkészlet a hiányzó értékeket "NA" értékeket a Pandas alakítja. Az azonos átalakítás történik von vissza (NA a Pandas lesznek átalakítva az Azure Machine Learning a hiányzó értékeket).
2. Index útvonalairól Pandas nem támogatottak az Azure gépi tanulás. A Python-függvény az összes bemeneti adatkeretek mindig van egy 64 bites numerikus indexet 0 mínusz 1 sorok számát. 
3. Az Azure Machine Learning-adatkészletek nem lehet ismétlődő oszlopneveket és oszlopneveket, amelyek nem karakterlánc. Ha egy kimeneti adatkeretbe nem numerikus oszlopokat tartalmaz, a keretrendszer meghívja `str` az oszlopneveket. Hasonlóképpen minden ismétlődő oszlopnevek automatikusan annak érdekében, hogy a nevek egyediek összekeveredett. A utótagot (2) ad hozzá az első ismétlődő, (3) a második ismétlődő, és így tovább.


## <a name="operationalizing-python-scripts"></a>Python-szkriptek modellezést

Bármely [Python-szkript végrehajtására] [ execute-python-script] webszolgáltatásként, amely közzétett modulok a pontozó kísérletben használt nevezzük. Ha például a 3. ábrán látható egy pontozó kísérletet, amely tartalmazza a kódot Python egyetlen kifejezés kiértékeléséhez. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

3. ábra Egy Python-kifejezés kiértékelése webszolgáltatást.

Ezzel a kísérlettel létrehozott webszolgáltatások:

- egy Python-kifejezés (karakterláncként) bemenetként vesz igénybe
- elküldi a Python-fordítóra 
- a kifejezés és az értékelés eredménye is tartalmazó táblát adja vissza.


## <a name="importing-existing-python-script-modules"></a>Meglévő Python-szkript modulok importálása

Egy gyakori használati számos adatszakértők számára az Azure Machine Learning-kísérletek beépítheti a meglévő Python-szkriptek. Nem minden kód összefűzött és beillessze egy egyetlen parancsfájllal mezőben kell megadni célhelyként a [Python-szkript végrehajtására] [ execute-python-script] modul fogad egy zip-fájlt, amely tartalmazza a Python-modulok, a harmadik bemeneti porthoz. A fájlt futásidőben a végrehajtási keretrendszer által a kicsomagolt, és a tartalmak kerülnek a Python-fordítóra könyvtár elérési útja. A `azureml_main` függvény importálhatja ezeket a modulokat közvetlenül belépési pontot.

Tegyük fel fontolja meg a fájlt egy egyszerű "Hello, World" függvény tartalmazó Hello.py.

![image6](./media/execute-python-scripts/figure4.png)

4. ábra Felhasználó által definiált függvény Hello.py fájlban.

Ezután létrehozunk egy fájlt, amely tartalmazza a Hello.py Hello.zip:

![image7](./media/execute-python-scripts/figure5.png)

5. ábra Felhasználó által definiált Python-kód tartalmazó zip-fájlt.

A zip-fájl feltöltése adatkészletként Azure Machine Learning studióba. Ezután hozzon létre és futtassa a kísérletet, amelyek a Python-kód használja a Hello.zip fájlban harmadik bemeneti portjával csatlakoztatásával a **Python-szkript végrehajtására** modult, ezen az ábrán látható módon.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

6. ábra A felhasználó által definiált Python-kód mintakísérlet feltöltött zip-fájlként.

A modul kimeneti jeleníti meg, hogy a zip-fájl becsomagolatlan lett-e, és hogy a függvény `print_hello` futtatása.
 
![image10](./media/execute-python-scripts/figure7.png)

7. ábra Használatban lévő, felhasználó által definiált függvény a [Python-szkript végrehajtására] [ execute-python-script] modul.


## <a name="working-with-visualizations"></a>Vizualizációk használata

A böngészőben a MatplotLib, amely az alábbi ábra segítségével létrehozott grafikon által visszaadható a [Python-szkript végrehajtására][execute-python-script]. De a grafikon rendszer nem irányítja át lemezképek úgy, mint amikor az r segítségével Így a felhasználó explicit módon menteni kell bármely grafikon PNG-fájlok, ha azok visszaadandó vissza az Azure Machine Learning Studióban. 

A rendszerképek készítése a MatplotLib, a következőképpen kell elvégeznie:

* a háttérrendszer Váltás az alapértelmezett Qt-alapú megjelenítő "AGG" 
* Hozzon létre egy új. ábra-objektumot 
* a tengely első és bele minden grafikon létrehozása 
* az ábra PNG-fájl mentése 

Ez a folyamat a következő 8. ábra, amely létrehoz egy pont diagram mátrix scatter_matrix funkció a Pandas használatával mutatja be.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

8. ábra Kód lemezképek MatplotLib adatok mentéséhez.

9. ábra mutatja, amely használja az előzőleg bemutatott való visszatéréshez parancsfájl kísérlet jeleníti meg a második kimeneti port használatával.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

9. ábra Python-kód által létrehozott grafikon megjelenítése.

Lehetőség több adatok visszaadandó menti őket a különböző képek, az Azure Machine Learning Studio modul összes lemezképet és egy naplóbejegyzésbe fűzi össze azokat a vizualizációt.


## <a name="advanced-examples"></a>Speciális példák

A telepített Azure Machine Learning Studio Anaconda környezet NumPy SciPy és Scikits – ismerje meg, például közös csomagot tartalmaz. Ezeket a csomagokat a machine learning-folyamat különféle adatfeldolgozási feladatok hatékonyan használható. Tegyük fel az alábbi kísérlet és parancsfájl használatát mutatják be ensemble tanulók Scikits további számítási szolgáltatás fontosság pontszámok egy adatkészlethez a. A pontszámok felügyelt szolgáltatás kiválasztása előtt folyamatban van egy másik gépi Tanulási modellt az adatkéréseket végrehajtásához használható.

Íme a Python-függvény fontosság pontszámokat és a szolgáltatásokat a pontszámok alapján kiszámításához használt:

![image11](./media/execute-python-scripts/figure8.png)

10. ábra Függvény által pontszámok rangsorolják funkciókat.
 
Az alábbi kísérlet majd kiszámítja, és adja vissza a szolgáltatások fontossági pontszámokat az Azure Machine Learning Studióban "Pima indiai küzdő" adatkészlet:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

11. ábra Kísérletezzen Pima indiai küzdő adatkészlet rangsorolják funkciókhoz is.

## <a name="limitations"></a>Korlátozások
A [Python-szkript végrehajtására] [ execute-python-script] jelenleg a következő korlátozások vonatkoznak:

1. *Elkülönített végrehajtását.* A Python-futtatókörnyezet jelenleg vendégünk, és ennek eredményeképpen nem engedélyezi a hozzáférést a hálózathoz, vagy a helyi fájlrendszerbe állandó módon. Minden fájl mentése helyileg elkülönített és törölni a modul befejeződése után. A Python-kód nem fér hozzá a legtöbb könyvtárak a gépen futtatott, az aktuális könyvtárban, illetve annak alkönyvtáraiba kivétel.
2. *Kifinomult fejlesztés és hibakeresés támogatás hiánya.* A Python modul jelenleg nem támogatja az intellisense és hibakeresés IDE funkciókat. Emellett ha a modul sikertelen futásidőben, a Python teljes híváslánc érhető el. De a modul kimeneti naplóban lehet megtekinteni. Jelenleg javasoljuk, hogy, fejlesztés és hibakeresés Python-szkriptek, például az IPython-környezetben és a kód Ezután importálja a modult.
3. *Keret kimeneti adatokat.* A Python belépési pont csak az ad vissza kimenetként egyetlen keret számára engedélyezett. Nincs jelenleg lehetővé tetszőleges Python objektumok, például közvetlenül a betanított modellek térjen vissza az Azure Machine Learning Studio modul. Például [R-szkript végrehajtása][execute-r-script], amely rendelkezik, és ugyanez a helyzet, lehetséges, egy bájttömböt objektumok pickle és visszatér, amely adatkeretek belül sok esetben.
4. *Nem lehet a Python-telepítés testreszabása*. Adja hozzá az egyéni Python-modulok csak úgy jelenleg a zip-fájl a korábban ismertetett mechanizmus révén. Bár ez megvalósítható az kis modulok, a nagy modulok (különösen azokat a natív DLL-ek) és a modulok nagy számú nehézkes. 

## <a name="conclusions"></a>Következtetések
A [Python-szkript végrehajtására] [ execute-python-script] modul lehetővé teszi, hogy a meglévő Python-kód zökkenőmentesen az Azure Machine Learning Studióban, és a felhőben futó gépi tanulási munkafolyamatok építsen be adatszakértő üzembe helyezheti őket egy webszolgáltatás részeként. A Python-szkript modul természetes módon együttműködik más modulok az Azure Machine Learning Studióban. A modul előzetes feldolgozása és kinyerése a szolgáltatás, és ezután értékelési adatfeltárás feladatok széles, és az eredmények utófeldolgozás használható. A háttérrendszer futásidejű végrehajtásához Anaconda, tesztelt és széles körben használt Python elosztási alapul. Ez a háttérmodul is egyszerűvé teszi az Ön számára fedélzeti meglévő kódot eszközök a felhőbe.

A további funkciókat várhatóan a [Python-szkript végrehajtására] [ execute-python-script] modul betanítását és modelleket, a Python és a fejlesztési jobb támogatása érdekében például és az Azure Machine Learning Studio kód hibakereséséhez.

## <a name="next-steps"></a>További lépések
További információ: [Python fejlesztői központban](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
