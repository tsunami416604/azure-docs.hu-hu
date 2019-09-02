---
title: A Linux megismerése
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan hajthat végre több gyakori adatelemzési feladatot a Linux Data Science Virtual Machine használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: 759c804fd4128d7b28cbba078cab5e9d7e6453f6
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208294"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Adatelemzés Linux-Data Science Virtual Machine az Azure-ban

Ez az útmutató bemutatja, hogyan végezheti el számos gyakori adatelemzési feladatot a Linux Data Science Virtual Machine (DSVM) használatával. A Linux DSVM az Azure-ban elérhető virtuálisgép-rendszerkép, amely az adatok elemzéséhez és a gépi tanuláshoz gyakran használt eszközök gyűjteményével van előtelepítve. A legfontosabb szoftver-összetevők a [linuxos Data Science Virtual Machine](linux-dsvm-intro.md)kiépítésében vannak részletezve. A DSVM-rendszerkép megkönnyíti az adatelemzés percek alatt történő megkezdését anélkül, hogy mindegyik eszközt külön kell telepítenie és konfigurálnia. Ha szükséges, egyszerűen méretezheti a DSVM, és leállíthatja, ha nincs használatban. A DSVM-erőforrás rugalmas és költséghatékony.

Az ebben az útmutatóban bemutatott adatelemzési feladatok követik a [Mi a csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) lépéseit? A csoportos adatelemzési folyamat az adatelemzés szisztematikus megközelítése, amely segítséget nyújt az adatszakértőknek a csapatok számára az intelligens alkalmazások létrehozásának életciklusa során. Az adatelemzési folyamat is biztosít egy iteratív keretrendszer adatelemzési módszer, amely egy egyéni követheti.

Ebben az útmutatóban elemezzük a [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) adatkészletet. A Spambase a levélszemét vagy a sonka (nem levélszemét) jelölésű e-mailek halmaza. A Spambase az e-mailek tartalmával kapcsolatos statisztikát is tartalmaz. A statisztikáról az útmutató későbbi részében olvashat.

## <a name="prerequisites"></a>Előfeltételek

A Linux-DSVM használatához a következő előfeltételek szükségesek:

* **Azure-előfizetés**. Azure-előfizetés beszerzéséhez tekintse [meg még ma az ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/)című témakört.
* [**Linux Data Science Virtual Machine**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). A virtuális gép kiépítésével kapcsolatos további információkért lásd: [a linuxos Data Science Virtual Machine kiépítése](linux-dsvm-intro.md).
* A [**X2Go**](https://wiki.x2go.org/doku.php) egy nyílt Xfce-munkamenettel telepítette a számítógépre. További információ: [a X2Go-ügyfél telepítése és konfigurálása](linux-dsvm-intro.md#install-and-configure-the-x2go-client).
* Ha gördülékenyebb görgetést kíván, a DSVM Firefox böngészőben kapcsolja be a `gfx.xrender.enabled` `about:config`jelölőt. [További információk](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). `mousewheel.enable_pixel_scrolling` A`False`beállítást is érdemes megfontolni. [További információk](https://support.mozilla.org/questions/981140).
* **Azure Machine learning fiók**. Ha még nem rendelkezik ilyennel, regisztráljon egy új fiókot a [Azure Machine learning kezdőlapján](https://studio.azureml.net/). Kipróbálhatja ingyenesen, hogy segítsen az első lépésekben.

## <a name="download-the-spambase-dataset"></a>Töltse le a spambase adatkészlet

A [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) adatkészlet egy viszonylag kis mennyiségű adat, amely 4 601-példákat tartalmaz. Az adatkészlet kényelmes méretet jelent a DSVM egyes főbb funkcióinak bemutatására, mivel az erőforrásokra vonatkozó követelmények szerények maradnak.

> [!NOTE]
> Ez az útmutató egy D2 v2 méretű Linux DSVM (CentOS-kiadás) készült. Az ebben a bemutatóban bemutatott eljárások elvégzéséhez használhatja ezt a méretet DSVM.

Ha több tárolóhelyre van szüksége, további lemezeket hozhat létre, és csatolhatja azokat a DSVM. A lemezek állandó Azure Storage-t használnak, így az adatmegőrzés akkor is megmarad, ha a kiszolgálót átméretezés vagy leállítás miatt újra kiépítik. Lemez hozzáadásához és a DSVM való csatlakoztatásához hajtsa végre a [lemez hozzáadása Linux rendszerű virtuális géphez](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című témakör lépéseit. A lemezek hozzáadásának lépései az Azure CLI-t használják, amely már telepítve van a DSVM. A lépéseket teljes mértékben magáról a DSVM hajthatja végre. A tárterület növelésének másik lehetősége a [Azure Files](../../storage/files/storage-how-to-use-files-linux.md)használata.

Az adatletöltéshez nyisson meg egy terminál ablakot, majd futtassa a következő parancsot:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

A letöltött fájlnak nincs fejlécsora. Hozzon létre egy másik fájlt, amelynek van fejléce. Hozzon létre egy fájlt a megfelelő fejlécek a következő parancs futtatásával:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Ezután fűzze össze a két fájlt:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Az adatkészlet többféle statisztikai adattípussal rendelkezik az egyes e-mailekhez:

* Az olyan oszlopok, mint a **Word\_freq\_* Word*** az e-mailben szereplő szavakszázalékos arányát jelölik. Ha például a **Word\_freq\_make** értéke **1**, akkor az e-mailben szereplő összes szó 1%-a.
* Oszlopok, például a **char\_freq\_* karakter*** az e-mailben szereplő összes karakter százalékos arányát jelzi.
* **tőke\_futtatása\_hossza\_leghosszabb** a leghosszabb hosszúsága nagybetűk sorozata.
* **tőke\_futtatása\_hossza\_átlagos** átlagos hosszúsága nagybetűk összes sorrendje.
* **tőke\_futtatása\_hossza\_teljes** teljes hosszúsága nagybetűk összes sorrendje.
* **Levélszemét** azt jelzi, hogy az e-mailben tekintették levélszemét, vagy nem (1 = levélszemét, 0 = nem levélszemét).

## <a name="explore-the-dataset-by-using-r-open"></a>Az adatkészlet megismerése az R Open használatával

Vizsgáljuk meg az adatok áttekintését, és végezzünk el néhány alapszintű gépi tanulást az R használatával. A DSVM a [Microsoft R Open](https://mran.revolutionanalytics.com/open/) előre telepítve van. Az előtelepített R-verzióban a többszálas matematikai kódtárak jobb teljesítményt nyújtanak, mint az egyszálas verziók. Az R Open a megismételhetőséget is biztosítja a CRAN Package adattár pillanatképének használatával.

Az útmutatóban használt kódrészletek példányainak beszerzéséhez használja a git-t az Azure-Machine-learning-adatelemzési tárház klónozásához. A git előre telepítve van a DSVM. A git parancssorában futtassa a következőt:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Nyisson meg egy terminál ablakot, és indítson el egy új R-munkamenetet az R interaktív konzolon. Használhatja a RStudio is, amely előre telepítve van a DSVM.

Az adatimportálás és a környezet beállítása:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Minden oszlop összefoglaló statisztikája megtekintése:

    summary(data)

Az adatok különböző megjelenítéséhez:

    str(data)

Ez a nézet az egyes változók típusát és az adatkészlet első néhány értékét jeleníti meg.

A **levélszemét** oszlop olvasta egész számként, de ez valójában egy kategorikus változó (vagy tényező). A típus megadása:

    data$spam <- as.factor(data$spam)

A feltárt elemzések elvégzéséhez használja a [ggplot2](https://ggplot2.tidyverse.org/) csomagot, amely egy népszerű, az R-hez készült, a DSVM előre telepített gráf-könyvtár. A korábban megjelenő összegző adatok alapján a felkiáltójelek gyakoriságára vonatkozó összefoglaló statisztikák szerepelnek. A következő parancsok futtatásával ábrázoljuk ezeket a gyakoriságokat:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Mivel a nulla sáv elferdíti a mintaterületet, töröljük a következőket:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Az 1. számú, érdekesnek tűnik. Nézzük meg, hogy csak az alábbi adatértékek szerepelnek:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ezután bontsa ki a spam és a Ham:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Ezeknek a példáknak a segítségével hasonló mintaterületeket készíthet, és más oszlopokban is megismerheti az adatelemzést.

## <a name="train-and-test-a-machine-learning-model"></a>Gépi tanulási modell betanítása és tesztelése

Több gépi tanulási modellt is betanítunk, hogy az adatkészletben lévő e-maileket a levélszemét vagy a sonka segítségével osztályozzák. Ebben a szakaszban egy döntési famodellt és egy véletlenszerű erdő modellt fogunk betanítani. Ezt követően teszteljük az előrejelzések pontosságát.

> [!NOTE]
> A következő kódban használt *rpart* -csomag (rekurzív particionálás és regressziós fák) már telepítve van a DSVM.

Először ossza fel az adatkészletet a képzési készletekbe és a tesztelési készletekbe:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Ezután hozzon létre egy döntési fát az e-mailek besorolásához:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Az eredmény a következő:

![A létrehozott döntési fa diagramja](./media/linux-dsvm-walkthrough/decision-tree.png)

Annak megállapításához, hogy arról, hogy az a gyakorlókészlethez hajt végre, a következő kód használatával:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Annak megállapítása, hogy arról, hogy a teszt esetén hajtja végre:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Próbáljuk meg még egy véletlenszerű erdőmodell. A véletlenszerű erdők számos döntési fát vezetnek be, és olyan osztályt mutatnak be, amely az összes egyéni döntési fában lévő besorolások módját képezi. Nagyobb teljesítményű gépi tanulási megközelítést biztosítanak, mivel a döntési fa modelljének overfit egy képzési adathalmazt.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio"></a>Modell üzembe helyezése Azure Machine Learning Studio

A [Azure Machine learning Studio](https://studio.azureml.net/) egy felhőalapú szolgáltatás, amely megkönnyíti a prediktív elemzési modellek elkészítését és üzembe helyezését. A Azure Machine Learning Studio egy szép funkciója, hogy bármely R-funkciót webszolgáltatásként tehet közzé. A Azure Machine Learning Studio R-csomag megkönnyíti az üzembe helyezést az R-munkamenetből, közvetlenül a DSVM.

Az előző szakaszban szereplő döntési fakód üzembe helyezéséhez jelentkezzen be Azure Machine Learning Studioba. A munkaterület-Azonosítót és a egy engedélyezési jogkivonatot jelentkezzen be van szüksége. Ezeknek az értékeknek a megkereséséhez és a Azure Machine Learning változók inicializálásához hajtsa végre a következő lépéseket:

1. A bal oldali menüben válassza a **Beállítások**lehetőséget. Jegyezze fel a **munkaterület azonosítójának**értékét.

   ![A Azure Machine Learning Studio munkaterület azonosítója](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Válassza ki az **engedélyezési** jogkivonatok lapot. Jegyezze fel az **elsődleges engedélyezési jogkivonat**értékét.

   ![A Azure Machine Learning Studio elsődleges engedélyezési jogkivonat](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Töltse be a **AzureML** csomagot, majd állítsa be a változók értékeit a token és a munkaterület azonosítójával az R-munkamenetben a DSVM:

        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Hozzunk egyszerűsíteni a modellt, hogy ez a bemutató könnyebben. Válassza ki a három változót a gyökérhez legközelebb lévő döntési fában, és hozzon létre egy új fát úgy, hogy csak a három változót használja:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. Előrejelzési függvény, amely az funkciók fogadja bemenetként, és adja vissza az előre jelzett értékek kell:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }


1. Tegye közzé a **predictSpam** függvényt a AzureML a **publishWebService** függvény használatával:

        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Ez a függvény a **predictSpam** függvényt veszi fel, létrehoz egy **spamWebService** nevű webszolgáltatást, amely megadott bemenetekkel és kimenetekkel rendelkezik, majd az új végpont adatait adja vissza.

    Ezzel a paranccsal megtekintheti a legújabb közzétett webszolgáltatás részleteit, beleértve az API-végpontját és a hozzáférési kulcsokat:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Próbálja ki az első 10 sort a teszt beállítása:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Mélyreható tanulási útmutatók és útmutatók

A keretrendszeren alapuló minták mellett átfogó útmutatók is elérhetők. Ezek az útmutatók segítségével gyorsan elindíthatja a tartományokban, mint például a lemezkép és a szöveg/language understanding deep learning-alkalmazások fejlesztését.

- [Neurális hálózatok futtatása különböző keretrendszerek között](https://github.com/ilkarman/DeepLearningFrameworks): Átfogó útmutató, amely bemutatja, hogyan telepítheti át a kódokat egyik keretrendszerről a másikra. Azt is bemutatja, hogyan lehet összehasonlítani a modelleket és a futásidejű teljesítményt a keretrendszerek között. 

- [Útmutató a képeken belüli termékek észlelésére szolgáló teljes körű megoldás](https://github.com/Azure/cortana-intelligence-product-detection-from-images)létrehozásához: A képészlelés olyan módszer, amellyel objektumokat lehet megkeresni és osztályozni a képeken belül. A technológiának lehetősége van arra, hogy hatalmas előnyökkel jutalmazza a sok valós üzleti tartományát. A kiskereskedők például használhatja ezt a módszert meghatározni, melyik terméket olyan rendelkezik felülettől a kereskedelmi. Ez az információ viszonzásul segíti a Termékleltár kezelése tárolók. 

- [Mély tanulási hang](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Ebből az oktatóanyagból megtudhatja, hogyan taníthat mély tanulási modellt a [városi hangok](https://urbansounddataset.weebly.com/)adatkészletében a Hangesemények észlelésére. Az oktatóanyag áttekintést nyújt a hangadatokkal való munkavégzésről.

- [Szöveges dokumentumok besorolása](https://github.com/anargyri/lstm_han): Ez az útmutató bemutatja, hogyan hozhat létre és taníthat két különböző neurális hálózati architektúrát: Hierarchikus figyelmet igényel a hálózat és a hosszú rövid távú memória (LSTM). Ezeket a Neurális hálózatokat a deep learninghez a Keras API használatával szöveges dokumentumok besorolását. A kerasz a legelterjedtebb mély tanulási keretrendszerek közül három: Microsoft Cognitive Toolkit, TensorFlow és theano.

## <a name="other-tools"></a>Egyéb eszközök

A többi szakaszban bemutatjuk, hogyan használhatja a Linux DSVM telepített eszközöket. A következő eszközöket tárgyaljuk:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL és mókus SQL
* Az SQL Server Data Warehouse

### <a name="xgboost"></a>XGBoost

A [XGBoost](https://xgboost.readthedocs.org/en/latest/) gyorsan és precízen növelt faszerkezetes megvalósítást biztosít.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

A XGBoost Pythonból vagy parancssorból is hívható.

### <a name="python"></a>Python

Python-fejlesztés esetén a 3,5-es és a 2,7-es anaconda Python-disztribúció a DSVM van telepítve.

> [!NOTE]
> Az anaconda eloszlása magában foglalja a [Conda](https://conda.pydata.org/docs/index.html). A Conda használatával olyan egyéni Python-környezeteket hozhat létre, amelyek különböző verziókkal vagy csomagokkal vannak telepítve.

Ismerkedjen meg néhány spambase adatkészlettel, és osztályozza az e-maileket a Scikit támogató vektoros gépekkel:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Az előrejelzéseket:

    clf.predict(X.ix[0:20, :])

Egy Azure Machine Learning-végpont közzétételének bemutatásához hozzon egy alapszintű modellt. A korábban az R-modell közzétételekor használt három változót fogjuk használni:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

A modell közzététele Azure Machine Learning:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)


> [!NOTE]
> Ez a beállítás csak a Python 2,7 esetében érhető el. A Python 3,5-es verzióban még nem támogatott. A futtatáshoz használja a **/anaconda/bin/python2.7**.

### <a name="jupyterhub"></a>JupyterHub

A DSVM anaconda-eloszlása egy Jupyter Notebook, egy többplatformos környezettel rendelkezik a Python, az R vagy a Julia-kód és-elemzés megosztásához. A Jupyter Notebook a JupyterHub keresztül érhető el. A bejelentkezéshez használja a helyi Linux-felhasználónevet és-jelszót a\<https://DSVM DNS-neve vagy\>IP-címe: 8000/. A JupyterHub összes konfigurációs fájlja megtalálható a/etc/jupyterhub.

> [!NOTE]
> Ha a Python Package Managert (a `pip` paranccsal) szeretné használni a jelenlegi kernel egyik Jupyter Notebookjában, használja ezt a parancsot a Code (kód) cellában:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Ha a Conda-telepítőt (a `conda` parancs használatával) szeretné használni az aktuális kernel egy Jupyter notebook, akkor ezt a parancsot kell használnia a kód cellájában:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Több minta jegyzetfüzet már telepítve van a DSVM:

* Python-jegyzetfüzetek – példa:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Példa R-jegyzetfüzetre:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> A Júlia nyelve a Linux DSVM parancssorában is elérhető.

### <a name="rattle"></a>Rattle

[Csörgő](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *a*analitikus *t*OOL *t*o *L*keres *E*asily) egy grafikus R-eszköz az adatbányászathoz. A Rattle olyan intuitív kezelőfelülettel rendelkezik, amely megkönnyíti az adatterhelést, az elemzést és az átalakítást, valamint a modellek kiépítését és értékelését. [Csörgő Az R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) adatbányászati grafikus felhasználói felülete egy olyan bemutatót biztosít, amely bemutatja a csörgő funkcióit.

Telepítse és indítsa el a csörgőt a következő parancsok futtatásával:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Nem kell telepítenie a csörgőt a DSVM. Előfordulhat azonban, hogy a rendszer megkéri a további csomagok telepítését a csörgő megnyitásakor.

Rattle egy lapon-alapú felületet használja. A lapok többsége megfelel a [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)lépéseinek, például az adat betöltésének vagy az adatfelderítésnek. Az adatelemzési folyamat folyamatok balról jobbra a lapfülekre. Az utolsó lapon a csörgő által futtatott R-parancsok naplója szerepel.

Betölteni, és az adatkészlet konfigurálása:

1. A fájl betöltéséhez válassza az adatlapot.
1. Válassza ki a **fájlnév**melletti választót, majd válassza az **spambaseHeaders.** -adatelemet.
1. A fájl betöltése. Válassza a **végrehajtás**lehetőséget. Az egyes oszlopok összegzését kell látnia, beleértve az azonosított adattípust is. legyen szó bemenetről, célhoz vagy más típusú változóról; és az egyedi értékek száma.
1. Rattle tartozik helyesen azonosítani a **levélszemét** oszlop célként. Válassza ki a **Levélszemét** oszlopot, majd állítsa a **Target adattípus** értéket **Categoric**értékre.

Az adatok megismerése:

1. Válassza ki a **Intéző** fülre.
1. Ha szeretné megtekinteni a változó típusokkal és néhány összefoglaló statisztikával kapcsolatos információkat, válassza az **Összefoglalás** > **végrehajtás**lehetőséget.
1. Ha meg szeretné tekinteni az egyes változókkal kapcsolatos egyéb statisztikai adatokat, válassza a további lehetőségek, például a **Leírás** vagy az **alapvető**beállítások lehetőséget.

A **Tallózás** lapon éleslátó ábrákat is létrehozhat. Az adatok hisztogram ábrázol:

1. Válassza ki **Disztribúciók**.
1. A **word_freq_remove** és a **word_freq_you**beállításnál válassza a **hisztogram**lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget. Mindkét sűrűséget egyetlen Graph-ablakban kell látnia, ahol egyértelmű, hogy a szó sokkal gyakrabban jelenik meg az e-mailekben, mint az _Eltávolítás_.

A **korrelációs** mintaterületek is érdekesek. Egy mintaterület létrehozásához:

1. A **Típus mezőben**válassza a **korreláció**lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget.
1. Rattle figyelmeztetést jelenít meg, hogy azt javasolja, legfeljebb 40 változókat. Válassza ki **Igen** megtekintéséhez az ábrázolást.

Vannak érdekes összefüggések, amelyek a következők: a _technológia_ szorosan összefügg a _HP_ -vel ésa laborokkal, például. Emellett a _650_ -as rendszer is szorosan összefügg, mivel az adatkészletek adományozóinak körzetszáma 650.

A szavak közötti korrelációk numerikus értékei a **Tallózás** ablakban érhetők el. Fontos megjegyezni, hogy például a _technológia_ negatívan összefügg a _saját és a_ _pénzével_.

Az adatkészlet kezelni néhány olyan gyakori problémát rattle alakíthatja át. Például átméretezheti a szolgáltatásokat, kiszámíthatja a hiányzó értékeket, kezelheti a kiugró elemeket, és eltávolíthatja a hiányzó adatokat tartalmazó változókat vagy megjegyzéseket. A csörgő a megfigyelések és változók közötti társítási szabályokat is képes azonosítani. Ezek a lapok nem szerepelnek ebben a bevezető bemutatóban.

A csörgő a fürt elemzését is képes futtatni. Nézzük kizárni néhány funkciót kínál, amellyel a kimeneti könnyebben olvasható. Az **adatok** lapon válassza a **figyelmen kívül hagyása** az egyes változók mellett jelölőnégyzetet az alábbi 10 elem kivételével:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* Levélszemét

Térjen vissza a **fürt** lapra. Válassza a **KMeans**lehetőséget, majd állítsa a **fürtök számát** **4**-re. Válassza a **Végrehajtás** lehetőséget. Az eredmények jelennek meg a kimeneti ablakban. Az egyik fürt a _George_ és a _HP_nagy gyakorisággal rendelkezik, és valószínűleg egy legitim üzleti e-mail-cím.

Alapszintű döntés faszerkezetű gépi tanulási modell létrehozása:

1. Válassza ki a **modell** lap
1. A **típushoz**válassza a **fa**elemet.
1. Válassza ki **Execute** szöveg űrlap a kimeneti ablakban a fa megjelenítéséhez.
1. Válassza ki a **rajzolása** gombra egy grafikus verzió megtekintéséhez. A döntési fa hasonlít a korábban a rpart használatával beszerzett fához.

A Rattle hasznos funkciója, hogy több gépi tanulási módszert is futtathat, és gyorsan kiértékelheti azokat. A lépések a következők:

1. A **Típus mezőben**válassza az **összes**lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget.
1. Ha a csörgő befejezi a futást, kiválaszthat bármilyen **típusú** értéket, például a **SVM**, és megtekintheti az eredményeket.
1. Az ellenőrzési csoport modelljeinek teljesítményét az **értékelés** lapon is összehasonlíthatja. Ha például a **hiba mátrix** kijelölés bemutatja, a keveredési mátrix, általános hiba, és az egyes átlagolt osztály hiba ellenőrzés esetén. Emellett a ROC-görbék, az érzékenységi elemzések és más típusú modellek kiértékelései is megtekinthetők.

Amikor elkészült a modellek létrehozásával, a **napló** lapon megtekintheti a csörgő által a munkamenet során futtatott R-kódot. Kiválaszthatja a **exportálása** gombra kattintva mentse azt.

> [!NOTE]
> A csörgő jelenlegi kiadása hibát tartalmaz. Ha módosítani szeretné a parancsfájlt, vagy a használatával szeretné később megismételni a lépéseket, **#** be kell szúrnia egy karaktert a napló exportálása előtt.. *.* a napló szövegében.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL és mókus SQL

A DSVM együttműködik a PostgreSQL telepítése. PostgreSQL egy olyan kifinomult, nyílt forráskódú relációs adatbázis. Ebben a szakaszban bemutatjuk, hogyan tölthető be a spambase adatkészlet a PostgreSQL-be, majd hogyan kérdezhető le.

Az adatok betöltése előtt engedélyeznie kell a jelszó-hitelesítést a localhost-ból. Egy parancssorból futtassa az alábbi parancsot:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

A lap alsó részén található a konfigurációs fájl, amely részletes információkat talál az engedélyezett kapcsolatok több sort a következők:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Módosítsa az **IPv4 helyi kapcsolatok** vonalat úgy, hogy az **MD5** -et használja az **ident**helyett, így bejelentkezhet a Felhasználónév és a jelszó használatával:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Ezután indítsa újra a PostgreSQL szolgáltatást:

    sudo systemctl restart postgresql

A *psql* (a PostgreSQL interaktív terminálja) elindításához a beépített postgres-felhasználóként futtassa ezt a parancsot:

    sudo -u postgres psql

Hozzon létre egy új felhasználói fiókot a bejelentkezéshez használt Linux-fiók felhasználónevével. Jelszó létrehozása:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Jelentkezzen be a psql:

    psql

Importálja az új adatbázisba:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Most Ismerkedjen meg az adataival, és futtasson néhány lekérdezést a mókus SQL használatával, amely egy grafikus eszköz, amellyel a JDBC-illesztőn keresztül használhatja az adatbázisokat.

Első lépésként nyissa meg a mókus SQL **alkalmazást az alkalmazások** menüben. Az illesztőprogram telepítéséhez:

1. Válassza a **Windows** > -**illesztőprogramok megtekintése**lehetőséget.
1. Kattintson a jobb gombbal a **PostgreSQL** elemre, és válassza az **illesztőprogram módosítása**lehetőséget.
1. Válassza a **további osztály elérési útja** > **Hozzáadás**lehetőséget.
1. A **fájlnév**mezőben adja meg a **/usr/share/Java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar**nevet.
1. Válassza az **Open** (Megnyitás) elemet.
1. Válassza az **illesztőprogramok listázása**lehetőséget. Az **Osztálynév**mezőben válassza a **org. PostgreSQL. Driver**elemet, majd kattintson **az OK gombra**.

A kapcsolat a helyi kiszolgáló beállítása:

1. Válassza a **Windows** > **View aliasok lehetőséget.**
1. Az új **+** alias létrehozásához kattintson a gombra. Az új alias neve mezőbe írja be a **Levélszemét-adatbázist**. 
1. Az **illesztőprogram**esetében válassza a **PostgreSQL**lehetőséget.
1. Az URL-Címének beállítása **jdbc:postgresql://localhost/spam**.
1. Adja meg a felhasználónevét és a jelszavát.
1. Kattintson az **OK** gombra.
1. Megnyitásához a **kapcsolat** ablakban kattintson duplán a **levélszemét adatbázis** alias.
1. Kattintson a **Csatlakozás** gombra.

Néhány lekérdezés futtatása:

1. Válassza ki a **SQL** fülre.
1. Az **SQL** lap tetején található lekérdezés mezőben adjon meg egy alapszintű lekérdezést, `SELECT * from data;`például:.
1. A lekérdezés futtatásához nyomja le a CTRL + ENTER billentyűkombinációt. Alapértelmezés szerint a mókus SQL a lekérdezés első 100 sorát adja vissza.

Az adatelemzéshez számos további lekérdezés is futtatható. Például hogyan történik a gyakoriságát, a word *győződjön meg arról,* eltérnek a levélszemét és sonka?

    SELECT avg(word_freq_make), spam from data group by spam;

Vagy milyen jellemzőkkel rendelkezik az e-mailek gyakran tartalmaznak *3D*-ket?

    SELECT * from data order by word_freq_3d desc;

A legtöbb olyan e-mail-cím, amelynek a *3D-s* előfordulása látszólag levélszemét. Ezek az információk hasznosak lehetnek az e-mailek besorolására szolgáló prediktív modell létrehozásához.

Ha a PostgreSQL-adatbázisban tárolt adataival szeretne gépi tanulást végezni, érdemes lehet [MADlib](https://madlib.incubator.apache.org/)használni.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

A Azure SQL Data Warehouse egy felhőalapú, kibővíthető adatbázis, amely nagy mennyiségű, a kapcsolatok és a nem rokon adatmennyiséget képes feldolgozni. További információkért lásd: [Mi az Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Kapcsolódás az adattárházhoz, és a tábla létrehozásához futtassa a következő parancsot a parancssorba:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

A Sqlcmd parancssorban futtassa a következő parancsot:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Másolja az adatfájlokat a BCP használatával:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> A letöltött fájl a Windows stílusú sorok végződéseit tartalmazza. A BCP eszköz a Unix stílusú sorok befejezését várja. A BCP megadásához használja az-r jelzőt.

Ezt követően a lekérdezés a Sqlcmd használatával:

    select top 10 spam, char_freq_dollar from spam;
    GO

A mókus SQL használatával is lekérdezheti. Kövesse a PostgreSQL-hez hasonló lépéseket a SQL Server JDBC-illesztő használatával. A JDBC-illesztő a/usr/share/Java/jdbcdrivers/sqljdbc42.jar mappában található.

## <a name="next-steps"></a>További lépések

Az Azure adatelemzési folyamatát alkotó feladatokkal kapcsolatos cikkek áttekintését itt találja: csoportos adatelemzési [folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

A csoportos adatelemzési folyamat lépéseit bemutató, részletes útmutatót a [csoportos](../team-data-science-process/walkthroughs.md)adatelemzési folyamatról szóló útmutatóban talál. A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőalapú és helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására is.
