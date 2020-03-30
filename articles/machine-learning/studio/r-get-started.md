---
title: Az R – első lépések
titleSuffix: ML Studio (classic) - Azure
description: Ezzel az R-programozási oktatóanyaggal az R-nyelv azure Machine Learning Studio (klasszikus) használatával hozhat létre előrejelzési megoldást.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 1b347707b3c656bd692a29f0fd748c1503be4fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217998"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Az R programozási nyelv első lépései az Azure Machine Learning Studio-ban (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Bevezetés

Ez az oktatóanyag segít az Azure Machine Learning Studio (klasszikus) bővítésének megkezdésében az R programozási nyelv használatával. Kövesse ezt az R programozási oktatóanyag létrehozásához, teszteléséhez és végrehajtásához R-kód studio (klasszikus). Az oktatóanyag on-val végzett munka során egy teljes előrejelzési megoldást hoz létre a Studio (klasszikus) R nyelvének használatával.  

Az Azure Machine Learning Studio (klasszikus) számos hatékony gépi tanulási és adatkezelési modult tartalmaz. A nagy teljesítményű R nyelvet az analitika lingua franca-jaként írták le. Szerencsére, elemzés és adatkezelés a Studio (klasszikus) bővíthető segítségével R. Ez a kombináció biztosítja a Studio (klasszikus) méretezhetőségét és egyszerű üzembe helyezését az R rugalmasságával és mély elemzésével.

### <a name="forecasting-and-the-dataset"></a>Előrejelzés és az adatkészlet

Az előrejelzés széles körben alkalmazott és nagyon hasznos analitikai módszer. A gyakori felhasználások a szezonális cikkek értékesítésének előrejelzésétől az optimális készletszintek meghatározásától a makrogazdasági változók előrejelzéséig terjednek. Az előrejelzés általában idősorozat-modellekkel történik.

Az idősorozat-adatok olyan adatok, amelyekben az értékek időindexszel rendelkeznek. Az időindex lehet rendszeres, pl. havonta vagy percenként, vagy szabálytalan. Az idősorozat-modell az idősorozat-adatokon alapul. Az R programozási nyelv rugalmas keretrendszert és kiterjedt elemzést tartalmaz az idősorozat-adatokhoz.

Ebben az útmutatóban a kaliforniai tejtermeléssel és árképzési adatokkal fogunk dolgozni. Ezek az adatok havi információkat tartalmaznak több tejtermék termeléséről és a tejzsír áráról, amely referencia-árucikk.

A cikkben használt adatok, valamint az R-parancsfájlok, letölthető [MachineLearningSamples-Notebooks/studio-samples.](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) A fájlban `cadairydata.csv` lévő adatokat eredetileg a Wisconsini [https://dairymarkets.com](https://dairymarkets.com)Egyetem től származó információkalapján szintetizálták.

### <a name="organization"></a>Szervezet

Az Azure Machine Learning Studio (klasszikus) környezetben az elemzési és adatkezelési R-kód létrehozásának, tesztelésének és végrehajtásának megismerésével több lépésben is haladunk.  

* Először is megismerjük az R nyelv használatának alapjait az Azure Machine Learning Studio (klasszikus) környezetben.
* Ezután az Azure Machine Learning Studio (klasszikus) környezetben az adatok, az R-kód és a grafikák I/O-jának különböző aspektusait tárgyaljuk.
* Ezután elkészítjük előrejelzési megoldásunk első részét az adatok tisztítására és átalakítására vonatkozó kód létrehozásával.
* Az elkészített adatainkkal elemzést végzünk az adatkészletünk számos változója közötti korrelációkról.
* Végül létrehozunk egy szezonális idősorozat-előrejelzési modellt a tejtermeléshez.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Az R-nyelv kölcsönhatása a Machine Learning Studióban (klasszikus)

Ez a szakasz a Machine Learning Studio (klasszikus) környezetben az R programozási nyelvvel való interakció néhány alapvető elemén vezet. Az R nyelv hatékony eszközt biztosít a testreszabott elemzési és adatkezelési modulok létrehozásához az Azure Machine Learning Studio (klasszikus) környezetben.

Fogom használni RStudio fejlesztése, tesztelése és hibakeresés R kódot egy kis léptékű. Ezt a kódot ezután kivágja, és illessze be egy [R-parancsfájl végrehajtása][execute-r-script] modul futtatására készen az Azure Machine Learning Studio (klasszikus).  

### <a name="the-execute-r-script-module"></a>Az R parancsfájl végrehajtása modul

A Machine Learning Studio (klasszikus) belül az R-parancsfájlok az [R-parancsfájl végrehajtása][execute-r-script] modulon belül futnak. Egy példa az [R-parancsfájl végrehajtása][execute-r-script] modul Machine Learning Studio (klasszikus) az 1.

 ![R programozási nyelv: A Machine Learning Studio programban kiválasztott R-parancsfájl végrehajtása modul (klasszikus)](./media/r-quickstart/fig1.png)

*1. ábra. A Machine Learning Studio (klasszikus) környezet, amely a végrehajtás R Script modul kiválasztott.*

Az [1.][execute-r-script]

* A kísérletben szereplő modulok a középső ablaktáblában jelennek meg.
* A jobb oldali ablaktábla felső része tartalmaz egy ablakot az R-parancsfájlok megtekintéséhez és szerkesztéséhez.  
* A jobb oldali ablaktábla alsó részén az [R parancsfájl végrehajtása][execute-r-script]néhány tulajdonságlátható. A hiba- és kimeneti naplókat az ablaktábla megfelelő pontjainak kiválasztásával tekintheti meg.

Természetesen részletesebben fogjuk megvitatni az [R-parancsfájl végrehajtását][execute-r-script] a cikk többi részében.

Amikor bonyolult R függvényekkel dolgozik, azt javaslom, hogy az RStudio-ban szerkesztse, tesztelje és debugolja. Mint minden szoftverfejlesztésnél, bővítse ki a kódot fokozatosan, és tesztelje kis egyszerű teszteseteken. Ezután vágja ki és illessze be a függvényeket az [R-parancsfájl végrehajtása][execute-r-script] modul R parancsfájlablakába. Ez a megközelítés lehetővé teszi, hogy kihasználja mind az RStudio integrált fejlesztői környezet (IDE) és az Azure Machine Learning Studio (klasszikus) erejét.  

#### <a name="execute-r-code"></a>R-kód végrehajtása

Az [R parancsfájl végrehajtása][execute-r-script] modulban lévő bármely R-kód a **Futtatás** gomb kiválasztásával hajtható végre a kísérlet futtatásakor. Ha a végrehajtás befejeződött, egy pipa jelenik meg az [R-parancsfájl végrehajtása][execute-r-script] ikonon.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Defenzív R-kódolás az Azure Machine Learninghez

Ha r-kódot fejleszt, például egy webszolgáltatáshoz az Azure Machine Learning Studio (klasszikus) használatával, akkor feltétlenül tervezze meg, hogy a kód hogyan kezeli a váratlan adatbevitelt és kivételeket. Az egyértelműség fenntartása érdekében a legtöbb megjelenített kódpéldában nem sokat foglaltam bele az ellenőrzés vagy a kivételkezelés módjába. Azonban, ahogy haladunk, adok önnek néhány példát a funkciók segítségével R kivételkezelési képesség.  

Ha szüksége van egy teljesebb kezelése R kivétel kezelése, azt javasoljuk, hogy olvassa el a vonatkozó szakaszok a könyv Wickham alább felsorolt [További olvasás](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>R hibakeresése és tesztelése a Machine Learning Studióban (klasszikus)

Megismételni, azt javasoljuk, hogy tesztelje és hibakeresés az R-kódot egy kis léptékű RStudio. Vannak azonban olyan esetek, amikor az R-kód problémáit magának az [R-parancsfájlnak][execute-r-script] a végrehajtása kornya nyomon kell követnie. Ezenkívül ajánlott ellenőrizni az eredményeket a Machine Learning Studio (klasszikus).

Kimenet az R-kód és az Azure Machine Learning Studio (klasszikus) platform elsősorban a output.log. Néhány további információ az error.log fájlban jelenik meg.  

Ha hiba történik a Machine Learning Studio (klasszikus) az R-kód futtatása közben, az első lépés az error.log. Ez a fájl hasznos hibaüzeneteket tartalmazhat, amelyek segítenek megérteni és kijavítani a hibát. A error.log fájl megtekintéséhez jelölje be a **Hibanapló megtekintése** jelölőnégyzetet a hibát tartalmazó [R-parancsfájl végrehajtása][execute-r-script] **párbeszédpanel tulajdonságpanelén.**

Például a következő R kódot futtattam egy meg határozatlan y változóval egy [R-parancsfájl végrehajtása][execute-r-script] modulban:

```R
x <- 1.0
z <- x + y
```

A kód végrehajtása sikertelen, ami hibaállapotot eredményez. A **Tulajdonságok ablaktáblán** a **Nézet hibanapló** kiválasztásával megjelenik a 2.

  ![Előugró hibaüzenet jelenik meg](./media/r-quickstart/fig2.png)

*2. ábra. Előugró hibaüzenet jelenik meg.*

Úgy néz ki, meg kell nézni output.log, hogy az R hibaüzenet. Jelölje ki az [R-parancsfájl végrehajtása lehetőséget,][execute-r-script] majd válassza a **Output.log megtekintése** elemet a **tulajdonságok ablaktábláján** jobbra. Megnyílik egy új böngészőablak, és a következőket látom.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ez a hibaüzenet nem tartalmaz meglepetéseket, és egyértelműen azonosítja a problémát.

Az R fájlban lévő objektumok értékének vizsgálatához ezeket az értékeket a output.log fájlba nyomtathatja. Az objektumértékek vizsgálatára vonatkozó szabályok lényegében megegyeznek az interaktív R-munkamenetszabályaival. Ha például egy sorba változónevet ír be, az objektum értéke a output.log fájlba lesz nyomtatva.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Csomagok a Machine Learning Studióban (klasszikus)

Studio jön-val több mint 350 előre telepített R nyelvi csomagok. Az [előtelepített][execute-r-script] csomagok listájának lekéréséhez az R parancsfájl végrehajtása modulban a következő kódot használhatja.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Ha jelenleg nem érti a kód utolsó sorát, olvasson tovább. A többi ezt a cikket fogunk alaposan megvitassák az R a Studio (klasszikus) környezetben.

### <a name="introduction-to-rstudio"></a>Bevezetés az RStudio bemutatása

RStudio egy széles körben használt IDE R. Fogom használni RStudio szerkesztésére, tesztelésére és hibakeresésére néhány R kódot használnak ebben az útmutatóban. Miután az R-kód tesztelésre került és készen áll, egyszerűen kivághatja és beillesztheti az RStudio szerkesztőből egy Machine Learning Studio (klasszikus) R Script modul [végrehajtása.][execute-r-script]  

Ha nincs telepítve az R programozási nyelv az asztali gépre, azt javaslom, hogy most tegye meg. A nyílt forráskódú R nyelv ingyenes letöltései elérhetők az [https://www.r-project.org/](https://www.r-project.org/)Átfogó R Archív Hálózaton (CRAN) a. A letöltések letölthetők Windows, Mac OS és Linux/UNIX rendszerekre. Válasszegy közeli tükröt, és kövesd a letöltési utasításokat. Ezenkívül a CRAN számos hasznos elemzési és adatkezelési csomagot tartalmaz.

Ha ön új -hoz RStudio, önnek kellene letölt és felszerel a iskolapad változat. Az RStudio letöltések A Windows, Mac OS és Linux http://www.rstudio.com/products/RStudio// UNIX a . Kövesse az RStudio asztali gépre történő telepítéséhez szükséges utasításokat.  

A bemutató bevezetés RStudio elérhető [használata RStudio IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Én néhány további információt használ RStudio a [Guide to RStudio dokumentáció](#appendixa) alatt.  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Adatok be- és kiírása az R-parancsfájl végrehajtása modulból

Ebben a szakaszban bemutatjuk, hogyan jut be és ki az [R-parancsfájl végrehajtása][execute-r-script] modul. Áttekintjük, hogyan kell kezelni a különböző adattípusok be- és [kiolvasása][execute-r-script] R Script modul.

A szakasz teljes kódja a [MachineLearningSamples-Notebooks/studio-samples .](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Adatok betöltése és ellenőrzése a Machine Learning Studio-ban (klasszikus)

#### <a name="load-the-dataset"></a><a id="loading"></a>Az adatkészlet betöltése

Először is a **csdairydata.csv** fájl betöltésével kezdjük az Azure Machine Learning Studio (klasszikus) gépi tanulási stúdióba.

1. Indítsa el az Azure Machine Learning Studio (klasszikus) környezetet.
1. Válassza a **+ ÚJ** lehetőséget a képernyő bal alsó részén, majd **az Adatkészlet**lehetőséget.
1. Válassza **a Helyi fájlból**lehetőséget, majd a **Tallózás gombra** a fájl kijelöléséhez.
1. Győződjön meg arról, hogy az **általános CSV-fájlt fejléccel (.csv)** jelölte az adatkészlet típusaként.
1. Jelölje be a négyzetet.
1. Az adatkészlet feltöltése után az adatkészlet et az **Adatkészletek** lap kiválasztásával kell látnia.  

#### <a name="create-an-experiment"></a>Kísérlet létrehozása

Most, hogy már van néhány adat a Machine Learning Studio (klasszikus), létre kell hoznunk egy kísérletet, hogy az elemzés.  

1. Válassza a **+ ÚJ** lehetőséget a bal alsó sarokban, majd a **Kísérlet**lehetőséget, majd az **Üres kísérlet**lehetőséget.
1. A kísérlet elnevezéséhez jelölje ki és módosítsa a ... címen **létrehozott kísérletet** az oldal tetején. Például módosítsa a **CA Dairy Analysis**.
1. A kísérlet lap bal oldalán bontsa ki a **Mentett adatkészletek**, majd a **Saját adatkészletek csomópontot.** Meg kell jelennie a **cadairydata.csv,** hogy a korábban feltöltött.
1. Húzza a **csdairydata.csv adatkészletet** a kísérletre.
1. A bal oldali ablaktábla tetején található **Kísérletelemek keresése** mezőbe írja be az [R-parancsfájl végrehajtása című szöveget.][execute-r-script] A modul megjelenik a keresési listában.
1. Húzza az [R-parancsfájl végrehajtása][execute-r-script] modult a raklapra.  
1. Csatlakoztassa a **csdairydata.csv adatkészlet** kimenetét az [R parancsfájl végrehajtása][execute-r-script]bal szélső bemenetéhez (**Dataset1**) .
1. **Ne felejtsük el, hogy válassza a "Mentés"!**  

Ezen a ponton a kísérlet nek valahogy a 3.

![A CA Dairy Analysis kísérlet adatkészlettel és R-parancsfájl végrehajtása modullal](./media/r-quickstart/fig3.png)

*3. ábra. A CA Dairy Analysis kísérlet adatkészlettel és R-parancsfájl végrehajtása modullal.*

#### <a name="check-on-the-data"></a>Az adatok ellenőrzése

Vessünk egy pillantást az adatokra, amelyeket betöltöttünk a kísérletünkbe. A kísérletben válassza ki a **cadairydata.csv adatkészlet** kimenetét, és válassza a **visualize**lehetőséget. Látnod kellene valami hasonlót a 4.  

![A cadairydata.csv adatkészlet összefoglalása](./media/r-quickstart/fig4.png)

*4. ábra. A cadairydata.csv adatkészlet összefoglalása.*

Ebben a nézetben sok hasznos információt látunk. Láthatjuk az adatkészlet első néhány sorát. Ha kiválasztunk egy oszlopot, a Statisztika szakasz további információkat jelenít meg az oszlopról. A Feature Type (Szolgáltatástípus) sor például megmutatja, hogy az Azure Machine Learning Studio (klasszikus) milyen adattípusokat rendel az oszlophoz. Miután egy gyors megjelenés, mint ez egy jó józanság ellenőrzés, mielőtt elkezdjük, hogy bármilyen komoly munkát.

### <a name="first-r-script"></a>Első R-parancsfájl

Hozzunk létre egy egyszerű első R-parancsfájlt, amelyet az Azure Machine Learning Studio (klasszikus) keretében kísérletezhet. Nekem van teremtett és baldachin a következő kézírás -ban RStudio.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Most át kell vinnie ezt a szkriptet az Azure Machine Learning Studio (klasszikus). Egyszerűen kivághatnám és beilleszthetném. Azonban ebben az esetben, én át az én R script keresztül zip fájlt.

### <a name="data-input-to-the-execute-r-script-module"></a>Az R-parancsfájl végrehajtása modulba irányuló adatbevitel

Vessünk egy pillantást az [R-parancsfájl végrehajtása][execute-r-script] modul bemenetei. Ebben a példában fogjuk olvasni a kaliforniai tejtermék adatokat a [Végrehajtás R Script][execute-r-script] modul.  

Az [R-parancsfájl végrehajtása][execute-r-script] modulnak három lehetséges bemenete van. Az alkalmazástól függően bármelyik vagy az összes ilyen bemenetet használhatja. Az is teljesen ésszerű, hogy egy R script, hogy nem vesz bemenet egyáltalán.  

Nézzük meg ezeket a bemeneteket, balról jobbra haladva. Az egyes bemenetek nevét úgy láthatja, hogy a kurzort a bemenet fölé helyezi, és elolvassa az elemleírást.  

#### <a name="script-bundle"></a>Parancsfájl köteg

A Script Bundle bemenet lehetővé teszi, hogy adja át a tartalmát egy zip fájlt [execute R Script][execute-r-script] modul. Az alábbi parancsok egyikével beolvashatja a zip-fájl tartalmát az R-kódba.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Az Azure Machine Learning Studio (klasszikus) úgy kezeli a zip-ben lévő fájlokat, mintha az src/ könyvtárban lennének, ezért ezzel a könyvtárnévvel kell előtagolnia a fájlneveket. Ha például a zip `yourfile.R` tartalmazza `yourData.rdata` a fájlokat, és a gyökér `src/yourfile.R` `src/yourData.rdata` a `source` zip, akkor ezeket, és amikor a és `load`a .

Már megvitattuk az adatkészletek betöltését [az adatkészlet betöltése során.](#loading) Miután létrehozta és tesztelte az előző szakaszban látható R-parancsfájlt, tegye a következőket:

1. Mentse az R parancsfájlt egy be. R fájl. A szkriptfájlomat "simpleplot"-nak hívom. R". Itt a tartalma.

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Hozzon létre egy zip fájlt, és másolja a parancsfájlt ebbe a zip fájlba. Windows rendszerben a jobb gombbal a fájlra kattinthat, és válassza a **Küldés a programnak** **parancsot, majd a Tömörített mappát.** Ez létrehoz egy új zip fájlt, amely tartalmazza a "simpleplot. R" fájlt.

1. Adja hozzá a fájlt az **adatkészletek** az Azure Machine Learning Studio (klasszikus), megadva a típus **zip.** Most látnia kell a zip fájlt az adatkészletekben.

1. Húzza a zip fájlt az **adatkészletekből** az **ML Studio (klasszikus) vászonra.**

1. Csatlakoztassa a **zip-adatikon** kimenetét az [R-parancsfájl végrehajtása][execute-r-script] modul **Script Bundle** bemenetéhez.

1. Írja `source()` be a funkció a zip fájl nevét a kód ablakban az [R Script végrehajtása][execute-r-script] modul. Az én esetemben gépeltem `source("src/simpleplot.R")`.  

1. Győződjön meg arról, hogy a Mentés lehetőséget **választja.**

Miután ezeket a lépéseket befejeződött, az [R-parancsfájl végrehajtása][execute-r-script] modul végrehajtja az R parancsfájlt a zip-fájlban a kísérlet futtatásakor. Ezen a ponton a kísérlet nek valahogy az 5.

![Kísérlet a tömörített R parancsfájl használatával](./media/r-quickstart/fig6.png)

*5. ábra. Kísérletezzen a tömörített R parancsfájl használatával.*

#### <a name="dataset1"></a>Adatkészlet1

A Dataset1 bevitel használatával átadhat egy téglalap alakú adattáblát az R-kódnak. Az egyszerű szkriptben a `maml.mapInputPort(1)` függvény beolvassa az adatokat az 1-es portról. Ezeket az adatokat ezután hozzárendeli egy dataframe változó nevéhez a kódban. A mi egyszerű script az első sor a kód elvégzi a feladatot.

```R
cadairydata <- maml.mapInputPort(1)
```

Végezze el a kísérletet a **Futtatás** gomb kiválasztásával. A végrehajtás befejezése után válassza az [R-parancsfájl végrehajtása][execute-r-script] modult, majd a **Kimeneti napló megtekintése** a tulajdonságok ablaktábláján lehetőséget. A kimenet.log fájl tartalmát megjelenítő új lapnak kell megjelennie a böngészőben. Lefelé görgetéskor a következőhez hasonlót kell látnia.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Lejjebb az oldalon részletesebb információkat az oszlopok, ami úgy néz ki, mint a következő.

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

Ezek az eredmények többnyire a várt módon, 228 megfigyelések és 9 oszlop az adatkeretben. Láthatjuk az oszlopneveket, az R adattípust és az egyes oszlopok mintáját.

> [!NOTE]
> Ugyanez a nyomtatott kimenet kényelmesen elérhető az [R-parancsfájl végrehajtása][execute-r-script] modul R eszközkimenetéből. A következő szakaszban bemutatjuk az [R-parancsfájl végrehajtása][execute-r-script] modul kimeneteit.  

#### <a name="dataset2"></a>Adatkészlet2

A Dataset2 bemenet viselkedése megegyezik a Dataset1 viselkedésével. Ezzel a bemenettel egy második téglalap alakú adattáblázatot adhat át az R-kódba. A `maml.mapInputPort(2)`függvény a 2.  

### <a name="execute-r-script-outputs"></a>R-parancsfájl kimenetek végrehajtása

#### <a name="output-a-dataframe"></a>Adatkeret kibocsátása

Az R adatkeret tartalmát téglalap alakú táblaként is kiadhatja az Eredmény adatkészlet1 porton keresztül a `maml.mapOutputPort()` függvény használatával. A mi egyszerű R script ezt végzi a következő sorban.

```
maml.mapOutputPort('cadairydata')
```

A kísérlet futtatása után jelölje ki az Eredményadatkészlet1 kimeneti portot, majd válassza a **Visualize**lehetőséget. Látnod kellene valami olyasmit, mint a 6.

![A kaliforniai tejipari adatok kimenetének megjelenítése](./media/r-quickstart/fig7.png)

*6. ábra. A kaliforniai tejipari adatok kimenetének megjelenítése.*

Ez a kimenet úgy néz ki, azonos a bemeneti, pontosan úgy, ahogy vártuk.  

### <a name="r-device-output"></a>R Eszköz kimenete

Az [R-parancsfájl végrehajtása][execute-r-script] modul eszközkimenete üzeneteket és grafikus kimenetet tartalmaz. Mind a szabványos kimeneti, mind az R-ből származó normál hibaüzenetek az R-eszköz kimeneti portjára kerülnek.  

Az R eszköz kimenetének megtekintéséhez jelölje ki a portot, majd a **Visualize területen.** A 7.

![Normál kimenet és szabványos hiba az R eszközportból](./media/r-quickstart/fig8.png)

*7. ábra. Normál kimenet és szabványos hiba az R eszközportról.*

Lefelé görgetve látjuk az R szkript grafikus kimenetét a 8.  

![Grafikus kimenet az R eszközportról](./media/r-quickstart/fig9.png)

*8. ábra. Grafikus kimenet az R eszközportról.*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Adatszűrés és -átalakítás

Ebben a szakaszban néhány alapvető adatszűrési és átalakítási műveletet hajtunk végre a kaliforniai tejtermékadatokon. A szakasz végére az adatok olyan formátumban lesznek, amely alkalmas egy analitikus modell elkészítésére.  

Pontosabban ebben a szakaszban számos gyakori adattisztítási és átalakítási feladatot hajtunk végre: típusátalakítás, adatkeretek szűrése, új számított oszlopok hozzáadása és értékátalakítások. Ez a háttér segít kezelni a sok variációk találkozott a valós problémákat.

A szakasz teljes R-kódja a [MachineLearningSamples-Notebooks/studio-samples alkalmazásban](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)érhető el.

### <a name="type-transformations"></a>Típusátalakítások

Most, hogy a kaliforniai tejtermékadatokat beolvashatjuk az R-kódba az [R-parancsfájl végrehajtása][execute-r-script] modulban, biztosítanunk kell, hogy az oszlopokban lévő adatok a kívánt típussal és formátummal rendelkezzenek.  

Az R egy dinamikusan beírt nyelv, ami azt jelenti, hogy az adattípusok at kötelezően kényszerítik egyikből a másikba. Az R atomi adattípusai közé tartozik a numerikus, a logikai és a karakter. A faktortípus a kategorikus adatok kompakt tárolására szolgál. Az adattípusokról az alábbi [További olvasmányokban](#appendixb) talál sokkal több információt.

Ha a táblázatos adatokat külső forrásból olvassa be az R-be, mindig célszerű ellenőrizni az oszlopokban kapott típusokat. Előfordulhat, hogy egy típusú oszlopot szeretne, de sok esetben ez tényezőként jelenik meg, vagy fordítva. Más esetekben egy numerikusnak tartandó oszlopot karakteradatok jelölnek, például "1,23" lebegőpontos számként, nem pedig 1,23.  

Szerencsére, ez könnyű konvertálni egy típus a másikra, amíg a leképezés lehetséges. Például a "Nevada" nem konvertálható numerikus értékké, de átalakítható tényezővé (kategorikus változó). Egy másik példa, hogy egy numerikus 1-et "1" karakterré vagy tényezővé alakíthat.  

E konverziók bármelyikének szintaxisa `as.datatype()`egyszerű: . Ezek a típuskonverziós függvények a következőket tartalmazzák.

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Az előző szakaszban bevitt oszlopok adattípusait tekintve: minden oszlop numerikus típusú, kivéve a "Hónap" feliratú oszlopot, amely típusjellegű. Alakítsuk át ezt egy tényezővé, és teszteljük az eredményeket.  

Töröltem a scatterplot mátrixot létrehozó sort, és hozzáadtam egy sort, amely a "Hónap" oszlopot egy tényezővé konvertálja. Az én kísérlet ben én csak kivágni és illessze be az R-kódot a kód ablakban a [Végrehajtás R Script][execute-r-script] modul. A zip-fájlt is frissítheti, és feltöltheti az Azure Machine Learning Studio (klasszikus) stúdióba, de ez több lépést is tesz.  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Hajtsuk végre ezt a kódot, és nézzük meg az R parancsfájl kimeneti naplóját. A naplóból származó releváns adatokat a 9.

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

*9. ábra. Az adatkeret összegzése tényezőváltozóval.*

A típus a hónap most azt kell mondani:**"Factor w / 14 szint**". Ez probléma, mivel az évben csak 12 hónap van. Azt is ellenőrizheti, hogy az Eredményadatkészlet-port **Visualize-ban** megadott típusa "**Kategorikus**".

A probléma az, hogy a "Hónap" oszlop nem volt szisztematikusan kódolva. Egyes esetekben egy hónap az úgynevezett április és másokban ez rövidítve április. Meg tudjuk oldani ezt a problémát a vágás a húr 3 karakter. A kódsor most a következőképpen néz ki:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Futtassa újra a kísérletet, és tekintse meg a kimeneti naplót. A várt eredményeket a 10.  

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

*10. ábra. Az adatkeret összegzése a tényezőszintek helyes számával.*

A faktor változó most már a kívánt 12 szinten.

### <a name="basic-data-frame-filtering"></a>Alapszintű adatkeret-szűrés

Az R adatkeretek hatékony szűrési lehetőségeket támogatnak. Az adatkészletek felosztása sorok vagy oszlopok logikai szűrőivel helyettesíthető. Sok esetben összetett szűrőfeltételekre lesz szükség. Az alábbi [további olvasatban](#appendixb) található hivatkozások részletes példákat tartalmaznak az adatkeretek szűrésére.  

Van egy kis szűrés, amit meg kell tennünk az adatkészletünkön. Ha megnézzük a cadairydata dataframe oszlopait, két felesleges oszlopot fog látni. Az első oszlop csak egy sorszámot tartalmaz, ami nem túl hasznos. A második oszlop, a Year.Month redundáns információkat tartalmaz. Ezeket az oszlopokat a következő R-kód dal könnyedén kizárhatjuk.

> [!NOTE]
> Mostantól ebben a szakaszban, én csak megmutatja a további kódot vagyok hozzá a [Végrehajtás R Script][execute-r-script] modul. Minden új sort hozzáadok `str()` a funkció **előtt.** Ezzel a funkcióval igazolhatom az eredményeket az Azure Machine Learning Studio (klasszikus) alkalmazásban.

Hozzáteszem a következő sort az R-kódomhoz az [R parancsfájl végrehajtása][execute-r-script] modulban.

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Futtassa ezt a kódot a kísérletben, és ellenőrizze a kimeneti napló eredményét. Ezeket az eredményeket a 11.

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

*11. ábra. Az adatkeret összegzése két oszlop eltávolításával.*

Van egy jó hírünk! Megkapjuk a várt eredményeket.

### <a name="add-a-new-column"></a>Új oszlop hozzáadása

Idősorozat-modellek létrehozásához célszerű egy oszlopot létrehozni, amely az idősorozat kezdete óta eltelt hónapokat tartalmazza. Létrehozunk egy új oszlopot "Month.Count".

Annak érdekében, hogy megszervezze a `num.month()`kódot fogunk létrehozni az első egyszerű funkció, . Ezután ezt a függvényt alkalmazzuk egy új oszlop létrehozásához az adatkeretben. Az új kód a következő.

```R
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
```

Most futtassa a frissített kísérletet, és használja a kimeneti naplót az eredmények megtekintéséhez. Ezeket az eredményeket a 12.

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

*12. ábra. Az adatkeret összegzése a további oszloppal.*

Úgy tűnik, minden működik. Az új oszlop a várt értékeket a mi dataframe.

### <a name="value-transformations"></a>Értékátalakítások

Ebben a szakaszban néhány egyszerű átalakítást hajtunk végre az adatkeret egyes oszlopaiban lévő értékeken. Az R nyelv csaknem tetszőleges értékátalakításokat támogat. Az alábbi [további olvasatban](#appendixb) található hivatkozások részletes példákat tartalmaznak.

Ha megnézzük az értékeket az adatkeretünk összegzésében, itt valami furcsát kell látnia. Több jégkrémet termelnek, mint tejet Kaliforniában? Nem, persze, hogy nem, mivel ennek nincs értelme, szomorú, mivel ez a tény lehet, hogy néhány unk fagylalt szerelmeseinek. Az egységek mások. Az ár egységben amerikai font, a tej egységekben 1 M amerikai font, fagylalt egységekben 1000 amerikai gallon, és a túró egységben 1000 amerikai font. Feltételezve, hogy a fagylalt súlya körülbelül 6,5 font / gallon, akkor könnyen nem a szorzás átalakítani ezeket az értékeket, így ezek mind egyenlő egységek 1000 font.

Előrejelzési modellünkhöz multiplikatív modellt használunk ezen adatok trend- és szezonális kiigazításához. A naplóátalakítás lehetővé teszi számunkra, hogy egy lineáris modell, egyszerűsíti ezt a folyamatot. A naplótranszformációt ugyanabban a függvényben alkalmazhatjuk, ahol a szorzót alkalmazzák.

A következő kódban új függvényt `log.transform()`definiálok, és alkalmazom a numerikus értékeket tartalmazó sorokra. Az `Map()` R függvény a `log.transform()` függvényt az adatkeret kijelölt oszlopaira alkalmazza. `Map()`hasonló, `apply()` de lehetővé teszi, hogy több argumentumot tartalmazó listát is lehetővé tetsenek a függvénynek. Ne feledje, hogy a szorzók `log.transform()` listája a függvény második argumentumát szolgáltatja. A `na.omit()` függvény egy kis tisztításként használatos annak biztosítására, hogy ne legyenek hiányzó vagy nem definiált értékeink az adatkeretben.

```R
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
```

Van egy kicsit történik `log.transform()` a funkciót. A legtöbb ez a kód ellenőrzi a lehetséges problémákat az argumentumokat, vagy foglalkozik a kivételek, amelyek továbbra is előfordulhatnak a számítások során. Ennek a kódnak csak néhány sora teszi meg a számításokat.

A védelmi programozás célja, hogy megakadályozza egyetlen függvény meghibásodását, amely megakadályozza a feldolgozás folytatását. Egy hosszú ideig futó elemzés hirtelen kudarca meglehetősen frusztráló lehet a felhasználók számára. Ennek a helyzetnek a elkerülése érdekében olyan alapértelmezett visszatérési értékeket kell választani, amelyek korlátozzák a későbbi feldolgozás károsodását. Egy üzenet is elő, hogy figyelmeztesse a felhasználókat, hogy valami baj van.

Ha nem használják a védekező programozás R, mindez a kód tűnhet egy kicsit nyomasztó. Végigvezetem a főbb lépéseken:

1. Négy üzenetből álló vektor van definiálva. Ezek az üzenetek a kóddal kapcsolatos egyes lehetséges hibákkal és kivételekkel kapcsolatos információk közlésére szolgálnak.
2. Minden esetnél visszaküldök egy NA értéket. Vannak sok más lehetőségek, amelyek kevesebb mellékhatása lehet. Visszaadhatok egy nullákból álló vektort, vagy például az eredeti bemeneti vektort.
3. A függvény argumentumait a függvény argumentumain futtatja a csekk. Hiba észlelése esetén minden esetben egy alapértelmezett értéket ad vissza a `warning()` függvény, és egy üzenetet hoz létre. Én használ, `warning()` nem `stop()` pedig az utóbbi megszünteti a végrehajtást, pontosan mit próbálok elkerülni. Ne feledje, hogy én írtam ezt a kódot egy eljárási stílus, mint ebben az esetben a funkcionális megközelítés tűnt bonyolult és homályos.
4. A naplószámítások be vannak `tryCatch()` csomagolva, így a kivételek nem okoznak hirtelen leállítást a feldolgozásban. Az `tryCatch()` R függvények által felvetett legtöbb hiba nélkül a stop jel, ami nem csak ezt.

Hajtsa végre ezt az R-kódot a kísérletben, és tekintse meg a output.log fájl ban lévő nyomtatott kimenetet. Most a naplónégy oszlopának átformált értékeit fogja látni, amint az a 13.

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

*13. ábra. Az átalakított értékek összegzése az adatkeretben.*

Látjuk, hogy az értékek átalakultak. A tejtermelés ma már jelentősen meghaladja az összes többi tejterméktermelést, emlékeztetve arra, hogy most egy rönkskála van.

Ezen a ponton az adataink törlődnek, és készen állunk néhány modellezésre. Az [R-parancsfájl végrehajtása][execute-r-script] modul eredményadatkészlet-kimenetének vizualizációs összegzését tekintve a "Hónap" oszlop "Kategorikus" 12 egyedi értékkel, ismét, ahogy szerettük volna.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Idősorozat-objektumok és korrelációs elemzés

Ebben a szakaszban néhány alapvető R idősorozat-objektumot fogunk feltárni, és elemezzük a változók közötti korrelációkat. Célunk, hogy a kimenet egy dataframe, amely a párban korrelációs információkat több lags.

A szakasz teljes R-kódja a [MachineLearningSamples-Notebooks/studio-samples .](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)

### <a name="time-series-objects-in-r"></a>Idősorozat-objektumok az R-ben

Mint már említettük, az idősorok az idő szerint indexelt adatértékek sorozata. Az R idősorozat-objektumok az időindex létrehozására és kezelésére szolgálnak. Az idősorozat-objektumok használatának számos előnye van. Az idősorozat-objektumok megszabadítják Önt az objektumba ágyazott idősorozat-indexértékek kezelésének számos részletétől. Ezenkívül az idősorozat-objektumok lehetővé teszik a sok idősorozat-módszer használatát a nyomtatáshoz, a nyomtatáshoz, a modellezéshez stb.

A POSIXct idősorozat-osztály táltalában használatos, és viszonylag egyszerű. Ez az idősorozat osztály méri idő kezdetétől a korszak, január 1, 1970. Ebben a példában POSIXct idősorozat-objektumokat fogunk használni. Más széles körben használt R idősorozat objektum osztályok közé állatkert és xts, bővíthető idősorok.

### <a name="time-series-object-example"></a>Példa idősorozat-objektumra

Kezdjük a példánkkal. Húzzon egy **új** [R-parancsfájl-végrehajtás][execute-r-script] modult a kísérletbe. Csatlakoztassa a meglévő [R parancsfájl végrehajtása][execute-r-script] modul Eredményadatkészlet1 kimeneti portját az új [R-parancsfájl végrehajtása][execute-r-script] modul Dataset1 bemeneti portjához.

Ahogy én is tettem az első példákban, ahogy haladunk a példán, bizonyos pontokon csak az R-kód növekményes további sorait fogom megjeleníteni minden lépésnél.  

#### <a name="reading-the-dataframe"></a>Az adatkeret olvasása

Első lépésként olvassuk el az adatkeretet, és győződjünk meg róla, hogy megkapjuk a várt eredményeket. A következő kódnak kell ezt a feladatot végeznie.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Most futtassa le a kísérletet. Az új R-parancsfájl-végrehajtás alakzat naplója a 14.

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

*14. ábra. Az R-parancsfájl végrehajtása modul adatkeretének összegzése.*

Ezek az adatok a várt típusúés formátumúak. Ne feledje, hogy a "Hónap" oszlop típustényező, és a várható számú szinttel rendelkezik.

#### <a name="creating-a-time-series-object"></a>Idősorozat-objektum létrehozása

Idősorozat-objektumot kell hozzáadnunk az adatkeretünkhöz. Cserélje le az aktuális kódot a következőre, amely hozzáadja a POSIXct osztály új oszlopát.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Most nézd meg a naplót. Úgy kell kinéznie, mint a 15- ös ábra.

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

*15. ábra. Az adatkeret összegzése idősorozat-objektummal.*

Láthatjuk az összefoglaló, hogy az új oszlop valójában osztály POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Az adatok feltárása és átalakítása

Tárja fel az adatkészlet néhány változóját. A scatterplot mátrix egy jó módja annak, hogy készítsen egy gyors pillantást. Az előző R-kód függvényét a `str()` következő smára cserélem.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Futtassa le ezt a kódot, és nézze meg, mi történik. Az R eszközporton előállított mintaterületnek a 16.

![A kijelölt változók scatterplot mátrixa](./media/r-quickstart/fig17.png)

*16. ábra. A kiválasztott változók scatterplot mátrixa.*

Van néhány furcsa kinézetű struktúra a változók közötti kapcsolatokban. Talán ez az adatok tendenciáiból és abból a tényből ered, hogy nem szabványosítottuk a változókat.

### <a name="correlation-analysis"></a>Korrelációs elemzés

A korrelációs elemzés elvégzéséhez detrend-de-trendés szabványosítása szükséges. Egyszerűen használhatjuk az `scale()` R függvényt, amely mind a változókat középre, mind skálázást. Ez a funkció talán gyorsabban fut. Azonban szeretnék mutatni egy példát a védekező programozás R.

Az `ts.detrend()` alábbi funkció mindkét műveletet végrehajtja. A következő két sornyi kód detrend az adatokat, majd szabványosítani az értékeket.

```R
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
```

Van egy kicsit történik `ts.detrend()` a funkciót. A legtöbb ez a kód ellenőrzi a lehetséges problémákat az argumentumokat, vagy foglalkozik a kivételek, amelyek továbbra is előfordulhatnak a számítások során. Ennek a kódnak csak néhány sora teszi meg a számításokat.

Már megvitattuk egy példa a defenzív programozás érték átalakítások. Mindkét számítási blokk be `tryCatch()`van csomagolva. Néhány hiba esetén érdemes visszaadni az eredeti bemeneti vektort, más esetekben pedig nullákból álló vektot adok vissza.  

Vegye figyelembe, hogy a felengedéshez használt lineáris regresszió egy idősorozat-regresszió. A prediktor változó egy idősorozat-objektum.  

Miután `ts.detrend()` meghatároztuk, alkalmazzuk az adatkeretünk ben található érdekes változókra. A rendszernek a használatával rá `lapply()` kell kényszerítenünk `as.data.frame()`az adatrendszer által létrehozott listát. A defenzív szempontok `ts.detrend()`miatt az egyik változó feldolgozásának elmulasztása nem akadályozza meg a többi helyes feldolgozását.  

Az utolsó kódsor létrehoz egy páronkénti scatterplot-t. Az R-kód futtatása után a scatterplot eredményeit a 17.

![A detrended és a szabványosított idősorok párban izolált szála](./media/r-quickstart/fig18.png)

*17. ábra. A detrended és a standardizált idősorok párban izolált szála.*

Ezeket az eredményeket összehasonlíthatja a 16. A trend eltávolítása és a változók szabványosításával sokkal kevesebb struktúrát látunk a változók közötti kapcsolatokban.

A korrelációk R ccf objektumokként való kiszámítására szolgáló kód a következő.

```R
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
```

A kód futtatása a 18.

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

*18. ábra. Ccf objektumok listája a páros korrelációs elemzésből.*

Minden egyes késleltetéshez korrelációs érték van. A korrelációs értékek egyike sem elég nagy ahhoz, hogy jelentős legyen. Ezért arra a következtetésre juthatunk, hogy minden változót egymástól függetlenül modellezhetünk.

### <a name="output-a-dataframe"></a>Adatkeret kibocsátása
A páros korrelációkat az R ccf objektumok listájaként számítottuk ki. Ez egy kis problémát jelent, mivel az eredményadatkészlet kimeneti portja valóban adatkeretet igényel. Továbbá, a ccf objektum maga egy lista, és azt akarjuk, hogy csak az értékeket az első eleme ennek a listának, a korrelációk a különböző elmarad.

A következő kód kinyeri a késés értékeket a ccf objektumok listájából, amelyek maguk is listák.

```R
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
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

Az első sor a kód egy kicsit trükkös, és néhány magyarázat segíthet megérteni. Belülről kifelé dolgozva a következők vannak:

1. A '**[**' operátor a '**1**argumentummal ' jelöléssel ' kiválasztja a korrelációk vektorát a ccf objektumlista első elemétől.
2. A `do.call()` függvény `rbind()` a függvényt alkalmazza a `lapply()`lista visszaadott elemeire.
3. A `data.frame()` függvény az adatkeret által `do.call()` létrehozott eredményt kényszeríti.

Ne feledje, hogy a sornevek az adatkeret egyik oszlopában találhatók. Ezzel megőrzi a sorneveket, amikor az R végrehajtása parancsfájlból kerülnek [kimenetre.][execute-r-script]

A kód futtatása a 19. **Visualize** A sornevek az első oszlopban találhatók, ahogy tervezték.

![A korrelációs elemzés eredményei](./media/r-quickstart/fig20.png)

*19. ábra. A korrelációs elemzés eredményei.*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Példa idősorokra: szezonális előrejelzés

Adataink most olyan formában vannak, amely alkalmas az elemzésre, és megállapítottuk, hogy nincs jelentős összefüggés a változók között. Lépjünk tovább, és hozzunk létre egy idősorozat-előrejelzési modellt. Ezzel a modellel 2013 12 hónapjára fogjuk előre jelezni a kaliforniai tejtermelést.

Előrejelzési modellünk két összetevőből áll, egy trendösszetevőből és egy szezonális összetevőből. A teljes előrejelzés e két összetevő szorzata. Ezt a modelltípust multiplikatív modellnek nevezik. Az alternatíva egy additív modell. Már alkalmaztunk naplóátalakítást az érdeklődési változókra, ami ezt az elemzést kezelhetővé teszi.

A szakasz teljes R-kódja a [MachineLearningSamples-Notebooks/studio-samples .](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)

### <a name="creating-the-dataframe-for-analysis"></a>Az adatkeret létrehozása elemzéshez

Először adjon hozzá egy **új** [R-parancsfájl-végrehajtás][execute-r-script] modult a kísérlethez. Csatlakoztassa a meglévő [Végrehajtási R Parancsfájl][execute-r-script] modul **eredményadatkészlet-kimenetét** az új modul **Dataset1** bemenetéhez. Az eredménynek valahogy a 20.

![A kísérlet az új Végrehajtási R Script modul hozzáadott](./media/r-quickstart/fig21.png)

*20. ábra. A kísérlet az új Végrehajtási R Script modul hozzáadott.*

Csakúgy, mint a korrelációs elemzés, amit most fejeztünk be, hozzá kell adnunk egy oszlopot egy POSIXct idősorozat-objektummal. A következő kód akarat csinál csak ez.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Futtassa ezt a kódot, és nézze meg a naplót. Az eredménynek a 21.

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

*21. ábra. Az adatkeret összegzése.*

Ezzel az eredménnyel készen állunk az elemzés megkezdésére.

### <a name="create-a-training-dataset"></a>Betanítási adatkészlet létrehozása

A létrehozott dataframe létre kell hoznunk egy betanítási adatkészletet. Ezek az adatok tartalmazzák az összes észrevételt, kivéve a 2013-as év utolsó 12-jét, amely a tesztadatkészletünk. A következő kód részre állítja az adatkeretet, és létrehozza a tejtermelés és az árváltozók mintáit. Ezután létre telkek a négy termelési és árváltozók. Egy névtelen függvény segítségével definiálja a telek néhány bővített változatait, majd a másik `Map()`két argumentum listáját a segítségével itatja. Ha azt gondolja, hogy a hurok jól működött volna itt, akkor igaza van. De mivel az R egy funkcionális nyelv, megmutatom a funkcionális megközelítést.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

A kód futtatása a 22. Ne feledje, hogy az időtengely dátumegységekben van, ami az idősorozat-nyomtatási módszer jó előnye.

![Az első idősorozat telkek a kaliforniai tejtermelés és az árak adatok](./media/r-quickstart/unnamed-chunk-161.png)

![Második idősorok telkek a kaliforniai tejtermelés és az árak adatok](./media/r-quickstart/unnamed-chunk-162.png)

![Harmadik idősorok telkek a kaliforniai tejtermelés és az árak adatok](./media/r-quickstart/unnamed-chunk-163.png)

![Negyedik idősorozat telkek a kaliforniai tejtermelés és az árak adatok](./media/r-quickstart/unnamed-chunk-164.png)

*22. ábra. Idősorozat telkek a kaliforniai tejtermelés és az árak adatait.*

### <a name="a-trend-model"></a>Trendmodell

Miután létrehozott egy idősorozat-objektumot, és áttekintette az adatokat, kezdjük el létrehozni a kaliforniai tejtermelési adatok trendmodelljét. Meg tudjuk csinálni egy idősorozat regresszió. Azonban egyértelmű, hogy a telek, hogy mi lesz szükség több, mint egy lejtőn, és elfogása, hogy pontosan modell a megfigyelt trend a betanítási adatok.

Tekintettel az adatok kis méretére, létrehozom a modellt az RStudio trendje számára, majd kivágom és beillesztem az eredményül kapott modellt az Azure Machine Learning Studio -ba (klasszikus). Az RStudio interaktív környezetet biztosít az ilyen típusú interaktív elemzésekhez.

Első próbálkozásként megpróbálok egy polinom regressziót 3-ig. Fennáll annak a veszélye, hogy túlszerelik az ilyen modelleket. Ezért a legjobb, ha elkerüljük a magas rendű feltételeket. A `I()` függvény gátolja a tartalom értelmezését (a tartalmat úgy értelmezi, ahogy van), és lehetővé teszi, hogy szó szerint értelmezett függvényt írjon egy regressziós egyenletbe.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Ez a következőket hozza létre.

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

A P`Pr(>|t|)`értékek ( ) ebben a kimenetben, láthatjuk, hogy a négyzetes kifejezés nem lehet jelentős. Fogom használni `update()` a funkciót, hogy módosítsa ezt a modellt csepegés a négyzetes kifejezés.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Ez a következőket hozza létre.

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

Ez jobban néz ki. Minden feltétel jelentős. A 2e-16 érték azonban alapértelmezett érték, ezért nem szabad túl komolyan venni.  

Mint egy józanság teszt, let's make a time series telek a kaliforniai tejtermelési adatok a trend görbe látható. A következő kódot adtam hozzá az Azure Machine Learning Studio (klasszikus) [R Script][execute-r-script] modell végrehajtása (nem RStudio) a modell létrehozásához, és egy telek. Az eredményt a 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Kaliforniai tejtermelési adatok trendmodellel](./media/r-quickstart/unnamed-chunk-18.png)

*23. ábra. Kaliforniai tejtermelési adatok a megjelenített trendmodellel.*

Úgy néz ki, mint a trend modell illeszkedik az adatok meglehetősen jól. Továbbá, nem úgy tűnik, hogy bizonyíték a túlzott illeszkedés, mint például a furcsa wiggles a modell görbe.  

### <a name="seasonal-model"></a>Szezonális modell

A trend modell a kezében, meg kell nyomni, és tartalmazza a szezonális hatások. Az év hónapját a lineáris modellben dummy változóként használjuk a hónapról hónapra hatás rögzítésére. Vegye figyelembe, hogy ha faktorváltozókat vezet be egy modellbe, az elfogást nem szabad kiszámítani. Ha ezt nem teszi meg, a képlet túl van adva, és az R eldobja az egyik kívánt tényezőt, de megtartja az elfogási kifejezést.

Mivel van egy kielégítő trend modell `update()` tudjuk használni a funkciót, hogy adjunk az új kifejezéseket a meglévő modell. A frissítési képletben lévő -1 elejtési kifejezést ejti. Folytatása RStudio ebben a pillanatban:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Ez a következőket hozza létre.

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

Azt látjuk, hogy a modell már nem egy elfogási kifejezés, és 12 jelentős hónap tényezők. Pontosan ezt akartuk látni.

Tegyünk egy másik idősorozat telek a kaliforniai tejtermelés adatok, hogy milyen jól működik a szezonális modell. Hozzáadtam a következő kódot az Azure Machine Learning Studio (klasszikus) [R-parancsfájl végrehajtása][execute-r-script] a modell létrehozásához, és egy telek.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Ez a kód futtatása az Azure Machine Learning Studio (klasszikus) a 24.

![Kaliforniai tejtermelés modellel, beleértve a szezonális hatásokat is](./media/r-quickstart/unnamed-chunk-20.png)

*24. ábra. Kaliforniai tejtermelés modellel, beleértve a szezonális hatásokat is.*

A 24. Mind a trend, mind a szezonális hatás (havi változás) ésszerűnek tűnik.

A modellünk újabb ellenőrzéseként vessünk egy pillantást a maradványokra. A következő kód kiszámítja a két modell előre jelzett értékeit, kiszámítja a szezonális modell maradékait, majd ezeket a maradékokat a betanítási adatokhoz.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

A maradék mintarészt a 25.

![A betanítási adatok szezonális modelljének maradékai](./media/r-quickstart/unnamed-chunk-21.png)

*25. ábra. A betanítási adatok szezonális modelljének maradékai.*

Ezek a maradványok ésszerűnek tűnnek. Nincs különösebb struktúra, kivéve a 2008-2009-es recesszió hatását, amelyet modellünk nem különösebben jól figyelembe.

A 25. Az explicit megközelítés a számítástechnika és a nyomtatás a maradékot használtam helyezi a maradékot időben, hogy a telek. Ha viszont, én már ábrázolt, `milk.lm$residuals`a telek nem lett volna időben.

Diagnosztikai telkek `plot.lm()` sorozatának előállítására is használható.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Ez a kód a 26.

![A szezonális modell diagnosztikai mintájának első része](./media/r-quickstart/unnamed-chunk-221.png)

![A szezonális modell diagnosztikai mintájának második része](./media/r-quickstart/unnamed-chunk-222.png)

![A szezonális modell diagnosztikai mintarészeinek harmada](./media/r-quickstart/unnamed-chunk-223.png)

![A szezonális modell diagnosztikai mintájának negyedik része](./media/r-quickstart/unnamed-chunk-224.png)

*26. ábra. A szezonális modell diagnosztikai mintázatai.*

Van néhány igen befolyásos pontokat azonosítottak ezekben a telkek, de semmi sem okoz nagy aggodalomra ad okot. Továbbá, láthatjuk a normál Q-Q telek, hogy a maradékok közel vannak a rendesen elosztott, fontos feltételezés a lineáris modellek.

### <a name="forecasting-and-model-evaluation"></a>Előrejelzés és modellértékelés

Már csak egy dolog van, amit tehetünk, hogy befejezsük a példánkat. Ki kell számolnunk az előrejelzéseket, és a hibát a tényleges adatokhoz kell bevetnünk. Előrejelzésünk 2013 12 hónapjára lesz. Kiszámíthatunk egy hibamértéket ehhez az előrejelzéshez a tényleges adatokra, amelyek nem részei a betanítási adatkészletünknek. Ezenkívül összehasonlíthatjuk a 18 éves betanítási adatok teljesítményét a 12 hónapos tesztadatokkal.  

Az idősorozat-modellek teljesítményének mérésére számos metrika használatos. A mi esetünkben fogjuk használni a gyökér átlagos négyzet (RMS) hiba. A következő függvény két adatsor között számítja ki az RMS-hibát.  

```R
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
```

Az "Értékátalakítások" részben tárgyalt `log.transform()` függvényhez ugyanúgy, mint az "Értékátalakítások" című szakaszban, ebben a függvényben elég sok hiba-ellenőrzési és kivétel-helyreállítási kód van. Az alkalmazott elvek ugyanazok. A munkát két helyen végezzük csomagolva. `tryCatch()` Először is, az idősorozat exponenciális, mivel már dolgozik a naplókat az értékek. Másodszor, a tényleges RMS-hiba kiszámításra kerül.  

Az RMS-hiba mérésére funkcióval felszerelve hozzunk létre és adjunk ki egy adatkeretet, amely tartalmazza az RMS hibákat. Mi is feltételeket a trend modell egyedül és a teljes modell szezonális tényezők. A következő kód a két lineáris modell használatával végzi el a munkát.

```R
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
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

A kód futtatása a 27.

![Az RMS-hibák összehasonlítása a modelleknél](./media/r-quickstart/fig26.png)

*27. ábra. Az RMS-hibák összehasonlítása a modelleknél.*

Ezekből az eredményekből azt látjuk, hogy a szezonális tényezők hozzáadása a modell jelentősen csökkenti az RMS-hiba. Nem túl meglepő, hogy a betanítási adatok RMS-hibája egy kicsit kevesebb, mint az előrejelzésnél.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Útmutató az RStudio dokumentációjához

RStudio van egész jól dokumentált. Íme néhány link az RStudio dokumentációjának kulcsfontosságú szakaszaira a kezdéshez.

* **Projektek létrehozása** – Az R-kódot az RStudio használatával projektekbe rendezheti és kezelheti. A részletekért olvassa el a [Projektek használata.](https://support.rstudio.com/hc/articles/200526207-Using-Projects) Azt javaslom, hogy kövesse ezeket az utasításokat, és hozzon létre egy projektet az R-kód példák ebben a cikkben.  
* **Az R-kód szerkesztése és végrehajtása** - Az RStudio integrált környezetet biztosít az R-kód szerkesztéséhez és végrehajtásához. A részleteket a [Kód szerkesztése és végrehajtása](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) tartalmazza.
* **Hibakeresés** – Az RStudio hatékony hibakeresési lehetőségeket tartalmaz. Ezekről a szolgáltatásokról az [RStudio hibakeresés](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) című témakörben talál további információt. A töréspont hibaelhárítási szolgáltatásairól a [Töréspont hibaelhárítás című témakörben](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)talál további információt.

## <a name="further-reading"></a><a id="appendixb"></a>További információ

Ez az R programozási oktatóanyag ismerteti az alapokat, hogy mit kell használni az R nyelvet az Azure Machine Learning Studio (klasszikus). Ha nem ismeri az R-t, két bemutatkozás érhető el a CRAN-on:

* [R kezdőknek](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) Emmanuel Paradis egy jó kiindulópont lehet.  
* [Bevezetés az R](https://cran.r-project.org/doc/manuals/R-intro.html) W. N. Venables és én. Al. egy kicsit mélyebbre megy.

Sok könyvet R, amely segít az induláshoz. Íme néhány találhasznos:

* Az **Art of R programozás: A Tour statisztikai szoftver design** Norman Matloff egy kiváló bevezetés a programozás R.  
* **R Cookbook** Paul Teetor nyújt problémát és megoldást megközelítést az R.  
* **R in Action** Robert Kabacoff egy másik hasznos bevezető könyv. A társ [Quick R honlapján](https://www.statmethods.net/) egy hasznos forrás.
* **R Inferno** Patrick Burns egy meglepően humoros könyv, amely foglalkozik számos trükkös és nehéz téma, hogy lehet találkozni, ha programozás R. A könyv ingyenesen elérhető a [The R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Ha szeretne egy mély merülés a fejlett témák R, vessen egy pillantást a könyv **Advanced R** Hadley Wickham. A könyv online változata ingyenesen [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/)elérhető a.

Az R idősorozat-csomagok katalógusa megtalálható a [CRAN Task View: Time Series Analysis (Idősorozat-elemzés](https://cran.r-project.org/web/views/TimeSeries.html)) című részben. Az egyes idősorozat-objektumcsomagokról az adott csomag dokumentációjában tájékozódhat.

Paul Cowpertwait és Andrew Metcalfe **R-rel című könyve** bemutatja az R használatát az idősorok elemzéséhez. Sokkal több elméleti szöveg nyújt R példákat.

Íme néhány nagy internetes források:

* A DataCamp videóleckékkel és kódolási gyakorlatokkal tanítja az R-t a böngésző kényelmében. Vannak interaktív útmutatók a legújabb R technikák és csomagok. Vegyük az ingyenes [interaktív R bemutató](https://www.datacamp.com/courses/introduction-to-r).
* [Ismerje meg R programozás, a végleges útmutató](https://www.programiz.com/r-programming) a Programiz.
* Egy gyors [R tutorial](https://www.cyclismo.org/tutorial/R/) Kelly Black a Clarkson Egyetem.
* Több mint 60 R erőforrás szerepel a [Top R nyelvi erőforrásokban az adattudás javítása érdekében.](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html)

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
