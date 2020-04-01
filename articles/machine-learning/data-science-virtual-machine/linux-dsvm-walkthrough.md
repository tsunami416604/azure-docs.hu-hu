---
title: Fedezze fel a Linuxot
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan végezhet el számos gyakori adatelemzési feladatot a Linux Data Science virtuális gép használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: 1d15d53816d916bd28841aae39255685524faa2d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477872"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Adattudomány linuxos adatelemzési virtuális géppel az Azure-ban

Ez a forgatókönyv bemutatja, hogyan végezhet el számos gyakori adatelemzési feladatot a Linux Data Science virtuális gép (DSVM) használatával. A Linux DSVM egy virtuális gépi lemezkép érhető el az Azure-ban, amely előre telepített eszközök gyűjteménye általánosan használt adatelemzési és gépi tanulás. A legfontosabb szoftverösszetevők a [Linux Data Science virtuális gép kiépítésében](linux-dsvm-intro.md)vannak részletezve. A DSVM-lemezkép megkönnyíti az adatelemzés percek alatt végzett elkezdését anélkül, hogy az egyes eszközöket külön-külön kellene telepítenie és konfigurálnia. Könnyedén felskálázhatja a DSVM-et, ha szükséges, és leállíthatja, amikor nincs használatban. A DSVM-erőforrás rugalmas és költséghatékony.

Az adatelemzési feladatok ebben a forgatókönyvben bemutatott kövesse a lépéseket a [Mi a csapat adatelemzési folyamat?](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) A csapatadat-elemzési folyamat az adatelemzés szisztematikus megközelítése, amely segít az adatszakértők csapatainak hatékonyan együttműködni az intelligens alkalmazások létrehozásának életciklusa során. Az adatelemzési folyamat is biztosít egy iteratív keretet az adatelemzés, amely az egyén által követhető.

Ebben a forgatókönyvben elemezzük a [spambázis-adatkészletet.](https://archive.ics.uci.edu/ml/datasets/spambase) A Spambase olyan e-mailek halmaza, amelyek spamvagy sonka (nem spam) jelöléssel vannak ellátva. Spambase is tartalmaz néhány statisztikát a tartalmát az e-maileket. Beszélünk a statisztikák később a walkthrough.

## <a name="prerequisites"></a>Előfeltételek

A Linux DSVM használata előtt a következő előfeltételekkel kell rendelkeznie:

* **Azure-előfizetés**. Azure-előfizetés beszerezése még [ma az ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/)című témakörben.
* [**Linux Data Science virtuális gép**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu). A virtuális gép kiépítéséről a [Linux data science virtuális gép kiépítése](linux-dsvm-intro.md)című témakörben talál.
* [**X2Go**](https://wiki.x2go.org/doku.php) telepítve a számítógépre egy nyitott XFCE session. További információt [az X2Go ügyfél telepítése és konfigurálása](linux-dsvm-intro.md#x2go)című témakörben talál.
* A simább görgetésélmény érdekében a DSVM Firefox böngészőjében `gfx.xrender.enabled` válthat `about:config`a jelzőközött. [További információ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Szintén fontolja `mousewheel.enable_pixel_scrolling` `False`meg a beállítását a beállítására is. [További információ](https://support.mozilla.org/questions/981140).
* **Azure Machine Learning-fiók**. Ha még nem rendelkezik ilyen, regisztráljon egy új fiókot az [Azure Machine Learning kezdőlapján.](https://azure.microsoft.com/free/services/machine-learning//)

## <a name="download-the-spambase-dataset"></a>A spambázis-adatkészlet letöltése

A [spambázis-adatkészlet](https://archive.ics.uci.edu/ml/datasets/spambase) viszonylag kis adathalmaz, amely 4601 példát tartalmaz. Az adatkészlet egy kényelmes méret a DSVM néhány kulcsfontosságú funkciójának bemutatásához, mert az erőforrás-szükségleteket szerényen tartja.

> [!NOTE]
> Ezt a forgatókönyvet egy D2 v2 méretű Linux DSVM (CentOS Edition) használatával hozták létre. A dsvm-t ilyen méretű használatával elvégezheti az ebben a forgatókönyvben bemutatott eljárásokvégrehajtásával.

Ha több tárhelyre van szüksége, létrehozhat további lemezeket, és csatolhatja őket a DSVM-hez. A lemezek állandó Azure-tárolót használnak, így az adataik akkor is megőrződnek, ha a kiszolgáló átméretezés miatt újra kivan építve, vagy leáll. Lemez hozzáadásához és a DSVM-hez való csatlakoztatásához hajtsa végre a [Lemez hozzáadása linuxos virtuális géphez](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)című lépéseit. A lemez hozzáadásának lépései az Azure CLI-t használják, amely már telepítve van a DSVM-en. A lépéseket teljes egészében a DSVM-ből teheti meg. A tárhely növelésének másik lehetősége az [Azure Files használata.](../../storage/files/storage-how-to-use-files-linux.md)

Az adatok letöltéséhez nyisson meg egy terminálablakot, majd futtassa a következő parancsot:

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

A letöltött fájlnak nincs fejlécsora. Hozzunk létre egy másik fájlt, amelynek fejléce van. A parancs futtatásával hozzon létre egy fájlt a megfelelő fejlécekkel:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Ezután fűzze össze a két fájlt:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Az adatkészlet többféle statisztikát is lehet az egyes e-mailekhez:

* Oszlopok, mint **a szó\_freq\_WORD** jelzik a százalékos szavak az e-mail, amelyek megfelelnek a *WORD*. Ha például a **freq\_\_make szó** **1**, akkor az e-mailben lévő összes szó 1% -a *lett.*
* Az olyan oszlopok, mint a **char\_freq\_CHAR,** azt jelzik, hogy az e-mail összes karakterének hány százaléka *KARAKTER*.
* **a\_\_\_leghosszabb nagyszámú nagybetűs** sorozat leghosszabb hossza.
* **a\_\_nagybetűs\_hossz átlaga** az összes nagybetűs sorozat átlagos hossza.
* **a\_\_nagybetűs futási\_hossz teljes** hossza az összes nagybetűs sorozat nak.
* **spam** jelzi, hogy az e-mailt spamnek tekintették-e vagy sem (1 = spam, 0 = nem spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Az adatkészlet felfedezése az R Open használatával

Vizsgáljuk meg az adatokat, és végezzeel el néhány alapvető gépi tanulást az R használatával. A DSVM előre telepített [Microsoft R Open](https://mran.revolutionanalytics.com/open/) rendszerrel érkezik. Az R előtelepített verziójában található többszálas matematikai kódtárak jobb teljesítményt nyújtanak, mint az egyszálas verziók. Az R Open a CRAN csomagtár pillanatképén keresztül is biztosít reprodukálhatóságot.

A forgatókönyvben használt kódminták másolatainak beszerezéséhez használja a git segítségével az Azure-Machine-Learning-Data-Science tárház klónozásához. A Git elő van telepítve a DSVM-en. A git parancssorból futtassa a következőt:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Nyisson meg egy terminálablakot, és indítson új R-munkamenetet az R interaktív konzolon. Használhatja az RStudio-t is, amely elő van telepítve a DSVM-en.

Az adatok importálása és a környezet beállítása:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Az egyes oszlopok ravonatkozó összefoglaló statisztikáinak megtekintése:

    summary(data)

Az adatok eltérő nézete esetén:

    str(data)

Ez a nézet az egyes változók típusát és az adatkészlet első néhány értékét mutatja.

A **levélszemét** oszlop egész számként lett felolvasva, de valójában egy kategorikus változó (vagy tényező). A típus beállítása:

    data$spam <- as.factor(data$spam)

Néhány feltáró elemzéshez használja a [ggplot2](https://ggplot2.tidyverse.org/) csomagot, egy népszerű R grafikus könyvtárat, amely elő van telepítve a DSVM-en. A korábban megjelenített összegző adatok alapján összefoglaló statisztikáink vannak a felkiáltójel karakterének gyakoriságáról. Nézzük meg ezeket a frekvenciákat itt fut a következő parancsokat:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Mivel a nulla sáv ferde a telek, nézzük meg szüntetni:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Van egy nem triviális sűrűség felett 1, hogy érdekesnek tűnik. Nézzük csak, hogy az adatok:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ezután ossza meg a spam versus sonka:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Ezek a példák segíthetnek a hasonló telkek elrajzolásában és a többi oszlop adatainak feltárásában.

## <a name="train-and-test-a-machine-learning-model"></a>Gépi tanulási modell betanítása és tesztelése

Tanítsunk be néhány gépi tanulási modellt, hogy az adatkészletben lévő e-maileket spamet vagy sonkát tartalmazóként osztályozza. Ebben a szakaszban egy döntési fa modellt és egy véletlenszerű erdőmodellt tanítunk be. Ezután teszteljük az előrejelzések pontosságát.

> [!NOTE]
> A következő kódban használt *rpart* (Rekurzív particionálási és regressziós fák) csomag már telepítve van a DSVM-en.

Először ossza fel az adatkészletet betanítási és tesztkészletekre:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Ezután hozzon létre egy döntési fát az e-mailek besorolásához:

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Itt az eredmény:

![A létrehozott döntési fa diagramja](./media/linux-dsvm-walkthrough/decision-tree.png)

Annak meghatározásához, hogy milyen jól teljesít a betanítási készleten, használja a következő kódot:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Annak meghatározásához, hogy milyen jól teljesít a tesztkészleten:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Próbáljunk ki egy véletlenszerű erdőmodellt is. Véletlenszerű erdők vonat számos döntési fák és kimenet egy osztály, amely a mód a besorolások az összes egyes döntési fák. Hatékonyabb gépi tanulási megközelítést biztosítanak, mert korrigálják a döntési famodell túlterhelését a betanítási adatkészletet.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>Modell üzembe helyezése az Azure Machine Learning Studio-ban (klasszikus)

[Az Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net/) egy felhőalapú szolgáltatás, amely megkönnyíti a prediktív elemzési modellek készítését és üzembe helyezését. Az Azure Machine Learning Studio (klasszikus) egy szép funkciója, hogy képes bármilyen R-funkciót webszolgáltatásként közzétenni. Az Azure Machine Learning Studio (klasszikus) R-csomag egyszerűvé teszi az üzembe helyezést, közvetlenül a DSVM R-munkamenetéből.

A döntési fa kódjának üzembe helyezéséhez az előző szakaszból jelentkezzen be az Azure Machine Learning Studio (klasszikus). A bejelentkezéshez szüksége van a munkaterület-azonosítóra és egy engedélyezési jogkivonatra. Ezeknek az értékeknek a megkereséséhez és az Azure Machine Learning-változók inicializálásához hajtsa végre az alábbi lépéseket:

1. A bal oldali menüben válassza a **Beállítások lehetőséget.** Figyelje meg a **WORKSPACE-azonosító**értékét.

   ![Az Azure Machine Learning Studio (klasszikus) munkaterület-azonosítója](./media/linux-dsvm-walkthrough/workspace-id.png)

1. Válassza az **Engedélyezési jogkivonatok** lapot. **Primary Authorization Token**

   ![Az Azure Machine Learning Studio (klasszikus) elsődleges engedélyezési jogkivonata](./media/linux-dsvm-walkthrough/workspace-token.png)
1. Töltse be az **AzureML-csomagot,** majd állítsa be a változók értékeit a tokenés munkaterület-azonosítóval a DSVM R-munkamenetében:

        if(!require("devtools")) install.packages("devtools")
        devtools::install_github("RevolutionAnalytics/AzureML")
        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. Egyszerűsítsük le a modellt, hogy ez a bemutató könnyebben megvalósítható legyen. Válassza ki a gyökérhez legközelebb eső döntési fa három változóját, és hozzon létre egy új fát csak a három változó használatával:

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. Szükségünk van egy előrejelzési függvényre, amely a funkciókat bemenetként veszi fel, és visszaadja az előre jelzett értékeket:

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }

1. Hozzon létre egy settings.json fájlt ehhez a munkaterülethez:

        vim ~/.azureml/settings.json

1. Győződjön meg arról, hogy a következő tartalom van a settings.json fájlba:

         {"workspace":{
           "id": "<workspace-id>",
           "authorization_token": "<authorization-token>",
           "api_endpoint": "https://studioapi.azureml.net",
           "management_endpoint": "https://management.azureml.net"
         }


1. Tegye közzé a **predictSpam** függvényt az AzureML-ben a **publishWebService** függvény használatával:

        ws <- workspace()
        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. Ez a függvény veszi a **predictSpam** függvényt, létrehoz egy **spamWebService** nevű webszolgáltatást, amely definiált bemenetekkel és kimenetekkel rendelkezik, majd információt ad vissza az új végpontról.

    Ezzel a paranccsal megtekintheti a legújabb közzétett webszolgáltatás részleteit, beleértve az API-végpontot és a hozzáférési kulcsokat:

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. Próbálja ki a tesztkészlet első 10 sorában:

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Mélytanulási útmutatók és forgatókönyvek

A keretalapú minták mellett átfogó forgatókönyvek is rendelkezésre állnak. Ezek a forgatókönyvek segítségével elindíthatja a deep learning alkalmazások fejlesztését olyan területeken, mint a kép és a szöveg/nyelv megértése.

- [Neurális hálózatok futtatása különböző keretrendszerek között:](https://github.com/ilkarman/DeepLearningFrameworks)Átfogó forgatókönyv, amely bemutatja, hogyan lehet áttelepíteni a kódot az egyik keretrendszerből a másikba. Azt is bemutatja, hogyan lehet összehasonlítani a modell és a futásidejű teljesítmény keretrendszerek között. 

- [Útmutató egy végponttól végpontig használható megoldás létrehozásához a képeken belüli termékek észlelésére:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)A képfelismerés olyan technika, amely képes megtalálni és osztályozni az objektumokat a képeken belül. A technológia rendelkezik azzal a potenciállal, hogy hatalmas jutalmakat számos valós üzleti területeken. A kiskereskedők például ezzel a technikával határozhatják meg, hogy a vevő melyik terméket vette fel a polcról. Ez az információ pedig segít a termékkészlet kezelésében. 

- [Deep learning for audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Ez a bemutató bemutatja, hogyan kell betanítani egy mély tanulási modellaudio eseményfelismerés a [városi hangok adatkészlet](https://urbansounddataset.weebly.com/). Az oktatóanyag áttekintést nyújt a hangadatok kezeléséről.

- [Szöveges dokumentumok besorolása](https://github.com/anargyri/lstm_han): Ez a forgatókönyv bemutatja, hogyan hozhat létre és taníthat be két különböző neurális hálózati architektúrát: hierarchikus figyelemhálózat és hosszú rövid távú memória (LSTM). Ezek a neurális hálózatok a Keras API-t használják a mély tanuláshoz a szöveges dokumentumok besorolásához. A Keras a három legnépszerűbb mélytanulási keretrendszer elülső része: Microsoft Cognitive Toolkit, TensorFlow és Theano.

## <a name="other-tools"></a>Egyéb eszközök

A többi szakasz bemutatja, hogyan kell használni a Linux DSVM-re telepített eszközök némelyikét. Megbeszéljük ezeket az eszközöket:

* Xgboost
* Python
* JupyterHub
* Rattle
* PostgreSQL és SQuirreL SQL
* SQL Server adattárház

### <a name="xgboost"></a>Xgboost

[Az XGBoost](https://xgboost.readthedocs.org/en/latest/) gyors és pontos famegvalósítást biztosít.

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

Az XGBoost pythonból vagy parancssorból is hívhat.

### <a name="python"></a>Python

Python-fejlesztés, az Anaconda Python disztribúciók 3.5 és 2.7 telepítve vannak a DSVM.

> [!NOTE]
> Az Anaconda disztribúció tartalmazza [a Conda- t.](https://conda.pydata.org/docs/index.html) A Conda segítségével egyéni Python-környezeteket hozhat létre, amelyekben különböző verziók vagy csomagok vannak telepítve.

Olvassuk el a spambázis egyes adatkészleteit, és osztályozzuk az e-maileket a support vector machines-rel a Scikit-learn-ben:

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Ahhoz, hogy előrejelzések:

    clf.predict(X.ix[0:20, :])

Az Azure Machine Learning-végpont közzétételének bemutatásához készítsünk egy alapvető bb modellt. Az R modell korábbi közzétételekor használt három változót fogjuk használni:

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

A modell közzététele az Azure Machine Learningben:

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
> Ez a beállítás csak a Python 2.7 esetén érhető el. A Python 3.5 még nem támogatott. A futtatáshoz használja az **/anaconda/bin/python2.7 kapcsolót.**

### <a name="jupyterhub"></a>JupyterHub

Az Anaconda disztribúció a DSVM jön egy Jupyter notebook, egy platformfüggetlen környezet ben a Python, R vagy Julia kód és elemzés megosztására. A Jupyter notebook a JupyterHubon keresztül érhető el. A helyi Linux-felhasználónévvel és jelszóval\<jelentkezik be https:// DSVM DNS-nevében vagy IP-címén\>:8000/. A JupyterHub összes konfigurációs fájlja megtalálható az /etc/jupyterhub könyvtárban.

> [!NOTE]
> Ha a Python Package Managert (a `pip` parancson keresztül) az aktuális rendszermagban lévő Jupyter notebookból szeretné használni, használja ezt a parancsot a kódcellában:
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Ha a Conda telepítőt `conda` (a parancson keresztül) az aktuális rendszermagban lévő Jupyter notebookból szeretné használni, használja ezt a parancsot egy kódcellában:
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

A DSVM-re már több mintajegyzetfüzet van telepítve:

* Python-jegyzetfüzetek mintája:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* Minta R notebook:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> A Julia nyelv is elérhető a parancssorból a Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) *(R* *A*nalytical T ool *T* *oL*keresni *E*asily) egy grafikus R eszköz adatbányászat. *T* A Rattle intuitív kezelőfelülettel rendelkezik, amely megkönnyíti az adatok betöltését, felfedezését és átalakítását, valamint a modellek létrehozását és értékelését. [Csörgő: Az R adatbányászati grafikus felhasználói felülete](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) egy olyan forgatókönyvet biztosít, amely bemutatja a Rattle funkcióit.

Telepítse és indítsa el a Rattle-t az alábbi parancsok futtatásával:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Nem kell telepítenie a Rattle-t a DSVM-re. A Rattle megnyitásakor azonban további csomagok telepítésére lehet szükség.

A Rattle tab-alapú felületet használ. A legtöbb lap a [csapatadat-elemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)lépéseinek felel meg, például az adatok betöltése vagy az adatok feltárása. Az adatelemzési folyamat balról jobbra halad a lapokközött. Az utolsó lap a Rattle által futtatott R parancsok naplóját tartalmazza.

Az adatkészlet betöltése és konfigurálása:

1. A fájl betöltéséhez válassza az **Adatok** lapot.
1. Válassza a **Fájlnév**melletti választót, majd a **spambaseHeaders.data**elemet.
1. A fájl betöltéséhez. válassza **a Végrehajtás lehetőséget.** Meg kell jelennie az egyes oszlopok összegzésének, beleértve az azonosított adattípust; legyen szó bemenetről, célról vagy más típusú változóról; és az egyedi értékek számát.
1. Rattle helyesen azonosította a **spam** oszlopot, mint a cél. Jelölje ki a **levélszemét** oszlopot, majd állítsa a **céladattípust** **Kategoric**értékre.

Az adatok feltárása:

1. Válassza a **Felfedezés** lapot.
1. A változótípusokkal és néhány összegző statisztikával kapcsolatos információk megtekintéséhez válassza az **Összefoglaló** > **végrehajtás lehetőséget.**
1. Az egyes változókkal kapcsolatos egyéb típusú statisztikák megtekintéséhez válasszon más beállításokat is, **például a Leírás** vagy **az Alapok lehetőséget.**

A **Felfedezés** lapon éleslátó telkeket is létrehozhat. Az adatok hisztogramjának nyomtatása:

1. Válassza **a Felosztások**lehetőséget.
1. **A word_freq_remove** és **word_freq_you**esetén válassza **a Hisztogram**lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget. Mindkét sűrűségdiagramnak egy grafikonablakban kell lennie, ahol egyértelmű, hogy a _szó, amelyet_ sokkal gyakrabban jelenik meg az e-mailekben, mint _az eltávolítás._

A **korrelációs** telkek is érdekesek. Telek létrehozása:

1. A **Típus mezőben**válassza a **Korreláció**lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget.
1. Rattle figyelmeztet, hogy legfeljebb 40 változót javasol. A telek megtekintéséhez válassza az **Igen** lehetőséget.

Van néhány érdekes összefüggések, hogy jöjjön létre: _a technológia_ szorosan összefügg a _HP_ és _a laborok,_ például. Ez is erősen korrelál _a 650,_ mert a körzetszám az adatkészlet adományozók 650.

A szavak közötti korrelációk numerikus értékei a **Felfedezés** ablakban érhetők el. Érdekes megjegyezni, például, hogy a _technológia_ negatívan korrelál _az Ön_ és _a pénz_.

A Rattle átalakíthatja az adatkészletet néhány gyakori probléma kezeléséhez. Például átskáláhatja a funkciókat, imputálhatja a hiányzó értékeket, kezelni tudja a kiugró értékeket, és eltávolíthatja a hiányzó adatokat tartalmazó változókat vagy megfigyeléseket. Rattle is képes azonosítani a megfigyelések és a változók közötti társítási szabályokat. Ezek a lapok nem jelennek meg ebben a bevezető forgatókönyvben.

Rattle is futtathatja a fürt elemzést. Zárjon ki néhány funkciót, hogy a kimenet könnyebben olvasható legyen. Az **Adatok** lapon válassza a **Figyelmen kívül hagyás** lehetőséget az egyes változók mellett, kivéve a 10 elemet:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Visszatérés a **Fürt** lapra. Válassza a **KMeans**lehetőséget, majd állítsa **a Fürtök száma** **4-re.** Válassza a **Végrehajtás** lehetőséget. Az eredmények a kimeneti ablakban jelennek meg. Az egyik klaszter magas gyakorisággal _george_ és _a HP_, és valószínűleg egy jogos üzleti e-mail.

Alapvető döntési fa gépi tanulási modell létrehozása:

1. Válassza a **Modell** lapot,
1. A **Típus mezőben**válassza a **Fa**lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget, ha a fát szöveges formában szeretné megjeleníteni a kimeneti ablakban.
1. Grafikus változat megtekintéséhez kattintson a **Rajz** gombra. A döntési fa hasonlít a fa kaptunk korábban segítségével rpart.

A Rattle hasznos jellemzője, hogy számos gépi tanulási módszert futtathat, és gyorsan kiértékelheti azokat. Itt vannak a lépések:

1. A **Típus mezőben**válassza az **Összes**lehetőséget.
1. Válassza a **Végrehajtás** lehetőséget.
1. Amikor a Rattle befejezi a futást, bármilyen **Típus** értéket kiválaszthat, például **az SVM-et,** és megtekintheti az eredményeket.
1. Az érvényesítési készletben lévő modellek teljesítményét a **Kiértékelés** lapon is összehasonlíthatja. Például a **Hibamátrix** kiválasztása megmutatja a zűrzavarmátrixot, az általános hibát és az átlagos osztályhibát az érvényesítési készlet minden modelljéhez. Roc-görbéket is ábrázolhat, érzékenységelemzést futtathat, és más típusú modellértékeléseket is elvégezhet.

Amikor befejezte a modellek készítését, válassza a **Napló** lapot a Rattle által a munkamenet során futtatott R-kód megtekintéséhez. Az **Exportálás** gombra kattintva mentheti azt.

> [!NOTE]
> A Rattle jelenlegi kiadása hibát tartalmaz. A parancsfájl módosításához vagy későbbi lépések megismétléséhez be **#** kell szúrnia egy karaktert a *napló exportálása ...* elé a napló szövegében.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL és SQuirreL SQL

A DSVM postgreSQL-t telepít. A PostgreSQL egy kifinomult, nyílt forráskódú relációs adatbázis. Ez a szakasz bemutatja, hogyan töltheti be a spambázis-adatkészletet a PostgreSQL-be, majd hogyan kérdezheti le.

Az adatok betöltése előtt engedélyeznie kell a jelszó-hitelesítést a localhost-tól. Egy parancssorból futtassa az alábbi parancsot:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

A konfigurációs fájl alján több sor található, amelyek részletezik az engedélyezett kapcsolatokat:

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Módosítsa az **IPv4 helyi kapcsolatok** vonalát úgy, hogy **az ident**helyett **az md5-öt** használja, így felhasználónévvel és jelszóval tudunk bejelentkezni:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Ezután indítsa újra a PostgreSQL szolgáltatást:

    sudo systemctl restart postgresql

Ha a *psql-t* (a PostgreSQL interaktív terminálját) szeretné elindítani beépített postgres felhasználóként, futtassa a következő parancsot:

    sudo -u postgres psql

Hozzon létre egy új felhasználói fiókot a bejelentkezéshez használt Linux-fiók felhasználónevével. Jelszó létrehozása:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Jelentkezzen be a psql be:

    psql

Az adatok importálása új adatbázisba:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Most nézzük meg az adatokat, és futtasson néhány lekérdezést a SQuirreL SQL, egy grafikus eszköz használatával, amellyel jdbc-illesztőprogramon keresztül kommunikálhat az adatbázisokkal.

A kezdéshez nyissa meg a SQuirreL SQL-t az **Alkalmazások** menüben. Az illesztőprogram beállítása:

1. Válassza a **Windows** > **nézet illesztőprogramjait**.
1. Kattintson a jobb gombbal a **PostgreSQL elemre,** és válassza **az Illesztőprogram módosítása parancsot.**
1. Válassza **az Extra Class Path** > **Add**lehetőséget.
1. A **Fájlnév**mezőbe írja be a **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar értéket.**
1. Válassza az **Open** (Megnyitás) elemet.
1. Válassza **az Illesztőprogramok lista lehetőséget.** **Az Osztálynév mezőbe**válassza az **org.postgresql.Driver**elemet, majd kattintson **az OK gombra.**

A helyi kiszolgálóval való kapcsolat beállítása:

1. Válassza a **Windows** > **nézet aliasait.**
1. Új **+** alias létrehozásához kattintson a gombra. Az új aliasnévhez írja be a **Spam adatbázist.** 
1. **Driver esetén**válassza a **PostgreSQL**lehetőséget.
1. Állítsa be az URL-t **jdbc:postgresql://localhost/spam**.
1. Adja meg a felhasználónevét és a jelszavát.
1. Válassza **az OK gombot.**
1. A **Kapcsolat** ablak megnyitásához kattintson duplán a **Spam adatbázis** aliasára.
1. Kattintson a **Csatlakozás** gombra.

Egyes lekérdezések futtatása:

1. Válassza az **SQL** lapot.
1. Az **SQL** lap tetején található lekérdezési mezőbe írjon `SELECT * from data;`be egy egyszerű lekérdezést, például .
1. A lekérdezés futtatásához nyomja le a Ctrl+Enter billentyűkombinációt. Alapértelmezés szerint a SQuirreL SQL a lekérdezés első 100 sorát adja vissza.

Az adatok feltárásához sokkal több lekérdezést futtathat. Például, hogyan *változik* a szó gyakorisága a spam és a sonka között?

    SELECT avg(word_freq_make), spam from data group by spam;

Vagy, mik a jellemzői az e-mail, amely gyakran tartalmaz *3d?*

    SELECT * from data order by word_freq_3d desc;

A legtöbb e-maileket, amelyek nagy előfordulása *3d* látszólag spam. Ez az információ hasznos lehet egy prediktív modell létrehozásához az e-mailek besorolásához.

Ha a gépi tanulást a PostgreSQL-adatbázisban tárolt adatok használatával szeretné végezni, fontolja meg a [MADlib](https://madlib.incubator.apache.org/)használatát.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

Az Azure SQL Data Warehouse egy felhőalapú, kibővített adatbázis, amely képes feldolgozni a nagy mennyiségű adat, mind a relációs és nem relációs. További információ: [Mi az Azure SQL Data Warehouse?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Az adattárházhoz való csatlakozáshoz és a tábla létrehozásához futtassa a következő parancsot a parancssorból:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Az sqlcmd parancssorból futtassa a következő parancsot:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Másolja az adatokat a bcp használatával:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> A letöltött fájl Windows-stílusú sorvégződéseket tartalmaz. A bcp eszköz Unix-stílusú vonalvégződéseket vár. A bcp üzenetének a -r jelzővel.

Ezt követően lekérdezés sqlcmd használatával:

    select top 10 spam, char_freq_dollar from spam;
    GO

A SQuirreL SQL használatával is lekérdezhet. Kövesse a PostgreSQL-hez hasonló lépéseket az SQL Server JDBC illesztőprogramjával. A JDBC illesztőprogram a /usr/share/java/jdbcdrivers/sqljdbc42.jar mappában található.

## <a name="next-steps"></a>További lépések

Az Azure-beli adatelemzési folyamatot alkotó feladatokat elvezető cikkek áttekintését a [Csapatadat-elemzési folyamat című](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)témakörben találja.

A csapatadat-elemzési folyamat egyes forgatókönyvekhez tartozó lépéseit bemutatandó végpontok forgatókönyveinek leírását a [Csapatadat-elemzési folyamat forgatókönyvei](../team-data-science-process/walkthroughs.md)című témakörben található. A forgatókönyvek azt is bemutatják, hogyan kombinálhatja a felhőbeli és a helyszíni eszközöket és szolgáltatásokat egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához.
