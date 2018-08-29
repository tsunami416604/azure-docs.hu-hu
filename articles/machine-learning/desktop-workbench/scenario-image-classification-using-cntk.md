---
title: Kép besorolása a CNTK belül az Azure Machine Learning Workbench használatával |} A Microsoft Docs
description: Betanításához, vizsgálja, és a egy egyéni rendszerkép osztályozási modell segítségével az Azure ML Workbench üzembe helyezése.
services: machine-learning
documentationcenter: ''
author: PatrickBue
ms.author: pabuehle
manager: mwinkle
ms.reviewer: marhamil, mldocs, garyericson, jasonwhowell
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 10/17/2017
ms.openlocfilehash: 48c21638fe5756e6527288ed0fdc73dd9e331afd
ms.sourcegitcommit: baed5a8884cb998138787a6ecfff46de07b8473d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "35622216"
---
# <a name="image-classification-using-azure-machine-learning-workbench"></a>Képek besorolása Azure Machine Learning Workbench használatával

Kép besorolási megközelítések nagy számú számítógépes Látástechnológiai problémák megoldásához használható.
Ezek közé tartozik a modellek, amelyek például egyéb kérdésre készítése: *szerepel a lemezkép objektum?* ahol objektum lehet például *kutya*, *autó*, vagy  *szállításra*. Vagy összetettebb kérdések, például: *milyen osztályba tartozó szemmel betegségek súlyosság szerint a betegek retinal vizsgálat van érintő?*.

Ez az oktatóanyag címek az ilyen problémák megoldásához. Bemutatjuk, hogyan betanításához, kiértékelheti és üzembe helyezés a saját rendszerkép besorolási modell az [Microsoft Cognitive Toolkit (CNTK) ](https://docs.microsoft.com/cognitive-toolkit/) mély tanulás.
A példában képek biztosított, de az olvasó is használata a saját adatkészletet, és a saját egyéni modelleket taníthat be.

Számítógépes Látástechnológia megoldások hagyományosan ismeretével manuálisan azonosíthatja és úgynevezett megvalósításához szükséges *funkciók*, amely jelölje ki a kívánt információk képeken.
Ez a manuális módszer megváltozott a híres a 2012-ben [AlexNet](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) [1] Deep Learning-papír, és a jelenlegi, részletes Neurális hálózatok (DNN) használhatók ezek a szolgáltatások automatikus megkereséséhez.
Dnn-eket egy hatalmas javulása a mező nem csak képbesorolás, de más Computer Vision problémák, például objektumfelismerés és lemezkép hasonlóság is vezetett.


## <a name="link-to-the-gallery-github-repository"></a>A katalógus GitHub-adattárra mutató hivatkozás
[https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK](https://github.com/Azure/MachineLearningSamples-ImageClassificationUsingCNTK)

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban van felosztva, amelyek három részből áll:

- 1. rész bemutatja, hogyan betanításához, kiértékeléséhez és üzembe helyezése egy előre betanított DNN featurizer használatával, és a kimenetét egy SVM képzési egy képosztályozó rendszert.
- 2. rész majd bemutatja, hogyan, pontosságának növelése érdekében például a DNN pontosítását használni egy rögzített featurizer helyett.
- 3. rész bemutatja, hogyan saját adatkészlet használata helyett a például képeket, és szükség esetén hogyan adatkészletet a saját előállításához automatizované získávání dat lemezképek, a háló.

Míg a korábbi élmény gépi tanulással és a CNTK nem kötelező, hasznos lehet a mögött húzódó irányelveket megértésében. Pontosság számok, képzést idő, stb. a jelen oktatóanyagban jelentett csak referenciaként szolgálnak, és a tényleges értékek, ha a kódot futtató szinte biztosan eltérőek.


## <a name="prerequisites"></a>Előfeltételek

Ez a példa futtatásához az Előfeltételek a következők:

1. Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el).
2. A [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) következő a [rövid telepítési útmutatójában](../service/quickstart-installation.md) telepítse a programot, és hozzon létre egy munkaterületet.  
3. Egy Windows-gépen. Windows operációs rendszer szükség, mivel a Workbench támogatja, csak a Windows és MacOS, miközben a Microsoft Cognitive Toolkit (amely használjuk, deep learning-kódtár) csak támogatja a Windows és Linux rendszereken.
4. Egy dedikált GPU, nem hajtható végre a SVM képzés 1,. rész az szükséges, azonban szükség van a 2. rész ismertetett DNN finomítása. Ha nem áll rendelkezésre az erős GPU, a több gpu-n be szeretne, vagy nem rendelkezik egy Windows-gépen, majd fontolja meg az Azure Deep Learning virtuális gép Windows operációs rendszerrel. Lásd: [Itt](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning) 1 kattintással üzembe helyezési útmutató. Üzembe helyezését követően csatlakozzon a virtuális Géphez távoli asztali kapcsolaton keresztül, Workbench telepítése van, és futtassa a kódot helyileg a virtuális gépről.
5. Például OpenCV különböző Python-kódtárakat kell telepíteni. Kattintson a *parancssor megnyitása* származó a *fájl* menüjében a Workbench, és futtassa a következő parancsokat a függőségek telepítéséhez:  
    - `pip install https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`  
    - `pip install opencv_python-3.3.1-cp35-cp35m-win_amd64.whl` Miután letöltötte a OpenCV kerék a http://www.lfd.uci.edu/~gohlke/pythonlibs/ (a pontos fájl nevét és verzióját módosíthatja)
    - `conda install pillow`
    - `pip install -U numpy`
    - `pip install bqplot`
    - `jupyter nbextension enable --py --sys-prefix bqplot`
    - `jupyter nbextension enable --py widgetsnbextension`

### <a name="troubleshooting--known-bugs"></a>Hibaelhárítási / ismert hibák
- 2. rész a grafikus Processzor szükséges, és ellenkező esetben a hiba "Batch normalizálási képzési processzor még nem használható" van értékelésére tett kísérlet során a DNN finomíthatja.
- DNN-betanítás során memóriahiány hibák javíthatják a minibatch méretének csökkentésével (változó `cntk_mb_size` a `PARAMETERS.py`).
- A kódot tesztelés CNTK 2.2 használatával, illetve ha a módosításokat kell azok nélkül, vagy csupán kisebb futtatási is, a régebbi (2.0-s verziójú akár) és újabb verziók.
- A írása idején az Azure Machine Learning Workbench notebookok 5 MB-nál nagyobb megjelenítő problémákba ütközött a. Notebookok ilyen nagy méretű akkor fordulhat elő, ha a rendszer menti a notebookot az összes cella kimenet jelenik meg. Ha ez a hiba jelentkezik, majd nyissa meg a parancssort a Workbench alkalmazás fájl menüjében, hajtsa végre a `jupyter notebook`, nyissa meg a notebook, törölje az összes kimeneti és mentse a notebookot. Után hajt végre ezeket a lépéseket, a jegyzetfüzet nyílik meg megfelelően az Azure Machine Learning Workbench belül újra.
- Ebben a példában a megadott összes parancsfájl kell helyileg, hajtható végre, nem pedig például a docker távoli környezetben. Az összes jegyzetfüzet kell hajtható végre, kernel a projekt helyi kernel "PROJECTNAME helyi" nevű (például "myImgClassUsingCNTK helyi") értékre.

    
## <a name="create-a-new-workbench-project"></a>Egy új workbench-projekt létrehozása

Ebben a példában egy sablon használatával új projekt létrehozásához:
1.  Nyissa meg az Azure Machine Learning Workbenchet.
2.  Az a **projektek** lap, kattintson a **+** aláírásához, és válassza ki **új projekt**.
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt.
4.  Az a **projektsablonok keresése** keresőmezőbe, írja be a "Besorolást Image", és válassza ki a sablont.
5.  Kattintson a **Create** (Létrehozás) gombra.

Az alább látható projektstruktúra elvégezhesse ezeket a lépéseket hoz létre. A projekt könyvtárában korlátozva lehet kisebb, mint 25 MB-ban, mivel az Azure Machine Learning Workbench másolatot hoz létre a mappa (való engedélyezéséhez a futtatási előzmények) minden egyes futás után. Ezért az összes rendszerkép és ideiglenes fájlok lesznek mentve, és a könyvtárból *~/Desktop/imgClassificationUsingCntk_data* (néven *DATA_DIR* ebben a dokumentumban).

  Mappa| Leírás
  ---|---
  aml_config/|                           Az Azure Machine Learning Workbench konfigurációs fájlokat tartalmazó könyvtárat
  szalagtárak /|                              Az összes Python és a Jupyter segédfüggvények tartalmazó könyvtár
  jegyzetfüzetek /|                              Az összes jegyzetfüzet tartalmazó könyvtár
  erőforrások /|                              Minden erőforrás tartalmazó (a példában URL-címe módon képek)
  parancsfájlok /|                              Könyvtárra, amelyben az összes parancsfájl
  PARAMETERS.py|                       Python-szkript összes paramétereinek megadása
  Readme.md|                           Ez a fontos dokumentum


## <a name="data-description"></a>Adatok leírása

Ebben az oktatóanyagban használja a következő példa egy felső törzs ruházati mintázat adatkészlet akár 428 lemezképek álló futtatása. Minden egyes képe egy három különböző textúrákhoz (pontozott, csíkozott, leopard) leképezésként van feliratozva. Azt tartani lemezképek száma kisebb, hogy ez az oktatóanyag gyorsan hajthatók végre. Azonban a kód tesztelt és képek vagy több tízezer együttműködik. Az összes rendszerkép lett lekaparták a Bing Image Search használatával, és az aktuális-leképezésként ismertetése [3. rész](#using-a-custom-dataset). A kép URL-címet a megfelelő attribútumaik szerepelnek a */resources/fashionTextureUrls.tsv* fájlt.

A parancsfájl `0_downloadData.py` letölti az összes rendszerkép a *DATA_DIR/képek/fashionTexture/* könyvtár. A 428 URL-címek valószínűleg sérült. Ez nem probléma, és csak azt jelenti, hogy van-e valamivel kisebb lesz lemezképek betanítására és tesztelésére. Ebben a példában a megadott összes parancsfájl kell helyileg, hajtható végre, nem pedig például a docker távoli környezetben.

A következő ábrán látható példa az attribútumok (balra), pontozott csíkozott (középső), és a (jobb oldali) leopard. Jegyzetek elkészült, a felső törzs ruházati elem alapján.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/examples_all.jpg"  alt="alt text" width="700">
</p>


## <a name="part-1---model-training-and-evaluation"></a>1. rész – modell betanítása és kiértékelése

Ez az oktatóanyag első részében azt vannak egy rendszer, amely használja, de nem módosíthatják, egy előre betanított Neurális hálózat betanítása. Ezt előre betanított DNN-featurizer szolgál, és a egy lineáris SVM előre jelezni az attribútum be van tanítva (pontozott, csíkozott vagy leopard) egy adott rendszerkép.

Mi most részletes, lépésről lépésre, és a parancsfájlok kell hajtható végre, jelenítse meg ezt a megközelítést ismerteti. Javasoljuk, hogy minden lépése után mely fájlok íródtak, és ahol azokat írt vizsgálhatja meg.

Minden fontos paraméterek vannak megadva, és a egy rövid magyarázatot megadni, egyetlen helyen történő: a `PARAMETERS.py` fájlt.




### <a name="step-1-data-preparation"></a>1. lépés: Adatok előkészítése
`Script: 1_prepareData.py. Notebook: showImages.ipynb`

A notebook `showImages.ipynb` jelenítheti meg a képeket, és javítsa ki a jegyzet, igény szerint használható. A jegyzetfüzet futtatásához nyissa meg az Azure Machine Learning Workbench, ez a beállítás jelenik meg, ha a "Start Notebook Server" módosítása a helyi projekt kernel nevű "PROJECTNAME helyi" (pl. "myImgClassUsingCNTK helyi"), kattintson a, és ezután hajtsa végre az összes celláját a notebook. Ha panaszkodik, hogy túl nagy a megjelenítéshez-e a notebook hibaüzenetet kap, tekintse meg a hibaelhárítási rész a jelen dokumentum.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showImages.jpg" alt="alt text" width="700"/>
</p>

Most már a nevű parancsprogram végrehajtása `1_prepareData.py`, mely rendel az összes rendszerkép vagy, hogy a betanítás beállítása, vagy a vizsgálatot. Ez a hozzárendelés egymást kölcsönösen kizáró – nincs képzési kép is használatos, tesztelési vagy fordítva. Alapértelmezés szerint egy véletlenszerű 75 %-a képek az egyes attribútum az osztály a betanítási vannak rendelve, és a maradék 25 % hozzárendelt tesztelése. A parancsfájl által létrehozott összes adatot mentett a *DATA_DIR/proc/fashionTexture/* mappát.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_1_white.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-2-refining-the-deep-neural-network"></a>2. lépés: A Neurális hálózat finomítása
`Script: 2_refineDNN.py`

Leírtak azt ez az oktatóanyag 1. rész, az előre betanított DNN tárolt befektetett (azt jelenti, hogy nem történik). Azonban a parancsfájl nevű `2_refineDNN.py` továbbra is hajtja végre az 1. rész előre betanított betöltés közben [ResNet](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf) : [2] modell, és módosítja azt, például, hogy a magasabb bemeneti kép feloldásához. Ez a lépés gyors (másodperc), és nem szükséges egy GPU.

Az oktatóanyag 2 részében a PARAMETERS.py módosítása okok fájlt a `2_refineDNN.py` pontosíthatja az előre betanított DNN-szkript. Alapértelmezés szerint nyújtunk, 45 képzési alapidőszakkal pontosítás során.

Mindkét esetben a kész modell majd íródik a fájl *DATA_DIR/proc/fashionTexture/cntk_fixed.model*.

### <a name="step-3-evaluate-dnn-for-all-images"></a>3. lépés: Az összes rendszerkép DNN kiértékeléséhez
`Script: 3_runDNN.py`

Az (esetleg finomított) DNN az utolsó lépésben a szabadkézi az rendszerképek most használatával. Adja meg egy képet a DNN bemeneteként, a kimenete a modell az utolsó előtti rétegből az 512-úszó vektort. Ezt a támadási, mint maga a kép sokkal kisebb dimenzionális. Mindazonáltal, tartalmazza az (és még kiemelése) a kép fontos felismerje a kép attribútummal, akkor, ha a ruházati cikk egy pontozott csíkozott, vagy a leopard mintázat szereplő információk.

Az összes DNN kép megjelenítésének menti, és a fájl *DATA_DIR/proc/fashionTexture/cntkFiles/features.pickle*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_4_white.jpg" alt="alt text" width="700"/>
</p>


### <a name="step-4-support-vector-machine-training"></a>4. lépés: Tartóvektor-gép képzés
`Script: 4_trainSVM.py`

Az 512 úszó semmilyen felelősséget a legutóbbi lépésben számított használnak egy SVM besorolás betanítására: megadva bemenetként egy képen, a SVM kimenete egy pontszám minden attribútum meglétét. A példa adatkészlet Ez azt jelenti, egy pontszám "csíkozott", "pontozott", és a "leopard".

Parancsfájl `4_trainSVM.py` betölti a betanító kép, egy eltérő értékek a regularizációs (slack) paraméter C SVM betanítja és a SVM tartja a legnagyobb pontosságú. A besorolás pontossága hátoldalán található a konzol és a Workbench megjelennek. A megadott mintázat adatok ezeket az értékeket kell körülbelül 100 %-os és 88 % jelölik. Végül a betanított SVM íródik a fájl *DATA_DIR/proc/fashionTexture/cntkFiles/svm.np*.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/vienna_svm_log_zoom.jpg" alt="alt text" width="700"/>
</p>



### <a name="step-5-evaluation-and-visualization"></a>5. lépés: Értékelés és Vizualizáció
`Script: 5_evaluate.py. Notebook: showResults.ipynb`

A betanított kép osztályozó pontosságát a parancsfájl mérhető `5_evaluate.py`. Az összes teszt rendszerkép használata a betanított SVM osztályozó, rendel minden egyes kép az attribútum a legnagyobb pontszámot, és összehasonlítja az előre jelzett attribútumok az alapoktól hiteles jegyzetekkel ellátott parancsfájl pontszámokat.

Szkript kimenete `5_evaluate.py` alább találja. Minden egyes osztály a besorolás pontossága számított, valamint a teljes vizsgálat beállítása ("általános pontosság"), és az átlagos pontosságának keresztül az egyes pontosságú ("általános osztály átlagolt pontosság"). 100 %-os felel meg a lehető legpontosabb és 0 %-a legrosszabb. Véletlenszerű találgatás akkor átlagosan az eredmény 1 osztály átlagolt pontosságát keresztül attribútumok számát: a mi esetünkben ez pontossága 33,33 % lenne. Ezekkel az eredményekkel jelentős javítására, mint például a nagyobb bemeneti felbontást használatakor `rf_inputResoluton = 1000`, azonban a DNN-számítás hosszabb időt rovására.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_6_white.jpg" alt="alt text" width="700"/>
</p>

Mellett pontossága ROC-görbe rajzolódik ki a megfelelő terület-a-görbe (balra); és a keveredési mátrix (jobb oldali) jelenik meg:

<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat.jpg" alt="alt text" width="700"/>
</p>

Végül a notebook `showResults.py` Görgessen végig a tesztelési lemezképeihez, és megjelenítheti a megfelelő besorolási pontszámok van megadva. 1. lépés leírtak ebben a példában minden notebook kell használnia a projekt helyi kernel "PROJECTNAME helyi" nevű:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/notebook_showResults.jpg" alt="alt text" width="700"/>
</p>





### <a name="step-6-deployment"></a>6. lépés: központi telepítés
`Scripts: 6_callWebservice.py, deploymain.py. Notebook: deploy.ipynb`

A betanított rendszer most REST API-ként tehető közzé. Üzembe helyezés ismertetése a notebook `deploy.ipynb`, és az Azure Machine Learning Workbench belül funkciók alapján (ne felejtse el "PROJECTNAME helyi" nevű a helyi projekt kernel kernel állítja). Lásd még a kiváló telepítési szakaszát a [osztályozása oktatóanyaghoz](tutorial-classifying-iris-part-3.md) további üzembe helyezési a kapcsolódó adatokat.

Miután üzembe helyezte a webszolgáltatás nem hívható meg a szkripttel `6_callWebservice.py`. Vegye figyelembe, hogy a webszolgáltatás IP-címét (helyi vagy felhőbeli) kell először be kell állítani a szkriptben. A notebook `deploy.ipynb` azt ismerteti, hogyan találhatja meg az IP-címet.








## <a name="part-2---accuracy-improvements"></a>2. rész – pontossága fejlesztései

Az 1. rész hogy bemutatta, hogyan kép besorolása alapján egy lineáris Tartóvektor-gép a az 512-úszó kimenetet egy részletes Neurális hálózat betanítása. Ebben DNN előre betanított kép millió volt, és az utolsó előtti réteg adja vissza a szolgáltatás vektort. Ez a megközelítés akkor gyors, mert a DNN van –, de mégis gyakran a megfelelő eredményt ad.

Azt most 1. rész a modell pontosságának javítása több módot is nyújt. Legjelentősebb hogy finomíthatja a lekapcsolva tartja, rögzített helyett a DNN.

### <a name="dnn-refinement"></a>A DNN finomítása

Egy SVM helyett egy teheti meg a besorolást, közvetlenül a Neurális hálózatban. Ez az előre betanított DNN, az utolsó előtti rétegből bemenetként az 512 úszó foglalnak egy új utolsó réteg hozzáadásával érhető el. Ez a besorolás a DNN előnyeit, hogy a teljes hálózati visszaterjesztés újrataníthatók most. Ezt a módszert gyakran sokkal jobb besorolási pontosságú, mint az előre betanított DNN használatához képest vezet-azonban sokkal több képzési időt (akár a GPU) rovására.

A változó módosítása helyett egy SVM Neurális hálózat betanítása történik `classifier` a `PARAMETERS.py` a `svm` való `dnn`. Ezt követően 1. rész leírtak (1. lépés) adat-előkészítési és SVM képzési (4. lépés) kivételével az összes parancsfájl kell újra végre kell hajtania. DNN finomítása egy GPU van szükség. Ha nincs GPU nem található, vagy ha a GPU zárolva van (például a korábbi CNTK futtatása) majd parancsfájl `2_refineDNN.py` hibát jelez. DNN képzési nagyvállalat memóriahiány hiba történt az egyes gpu-k, amelyek javíthatják a minibatch méretének csökkentésével (változó `cntk_mb_size` a `PARAMETERS.py`).

Képzési befejezése után a rendszer menti a finomított modell *DATA_DIR/proc/fashionTexture/cntk_refined.model*, és a egy diagram dolgozni, amely azt ismerteti, hogyan módosíthatja a hibák a tanítási és tesztelési besorolási betanítás során. Vegye figyelembe az adott diagram, amely a hiba a gyakorlókészlethez sokkal kisebb, mint a vizsgálat esetén. Az úgynevezett túlterhelt záró viselkedés csökkenteni lehet, például jelkiesés aránya magasabb érték használatával `rf_dropoutRate`.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/output_script_3_plot.png" alt="alt text" height="300"/>
</p>

Ahogy az alábbi diagram látható, DNN finomítható a megadott adatkészlet használatával pontossága 92.35 % előtt (1. rész) 88.92 % kedvezményt. Különösen a "pontozott" képek jelentős javítására, egy ROC terület-a-görbéjének 0,98 studióval programhiba-keresést finomítható az. 0.94 előtt. Egy kisebb adatkészletet használunk, és ezért nem egyeznek a tényleges pontosság, a kód futtatása. Ez az eltérés van például a képek egy képzés és egy tesztelési a véletlenszerű felosztás sztochasztikus hatásai miatt.
<p align="center">
<img src="media/scenario-image-classification-using-cntk/roc_confMat_dnn.jpg" alt="alt text" width="700"/>
</p>

### <a name="run-history-tracking"></a>Futtassa az előzmények nyomon követése

Az Azure Machine Learning Workbench tárolt, az előzmények az egyes Azure-on futtatja, hogy két vagy több futtatás, amelyek összehasonlítása még hét egymástól. Ennek a magyarázatát részletesen ismertetjük a [osztályozása oktatóanyaghoz](tutorial-classifying-iris-part-2.md). Azt is szemlélteti az alábbi képernyőfelvételnek megfelelően, ahol a parancsfájl futtatásának két összehasonlítva `5_evaluate.py`, vagy DNN pontosítás használatával, `classifier = "dnn"`(Futtatás száma 148) vagy SVM képzés, `classifier = "svm"` (Futtatás száma 150).

Az első képernyőképen DNN pontosítás SVM képzés az összes osztály-nál nagyobb pontosságú vezet. A második képernyőképen látható összes metrikát, amely nyomon vannak, beleértve az osztályozó által igénybe vett volt. A nyomon követési történik a szkriptben `5_evaluate.py` az Azure Machine Learning Workbench naplózó meghívásával. Emellett a parancsfájl ROC görbe és a keveredési mátrix is menti a *kimenete* mappát. Ez *kimenete* mappa a következő különleges, hogy a tartalom is követi nyomon a Workbench előzmények funkció, és ezért a kimeneti fájlokat is elérhető, tetszőleges időpontban, függetlenül attól, hogy helyi példányt felül lett írva.

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison1.jpg" alt="alt text" width="700"/>
</p>

<p align="center">
<img src="media/scenario-image-classification-using-cntk/run_comparison2b.jpg" alt="alt text" width="700"/>
</p>


### <a name="parameter-tuning"></a>A paraméter hangolása

Az igaz a legtöbb machine learning-projektek, gondos paraméter hangolása, valamint a különböző tervezési döntésekhez kiértékelése jó eredmények beolvasása egy új adatkészlet szükséges. Ezek a feladatok ellátását megkönnyítő, minden fontos paraméterek vannak megadva, és röviden ismerteti a megadott, egyetlen helyen történő: a `PARAMETERS.py` fájlt.

A legígéretesebb javításai rések leggyakoribb okai a következők:

- Adatminőség: Győződjön meg róla a tanítási és tesztelési eljárások kiváló minőségű rendelkezik. Azt jelenti, a képek megfelelően annotált, nem egyértelmű képek eltávolítása (például ruházati elem szétterítése és pontok is), és az attribútumok egymást kölcsönösen kizáró (vagyis a kiválasztott úgy, hogy az egyes rendszerképek pontosan egy attribútum tartozik).

- Ha az objektum-– fontos kicsi a képen majd kép besorolási megközelítések ismert, hogy nincsenek megfelelően működjön. Ebben az esetben fontolja meg egy objektum észlelési módszert a leírtak szerint [oktatóanyag](https://github.com/Azure/ObjectDetectionUsingCntk).
- A DNN pontosítás: kellhet késései legfontosabb paraméter megfelelő tanulási rátát `rf_lrPerMb`. Ha a pontosságot, a képzési beállítása (2. rész az első ábrát), nem megközelíti a 0 – 5 %-a legvalószínűbb, hogy helytelen a tanulási rátát. A többi paraméter kezdve `rf_` kevésbé fontos. Általában a betanítási hiba kell csökkentenie hatványozott mértékben növekszik, és 0 %-os közeli képzési. után lép érvénybe.

- Bemeneti feloldása: az alapértelmezett lemezkép 224 x 224 képpont monitorfelbontást. Magasabb képfelbontás használatával (paraméter: `rf_inputResoluton`), például gyakran jelentős 448 x 448 vagy 896 x 896 képpont javítja a pontosságot, de DNN pontosítás lelassul. **Magasabb képfelbontás használatával szinte ingyenebéd, és szinte mindig felgyorsíthatók a pontosság**.

- A DNN túlzott méretezés: elkerülése érdekében a betanítási és vizsgálati pontossága nagy különbség DNN pontosítás során (első rész 2. ábra). Ez a szünet csökkenthető jelkiesés díjszabás vonatkozik `rf_dropoutRate` legalább 0.5-ös, és növelje a regularizer súly `rf_l2RegWeight`. A magas jelkiesés díj alapján különösen hasznos lehet, ha túl magas a bemeneti DNN képfelbontás.

- Próbáljon mélyebb dnn-eket módosításával `rf_pretrainedModelFilename` a `ResNet_18.model` egyaránt `ResNet_34.model` vagy `ResNet_50.model`. A Resnet-50-modell nem csak mélyebb, de az utolsó előtti réteg annak kimenete az mérete 2048 úszó (vs. a ResNet-18-ra és a ResNet-34 modellekhez 512 úszó). Ez a megnövekedett dimenzió különösen hasznos lehet, amikor egy SVM osztályozó által igénybe vett képzés.

## <a name="part-3---custom-dataset"></a>3. rész - egyéni adatkészlet

1. és 2 rész hozunk betanított és egy kép osztályozási modell a megadott felső törzs ruházati textúrákhoz lemezképek használatával értékeli ki. Most már bemutatjuk, hogyan használhatja egy egyéni felhasználó által megadott adatkészlet is. Vagy, ha nem érhető el, hogyan hozhat létre és jegyzettel láthatja el például egy adatkészletet, a Bing használatával rendszerkép keresése.

### <a name="using-a-custom-dataset"></a>Egyéni adatkészlet használata

Először nézzük meg, a mappastruktúra a ruházati mintázat adatok címen. Vegye figyelembe, hogyan összes képeket a különböző attribútumokat a következők a megfelelő almappák *pontozott*, * leopard, és *csíkozott* , *DATA_DIR/képek/fashionTexture/*. Ügyeljen arra is, hogyan a lemezképnév mappát is történik a `PARAMETERS.py` fájlt:
```python
datasetName = "fashionTexture"
```

Az egyéni adatkészlet a következő egyszerű: a gyökérmappa-szerkezetében szaporodó almappák azok attribútum alapján, és ezek az almappák másolja egy új, felhasználó által megadott könyvtárat az összes rendszerkép amelyeknél *DATA_DIR/képek/newDataSetName/*. Az egyetlen szükséges kód változás az, hogy állítsa be a `datasetName` változó *newDataSetName*. 1 – 5 parancsfájlok majd hajtható végre sorrendben, és az összes köztes fájlok írt *DATA_DIR/proc/newDataSetName/*. Más kódmódosítás nélkül szükség.

Fontos, hogy egyes rendszerképek pontosan egy attribútummal is hozzárendelhető. Ha például lenne attribútumokkal "állat" és "leopard", mivel a "leopard" lemezkép "állat" lenne is tartozhat, nem megfelelő. Azt is ajánlott eltávolítani a lemezképek, amelyek nem egyértelmű, és ezért nehezen jegyzettel láthatja el.



### <a name="image-scraping-and-annotation"></a>Automatizované získávání dat és jegyzet

Jegyzettel ellátott képek és tesztelésre elég nagy számú gyűjtése nehézkes lehet. A probléma megoldásához az egyik lehetőség, hogy scrape képek az internetről. Például, tekintse meg alább a lekérdezés a Bing Képkeresés eredmények *feliratú pólóban csíkozott*. Megfelelően működik, a legtöbb lemezképek valóban csíkozott pólók. Néhány nem megfelelő vagy nem egyértelmű képet (például az 1. oszlop, 1; sor vagy oszlop 3, sor 2) lehet azonosítani és egyszerűen eltávolítani:
<p align="center">
<img src="media/scenario-image-classification-using-cntk/bing_search_striped.jpg" alt="alt text" width="600"/>
</p>

A nagy és változatos adatkészletet létrehozása több lekérdezést kell használni. Ha például 7\*lekérdezések használatával automatikusan {blouse, hoodie, pulóver, sweater, ing, feliratú pólóban, alsóing} ruházati elemek és attribútumok {csíkozott, pontozott, leopard} minden kombinációja lehet synthesized 3 = 21. Lekérdezés az 50 legfontosabb kép letöltése 21 * 50 = 1050 legfeljebb majd vezetne lemezképek.

Ahelyett, hogy képeket le kézzel a Bing Képkeresés, ehelyett sokkal egyszerűbb legyen a [Cognitive Services a Bing Image Search API](https://www.microsoft.com/cognitive-services/bing-image-search-api) egy lekérdezési karakterláncban megadott kép URL-címek készletét adja vissza, amely.

A letöltött lemezképek némelyike pontos vagy ismétlődő (csak a lemezkép megoldás vagy jpg-összetevők által például eltérőek). Ezeket az ismétlődéseket, hogy a tanítási és tesztelési felosztás az azonos lemezképek nem tartalmaznak el kell távolítani. Ismétlődő képek eltávolítása tehető egy ujjlenyomat-alapú módszer, amely két lépésben használható: (i) először az összes rendszerkép; a Jelszókivonat-karakterlánc jön létre (ii) a második fázis a képek csak az adott képeket őrzi meg, amely még nem lett találkozott kivonat karakterláncot. Az összes rendszerkép elvesznek. Találtunk a `dhash` megközelítés a Python-kódtár az `imagehash` és a leírt [blog](http://www.hackerfactor.com/blog/index.php?/archives/529-Kind-of-Like-That.html) , végezze el a paramétert `hash_size` 16 értékre. Azt az OK nem megfelelően távolítsa el az egyes egyszer előforduló képek mindaddig, amíg a legtöbb valós ismétlődések törlődnek.





## <a name="conclusion"></a>Összegzés

Néhány fontosabb az ebben a példában a következők:
- Kód betanítását, kiértékelheti és képbesorolási modellek üzembe helyezése.
- Bemutató rendelkezésre bocsátott rendszerképek találhatók, de egyszerűen testreszabható (egysoros) használatára való áttérést saját rendszerképet adatkészlet.
- Állapota-a-a legújabb szakértői szolgáltatások átvitele tanulás alapján nagy pontosságú modelleket taníthat be implementálva.
- Interaktív modell fejlesztését az Azure Machine Learning Workbench és Jupyter Notebookot.


## <a name="references"></a>Referencia

[1] Alex Krizhevsky Ilya Sutskever és Geoffrey E. Hinton, [ _épít besorolás, a mély Konvolúciós Neurális hálózatokkal és_](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf). NIPS 2012.  
[2] Kaiming He, Xiangyu Zhang, Shaoqing Ren és Jian Sun, [ _mély maradék Képfelismerés tanulási_](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf). CVPR 2016.
