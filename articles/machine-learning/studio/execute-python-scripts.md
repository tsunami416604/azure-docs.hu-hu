---
title: Python machine learning parancsfájlok végrehajtása |} Microsoft Docs
description: Vázol fel tervezési alapelvek az alapul szolgáló Azure Machine Learning és alapvető használati forgatókönyvek, képességekre és korlátozásokra Python parancsfájlok támogatása.
keywords: Python gépi tanulási, pandas, a python pandas, a python-parancsfájlok, python-parancsfájl végrehajtása
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 0efa2a7604b41b791ed3717b7f2b667b7181085d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>A Python Machine Learning parancsfájlok végrehajtása az Azure Machine Learning Studióban

Ez a témakör ismerteti a tervezési alapelvek az alapul szolgáló Python-parancsfájl az Azure Machine Learning aktuális támogatása. A fő képesség azt is, beleértve:

- a végrehajtás alapvető használati forgatókönyvek
- a kísérlet pontozása egy webszolgáltatás
- importálja a meglévő kód támogatása
- képi megjelenítések exportálása
- hajtsa végre a felügyelt szolgáltatás kiválasztása
- bizonyos korlátozások megértése

[Python](https://www.python.org/) a sok adatszakértőkön eszköz mellkasát elengedhetetlen eszköz. Rendelkezik:

* egy elegáns és tömör szintaxis 
* többplatformos támogatást, 
* hatékony szalagtárak hatalmas és 
* érett Fejlesztőeszközök. 

Python használatban van a machine learning modellezési jellemzően használt munkafolyamat minden egyes szakaszába:

- adatok betöltési és feldolgozása 
- szolgáltatás létrehozása
- modell betanítási 
- Modellellenőrzés
- központi telepítési modell

Azure Machine Learning Studio támogatja a beágyazási Python-parancsfájlok tanulási kísérlet és zökkenőmentesen is közzé őket a Microsoft Azure webszolgáltatásként gépek különböző részre.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Python gépi tanulási parancsfájlok tervezési alapelvei

Az elsődleges kapcsolódási felületet az Azure Machine Learning Studio Python keresztül van a [Python-parancsfájl végrehajtására] [ execute-python-script] modul 1. ábrán látható.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

1. ábra. A **Python-parancsfájl végrehajtására** modul.

A [Python-parancsfájl végrehajtására] [ execute-python-script] modul az Azure ML Studio legfeljebb három bemeneti adatokat fogad, és legfeljebb két kimenetek (a következő szakaszban tárgyalt), az R analóg például létrehozza a [R végrehajtása Parancsfájl] [ execute-r-script] modul. A Python kódot hajthatnak végre is meg kell adni a paraméter mezőbe, kifejezetten nevű belépési pont hívott függvény `azureml_main`. Az alábbiakban a tervezési alapelvek, ez a modul végrehajtásához használja:

1. *A Python felhasználók idiomatikus kell lennie.* A Python-felhasználók kódjukat funkciók modulok belül, figyelembe. Ezért helyezése egy legfelső szintű modul végrehajtható kimutatások számos olyan viszonylag ritkán fordul elő. A parancsfájl mezőben emiatt is egy speciális Python függvény figyelésekor utasításokat egy sorozatát vesz igénybe. Az objektumok, a függvény felfedett például olyan szabványos Python könyvtár típusok [Pandas](http://pandas.pydata.org/) adatkeretek és [NumPy](http://www.numpy.org/) tömbök.
2. *Valósághű helyi között kell lennie, és a felhő végrehajtások.* A háttérkiszolgálón hajthatók végre a Python-kód alapján [Anaconda](https://store.continuum.io/cshop/anaconda/), a platformok közötti tudományos Python elosztási széles körben használt. Közel 200 a leggyakrabban használt Python-csomagokat, a mellékelt azt. Ezért az adatelemzők debug és a helyi Azure Machine Learning-kompatibilis Anaconda-környezetre kódjukat minősítéséhez. Majd használjon például egy meglévő fejlesztőkörnyezet [IPython](http://ipython.org/) notebook vagy [a Python Tools for Visual Studio](http://aka.ms/ptvs), az Azure ML kísérlet részeként futtatható. A `azureml_main` belépési pont, a Python eredeti függvény, ezért x hozhat létre az Azure ML-tartozó kódot és az SDK telepítése nélkül.
3. *A többi Azure Machine Learning modulok zökkenőmentesen összeállítható kell lennie.* A [Python-parancsfájl végrehajtására] [ execute-python-script] modul fogad el, bemenetekhez és kimenetekhez, szabványos Azure Machine Learning adatkészletek. Az alapul szolgáló keretrendszer átlátható és hatékonyan kulcsösszetevő az Azure ML és Python futtatókörnyezetek. Ezért a Python meglévő Azure ML munkafolyamathoz, beleértve azokat is, hívja az R és SQLite együtt használható. Eredményeként adatok tudósok munkafolyamatok állítható össze, amelyek:
   * Python és Pandas használja az adatok előzetes feldolgozás és tisztítás
   * az adatok hírcsatornát, hogy több adatkészletet csatlakoztatása a szolgáltatások SQL átalakítás
   * az algoritmusok használata az Azure Machine Learning modellek betanítása 
   * értékelje ki és utófeldolgozási az eredményeket R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Python parancsfájlok ml alapvető használati forgatókönyvek

Ez a szakasz azt megtekintheti, néhány alapvető felhasználási területei a [Python-parancsfájl végrehajtására] [ execute-python-script] modul. A Python modul bemeneti mint Pandas adatkeretek érhetők el. A függvény kell visszaadnia egy Python belül csomagolt egyetlen Pandas adatok keret [feladatütemezési](https://docs.python.org/2/c-api/sequence.html) például egy rekord, lista vagy NumPy tömb. Az első elem az a sorozat majd eredmény abban az esetben a modul az első kimeneti portjára. Ez a rendszer a 2. ábrán látható.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

2. ábra Leképezése bemeneti portok paramétereket és visszatérési érték a kimeneti portra.

Részletesebb szemantikáját hogyan a bemeneti portok paramétereinek beolvasása leképezve a `azureml_main` függvény az 1 láthatók:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

1. táblázat. A bemeneti portok függvényparamétereket leképezését.

A bemeneti portok és függvényparamétereket között lesz pozicionális:

- Az első csatlakoztatott bemeneti portját a függvény első paramétere van leképezve. 
- A második bemenet (Ha csatlakozott) függvény második paramétere van leképezve.

Lásd: *adatelemzéshez Python* (O'Reilly, 2012) további információt a Python Pandas és hogyan használható adatok módosítására, hatékonyabb és gazdaságosabb Nyugat McKinney által. 


## <a name="translation-of-input-and-output-types"></a>Bemeneti és kimeneti típusú fordítás 
Azure ml bemeneti adatkészletek adatkeretek a Pandas alakulnak. Kimeneti adatkeretek vissza Azure ML adatkészletek alakulnak. A következő átalakítás esetén kell végrehajtani:

1. Konvertálja a karakterláncot, és a numerikus oszlopok-van és dataset a hiányzó értékeket Pandas "NA" értékek alakulnak. Az azonos átalakítás történik, az vissza módja (Pandas NA értékek a hiányzó értékeket Azure ML alakulnak).
2. Index vektorok Pandas az Azure ml nem támogatottak. A Python függvény összes bemeneti adatkeretek mindig tartozik egy 64 bites numerikus index 0 mínusz 1 sorok száma. 
3. Az Azure ML adatkészletek nem lehet ismétlődő oszlopneveket tartalmaz, az oszlopnevek, amelyek nem karakterlánc. Ha egy kimeneti adatok keret nem numerikus oszlopot tartalmaz, meghívja-e a keretrendszer `str` meg az oszlopok neveit. Hasonlóképpen bármely ismétlődő oszlopnevek automatikusan összekeveredése biztosítását, hogy a nevek egyediek. A utótagot (2) a második duplikált, és így tovább ad hozzá az első ismétlődő, (3).


## <a name="operationalizing-python-scripts"></a>Python parancsfájlok végrehajtott

Bármely [Python-parancsfájl végrehajtására] [ execute-python-script] pontozási kísérletben használt modulok webszolgáltatásként közzétételekor nevezzük. Például a 3. ábrán látható egy pontozási kísérletet, amely tartalmaz egy Python-kifejezés kiértékelése a kódot. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

3. ábra. Webszolgáltatás egy Python-kifejezés kiértékelésekor.

Ez a kísérlet létrehozása webszolgáltatás:

- a Python kifejezés (karakterláncként) bemenetként vesz igénybe
- elküldi a Python értelmező 
- egy kifejezés, mind az értékelés eredményét tartalmazó táblázatot ad vissza.


## <a name="importing-existing-python-script-modules"></a>Meglévő Python-parancsfájl modulok importálása

Egy gyakori használati eset a sok adatszakértőkön hoz meglévő Python parancsfájlok beépítse Azure ML kísérletek. Ahelyett, hogy az összes kód összefűzendő és illeszthetők be egy parancsfájl mezőbe a [Python-parancsfájl végrehajtására] [ execute-python-script] modul elfogadja a harmadik bemeneti portját a Python-modulok tartalmazó zip-fájl. A fájlt futásidőben a végrehajtási keretrendszer van unzipped, és a tartalom hozzáadódnak a Python értelmező könyvtár elérési útját. A `azureml_main` függvény importálhatja ezeket a modulokat közvetlenül a belépési pont.

Tegyük fel fontolja meg a fájlt egy egyszerű "Hello, World" függvényt tartalmazó Hello.py.

![image6](./media/execute-python-scripts/figure4.png)

4. ábra. Felhasználó által definiált függvény Hello.py fájlban.

A következő létrehozhatunk Hello.zip Hello.py tartalmazó fájlt:

![image7](./media/execute-python-scripts/figure5.png)

5. ábra. Felhasználó által definiált Python kódot tartalmazó zip-fájl.

Töltse fel a zip-fájl adatkészletként Azure Machine Learning Studio. Ezután létrehozhat és, hogy a Python kódot használja a Hello.zip fájl által a harmadik bemeneti porthoz való csatlakoztatás a kísérlet futtatásához a **Python-parancsfájl végrehajtására** modul, az ábrán látható módon.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

6. ábra. Felhasználó által definiált Python kóddal mintakísérletet feltöltött csomagot .zip fájlként.

A modul kimenete jeleníti meg, hogy a zip-fájl csomagolatlan megtörtént-e, és hogy a függvény `print_hello` futott.
 
![image10](./media/execute-python-scripts/figure7.png)

7. ábra. Felhasználó által definiált függvény belül használja a [Python-parancsfájl végrehajtására] [ execute-python-script] modul.


## <a name="working-with-visualizations"></a>Képi megjelenítés használata

Felvétel létrehozott MatplotLib, amely képes formájában jelenik meg a böngésző által adhatók vissza a [Python-parancsfájl végrehajtására][execute-python-script]. De a felvétel a rendszer nem automatikusan átirányítja képek, R. használatakor Ezért a felhasználó explicit módon menteni kell bármely előkészítésére PNG-fájlok Ha vissza az Azure Machine Learning kell állítani. 

Hozzon létre képeket MatplotLib, az alábbi eljárást kell végrehajtania:

* a háttérrendszer Váltás az alapértelmezett Qt-alapú leképező "AGG" 
* Hozzon létre egy új. ábra-objektumot 
* a tengely első és bele felvételt minden készítése 
* az ábra PNG fájlba mentése 

Ezt a folyamatot mutatja be az alábbi 8. ábra, amely Pandas található scatter_matrix funkcióval mátrix pont rajzot hoz létre.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

8. ábra. Kód MatplotLib adatok mentése lemezképbe.

9. ábra mutatja, amely a visszaadandó előzőleg bemutatott parancsfájlt használ a kísérlet tevékenységtérkép második kimenő porton keresztül.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

9. ábra. Python kódból generált előkészítésére megjelenítése.

Lehetőség több adatok vissza kijelzőként különböző képek, az Azure Machine Learning futásidejű szerzi be az összes képek és fűzi össze azokat a képi megjelenítéshez tartozó.


## <a name="advanced-examples"></a>Speciális példák

Az Azure Machine Learning telepített Anaconda-környezete például NumPy SciPy vagy Scikits további közös csomagot tartalmaz. Ezeket a csomagokat a machine learning-feldolgozási folyamat különböző adatfeldolgozási feladatok hatékony használható. Tegyük fel parancsfájl és a következő kísérlet használatát mutatják be a Scikits-Ismerkedjen meg az adatkészlet funkció fontos pontszámait számítási ensemble tanulókkal. Az eredmények felügyelt szolgáltatás kiválasztása előtt be egy másik ML-modell alatt táplált végrehajtásához használható.

A Python funkció a fontos pontokat és a szolgáltatások alapján a pontszámok kiszámítására használt itt található:

![image11](./media/execute-python-scripts/figure8.png)

10. ábra. Működik a sorrendet megadó szolgáltatások által pontszámait.
  A következő kísérlet majd kiszámítja, és az Azure Machine Learning "Pima indiai cukorbetegség" adatkészlet a szolgáltatások fontos eredményét adja vissza:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

11. ábra. Pima indiai cukorbetegség adatkészlet rank szolgáltatásainak kipróbálásához.

## <a name="limitations"></a>Korlátozások
A [Python-parancsfájl végrehajtására] [ execute-python-script] van a következő korlátozások vonatkoznak:

1. *Elkülönített végrehajtása.* A Python-futtatókörnyezet jelenleg védőfal mögött, és emiatt nem engedélyezi a hozzáférést a hálózati vagy a helyi fájlrendszer tartósan. Összes fájl mentése helyileg elkülönített és törölte a modul befejezését követően. A Python-kód nem érhető el a számítógépen az fut, az aktuális könyvtárban és annak alkönyvtáraiban kivétel legtöbb könyvtárak.
2. *Kifinomult fejlesztési és a hibaelhárítási támogatás hiánya.* A Python modul jelenleg nem támogatja IDE-szolgáltatásokkal, például az intellisense és hibakeresés folyamatát. Is ha a modul futásidejű nem sikerül, a Python teljes Veremkivonat érhető el. De a modul a kimeneti naplóban lehet megtekinteni. Jelenleg javasoljuk, hogy Ön által fejlesztett és egy környezetben, például IPython Python-parancsfájlok hibakeresése és a kód majd importálja a modult.
3. *Keret kimeneti egyetlen adatokat.* A Python belépési pont csak engedélyezett egyetlen keret kimeneteként adja vissza. Nincs jelenleg lehetőség tetszőleges Python objektumok például közvetlenül a betanított modellek térjen vissza az Azure Machine Learning futásidejű. Például [R-parancsfájl végrehajtása][execute-r-script], amelynek van ugyanez a helyzet, sok esetben egy bájttömbben az objektumok körte és térjen vissza, amely adatok kereten belül lehet.
4. *Nem tudja telepíteni a Python telepítés testreszabása*. Adja hozzá az egyéni Python-modulok csak úgy jelenleg a zip-fájl a korábban ismertetett mechanizmus révén. Ez nem valósítható meg, hogy kis modulok, akkor nagy modulok (különösen a natív DLL-ek) és a modulok nagy számú nehézkes. 

## <a name="conclusions"></a>Következtetéseket
A [Python-parancsfájl végrehajtására] [ execute-python-script] modul lehetővé teszi, hogy egy meglévő Python kódját beépíteni az Azure Machine Learning és a felhőben üzemeltetett gépi tanulás munkafolyamatok zökkenőmentesen adatok tudósok azok a webes szolgáltatás részeként. A Python-parancsfájl modul természetes együttműködik az Azure Machine Learning más modulok. A modul az adatok feltárása feladatok előzetes feldolgozás és a szolgáltatás kivonása, és ezután értékelési számos és az eredmények utófeldolgozás használható. A háttérrendszer futásidejű végrehajtási használt Anaconda, tesztelt és széles körben használt Python elosztási alapul. A háttérrendszer egyszerűen meg a helyi meglévő kódot eszközökhöz kiterjeszti a felhőbe.

Várhatóan új lehetőségeket biztosítanak a [Python-parancsfájl végrehajtására] [ execute-python-script] modul például betanítása, és azok modellek a Python, valamint nagyobb mértékű támogatást biztosítsanak a fejlesztési hozzáadása és az Azure Machine Learning Studióban kód hibakereséséhez.

## <a name="next-steps"></a>További lépések
További információ: [Python fejlesztői központban](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
