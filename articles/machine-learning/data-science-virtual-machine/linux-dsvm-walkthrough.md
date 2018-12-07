---
title: Az adatelemzés és a Linux rendszerű adatelemző virtuális gép az Azure-ban |} A Microsoft Docs
description: Hogyan végezheti el számos gyakori adatelemzési feladatok a Linux rendszerű adatelemző virtuális gép.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: gokuma
ms.openlocfilehash: 5c590b8df2eeb8859a6b78f4d719661c8b542ebe
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000269"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Adatelemzés a egy Linux rendszerű adatelemző virtuális gép az Azure-ban
Ez az útmutató bemutatja, hogyan hajthat végre számos gyakori adatelemzési feladatok a Linux rendszerű adatelemző virtuális gép. A Linux rendszerű adatelemzési virtuális gép (DSVM), amely előre telepített gyakran használt adatok elemzési és gépi tanulási eszközök gyűjteményét az Azure-ban elérhető virtuálisgép-lemezkép. A kulcs szoftverösszetevőket esethez vannak a [a Linux rendszerű adatelemző virtuális gép kiépítése](linux-dsvm-intro.md) témakör. A Virtuálisgép-lemezkép megkönnyíti az első lépések adatelemzésre percben, anélkül, hogy telepítse és konfigurálja az egyes eszközökről külön-külön kellene. Egyszerűen vertikális felskálázás a virtuális Gépet, ha szükséges, és állítsa le, amikor nincs használatban. Ehhez az erőforráshoz így rugalmas és költséghatékony is.

Az ebben az útmutatóban bemutatott adatelemzési feladatokat kövesse az ismertetett lépéseket a [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview). Ezt a folyamatot, amelyek lehetővé teszik, hogy hatékonyan intelligens alkalmazások életciklusa az adatelemzés szisztematikus módszert biztosít. Az adatelemzési folyamat is biztosít egy iteratív keretrendszer adatelemzési módszer, amely egy egyéni követheti.

Elemezzük a [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) adatkészlet ebben az útmutatóban. Ez egy olyan sonka (ami azt jelenti, hogy ne legyenek a levélszemét), vagy a levélszemét megjelölt e-mailek és az e-mailek tartalmának néhány statisztikai értéket is tartalmaz. A statisztika tartalmazza a következő, de egy szakasz tárgyalja.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt használhatná egy Linux rendszerű adatelemzési virtuális gépet, az alábbiakkal kell rendelkeznie:

* Egy **Azure-előfizetés**. Ha Ön még nem rendelkezik egy, [már ma létrehozhatja ingyenes Azure-fiók](https://azure.microsoft.com/free/).
* A [ **Linux rendszerű adatelemző virtuális gép**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Ez a virtuális gép kiépítése a további információkért lásd: [a Linux rendszerű adatelemző virtuális gép kiépítése](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) telepítve a számítógépre, és a egy XFCE munkamenet megnyitva. Információ telepítésével és konfigurálásával egy **X2Go ügyfél**, lásd: [telepítése és konfigurálása X2Go ügyfél](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Az egyenletesebb görgethető élményt, váltsa át a gfx.xrender.enabled jelzőt a kapcsolatos: konfiguráció a virtuális gépek FireFox böngésző. [További részletek megtekintéséhez. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Megfontolnia a lekérdezésbetöltés *mousewheel.enable_pixel_scrolling* hamis értékre. [Itt leírt utasításokat.](https://support.mozilla.org/en-US/questions/981140)
* Egy **AzureML fiók**. Ha még nem rendelkezik egy újat, Regisztráljon a [AzureML kezdőlap](https://studio.azureml.net/). Nincs segít megismerkedni egy ingyenes használat szintjét.

## <a name="download-the-spambase-dataset"></a>Töltse le a spambase adatkészlet
A [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) adatkészlet egy viszonylag kis adatkészletet, amely csak 4601 példákat tartalmaz. Ez a bemutatására, hogy az adatelemző virtuális gép, mert a legfontosabb funkcióit tartja az erőforrás-igényű szerény használni egy kényelmes mérete.

> [!NOTE]
> Ez a forgatókönyv a egy D2 v2 méretű Linux rendszerű adatelemző virtuális gép (CentOS-kiadás) lett létrehozva. A DSVM mérete ebben az útmutatóban szereplő eljárások kezelésére képes.
>
>

Ha több tárhely van szüksége, hozzon létre további lemezeket, és csatolja őket a virtuális gép. Ezek a lemezek állandó az Azure storage, használata, így az adatok megmaradjanak, akkor is, ha a kiszolgáló átméretezése miatt van kiépíteni, vagy le van állítva. Adjon hozzá egy lemezt, és csatlakoztassa a virtuális Géphez, hajtsa végre a következő témakör utasításait [lemez hozzáadása Linux rendszerű virtuális gép](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ezeket a lépéseket az Azure parancssori felület (Azure CLI-), amelyre már telepítve van a DSVM használata. Tehát ezekkel az eljárásokkal teheti meg teljesen a virtuális gépre. Egy másik lehetőség a tárhely bővítése érdekében [az Azure files](../../storage/files/storage-how-to-use-files-linux.md).

Töltse le az adatokat, nyisson meg egy terminálablakot, és futtassa a következő parancsot:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

A letöltött fájl nem rendelkezik most hozzon létre egy másik fájlt, amely fejléccel rendelkeznek egy fejléc sorra. Hozzon létre egy fájlt a megfelelő fejlécek a következő parancs futtatásával:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Majd összefűzi a két fájlt, és a parancsot:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Az adatkészlet minden e-mailek számos különböző típusú statisztikák rendelkezik:

* Más hasonló tartalmú oszlopok ***word\_freq\_WORD*** százalékos aránya az e-mailben a szavakat, amelyek jelzik *WORD*. Például ha *word\_freq\_győződjön meg arról,* értéke 1, akkor az e-mailben minden szó 1 % volt *győződjön meg arról,*.
* Más hasonló tartalmú oszlopok ***char\_freq\_CHAR*** százalékos aránya, amelyek az e-mailben összes karakter jelzi *CHAR*.
* ***tőke\_futtatása\_hossza\_leghosszabb*** a leghosszabb hosszúsága nagybetűk sorozata.
* ***tőke\_futtatása\_hossza\_átlagos*** átlagos hosszúsága nagybetűk összes sorrendje.
* ***tőke\_futtatása\_hossza\_teljes*** teljes hosszúsága nagybetűk összes sorrendje.
* ***Levélszemét*** azt jelzi, hogy az e-mailben tekintették levélszemét, vagy nem (1 = levélszemét, 0 = nem levélszemét).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Ismerje meg az adatkészlet, a Microsoft R Open
Nézzük az adatok vizsgálatát, és végezze el néhány alapszintű gépi tanuláshoz az r segítségével Az adatelemző virtuális gép tartalmaz [Microsoft R Open](https://mran.revolutionanalytics.com/open/) előre telepítve van. Ebben a verzióban az R a többszálas matematikai kódtárak egyszálas verziók jobb teljesítményt nyújtanak. A Microsoft R Open is megismételhetőség segítségével biztosítja a csomag CRAN tárból pillanatképét.

Ebben az útmutatóban használt Kódminták másolatot kap, klónozza a **Azure-gép – tanulási-Adattudományi** segítségével git, amely előre telepítve van a virtuális gép a tárházban. A git-parancssorban futtassa:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Nyisson meg egy terminálablakot, és egy új relace jazyka R kezdődik, az R-interaktív konzol, vagy a gépen előre telepítve az RStudio használatához.


Importálja az adatokat, és állítsa be a környezetet, futtassa:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Minden oszlop összefoglaló statisztikája megtekintése:

    summary(data)

Az adatok különböző megjelenítéséhez:

    str(data)

Ez bemutatja, az egyes változók és az első néhány értékeket az adatkészletben.

A *levélszemét* oszlop olvasta egész számként, de ez valójában egy kategorikus változó (vagy tényező). A típus megadása:

    data$spam <- as.factor(data$spam)

Feltáró jellegű elemzést végezni, használja a [ggplot2](http://ggplot2.org/) csomag, az R, amely már telepítve van a virtuális gép egy népszerű adatbejegyzéssel könyvtár. Vegye figyelembe, az összesítő adatok jelennek meg a korábban, az összefoglaló statisztikák rendelkezünk felkiáltójel karakter gyakoriságát. Nézzük megjeleníteni ezeket gyakoriságot itt az alábbi parancsokkal:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Mivel a nulla sáv van eltorzítják a diagram, nézzük távolíthatja el azt:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Nincs fent 1 érdekesnek tűnik, nem triviális sűrűsége. Nézzük meg, csak az adatokat:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ezután ossza levélszemét vs sonka szerint:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Ezekben a példákban kell lehetővé teszi, hogy az általuk tartalmazott adatok az egyéb oszlopok hasonló grafikon.

## <a name="train-and-test-an-ml-model"></a>Taníthat vagy tesztelhet egy gépi Tanulási modellek
Most tekintsük a machine learning-modellek az adatkészlet span vagy sonka tartalmazzon, az e-mailek besorolására néhány betanításához. Azt a döntési fa modell és a egy véletlenszerű erdőmodell ebben a szakaszban, és tesztelje a saját az előrejelzés pontosságát.

> [!NOTE]
> A rpart (rekurzív particionálás és regressziós fák) csomagot használja a következő kód az adatelemző virtuális gép már telepítve van.
>
>

Először nézzük az adatkészlet felosztása tanítási és tesztelési csoportok:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

És hozzon létre egy döntési fa az e-mailek besorolását.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Az eredmény a következő:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

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

Próbáljuk meg még egy véletlenszerű erdőmodell. Véletlenszerű erdők számos döntési fák betanítása, és a egy osztályt, amely a besorolások minden egyes döntési fákra mód a kimeneti. Nagyobb teljesítményű machine learning megközelítés, mivel azokat a döntési fa modell a betanítási adatkészletet overfit megfelelő kijavítja a biztosítanak.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Modell üzembe helyezése az Azure gépi tanulás
[Az Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) egy felhőalapú szolgáltatás, amellyel egyszerűen készíthet és helyezhet üzembe prediktív elemzési modellek. Az AzureML nagyszerű funkcióit egyik, hogy webszolgáltatásként, amely minden olyan R-függvény közzététele. Az AzureML R csomag közvetlenül az R-munkamenetet a dsvm-hez a könnyű teszi az üzembe helyezést.

A döntési fa kód az előző szakaszban üzembe helyezéséhez szeretne bejelentkezni az Azure Machine Learning Studióban. A munkaterület-Azonosítót és a egy engedélyezési jogkivonatot jelentkezzen be van szüksége. Keresse meg ezeket az értékeket, és a velük AzureML változók inicializálása:

Válassza ki **beállítások** a bal oldali menüben. Megjegyzés: a **MUNKATERÜLET-Azonosítót**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Válassza ki **hitelesítési jogkivonatok** az általános menüből, és vegye figyelembe a **elsődleges engedélyezési jogkivonat**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Betöltés a **AzureML** csomag és a fogja beállítani a jogkivonatot, és a munkaterület Azonosítóját és a változók értékeit, R-munkamenetben a dsvm-hez:

    if(!require("AzureML")) install.packages("AzureML")
    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Hozzunk egyszerűsíteni a modellt, hogy ez a bemutató könnyebben. Válassza ki a három változók a legközelebbi a legfelső szintű döntési fa, és hozhat létre egy új fa csak mindhárom változóval használatával:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Előrejelzési függvény, amely az funkciók fogadja bemenetként, és adja vissza az előre jelzett értékek kell:

    predictSpam <- function(newdata) {
      predictDF <- predict(model.rpart, newdata = newdata)
      return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }


Tegyen közzé a predictSpam függvényt az AzureML használatával a **publishWebService** függvény:

    spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)


Ez a függvény a **predictSpam** működik, létrehoz egy webszolgáltatás nevű **spamWebService** meghatározott bemenetek és kimenetek és az új végpont kapcsolatos információkat ad vissza.

A legutóbb közzétett webes szolgáltatás, beleértve az API-végpont a részletek megtekintéséhez és a hozzáférési kulcsok a paranccsal:

    s<-tail(services(ws, name = "spamWebService"), 1)
    ep <- endpoints(ws,s)
    ep

Próbálja ki az első 10 sort a teszt beállítása:

    consume(ep, smallTestSet[1:10, ])


## <a name="use-other-tools-available"></a>Rendelkezésre álló egyéb eszközök
A fennmaradó szakaszok bemutatják, hogyan használhatja az egyes eszközök a Linux rendszerű adatelemző virtuális gép telepítve. A következő tárgyalt eszközök listájában:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL és az Squirrel SQL
* Az SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) olyan eszköz, amely gyors és pontos gyorsított fa valósítja meg.

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

XGBoost is meghívhatja a pythonból vagy a parancssorból.

## <a name="python"></a>Python
Python fejlesztést az Anaconda Python disztribúciók 2.7-es és 3.5-ös telepítve vannak a dsvm-hez.

> [!NOTE]
> Tartalmazza a Anaconda terjesztési [Conda](http://conda.pydata.org/docs/index.html), amelyek segítségével egyéni környezetek létrehozása, amely rendelkezik a különböző verziók, illetve a rajtuk telepített csomagok Pythonhoz készült.
>
>

Most néhány spambase adatkészlet olvasása és az e-mailek besorolására támogatási vektor gépekkel a scikit-ismerje meg:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Az előrejelzéseket:

    clf.predict(X.ix[0:20, :])

AzureML végpontok közzététele megjeleníthető tekintsük egyszerűbb modell három változókat, hogy a közzétételkor a R modell korábban végrehajtott.

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

A modell közzétételére AzureML:

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
> Ez a lehetőség csak a python 2.7-es, és még nem támogatott a 3.5-ös verzióját. Futtassa a **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
A dsvm-hez az Anaconda terjesztési tartalmaz egy Jupyter notebookot, olyan többplatformos környezetben megosztani a kódot Python, R vagy Julia és -elemzés. A Jupyter notebook JupyterHub keresztül érhető el. Bejelentkezés a helyi Linux-felhasználónév és jelszó használatával ***https://\<virtuális gép DNS-nevét vagy IP-cím\>: 8000 /***. Minden konfigurációs fájlt a JupyterHub könyvtárban találhatók **/etc/jupyterhub**.

> [!NOTE]
> A Python Csomagkezelő használandó (keresztül a `pip` parancs) a Jupyter notebook a jelenlegi kernel, az alábbi parancsot a használni kódcellába, például:
```python
   import sys
   ! {sys.executable} -m pip install numpy -y
```
>
>

> [!NOTE]
> A Conda-telepítővel (keresztül a `conda` parancs) a Jupyter notebook a jelenlegi kernel, az alábbi parancsot a használni kódcellába, például:
```python
   import sys
   ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
```
>
>

Több mintafüzetek már telepítve vannak a virtuális gépen:

* Tekintse meg a [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) egy minta Python-jegyzetfüzetet a.
* Lásd: [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) minta **R** notebookot.
* Tekintse meg a [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) egy másik minta **Python** notebookot.

> [!NOTE]
> A Julia nyelvhez érhető el a Linuxos adatelemző virtuális gép a parancssorból.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (az R analitikai eszközt, ismerje meg, egyszerűen) az adatbányászat R egy grafikus eszközt. Rendelkezik egy intuitív felületen, amely megkönnyíti a betöltése, ismerje meg, és alakíthat át adatokat, és hozhat létre és modellek értékeléséhez.  A cikk [Rattle: A Data Mining GUI az R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) biztosít a forgatókönyv azt mutatja be, annak szolgáltatásait.

Telepítse, és indítsa el a Rattle az alábbi parancsokkal:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> A dsvm-hez a telepítés nem szükséges. De Rattle késztethetik, szeretne további csomagokat telepíteni, amikor betölti azt.
>
>

Rattle egy lapon-alapú felületet használja. A lapok többsége felel meg a lépéseket a [adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/), például adatok letöltése – vagy legalábbis azt. Az adatelemzési folyamat folyamatok balról jobbra a lapfülekre. Csak az utolsó lapon Rattle által futtatott parancsok R napló tartalmazza.

Betölteni, és az adatkészlet konfigurálása:

* A fájl betöltése, válassza ki a **adatok** lapot, majd
* Kattintson a Tovább gombra a választó **Filename** válassza **spambaseHeaders.data**.
* A fájl betöltése. Válassza ki **Execute** gombot a felső sorba. Egy összefoglaló az egyes oszlopok, beleértve az azonosított adattípusa, hogy egy bemeneti, cél vagy más típusú változót, és egyedi értékek számának kell megjelennie.
* Rattle tartozik helyesen azonosítani a **levélszemét** oszlop célként. Válassza ki a levélszemét oszlopot, majd állítsa be a **cél adattípus** való **Categoric**.

Az adatok megismerése:

* Válassza ki a **Intéző** fülre.
* Kattintson a **összefoglaló**, majd **Execute**, némi információt az változótípusok és néhány összefoglaló statisztikáit.
* Más típusú statisztikai adatok az egyes változók megtekintéséhez jelölje ki például a többi beállítást **leírási** vagy **alapjai**.

A **Intéző** lapon is lehetővé teszi számos szemléletes grafikon létrehozásához. Az adatok hisztogram ábrázol:

* Válassza ki **Disztribúciók**.
* Ellenőrizze **hisztogram** a **word_freq_remove** és **word_freq_you**.
* Válassza a **Végrehajtás** lehetőséget. Mindkét diagramon ablakban, ahol a szolgáltatás egyértelmű, hogy a szó "," látható sokkal gyakrabban e-mailek, mint az "Eltávolítás" sűrűségű grafikon kell megjelennie.

A korrelációs grafikon is érdekes lesznek. Hozhat létre egyet:

* Válasszon **korrelációs** , a **típus**, majd
* Válassza a **Végrehajtás** lehetőséget.
* Rattle figyelmeztetést jelenít meg, hogy azt javasolja, legfeljebb 40 változókat. Válassza ki **Igen** megtekintéséhez az ábrázolást.

Néhány érdekes talál összefüggéseket merülnek fel: "technológia" szorosan összefüggnek "HP" és "-laborgyakorlatok", például. Azt is szorosan összefüggnek, a "650", mert az adatkészlet adományozókat, a körzetszámot 650.

A numerikus értékek a szavak közötti összefüggéseket keres az Intéző ablakában érhetők el. Vegye figyelembe, ha például, hogy a "technológia" negatív korrelált "a" és "pénzt" érdekes.

Az adatkészlet kezelni néhány olyan gyakori problémát rattle alakíthatja át. Például lehetővé teszi szolgáltatások átméretezése, imputálására a hiányzó értékeket, kezelni a kiugró értékek, és távolítsa el a változók vagy megfigyeléseket hiányzó adatokkal. Rattle is azonosítani tudja megfigyelés és/vagy változók között a társítási szabályok. Ezeken a lapokon nem sorolhatók fel ebben a bevezető bemutatóban.

Rattle is elvégezheti a fürt elemzése. Nézzük kizárni néhány funkciót kínál, amellyel a kimeneti könnyebben olvasható. Az a **adatok** lapra, majd **figyelmen kívül hagyása** mellett a változók ezek tíz olyan elem kivételével:

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

Ezután lépjen vissza a **fürt** lapra, majd **KMeans**, és állítsa be a *a fürtök számának* 4. Ezután **végrehajtása**. Az eredmények jelennek meg a kimeneti ablakban. Egy fürt nagy gyakoriságú "george" és "hp" rendelkezik, és valószínűleg egy valós üzleti e-mail-címet.

Egy egyszerű döntési fa gépi tanulási modell felépítése:

* Válassza ki a **modell** lap
* Válasszon **fa** , a **típus**.
* Válassza ki **Execute** szöveg űrlap a kimeneti ablakban a fa megjelenítéséhez.
* Válassza ki a **rajzolása** gombra egy grafikus verzió megtekintéséhez. Ez meglehetősen hasonlít a fához, hogy a korábban beszerzett *rpart*.

Nagyszerű funkcióját Rattle egyik történő futtatása több machine learning módszert és gyorsan kiértékelheti az őket. A következő eljárás:

* Válasszon **összes** számára a **típusa**.
* Válassza a **Végrehajtás** lehetőséget.
* Annak befejeződése után kattintson bármely egyedi **típus**, pl. **SVM**, és tekintse meg az eredményeket.
* A teljesítmény, a modellek az érvényesítés használatával is összehasonlíthatja a **Evaluate** fülre. Ha például a **hiba mátrix** kijelölés bemutatja, a keveredési mátrix, általános hiba, és az egyes átlagolt osztály hiba ellenőrzés esetén.
* ROC görbék ábrázolása, érzékenységi elemzéseket végezhet, és végezze el a más típusú modell értékeléseket.

Miután végzett a modellek létrehozásához, válassza ki a **Log** fülre kattintva megtekintheti az R-kód futtatásához Rattle a munkamenet során. Kiválaszthatja a **exportálása** gombra kattintva mentse azt.

> [!NOTE]
> Programhiba van Rattle jelenlegi kiadásában. Módosítsa a parancsfájlt, vagy ismételje meg a későbbi használatával, egy # karaktert elé kell beilleszteni *... Ez a napló exportálása * a napló a szövegben.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL és az Squirrel SQL
A DSVM együttműködik a PostgreSQL telepítése. PostgreSQL egy olyan kifinomult, nyílt forráskódú relációs adatbázis. Ez a szakasz bemutatja az levélszemét adatkészletet betöltheti PostgreSQL és majd lekérdezheti azt.

Az adatok betöltése előtt, engedélyeznie kell a localhost jelszó-hitelesítést. A parancssorba:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

A lap alsó részén található a konfigurációs fájl, amely részletes információkat talál az engedélyezett kapcsolatok több sort a következők:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Módosítsa a "Helyi kapcsolatok IPv4" sort kell használni md5 ident, ahelyett hogy lehet bejelentkezni egy felhasználónevet és jelszót:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

És indítsa újra a postgres-szolgáltatást:

    sudo systemctl restart postgresql

Az interaktív terminál beépített postgres felhasználóként, a PostgreSQL-hez psql indításához futtassa a következő parancsot a parancssorba:

    sudo -u postgres psql

Hozzon létre egy új felhasználói fiókot, amely jelenleg a következőként van bejelentkezve, és adjon meg egy jelszót a Linux rendszerű fiók a azonos felhasználónév használata:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Ezután jelentkezzen be a psql-jének a felhasználóként:

    psql

És a egy új adatbázist az adatok importálása:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Most tekintsük feltárhatja az adatait, és futtathat néhány lekérdezést használatával **Squirrel SQL**, egy grafikus eszközt, amely lehetővé teszi az adatbázisok egy JDBC-illesztőprogram használatával kommunikálhat.

Első lépésként nyissa meg az alkalmazások menüben Squirrel SQL. Az illesztőprogram telepítéséhez:

* Válassza ki **Windows**, majd **illesztőprogramok megtekintése**.
* Kattintson a jobb gombbal a **PostgreSQL** válassza **módosítása illesztőprogram**.
* Válassza ki **Extra osztály az elérési út**, majd **hozzáadása**.
* Adja meg ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** számára a **Fájlnév** és
* Válassza az **Open** (Megnyitás) elemet.
* Válassza ki a lista illesztőprogramokat, majd válassza ki **org.postgresql.Driver** a **osztálynév**, és válassza ki **OK**.

A kapcsolat a helyi kiszolgáló beállítása:

* Válassza ki **Windows**, majd **aliasok megjelenítése.**
* Válassza ki a **+** gombra, hogy egy új aliast.
* Nevezze el *levélszemét adatbázis*, válassza a **PostgreSQL** a a **illesztőprogram** listából.
* Az URL-Címének beállítása *jdbc:postgresql://localhost/spam*.
* Adja meg a *felhasználónév* és *jelszó*.
* Kattintson az **OK** gombra.
* Megnyitásához a **kapcsolat** ablakban kattintson duplán a ***levélszemét adatbázis*** alias.
* Kattintson a **Csatlakozás** gombra.

Néhány lekérdezés futtatása:

* Válassza ki a **SQL** fülre.
* Adja meg például egy egyszerű lekérdezést `SELECT * from data;` be a lekérdezés szövegmezőbe az SQL lap tetején.
* Nyomja meg **Ctrl-adja meg a** a futtatáshoz. Alapértelmezés szerint Squirrel SQL a lekérdezés az első 100 sort adja vissza.

Nincsenek számos további lekérdezések az adatok feltárása futtathatja. Például hogyan történik a gyakoriságát, a word *győződjön meg arról,* eltérnek a levélszemét és sonka?

    SELECT avg(word_freq_make), spam from data group by spam;

Mik azok az e-mailek gyakran tartalmaznak jellemzőit vagy *3d*?

    SELECT * from data order by word_freq_3d desc;

A legtöbb e-maileket, amelyek egy nagy előfordulása *3d* vannak kártevőnek fogjuk kéretlen információk küldésére, így előfordulhat, hogy egy hasznos funkció, amellyel az e-mailek besorolására egy prediktív modellt.

Ha szeretne egy PostgreSQL-adatbázisban tárolt adatokat a machine learning alkalmazásához, érdemes [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Az SQL Server Data Warehouse
Az Azure SQL Data Warehouse egy felhőalapú, horizontálisan felskálázható adatbázis, amely nagy mennyiségű relációs és nem relációs adatot képes feldolgozni. További információkért lásd: [Mi az Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Kapcsolódás az adattárházhoz, és a tábla létrehozásához futtassa a következő parancsot a parancssorba:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Ezután az sqlcmd parancssorba:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Adatok másolása a BCP használatával:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> A letöltött fájlban a sorvégződések Windows stílusú, de bcp UNIX stílusú vár, ezért ellenőriznünk kell a megállapítható bcp, ha az - r jelölővel együtt.
>
>

És lekérdezés az Sqlcmd használatával:

    select top 10 spam, char_freq_dollar from spam;
    GO

Squirrel SQL-lel is lekérdezhet. Használatával a Microsoft MSSQL Server JDBC-illesztőprogram, amely megtalálható a PostgreSQL-hez hasonló lépésekkel ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>További lépések
Témakörök, amelyek végigvezetik a feladatokat az Azure-ban az adatelemzési folyamat alkotó áttekintését lásd: [csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview).

Más végpontok közötti forgatókönyvek, amelyek bemutatják, bizonyos forgatókönyvek esetén a csoportos adatelemzési folyamat lépéseit ismertetését lásd: [csoportos adatelemzési folyamat forgatókönyvek](../team-data-science-process/walkthroughs.md). A forgatókönyvek bemutatják, hogyan kombinálhatja a felhőalapú és helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására is.
