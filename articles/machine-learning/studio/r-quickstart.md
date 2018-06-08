---
title: Az R nyelv a Machine Learning gyors üzembe helyezési útmutató |} Microsoft Docs
description: Az R programozási oktatóanyag segítségével gyorsan az előrejelzési megoldások létrehozásához Azure Machine Learning Studio az R nyelv első lépéseiben.
keywords: gyors üzembe helyezés, r nyelven, r programozási nyelv, r programozási útmutató
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 99a3a0fd-b359-481a-b236-66868deccd96
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 57c0030ac613167dc3ed567c2876b8e2e110d47a
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836375"
---
# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning"></a>Bevezető oktatóprogram az Azure Machine Learning rendszerrel használt R programozási nyelvbe

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Bevezetés
Gyors üzembe helyezési oktatóanyag segítségével gyorsan az Azure Machine Learning kiterjesztése az R programozási nyelv használatával indíthat el. Ez az oktatóanyag R programozási létrehozása, tesztelése és R kódban az Azure Machine Learning segítségével hajtható végre. Oktatóanyag munka, az R nyelv használatával az Azure Machine Learning előrejelzési teljes körű megoldást hoz létre.  

Számos hatékony gépi tanulási és adatok adatkezelési modulok Microsoft Azure Machine Learning tartalmazza. A hatékony R nyelv, a nyelv franca Analytics leírása. Elemzés és az adatok kezelése az Azure Machine Learning boldogan, bővíthető R. használatával Ez a kombináció biztosít a méretezhetőséget és a központi telepítés az Azure Machine Learning könnyű a rugalmasságot és az r mély elemzés

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="forecasting-and-the-dataset"></a>Az előrejelzés és az adatkészletet
Az előrejelzés a széles körben munkavállalók és elég hasznos lehet analitikai módszer. Közös közé határozza cikkek optimális készlet szintek történő előrejelzésére makrogazdasági változók meghatározása előrejelzésére használja. Az előrejelzés idő adatsorozat modellekkel való általában történik.

Adatsorozat időadatok az adatai, amelyben az értékek rendelkezik idő indexszel. Az idő index rendszeres, lehet, például minden hónap vagy percben, vagy szabálytalan. Egy idősorozat-modellbe idő adatsorozat adatokon alapul. Az R programozási nyelvet egy rugalmas keretrendszer és az idő adatsorozat adatok széles körű elemzés tartalmazza.

Az első lépések útmutató rendszer kell kaliforniai tejtermelésre használata és díjszabási adatok. Ezek az adatok több tejtermékek és az ár tejzsír, egy teljesítményteszt hagyományos havi információt tartalmaz.

A cikkben, és az R parancsfájlokat használt adatok [itt letöltött][download]. Ezeket az adatokat eredetileg synthesized volt, a Wisconsin egyetemi elérhető információk http://future.aae.wisc.edu/tab/production.html.

### <a name="organization"></a>Szervezet
Megismerheti, hogyan hozhat létre, tesztelheti és elemzés és az adatok R adatkezelési kód végrehajtása az Azure Machine Learning környezetben, azt fogja előrehaladás számos lépésen.  

* Először néhány a alapjait, az Azure Machine Learning Studio környezet az R nyelv használatával.
* Ezután azt előrehaladás megvitatása adatok, az R-kód és az Azure Machine Learning környezetben grafikus i/o különböző jellemzőinek megtekintését.
* Azt fogja majd összeállítani az előrejelzési megoldás első része adatok tisztítása és átalakítása kódjának létrehozásával.
* Az előkészített adataink végezzük el az adathalmaz változók számos közötti összefüggések elemzése.
* Végül létre fogunk hozni egy határozza idő series előrejelzési modell tejtermelésre.

## <a id="mlstudio"></a>Együttműködhet az R nyelv a Machine Learning Studióban
Ez a szakasz végigvezeti az R programozási nyelv, a Machine Learning Studio környezetben való interakció néhány alapjait. Az R nyelv hozzon létre egyéni elemzés és adatok adatkezelési modulok az Azure Machine Learning környezeten belül hatékony eszközt biztosít.

Rstudióból történő fejlesztéséhez, tesztelése és hibakeresése kis méretű R-kód fogja használni. Ez a kód vágjon és a beillesztési műveleteket egy [R-parancsfájl végrehajtása] [ execute-r-script] modul a Machine Learning Studióban készen áll a futtatásra.  

### <a name="the-execute-r-script-module"></a>Az R-parancsfájl végrehajtása modul
A Machine Learning Studio belül R parancsfájlok belül a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Egy példa a [R-parancsfájl végrehajtása] [ execute-r-script] modul a Machine Learning Studióban az 1. ábrán látható.

 ![R programozási nyelv: végrehajtás R-parancsfájl a modul a Machine Learning Studióban kiválasztva][1]

*1. ábra A Machine Learning Studio környezet az R-parancsfájl végrehajtása modul kiválasztva megjelenítő.*

1. ábra hivatkozik, vizsgáljuk meg néhány használata a Machine Learning Studio környezet legfontosabb részeit a [R-parancsfájl végrehajtása] [ execute-r-script] modul.

* A modul a kísérletben a középső ablaktáblán jelennek meg.
* A jobb oldali ablaktábla felső részén egy ablakban megtekintheti és szerkesztheti az R parancsfájlok tartalmazza.  
* A jobb oldali alsó részén látható néhány tulajdonságát a [R-parancsfájl végrehajtása][execute-r-script]. A hiba- és kimeneti naplókat, ha a panel megfelelő tesztüzeméhez kattintva tekintheti meg.

Azt, természetesen megvitatása lesz, a [R-parancsfájl végrehajtása] [ execute-r-script] Ez a dokumentum további részében részletesebben.

Az összetett R funkciók használatakor javasolt, hogy szerkesztése, tesztelése és hibakeresése a Rstudióból. Csakúgy, mint bármely szoftverfejlesztői Növekményesen kiterjesztése a kódot, és kis egyszerű vizsgálati eseteknél az tesztelik azt. Kivágás, majd az R parancsfájl ablakban illessze be a funkciók a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Ez a megközelítés lehetővé teszi az Rstudióból integrált fejlesztési környezeti (IDE) és a hatványra emelésének Azure Machine Learning hasznosítására.  

#### <a name="execute-r-code"></a>R-kód végrehajtása
Bármely az R-kód a [R-parancsfájl végrehajtása] [ execute-r-script] modul hajtja végre, amikor a kísérlet futtatásához kattintson a a **futtatása** gombra. Végrehajtás befejezése után egy pipa jelenik meg a [R-parancsfájl végrehajtása] [ execute-r-script] ikonra.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Defenzív R kódolási Azure Machine Learning
Tegyük fel például, egy webszolgáltatás-bővítmény az R-kód fejlesztői Azure Machine Learning segítségével, meg kell mindenképpen terveznie módját a kód foglalkozni fog váratlan adatok bemeneti és a kivételek. Érthetőség kedvéért I nem szereplő nagy átszállítást megakadályozzák ellenőrzik, illetve a legtöbb látható példák kivételkezelő. Azonban, a Folytatás I fogja diktálni funkciók számos példát R-hez tartozó kivételkezelő funkció használatával.  

R kivételkezelést teljesebb kezelést van szüksége, ha szeretnék javasoljuk, hogy olvassa el a megfelelő szakaszok a könyv felsorolt Wickham által [B függelék – további olvasási](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Hibakeresését és tesztelését R a Machine Learning Studióban
Megújítja, a javasolt tesztelése és hibakeresése az R-kód az Rstudióból kis méretű. Azonban, vannak esetek, ahová el kell nyomon követheti az R kódproblémáknak az [R-parancsfájl végrehajtása] [ execute-r-script] magát. Ezenkívül ajánlott ellenőrizni a Machine Learning Studio eredményezi.

Az R-kód és az Azure Machine Learning platformon végrehajtása kimenete elsősorban megtalálható kimenetét. További információk a error.log fogja látni.  

Ha a hiba akkor fordul elő, a Machine Learning Studióban az R-kód futtatása során, az első lépések error.log lássunk kell lennie. Ez a fájl hasznos hibaüzenetek segítenek megérteni, és javítsa ki a hibát tartalmazza. Error.log megtekintéséhez kattintson a **nézet hibanapló** a a **tulajdonságok panelen** a a [R-parancsfájl végrehajtása] [ execute-r-script] hiba.

Például futtattam a következő R-kód egy nem definiált változó y a egy [R-parancsfájl végrehajtása] [ execute-r-script] modul:

    x <- 1.0
    z <- x + y

Ez a kód nem tud végrehajtani, hibát eredményez. Kattintson a **nézet hibanapló** a a **tulajdonságok panelen** hozza létre a képernyőt a 2. ábrán látható.

  ![Felugró hibaüzenet][2]

*2. ábra Előugró hibaüzenet.*

Úgy tűnik, igazolnia kell a hely kimenetét a R hibaüzenet megtekintéséhez. Kattintson a [R-parancsfájl végrehajtása] [ execute-r-script] majd kattintson a a **kimenetét megtekintése** elemet a **tulajdonságok panelen** jobb. Egy új böngészőablakban nyílik meg, és a következő látható.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ez a hibaüzenet nem meglepetések számát tartalmazza, és egyértelműen azonosítja a probléma.

Az érték az R bármely objektum vizsgálata, kinyomtathatja ezeket az értékeket a kimenetét fájlba. A szabályok objektum értékek vizsgálata azonosak lényegében egy interaktív R munkamenet. Például ha sorban adjon meg egy változónevet, az objektum értékének nyomtat a kimenetét fájlba.  

#### <a name="packages-in-machine-learning-studio"></a>A Machine Learning Studióban csomagok
Az Azure Machine Learning több mint 350 előre telepített R nyelvi csomagokat tartalmaz. A következő kódot használhatja a [R-parancsfájl végrehajtása] [ execute-r-script] modul előre telepített csomagok listáját.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Ha ez a kód utolsó sora jelenleg nem világos, olvassa el a. Ez a dokumentum többi része nagymértékben ismertetik az Azure Machine Learning-környezet az R nyelv használatát.

### <a name="introduction-to-rstudio"></a>Rstudióból bemutatása
Rstudióból egy széles körben használt IDE az R. A Szerkesztés, a tesztelés és a hibakereséshez az R-kód gyors üzembe helyezési útmutatóban használt egyes használom Rstudióból. Ha tesztelt, és készen áll, az R-kód egyszerűen kimásolni, majd a Rstudióból szerkesztő illessze be a Machine Learning Studio [R-parancsfájl végrehajtása] [ execute-r-script] modul.  

Ha nem rendelkezik az R programozási nyelv, a asztali gépen telepítve van, akkor tegye meg most javasolt. Szabad nyílt forráskódú R nyelv érhető el, az átfogó R archív hálózati (CRAN), [ http://www.r-project.org/ ](http://www.r-project.org/). Nincsenek elérhető a Windows, Mac OS és Linux/UNIX letöltések. Válassza ki a közeli tükrözött, és kövesse a letöltési utasításokat. Ezenkívül a CRAN számos hasznos elemzés és az adatok adatkezelési csomagok olyan tartalmazza.

Ha most ismerkedik a Rstudióból, töltse le és telepítse az asztali verzióját. Windows, Mac OS és Linux/UNIX, letölti a Rstudióból található http://www.rstudio.com/products/RStudio/. Kövesse az utasításokat, az asztali számítógépre telepítéséhez Rstudióból megadott.  

Egy oktatóanyag bemutatása Rstudióból érhető el: https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

I néhány kiegészítő információt nyújt az Rstudióból használatával [függelék][appendixa].  

## <a id="scriptmodule"></a>Az R-parancsfájl végrehajtása modul mindkét adatok beolvasása
Ebben a szakaszban ismertetjük be és ki hogyan be adatok a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Azt a rendszer tekintse át kezelését a különböző adattípusok olvassa be és ki a [R-parancsfájl végrehajtása] [ execute-r-script] modul.

A teljes kód látható az ebben a szakaszban korábban letöltött zip-fájlban van.

### <a name="load-and-check-data-in-machine-learning-studio"></a>Betölteni, és ellenőrizze az adatokat a Machine Learning Studióban
#### <a id="loading"></a>Az adatkészlet betöltése
Először tölt a **csdairydata.csv** Azure Machine Learning Studio fájlból.

* Indítsa el az Azure Machine Learning Studio környezetet.
* Kattintson a **+ új** a képernyő, és válassza ki a bal alsó **Dataset**.
* Válassza ki **helyi fájlból**, majd **Tallózás** válassza ki a fájlt.
* Győződjön meg arról, hogy a kijelölt **általános CSV-fájl (.csv) fejlécű** az adatkészlet típusként.
* Kattintson a pipa jelre.
* A dataset feltöltése után megtekintheti az új adatkészlet parancsával a **adatkészletek** fülre.  

#### <a name="create-an-experiment"></a>A kísérlet létrehozása
Most, hogy néhány adat a Machine Learning Studióban, igazolnia kell hozni egy kísérletet az elemzés végrehajtásához.  

* Kattintson a **+ új** az alsó balra, és válassza ki **kísérlet**, majd **üres kísérlet**.
* Jelölje ki, és módosítja, a kísérlet nevet adhat a **kísérlet létre...**  cím az oldal tetején. Például megváltoztathatja úgy, hogy **hitelesítésszolgáltató tejtermék Analysis**.
* Bontsa ki a kísérlet lap bal oldalán **mentett adatkészletek**, majd **saját adatkészletek**. Megjelenik a **cadairydata.csv** korábban feltöltött.
* Húzza a **csdairydata.csv dataset** alakzatot a kísérletet.
* Az a **keresési kísérletezhet elemek** mező a bal oldali ablaktáblán, a típus tetején [R-parancsfájl végrehajtása][execute-r-script]. A modul jelennek meg a keresési listája jelenik meg.
* Húzza a [R-parancsfájl végrehajtása] [ execute-r-script] modul alakzatot a sorát tartalmazza.  
* Csatlakoztassa a kimenetét a **csdairydata.csv dataset** a bal oldali bemeneti (**Dataset1**), a [R-parancsfájl végrehajtása][execute-r-script].
* **Ne feledje kattintson a "Mentés"!**  

Ezen a ponton a kísérletben hasonlóan kell kinéznie 3. ábra.

![A hitelesítésszolgáltató tejtermék elemzés kísérletezhet a DataSet adatkészlet és R-parancsfájl végrehajtása modul][3]

*3. ábra A hitelesítésszolgáltató tejtermék elemzés a DataSet adatkészlet és R-parancsfájl végrehajtása modul kísérletezhet.*

#### <a name="check-on-the-data"></a>Az adatok ellenőrzése
Most rá egy pillantást az adatok azt töltött be a kísérletet. A kísérletben, kattintson a kimenetét a **cadairydata.csv dataset** válassza **megjelenítése**. 4. ábra hasonlót meg kell jelennie.  

![A cadairydata.csv dataset összegzése][4]

*4. ábra A cadairydata.csv adatkészlet összegzését.*

Ebben a nézetben látható nagy mennyiségű hasznos információkat. Láthatja, hogy a dataset több első sorát. Ha olyan oszlopot válasszon ki, hogy az a statisztika szakasz az oszlop további információkat jeleníti meg. Például a szolgáltatástípus sor látható az Azure Machine Learning Studio rendelt az oszlop milyen adatokat. Ehhez hasonló gyorsan át, akkor az a jó megerősítést ellenőrzés előtt először bármely súlyos munkájuk elvégzéséhez.

### <a name="first-r-script"></a>Első R-parancsfájl
Hozzon létre egy egyszerű első R-parancsfájl az Azure Machine Learning Studióban kísérletezhet. Létrehozott és a következő parancsfájl tesztelve az Rstudióból.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    str(cadairydata)
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Most át kell adnom a parancsfájl az Azure Machine Learning Studio. Sikerült egyszerűen Kivágás, majd illessze be. Azonban ebben az esetben fogja átvinni az R-parancsfájl egy zip-fájl használatával.

### <a name="data-input-to-the-execute-r-script-module"></a>Adatbevitel az R-parancsfájl végrehajtása modulhoz
Most rendelkezik a bemeneti adatok számára egy pillantást a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Ebben a példában a kaliforniai tejelő adatok olvasását azt a [R-parancsfájl végrehajtása] [ execute-r-script] modul.  

Három lehetséges bemeneteit van a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Bármely egyikét vagy mindegyikét a bemenetek, attól függően, hogy az alkalmazás használhat. Egyúttal tökéletesen nagy valószínűséggel nem bemenetből fogad adatokat minden R-parancsfájl használata.  

Vizsgáljuk meg mindegyik e ráfordítások fog balról jobbra. A bemenetek nevei helyezi el a kurzor fölé a bemeneti és a hozzájuk tekintheti meg.  

#### <a name="script-bundle"></a>Parancsfájl-csomag
A parancsfájl csomagot adjon meg lehetővé teszi a azokat egy zip-fájl tartalmát adja át [R-parancsfájl végrehajtása] [ execute-r-script] modul. A zip-fájl tartalmának olvassa be az R-kód a következő parancsok egyikét használhatja.

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [!NOTE]
> Az Azure Machine Learning kezeli a zip fájlokat, ha azok src / könyvtár, ezért szüksége lesz a fájlneveket, a könyvtárnév előtagja. Például, ha a zip-fájlokat tartalmazza `yourfile.R` és `yourData.rdata` a zip-gyökérben, meg kellene cím ezek `src/yourfile.R` és `src/yourData.rdata` használatakor `source` és `load`.
> 
> 

Ismertettük már adathalmaz betöltése [betöltése a dataset](#loading). Miután létrehozta és tesztelte az R-parancsfájl az előző szakaszban látható, a következő műveleteket:

1. Mentse az R-parancsfájl egy. R-fájl. A parancsfájl "simpleplot jelentkezem. R". Ez a tartalom.
   
        ## Only one of the following two lines should be used
        ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
        ## If in RStudio, use the second line with read.csv()
        cadairydata <- maml.mapInputPort(1)
        # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
        str(cadairydata)
        pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
        ## The following line should be executed only when running in
        ## Azure Machine Learning Studio
        maml.mapOutputPort('cadairydata')
2. Hozzon létre egy zip fájlt, és másolja a parancsfájlt a zip-fájlba. A Windows, kattintson a jobb gombbal a fájlra és kiválasztása **küldése**, majd **tömörített mappa**. Ezzel létrehoz egy új zip-fájl, amely tartalmazza a "simpleplot. R"fájl.
3. Adja hozzá a fájlt, hogy a **adatkészletek** a Machine Learning Studióban, mint típusának megadásával **zip**. Most látnia kell a zip-fájl a adatkészletek.
4. A zip-fájl a húzással **adatkészletek** alakzatot a **ML Studio vászonra**.
5. Csatlakoztassa a kimenetét a **zip-adatok** ikonra a **parancsfájl köteg** a bemeneti a [R-parancsfájl végrehajtása] [ execute-r-script] modul.
6. Típus a `source()` a kód ablakának zip fájlt nevű függvény a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Abban az esetben, ha a beírt `source("src/simpleplot.R")`.  
7. Ellenőrizze, hogy **mentése**.

Ha ezeket a lépéseket befejeződött, a [R-parancsfájl végrehajtása] [ execute-r-script] modul végrehajtja az R-parancsfájl a zip-fájlban szereplő a kísérlet futtatásakor. Ezen a ponton a kísérletben hasonlóan kell kinéznie 5. ábra.

![Kísérlet zip R-parancsfájl használatával][6]

*5. ábra A kísérletben zip R-parancsfájl használatával.*

#### <a name="dataset1"></a>Dataset1
Az R-kód továbbíthatja a Dataset1 bemeneti használatával adatok téglalap alakú tábla. Az egyszerű parancsfájlban a `maml.mapInputPort(1)` függvény olvassa be az adatok %1 porton. Ezek az adatok majd hozzá van rendelve egy dataframe változó neve, a kódban. Az egyszerű parancsprogram az első kódsort a hozzárendelés hajtja végre.

    cadairydata <- maml.mapInputPort(1)

Hajtsa végre a kísérletben kattintva a **futtatása** gombra. A végrehajtás befejezése után kattintson a a [R-parancsfájl végrehajtása] [ execute-r-script] modul, és kattintson **kimeneti napló megtekintése** a Tulajdonságok panelen. Egy új lapot a böngészőben a kimenetét fájl tartalmát megjelenítő meg kell jelennie. Előfordulhat, hogy le kell megjelennie a következő hasonlót.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Megtalálhatók a lap le további információk a oszlop, amely a következő hasonlót fog kinézni.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Ezekkel az eredményekkel többnyire 228 megfigyelés és a dataframe 9 oszlopai a várt módon vannak. Az oszlopok neveit, az R adattípus és egy minta oszlopok láthatja.

> [!NOTE]
> Az azonos nyomtatásra kényelmesen érhető el R eszköz kimenetében a [R-parancsfájl végrehajtása] [ execute-r-script] modul. A kimenetének ismertetik a [R-parancsfájl végrehajtása] [ execute-r-script] modul a következő szakaszban.  
> 
> 

#### <a name="dataset2"></a>Dataset2
A Dataset2 bemeneti működése megegyezik-e Dataset1. Használja a bemeneti átviheti az adatok egy második téglalap alakú tábla az R-kódba. A függvény `maml.mapInputPort(2)`, az argumentumnak 2 használatával továbbítja ezeket az adatokat.  

### <a name="execute-r-script-outputs"></a>R-parancsfájl kimenetek végrehajtása
#### <a name="output-a-dataframe"></a>Egy dataframe kimeneti
A kimenetre küldheti az R dataframe tartalmát és az eredmény Dataset1 porton keresztül téglalap alakú táblázatként használatával a `maml.mapOutputPort()` függvény. Az egyszerű R-parancsfájl ez hajtható végre a következő sort.

    maml.mapOutputPort('cadairydata')

Miután a kísérletet, kattintson a az eredmény Dataset1 kimeneti portra, és kattintson a **Visualize**. 6. ábra hasonlót meg kell jelennie.

![A képi megjelenítés a kimeneti kaliforniai tejelő adatok][7]

*6. ábra A képi megjelenítés a kimeneti kaliforniai tejelő adatok.*

A kimeneti keres a bemeneti azonos, pontosan úgy várt.  

### <a name="r-device-output"></a>R eszköz kimeneti
Az eszköz kimenetét a [R-parancsfájl végrehajtása] [ execute-r-script] modul üzenetek és grafikus kimeneti tartalmazza. Mindkét standard kimenet és a standard hiba R üzenetei R eszköz kimeneti portjával.  

Az R eszköz kimeneti megtekintéséhez kattintson a porton majd a **Visualize**. A standard kimenet és a standard hiba a 7. ábra az R-parancsfájl látható.

![Standard kimenet és a standard hiba R eszköz port][8]

*7. ábra Standard kimenet és a standard hiba R eszköz port.*

Azt lefelé görgetéshez használható tekintse meg a 8. ábrán az R-parancsfájl kimenete grafikus.  

![Az R eszköz port grafikus kimenete][9]

*8. ábra A grafikus az R eszköz port kimenetét.*  

## <a id="filtering"></a>Adatok szűrése és átalakítás
Ebben a szakaszban végezzük el a néhány alapvető adatok szűrése és átalakítási műveletek a kaliforniai tejelő adatokon. Ez a szakasz végén azt fogja tudni adatok formátuma nem megfelelő az elemzési modell készítéséhez.  

Pontosabban, ez a szakasz azt feladatokat kell elvégezni több közös adatok tisztítása és átalakítás: Adja meg a szűrést dataframes, hozzáadása új számított oszlop, az átalakítási és átalakítások értéket. A háttérben segítséget valós problémákat észlelt a számos változata foglalkozik.

Az ebben a szakaszban a teljes R-kód nem áll rendelkezésre a korábban letöltött zip-fájl.

### <a name="type-transformations"></a>Típus átalakítások
Most, hogy azt a kaliforniai tejelő adatok olvashatja be az R-kód a [R-parancsfájl végrehajtása] [ execute-r-script] modul, igazolnia kell, hogy az oszlopok adatainak legyen-e a kívánt típusát és formátumát.  

R az egy dinamikusan gépelt nyelv, ami azt jelenti, hogy adattípusok kényszerített vannak-e egy másik szükség szerint. Az R atomi adattípusait numerikus, logikai és karakter közé tartozik. A tényező típusú compactly kategorikus adatok tárolására szolgál. Adattípusok sokkal további információk találhatók a hivatkozások [B függelék – további információ](#appendixb).

A táblázatos adatolvasás a külső forrásból származó R, esetén mindig egy célszerű ellenőrizni az eredményül kapott típusok az oszlopok. Érdemes lehet egy oszlophoz Típusjelző karakter, de sok esetben ez fog megjelenni tényezőként vagy fordítva. Más esetekben egy oszlopot úgy gondolja, hogy legyen numerikus által képviselt karakter adatok, például az 1,23, lebegőpontos helyett a "1,23" pont számát.  

Szerencsére is könnyen egy típuskonverzió a másikra, mindaddig, amíg lesz lehetséges. Például "Nevada" konvertálása nem egy numerikus érték, de akkor átalakíthatja egy tényező (kategorikus változó). Másik példaként egy numerikus 1 átalakíthatja a következő karaktert: "1" vagy egy tényező.  

Ezek az átalakítások bármelyikét szintaxisa a következő egyszerű: `as.datatype()`. A típuskonverziós a következők:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Azt az előző szakaszban bemeneti oszlopok adattípusai megnézi: az összes oszlop sem típusú numerikus, kivéve az oszlop, "Month", amelynek a típuskarakter címkével. Most konvertálja az tényező, és a teszteredmények.  

Rendelkezik a sort, amelyben a scatterplot mátrix létrehozza és hozzáadja a "Month" oszlop átalakítása tényezőként sor törölve A kísérletemben I fog csak kivágása és R illessze be a kódját ablakot, a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Is nem sikerült frissíteni a zip-fájl, és töltse fel az Azure Machine Learning Studio, de ez számos lépést időt vesz igénybe.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(cadairydata$Month)
    str(cadairydata) # Check the result
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Most futtassa ezt a kódot, és tekintse meg a kimeneti naplót, az R-parancsfájl. 9. ábra jelenik meg a vonatkozó adatokat a naplóból.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*9. ábra. A dataframe tényező változóhoz összegzését.*

A hónap típusú most üzenetnek kell megjelennie "**keresztüli 14 szintek rendelkező tényező**". Ez az probléma, mivel az év csak 12 hónapon keresztül. Is ellenőrizheti, hogy a típus **Visualize** eredmény DataSet port van "**Categorical**".

A probléma oka, hogy a "Month" oszlop nem lett a kódolt rendszeresen. Bizonyos esetekben egy hónap. április nevezik, és más, ápr rövidítéseket tartalmaz. A karakterlánc 3 karakter segítségével azt is megoldhatja a problémát. A kódsort most a következőképpen néznek:

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Futtassa újra a kísérletet, és tekintse meg a kimeneti naplót. A kívánt eredmény elérése érdekében a 10. ábrán láthatók.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*10. ábra. Megfelelő számú tényező szintek dataframe összegzését.*

A tényező változó most már a kívánt 12 szinteket.

### <a name="basic-data-frame-filtering"></a>Alapvető adatok keret szűrése
R dataframes hatékony szűrési lehetőségeket nyújtanak. Adatkészletek sorok vagy oszlopok logikai szűrők használatával lehet részlegesen beágyazott. Sok esetben összetett szűrési feltételeket lesz szükség. A hivatkozások [B függelék – további információ](#appendixb) dataframes szűrés kiterjedt példái tartalmaz.  

Van egy bittel szűrése az adathalmaz azt kell végeznie. Ha az oszlop szerepel a cadairydata dataframe tekinti meg, látni fogja a két felesleges oszlopok. Az első oszlop csak egy sor számát, amely nem nagyon hasznos tartalmazza. A második oszlopban Year.Month, redundáns információkat tartalmaz. Azt is könnyen használatával zárhatja ki ezeket az oszlopokat a következő R-kód.

> [!NOTE]
> Mostantól ebben a szakaszban I csak láthatja a hozzáadott a kód a [R-parancsfájl végrehajtása] [ execute-r-script] modul. Minden új sor veszek fel **előtt** a `str()` függvény. Ez a függvény az eredményeket az Azure Machine Learning Studióban használni.
> 
> 

Az R kód adhatok hozzá a következő sort a [R-parancsfájl végrehajtása] [ execute-r-script] modul.

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Futtassa a következő kódot a kísérletben, és ellenőrizze a kimeneti naplót kapott eredmény. Ezekkel az eredményekkel 11. ábra mutatja be.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*11. ábra. A két oszlop eltávolítása dataframe összegzését.*

Jó hírünk van! A várt eredményt azt.

### <a name="add-a-new-column"></a>Egy olyan új oszlop hozzáadása
Idő adatsorozat modellek létrehozásához lesz idősorozatban elindítása óta a hónap tartalmazó oszlopa lehet. Létrehozunk egy olyan új oszlop "Month.Count".

A kód létre fogunk hozni az első egyszerű függvény rendszerezésének elősegítése érdekében `num.month()`. Ez a függvény egy olyan új oszlop létrehozása a dataframe érvényes azt lesz. Az új kódot a következőképpen történik.

    ## Create a new column with the month count
    ## Function to find the number of months from the first
    ## month of the time series
    num.month <- function(Year, Month) {
      ## Find the starting year
      min.year  <- min(Year)

      ## Compute the number of months from the start of the time series
      12 * (Year - min.year) + Month - 1
    }

    ## Compute the new column for the dataframe
    cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

Most futtassa a frissített kísérletet, és a kimeneti naplót használja az eredmények megtekintéséhez. Ezekkel az eredményekkel 12. ábra mutatja be.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*12. ábra. További oszlopok dataframe összegzését.*

Úgy tűnik, minden működik. A saját dataframe van a várt értékeket az új oszlopot.

### <a name="value-transformations"></a>Érték átalakítása
Ebben a szakaszban végezzük el néhány egyszerű átalakítások értékeinek néhány a dataframe oszlopot. Az R nyelv támogatja az átalakítások majdnem tetszőleges érték. A hivatkozások [B függelék – további olvasási](#appendixb) kiterjedt példákat tartalmaz.

Ha megnézi a dataframe összegzéseinek szereplő értékeket kell valami páratlan itt. Kaliforniai előállított tej-nál több jégkrémje? Nem, természetesen ez nincs értelme, mivel EV, mint a tény lehet, hogy nem egyes velünk jégkrémje lovers. Az egységek eltérnek. Az ár egységekbe, az font, tej van egységekbe, az 1 millió egység USA font, jégkrémje 1000 egységekbe gallon VELÜNK, pedig túró egységekbe, az USA 1000 font. Ha jégkrémje tömege / gallonra készül 6.5 font, tehetünk könnyen ennek a szorzás ezeket az értékeket átalakítani, így azok minden 1000 font egyenlő egységekben.

Az előrejelzési modell azt használjon tényezőt modellt a trendek és a határozza ezeket az adatokat. Napló-átalakítás lehetővé teszi egy lineáris modell, hogy az a folyamat használja. A napló átalakítása ugyanezt a funkciót, ha a többszöröző alkalmazzák a alkalmazhatja azt.

A következő kódot a I meg egy új funkció `log.transform()`, és alkalmazza azt az numerikus értékeket tartalmazó sorok. Az R `Map()` függvény alkalmazására szolgál a `log.transform()` működnek, mint a kijelölt oszlopokat a dataframe a. `Map()` hasonló `apply()` , de lehetővé teszi, hogy a függvény argumentumainak egynél több listáját. Vegye figyelembe, hogy tényezők listájának biztosított a második argumentumának a `log.transform()` függvény. A `na.omit()` függvény győződjön meg arról, nem találtunk hiányzik vagy nincs megadva érték a dataframe a karbantartási bit típust használja.

    log.transform <- function(invec, multiplier = 1) {
      ## Function for the transformation, which is the log
      ## of the input value times a multiplier

      warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                           "ERROR: Arguments to function log.transform must be greate than zero",
                           "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                           "ERROR: Invalid time seies value encountered in function log.transform"
                           )

      ## Check the input arguments
      if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
      if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
      if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

      ## Wrap the multiplication in tryCatch
      ## If there is an exception, print the warningmessage to
      ## standard error and return NA
      tryCatch(log(multiplier * invec),
               error = function(e){warning(warningmessages[4]); NA})
    }


    ## Apply the transformation function to the 4 columns
    ## of the dataframe with production data
    multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
    cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

    ## Get rid of any rows with NA values
    cadairydata <- na.omit(cadairydata)  

Nincs elég egy bit azonban a a `log.transform()` függvény. Ez a kód a legtöbb ellenőrzi az argumentumok potenciális problémákat vagy kivételek, továbbra is a számítások során felmerülő foglalkozik. Ez a kód csak néhány sornyi ténylegesen tegye a számításokat.

A defenzív programozás célja egy funkcióval, amely megakadályozza a folyamatos feldolgozási hibája megelőzése érdekében. Lehet, hogy a hosszan futó elemzési hirtelen hibát igen kellemetlen, a felhasználók számára. Ez a helyzet elkerüléséhez alapértelmezett visszatérési értékek ki kell választani, amely korlátozza az alárendelt feldolgozási való megosztása kárt. Egy üzenet is elő, hogy valami állapotba került rossz riasztási felhasználók számára.

Ha nincsenek használatban lévő R defenzív programozás, ez a kód egy kicsit túlságosan tűnhet. I végigvezeti a fő lépést:

1. Négy üzenetek vektor van definiálva. Ezek az üzenetek segítségével kommunikálnak a lehetséges hibákat és kivételeket, amelyek akkor fordulhat elő, ezzel a kóddal kapcsolatos információkat.
2. NA érték minden egyes esetben vissza Nincsenek sok egyéb lehetőségeket, amelyekkel kevesebb hatásai lehetnek. I visszaadhatja nullából vektor, vagy az eredeti bemeneti vektort, például.
3. A függvény ellenőrzéseket futtatja a argumentumai. Minden esetben a rendszer hibát észlel, ha ki egy alapértelmezett értéket adja vissza, és üzenet hozzák a `warning()` függvény. Használok `warning()` helyett `stop()` , ez utóbbi megszünteti végrehajtási, pontosan milyen próbálok elkerülése érdekében. Vegye figyelembe, hogy I írt ezt a kódot eljárási Style, ahogy a gyorsítás esetében a rendszer valószínűleg összetett és homályos működési megközelítést.
4. A napló számítások csomagolni vannak `tryCatch()` , hogy a kivételek nem okoz egy hirtelen halt feldolgozás. Nélkül `tryCatch()` R funkciók eredményt stopjelzést, viszont csak, amelyek miatt a legtöbb hiba.

Az R-kód végrehajtása a kísérletben, és nyomtatott kimenete egy pillantást a a kimenetét fájlt. Ekkor az átalakítani kívánt értékeket a négy oszlopot a naplóban is, ahogy az 13. ábra.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*13. ábra. A dataframe átalakított értékeinek összegzése.*

Az érték átalakítása látható. Most tejtermelés nagy mértékben meghaladja minden más tejtermék éles, hogy azt most nézi a Logaritmikus skála tárolóról.

Ezen a ponton az adatok törlődnek, és készen áll az egyes modellezési azt. A képi megjelenítés eredménye Dataset kimenete összegzése megnézi a [R-parancsfájl végrehajtása] [ execute-r-script] modul, látni fogja a "Month" oszlop: "Categorical" 12 egyedi értékekkel újra, ugyanúgy, mint akartunk.

## <a id="timeseries"></a>Idő adatsorozat objektumok és a korrelációs elemzés
Ebben a szakaszban rendszer R idő néhány alapvető adatsorozat objektumok felfedezése és kapcsolatos néhány közötti összefüggések elemzése. A cél, hogy egy több késedelmes jelentések a páros korrelációs információkat tartalmazó dataframe kimeneti.

A teljes R-kód az ebben a szakaszban korábban letöltött zip-fájlban van.

### <a name="time-series-objects-in-r"></a>Az R idő adatsorozat objektumok
Már említettük, az idő a adatsorokat idő indexelik adatértékek sorozata. R idő adatsorozat objektumok létrehozása és kezelése az idő index szolgálnak. Nincsenek idő adatsorozat objektumok használata számos előnnyel jár. Idő adatsorozat objektumok szabad a sok az adatsorozat index időértékek az objektum ágyazott kezelése adatait. Ezenkívül idő adatsorozat objektumok lehetővé teszik az módszerekkel sok időt adatsorozat ábrázolásához, nyomtatási, modellezési stb.

A POSIXct idő adatsorozat osztály általában arra használják, és viszonylag egyszerű. Ez a time series intézkedések idő epoch, 1970. január 1. a kezdetektől osztály. Ebben a példában használjuk POSIXct idő adatsorozat objektumok. Más széles körben használt R idő adatsorozat objektum osztályok itt és xts, bővíthető idősor tartalmazza.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Idő adatsorozat objektum – példa
Lássunk neki a fenti példában. Húzza a **új** [R-parancsfájl végrehajtása] [ execute-r-script] modult a kísérletvászonra. Kapcsolódás a meglévő az eredmény Dataset1 kimeneti portjára [R-parancsfájl végrehajtása] [ execute-r-script] a Dataset1 modul bemeneti port az új [R-parancsfájl végrehajtása] [ execute-r-script] modul.

Ahogyan az első példák mint azt végighalad a példa, bizonyos időpontokban I csak a növekményes további sor megjelenik az R-kód egyes lépésnél.  

#### <a name="reading-the-dataframe"></a>A dataframe olvasása
Első lépésként tegyük egy dataframe olvasása, és győződjön meg arról, hogy a várt eredményt. A következő kódot kell tennie a feladatot.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

Most futtassa a kísérletet. A napló az új R-parancsfájl végrehajtása alakzat 14. ábra hasonlóan kell kinéznie.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*14. ábra. Az R-parancsfájl végrehajtása modul dataframe összegzését.*

Ezek az adatok nem a várt típusok és formátumát. Vegye figyelembe, hogy a "Month" oszlop típusa tényező és szintek várt száma.

#### <a name="creating-a-time-series-object"></a>Idő adatsorozat objektum létrehozása
Egy alkalommal adatsorozat objektum hozzáadása az dataframe kell. Az aktuális kód cserélje le a következő, amely egy olyan új oszlop osztály POSIXct hozzáadja.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Most tekintse meg a naplót. 15. ábra kell hasonlítania.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*15. ábra. Idő adatsorozat objektum dataframe összegzését.*

Az összefoglalást adunk, amely az új oszlop ténylegesen osztály POSIXct láthatja.

### <a name="exploring-and-transforming-the-data"></a>Fel és az adatok átalakítása
Most felfedezheti a változók a DataSet adatkészletben. Egy scatterplot mátrix jó módja a gyorsan át létrehozásához. I vagyok cseréje a `str()` függvény a következő sorral az előző R kódban.

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Futtassa a következő kódot, és tekintse meg, mi történik. Az előállított, az R eszköz port rajzot 16. ábra hasonlóan kell kinéznie.

![A kijelölt változók Scatterplot mátrix][17]

*16. ábra. A kijelölt változók mátrix Scatterplot.*

Van néhány odd-looking struktúra kapcsolatok változókhoz között. Lehet, hogy ez akkor merül fel az adatok a trendek és a tényt, hogy azt nem rendelkezik szabványosított a változókat.

### <a name="correlation-analysis"></a>Korrelációs elemzés
Korrelációs elemzés végrehajtásához kell deszerializálni trend és a változók szabványosítására is. Sikerült egyszerűen használjuk az R `scale()` függvénynek, amely az adatközpontok, mind az méretezi a változókat. Ez a funkció is előfordulhat, hogy gyorsabban futnak. Azonban kívánt defenzív programing példa látható R.

A `ts.detrend()` függvény alább látható mindkét ezeket a műveleteket hajt végre. A következő két sornyi kód deszerializálni trend az adatokat, és majd szabványosítására az értékeket.

    ts.detrend <- function(ts, Time, min.length = 3){
      ## Function to de-trend and standardize a time series

      ## Define some messages if they are NULL  
      messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                    'ERROR: ts.detrend requires argument ts to be of type numeric',
                    paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                    'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                    'ERROR: Detrend regression has failed in ts.detrend',
                    'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
      )
      # Create a vector of zeros to return as a default in some cases
      zerovec  <- rep(length(ts), 0.0)

      # The input arguments are not of the same length, return ts and quit
      if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

      # If the ts is not numeric, just return a zero vector and quit
      if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

      # If the ts is too short, just return it and quit
      if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

      ## Check that the Time variable is of class POSIXct
      if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

      ## De-trend the time series by using a linear model
      ts.frame  <- data.frame(ts = ts, Time = Time)
      tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
               error = function(e){warning(messages[5]); zerovec})

      tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
                 ts <- ts/stdev},
                error = function(e){warning(messages[6]); zerovec})

      ts
    }  
    ## Apply the detrend.ts function to the variables of interest
    df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

    ## Plot the results to look at the relationships
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

Nincs elég egy bit azonban a a `ts.detrend()` függvény. Ez a kód a legtöbb ellenőrzi az argumentumok potenciális problémákat vagy kivételek, továbbra is a számítások során felmerülő foglalkozik. Ez a kód csak néhány sornyi ténylegesen tegye a számításokat.

Rendelkezik már tárgyalt defenzív programozás a példát [átalakítások érték](#valuetransformations). Mindkét számítási blokkok csomagolni vannak `tryCatch()`. A hibák érdemes térjen vissza az eredeti bemeneti vektort, és más esetekben nullák vektor visszatérés.  

Vegye figyelembe, hogy a használt deszerializálni trendek lineáris regressziós egy idő adatsorozat regressziós. A előrejelzőjének változó egy idő adatsorozat objektum.  

Egyszer `ts.detrend()` definiált azt alkalmazhatja azt a dataframe érdeklődik a változókat. A megjelenő listában által létrehozott igazolnia kell kényszerítési `lapply()` adatok dataframe használatával történő `as.data.frame()`. Defenzív aspektusainak miatt `ts.detrend()`, nem sikerült feldolgozni a változók egyikét nem akadályozza meg a helyes feldolgozását a többi.  

A végső kódsort páros scatterplot hoz létre. Miután az R-kód, a scatterplot eredményei láthatók 17. ábra.

![Vonja trendszerű és szabványos idősorozat páros scatterplot][18]

*17. ábra. Páros scatterplot deszerializálni trendszerű és szabványos idősorozat.*

Összehasonlíthatja a 16 feltüntetett ezekkel az eredményekkel. A trend eltávolítása és a szabványos változók sokkal kevesebb szerkezetben vannak ezek a változók kapcsolatai látható.

Az R FTB objektumként korrelációk számítási kódot a következőképpen történik.

    ## A function to compute pairwise correlations from a
    ## list of time series value vectors
    pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
      ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
    }

    ## A list of the pairwise indices
    corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

    ## Compute the list of ccf objects
    cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

    cadairycorrelations

Ezt a kódot futtató hoz létre a naplófájl látható a 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*18. ábra. FTB listája a páros korrelációs elemzés a következő helyről objektumokat.*

Minden egyes lag korrelációs értéke van. A következő korrelációs értékek nincs elég nagy ahhoz, hogy jelentős. Azt ezért be, hogy azt az egyes minta egymástól függetlenül is.

### <a name="output-a-dataframe"></a>Egy dataframe kimeneti
A páros korrelációk számított azt rendelkezik, az R FTB objektumok listájának. Ez megadja bit típust a probléma, az eredmény adathalmaz kimeneti portjával valóban egy dataframe van szüksége. További a FTB objektum pedig egy lista, és azt szeretné, hogy csak az értékeket a lista első elemében, a különböző késedelmes jelentések korrelációk.

A következő kódot a késés értékek kiolvassa a FTB objektumok, amelyek maguk is listák listáját.

    df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

    c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
    r.names  <- c("Corr Cot Cheese - Ice Cream",
                  "Corr Cot Cheese - Milk Prod",
                  "Corr Cot Cheese - Fat Price",
                  "Corr Ice Cream - Mik Prod",
                  "Corr Ice Cream - Fat Price",
                  "Corr Milk Prod - Fat Price")

    ## Build a dataframe with the row names column and the
    ## correlation data frame and assign the column names
    outframe <- cbind(r.names, df.correlations)
    colnames(outframe) <- c.names
    outframe


    ## WARNING!
    ## The following line works only in Azure Machine Learning
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

Az első kódsort kicsit legbonyolultabb, és rövid segítenek megérteni azt. Belülről kifelé működik vezetünk be a következőket:

1. A "**[[**"argumentum operator"**1**" választja ki, a késedelmes jelentések korrelációk vektorát a FTB objektum-lista első eleme.
2. A `do.call()` függvényt alkalmazza a `rbind()` keresztül a lista elemeinek függvény által `lapply()`.
3. A `data.frame()` függvény típusú értékké konvertál, az eredmény által előállított `do.call()` egy dataframe számára.

Vegye figyelembe, hogy a sor nevek a dataframe oszlopában. Ez így megtartja a sor neve, amikor azok kimenetét a [R-parancsfájl végrehajtása][execute-r-script].

A kód futtatásával hozza létre a 19. ábrán látható kimeneti amikor I **Visualize** az eredmény Dataset port a kimenetet. A sor nevek szerepelnek az első oszlop rendeltetésszerű.

![A korrelációs elemzési eredmények kimenete][20]

*19. ábra. Az eredmények a korrelációs elemzés kimenetét.*

## <a id="seasonalforecasting"></a>Idő adatsorozat példa: határozza előrejelzés
Az adatok most elemzés alkalmas űrlapon, és azt észlelte a változók között nincs jelentős korrelációk van. Most lépés, és hozzon létre egy idősorozat-modell előrejelzési. A modell segítségével azt fogja előrejelzési kaliforniai tejtermelés 2013 12 hónapig.

Az előrejelzési modell két összetevőt, a trend összetevőt és határozza lesz. A teljes előrejelzés a két összetevő szorzatát. Az ilyen típusú modell tényezőt modell néven ismert. Ez esetben egy additívak modellt. A változók iránt, így tractable az elemzés azt már alkalmazott napló átalakítás.

A teljes R-kód az ebben a szakaszban korábban letöltött zip-fájlban van.

### <a name="creating-the-dataframe-for-analysis"></a>Elemzés dataframe létrehozása
Először vegyen fel egy **új** [R-parancsfájl végrehajtása] [ execute-r-script] modult a kísérletvászonra. Csatlakozás a **eredmény Dataset** kimenetét a meglévő [R-parancsfájl végrehajtása] [ execute-r-script] modult a **Dataset1** új modul bemeneti. Az eredmény hasonlóan kell kinéznie. ábra 20.

![A hozzáadott új R-parancsfájl végrehajtása modul a kísérletben][21]

*20. ábra. A hozzáadott új R-parancsfájl végrehajtása modul a kísérletben.*

Mint a korrelációs elemzése, azt csak befejeződött, a kell adatsorozat objektum POSIXct idő oszlop hozzáadása. A következő kódot fog ehhez csak.

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

Futtassa a következő kódot, és nézze meg a naplót. Az eredmény 21. ábrát hasonlóan kell kinéznie.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*21. ábra. A dataframe összegzését.*

Ez az eredmény a azt az analysis készen áll.

### <a name="create-a-training-dataset"></a>Hozzon létre egy képzési adatkészlet
Az összeállított dataframe a igazolnia kell létrehozni egy képzési adatkészletet. Ezeket az adatokat tartalmazza az összes az év 2013, kivéve az utolsó 12 észrevételeit Ez az a tesztelési adatkészletnél. A következő code alkészletek a dataframe, és létrehozza a tejelő éles üzemi pontjának és ár változók előkészítésére. Majd a négy termelési előkészítésére létrehozhatók és változók ár. Egy névtelen függvény segítségével határozza meg, néhány szabályozva az információhasználat a rajzolási, és a többi két argumentumot listájának majd ismétlés `Map()`. Ha vannak-e végezni, amely egy a hurok kellene rendelkeznie működött itt, hogy helyesek. Azonban mivel R I vagyok jeleníti meg a működési megközelítés működési nyelvet.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

A kód futtatásával hozza létre az adatsorozat series tevékenységtérkép az R eszköz kimenetből látható 22. ábra idő. Vegye figyelembe, hogy a időtengelye a dátumok, az adatsorozat megrajzolásához metódus időt töltött előnyt egységekben.

![Első idő adatsorozat előkészítésére kaliforniai tejelő éles üzemi pontjának és ár adatok](./media/r-quickstart/unnamed-chunk-161.png)

![Második idő adatsorozat előkészítésére kaliforniai tejelő éles üzemi pontjának és ár adatok](./media/r-quickstart/unnamed-chunk-162.png)

![Az idő adatsorozat előkészítésére kaliforniai tejelő éles üzemi pontjának és ár adatok külső](./media/r-quickstart/unnamed-chunk-163.png)

![Az idő adatsorozat előkészítésére kaliforniai tejelő éles üzemi pontjának és ár adatok negyedik](./media/r-quickstart/unnamed-chunk-164.png)

*22. ábra. Idő adatsorozat előkészítésére kaliforniai tejtermelésre és adatokat.*

### <a name="a-trend-model"></a>A trend modell
Egy alkalommal adatsorozat objektum és volna az adatokat egy pillantást létrehozása kezdjük a kaliforniai tej termelési adatok trend modell összeállításához. Azt a idő adatsorozat regressziós végezheti el. Azt azonban nem egyértelmű, hogy a rendszer több mint egy görbét kell és a megfigyelt változása a betanítási adatok pontosan modellezheti intercept a rajzolási a.

Az adatok kis léptékű megadott, I lesz a trend Rstudióból a modell létrehozása Kivágás és illessze be az eredményül kapott modell Azure Machine Learning. Az ilyen típusú interaktív elemzések elvégzéséhez interaktív környezetet biztosít az Rstudióból.

Első kísérlet, mint a polinom regressziós rendelkező legfeljebb 3 megpróbálom. Nincs túlzott illeszkedő modellek az ilyen típusú valós veszélye. Emiatt érdemes magas rendelés feltételeit elkerülése érdekében. A `I()` funkció meggátolja a tartalmának értelmezése rövid nevének (értelmezi a tartalma ","), és lehetővé teszi a egy regressziós egyenlet egy szó értelmezett függvény írása.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

Ezt követően a következő.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

P értékek (Pr (> |} t |})) szöveget a kimenetben, láthatja, hogy a squared kifejezés nem mindig jelentős. Használom a `update()` működnek, mint ez a modell módosításához squared kifejezés ejtésével.

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

Ezt követően a következő.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Ez a következőhöz jobb. A feltételek mindegyikének szükség. Azonban a 2e-16 érték az alapértelmezett érték, és nem kell venni súlyosan túl.  

Megerősítést tesztként most Meggyőződünk ki kaliforniai tejtermelésre adatok idő adatsorozat rajzot a trend görbe látható. A következő kódot az Azure Machine Learning hozzáadott [R-parancsfájl végrehajtása] [ execute-r-script] modell (nem Rstudióból), hogy rajzot, és a modell létrehozása. Az eredmény 23. ábrán látható.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Kaliforniai tej termelési adatok trend modellel látható](./media/r-quickstart/unnamed-chunk-18.png)

*23. ábra. Kaliforniai tej termelési adatok trend modellel látható.*

Úgy tűnik, a trend modell viszonylag jól illeszkedik az adatokhoz. További úgy nem tűnik, hogy túlzott méretezés lennie, mint például a modell görbén wiggles páratlan.  

### <a name="seasonal-model"></a>Határozza modell
Az aktuális trend modell igazolnia kell leküldéses és a határozza hatások közé tartozik. Az év hónapját használjuk a lineáris modell dummy változóként havonta készülő hatásának rögzítése. Vegye figyelembe, hogy a modell tényező változók bevezetéséhez, ha a webhelynek nem kell számolni. Ha ezt nem teszi meg, a képlet osztana meg van adva, de R fog dobja el az egyik a kívánt tényezőket, de megőrizni annak a webhelynek kifejezés.

Mivel kielégítő trend modell tudunk is használhatók a `update()` működnek, mint az új feltételeket hozzáadása a meglévő modellt. A -1, a frissítés képletben intercept kifejezés esik. Egyelőre folytatja, a Rstudióból:

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

Ezt követően a következő.

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Látható, hogy a modell már nem rendelkezik egy intercept kifejezés és 12 hónap jelentős tényező rendelkezik. Ennek az az pontosan mit akartunk megjelenítéséhez.

Ellenőrizze egy másik idő adatsorozat rajz kaliforniai tejtermelésre adatok milyen jól működik-e a határozza modell. A következő kódot az Azure Machine Learning hozzáadott [R-parancsfájl végrehajtása] [ execute-r-script] hogy rajzot, és a modell létrehozása.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Ez a kód futtatása az Azure Machine Learning hoz létre a rajzolási 24. ábrán látható.

![Kaliforniai tejtermelés határozza hatások beleértve modell](./media/r-quickstart/unnamed-chunk-20.png)

*24. ábra. Kaliforniai tejtermelés határozza hatások beleértve modellt.*

24. ábrán látható adatok kitöltése ahelyett, hogy ezzel. A trendek és a hatása (havi változat) határozza meg a ésszerű.

A modell ellenőrzése, mint most rá egy pillantást a például. Kiszámítja a két modell az előre jelzett értékek, a például a határozza modell kiszámítja és majd tevékenységtérkép ezeket a betanítási adatok például a következő kódot.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

A maradék rajzot 25. ábra jelenik meg.

![Például a határozza modell a tanítási adatokat](./media/r-quickstart/unnamed-chunk-21.png)

*25. ábra. Például a határozza modell a tanítási adatokat.*

Ezeket például hely ésszerű. Nem adott struktúra, kivéve a hatását, hogy a 2008-2009 recesszió, amely a modell nem derül ki van különösen jól.

A megjelenő ábra 25 rajzot bármely időfüggő minták észlelésére a például a. Az explicit módszert is, amely a használt például számítási és a például a rajzolási sorrendben idő helyezi. Ha, másrészt I kellett ábrázolt `milk.lm$residuals`, a rajzolási nem volna időrendjét.

Is `plot.lm()` diagnosztikai előkészítésére sorozata létrehozásához.

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Ez a kód 26. ábrán látható diagnosztikai előkészítésére sorozata eredményez.

![Diagnosztikai előkészítésére határozza modell elsején](./media/r-quickstart/unnamed-chunk-221.png)

![Diagnosztikai előkészítésére határozza modell második](./media/r-quickstart/unnamed-chunk-222.png)

![A diagnosztikai előkészítésére határozza modell harmadik](./media/r-quickstart/unnamed-chunk-223.png)

![A diagnosztikai előkészítésére határozza modell negyedik](./media/r-quickstart/unnamed-chunk-224.png)

*26. ábra. Diagnosztikai tevékenységtérkép határozza modell.*

Nincsenek ezek előkészítésére, de nem végezhető el a kiváló aggodalomra azonosított néhány magas segíti elő pontok. További láthatja a normál Q-Q rajzot az, hogy a például a Bezárás gombra a szokásos módon elosztott, egy fontos feltételezésen lineáris modellek-e.

### <a name="forecasting-and-model-evaluation"></a>Előrejelzési és modell kiértékelése
Nincs elegendő egyetlen dolog példánkban befejezéséhez. Igazolnia kell a számítási előrejelzések, és mérheti a hiba a tényleges adatok alapján. Az előrejelzés 2013 12 hónapig lesz. Ez az előrejelzés a tényleges adatok, amely nem része az képzési adatkészletet hiba intézkedésként számíthatja azt. Emellett azt is vetik össze a 18 évnyi vizsgálati adatok számított 12 hónapon tanítási adatokat.  

A metrikák számú idő adatsorozat modellek teljesítményének méréséhez szolgálnak. A mi esetünkben használjuk a négyzetes (RMS) által jelzett hibát. A következő függvény kiszámítja a két sorozat közötti RMS hiba.  

    RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
      ## Function to compute the RMS error or difference between two
      ## series or vectors

      messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                    "ERROR: Input vector to function RMS.error is too short",
                    "ERROR: Input vectors to function RMS.error must be of same length",
                    "WARNING: Funtion rms.error has received invald input time series.")

      ## Check the arguments
      if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
        warning(messages[1])
        return(NA)}

      if(length(series1) < min.length) {
        warning(messages[2])
        return(NA)}

      if((length(series1) != length(series2))) {
           warning(messages[3])
        return(NA)}

      ## If is.log is TRUE exponentiate the values, else just copy
      if(is.log) {
        tryCatch( {
          temp1 <- exp(series1)
          temp2 <- exp(series2) },
          error = function(e){warning(messages[4]); NA}
        )
      } else {
        temp1 <- series1
        temp2 <- series2
      }

     ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute the RMS error in a dataframe
      tryCatch( {
        sqrt(sum((temp1 - temp2)^2) / length(temp1))},
        error = function(e){warning(messages[4]); NA})
    }

Például a `log.transform()` "Érték átalakítások" szakaszban tárgyalt funkció nincs elég nagy mennyiségű ellenőrzése és a kivétel helyreállítási hibakód ebben a függvényben. Ezeket az alapelveket alkalmazott azonosak. A munkát csomagolni két helyen `tryCatch()`. Először idő adatsorokat exponentiated, mivel azt dolgozott az értékek a naplókat. Második a tényleges RMS hiba számított.  

Most egy olyan függvényt felszerelt méréséhez az RMS-hiba, hozza létre és egy dataframe az RMS a hibákat tartalmazó kimeneti. Kizárólag trend modell feltételek és a teljes modell határozza tényezők is. A következő kódot a feladat használatával hajtja végre a két lineáris modell, igazolnia kell kialakítani.

    ## Compute the RMS error in a dataframe
    ## Include the row names in the first column so they will
    ## appear in the output of the Execute R Script
    RMS.df  <-  data.frame(
    rowNames = c("Trend Model", "Seasonal Model"),
      Traing = c(
      RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
      RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
      Forecast = c(
        RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
        RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
    )
    RMS.df

    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('RMS.df')

Ezt a kódot futtató hoz létre, az eredmény adathalmaz kimeneti portjával. ábra 27 kimenettel.

![RMS hibák modellek összehasonlítása][26]

*27. ábra. RMS hibák modellek összehasonlítása.*

Ezekkel az eredményekkel, az látható, hogy a modell ad hozzá a határozza tényezők csökkenti az RMS hiba jelentősen. Nem túl beállítás a képzési adatok RMS hiba: jelző bit kisebb, mint az előrejelzéshez.

## <a id="appendixa"></a>FÜGGELÉK: Útmutató Rstudióból
Rstudióból meglehetősen megfelelően legyen dokumentálva, így a függelék I fogja adni az egyes hivatkozásokon a kulcs az első lépésekhez Rstudióból dokumentáció.

1. Projektek létrehozása
   
   Rendezheti és az R-kód a projektek Rstudióból használatával kezelni. A projektek használó dokumentációjában található https://support.rstudio.com/hc/articles/200526207-Using-Projects.
   
   Kövesse az alábbi utasításokat, és az R-Kódminták projekt létrehozása ebben a dokumentumban javasolt.  
2. Szerkesztés és az R-kód végrehajtása
   
   A szerkesztési és R-kód végrehajtása integrált környezetet biztosít az Rstudióból. Dokumentáció itt található https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.
3. Hibakeresés
   
   Rstudióból hatékony hibakeresési képességeket tartalmazza. Ezek a szolgáltatások dokumentációja jelenleg https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.
   
   A töréspont hibaelhárítási funkciók leírása a következő https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

## <a id="appendixb"></a>B függelék: További információ
Az R programozási oktatóanyag az Azure Machine Learning Studio az R nyelv használatával kell alapjait ismerteti. Ha nem ismeri a R, a CRAN két bevezetés érhetők el:

* R kezdőknek Emmanuel Paradis által a remek kezdőpont http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  
* R w n bemutatása Venables et. al. egy kicsit nagyobb mélysége hiányzóra változik http://cran.r-project.org/doc/manuals/R-intro.html.

Nincs olyan sok könyvek R, amelyik segíthet a kezdéshez. Íme néhány hasznos található:

* A kép R programozás: A bemutató a statisztikai szoftver kialakítása által Norman Matloff esetén programozásba a R. kiváló bemutatása  
* R Cookbook által Paul Teetor R. használatával probléma és a megoldás megközelítését ismerteti.  
* A művelet által Robert Kabacoff R egy másik hasznos bevezető címjegyzék-alkalmazásával. A kiegészítő gyors R webhely nem egy hasznos erőforrás http://www.statmethods.net/.
* R Inferno által Patrick égési, kezelje a legbonyolultabb és nehezen méretezhetővé foglalkozó témakörök, amelyek is lehet észlelt, amikor az r programozási számos érdekes vicces könyv A címjegyzék érhető el az ingyenes http://www.burns-stat.com/documents/books/the-r-inferno/.
* A részletes bemutatója a speciális témakörei R, van szükség egy pillantást a könyv speciális R Hadley Wickham által. A könyv online verziója rendelkezésre álló szabad http://adv-r.had.co.nz/.

A katalógus R idő adatsorozat csomagok adatsorozat elemzés CRAN feladat nézetben található: http://cran.r-project.org/web/views/TimeSeries.html. Adatsorozat objektum csomagok adott időben információt, hogy a csomag dokumentációjában kell hivatkozunk.

A címjegyzék bevezető Time Series Paul Cowpertwait és Andrew Metcalfe r bevezetője, amelyek adatsorozat elemzés R használja. Számos további elméleti szövegek R példákat.

Néhány nagy internetes erőforrásokhoz:

* DataCamp: DataCamp útmutatást ad az R a videó megszerzett és kódolási gyakorlatok a böngésző kényelmét. A legújabb R technikák és csomagok interaktív oktatóanyagok van. A szabad interaktív R oktatóanyag következő igénybe https://www.datacamp.com/courses/introduction-to-r
* Útmutató, kezdeti Programiz az R használatába https://www.programiz.com/r-programming
* A Clarkson egyetemi Tibor fekete által gyors R oktatóanyag http://www.cyclismo.org/tutorial/R/
* 60 + R felsorolt erőforrások: http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/r-quickstart/fig1.png
[2]: ./media/r-quickstart/fig2.png
[3]: ./media/r-quickstart/fig3.png
[4]: ./media/r-quickstart/fig4.png
[5]: ./media/r-quickstart/fig5.png
[6]: ./media/r-quickstart/fig6.png
[7]: ./media/r-quickstart/fig7.png
[8]: ./media/r-quickstart/fig8.png
[9]: ./media/r-quickstart/fig9.png
[10]: ./media/r-quickstart/fig10.png
[11]: ./media/r-quickstart/fig11.png
[12]: ./media/r-quickstart/fig12.png
[13]: ./media/r-quickstart/fig13.png
[14]: ./media/r-quickstart/fig14.png
[15]: ./media/r-quickstart/fig15.png
[16]: ./media/r-quickstart/fig16.png
[17]: ./media/r-quickstart/fig17.png
[18]: ./media/r-quickstart/fig18.png
[19]: ./media/r-quickstart/fig19.png
[20]: ./media/r-quickstart/fig20.png
[21]: ./media/r-quickstart/fig21.png
[22]: ./media/r-quickstart/fig22.png

[26]: ./media/r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
