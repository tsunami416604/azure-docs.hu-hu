---
title: Az R nyelvhez a rövid útmutató
titleSuffix: Azure Machine Learning Studio
description: Az R programozási oktatóanyag segítségével gyorsan az R nyelv-használatának az Azure Machine Learning Studio-előrejelzési megoldás létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/06/2017
ms.openlocfilehash: 1c0035b959cf08253e92eb5dade36fc3d020ab25
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818595"
---
# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning-studio"></a>Gyors üzembe helyezési oktatóanyag az R programozási nyelv, az Azure Machine Learning Studióban

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Bevezetés
Ez a rövid útmutató segítségével gyorsan elindíthatja az Azure Machine Learning Studio kiterjesztése az R programozási nyelv használatával. Ez az oktatóanyag az R programozási hozhat létre, tesztelheti és Studión belül R-kód végrehajtása. Végzett munka, az oktatóanyagokat, létrehozhat egy teljes előrejelzési megoldás a Studióban az R nyelv használatával.  

A Microsoft Azure Machine Learning Studio számos hatékony gépi tanulási és adatmanipulációs modulokat tartalmaz. A hatékony R nyelvvel, a nyelv franca Analytics leírása. Boldogan elemzési és adatkezelési a Studióban is kiterjeszthető az R. Ebben együttes használata biztosít a méretezhetőség és a könnyű telepíthetőségét a Studio rugalmasságával és részletes elemzést R.



### <a name="forecasting-and-the-dataset"></a>Előrejelzés és az adatkészlet
Előrejelzés a széles körben alkalmazott és igen hasznos elemzési módszer. Közös értékesítési szezonális elemek optimális leltári állományt, jelezni makrogazdasági változók meghatározása előrejelzésére közé eső használ. Előrejelzés általában time series modellel történik.

Idősorozat-adatok az adatokhoz, amelyben az értékeket egy idő index rendelkezik. Az idő index is lehetnek, például minden hónap vagy percenként, vagy nem. Egy idősorozat-modell idősorozat-adatok alapján. Az R programozási nyelv tartalmaz egy idősorozat-adatok széles körű elemzés és rugalmas keretében.

A jelen rövid útmutatóban azt fogja California tejtermelésre használata és díjszabási adatokat. Ezeket az adatokat több tejelő termékek és tejzsír, a teljesítményteszt hagyományos az ár havi információkat biztosít.

Ebben a cikkben R-szkriptek, együtt használt adatokat lehet [itt letöltött](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/blob/master/studio-samples/cadairydata.csv). Ezeket az adatokat eredetileg synthesized volt a University of Wisconsin címen elérhető információk https://dairymarkets.com.

### <a name="organization"></a>Szervezet
Számos lépést azt fogja halad, megtudhatja hogyan hozhat létre, tesztelheti és elemzési és adatkezelési R-kód végrehajtása az Azure Machine Learning Studio-környezetben.  

* Először az Azure Machine Learning Studio-környezet az R nyelv használatával kapcsolatos alapfogalmakat fog tárgyaljuk.
* Ezután azt halad megvizsgálja az üzemeltetés minőségének különböző szempontjait i/o-adatok, R-kód és a grafikai az Azure Machine Learning Studio-környezetben.
* Azt fogja majd hozhatnak létre az első rész az előrejelzési megoldás adattisztítást és adatátalakítást kódjának létrehozásával.
* Az előkészített adatok végezzük el a korrelációk között számos, a változókat az adathalmaz elemzése.
* Végül létre fogunk hozni egy szezonális time series előrejelzési modell tejtermelésre.

## <a id="mlstudio"></a>Az R nyelvhez a Machine Learning Studio használatához
Ez a szakasz végigvezeti néhány alapjait az R programozási nyelv, a Machine Learning Studio-környezet használata. Az R nyelv hozhat létre testreszabott analytics és data adatkezelési modulok az Azure Machine Learning Studio környezetből hatékony eszközt biztosít.

Az RStudio fejlesztéséhez, teszteléséhez és kis léptékű R-kód hibaelhárítása a fogom használni. Ez a kód ezután Kivágás és beillesztés, egy [R-szkript végrehajtása] [ execute-r-script] modul a Machine Learning Studióban futtatható.  

### <a name="the-execute-r-script-module"></a>Az R-szkript végrehajtása modul
Machine Learning studióban R-parancsfájlok futtathatók a [R-szkript végrehajtása] [ execute-r-script] modul. Példa a [R-szkript végrehajtása] [ execute-r-script] modul a Machine Learning Studióban az 1. ábrán látható.

 ![Az R programozási nyelv: Az R-szkript végrehajtása modul kiválasztva a Machine Learning Studióban](./media/r-quickstart/fig1.png)

*1. ábra A Machine Learning Studio környezet az R-szkript végrehajtása modul kiválasztva megjelenítése.*

1. ábra hivatkozó, nézzük meg a legfontosabb elemei a Machine Learning Studio-környezetben való munkához némelyike a [R-szkript végrehajtása] [ execute-r-script] modul.

* A modul a kísérletben a középső ablaktáblán jelennek meg.
* A jobb oldali panel felső részén egy ablakban megtekintheti és szerkesztheti az R-szkriptek tartalmazza.  
* Jobb oldali panel alsó részén látható néhány tulajdonságát a [R-szkript végrehajtása][execute-r-script]. A hiba és kimeneti naplók az ezen a panelen megfelelő kritikus pontok elkerülése érdekében kattintva tekintheti meg.

Hogy természetesen lesznek, megvizsgálni a [R-szkript végrehajtása] [ execute-r-script] Ez a dokumentum további részében részletesebben.

Az összetett R funkciók használatakor javasolt, hogy szerkeszti, tesztelése és hibakeresése az RStudio. Csakúgy, mint bármely szoftverfejlesztői növekményes kiterjesztheti a kódot, és kis egyszerű vizsgálati eseteknél tesztelni. Majd kivágja és beilleszti a függvények a R szkriptablakba, a [R-szkript végrehajtása] [ execute-r-script] modul. Ez a megközelítés lehetővé teszi az RStudio integrált fejlesztőkörnyezet (IDE) és az Azure Machine Learning Studio hatékonyságát is kihasználhatja.  

#### <a name="execute-r-code"></a>Hajtsa végre az R-kód
Az összes R-kód a [R-szkript végrehajtása] [ execute-r-script] modul hajtja végre, amikor a kísérlet futtatásához kattintson a a **futtatása** gombra. Végrehajtás befejezése után egy pipa jelenik meg a [R-szkript végrehajtása] [ execute-r-script] ikonra.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Az Azure Machine Learning védelmi vonalként R programozási
Tegyük fel, a webszolgáltatás az R-kód az Azure Machine Learning Studio használatával fejleszt, mindenképp tervez módját a kód foglalkozni fog egy nem várt adatok bemeneti és a kivételeket. Érthetőség kedvéért I nem szereplő átszállítást megakadályozzák az ellenőrzési vagy kivételkezelés látható hitelesítésikód-példák a legtöbb nagy részét. Azonban, a Folytatás e kap funkciók számos példát R a kivételkezelő funkció használatával.  

A teljes R kivételkezelés kezelésére van szüksége, ha javasolni tudom olvassa el a alkalmazni szakaszait szereplő Wickham könyve [B függelék – további olvasási](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Hibakeresés és R tesztelése a Machine Learning Studióban
Kifejezni, javasolni tudom a teszteléshez, és a hibakereséshez az R-kód az RStudio kis léptékű. Előfordulhatnak azonban olyan esetekben, ahol lesz nyomon követheti az R-kód jelentkező a [R-szkript végrehajtása] [ execute-r-script] magát. Ezenkívül mindig érdemes ellenőrizni az eredményeket a Machine Learning Studióban.

Az R-kód és az Azure Machine Learning Studio platformon a végrehajtás kimenetének elsősorban kimenetét található. További információkat error.log lesz látható.  

Ha hiba történik a Machine Learning Studióban az R-kód futtatása során, az első lépések error.log meg kell lennie. Ez a fájl tartalmazhat hasznos hibaüzenetek segítségével megismerheti, és javítsa ki a hibát. Error.log megtekintéséhez kattintson a **hiba napló megtekintése** a a **tulajdonságait tartalmazó ablaktáblán** számára a [R-szkript végrehajtása] [ execute-r-script] a hibát tartalmazó.

Például futtattam a következő R-kód egy nem definiált változó y a egy [R-szkript végrehajtása] [ execute-r-script] modul:

    x <- 1.0
    z <- x + y

Ez a kód nem tud végrehajtani, hibát eredményez. Kattintson a **hiba napló megtekintése** a a **tulajdonságait tartalmazó ablaktáblán** hoz létre a megjelenítés, a 2. ábrán látható.

  ![Chybová zpráva merül fel](./media/r-quickstart/fig2.png)

*2. ábra Chybová zpráva előugró.*

Úgy tűnik, ellenőriznünk kell a kimenetét, az R hibaüzenet jelenik meg. Kattintson a a [R-szkript végrehajtása] [ execute-r-script] majd kattintson a a **kimenetét megtekintése** az elemet a **tulajdonságait tartalmazó ablaktáblán** jobbra. Megnyílik egy új böngészőablakot, és a következők láthatók.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ez a hibaüzenet meglepetések tartalmaz, és egyértelműen azonosítja a probléma.

Vizsgálja meg az érték minden R-objektum, kinyomtathatja ezeket az értékeket a kimenetét fájlt. Objektum értékek vizsgálata a szabályok a következők ugyanaz, mint az interaktív R-munkamenet. Például ha sorba írja be a változó nevét, az objektum értéke nyomtat a kimenetét fájlt.  

#### <a name="packages-in-machine-learning-studio"></a>A Machine Learning Studio-csomagok
Studio több mint 350 előre telepített R nyelvi csomagokat tartalmaz. A következő kódot használhatja a [R-szkript végrehajtása] [ execute-r-script] modul lekérni az előre telepített csomagok listáját.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Ha ez a kód utolsó sora jelenleg nem ismeri, akkor Olvasson tovább. Ez a dokumentum többi nagymértékben ismertetik a Studio-környezet az r nyelv használatát.

### <a name="introduction-to-rstudio"></a>Az RStudio bemutatása
Az RStudio egy széles körben használt IDE az r segítségével Az RStudio szerkesztését, teszteléséhez és a hibakeresés néhány rövid útmutatóban használt R-kód használok. Után az R-kód tesztelt, és készen áll a is egyszerűen kimásolni, majd illessze be a RStudio szerkesztőből egy Machine Learning studióba [R-szkript végrehajtása] [ execute-r-script] modul.  

Ha nem rendelkezik az R programozási nyelv, telepítve van a asztali gépén, javasolni tudom, tegye meg most. Ingyenes, nyílt forráskódú R nyelvvel érhető el, a átfogó R Archive Network (CRAN), [ http://www.r-project.org/ ](http://www.r-project.org/). Nincsenek elérhető Windows, Mac OS és a Linux-/ UNIX letöltéseket. Válasszon egy közeli tükrözött, és kövesse a letöltési utasításokat. Emellett a CRAN rengeteg hasznos elemzési és adatkezelési csomagokat tartalmazza.

Ha most ismerkedik az RStudio, töltse le és telepítse az asztali verziót. Az RStudio letölti a Windows, Mac OS és a Linux-/ UNIX címen található http://www.rstudio.com/products/RStudio/. Kövesse az utasításokat az RStudio telepítése asztali gépén.  

Elérhető legyen a RStudio oktatóanyag bemutatását https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

További információkat az RStudio használatával megadom [függelék][appendixa].  

## <a id="scriptmodule"></a>Az R-szkript végrehajtása modul adataikkal adatok lekérése
Ebben a szakaszban ismertetjük be és ki, hogyan juthat adatokat a [R-szkript végrehajtása] [ execute-r-script] modul. Ellenőrizzük pályázatát, olvassa el a be- és kijelentkezés a különböző adattípusoknak kezelése a [R-szkript végrehajtása] [ execute-r-script] modul.

Ez a szakasz a teljes kódját a korábban letöltött zip-fájl szerepel.

### <a name="load-and-check-data-in-machine-learning-studio"></a>Betölteni, és ellenőrizze az adatokat a Machine Learning Studióban
#### <a id="loading"></a>Az adatkészlet betöltése
Fog betöltjük a **csdairydata.csv** fájlt az Azure Machine Learning studióba.

* Indítsa el az Azure Machine Learning Studio környezetet.
* Kattintson a **+ új** a képernyőn, és válassza a bal alsó **adatkészlet**.
* Válassza ki **helyi fájlból**, majd **Tallózás** válassza ki a fájlt.
* Győződjön meg arról, hogy a kiválasztott **fejléccel (.csv) általános CSV-fájl** a az adatkészlet típusa.
* Kattintson a pipa jelre.
* Az adatkészlet feltöltése után az új adatkészletre kattintva megjelenik a **adatkészletek** fülre.  

#### <a name="create-an-experiment"></a>Kísérlet létrehozása
Most, hogy bizonyos adatokat a Machine Learning Studióban, kell hozni egy kísérletet az elemzés végrehajtásához.  

* Kattintson a **+ új** a alacsonyabb bal oldalon, majd válassza a **kísérlet**, majd **üres kísérlet**.
* Nevezze el a kísérletben kiválasztásával, és módosítja, a **a létrehozott kísérlet...**  címe a lap tetején. Például megváltoztathatja, hogy **hitelesítésszolgáltató tejtermék elemzési**.
* Az oldal bal oldalán a kísérletet, bontsa ki a **mentett adatkészletek**, majd **saját adatkészletek**. Megtekintheti a **cadairydata.csv** korábban feltöltött.
* Húzza át a **csdairydata.csv adatkészlet** alakzatot a kísérletet.
* Az a **keresési elemek kísérletezhet** felső részén a bal oldali panelen írja be a mezőbe [R-szkript végrehajtása][execute-r-script]. A modul jelennek meg a keresési lista jelenik meg.
* Húzza át a [R-szkript végrehajtása] [ execute-r-script] alakzatot a raklapos modul.  
* Csatlakozzon a kimenetét a **csdairydata.csv adatkészlet** a bal oldali bemeneti (**Dataset1**), a [R-szkript végrehajtása][execute-r-script].
* **Ne felejtse el, kattintson a "Mentés"!**  

Ezen a ponton a kísérlet várható megjelenése a 3. ábra.

![A hitelesítésszolgáltató tejtermék Analysis adatkészlet és R-szkript végrehajtása modul kísérletezhet](./media/r-quickstart/fig3.png)

*3. ábra A hitelesítésszolgáltató tejtermék Analysis adatkészlet és R-szkript végrehajtása modul kísérletezhet.*

#### <a name="check-on-the-data"></a>Az adatok ellenőrzése
Nézzük meg, az adatok betöltése a kísérletbe sikerült rendelkezik. A kísérletben, kattintson a kimenete a **cadairydata.csv adatkészlet** válassza **megjelenítése**. 4. ábra hasonló üzenet jelenik meg.  

![A cadairydata.csv adatkészlet összefoglalása](./media/r-quickstart/fig4.png)

*4. ábra A cadairydata.csv adatkészlet összefoglalása.*

Ebben a nézetben látható sok hasznos adat. Láthatjuk, hogy az adatkészlet több első sorát. Ha kiválasztjuk az oszlopot, a a statisztikák szakasz bemutatja az oszlop további információt. Például a szolgáltatástípus sor megmutatja milyen adattípusokat, az oszlop az Azure Machine Learning Studio rendelve. Ilyen gyorsan át, akkor jó megerősítést ellenőrzi azt ehhez súlyos munka megkezdése előtt.

### <a name="first-r-script"></a>Első R-szkript
Hozzunk létre egy egyszerű első R-szkriptet az Azure Machine Learning Studióban kísérletezhet. Létrehozott és tesztelni a következő szkriptet az RStudio.  

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

Most át kell adnom a szkript az Azure Machine Learning Studióban. Sikerült egyszerűen Kivágás és beillesztés. Azonban ebben az esetben fogja átvinni saját R-szkript használatával egy zip-fájlt.

### <a name="data-input-to-the-execute-r-script-module"></a>Az R-szkript végrehajtása modul bemeneti adatokhoz
Nézzük meg, a bemeneti adatok, a [R-szkript végrehajtása] [ execute-r-script] modul. Ebben a példában a kaliforniai tejelő adatok beolvasása a [R-szkript végrehajtása] [ execute-r-script] modul.  

Nincsenek három lehetséges bemenetek a [R-szkript végrehajtása] [ execute-r-script] modul. Használhat bármilyen egyikét vagy mindegyikét ezeket a bemeneteket az alkalmazástól függően. Emellett akkor is tökéletesen ésszerű nincs bemenet minden szükséges R-szkriptek használata.  

Vizsgáljuk meg ezeket a bemeneteket, balról jobbra történik minden egyes. Minden egyes bemenet neveinek megtekintéséhez helyezi a kurzort a bemeneti keresztül, és az elemleírásban olvasása.  

#### <a name="script-bundle"></a>Parancsfájl-csomag
A parancsfájl csomagot adjon meg lehetővé teszi, hogy adja át a tartalmát egy zip-fájlba történő [R-szkript végrehajtása] [ execute-r-script] modul. A zip-fájl tartalmát olvassa be az R-kód a következő parancsok egyikét használhatja.

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [!NOTE]
> Az Azure Machine Learning Studio fájlokat a zip-fájl kezeli, mintha azok az src / könyvtár, ezért a fájlneveket, a könyvtárnév a előtagot kell. Ha például a zip-fájl tartalmazza a fájlok `yourfile.R` és `yourData.rdata` a zip-fájl gyökerében szeretné megoldani ezeket az adatokat `src/yourfile.R` és `src/yourData.rdata` használatakor `source` és `load`.
> 
> 

Már beszéltünk betöltése adatkészleteket az [az adatkészlet betöltése](#loading). Miután létrehozott és tesztelni az R-szkriptet az előző szakaszban látható, tegye a következőket:

1. Az R-parancsfájl mentése egy. R-fájlt. Hívhatom meg a saját parancsfájl "simpleplot. R". Íme a tartalmak.
   
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
2. Hozzon létre egy zip-fájlt, és másolja a szkriptet a zip-fájlban. A Windows, kattintson a jobb gombbal a fájlra és válassza ki **küldése**, majd **tömörített mappa**. Ezzel létrehoz egy új zip-fájl, amely tartalmazza a "simpleplot. R"fájl.
3. Adja hozzá a fájlt, hogy a **adatkészletek** a Machine Learning Studióban, mint típusát megadó **zip**. Meg kell jelennie a zip-fájlt az adatkészletekben.
4. A zip-fájlt a áthúzása **adatkészletek** alakzatot a **gépi tanulás stúdió vászonalapú**.
5. Csatlakozzon a kimenetét a **zip-adatok** ikonra a **parancsfájl csomag** a bemeneti a [R-szkript végrehajtása] [ execute-r-script] modul.
6. Írja be a `source()` függvényt a code ablakának be a zip-fájl neve a [R-szkript végrehajtása] [ execute-r-script] modul. Abban az esetben a beírt `source("src/simpleplot.R")`.  
7. Ellenőrizze, hogy **mentése**.

Ha befejeződött, ezeket a lépéseket a [R-szkript végrehajtása] [ execute-r-script] modul végrehajtja az R-szkriptet a zip-fájlban a kísérlet futtatásakor. Ezen a ponton a kísérlet várható megjelenése 5. ábra.

![Kísérletezzen a tömörített R-szkript használatával](./media/r-quickstart/fig6.png)

*5. ábra A kísérlet tömörített R-parancsfájl használatával.*

#### <a name="dataset1"></a>Dataset1
Az R-kód egy téglalap alakú táblát a Dataset1 bemeneti használatával adhat át. Az egyszerű szkript a `maml.mapInputPort(1)` függvény beolvassa az adatokat a porthoz 1. Ezeket az adatokat ezután hozzá van rendelve egy adathalmaz változó neve, a kódban. Az egyszerű szkript a kód első sorában a hozzárendelés hajt végre.

    cadairydata <- maml.mapInputPort(1)

Hajtsa végre a kísérlethez kattintva a **futtatása** gombra. Ha a végrehajtás befejezését követően kattintson a a [R-szkript végrehajtása] [ execute-r-script] modult, majd kattintson **kimeneti napló megtekintése** a Tulajdonságok panelen. Egy új lapon kell megjelennie a böngészőben a kimenetét fájl tartalmának megjelenítése. Görgessen le, amikor megjelenik a következőhöz hasonló.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Brazíliáénál, az oldal a részletesebb információkat az oszlopot, így fog kinézni a következőhöz hasonló.

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

Ezekkel az eredményekkel megfelelően működik, 228 észrevételek és 9 oszlop az adathalmaz a leginkább vannak. Láthatjuk, hogy az oszlopok neveit, az R-adattípus és az egyes oszlopok mintát.

> [!NOTE]
> A nyomtatott ugyanazzal a hibaüzenettel kényelmesen érhető el az R-eszköz kimenetéből származó a [R-szkript végrehajtása] [ execute-r-script] modul. A kimeneteinek ismertetik a [R-szkript végrehajtása] [ execute-r-script] modul a következő szakaszban.  
> 
> 

#### <a name="dataset2"></a>Dataset2
A Dataset2 bemeneti működése megegyezik-e Dataset1. A bemeneti, az R-kód egy második téglalap alakú táblát adhat át. A függvény `maml.mapInputPort(2)`, 2 argumentummal, amellyel át ezeket az adatokat.  

### <a name="execute-r-script-outputs"></a>Hajtsa végre az R-szkript kimenetek
#### <a name="output-a-dataframe"></a>Kimeneti dataframe
Készíthető egy R-adathalmaz tartalmának a eredmény Dataset1 porton keresztül téglalap alakú táblázatként használatával a `maml.mapOutputPort()` függvény. Az egyszerű R-szkript ez hajtja végre a következő sort.

    maml.mapOutputPort('cadairydata')

Miután a kísérletet, kattintson az eredmény Dataset1 kimeneti portjára, és kattintson a **Visualize**. 6. ábra hasonló üzenet jelenik meg.

![A kimenet a kaliforniai tejelő adatok a Vizualizáció](./media/r-quickstart/fig7.png)

*6. ábra A kimenet a kaliforniai tejelő adatok a Vizualizáció.*

Ez a kimenet a bemeneti azonos keres, pontosan a várt módon.  

### <a name="r-device-output"></a>Az R az eszközkimenetre
Az eszköz kimenete a [R-szkript végrehajtása] [ execute-r-script] modul kimeneti üzenetek és a grafikai tartalmaz. Mindkét standard kimeneti és a standard hiba üzeneteit R R eszköz kimeneti portját érkeznek.  

Az R-eszköz kimenet megtekintéséhez kattintson a porton majd a **Visualize**. Láthatjuk, hogy a standard kimeneti és a standard hiba a 7. ábra az R-szkriptet.

![Standard kimenet és az R-eszköz portról standard hiba](./media/r-quickstart/fig8.png)

*7. ábra Standard kimenet és az R-eszköz portról standard hiba.*

Görgetés lefelé, hogy az R-szkriptet a 8. ábrán a grafikus kimenetének megtekintése.  

![Az R-eszköz port grafikus kimenete](./media/r-quickstart/fig9.png)

*8. ábra Az R-eszköz port kimenete grafikus.*  

## <a id="filtering"></a>Adatok szűrése és átalakítása
Ebben a szakaszban fogunk végrehajtani, néhány alapvető adatok szűrése és átalakítási műveletek a kaliforniai tejelő adatokon. Ez a szakasz végén kell adatokat egy analitikus modell létrehozásához megfelelő formátumú.  

Pontosabban, ebben a szakaszban végezzük el számos gyakori adatok tisztítása és átalakítása feladatok: írja be a szűrést dataframes, hozzáadás, új számított oszlopok, az átalakítási és az értéke átalakításokat. Ez a háttér segítenek a való életből vett problémákat észlelt számos változata létezik foglalkozik.

Ez a szakasz a teljes R-kód a korábban letöltött zip-fájl érhető el.

### <a name="type-transformations"></a>Írja be az átalakítások
Most, hogy azt a kaliforniai tejelő adatok olvashatja be az R-kód a [R-szkript végrehajtása] [ execute-r-script] modul kell ügyeljen arra, hogy az oszlopok adatait a kívánt típusát és formátumát.  

Az "R" dinamikusan gépelt nyelv, ami azt jelenti, hogy az adattípusok vannak kényszerítése egy másikba, szükség szerint. Az atomi adattípusok R a numerikus, logikai és karakter közé tartozik. A szolgáltatás típus compactly a kategorikus adatok tárolására szolgál. Az adattípusok sokkal több adatot található szereplő hivatkozások [B függelék – további információ](#appendixb).

Be külső forrásból származó R tabulált elolvasásáról, mindig érdemes ellenőrizni az eredményül kapott típusok az oszlopok. Érdemes lehet egy oszlop típusa karakter, de a legtöbb esetben ez fog megjelenni tényezőként vagy fordítva. Más esetekben egy oszlopot úgy gondolja, hogy legyen numerikus által jelölt karakteres adatot, például: "1,23" helyett, lebegőpontos 1,23 pont számát.  

Szerencsére a könnyebbé vált a átalakítani egy típust a másikra, mindaddig, amíg leképezés nem lehetséges. Például nem lehet "Nevada" átalakítása numerikus érték, de átválthat egy tényező (kategorikus változó). Másik példaként egy numerikus 1 átalakítása "1" karaktert vagy tényezőt.  

Ezek az átalakítások bármelyikét szintaxisa a következő egyszerű: `as.datatype()`. Ezek írja be a konverziós függvények a következők:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Az adattípusok, az oszlopok, hogy adjon meg az előző szakaszban megnézzük: az összes oszlop van, kivéve az oszlop, "Hónap", típus karakter, ami feliratú numerikus típusú. Nézzük konvertálja az a tényező, és a teszteredmények.  

Tudom, hogy törölték a teszteredményekből mátrix létrehozott, és hozzá egy sort egy tényező a "Hónap" oszlop átalakítása vonal. Kísérletemben van fogja csak kivágása és az R-kódokat illessze be a code-ablak a [R-szkript végrehajtása] [ execute-r-script] modul. Is sikerült frissíteni a zip-fájlt, és töltse fel az Azure Machine Learning Studióban, de ez több lépést tart.  

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

Nézzük hajtsa végre ezt a kódot, és tekintse meg a kimeneti naplót a R-parancsfájl. 9. ábra jelenik meg a vonatkozó adatokat a naplófájlból.

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

*9. ábra. Az adathalmaz tényező változóhoz összefoglalása.*

A típus hónapban most a következő: "**14 szintek használatával tényező**". Ez a probléma, mivel csak 12 hónappal az évben. Is ellenőrizheti, hogy a típus a **Visualize** az eredmény adatkészlet port van "**Kategorikus**".

A probléma, hogy a "Hónap" oszlop nem rendszeresen lett kódolt. Bizonyos esetekben egy hónapban április nevezik, és más, Diagramhalmazban rövidítéseket tartalmaz. Azt is megoldhatja a problémát úgy 3 karaktert a karakterlánc-csonkolás. A sor kód most az alábbihoz hasonló:

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Futtassa újra a kísérletet, és megtekintheti a kimeneti naplót. A várt eredmény 10. ábrán látható.  

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

*10. ábra. Az adathalmaz tényező szintek megfelelő számú összefoglalása.*

A szolgáltatás a változó most már rendelkezik a kívánt 12 szinteket.

### <a name="basic-data-frame-filtering"></a>Alapvető adatok keret szűrése
R dataframes hatékony szűrési képességek támogatásához. Adatkészletek lehet részlegesen beágyazott logikai szűrők a sorok vagy oszlopok használatával. Sok esetben összetett szűrési feltételeket lesz szükség. A hivatkozások [B függelék – további információ](#appendixb) kiterjedt példát szűrésének adatkerettípusokat jelölhet.  

Van egy bittel szűrése az adatkészlet azt érdemes megtenni. Ha megtekinti a cadairydata dataframe oszlopok, látni fogja a két felesleges oszlopok. Az első oszlop csak egy sor száma, amely nem hasznos tárolja. A második oszlop Year.Month, redundáns információt tartalmaz. A Microsoft egyszerűen ezeket az oszlopokat a következő R-kód használatával zárhat ki.

> [!NOTE]
> Mostantól ebben a szakaszban csak bemutatom majd, a kód a hozzáadott a [R-szkript végrehajtása] [ execute-r-script] modul. Az új sorok veszek fel **előtt** a `str()` függvény. Ez a funkció az Azure Machine Learning Studióban az eredményeket használni.
> 
> 

A saját R-kód adhatok hozzá a következő sort a [R-szkript végrehajtása] [ execute-r-script] modul.

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Futtassa a következő kódot a kísérlet során, és ellenőrizze az eredményt a kimeneti naplóból. Ezekkel az eredményekkel 11. ábrán látható.

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

*11. ábra. A két oszlop eltávolítva az adathalmaz összefoglalása.*

Jó hírünk van! A várt eredményt kapunk.

### <a name="add-a-new-column"></a>Egy olyan új oszlop hozzáadása
Time series modellek létrehozásának eljárásait lesz kényelmes, az idősorozat elindítása óta a hónapok nevét tartalmazó oszlop. Egy olyan új oszlop "Month.Count" hozunk létre.

A kód hozunk létre az első egyszerű függvény rendszerezésének elősegítése érdekében `num.month()`. Ez az adathalmaz egy új oszlop létrehozása a függvény ezután kerülnek érvényre. Az új kódot a következőképpen történik.

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

Most futtassa a frissített kísérletet, és az eredmények megtekintéséhez használja a kimeneti naplót. Ezekkel az eredményekkel 12. ábra láthatók.

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

*12. ábra. Az adathalmaz további oszlopok összefoglalása.*

Úgy tűnik, minden jól működik. A várt értékek az új oszlopot az adathalmaz a rendelkezünk.

### <a name="value-transformations"></a>Érték átalakítások
Ebben a szakaszban végezzük el néhány egyszerű átalakítások néhány az adatkerettípust oszlop értékeit. Az R nyelv gyakorlatilag tetszőleges érték átalakítások támogatja. A hivatkozások [B függelék – további információk](#appendixb) kiterjedt példát.

Ha megtekinti a dataframe összegzéseinek értékei alábbihoz páratlan itt. További az ice cream tej, mint a kaliforniai állítja elő? Nem, természetesen ez nincs értelme, mert távozik, emiatt előfordulhat, hogy nem egyes velünk a kapcsolatot az ice cream lovers. Az egységek eltérnek. A szolgáltatás díja az USA-egységekben font, tej van egység: 1 millió amerikai font, ice cream 1000-es egységekben gallon NEKÜNK, pedig túró 1000 USA font-egységekben. Ha az ice cream tömege fogyasztási készül 6.5-ös font, azt könnyen megteheti a szorzás konvertálása ezeket az értékeket, így azok minden 1000 font egyenlő egységekben.

Az előrejelzési modell egy tényezőt modellt használjuk a trendek és a szezonális ezeket az adatokat. Log átalakítás lehetővé teszi számunkra, hogy ez a folyamat egyszerűsítése lineáris modellt. Ugyanezt a funkciót, ahol a Szorzó van alkalmazva a napló átalakítási műveletet alkalmazhatja azt.

Az alábbi kódot egy új függvény vonatkozó `log.transform()`, és alkalmazza azt a numerikus értékeket tartalmazó sorokat. Az R `Map()` függvény szolgál a alkalmazni a `log.transform()` függvényt, hogy a kijelölt oszlopokat a dataframe-típust. `Map()` hasonló `apply()` , de lehetővé teszi, hogy a függvény argumentumainak egynél több listáját. Vegye figyelembe, hogy szorzók listáját választékát kínálja, a második argumentum a `log.transform()` függvény. A `na.omit()` függvény győződjön meg arról, hogy nem rendelkeznek hiányzik vagy nem definiált értéket az adathalmaz a tisztítás egy kis használja.

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

Van még egy bit képzésén a `log.transform()` függvény. Ez a kód a legtöbb ellenőrzi az argumentumokkal potenciális problémákat vagy kivételeket, továbbra is a számítások során felmerülő többé vesződnie a sérült. Ez a kód csak néhány sornyi csinálni számításokat.

Defenzív programozás célja, hogy egy adott függvény, amely megakadályozza a folyamatos feldolgozása sikertelen. Lehet, hogy igen kellemetlen, a felhasználók számára egy egy hosszú ideig futó elemzési hirtelen hiba. Ezen helyzet elkerülése érdekében korlátozza, amely kárt alsóbb feldolgozási alapértelmezett visszatérési értékek kell választani. Egy üzenet is, hogy probléma merült fel felhasználókat jön létre.

Ha nem használta az R defenzív programozás, ez a kód előfordulhat, hogy kezdünk, egy kicsit. E lépésről lépésre a fő lépést:

1. Négy üzenet vektor van meghatározva. Ezek az üzenetek néhány lehetséges hibák és kivételek, amelyek ezzel a kóddal kapcsolatos információkat folytatott kommunikációra szolgálnak.
2. Visszatérés minden esetben az NA értéket. Nincsenek számos egyéb lehetőségeket, amelyeket kevesebb mellékhatással. E eredményezhetnek nullák vektor, vagy az eredeti bemeneti vektort, például.
3. A függvény ellenőrzéseket futtatja az argumentumokon. Minden esetben, ha hibát észlel, egy alapértelmezett értéket adja vissza és a által előállított üzenet a `warning()` függvény. Használok `warning()` helyett `stop()` , ez utóbbi leállíthatja végrehajtási, pontosan mit próbálok elkerülése érdekében. Vegye figyelembe, hogy e írt ezt a kódot egy eljárási tetszetős, ahogy ebben az esetben egy összetett és homályos tűnt működési megközelítés.
4. A napló számítások burkolja `tryCatch()` , hogy a kivételek nem okoz egy hirtelen halt a feldolgozásra. Nélkül `tryCatch()` váltotta ki R függvények eredménye stopjelzést, ezt végzi a legtöbb hibát.

Hajtsa végre az R-kód is futtathatja a kísérletet, és rendelkezik a nyomtatásra egy pillantást a kimenetét fájlban. Most már megjelenik az átalakított értékeket a négy oszlopot a naplóban, ahogyan a 13. ábra.

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

*13. ábra. Az adathalmaz átalakított értékek összegzése.*

Láthatjuk, hogy az értékek átalakítása rendelkezik. Most tejtermelés jelentősen meghaladja a minden más tejtermék éles környezetben, hogy mi most láthatjuk logaritmus skála visszahívása.

Ezen a ponton az adatok törlődnek, és bizonyos modellezési készen állunk. Adatkészlet eredmény kimenete összegzése a Vizualizáció megnézzük a [R-szkript végrehajtása] [ execute-r-script] modult, látni fogja a "Hónap" oszlop "Kategorikus" 12 egyedi értékekkel, újra épp úgy, hogy.

## <a id="timeseries"></a>Time series objektumok és korrelációs elemzés
Ebben a szakaszban azt fogja vizsgálata R idő néhány alapvető sorozat objektumok és elemzése a kapcsolatos néhány közötti összefüggéseket. Az a célunk, hogy a több késedelmes jelentések páros megfelelési adatait tartalmazó dataframe kimeneti.

Ez a szakasz a teljes R-kód a korábban letöltött zip-fájl szerepel.

### <a name="time-series-objects-in-r"></a>Time series objektumok az R
Mint már említettük, az idő az adatsorokat időpontig indexelt adatok értékeket. R time series objektumok létrehozása és kezelése az idő index szolgálnak. Nincsenek a time series objektumok használata számos előnnyel jár. Time series objektumok ingyenes, számos részleteit az idő index sorozatértékek, az objektum ágyazott kezelése. Emellett time series objektumok lehetővé teszik a számos time series metódusok használata a küldik az ábrázolást, nyomtatás, modellezés, stb.

A POSIXct time series osztály gyakran használják, és viszonylag egyszerű. A time series mértékek idő kezdete és az alapidőpont, 1970. január 1. osztály. Ebben a példában használjuk a POSIXct time series objektumokat. Más széles körben használt R time series objektum osztályok állatkert és xts, bővíthető idősorozat tartalmazza.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Time series objektum példa
Első lépések a fenti példában. Húzza át egy **új** [R-szkript végrehajtása] [ execute-r-script] modult is futtathatja a kísérletet. Csatlakozás az eredmény Dataset1 kimeneti portjára, a meglévő [R-szkript végrehajtása] [ execute-r-script] a Dataset1 modul bemeneti portjával az új [R-szkript végrehajtása] [ execute-r-script] modul.

Ahogyan az első példa a, hogy a példában keresztül, bizonyos pontokon bemutatom majd csak a növekményes további kódsort az egyes lépéseknél R-kód.  

#### <a name="reading-the-dataframe"></a>Az adathalmaz olvasása
Első lépésként hozzunk dataframe olvassa el, és győződjön meg arról, hogy a várt eredményt. A következő kódot a feladat kell tennie.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

Most futtassa a kísérletet. A napló az új R-szkript végrehajtása alakzat 14. ábra hasonlóan kell kinéznie.

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

*14. ábra. Az R-szkript végrehajtása modul az adathalmaz összefoglalása.*

Ezek az adatok nem a várt típusok és a formátuma. Vegye figyelembe, hogy a "Hónap" oszlop típusa tényező, és a várt számú szintek.

#### <a name="creating-a-time-series-object"></a>Egy time series-objektum létrehozása
A dataframe egy time series objektumot hozzá kell. Cserélje le az aktuális kód a következő, POSIXct osztály egy új oszlopot ad hozzá.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Most tekintse meg a naplót. 15. ábra hasonlónak kell lennie.

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

*15. ábra. Az adathalmaz-time series objektum összefoglalása.*

Láthatjuk, hogy a, amely az új oszlop van sőt osztály POSIXct összegzés.

### <a name="exploring-and-transforming-the-data"></a>Felfedezése és az adatok átalakítása
Vizsgáljuk meg néhányat a változókat, ez az adatkészlet. Teszteredményekből mátrix egy jó módja egy gyors pillantást előállításához. E vagyok cseréje a `str()` függvényt a következő sort az előző R-kód.

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Futtassa ezt a kódot, és lássuk, mi történik. A diagram R eszköz kikötő előállított 16. ábra hasonlóan kell kinéznie.

![Kijelölt változók Teszteredményekből mátrix](./media/r-quickstart/fig17.png)

*16. ábra. Kijelölt változók Teszteredményekből össze.*

Van néhány odd-looking struktúra a kapcsolatok között ezeket a változókat. Talán ez akkor merül fel az adatok a trendek pedig az a tény, hogy azt nem rendelkezik szabványosított a változókat.

### <a name="correlation-analysis"></a>Korrelációs elemzése
Korrelációs elemzést kell megszüntetéséhez trend és egységesítheti a változókat. Egyszerűen használhatja, hogy az R `scale()` függvény, amely azonos adatközpontban üzemelő és méretezi a változók is. Ez a funkció is előfordulhat, hogy gyorsabban futnak. Azonban szeretnék mutatni, az r egy olyan védelmi vonalként programozási példa

A `ts.detrend()` alábbi függvényt is ezeket a műveleteket hajt végre. A következő két sornyi kóddal megszüntetéséhez trend az adatokat, és majd szabványosíthatja az értékeket.

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

Van még egy bit képzésén a `ts.detrend()` függvény. Ez a kód a legtöbb ellenőrzi az argumentumokkal potenciális problémákat vagy kivételeket, továbbra is a számítások során felmerülő többé vesződnie a sérült. Ez a kód csak néhány sornyi csinálni számításokat.

Már beszéltünk rendelkezik az értéket az átalakítások defenzív programozás egy példát. Mindkét számítási blokkok burkolja `tryCatch()`. Bizonyos hibák logikus adja vissza az eredeti bemeneti vektort, és a többi nullák vektor visszatérés.  

Vegye figyelembe, hogy megszünteti trendelemzés használt lineáris regressziós modell futtatása egy time series regressziós. A előjelző változó egy time series objektumot.  

Egyszer `ts.detrend()` van definiálva az adathalmaz iránt változókhoz alkalmazunk. Azt kell alakíthatók át az eredményül kapott bejegyzéslista által létrehozott `lapply()` adatok dataframe használatával való `as.data.frame()`. Védelmi vonalként aspektusait miatt `ts.detrend()`, a változók egy folyamat sikertelen nem akadályozza meg a többi helyes feldolgozását.  

A végső kódsort páros teszteredményekből hoz létre. Után futtatja az R-kód, a teszteredményekből eredményei láthatók 17. ábra.

![Páros teszteredményekből megszüntetéséhez trendszerű és egységesített idősorok](./media/r-quickstart/fig18.png)

*17. ábra. Páros teszteredményekből megszüntetéséhez trendszerű és a szabványos sorozat harmadik része.*

Ezekkel az eredményekkel, 16. ábra szereplő termékektől össze lehessen hasonlítani. Az a tendencia eltávolítva és a változókat, szabványosított láthatjuk, sokkal kevesebb felépítését, ezek a változók kapcsolatai a.

A kód az R FTB objektumként összefüggéseket számítási a következőképpen történik.

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

A napló látható 18. ábra ezt a kódot futtató eredményez.

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

*18. ábra. FTB listája az páros korrelációs elemzés objektumokat.*

Nincs minden lag korrelációs értékét. Ezek az értékek korrelációs sem elég nagy legyen jelentős lehet. A Microsoft ezért kötnek, hogy azt az egyes változók modell egymástól függetlenül is.

### <a name="output-a-dataframe"></a>Kimeneti dataframe
R FTB objektumok listáját, hogy rendelkezik számított a páros összefüggéseket. Ez megjelenít probléma egy kis eredmény adathalmaz kimeneti portját valóban megköveteli egy adathalmaz. További FTB objektum maga listáját, és szeretnénk, hogy csak az értékeket a lista első elemében, a különböző késedelmes jelentések az összefüggéseket.

A következő kódot a késés értékek kibontása FTB objektumok, amelyek maguk is listák listájából.

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
    ## The following line works only in Azure Machine Learning Studio
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

A kód első sorára bonyolult egy kicsit, és néhány magyarázata is segítenek megérteni azt. Belülről kifelé működik az alábbiakkal:

1. A "**[[**"argumentummal üzemeltető"**1**" összefüggéseket, a késedelmes jelentések vektor kiválasztja az első elem FTB objektum listáját.
2. A `do.call()` függvény vonatkozik a `rbind()` keresztül a lista elemeinek függvény által visszaadott `lapply()`.
3. A `data.frame()` függvény típusú értékké konvertál, az eredmény által előállított `do.call()` dataframe való.

Vegye figyelembe, hogy a sor neveket az adathalmaz oszlopában. Így megtartja ezt a sort neveket, amikor azok kimenete a [R-szkript végrehajtása][execute-r-script].

A kimenetet a 19. ábra jelenik meg a kódot futtató hoz létre. Ha e **Visualize** a kimenetet a az adatkészlet eredmény port. A sor nevek szerepelnek az első oszlop helyesen.

![A korrelációs elemzési eredmények kimenete](./media/r-quickstart/fig20.png)

*19. ábra. Kimenete a korrelációs elemzési eredményeket.*

## <a id="seasonalforecasting"></a>Time series példa: szezonális előrejelzés
Az adatok már az elemzési alkalmas űrlapot, és hogy eldöntöttük, nincs jelentős talál összefüggéseket között a változókat. Nézzük lépés, és a egy idősor-előrejelzési modell létrehozása. Ez a modell használatával azt fogja előrejelzési California tejtermelés 2013 12 hónapig.

Az előrejelzési modell két összetevőből, egy trend összetevőt és időszakos lesz. A teljes előrejelzés, amely a két összetevőt. A modell típusa tényezőt modell néven ismert. Ez esetben az additív modell. A változók a lényeges, ami lehetővé teszi az elemzés tractable azt már telepített egy napló átalakítás.

Ez a szakasz a teljes R-kód a korábban letöltött zip-fájl szerepel.

### <a name="creating-the-dataframe-for-analysis"></a>Az adathalmaz elemzéshez létrehozása
Első lépésként adjon hozzá egy **új** [R-szkript végrehajtása] [ execute-r-script] modult a kísérletvászonra. Csatlakozás a **eredmény adatkészlet** kimenete a meglévő [R-szkript végrehajtása] [ execute-r-script] modult a **Dataset1** az új modul bemeneti. Az eredmény hasonlóan kell kinéznie 20. ábra.

![A kísérlet hozzáadott új R-szkript végrehajtása modullal](./media/r-quickstart/fig21.png)

*20. ábra. A kísérlet hozzáadott új R-szkript végrehajtása modullal.*

A korrelációs elemzésével az imént fejeződött be, hozzá kell adnunk a-sorozat objektum POSIXct idő oszlop. A következő kódot fog ehhez csupán.

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

Futtassa ezt a kódot, és tekintse meg a naplót. Az eredmény 21. ábra hasonlóan kell kinéznie.

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

*21. ábra. Az adathalmaz összegzését.*

Ez az eredmény igyekszünk készen áll az elemzéshez.

### <a name="create-a-training-dataset"></a>Betanítási adatkészletet létrehozása
Mivel az adathalmaz össze kell betanítási adatkészletet létrehozása. Ezeket az adatokat tartalmazza az összes az év 2013, az utolsó 12, kivéve a megfigyelések Ez az a tesztelési adatkészletnél. A következő kód részhalmazának az adathalmaz, és létrehozza a grafikon a termelési és az ár tejelő változók. Tudom a négy termelési grafikon létrehozása és változók díjszabása. Egy névtelen függvény segítségével határozza meg, néhány úgy bővíti az ábrázolást, és majd megismételheti a másik két argumentumok listájának `Map()`. Ha gondol, amely egy ciklus kellene rendelkeznie működtek itt, az Ön megfelelő. De mivel az R egy funkcionális nyelv mutatom meg, a funkcionális megközelítést.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

A time series jeleníti meg az R-eszköz kimenetében megjelennek a 22. ábra sorozata a kódot futtató eredményez. Vegye figyelembe, hogy időtengelye egységet a dátumok, a már jól néz kis előnye, hogy az adatsorozat ábrázolásához metódus idő.

![A time series grafikon California tejelő éles és az ár adatok első](./media/r-quickstart/unnamed-chunk-161.png)

![Time series grafikon California tejelő éles és az ár adatok száma másodpercenként](./media/r-quickstart/unnamed-chunk-162.png)

![A time series grafikon California tejelő éles és az ár adatok külső](./media/r-quickstart/unnamed-chunk-163.png)

![A time series grafikon California tejelő éles és az ár adatok negyedik](./media/r-quickstart/unnamed-chunk-164.png)

*22. ábra. Time series grafikon California tejtermelésre és ár-adatok.*

### <a name="a-trend-model"></a>A trend modell
Miután létrehozta a egy time series objektumot, és hogy megtekintette az adatokat, először a kaliforniai tej éles adatok trend modell összeállításához. Mi a time series regressziós módszerrel történő teheti meg. Azt azonban nincs bejelölve, a diagram, amely a rendszer több mint egy görbét kell és pontos modellezésére a betanítási adatok megfigyelt trendje intercept.

Adja meg a kis léptékű az adatok, e lesz a modell az RStudio trend és majd kivágja és beilleszti az eredményül kapott modell Azure Machine Learning studióba. Az RStudio biztosít egy interaktív környezetet az ilyen típusú interaktív elemzés céljából.

Első kísérlet alkalmával, mint a powers legfeljebb 3 polinom regresszió fogja meg. Nincs túlterhelt igyekeznek az ilyen típusú modellek valós veszélyt. Emiatt tanácsos magas rendelés feltételeit elkerülése érdekében. A `I()` függvény gátolja a tartalmának értelmezése (értelmezi a tartalma ","), és lehetővé teszi, hogy egy regressziós egyenlőségi szó szerint értelmezett függvény írhat.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

Ez létrehozza a következő.

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

A P értékeket (lekéréses kérelem (> |} t |})) Ez a kimenet, láthatjuk, hogy a squared kifejezés nem mindig jelentős. Használom a `update()` függvény squared kifejezés elvetését módosíthatja ezt a modellt.

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

Ez létrehozza a következő.

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

Ez tűnik jobb. A feltételek mindegyike jelentős. (2E) – 16 értéke azonban az alapértelmezett érték, és nem kell túl komolyan venni.  

Megerősítést tesztként tekintsük California tejtermelésre adatok idő sorozat rajzot a trend görbéjű látható. A következő kódot az Azure Machine Learning studióban hozzáadott [R-szkript végrehajtása] [ execute-r-script] (nem az RStudio) modell a modell létrehozásához, és győződjön meg arról, egy diagram. 23. ábra jelenik meg az eredményt.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![Kaliforniai tej termelési adatok látható trendek modellel](./media/r-quickstart/unnamed-chunk-18.png)

*23. ábra. Kaliforniai tej éles adatok trend modellel is látható.*

Úgy tűnik, a trend modell viszonylag jól illik az adatokhoz. További nem tűnik, hogy túlzott méretezés bizonyíték lehet, mint például a modell görbén wiggles páratlan.  

### <a name="seasonal-model"></a>Szezonális modell
Az aktuális trend-modell kell leküldéses és az időszakos hatások közé tartozik. Az év hónapját használjuk a lineáris modell helyőrző változóként capture havonta készülő hatása. Vegye figyelembe, hogy tényező változók modellbe vezeti be, amikor az intercept nem kell számolni. Ha ezt nem teszi meg, a képlet túlterhelt van megadva, és R fog dobja el a kívánt kiemelten, de tartsa intercept kifejezés.

Mivel van egy megfelelő trend modell használhatjuk a `update()` függvényt, hogy az új feltételeket ad hozzá a létező modellt. A -1, a frissítés képletben intercept kifejezés csökken. Folytatás az RStudio egyelőre:

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

Ez létrehozza a következő.

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

Láthatjuk, hogy a modell már nem egy intercept szól, és rendelkezik a 12 hónap jelentős tényezők. Ez a pontosan, mi szerettünk megtekintéséhez.

Tekintsük meg egy másik time series rajz California tejtermelésre adatok arról, hogy működik-e a szezonális modell. A következő kódot az Azure Machine Learning studióban hozzáadott [R-szkript végrehajtása] [ execute-r-script] a modell létrehozásához, és győződjön meg arról, egy diagram.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

Ez a kód futtatása az Azure Machine Learning Studióban az ábrázolást. ábra 24 bemutatott hoz létre.

![Kaliforniai tejtermelés többek között a szezonális hatások modellel](./media/r-quickstart/unnamed-chunk-20.png)

*24. ábra. Kaliforniai tejtermelés többek között a szezonális hatások modellel.*

A laphoz igazítás 24. ábra megjelenített adatok inkább ösztönzése. A trendek és a szezonális hatás (havi változat) is ésszerű jelenik meg

A modell egy másik ellenőrzést, mint nézzük meg, például a következő. A következő kódot az előrejelzett értékeket a két modell kiszámítja a például a szezonális modell kiszámítja és majd jeleníti meg ezeket a betanítási adatok például.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

A fennmaradó ábrázoláson 25. ábra jelenik meg.

![Például az időszakos modell a betanítási adatok](./media/r-quickstart/unnamed-chunk-21.png)

*25. ábra. Például az időszakos modell a betanítási adatok.*

Ezek a maradványok ésszerű meg. Nem adott struktúra, kivéve a 2008-2009 recesszió, amely a modell nem veszi figyelembe a hatása van különösen jól.

A diagram látható 25. ábra bármely időfüggő minták észlelésére a például a hasznos. Az explicit módszert is, amely a használt például számítástechnika és a például a a rajzolási sorrendben helyezi. Ha, másrészt I kellett ábrázolt `milk.lm$residuals`, a diagram nem lett volna a sorrendben.

Is `plot.lm()` diagnosztikai grafikon sorozatát előállításához.

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Ez a kód egy sorozatát 26. ábra látható diagnosztikai grafikon eredményez.

![Első napján szezonális modell diagnosztikai grafikon](./media/r-quickstart/unnamed-chunk-221.png)

![Az időszakos modell diagnosztikai grafikon száma másodpercenként](./media/r-quickstart/unnamed-chunk-222.png)

![Külső, az évszaknak megfelelő modell diagnosztikai grafikon](./media/r-quickstart/unnamed-chunk-223.png)

![A diagnosztikai grafikon szezonális modell negyedik](./media/r-quickstart/unnamed-chunk-224.png)

*26. ábra. Diagnosztikai szezonális modell jeleníti meg.*

Van néhány magas befolyásos pontok ezen grafikon, de a nagyszerű aggodalomra semmi nem szerepelt. További láthatjuk, a normál Q-Q diagram, hogy a például általában elosztott, fontos feltételezésből lineáris modellek közel-e.

### <a name="forecasting-and-model-evaluation"></a>Előrejelzési és a modell értékelése
Nincs csak egy dolog példánkban végrehajtásához. Igazolnia kell, a számítási előrejelzéseket és a hiba mérhetők a tényleges adatok. Az előrejelzés lesz a 2013 12 hónapig. Azt is számítási hiba, amely nem része a betanítási adatkészletet a tényleges adatok az adott előrejelzési intézkedésként. Ezenkívül a betanítási adatok Tesztadatok 12 hónapig 18 éves teljesítményét is összehasonlítva.  

A time series modellek teljesítményének mérésére mérőszámokat szolgálnak. Ebben az esetben a négyzetes (RMS) hiba használunk. A következő függvény kiszámítja a két sorozat közötti RMS hiba.  

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

A a `log.transform()` függvény "Értéket az átalakítások" szakaszában beszéltünk hiba ellenőrzése és a kivétel visszaszerzési kód ebben a függvényben igen sok van. Az alkalmazott alapelvek megegyeznek. A munkát a burkolt be két helyen `tryCatch()`. Először a time series exponentiated, mivel, a naplók az értékek dolgozunk. A második a tényleges RMS hiba számított.  

Nézzük felszerelt függvény méréséhez az RMS-hiba, hozhat létre és az RMS hibákat tartalmazó dataframe kimeneti. Önálló trend modell feltételeket és a teljes modell szezonális tényezőket is. A következő kódot kell kialakítani, hogy a két lineáris modell használatával hajtja végre a feladatot.

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

A kimenetben látható, az eredmény adathalmaz kimeneti portját 27. ábra ezt a kódot futtató eredményez.

![Az RMS-hibák modellek összehasonlítása](./media/r-quickstart/fig26.png)

*27. ábra. A modellek RMS által jelzett hibákat összehasonlítása.*

Az ezekkel az eredményekkel láthatjuk, hogy a szezonális tényezők ad hozzá a modell csökkenti az RMS hiba jelentősen. Az RMS-hiba a betanítási adatok nem meglepő túl jelző bit kevesebb, mint az előrejelzéshez.

## <a id="appendixa"></a>FÜGGELÉK: Útmutató az RStudio
Az RStudio meglehetősen megfelelően legyen dokumentálva, így az ebben a függelékben megadom a fő részből áll az RStudio dokumentáció révén elsajátíthatja az egyes hivatkozások.

1. Projektek létrehozása
   
   Rendezheti és az R-kód kezelése projektek az RStudio használatával. A projektek használó dokumentációjában található https://support.rstudio.com/hc/articles/200526207-Using-Projects.
   
   Javasolt, hogy kövesse az alábbi utasításokat, és hozzon létre egy projektet a R-kód példák ebben a dokumentumban.  
2. Szerkesztés és az R-kód végrehajtása
   
   Az RStudio R-kód végrehajtása és a Szerkesztés integrált környezetet biztosít. Dokumentáció itt található https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.
3. Hibakeresés
   
   Az RStudio hatékony hibakeresési képességeket is tartalmaz. Ezek a szolgáltatások dokumentációját jelenleg https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.
   
   A töréspont hibaelhárítási funkciók vannak dokumentálva https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

## <a id="appendixb"></a>B FÜGGELÉK: További olvasnivalók
Az R programozási oktatóanyag kell az R nyelv használata az Azure Machine Learning Studio alapjait ismerteti. Ha nem ismeri a R, a CRAN két tudnivalókra érhetők el:

* R által Emmanuel Paradis kezdőknek remek indítás a http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  
* L. n R bemutatása Venables et. al. egy kicsit részletesebben hiányzóra változik http://cran.r-project.org/doc/manuals/R-intro.html.

Nincsenek számos könyvek R, amelyek segíthetnek a kezdéshez. Íme néhány hasznos találhatók:

* Az R programozási Art: A bemutató a statisztikai szoftver kialakítást is Norman Matloff az r programozási kiváló bemutatása  
* R Cookbook Paul Teetor által probléma és megoldás r segítségével módszert biztosít a  
* Robert Kabacoff által működés az R egy másik hasznos bevezető könyv. A kiegészítő gyors R webhely egy olyan hasznos, erőforrás: http://www.statmethods.net/.
* R Inferno Patrick Burns által egy meglepően szorult könyv, hogy kezelje a számos bonyolult és nehezen észlelt is, ha az r programozási témakörök A könyv érhető el ingyenesen http://www.burns-stat.com/documents/books/the-r-inferno/.
* R – speciális témák mélyreható, van egy pillantást a könyv speciális R Hadley Wickham szerint. Ingyenesen érhető el az online változata annak a könyv http://adv-r.had.co.nz/.

R-time series csomagok katalógusában található idősoros elemzés a CRAN feladat nézetben: http://cran.r-project.org/web/views/TimeSeries.html. Adatsorozat objektum csomagok meghatározott időre szóló információ hivatkozzon az adott csomag dokumentációjában.

A könyv bevezető Time Series Paul Cowpertwait és Andrew Metcalfe r mutatja be, idősoros elemzés az R használatával. Számos további elméleti szövegek R példákat biztosítanak.

Néhány nagyszerű internetes erőforrások:

* DataCamp: DataCamp R a böngészőjében az videó leckékben és kódolási gyakorlatok kényelmes azzal foglalkozunk. Nincsenek interaktív oktatóanyagok a legújabb R technikák és csomagokat. Jelenleg ingyenes interaktív R oktatóanyag elvégzése https://www.datacamp.com/courses/introduction-to-r
* Útmutató az első lépések Programiz az R-rel https://www.programiz.com/r-programming
* R Tibor fekete Clarkson egyetemen által rövid oktatóanyagot http://www.cyclismo.org/tutorial/R/
* Több mint 60 R felsorolt forrásokat: http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html


<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/