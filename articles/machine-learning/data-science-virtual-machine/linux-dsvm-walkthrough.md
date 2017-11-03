---
title: "A Linux adatok tudományos virtuális gépet az Azure adattudomány |} Microsoft Docs"
description: "Hogyan hajthat végre több közös adatok tudományos feladatokat a a Linux adatok tudományos virtuális Géphez."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev;paulsh
ms.openlocfilehash: 650b11d66f3ca32266b9842af77c909e125b4e4d
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Adattudomány rendelkező Linux adatok tudományos virtuális gépek Azure-on
Ez a forgatókönyv bemutatja, hogyan végezhető több közös adatok tudományos a Linux adatok tudományos virtuális gép. A Linux adatok tudományos virtuális gép (DSVM) érhető el, amely adatelemzés és a gépi tanulás általánosan használt eszközöket együtt telepített Azure virtuálisgép-lemezkép. A kulcs szoftverösszetevőket van felsorolva a [a Linux adatok tudományos virtuális gép kiépítéséhez](linux-dsvm-intro.md) témakör. A Virtuálisgép-lemezkép megkönnyíti az első lépések során adattudomány (percben), anélkül, hogy telepítse és konfigurálja az egyes eszközökről külön-külön kellene. Könnyedén növelheti a virtuális gép, ha szükséges, és állítsa le, ha nincsenek használatban. Ehhez az erőforráshoz, mind a rugalmas és költséghatékony.

Az ebben a forgatókönyvben bemutatott tudományos feladatok kövesse a a [Team adatok tudományos folyamat](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Ez a folyamat, amely lehetővé teszi az adatszakértőkön át a hatékony az intelligens alkalmazások teljes életciklusát keresztül együttműködve megosszanak csoportjai adattudomány rendszeres megközelítését ismerteti. Az tudományos folyamat egy iteratív keretet is biztosít, amely egy adott követhetnek adattudomány.

Azt a elemzése a [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) dataset ebben a forgatókönyvben. Ez egy olyan van megjelölve, vagy a levélszemét, vagy a sonka (ami azt jelenti, amelyek nincsenek levélszemét), e-mailek és az e-mailek tartalmának néhány statisztikai is tartalmaz. A statisztikai adatokat tartalmaz a következő, de egy szakasz tárgyalja.

## <a name="prerequisites"></a>Előfeltételek
Adatok tudományos Linux virtuális gépek használata előtt az alábbiakkal kell rendelkeznie:

* Egy **Azure-előfizetés**. Ha még nem rendelkezik egy, lásd: [ma létrehozása az ingyenes Azure-fiókjával](https://azure.microsoft.com/free/).
* A [ **adattudomány Linux virtuális gép**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). A virtuális gép további információkért lásd: [a Linux adatok tudományos virtuális gép kiépítéséhez](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) telepítve a számítógépre, és egy XFCE munkamenet megnyitása. Telepítésével és konfigurálásával kapcsolatos egy **X2Go ügyfél**, lásd: [telepítése és konfigurálása X2Go ügyfél](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Gördülékenyebb görgethető élmény váltása a gfx.xrender.enabled jelzőt kapcsolatos: virtuális gépek FireFox böngészőben config. [Itt talál. ](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Fontolja meg is való átváltással *mousewheel.enable_pixel_scrolling* False értékre. [Utasításokat itt.](https://support.mozilla.org/en-US/questions/981140)
* Egy **AzureML fiók**. Ha még nem rendelkezik egy újat, Regisztráljon a [AzureML kezdőlap](https://studio.azureml.net/). Van egy ingyenes használati módosítása a következőre segítségére lesz.

## <a name="download-the-spambase-dataset"></a>Töltse le a spambase adatkészlet
A [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) adatkészlet olyan viszonylag kicsi, amely csak 4601 példákat tartalmaz adatokat. Ez akkor használja, ha a bemutatásához, hogy az adatok tudományos virtuális gép, mert a kulcsfontosságú szolgáltatásokat egy részénél tartja az erőforrás-követelmények mérsékelt tetszés szerinti méretét.

> [!NOTE]
> Ez a forgatókönyv a D2 v2 méretű Linux adatok tudományos virtuális gépek hozták létre. A DSVM mérete a Kalauzban leírt eljárások kezelésére képes.
>
>

Ha több tárhelyet igényel, további lemezeket hozhat létre, és csatolja azokat a virtuális Gépet. Ezeknek a lemezeknek állandó Azure tárhelyet használja, így az adatok megmaradjanak, akkor is, ha a kiszolgáló átméretezése miatt újra kell kiépíteni, vagy le van állítva. Adjon hozzá egy lemezt, és csatlakoztassa a virtuális Gépet, kövesse az utasításokat a [vehet fel lemezt a Linux virtuális gép](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Ezeket a lépéseket az Azure parancssori felület (Azure CLI), amelyen már telepítve van a DSVM használja. Így teljesen át a virtuális gépért ezekkel az eljárásokkal végezhető el. Egy másik lehetőség az megnövelni a tárhely [az Azure files](../../storage/files/storage-how-to-use-files-linux.md).

Az adatok letöltése, nyisson meg egy terminálablakot, és futtassa a parancsot:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Most hozzon létre egy másik fájlba, amelyen fejlécet a letöltött fájl nem rendelkezik a fejlécsor. Futtassa ezt a parancsot a megfelelő fejlécekkel-fájl létrehozásához:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Majd összefűzésére együtt a parancs két fájlt:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Az adatkészlet egyes e-mail számos különböző típusú statisztikák rendelkezik:

* Oszlopok, például ***word\_freq\_WORD*** jelzi az e-mailben a szavakat, amelyek *WORD*. Például ha *word\_freq\_ellenőrizze* értéke 1 és 1 %-át az e-mailben szereplő minden szó volt *ellenőrizze*.
* Oszlopok, például ***char\_freq\_CHAR*** , melyeket az e-mailben szereplő összes karakter jelzi *CHAR*.
* ***beruházási\_futtatása\_hossza\_leghosszabb*** nagybetűvel sorozatát leghosszabb hossza.
* ***beruházási\_futtatása\_hossza\_átlagos*** nagybetűvel összes sorozatát átlagos hossza.
* ***beruházási\_futtatása\_hossza\_teljes*** nagybetűvel összes sorozatát teljes hossza.
* ***Levélszemét*** azt jelzi, hogy az e-mailt tekintették levélszemét, vagy nem (1 = levélszemét, 0 = nem levélszemét).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>A Microsoft R nyissa meg az adatkészlet felfedezés
Most az adatok vizsgálatát, és hajtsa végre az r tanulási néhány alapvető gép Az adatok tudományos VM mellékelt [Microsoft R nyitott](https://mran.revolutionanalytics.com/open/) előre telepítve. Az R jelen verziójában a többszálas matematikai könyvtárak egyszálas verziók jobb teljesítményt nyújtanak. Microsoft R nyitott is reprodukálhatósági segítségével biztosítja a CRAN csomag tárház pillanatkép.

Ahhoz, hogy ebben a forgatókönyvben használt mintakódok példányait, klónozza a **Azure-Machine-tanulás-adatok-tudományos** segítségével git, amely előre telepítve van a virtuális Gépet a tárházban. A git parancssorból futtassa:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Nyisson meg egy terminálablakot, és indítson el egy új R munkamenetet az R-interaktív konzol.

> [!NOTE]
> Az alábbi eljárásokat használhatja Rstudióból is. Rstudióból telepítéséhez az adott parancs végrehajtásához:`./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Importálhatja az adatokat, és állítsa be a környezetet, futtassa:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Egyes oszlopok összefoglaló statisztikája megtekintéséhez:

    summary(data)

Az adatok különböző nézet:

    str(data)

Megjelenik az egyes és az első néhány értékeket az adatkészletben.

A *levélszemét* oszlop beolvasott érték, de ténylegesen egy kategorikus változó (vagy tényező). A típus megadása:

    data$spam <- as.factor(data$spam)

Néhány felderítő elemzés teheti a [ggplot2](http://ggplot2.org/) csomag, az R, amely már telepítve van a virtuális gép egy népszerű nyújtó grafikus könyvtár. Vegye figyelembe, jelenik meg a korábban, az összefoglaló adatokból statisztikák tudunk felkiáltójel karakter gyakoriságát. Most ábrázolhatók ezek gyakoriságot itt az alábbi parancsokkal:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

A nulla sávon a rajzolási van döntés, mivel most selejtezni azt:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

1 kereső érdekes fent nem triviális sűrűségű van. Most, hogy az adatok vizsgáljuk meg:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ezután ossza levélszemét vs sonka szerint:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Ezekben a példákban lehetővé kell tennie, hogy más oszlop alapján megismerheti a bennük található adatok hasonló előkészítésére.

## <a name="train-and-test-an-ml-model"></a>Betanítása és tesztelni az ML-modell
Most tegyük a machine learning modellek az adatkészletben, span vagy sonka tartalmazzon, az e-mailek besorolására néhány betanításához. Azt a döntési fa modellek és ebben a szakaszban egy véletlenszerű erdő modell betanítását, és tesztelje a azok az előrejelzés pontosságát.

> [!NOTE]
> Az alábbi kódban használt rpart (rekurzív particionálás és regressziós fák) csomag már telepítve van az adatok tudományos virtuális gépen.
>
>

Első, az adatkészlet most felosztása tanítási és tesztelési beállítása:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

És az e-mailek besorolására a döntési fában.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Az eredmény a következő:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Határozza meg, milyen mértékben a gyakorlókészlethez elvégez, használja a következő kódot:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Annak meghatározásához, hogy milyen mértékben a TesztKészlet a hajtja végre:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Próbáljuk meg is véletlenszerű erdőmodell. Véletlenszerű erdők számos döntési fák betanítása, és kimeneti egy osztály, amely az összes az egyes döntési fák besorolások mód. Nagyobb teljesítményű machine learning-módszer, azok küszöbölje ki a döntési fa modell hajlamos képzési dataset overfit biztosítanak.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>A modell rendszerbe állítása Azure gépi tanulás
[Az Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) egy felhőszolgáltatás, amely segítségével egyszerűen és üzembe prediktív elemzési modellek. Az AzureML töltött funkcióit egyik közzététele az R funkció webszolgáltatásként való képességét. Az AzureML R csomagot közvetlenül a az R munkamenetet a DSVM elvégzéséhez egyszerűen központi telepítés.

A döntési fa kódot a fenti szakaszban leírt központi telepítéséhez kell bejelentkezni az Azure Machine Learning Studio. A munkaterület azonosítója és egy engedélyezési jogkivonatot való bejelentkezéshez szükséges. Ezek az értékek található, és a velük AzureML változók inicializálása:

Válassza ki **beállítások** a bal oldali menüben. Megjegyzés: a **MUNKATERÜLET azonosítója**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Válassza ki **engedélyezési jogkivonatok** az általános menüből, és vegye figyelembe a **elsődleges engedélyezési jogkivonat**.![ 3](./media/linux-dsvm-walkthrough/workspace-token.png)

Betöltési a **AzureML** csomag és utána állítsa be a jogkivonatot, és a munkaterület Azonosítóját a változók értékeit az R-munkamenetben a DSVM meg:

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Most egyszerűsítése ebben a bemutatóban egyszerűbb, hogy a modell. Válassza ki a három változók a döntési fa gyökere legközelebbi, és csak a megadott három változók használata új fa létrehozása:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Igazolnia kell a előrejelző függvényben a szolgáltatások veszi bemenetként, és visszaadja az előre jelzett értékek:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

A predictSpam függvény közzétételére az AzureML használatával a **publishWebService** függvény:

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Ez a függvény a **predictSpam** működik, létrehoz egy nevű webszolgáltatás-bővítmény **spamWebService** a meghatározni a bemenetekhez és kimenetekhez, és az új végpont információt ad vissza.

A közzétett webes szolgáltatás, így az API-végpont részleteinek megtekintése, és hozzáférési kulcsokkal paranccsal:

    spamWebService[[2]]

Próbálja ki az első 10 sor a vizsgálat beállítása:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Rendelkezésre álló eszközök
A fennmaradó részei bemutatják, hogyan használhatja néhány a Linux adatok tudományos virtuális Gépre telepített eszközök. A tárgyalt eszközök listája itt található:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL & Squirrel SQL
* SQL Server-adatraktár

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) olyan eszköz, amely gyors és pontos súlyozott fa valósítja meg.

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

XGBoost is meghívhatja a python vagy a parancssorból végzi.

## <a name="python"></a>Python
A fejlesztési pythonos környezetekben az Anaconda Python azokat a terjesztéseket 2.7 és 3.5-ös telepítve vannak a DSVM.

> [!NOTE]
> A Anaconda terjesztési tartalmaz [Condas](http://conda.pydata.org/docs/index.html), amelyek segítségével hozzon létre egyéni környezeteket, amelyek különböző verziói és/vagy a bennük foglalt telepített csomagok Python.
>
>

Most a spambase adatkészlet egyes olvasása és az e-mailek besorolására scikit a támogatási vektoros gépekkel – ismerje meg:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

A előrejelzéseket készítsen:

    clf.predict(X.ix[0:20, :])

Megjelenítése a közzététele AzureML végpont, most Meggyőződünk egy egyszerűbb modell a három változók, ahogy azt korábban nyitva jelenleg az R-modell.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
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
> Ez a lehetőség csak a python 2.7, és még nem támogatott a 3.5-ös verzióját. Futtassa a **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
A Anaconda terjesztési a DSVM a Jupyter notebook, olyan többplatformos környezetben, a Python, R vagy Ágnes kóddal és elemzési tartalmaz. A Jupyter notebook JupyterHub keresztül érhető el. A helyi Linux-felhasználónév és jelszó használatával bejelentkezik ***https://\<VM DNS-nevét vagy IP-cím\>: 8000 /***. Minden konfigurációs fájlt a JupyterHub könyvtárban találhatók **/etc/jupyterhub**.

Több minta jegyzetfüzetet már telepítve van a virtuális Gépet:

* Tekintse meg a [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) egy minta Python jegyzetfüzet.
* Lásd: [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) egy minta **R** notebookot.
* Tekintse meg a [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) egy másik minta **Python** notebookot.

> [!NOTE]
> Ágnes nyelvét a Linux adatok tudományos virtuális Gépen a parancssorból is érhető el.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (az R analitikai eszköz a további könnyen) az adatbányászathoz grafikus R eszközzel. Rendelkezik egy intuitív felület, amellyel könnyedén betölteni, vizsgálatát, és adatok átalakítása és build és modellek értékeléséhez.  A cikk [Rattle: A Data Mining GUI az R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) biztosít a forgatókönyv azt mutatja be, a szolgáltatásai.

Telepítse, és indítsa el a Rattle az alábbi parancsokkal:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> Telepítés nem szükséges a DSVM. De Rattle késztethetik további csomagok telepítése, ha betölti a.
>
>

Rattle egy lapon-alapú felületet használja. A lapok többsége felel meg a lépéseket a [adatok tudományos folyamat](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), például az adatok betöltése vagy felfedezését. Az tudományos folyamat zajlik balról jobbra a lapfülekre. De az utolsó lap tartalmazza az R-parancsok Rattle futtatásához.

Betölteni, és konfigurálja az adatkészlet:

* Töltse be a fájlt, jelölje be a **adatok** lapra, majd
* Válassza ki a választó melletti **Fájlnév** válassza **spambaseHeaders.data**.
* A fájl betöltése. Válassza ki **Execute** gombok legfelső sorában. Megtekintheti az egyes oszlopok, beleértve az azonosított adattípushoz, hogy bemeneti, cél vagy más típusú változó, és egyedi értékek számának összegzését.
* Rattle helyesen észlelt a **levélszemét** oszlop céljaként. Válassza ki a levélszemét oszlopot, majd állítsa be a **cél adattípus** való **Categoric**.

Ásna az adatokban:

* Válassza ki a **böngészés** fülre.
* Kattintson a **összefoglaló**, majd **Execute**, a változó típusok kapcsolatos információkat, és néhány statisztikák.
* Válassza ki, ha az egyes statisztikája más típusú egyéb beállítások, például a **adja** vagy **alapjai**.

A **böngészés** lapon is lehetővé teszi számos osztályon előkészítésére. Az adatok hisztogram megrajzolásához:

* Válassza ki **Terjesztéseket**.
* Ellenőrizze **hisztogram** a **word_freq_remove** és **word_freq_you**.
* Válassza ki **hajtható végre**. Meg kell jelennie egy grafikonon ablakban, amennyiben egyértelmű, hogy a word "Ön" sokkal gyakran megjelenik az e-mailek, mint az "Eltávolítás" mindkét sűrűség előkészítésére.

A korrelációs előkészítésére érdekes egyaránt. A létrehozáshoz:

* Válasszon **korrelációs** , a **típus**, majd
* Válassza ki **hajtható végre**.
* Rattle figyelmezteti, hogy legfeljebb 40 változók javasol. Válassza ki **Igen** a rajzolási megtekintéséhez.

Van néhány érdekes közti korrelációk elérni: "technológia" erősen visszamenőleges korrelációban állnak "HP" és "labs", például. Azt is erősen visszamenőleges korrelációban állnak, a "650", mert az adatkészlet adók, a körzetszámot 650.

A numerikus érték, a szavak közötti összefüggések az Intéző ablakában érhetők el. Érdemes megjegyzés, például, hogy "technológia" negatívan tartozzanak "a" és "pénzt".

Rattle alakíthatja át a dataset gyakori problémákat kezelésére. Például lehetővé teszi szolgáltatások átméretezése, imputálására a hiányzó értékeket, kiugró kezelni és változók vagy megfigyelések hiányzó adatok eltávolítása. Rattle is azonosíthatja a társítási szabályok megfigyelések és/vagy változók között. A lapok kívül esnek a hatókörön bevezető forgatókönyvhöz.

Rattle fürt analysis is végrehajtható. Most kizárása egyes funkciók a kimeneti könnyebb átláthatóság érdekében. Az a **adatok** lapra, majd **figyelmen kívül hagyása** mellett minden egyes tíz elemekhez kivételével a változók:

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

Ezután térjen vissza a **fürt** lapra, majd **KMeans**, és állítsa be a *számát* 4. Majd **hajtható végre**. Az eredmények a kimeneti ablakban jelennek meg. Egy fürt nagy gyakoriságú "hp" és "nagy szóval" rendelkezik, és valószínűleg egy valós üzleti e-mailt.

Egy egyszerű döntési fa gépi tanulási modellek létrehozásához:

* Válassza ki a **modell** lap
* Válasszon **fa** , a **típus**.
* Válassza ki **Execute** szöveg űrlap a kimeneti ablakban a fa megjelenítéséhez.
* Válassza ki a **megrajzolásához** gombra kattintva megtekintheti az olyan grafikus verziója. Ez elég hasonlít a fához azt a korábban beszerzett *rpart*.

A Rattle töltött szolgáltatásait egyik képességét a számos machine learning módszer futtatásához, és gyorsan értékeli őket. A folyamat során a rendszer:

* Válasszon **összes** a a **típus**.
* Válassza ki **hajtható végre**.
* Művelet befejeződése után kattintson a bármely egyetlen **típus**, például **SVM**, és tekintse meg az eredményeket.
* Összehasonlíthatja a teljesítmény a modellek segítségével ellenőrzéséről a **Evaluate** fülre. Például a **hiba mátrix** kijelölés megjeleníti a a félreértések mátrix, általános hiba, és az egyes átlagolt osztály hiba az érvényesítési beállítása a.
* ROC görbék megrajzolásához, érzékenységi elemzést, és hajtsa végre a modell értékelések más típusú.

Miután elkészült, létrehozási modelleket, válassza ki a **napló** fülre kattintva megtekintheti az R-kód Rattle futtatásához a munkamenet során. Kiválaszthatja a **exportálása** gombra kattintva mentse.

> [!NOTE]
> Hogy programhiba van Rattle jelenlegi kiadásában. A parancsfájl módosításával, vagy ismételje meg a későbbi segítségével, egy # karaktert elé kell beilleszteni * exportálása... Ez a napló * a szöveges napló.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL & Squirrel SQL
A DSVM PostgreSQL telepítve rendelkezik. PostgreSQL egy olyan kifinomult, nyílt forráskódú relációs adatbázis. Ez a szakasz bemutatja, hogyan a levélszemét adatkészlet betöltése a PostgreSQL és majd lekérdezése.

Az adatok betöltése előtt szeretné engedélyezni a localhost jelszó-hitelesítést. A parancssorba:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

A konfigurációs fájl alján, amelyek az engedélyezett kapcsolatok több sorba a következők:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Módosítsa a "Helyi kapcsolatok IPv4" sort használatát md5 helyett ident, azt is bejelentkezhetnek a felhasználónévvel és jelszóval:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Majd indítsa újra a postgres szolgáltatást:

    sudo systemctl restart postgresql

Az interaktív terminál PostgreSQL, a beépített postgres felhasználóként a psql elindításához futtassa a következő parancsot a parancssorba:

    sudo -u postgres psql

Hozzon létre egy új felhasználói fiókot, amely a azonos felhasználónév használata a Linux fiókként van jelenleg a következőként bejelentkezve, és adjon neki egy jelszó:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Majd jelentkezzen be psql a felhasználó:

    psql

És az adatok importálása egy új adatbázist:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Most tegyük feltérképezheti az adatokat, és egyes lekérdezések futtatása használatával **Squirrel SQL**, egy grafikus eszközt, amely lehetővé teszi az adatbázisok JDBC-illesztőt keresztül kommunikál.

A kezdéshez indítsa el az alkalmazás menüből Squirrel SQL. Az illesztőprogram telepítéséhez:

* Válassza ki **Windows**, majd **illesztőprogram megjelenítése**.
* Kattintson a jobb gombbal a **PostgreSQL** válassza **módosítása illesztőprogram**.
* Válassza ki **Extra osztály az elérési út**, majd **hozzáadása**.
* Adja meg ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** a a **Fájlnév** és
* Válassza ki **nyitott**.
* Válassza ki a lista illesztőprogramokat, majd válassza ki **org.postgresql.Driver** a **osztálynév**, és válassza ki **OK**.

A kapcsolat a helyi kiszolgáló beállítása:

* Válassza ki **Windows**, majd **aliasok megjelenítése.**
* Válassza ki a  **+**  gomb új aliast.
* Nevezze el *levélszemét adatbázis*, válassza a **PostgreSQL** a a **illesztőprogram** legördülő listán.
* Az URL-cím beállítása *jdbc:postgresql://localhost/spam*.
* Adja meg a *felhasználónév* és *jelszó*.
* Kattintson az **OK** gombra.
* Lehetőségre a **kapcsolat** ablakban kattintson duplán a ***levélszemét adatbázis*** alias.
* Kattintson a **Csatlakozás** gombra.

Egyes lekérdezések futtatása:

* Válassza ki a **SQL** fülre.
* Adja meg például egy egyszerű lekérdezést `SELECT * from data;` a lekérdezés szövegmezőjének az SQL lap tetején.
* Nyomja le az **Ctrl-adja meg a** futtatni. Alapértelmezés szerint Squirrel SQL sorát adja vissza az első 100 a lekérdezésből.

Nincsenek további lekérdezések futtathatja az adatokba. Például hogyan működik a word gyakoriságát *ellenőrizze* eltérnek a levélszemét és sonka?

    SELECT avg(word_freq_make), spam from data group by spam;

Mik a tulajdonságokkal rendelkeznek az e-mailek gyakran tartalmaznak, vagy *3d*?

    SELECT * from data order by word_freq_3d desc;

A legtöbb e-mailek, amelyek magas előfordulása *3d* vannak látszólag levélszemét, így az e-mailek besorolására a prediktív modell létrehozásához egy hasznos funkció lehet.

Ha egy PostgreSQL-adatbázisban tárolt adatok gépi tanulás elvégzésére, érdemes lehet [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server-adatraktár
Az Azure SQL Data Warehouse egy felhőalapú, horizontálisan felskálázható adatbázis, amely nagy mennyiségű relációs és nem relációs adatot képes feldolgozni. További információkért lásd: [Mi az Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Csatlakozás az adatraktárhoz, és a tábla létrehozásához futtassa a következő parancsot a parancssorba:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Majd az sqlcmd parancssorból:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Másolja az adatokat a BCP-vel:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> A sorvégződések a letöltött fájlt a Windows-stílusú, de bcp UNIX stílusú vár, ezért ellenőriznünk kell, hogy bcp, amely az - r jelzővel.
>
>

És lekérdezés az Sqlcmd használatával:

    select top 10 spam, char_freq_dollar from spam;
    GO

Az Squirrel SQL is lekérdezhet. Hasonló lépésekkel PostgreSQL, használja a Microsoft MSSQL Server JDBC-illesztőt, amely itt található: a ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Következő lépések
Témakörök, amelyek végigvezetik a feladatokat az Azure-ban a Adattudomány folyamat alkotó áttekintését lásd: [Team adatok tudományos folyamat](http://aka.ms/datascienceprocess).

Egy másik végpont forgatókönyvek, amelyek bemutatják, meghatározott forgatókönyvek esetén az Team tudományos folyamat lépéseit ismertetését lásd: [Team adatok tudományos folyamat forgatókönyvek](../team-data-science-process/walkthroughs.md). A forgatókönyvek is bemutatják, hogyan lehet a felhő- és a helyszíni eszközök és szolgáltatások egyesítése munkafolyamat vagy csővezeték intelligens alkalmazás létrehozása.
