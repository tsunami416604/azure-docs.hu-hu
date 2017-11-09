---
title: "Rendszerkép használatával az Azure Machine Learning-munkaterület belül CNTK besorolás |} Microsoft Docs"
description: "A vonat, kiértékeléséhez és központi telepítése egy egyéni lemezkép besorolási modell Azure ML munkaterület használatával."
services: machine-learning
documentationcenter: 
author: PatrickBue
ms.author: pabuehle
ms.reviewer: mawah, marhamil, mldocs
ms.service: machine-learning
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: eefede6196bedf208d9b14cee63632922223a6d6
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Kép besorolás használata az Azure Machine Learning-munkaterület

Kép besorolás megközelítések számos számítógép stratégiai problémák megoldásához használható.
Ezek közé tartoznak, létrehozási modelleket, mint a kérdések megválaszolása: *a lemezképben található objektum?* ahol objektum lehet például *kutya*, *car*, vagy  *küldje el*. Vagy összetettebb kérdéseire: *szem elleni súlyosságú mely osztály a türelmet retinal vizsgálat során van érintő?*.

Az oktatóanyag címek ilyen problémák megoldásához. Azt mutatják be, értékelje ki, de a saját kép besorolási modell használatával telepítheti a [Microsoft kognitív Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) mély biztonságával.
Példa lemezképek találhatók, de az olvasó is kapcsolja a saját adatkészlet és a saját egyéni modell betanításához.

Számítógép stratégiai megoldások hagyományosan szükséges manuális azonosítása és valósítja meg az úgynevezett szakértői Tudásbázis *szolgáltatások*, amely kiemelheti kívánt képek.
Ezt a kézi módszert és a famous 2012 verzióban módosult [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] mély papír, és a jelenlegi, mély Neurális hálózatokat (DNN) használják ezeket a szolgáltatásokat automatikusan kereséséhez.
A mező nem csak a kép besorolás, hanem más számítógép stratégiai problémák, például objektum felderítését és a lemezkép hasonlóság hatalmas javulása DNNs vezetett.


## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag három részre oszlik:

- 1. rész bemutatja, hogyan betanítása, kiértékeléséhez és előre képzett DNN featurizer használatával, és egy SVM kimenetét a képzési kép besorolási rendszer központi telepítése.
- 2. rész majd bemutatja, hogyan által, pontosságának javítása érdekében például a DNN finomítása, nem pedig egy rögzített featurizer használni.
- 3. rész bemutatja, hogyan kell használni a saját dataset helyett a például képeket, és ha szükséges, hogyan lekaparást által a saját adatkészlet létrehozásához a háló kép.

Előzetes tapasztalata az gépi tanulási és CNTK nincs szükség, de napjainkban segít annak megértésében, alapelvek. Pontosság számok, idő stb. az oktatóanyag jelentett betanítása csak referenciaként szolgálnak, és a tényleges értékek, a kód futtatásakor szinte biztosan térnek el egymástól.


## <a name="prerequisites"></a>Előfeltételek

Ez a példa futtatásához az Előfeltételek a következők:

1. Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
2. A [Azure Machine Learning-munkaterület](./overview-what-is-azure-ml.md) következő a [– első lépések a telepítési útmutató](./quickstart-installation.md) telepíteni a programot, és hozzon létre egy munkaterület.  
3. A Windows-számítógép. Windows operációs rendszer szükség, mivel a munkaterületet üzemeltető támogatja az csak a Windows és a MacOS, miközben a Microsoft kognitív eszközkészlet (amelyek mély tanulási tárként használatára) csak támogatja a Windows és Linux.
4. Egy dedikált GPU nincs szükség a SVM képzési végrehajtása részében 1, azonban szükség van a 2. rész ismertetett DNN finomítása. Ha nem rendelkezik egy erős GPU, szeretné, hogy a több Feldolgozóegységekkel betanítása, vagy egy Windows-számítógép nem rendelkezik, fontolja meg Azure mély tanulási virtuális gépek használata a Windows operációs rendszerrel. Lásd: [Itt](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) egy 1 kattintással telepítési útmutató. Amennyiben telepített, csatlakoztassa a virtuális Gépet egy távoli asztali kapcsolaton keresztül, munkaterület nincs telepítése és a virtuális gépről helyi hajtható végre a kódot.
5. Például OpenCV különböző Python függvénytárak telepítve kell lennie. Kattintson a *nyissa meg a parancssort* a a *fájl* menü a munkaterületet üzemeltető futtatja a következő parancsokat a függőségek telepítése:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.0-cp35-cp35m-win_amd64.whl`után a OpenCV kerék letöltésére http://www.lfd.uci.edu/~gohlke/pythonlibs/ (a pontos fájl nevét és verzióját módosítható)
    - `conda install matplotlib numpy pillow`
    - `conda install -c conda-forge bqplot`

### <a name="troubleshooting--known-bugs"></a>Hibaelhárítási / ismert hibák
- 2. rész GPU szükséges, és ellenkező esetben a hiba "Kötegelt normalizálási képzési CPU még nincs megvalósítva" azért történik, pontosítsa a DNN tett kísérlet során.
- Kevés a memória hibák DNN betanítás során elkerülhetők minibatch méretének csökkentésével (változó `cntk_mb_size` a `PARAMETERS.py`).
- A kód teszteltük CNTK 2.0 és a 2.1-es verzióját, és kell újabb verzióin anélkül is futtassa (vagy csak kisebb) módosításait.
- A írásának időpontjában az Azure Machine Learning-munkaterület problémákba megjelenítő notebookok nagyobb, mint 5 MB-ban. Ez nagy méretű jegyzetfüzetek akkor fordulhat elő, ha a notebook menti az összes cella kimenet jelenik meg. Ha ez a hiba, majd nyissa meg a parancssort a Fájl menüből a munkaterületet üzemeltető belül, a végrehajtást `jupyter notebook`, nyissa meg a notebook, törölje az összes kimeneti és mentése a notebook. A lépések elvégzése után a notebook nyílik meg megfelelően az Azure Machine Learning-munkaterület belül újra.


## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Ebben a példában egy sablon használatával új projekt létrehozásához:
1.  Nyissa meg az Azure Machine Learning-munkaterület.
2.  Az a **projektek** lapján kattintson a  **+**  aláírásához, és válassza ki **új projekt**.
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt.
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Besorolást kép", és válassza ki a sablont.
5.  Kattintson a **Create** (Létrehozás) gombra.

Ezeket a lépéseket hajtja végre a lent látható módon szerkezetének hoz létre. A projekt könyvtárában korlátozva lehet legalább 25 MB-ban, mivel az Azure Machine Learning-munkaterület másolatot hoz létre a mappa minden egyes futtatásához (futtatási előzmények ahhoz) után. Emiatt összes lemezképet és az ideiglenes fájlokat menti a könyvtár érkező vagy oda irányuló *~/Desktop/imgClassificationUsingCntk_data* (néven *DATA_DIR* ebben a dokumentumban).

  Mappa| Leírás
  ---|---
  aml_config /|                           Az Azure Machine Learning-munkaterület konfigurációs fájlokat tartalmazó könyvtárat
  szalagtárak /|                              Minden Python és a Jupyter súgófunkciókat tartalmazó könyvtár
  notebookok /|                              Az összes jegyzetfüzet tartalmazó könyvtár
  erőforrások /|                              Minden erőforrás (a példa url módon képek) tartalmazó könyvtár
  parancsfájlok /|                              Az összes parancsfájl tartalmazó könyvtár
  PARAMETERS.py|                       Python-parancsfájl az összes paramétereinek megadása
  Readme.md|                           Ez fontos fájl


## <a name="data-description"></a>Adatok leírása

Ez az oktatóanyag használja, mint például egy felső törzs ruházati mintázat adatkészlet legfeljebb 428 képek álló futtató. Egyes lemezképek három különböző textúrák (pontozott, csíkozott, leopard) egyik leképezésként van feliratozva. Azt őrizni csomópontképek száma kisebb ez az oktatóanyag gyorsan hajtható végre. Azonban a kód tesztelt és lemezképek vagy több tízezreit működik. Összes lemezkép volt lekaparták a Bing-lemezkép keresési és aktuális-jegyzetelve leírt alapján [3. rész](#using-a-custom-dataset). A kép URL-címet a megfelelő attribútumaik jelennek meg a */resources/fashionTextureUrls.tsv* fájlt.

A parancsfájl `0_downloadData.py` összes lemezképek letölti a *DATA_DIR/képek/fashionTexture/* könyvtár. A 428 URL-címek valószínűleg sérült. Darabolása nem okoz problémát, és csak azt jelenti, hogy azt a modell betanítására és tesztelésére valamivel kisebb képek.

Az alábbi ábrán látható példát az attribútumok pontozott (bal oldali két), csíkozott (középső két oszlop) és leopard (jobb oldali két oszlop). Jegyzetek szerint a felső törzs ruházati elem volt történik.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_dotted.jpg"  alt="alt text" height="200">
<img src="media/scenario-image-classification-using-cntk/examples_striped.jpg" alt="alt text" height="200">
<img src="media/scenario-image-classification-using-cntk/examples_leopard.jpg" alt="alt text" height="200">
</p>


## <a name="part-1---model-training-and-evaluation"></a>1. rész – a modell betanítása és kiértékelése

Ez az oktatóanyag első részét, az azt van egy rendszer, amely használja, de nem módosíthatják, egy előre képzett mély Neurális hálózat betanítása. Ez előre képzett DNN egy featurizer szolgál, és egy lineáris SVM be van tanítva, az attribútum előrejelzése céljából (pontozott, csíkozott vagy leopard) a megadott lemezkép.

A Microsoft most ezt a megközelítést részletes, lépésenkénti, és a parancsfájlok futtatását kell megjelenítése ismerteti. Azt javasoljuk minden lépés után, mely fájlok vannak megírva, és írás ahol vizsgálata.

Az összes fontos paraméter meg van adva, és egy rövid magyarázatot egy helyen találhatók: a `PARAMETERS.py` fájlt.




### <a name="step-1-data-preparation"></a>1. lépés: Adatok előkészítése
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

A notebook `showImages.ipynb` jelenítheti meg a lemezképeket, és javítsa ki a jegyzet, igény szerint használható. A notebook futtatásához nyissa meg azt az Azure Machine Learning-munkaterület, és kattintson a "Start Notebook kiszolgáló", ha ez a beállítás jelenik, majd végre a notebook valamennyi cellájában. Ha panaszos, hogy túl nagy a megjelenítéshez-e a notebook hibaüzenetet kap, tekintse meg a hibaelhárítási rész ebben a dokumentumban.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Most végrehajtani a parancsprogramot, nevű `1_prepareData.py`, mely rendel összes lemezkép vagy a képzést, vagy a vizsgálatot. Ehhez a hozzárendeléshez egymást kölcsönösen kizáró – nem képzési kép is használatos, tesztelési, vagy fordítva. Alapértelmezés szerint egy véletlenszerű 75 %-a lemezképeket minden attribútum osztályból származik a képzés vannak rendelve, és a maradék 25 % rendelt tesztelése. A parancsfájl által létrehozott összes adatok lesznek mentve a *DATA_DIR/proc/fashionTexture/* mappát.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>2. lépés: A mély Neurális hálózat szűkebbé tenni.
`Script: 2_refineDNN.py`

Ahogy azt az oktatóanyag 1 részében, az előre képzett DNN rögzített tartják (Ez azt jelenti, hogy nem történik). Azonban a parancsfájl nevű `2_refineDNN.py` azt előre képzett betöltése még részben 1, végrehajtása [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) [2] modell és módosítja azt, például, hogy magasabb bemeneti képfelbontás lehetővé. Ez a lépés gyors (másodpercben), és nem szükséges egy grafikus processzort tartalmaz.

Az oktatóanyag 2 részén a PARAMETERS.py módosítása okok fájlt a `2_refineDNN.py` is pontosítsa a előre képzett DNN parancsfájl. Alapértelmezés szerint Futtatás finomítása során 45 képzési alapidőszakkal.

Mindkét esetben a végső modell majd írni a fájl *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>3. lépés: Kiértékelendő DNN összes lemezkép
`Script: 3_runDNN.py`

A Microsoft most már használhatja az (esetleg kifinomultabb) DNN az utolsó lépésben való featurize a képek. Megadott lemezkép számára a DNN, a kimenete az 512-úszó vektort az utolsó előtti réteg modell. Ezt a támadási sokkal kisebb dimenzionális, mint a lemezképet. Ettől függetlenül az tartalmazza az (és még jelöljön ki) vonatkozó ismeri fel a lemezkép operációs rendszerének attribútummal, akkor, ha a ruházati cikk egy pontozott csíkozott, a lemezkép vagy a leopard mintázat szereplő információk.

Az összes DNN kép megjelenítésének menti, és a fájl *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>4. lépés: Támogatás vektoros gép képzési
`Script: 4_trainSVM.py`

512-úszó az előző lépésben számított felelősséget használnak egy SVM osztályozó betanítása: a SVM kimenete lemezkép bemenetként megadott, minden elhelyezkednie attribútum pontszámot. Az példa adathalmaz Ez azt jelenti, hogy pontszámot "csíkozott", "pontozott", és a "leopard".

Parancsfájl `4_trainSVM.py` betölti a képzés lemezképek, a exportügyletek (slackhez) paraméter C különböző értékeket egy SVM betanítja és tartja a SVM legmagasabb pontossággal. A besorolás pontossága a konzol nyomtatva, és a munkaterületet üzemeltető ábrázolja. A megadott mintázat adatok ezeket az értékeket kell körülbelül 100 %-os és 88 % kulcsattribútumokkal. Végezetül a betanított SVM a fájl beíródik *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>5. lépés: Értékelési és -megjelenítésre
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

A parancsfájlt a betanított kép osztályozó pontosságát mérhető `5_evaluate.py`. A parancsfájl pontszámok a betanított SVM osztályozó lemezképet az összes teszt rendel az egyes kép az attribútum a legmagasabb pontszám, és összehasonlítja az előre jelzett attribútumok az alapoktól igazság megjegyzések.

Parancsfájl `5_evaluate.py` alább láthatók. Minden egyes osztály a besorolás pontossága számított, valamint a teljes vizsgálat beállítás ("általános pontosság"), és az átlagos pontosságának keresztül az egyes pontosság ("általános osztály átlagosan pontosság"). 100 %-os felel meg a lehető legpontosabb és a legrosszabb 0 %. Véletlenszerű találgatás akkor átlagosan az eredmény 1 osztály átlagosan pontosságát keresztül attribútumainak: Ebben az esetben a pontosság 33,33 % lenne. Ezekkel az eredményekkel jelentős javítására, mint a magasabb bemeneti felbontása használatakor `rf_inputResoluton = 1000`, azonban csökkenti a hosszabb DNN kiszámításához szükséges idő.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Mellett pontosságának a: ROC-görbe ábrázolja a megfelelő terület-a-görbe (bal oldali); és a félreértések mátrix megjelenik-e (jobb oldali):

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Végezetül a notebook `showResults.py` Görgessen végig a teszt képek és jelenítheti meg a megfelelő besorolási pontszámok biztosítja:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>6. lépés: központi telepítés
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

A betanított rendszer mostantól lehet közzé azt egy REST API-t. Központi telepítés esetén, tekintse meg a notebook a `deploy.ipynb`, és az Azure Machine Learning-munkaterület belül funkciókon alapulnak. További információ a kiváló telepítési szakasza a [IRIS oktatóanyag](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-3).

Amennyiben telepített, a webes szolgáltatás hívása a parancsfájllal `6_callWebservice.py`. Vegye figyelembe, hogy a webszolgáltatás IP-címe (helyi vagy a felhőbe) kell először be kell állítani a parancsfájlban. A notebook `deploy.ipynb` ismerteti az IP-cím kereséséhez.








## <a name="part-2---accuracy-improvements"></a>2. rész - pontossága fejlesztései

Az 1. rész azt bemutatta, hogyan lemezkép által egy lineáris támogatási vektoros gépet 512-úszó kimenetére mély Neurális hálózat betanítása besorolása. A DNN előre képzett a képek több millió volt, és az utolsó előtti réteg adja vissza a szolgáltatás vektort. Ez a megközelítés is gyors, mivel a DNN használatos-, de mégis gyakran a helyes eredményt ad.

A Microsoft most jelent-e többféleképpen 1. rész a modell pontosságának javítása érdekében. Ilyen például a Microsoft pontosítsa lekapcsolva tartja rögzített helyett a DNN.

### <a name="dnn-refinement"></a>DNN pontosítás

Egy SVM helyett egy teheti meg a besorolási közvetlenül a Neurális hálózat. Ez egy új utolsó réteg hozzáadásával a előre képzett DNN, ami a 512 úszó bemenetként utolsó előtti rétegből érhető el. Ez a besorolás jelölőnégyzetét a DNN előnye, hogy most a teljes hálózati backpropagation is meg lehet retrained. Ezt a módszert gyakran sokkal hatékonyabb besorolás pontosság használatával, előre képzett DNN vezet-azonban csökkenti a sokkal hosszabb képzési időt (akár a GPU-t).

A változó módosítása helyett egy SVM Neurális hálózat betanítása történik `classifier` a `PARAMETERS.py` a `svm` való `dnn`. Majd lásd 1. lépés:, (1. lépés) adatok előkészítése és SVM képzési (3. lépés) kivételével az összes parancsfájl kell újra végre kell hajtania. DNN pontosítás GPU igényel. Ha nincs GPU található, vagy ha a GPU zárolva van (például egy előző CNTK Futtatás követ) majd parancsfájl `2_refineDNN.py` hibát jelez. DNN képzési néhány Feldolgozóegységekkel, amely minibatch méretének csökkentésével elkerülhetők a is throw kevés a memória hiba (változó `cntk_mb_size` a `PARAMETERS.py`).

Képzési befejeztét követően a rendszer menti a kifinomultabb modell *DATA_DIR/proc/fashionTexture/cntk_refined.model*, és rajzot rajzolni, amely bemutatja, hogyan a tanítási és tesztelési besorolás hibák betanítás során módosítani. Vegye figyelembe, hogy adott rajzot, amely a hiba a gyakorlókészlethez sokkal kisebb, mint a vizsgálat beállítása. Az úgynevezett túlzott záró viselkedés csökkenteni lehet, például egy magasabb érték használatával jelkiesés sebessége `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Amint az alábbi ábra is látható, a megadott adatkészlet DNN pontosítás használatával pontosságának 92.35 % és a 88.92 % előtt (1. rész). Különösen a "pontozott" képek növelésére jelentősen, egy ROC terület-a-görbéjének 0,98 a pontosítás vs. 0.94 előtt. Kisebb adatkészlet használjuk, és ezért nem egyezik a tényleges pontosság az kód futtatásával. Ez az eltérés stochastic hatások, például a véletlenszerű felosztás a képek a modell betanítására és tesztelésére beállítása okozza.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Az előzmények követésének futtatása

Az előzmények az egyes futtatása összehasonlítás két vagy több fut, amelyek az Azure-on Azure Machine Learning-munkaterület tárolókat is hét egymástól. Ennek a magyarázatát részletesen a [Iris oktatóanyag](https://docs.microsoft.com/azure/machine-learning/preview/tutorial-classifying-iris-part-2). Is szemlélteti, ahol azt összehasonlítja két frissítési kísérletei során a parancsfájl alábbi képernyőfelvételnek `5_evaluate.py`, vagy DNN pontosítás használatával Ez azt jelenti, hogy `classifier = "dnn"`(futtatási szám 148) vagy SVM betanítása Ez azt jelenti, hogy `classifier = "svm"` (futtatási szám 150).

Az első a képernyőfelvételen látható DNN pontosítás SVM képzési minden osztály-nál nagyobb pontosság vezet. A második képernyőfelvételen látható összes metrikát követett, beleértve a osztályozó volt. A nyomkövetési végezheti el a parancsfájl `5_evaluate.py` az Azure Machine Learning-munkaterület naplózó meghívásával. Emellett a parancsfájl ROC görbe és zavart mátrix is menti a *kimenete* mappát. Ez *kimenete* mappa különleges abban, hogy a benne lévő tartalom is követi nyomon a munkaterületet üzemeltető előzmények funkció, és ezért a kimeneti fájlok érhető el tetszőleges időpontban, függetlenül attól, hogy helyi másolatot felül lett írva.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>  
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>A paraméter hangolása
Mivel a legtöbb gépi tanulási a projektek esetében igaz értékű, gondosan meg kell paramétert, hangolása, valamint a különböző tervezési döntésekhez kiértékelése jó eredmények beolvasása Új adatkészlet igényel. A feladatok érdekében minden fontos paraméter meg van adva, és egy rövid magyarázatot egy helyen találhatók: a `PARAMETERS.py` fájlt.

A legtöbb ígéret rendszere javításai vannak:

- Az adatminőségi: biztosítja a tanítási és tesztelési kiváló minőségű. Ez azt jelenti, hogy a képek megjegyzésekkel ellátott helytelenül, nem egyértelmű lemezképek eltávolítása (például ruházati elemek szétterítése és pontok), és az attribútumok kölcsönösen kizárják egymást (Ez azt jelenti, hogy akkor megválasztani, hogy pontosan egy attribútummal kellene tartozik minden kép).
- Ha az objektum--fontos kicsi a kép majd kép besorolás módszerekkel nem ismert működőképesek. Ilyen esetben fontolja meg az objektum észlelési módszer használatát, ez a [oktatóanyag](https://github.com/Azure/ObjectDetectionUsingCntk).
- DNN pontosítás: megszerezni a helyes késései legfontosabb paraméter a tanulási rátát `rf_lrPerMb`. Ha a képzési pontosságának beállítása (első ábra a 2. rész) nincs közel 0-5 %, valószínűleg az okozza helytelen a tanulási rátát. A többi paraméter kezdve `rf_` kevésbé fontos. Általában a képzés hiba kell exponenciálisan csökkentheti, és hamarosan 0 % képzési után kell.
- Megoldási bemeneti: az alapértelmezett lemezkép megoldás lehet 224 x 224 képpontban megadva. Magasabb képfelbontás használatával (paraméter: `rf_inputResoluton`), például gyakran jelentős 448 x 448 vagy 896 x 896 képpont pontossága javítja, de DNN pontosítás lelassul. **Magasabb képfelbontás segítségével szinte ingyenes ebédet, és szinte mindig növekedhet pontossága**.
- DNN túlzott méretezés: elkerülése érdekében a képzési és a vizsgálat pontosságának jelentős különbség DNN pontosítás során (első rész 2. ábra). Ez a szünet jelkiesés díjszabás csökkenthető `rf_dropoutRate` legalább 0.5, és a regularizer súly növelésével `rf_l2RegWeight`. A magas jelkiesés mértékben használata különösen akkor hasznos, ha a DNN bemeneti képfelbontás magas lehet.
- Próbálja használatával alaposabb DNNs módosításával `rf_pretrainedModelFilename` a `ResNet_18.model` vagy `ResNet_34.model` vagy `ResNet_50.model`. A Resnet-50 modell nem csak mélyebben, de az utolsó előtti réteg kimenetét mérete 2048 úszó (és a a ResNet-18-ra és ResNet-34 modellek 512 úszó). Ez a megnövekedett dimenzió különösen hasznos lehet, ha egy SVM osztályozó betanítása.

## <a name="part-3---custom-dataset"></a>3. rész - egyéni adatkészlet

1. és 2 részében azt betanítása és kiértékelése egy kép besorolási modell a megadott felső törzs ruházati textúrák lemezképek használatával. Most megmutatjuk, hogyan használhatja az egyéni, felhasználó által megadott adatkészlet is. Vagy, ha nem érhető el, hogyan hozza létre és megjegyzésekkel, például a Bing használatával dataset kép keresése.

### <a name="using-a-custom-dataset"></a>Egy egyéni adatkészlet

Először hozzuk rendelkezik egy pillantást a mappastruktúra a ruházati mintázat adatok. Vegye figyelembe, hogyan összes lemezképeket, a különböző attribútumokat a megfelelő almappákban *pontozott*, * leopard, és *csíkozott* : *DATA_DIR/képek/fashionTexture/*. Ügyeljen arra is, hogyan a kép mappanevet is lép fel a `PARAMETERS.py` fájlt:
```python
datasetName = "fashionTexture"
```

Egy egyéni adatkészlet más dolga, mint a gyökérmappa-szerkezetében lejátszó almappák azok attribútum alapján, és másolja a almappák egy új felhasználó által megadott belül hol áll összes lemezkép *DATA_DIR/képek/newDataSetName/*. A csak kód módosítása kötelező is, hogy a `datasetName` változó *newDataSetName*. 1-5 parancsfájlok majd végrehajthatók a sorrendben, és minden közbenső fájlokat *DATA_DIR/proc/newDataSetName/*. Nincs más kód módosításokra szükség.

Fontos, hogy egyes lemezképek pontosan egy attribútummal lehet hozzárendelni. Például lenne helytelen attribútumokkal "állat" és "leopard", mert a "leopard" lemezképek is tagja lenne "állat". Emellett érdemes távolítsa el a lemezképbe, amelyek nem egyértelmű, és ezért nehezen megjegyzésekkel.



### <a name="image-scraping-and-annotation"></a>Kép lekaparást és Megjegyzés

Megjegyzésekkel ellátott képek betanításra és tesztelésre elég nagy számú gyűjtése bonyolult lehet. Egy probléma megoldásához módja scrape képek az internetről. Például lásd az alábbi a Bing kép keresési eredmények között, a lekérdezés *csíkozott pólóra*. Várható, mivel a legtöbb képek valóban csíkozott pólók. A néhány nem megfelelő vagy nem egyértelmű képet (például az 1. oszlop, sor 1; vagy 3. oszlop, sor 2) azonosított, és könnyen eltávolítása:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

A nagy és sokszínű adatkészlet létrehozásához több lekérdezés kell használni. Például 7\*lekérdezések használatával automatikusan {blouse, hoodie, pulóver, sweater, shirt, pólóra, alsóing} ruházati elemek és attribútumok {csíkozott, pontozott, leopard} összes kombinációjának kell synthesized 3 = 21. Az 50 legfontosabb lemezképek lekérdezésenként letöltése 21 * 50 = 1050 legfeljebb majd vezetne képek.

Ahelyett, hogy a képek le kézzel a Bing kép keresési, ehelyett sokkal könnyebben a [kognitív szolgáltatások Bing kép Search API](https://www.microsoft.com/cognitive-services/bing-image-search-api) egy lekérdezési karakterlánc megadott kép URL-címek készletét adja vissza, amely.

A letöltött képek némelyike pontosan vagy ismétlődő (például térnek el egymástól csak lemezkép megoldási vagy jpg összetevők). Ezeket az ismétlődéseket, hogy a tanítási és tesztelési felosztás nem tartalmaznak a azonos képek el kell távolítani. Ismétlődő lemezképek eltávolítása elérhető egy ujjlenyomat-alapú módszert használja, amely két lépésben működik használatával: (i) először a kivonat karakterláncot értékei összes lemezkép; (ii) az egy második fázisban keresztül a képek csak ezeket a képeket tárolják, amely még nem lett tapasztalt kivonat karakterláncot. A rendszer elveti a többi képhez. Észleltünk a `dhash` megközelítés a Python könyvtárban `imagehash` és leírt [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) , végezze el a paraméter `hash_size` 16 értékre. OK helytelenül eltávolítása az egyes nem ismétlődő képek mindaddig, amíg a legtöbb, a rendszer a valós duplikált kerülnek eltávolításra.





## <a name="conclusion"></a>Összegzés

Néhány kulcsfontosságú emeli ki ebben a példában a következők:
- Kód betanítása, kiértékeléséhez és központi telepítése a lemezképet a besorolási modell.
- A megadott bemutató kép, de könnyen alkalmazkodó (egy sorban módosítása) saját kép adatkészlet használatához.
- Az-a-legkorszerűbb a tanulási átviteli alapuló pontosságot modellek betanítása bevezetett szolgáltatások szakértői.
- Interaktív modell fejlesztése az Azure Machine Learning-munkaterület és a Jupyter Notebook.


## <a name="references"></a>Referencia

[1] Alex Krizhevsky Ilya Sutskever és Geoffrey E. Hinton, [ _mély Convolutional Neurális hálózatokat ImageNet besorolás_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming Helykiszolgálójához, Xiangyu Zhang, Shaoqing Ren és Jian Sun, [ _mély maradék Learning a kép felismerés_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
