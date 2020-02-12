---
title: Első lépések az R-vel
titleSuffix: ML Studio (classic) - Azure
description: Ezzel az R programozási oktatóanyaggal megkezdheti az R nyelv és a Azure Machine Learning Studio (klasszikus) használatának megkezdését az előrejelzési megoldás létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 3972fb3c0717069f84b177c54e8fc002ec52f469
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152873"
---
# <a name="getting-started-with-the-r-programming-language-in-azure-machine-learning-studio-classic"></a>Első lépések az R programozási nyelv Azure Machine Learning Studio (klasszikus)

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introduction (Bevezetés)

Ez az oktatóanyag segítséget nyújt a Azure Machine Learning Studio (klasszikus) kiterjesztésének megkezdéséhez az R programozási nyelv használatával. Kövesse ezt az R programozási oktatóanyagot az R-kód létrehozásához, teszteléséhez és végrehajtásához a studión belül (klasszikus). Az oktatóanyagban végzett munka során egy teljes előrejelzési megoldást fog létrehozni a Studio (klasszikus) R-nyelvének használatával.  

A Azure Machine Learning Studio (klasszikus) számos hatékony gépi tanulási és adatkezelési modult tartalmaz. A nagy teljesítményű R nyelv leírása a lingua franca of Analytics. Szerencsére a Studio (klasszikus) elemzési és adatkezelési szolgáltatását az R használatával lehet kiterjeszteni. Ez a kombináció a Studio (klasszikus) méretezhetőségét és egyszerű üzembe helyezését teszi lehetővé az R rugalmasságával és mélyreható elemzésével.

### <a name="forecasting-and-the-dataset"></a>Előrejelzés és az adatkészlet

Az előrejelzés egy széles körben alkalmazott és nagyon hasznos analitikai módszer. A gyakori felhasználási tartomány a szezonális elemek előrejelzése, az optimális leltározási szintek meghatározása a makrogazdasági változók előrejelzéséhez. Az előrejelzés általában idősorozat-modellekkel történik.

Az idősorozat-adatok olyan adatok, amelyekben az értékek egy időindextel rendelkeznek. Az időindex lehet rendszeres, például havonta vagy percenként, vagy szabálytalan. Az idősorozat-modell az idősorozat-adatsorokon alapul. Az R programozási nyelv rugalmas keretrendszert és átfogó elemzést tartalmaz az idősorozat-adatokat illetően.

Ebben az útmutatóban a kaliforniai tejtermékek termelési és díjszabási információit fogjuk dolgozni. Ezek az adatok a különböző tejtermékek előállításával, valamint a tejzsír árának összehasonlításával kapcsolatos havi információkat tartalmazzák.

A cikkben használt adatok, valamint az R-parancsfájlok is letölthetők a [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)-ből. A (z) `cadairydata.csv` fájlban lévő adatokat eredetileg a Wisconsini Egyetemen elérhető, [https://dairymarkets.com](https://dairymarkets.com)címen található információk alapján állították elő.

### <a name="organization"></a>Szervezet

A Azure Machine Learning Studio (klasszikus) környezetben az elemzési és adatkezelési R-kód létrehozásával, tesztelésével és végrehajtásával kapcsolatban számos lépéssel elsajátítjuk a folyamatokat.  

* Először is megismerheti az R nyelv használatának alapjait a Azure Machine Learning Studio (klasszikus) környezetben.
* Ezt követően megbeszéljük az I/O adatkezelési szempontjait, az R-kódot és a grafikát a Azure Machine Learning Studio (klasszikus) környezetben.
* Az előrejelzési megoldás első részét az adattisztítás és-átalakítás kódjának létrehozásával fogjuk létrehozni.
* Az adatok előkészítésekor a rendszer elvégzi az adatkészlet számos változója közötti korrelációk elemzését.
* Végezetül létrehozunk egy szezonális idősorozat-előrejelzési modellt a tejtermékek termeléséhez.

## <a id="mlstudio"></a>Az R nyelvvel való interakció Machine Learning Studio (klasszikus)

Ez a szakasz végigvezeti az R programozási nyelv Machine Learning Studio (klasszikus) környezetben való interakciójának alapjain. Az R nyelv hatékony eszközt biztosít a testreszabott elemzési és adatkezelési modulok létrehozásához a Azure Machine Learning Studio (klasszikus) környezetben.

A RStudio használatával kis méretben fejlesztem, tesztelni és hibakeresést végezhetek az R-kódban. Ezt a kódot ezután kivágja és beilleszti egy [végrehajtási R script][execute-r-script] modulba, amely készen áll a Azure Machine learning Studio (klasszikus) futtatására.  

### <a name="the-execute-r-script-module"></a>Az R-szkript végrehajtása modul

Machine Learning Studio (klasszikus) belül az R-parancsfájlok az R- [szkript végrehajtása][execute-r-script] modulon belül futnak. Az 1. ábrán látható példa az [R-szkriptek végrehajtása][execute-r-script] Machine learning Studio (klasszikus) modulra.

 ![R programozási nyelv: a Machine Learning Studioben kiválasztott R-szkript végrehajtása modul (klasszikus)](./media/r-quickstart/fig1.png)

*1. ábra. Az Machine Learning Studio (klasszikus) környezet, amely az R-szkript végrehajtásának kiválasztására szolgáló modult jeleníti meg.*

Az 1. ábrára hivatkozva vizsgáljuk meg az Machine Learning Studio (klasszikus) környezet néhány kulcsfontosságú részét az [R-parancsfájl végrehajtása][execute-r-script] modul használatához.

* A kísérlet moduljai a középső ablaktáblán jelennek meg.
* A jobb oldali ablaktábla felső részén található az R-parancsfájlok megtekintésére és szerkesztésére szolgáló ablak.  
* A jobb oldali ablaktábla alsó részén az [R-szkript végrehajtásának][execute-r-script]néhány tulajdonsága látható. A hibát és a kimeneti naplókat a panel megfelelő pontjainak kiválasztásával tekintheti meg.

A cikk további részében természetesen az [R szkript végrehajtásának][execute-r-script] részletes ismertetését fogjuk megbeszélni.

Összetett R-függvények használata esetén azt javasoljuk, hogy szerkessze, tesztelje és végezzen hibakeresést a RStudio-ben. Csakúgy, mint bármely szoftverfejlesztés esetében, fokozatosan kiterjesztheti a kódot, és tesztelheti kis, egyszerű tesztelési esetekben. Ezután vágja ki és illessze be a függvényeket az R-szkript [végrehajtása][execute-r-script] modul r-szkript ablakába. Ez a módszer lehetővé teszi a RStudio integrált fejlesztési környezet (IDE) és a Azure Machine Learning Studio (klasszikus) teljesítményének kihasználását.  

#### <a name="execute-r-code"></a>R-kód végrehajtása

Az [r-szkript végrehajtása][execute-r-script] modulban az r-kód a **Futtatás** gomb kiválasztásával fog futni a kísérlet futtatásakor. A végrehajtás befejezését követően egy pipa jelenik meg az R- [szkript végrehajtása][execute-r-script] ikonon.

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>A Azure Machine Learning védelmi R-kódolása

Ha R-kódot fejleszt, például egy webszolgáltatást Azure Machine Learning Studio (klasszikus) használatával, érdemes megterveznie, hogyan kezelje a kód váratlan adatbevitelt és kivételeket. Az egyértelműség érdekében a kód legtöbb példájában nem szerepelt az ellenőrzés vagy a kivétel kezelésének módja. A folytatáshoz azonban több példát is biztosítunk a függvények számára az R kivétel-kezelési képességének használatával.  

Ha az R-kivételek kezelésének átfogóbb kezelésére van szüksége, akkor azt javasoljuk, hogy olvassa el a könyv megfelelő részeit az alább felsorolt Wickham [további olvasnivalóban](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Hibakeresés és tesztelés az R-ben Machine Learning Studio (klasszikus)

Az ismételt próbálkozáshoz azt javasoljuk, hogy az R-kódot kis méretekben tesztelje és hibakeresése a RStudio-ben. Vannak azonban olyan esetek, amikor le kell követnie az R-kód problémáit az [r-szkript végrehajtása][execute-r-script] során. Emellett érdemes megnézni az eredményeket Machine Learning Studio (klasszikus).

Az R-kód és a Azure Machine Learning Studio (klasszikus) platform végrehajtásának kimenete elsődlegesen a kimenet. log naplófájlban található. Néhány további információ jelenik meg a error. log naplófájlban.  

Ha az R-kód futtatása közben Machine Learning Studio (klasszikus) hiba történik, az első lépés a következő: error. log. Ez a fájl hasznos hibaüzeneteket tartalmazhat, amelyek segítenek megérteni és elhárítani a hibát. A hiba. log naplófájl megtekintéséhez a hibát tartalmazó [végrehajtási R-parancsfájl][execute-r-script] **Tulajdonságok paneljén** válassza a **hibanapló megtekintése** lehetőséget.

Például a következő R-kódot futtattam egy nem definiált változó y értékkel egy [Execute R script][execute-r-script] modulban:

```R
x <- 1.0
z <- x + y
```

Ez a kód nem hajtható végre, ami egy hiba feltétele. Ha a **Tulajdonságok panelen** a **hibanapló megtekintése** elemre kattint, a a 2. ábrán látható megjelenítést eredményezi.

  ![Hibaüzenet – felugró ablak](./media/r-quickstart/fig2.png)

*2. ábra. Hibaüzenet: előugró ablak.*

Úgy tűnik, hogy az R-hibaüzenet megjelenítéséhez meg kell keresnie a kimenet. log naplófájlt. Válassza az [R-szkript végrehajtása][execute-r-script] lehetőséget, majd a **Tulajdonságok ablaktáblán** kattintson a **kimenet megtekintése. log** elemre a jobb oldalon. Megnyílik egy új böngészőablak, és megjelenik a következő.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ez a hibaüzenet nem tartalmaz meglepetéseket, és egyértelműen azonosítja a problémát.

Az R-ben található bármely objektum értékének vizsgálatához ezeket az értékeket a kimeneti. log fájlba is kinyomtathatja. Az objektumok értékeinek vizsgálatára szolgáló szabályok lényegében megegyeznek az interaktív R-munkamenetekben. Ha például egy sorba írja be a változó nevét, az objektum értéke a kimenet. log fájlba lesz kinyomtatva.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Csomagok Machine Learning Studio (klasszikus)

A Studio több mint 350 előre telepített R nyelvi csomagot tartalmaz. Az előtelepített csomagok listájának beolvasásához használja a következő kódot az [R-parancsfájl végrehajtása][execute-r-script] modulban.

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Ha jelenleg nem érti a kód utolsó sorát, olvassa el a következőt:. A cikk további részében részletesen tárgyaljuk az R használatát a Studio (klasszikus) környezetben.

### <a name="introduction-to-rstudio"></a>A RStudio bemutatása

A RStudio egy széles körben használt IDE az R-hez. RStudio használok a jelen útmutatóban használt R-kód némelyikének szerkesztéséhez, teszteléséhez és hibakereséséhez. Miután megtörtént az R-kód tesztelése és elkészítése, egyszerűen kivágással és beillesztéssel a RStudio-szerkesztőből egy Machine Learning Studio (klasszikus) [r script][execute-r-script] modulba.  

Ha nincs telepítve az R programozási nyelv az asztali gépen, azt javasoljuk, hogy tegye meg most. A nyílt forráskódú R nyelv ingyenes letöltése a [https://www.r-project.org/](https://www.r-project.org/)címen elérhető átfogó r Archive Network (Cran) címen érhető el. Letölthetők a Windows, Mac OS és a Linux/UNIX rendszerhez készült letöltések. Válasszon egy közeli tükrözést, és kövesse a letöltési utasításokat. Emellett a CRAN számos hasznos elemzési és adatmanipulációs csomagot tartalmaz.

Ha még nem ismeri a RStudio, töltse le és telepítse az asztali verziót. A RStudio a Windows, Mac OS és Linux/UNIX rendszerű letöltéseit http://www.rstudio.com/products/RStudio/címen találja. Kövesse a RStudio telepítéséhez szükséges utasításokat az asztali gépen.  

A RStudio oktatóanyagának bemutatása [a RSTUDIO ide használatával](https://support.rstudio.com/hc/sections/200107586-Using-RStudio)érhető el.

További információkra van szükségem a RStudio használatáról az [útmutatóban az alábbi RStudio dokumentációban](#appendixa) .  

## <a id="scriptmodule"></a>Az R-szkript végrehajtása modulba beolvasott és kívüli adatlekérdezés

Ebből a szakaszból megtudhatja, hogyan szerezhet be és ki az [R-szkriptek végrehajtására][execute-r-script] szolgáló modult. Áttekintjük, hogyan kezelheti a különböző adattípusokat az R- [szkript végrehajtása][execute-r-script] modulból, illetve onnan.

A szakasz teljes kódja a [MachineLearningSamples-notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Machine Learning Studio (klasszikus) betöltési és ellenőrzési szolgáltatás

#### <a id="loading"></a>Az adatkészlet betöltése

Először töltse be a **csdairydata. csv** fájlt Azure Machine learning Studioba (klasszikus).

1. Indítsa el a Azure Machine Learning Studio (klasszikus) környezetet.
1. Válassza a képernyő bal alsó sarkában található **+ új** lehetőséget, majd válassza az **adatkészlet**lehetőséget.
1. Válasszon a **helyi fájlból**, majd **tallózással** válassza ki a fájlt.
1. Győződjön meg arról, hogy az adatkészlet típusaként az **általános CSV-fájlt (. csv)** jelölte meg.
1. Jelölje be a pipa.
1. Az adatkészlet feltöltése után az új adatkészletet az **adatkészletek** lap kiválasztásával tekintheti meg.  

#### <a name="create-an-experiment"></a>Kísérlet létrehozása

Most, hogy Machine Learning Studio (klasszikus) adataink vannak, létre kell hozni egy kísérletet az elemzés elvégzéséhez.  

1. Válassza az **+ új** lehetőséget a bal alsó sarokban, majd válassza a **kísérlet**, majd az **üres kísérlet**lehetőséget.
1. A kísérletet a lap tetején, a **... címmel létrehozott kísérlet** kiválasztásával és módosításával nevezheti el. Például módosítsa a **hitelesítésszolgáltatói tejtermékek elemzését**.
1. A kísérlet lap bal oldalán bontsa ki a **mentett adatkészletek**, majd **az adatkészletek**elemet. Ekkor meg kell jelennie a korábban feltöltött **cadairydata. csv** fájlnak.
1. Húzza át a **csdairydata. csv adatkészletet** a kísérletbe.
1. A bal oldali ablaktábla felső részén található **kísérletezési elemek keresése** mezőben írja be az [R-parancsfájl végrehajtása][execute-r-script]parancsot. Ekkor megjelenik a modul a keresési listán.
1. Húzza az [R szkript végrehajtása][execute-r-script] modult a raklapra.  
1. A **csdairydata. csv adatkészlet** kimenetének összekötése az [R szkript][execute-r-script]bal szélső bemenetével (**DataSet1 elemet**).
1. **Ne felejtse el kiválasztani a mentést!**  

Ekkor a kísérletnek a 3. ábrához hasonlóan kell kinéznie.

![A CA Dairy Analysis-kísérlet az adatkészlet és az R-parancsfájl végrehajtása modullal](./media/r-quickstart/fig3.png)

*3. ábra. A CA Dairy Analysis kísérlet az adatkészlettel és az R-parancsfájl végrehajtása modullal.*

#### <a name="check-on-the-data"></a>Az adatkeresés

Tekintsük át a kísérletbe betöltött adatgyűjtést. A kísérletben válassza ki a **cadairydata. csv adatkészlet** kimenetét, és válassza a **Megjelenítés**lehetőséget. A 4. ábrához hasonlóan kell megjelennie.  

![A cadairydata. csv adatkészlet összefoglalása](./media/r-quickstart/fig4.png)

*4. ábra. A cadairydata. csv adatkészlet összefoglalása.*

Ebben a nézetben sok hasznos információ látható. Az adatkészlet első néhány sorát láthatjuk. Ha kiválasztunk egy oszlopot, a statisztikai szakasz további információkat jelenít meg az oszlopról. A szolgáltatás típusa sor például megmutatja, hogy milyen adattípusok vannak hozzárendelve az oszlophoz Azure Machine Learning Studio (klasszikus). Egy gyors kinézete, hogy ez egy jó józanság, mielőtt elkezdjük a komoly munkát.

### <a name="first-r-script"></a>Első R-szkript

Hozzunk létre egy egyszerű, első R-szkriptet, amely Azure Machine Learning Studio (klasszikus) belül kísérletezni próbál. Létrehoztam és teszteltem a következő szkriptet a RStudio-ben.  

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

Most ezt a szkriptet át kell Azure Machine Learning Studio (klasszikus). Egyszerűen kivágással és beillesztéssel. Ebben az esetben azonban az R-szkriptet egy zip-fájlon keresztül viszem át.

### <a name="data-input-to-the-execute-r-script-module"></a>Adatok bevitele az R-parancsfájl végrehajtása modulba

Tekintse át az [R szkript végrehajtása][execute-r-script] modul bemeneteit. Ebben a példában beolvasjuk a kaliforniai tejtermékek szolgáltatásait az [R szkript végrehajtása][execute-r-script] modulba.  

Az [R-szkript végrehajtása][execute-r-script] modulnak három lehetséges bemenete van. Az alkalmazástól függően a bemenetek bármelyikét vagy mindegyikét használhatja. Emellett tökéletesen ésszerű olyan R-szkriptet használni, amely nem tesz elérhetővé semmilyen bemenetet.  

Nézzük meg ezeket a bemeneteket, balról jobbra haladva. Az egyes bemenetek nevét megtekintheti, ha az egérmutatót a bemenet fölé helyezi, és beolvassa az elemleírást.  

#### <a name="script-bundle"></a>Parancsfájl-csomag

A parancsfájl-csomag bemenete lehetővé teszi egy zip-fájl tartalmának átadását az [R-parancsfájl végrehajtása][execute-r-script] modulba. Az alábbi parancsok egyikével beolvashatja a zip-fájl tartalmát az R-kódjába.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (klasszikus) a zip-fájlokban lévő fájlokat úgy kezeli, mintha a src/könyvtárban vannak, ezért a fájlneveket előtaggal kell elvégeznie. Ha például a zip a zip-fájl gyökerében `yourfile.R` és `yourData.rdata` fájlokat tartalmazza, akkor `source` és `load`használatakor ezeket `src/yourfile.R`ként és `src/yourData.rdata`ként fogja kezelni.

Már beszéltünk az adathalmaz [betöltési](#loading)adatkészletének betöltéséről. Miután létrehozta és tesztelte az előző szakaszban bemutatott R-szkriptet, tegye a következőket:

1. Mentse az R-szkriptet a-ba. R-fájl. Meghívom a "simpleplot" szkriptet. R ". Itt látható a tartalom.

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

1. Hozzon létre egy zip-fájlt, és másolja a szkriptet ebbe a zip-fájlba. Windows rendszeren kattintson a jobb gombbal a fájlra, és válassza a **Küldés**, majd a **tömörített mappa**lehetőséget. Ezzel létrehoz egy új zip-fájlt, amely a következőt tartalmazza: "simpleplot. R "fájl.

1. Adja hozzá a fájlt a Azure Machine Learning Studio (klasszikus) **adatkészletekhez** , és adja meg a típust **zip**-ként. Ekkor megjelenik a zip-fájl az adatkészletekben.

1. Húzza a zip-fájlt az **adatkészletek** rajzsablonról a **ml Studio (klasszikus) vászonra**.

1. A **zip-adatok** ikon kimenetének összekötése az [R-parancsfájl végrehajtása][execute-r-script] modul **parancsfájl-kötegbeli** bemenetével.

1. Írja be a `source()` függvényt a zip-fájl nevével az R- [szkript végrehajtása][execute-r-script] modul kódjának ablakába. Az én esetemben begépeltem `source("src/simpleplot.R")`.  

1. Győződjön meg róla, hogy a **Mentés**gombra kattint.

A lépések befejezését követően az [r szkript végrehajtása][execute-r-script] modul végrehajtja az r-szkriptet a zip-fájlban a kísérlet futtatásakor. Ekkor a kísérletnek az 5. ábrához hasonlóan kell kinéznie.

![Kísérletezés a tömörített R-szkript használatával](./media/r-quickstart/fig6.png)

*5. ábra. Próbálja ki a tömörített R-szkriptet.*

#### <a name="dataset1"></a>DataSet1 elemet

A Dataset1 elemet bemenet használatával átadhat egy téglalap alakú adattábla adatait az R-kódra. Az egyszerű szkriptben a `maml.mapInputPort(1)` függvény beolvassa az 1. port adatait. Ezt követően a rendszer a kódban egy dataframe-változóhoz rendeli hozzá ezeket az adattípusokat. Az egyszerű szkriptben a kód első sora hajtja végre a hozzárendelést.

```R
cadairydata <- maml.mapInputPort(1)
```

A kísérlet végrehajtásához kattintson a **Futtatás** gombra. A végrehajtás befejeződése után válassza az R- [parancsfájl végrehajtása][execute-r-script] modult, majd a Tulajdonságok ablaktáblán válassza a **kimeneti napló megtekintése** lehetőséget. Egy új oldalnak kell megjelennie a böngészőben, amely a kimeneti. log fájl tartalmát mutatja. Ha lefelé görget, a következőhöz hasonlónak kell megjelennie.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

A lap részletesebben részletezi az oszlopokat, ami a következőhöz hasonlóan fog kinézni.

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

Ezek az eredmények többnyire a vártnak megfelelően működnek, és a dataframe a 228-es és 9 oszlopot is megtekintheti. Láthatjuk az oszlopok nevét, az R-adattípust és az egyes oszlopok mintáit.

> [!NOTE]
> Ugyanez a nyomtatott kimenet kényelmesen elérhető az [r-szkript végrehajtása][execute-r-script] modul r Device kimenetében. A következő szakaszban megbeszéljük az [R szkript végrehajtása][execute-r-script] modul kimeneteit.  

#### <a name="dataset2"></a>Dataset2

A Dataset2-bemenet viselkedése megegyezik a Dataset1 elemet. Ennek a bemenetnek a használatával egy második téglalap alakú táblázatot adhat át az R-kódjába. Az adat átadására a 2. argumentummal `maml.mapInputPort(2)`függvény szolgál.  

### <a name="execute-r-script-outputs"></a>R-szkriptek kimenetének végrehajtása

#### <a name="output-a-dataframe"></a>Dataframe kimenete

Az R-dataframe tartalmát téglalap alakú táblázatként is kiválaszthatja az eredményül kapott Dataset1 elemet-porton keresztül az `maml.mapOutputPort()` függvény használatával. Az egyszerű R-szkriptben ezt a következő sor hajtja végre.

```
maml.mapOutputPort('cadairydata')
```

A kísérlet futtatása után válassza ki az eredmény Dataset1 elemet kimeneti portot, majd válassza a **Megjelenítés**lehetőséget. A 6. ábrához hasonlóan kell megjelennie.

![A California Dairy-adatokat megjelenítő kimenet megjelenítése](./media/r-quickstart/fig7.png)

*6. ábra. A kaliforniai tejtermékek kimenetének vizualizációja.*

Ez a kimenet megegyezik a bemenettel, pontosan a várt módon.  

### <a name="r-device-output"></a>R-eszköz kimenete

Az [R-szkript végrehajtása][execute-r-script] modul kimenete üzenetekkel és grafikus kimenettel rendelkezik. Az R szabványos kimeneti és standard hibaüzeneteket küld az R-eszköz kimeneti portjára.  

Az R-eszköz kimenetének megtekintéséhez válassza ki a portot, majd a **megjelenítéshez**. Az R-szkript standard kimenete és standard hibája a 7. ábrán látható.

![Standard kimenet és standard hiba az R-eszköz portján](./media/r-quickstart/fig8.png)

*7. ábra. Standard kimenet és standard hiba az R-eszköz portján.*

Görgessen lefelé a 8. ábrán látható R-szkriptek grafikus kimenete.  

![Az R-eszköz portjának grafikus kimenete](./media/r-quickstart/fig9.png)

*8. ábra. Az R-eszköz portjának grafikus kimenete.*  

## <a id="filtering"></a>Adatszűrés és-átalakítás

Ebben a szakaszban néhány alapszintű adatszűrési és-átalakítási műveletet fogunk végrehajtani a kaliforniai tejtermékek adatain. Ennek a szakasznak a végére az analitikai modell létrehozásához megfelelő formátumú adatformátummal fogunk rendelkezni.  

Pontosabban, ebben a szakaszban számos általános adattisztítási és átalakítási feladatot végzünk: átalakítás, dataframes szűrés, új számított oszlopok hozzáadása és érték-átalakítások. Ez a háttér segíthet a valós problémákkal kapcsolatos számos változat kezelésében.

A szakasz teljes R-kódja a [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)-ben érhető el.

### <a name="type-transformations"></a>Átalakítások típusa

Most, hogy beolvasjuk a California Dairy-beli adattárat az r-kódban az [r-szkript végrehajtása][execute-r-script] modulban, biztosítaniuk kell, hogy az oszlopokban lévő információk a kívánt típussal és formátummal legyenek elküldve.  

Az R egy dinamikusan beírt nyelv, ami azt jelenti, hogy az adattípusokat az egyikről a másikra kell kényszeríteni. Az R-beli atomi adattípusok a következők: numerikus, logikai és karakter. A faktor típusa a kategorikus adattárolási művelet tömörítésére szolgál. Az adattípusokról az alábbi [További olvasnivalók](#appendixb) hivatkozásaiban talál további információt.

Ha a táblázatos adatokat egy külső forrásból olvassa be az R-be, mindig érdemes megtekinteni az eredményül kapott típusokat az oszlopokban. Előfordulhat, hogy egy karakter típusú oszlopra van szükség, de a legtöbb esetben ez faktorként jelenik meg, vagy fordítva. Más esetekben egy olyan oszlopot, amelyet úgy gondol, hogy numerikus értéket képvisel, például "1,23", nem pedig 1,23, lebegőpontos számként.  

Szerencsére az egyik típust könnyen át lehet alakítani egy másikra, amíg a leképezés lehetséges. Például nem alakíthatja át a "Nevada" értéket numerikus értékké, de a faktor (kategorikus változó) értékre is konvertálhatja. Egy másik példaként átalakíthatja a numerikus 1-től az "1" karaktert vagy egy faktort.  

A konverziók szintaxisa egyszerű: `as.datatype()`. Ilyen típusú átalakítási függvények a következők:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Az előző szakaszban megadott oszlopok adattípusai megvizsgálva: az összes oszlop numerikus típusú, a "Month" feliratú oszlop kivételével, amely karakter típusú. Alakítsa át ezt egy tényezőre, és tesztelje az eredményeket.  

Törölte azt a sort, amely létrehozta a scatterplot mátrixot, és hozzáadta a "Month" oszlopot egy tényezőhöz. A saját kísérletben most kivágtam és beillesztem az R-kódot az [r-szkript végrehajtása][execute-r-script] modul kódjának ablakába. Frissítheti a zip-fájlt, és feltöltheti Azure Machine Learning Studio (klasszikus), de ez több lépést is igénybe vehet.  

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

Hajtsa végre ezt a kódot, és tekintse meg az R-szkript kimeneti naplóját. A naplóból származó releváns adatok a 9. ábrán láthatók.

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

*9. ábra. A dataframe összefoglalása a faktor változóval.*

A hónap típusának most a "**Factor w/14 szint" értéket**kell mondania. Ez egy probléma, mivel az év során csak 12 hónap van. Azt is megtekintheti, hogy az eredmény adatkészlet-port **megjelenítésének** típusa "**kategorikus**".

A probléma az, hogy a "Month" oszlop nem lett szisztematikusan kódolva. Bizonyos esetekben a hónapot áprilisban, másokban pedig Apr-ként rövidítjük. Ezt a problémát úgy oldhatja meg, hogy a sztringet 3 karakterre vágja. A kód most a következőhöz hasonlóan néz ki:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Futtassa újra a kísérletet, és tekintse meg a kimeneti naplót. A várt eredmények a 10. ábrán láthatók.  

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

*10. ábra. A dataframe megfelelő számú tényezőjének összefoglalása.*

A faktor változó most már a kívánt 12 szinttel rendelkezik.

### <a name="basic-data-frame-filtering"></a>Alapszintű adatkeret-szűrés

Az R dataframes támogatja a hatékony szűrési képességeket. Az adatkészletek kiállíthatók a logikai szűrők használatával sorokon vagy oszlopokon. Sok esetben az összetett szűrési feltételekre lesz szükség. Az alábbi [további olvasnivalóban](#appendixb) található hivatkozások részletes példákat tartalmaznak a szűrés dataframes.  

Az adathalmazon egy kis szűrésre van szükségünk. Ha megtekinti a cadairydata dataframe lévő oszlopokat, két felesleges oszlop jelenik meg. Az első oszlop csak sorszámmal rendelkezik, ami nem nagyon hasznos. A második oszlop, év. hónap, redundáns adatokat tartalmaz. Ezeket az oszlopokat egyszerűen kizárhatja a következő R-kód használatával.

> [!NOTE]
> Mostantól ebben a szakaszban az [R-szkriptek végrehajtása][execute-r-script] modulban felvenni kívánt további kódokat is megmutatom. Új sort adok hozzá az `str()` függvény **előtt** . Ezt a funkciót használom a Azure Machine Learning Studio (klasszikus) eredményeinek ellenőrzéséhez.

Az [r-szkript végrehajtása][execute-r-script] modulban adja hozzá a következő sort az r-kódhoz.

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Futtassa ezt a kódot a kísérletben, és vizsgálja meg az eredményt a kimeneti naplóból. Ezek az eredmények a 11. ábrán láthatók.

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

*11. ábra. Az dataframe összefoglalása két oszloppal eltávolítva.*

Jó hír! Megkapjuk a várt eredményeket.

### <a name="add-a-new-column"></a>Új oszlop hozzáadása

Az idősorozat-modellek létrehozásához kényelmes, hogy egy olyan oszlop legyen, amely az idősorozat kezdete óta eltelt időt tartalmazza. Létre fogunk hozni egy új "month. Count" oszlopot.

A kód rendszerezése érdekében hozzuk létre az első egyszerű függvényt, `num.month()`. Ezután ezt a függvényt fogjuk alkalmazni egy új oszlop létrehozásához a dataframe. Az új kód a következő.

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

Most futtassa a frissített kísérletet, és a kimeneti napló használatával tekintse meg az eredményeket. Ezek az eredmények a 12. ábrán láthatók.

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

*12. ábra. A dataframe összegzése a további oszloppal.*

Úgy tűnik, minden működik. Az új oszlop a várt értékekkel rendelkezik a dataframe.

### <a name="value-transformations"></a>Értékek átalakítása

Ebben a szakaszban néhány egyszerű átalakítást végzünk a dataframe egyes oszlopaiban lévő értékekre. Az R nyelv szinte tetszőleges értékű átalakításokat támogat. Az [alább olvasható hivatkozások részletes](#appendixb) példákat tartalmaznak.

Ha megtekinti a dataframe összefoglalóinak értékeit, itt valami furcsaat kell látnia. Több fagylaltot, mint a Kaliforniában előállított tej? Nem, természetesen nem, mivel ez nincs értelme, szomorú, hogy ez a tény lehet az amerikai jégkrém szerelmeseinek. Az egységek eltérőek. Az ár az USA-beli font egységében van, a tej 1 millió USA-beli egységben van, az Ice Cream pedig 1 000 US gallon egységben van, a túró pedig 1 000 USD. Feltételezve, hogy az Ice Cream körülbelül 6,5 kilót mérlegel, a szorzást egyszerűen elvégezheti az értékek konvertálásával, így azok mind a 1 000 font egységben egyenlő egységben vannak.

Az előrejelzési modellhez multiplikatív modellt használunk az adattrendek és a szezonális beállítások megváltoztatásához. A log-átalakítás lehetővé teszi, hogy lineáris modellt használjon, ezzel egyszerűbbé téve ezt a folyamatot. A log-transzformációt ugyanabban a függvényben lehet alkalmazni, ahol a szorzót alkalmazták.

A következő kódban Definiálok egy új függvényt, `log.transform()`, majd azt a numerikus értékeket tartalmazó sorokra alkalmazza. Az R `Map()` függvény a `log.transform()` függvény alkalmazására szolgál a dataframe kijelölt oszlopaira. `Map()` hasonló a `apply()`hoz, de a függvényhez több argumentumot is engedélyez. Vegye figyelembe, hogy a multiplikátorok listája az `log.transform()` függvény második argumentumát adja meg. A `na.omit()` függvényt a rendszer egy kis karbantartási művelettel biztosítja, hogy a dataframe ne legyenek hiányzó vagy nem definiált értékek.

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

A `log.transform()` függvényben meglehetősen sok történik. A kód nagy része az argumentumokkal kapcsolatos lehetséges problémákat ellenőrzi, vagy a kivételekkel foglalkozik, amelyek a számítások során továbbra is felmerülhetnek. A kód csak néhány sora valójában a számításokat.

A védekező programozás célja, hogy megakadályozza egy olyan függvény meghibásodását, amely megakadályozza a feldolgozás folytatását. A hosszú futású elemzések hirtelen meghibásodása meglehetősen idegesítő lehet a felhasználók számára. A probléma elkerülése érdekében az alapértelmezett visszatérési értékeket úgy kell megválasztani, hogy az a sérülést az alsóbb rétegbeli feldolgozásra korlátozza. Egy üzenet figyelmezteti a felhasználókat, hogy valamilyen hiba történt.

Ha nem használja az R-ben az R-programozást, akkor ez a kód egy kicsit nyomasztónak tűnhet. Végigvezeti a főbb lépéseken:

1. Négy üzenet vektora van definiálva. Ezek az üzenetek a kóddal megjelenő lehetséges hibákkal és kivételekkel kapcsolatos információk közlésére szolgálnak.
2. Minden esetben a NA értéket visszaállítom. Számos más lehetőség is van, amelyek kevesebb mellékhatással rendelkezhetnek. Nullák vektort vagy az eredeti bemeneti vektort lehet visszaadni, például:.
3. Az ellenőrzések a függvény argumentumai szerint futnak. Ha a rendszer hibát észlel, a rendszer minden esetben visszaadja az alapértelmezett értéket, és a `warning()` függvény egy üzenetet hoz létre. `stop()` helyett `warning()` használok, mivel az utóbbi leállítja a végrehajtást, pontosan azt, amit megpróbálok elkerülni. Vegye figyelembe, hogy ezt a kódot egy eljárási stílusban írtam, ahogy ebben az esetben a funkcionális megközelítés bonyolultnak és homályosnak tűnt.
4. A rendszer becsomagolja a naplózási számításokat `tryCatch()`, így a kivételek nem okozzák hirtelen a feldolgozást. Az R functions által generált legtöbb hiba `tryCatch()` nélkül egy leállítási jelet eredményez, amely éppen ezt teszi.

Hajtsa végre ezt az R-kódot a kísérletben, és tekintse meg a kimeneti. log fájl nyomtatott kimenetét. Ekkor láthatja a naplóban szereplő négy oszlop átalakított értékeit, ahogy az a 13. ábrán látható.

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

*13. ábra. Az átalakított értékek összefoglalása a dataframe.*

Láthatjuk, hogy az értékek át lettek alakítva. A tejtermelés mostantól nagy mértékben meghaladja az összes többi tejtermék-termelést, és emlékeztet arra, hogy most már a naplózási léptéket vizsgáljuk.

Ezen a ponton a rendszer megtisztítja az adatainkat, és készen áll a modellezésre. Az [Execute R script][execute-r-script] modul eredmény-adatkészlet kimenetének vizualizációs összegzését megtekintve a "hónap" oszlop "kategorikus", 12 egyedi értékekkel, és csak a kívánt módon jelenik meg.

## <a id="timeseries"></a>Idősorozat-objektumok és korrelációs elemzés

Ebben a szakaszban néhány alapszintű R idősorozat-objektumot fogunk feltárni, és elemezni fogjuk az egyes változók közötti összefüggéseket. Célunk, hogy kiadja a páros korrelációs adatait tartalmazó dataframe több késéssel.

Ennek a szakasznak a teljes R-kódja a [MachineLearningSamples-notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Idősorozat-objektumok az R-ben

Ahogy már említettük, az idősorozat az adatértékek egy sorozata, az idő szerint indexelve. Az R idősorozat-objektumok az időindex létrehozásához és kezeléséhez használatosak. Az idősorozat-objektumok használatának számos előnye van. Az idősorozat-objektumok ingyenesen használhatók az objektumban beágyazott idősorozat-indexek kezelésének számos adatával. Emellett az idősorozat-objektumok lehetővé teszik a több idősorozat-módszer használatát a nyomtatáshoz, nyomtatáshoz, modellezéshez stb.

A POSIXct Time Series osztály általában használatban van, és viszonylag egyszerű. Ez az idősorozat-osztály az év elejétől, a 1970. január 1-jén méri az időt. Ebben a példában a POSIXct idősorozat-objektumokat fogjuk használni. Más, széles körben használt R idősorozat-objektumosztály például az Állatkert és a XTS, az Extensible Time Series.

### <a name="time-series-object-example"></a>Példa idősorozat-objektumra

Ismerkedjen meg a példával. Húzzon egy **új** [végrehajtási R szkript][execute-r-script] modult a kísérletbe. Kapcsolódjon a meglévő [r-szkriptek][execute-r-script] eredményének DataSet1 elemet kimeneti portjához az új [végrehajtási r szkript][execute-r-script] modul DataSet1 elemet bemeneti portjához.

Ahogy az első példákban is tettem, ahogy haladunk a példán, bizonyos pontokon csak az R-kód növekményes további sorai jelennek meg az egyes lépésekben.  

#### <a name="reading-the-dataframe"></a>A dataframe olvasása

Első lépésként olvassa el a dataframe, és győződjön meg arról, hogy megkapjuk a várt eredményeket. A következő kódnak kell elvégeznie a feladatot.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Most futtassa a kísérletet. Az új végrehajtási R parancsfájl-alakzat naplójának a 14. ábrához hasonlóan kell kinéznie.

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

*14. ábra. A dataframe összegzése az R-parancsfájl végrehajtása modulban.*

Ezek az adattípusok a várt típusú és formátumúak. Vegye figyelembe, hogy a "Month" oszlop típusú tényező, és a várt számú szintet tartalmaz.

#### <a name="creating-a-time-series-object"></a>Idősorozat-objektum létrehozása

Hozzá kell adnia egy idősorozat-objektumot a dataframe. Cserélje le az aktuális kódot a következőre, amely hozzáadja a POSIXct osztály új oszlopát.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Most keresse meg a naplót. Úgy kell kinéznie, mint a 15. ábrán.

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

*15. ábra. Egy idősorozat-objektummal rendelkező dataframe összefoglalása.*

Az összegzésből láthatjuk, hogy az új oszlop valójában a POSIXct osztály.

### <a name="exploring-and-transforming-the-data"></a>Az adatelemzés és-átalakítás

Vizsgáljuk meg az adatkészlet egyes változóit. A scatterplot mátrix jó módszer a gyors megjelenítéshez. A következő sorral cserélem a `str()` függvényt az előző R-kódban.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Futtassa ezt a kódot, és nézze meg, mi történik. Az R-eszköz portján létrehozott ábrának a 16. ábrához hasonlóan kell kinéznie.

![A kiválasztott változók scatterplot mátrixa](./media/r-quickstart/fig17.png)

*16. ábra. A kiválasztott változók scatterplot mátrixa.*

A változók közötti kapcsolatokban valamilyen furcsa megjelenésű struktúra szerepel. Lehet, hogy ez az adatok trendjeiből és abból ered, hogy nem szabványosítta a változókat.

### <a name="correlation-analysis"></a>Korrelációs elemzés

A korrelációs elemzés végrehajtásához a változókat is el kell végezni. Egyszerűen használhatja az R `scale()` függvényt, amely a központok és a skála változókat is használja. Előfordulhat, hogy ez a függvény jóval gyorsabban fut. Szeretném azonban megmutatni, hogyan lehet védekező programot használni az R-ben.

Az alább látható `ts.detrend()` függvény mindkét műveletet végrehajtja. A következő két sornyi kód lenyomja az adatokat, majd egységesíti az értékeket.

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

A `ts.detrend()` függvényben meglehetősen sok történik. A kód nagy része az argumentumokkal kapcsolatos lehetséges problémákat ellenőrzi, vagy a kivételekkel foglalkozik, amelyek a számítások során továbbra is felmerülhetnek. A kód csak néhány sora valójában a számításokat.

Már beszéltünk egy példát a védekező programozásra az érték-átalakításokban. Mindkét számítási blokk `tryCatch()`be van csomagolva. Bizonyos hibák esetén érdemes visszaadni az eredeti bemeneti vektort, más esetekben pedig nulla típusú vektort ad vissza.  

Vegye figyelembe, hogy a megszüntetéshez használt lineáris regresszió egy idősorozat-regresszió. A prediktív változó egy idősorozat-objektum.  

Ha `ts.detrend()` van meghatározva, azt a dataframe érdeklődési változóit alkalmazzuk. A `lapply()` által létrehozott listát a `as.data.frame()`használatával kell kikényszeríteni a dataframe adataival. A `ts.detrend()`védelmi szempontjai miatt a változók egyikének feldolgozása nem fogja megakadályozni a többi felhasználó helyes feldolgozását.  

A kód utolsó sora létrehoz egy páros-scatterplot. Az R-kód futtatása után a scatterplot eredményei a 17. ábrán láthatók.

![A páros scatterplot és a szabványosított idősorozatok](./media/r-quickstart/fig18.png)

*17. ábra. A páros a scatterplot és a szabványosított idősorozatot is felszámítja.*

Ezeket az eredményeket a 16. ábrán látható módon hasonlíthatja össze. Ha a trend el lett távolítva, és a változók szabványosítva vannak, sokkal kevesebb struktúrát látunk a változók közötti kapcsolatokban.

A korrelációk R CCF-objektumként való számításához szükséges kód a következő.

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

A kód futtatása a 18. ábrán látható naplót eredményezi.

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

*18. ábra. A páros korrelációs analízisből származó CCF-objektumok listája.*

Az egyes késések korrelációs értéke. A korrelációs értékek egyike sem elég nagy ahhoz, hogy jelentős legyen. Ezért azt is megállapítjuk, hogy az egyes változókat egymástól függetlenül modellezjük.

### <a name="output-a-dataframe"></a>Dataframe kimenete
A páros korrelációkat az R CCF-objektumok listájának megfelelően számítottuk ki. Ez egy kis problémát jelent, mivel az eredményül kapott adatkészlet kimeneti portja valóban dataframe igényel. A CCF objektum maga a lista, és csak a lista első elemében található értékeket szeretnénk használni, a különböző késések összefüggéseit.

A következő kód kibontja a késési értékeket a CCF-objektumok listájából, amelyek magukban foglalják a listákat.

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

A kód első sora egy kicsit trükkös, és néhány magyarázat segíthet megérteni. A belső használatból a következő műveleteket végezheti el:

1. Az "**1**" argumentummal rendelkező " **[[** " operátor kiválasztja a nem a CCF-objektumok listájának első elemében lévő késések vektorát.
2. A `do.call()` függvény alkalmazza a `rbind()` függvényt a lista elemein `lapply()`alapján.
3. A `data.frame()` függvény kényszeríti a `do.call()` által előállított eredményt egy dataframe.

Vegye figyelembe, hogy a sorok nevei a dataframe egyik oszlopában vannak. Ezzel megőrzi a sorok nevét, amikor a [végrehajtás R-szkriptből][execute-r-script]származnak.

A kód futtatásakor a rendszer a 19. ábrán látható kimenetet **jeleníti** meg, amikor a kimenetet megjeleníti az eredmény adatkészlet portján. A sorok neve az első oszlopban szerepel, a kívánt módon.

![A korrelációs elemzés eredményeinek kimenete](./media/r-quickstart/fig20.png)

*19. ábra. A korrelációs elemzés eredményeinek kimenete.*

## <a id="seasonalforecasting"></a>Idősorozat-példa: szezonális előrejelzés

Az adataink jelenleg elemzésre alkalmas formában vannak, és azt állapították meg, hogy a változók között nincsenek jelentős korrelációk. Térjünk át, és hozzon létre egy idősorozat-előrejelzési modellt. Ennek a modellnek a használatával a kaliforniai tejtermékek előállítását fogjuk megbecsülni a 12 hónapig 2013.

Az előrejelzési modell két összetevővel, egy trend-összetevővel és egy szezonális összetevővel fog rendelkezni. A teljes előrejelzés a két összetevő terméke. Az ilyen típusú modellt multiplikatív-modellnek nevezzük. Az alternatíva egy adalékanyag-modell. A rendszer már alkalmazta a naplózási átalakítást az érdeklődésre számot tartó változóra, ami elvégezheti ezt az elemzést.

Ennek a szakasznak a teljes R-kódja a [MachineLearningSamples-notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>A dataframe létrehozása az elemzéshez

Első lépésként vegyen fel egy **új** [végrehajtási R script][execute-r-script] modult a kísérletbe. Kapcsolja össze a meglévő [R-szkriptek][execute-r-script] **eredményét** az új modul **DataSet1 elemet** bemenetével. Az eredménynek a 20. ábrához hasonlóan kell kinéznie.

![A kísérlet az új végrehajtási R-szkripttel modullal bővült](./media/r-quickstart/fig21.png)

*20. ábra. A kísérlet az új végrehajtási R script modullal lett hozzáadva.*

Csakúgy, mint a korrelációs elemzések esetében, hozzá kell adnia egy oszlopot egy POSIXct idősorozat-objektummal. A következő kód csak ezt fogja elvégezni.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Futtassa ezt a kódot, és tekintse meg a naplót. Az eredménynek a 21. ábrához hasonlóan kell kinéznie.

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

*21. ábra. A dataframe összegzése.*

Ezzel az eredménnyel készen állunk az elemzés megkezdésére.

### <a name="create-a-training-dataset"></a>Képzési adatkészlet létrehozása

A kiépített dataframe létre kell hozni egy betanítási adatkészletet. Ez az adat tartalmazza az összes észrevételt, kivéve a 2013-as év utolsó 12 számát, amely a tesztelési adatkészlet. A következő kód kijelöli a dataframe, és létrehozza a tejtermékek termelési és árképzési változóit. Ezután létrehozunk a négy termelési és árképzési változóból álló ábrákat. A Névtelen függvények segítségével megadhatók a mintaterületek kibővítései, majd megismételhető a másik két argumentum listáján `Map()`. Ha úgy gondolja, hogy a for loop a következőhöz lett kidolgozva, akkor helyes. Mivel azonban az R funkcionális nyelv, egy funkcionális megközelítést mutatunk be.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

A kód futtatása a Time Series-sorozatot ábrázolja a 22. ábrán látható R-eszköz kimenetéről. Vegye figyelembe, hogy az időtengely a dátumok egységében van, és a Time Series ábrázolási módszerének egy kellemes előnye.

![A kaliforniai tejtermékek termelésével és árával kapcsolatos első idősorozat-mintaterületek](./media/r-quickstart/unnamed-chunk-161.png)

![Az idősorozatok második ábrái a kaliforniai tejtermék-termelés és az árak mennyiségéről](./media/r-quickstart/unnamed-chunk-162.png)

![Az idősorozat harmadik része a kaliforniai tejtermék-termelés és az árak](./media/r-quickstart/unnamed-chunk-163.png)

![Az idősorozat negyedik időpontja a kaliforniai tejtermék-termelés és az árak](./media/r-quickstart/unnamed-chunk-164.png)

*22. ábra. Az idősorozat a kaliforniai tejtermékek termelési és díjszabási szolgáltatásait ábrázolja.*

### <a name="a-trend-model"></a>Egy trend Model

Egy idősorozat-objektumot hozott létre, és megtekintette az adatokat, kezdjük egy trend-modellt létrehozni a kaliforniai tejtermék-termelési adatokat illetően. Ezt egy idősorozat-regresszióval teheti meg. Azonban a mintaterületből nem egyértelmű, hogy a betanítási adatokban a megfigyelt trendet pontosan modellezni fogjuk.

Az adat kis mérete miatt építem a RStudio trend modelljét, majd kivágja és beilleszti az eredményül kapott modellt Azure Machine Learning Studioba (klasszikus). A RStudio interaktív környezetet biztosít az ilyen típusú interaktív elemzésekhez.

Első kísérletként egy polinom-regressziót próbálok meg 3-ig terjedő hatáskörökkel. Az ilyen típusú modellek valós veszélyt jelentenek. Ezért érdemes elkerülni a nagy rendelési feltételeket. A `I()` függvény gátolja a tartalom értelmezését (a "as is" tartalmát értelmezi), és lehetővé teszi, hogy egy regressziós egyenletben egy szó szerint értelmezett függvényt írjon.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Ez a következőt hozza létre.

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

Ebben a kimenetben a P értékek (`Pr(>|t|)`) alapján láthatjuk, hogy a négyzetes kifejezés nem lehet jelentős. Az `update()` függvény használatával módosítom ezt a modellt a négyzetes kifejezés eldobásával.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Ez a következőt hozza létre.

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

Ez jobban néz ki. Az összes feltétel jelentős. A 2e-16 érték azonban alapértelmezett érték, és nem lehet túl komolyan venni.  

A "józan ész" teszt során tegyük fel, hogy a kaliforniai tejtermék-termelési adatokat tartalmazó idősorozat ábrázolja a látható trend görbét. Felvettem a következő kódot a Azure Machine Learning Studio (klasszikus) [R-szkriptek végrehajtása][execute-r-script] (nem RStudio) használatával a modell létrehozásához és a mintaterület készítéséhez. Az eredmény a 23. ábrán látható.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Kaliforniai tejtermékek termelési adatokat a trend Model látható](./media/r-quickstart/unnamed-chunk-18.png)

*23. ábra. Kalifornia tejtermék-termelési adatokat a trend Model látható.*

Úgy tűnik, hogy a trend Model meglehetősen jól illeszkedik az adatokat. Továbbá úgy tűnik, hogy a modell görbén túl sok a terhelés, például a páratlan kígyózik.  

### <a name="seasonal-model"></a>Szezonális modell

A trend Model esetében be kell jelentkeznie, és tartalmaznia kell az idényjellegű hatásokat. Az év hónapját a lineáris modellben lévő dummy változóként fogjuk használni, hogy rögzítse a hónapról hónapra érvényes hatást. Vegye figyelembe, hogy amikor egy modellbe bevezeti a faktor változóit, az elfogás nem számítható ki. Ha ezt nem teszi meg, a képlet túl van megadva, és az R levonja az egyik kívánt tényezőt, de megtartja az elfogási kifejezést.

Mivel megfelelő trend-modellel rendelkezünk, az `update()` függvény használatával adhatjuk hozzá az új feltételeket a meglévő modellhez. A frissítési képletben az-1 az elfogási kifejezést eldobja. Folytatás a RStudio a pillanatra:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Ez a következőt hozza létre.

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

Láthatjuk, hogy a modell már nem rendelkezik elfogási kifejezéssel, és 12 jelentős hónapra vonatkozó tényezővel rendelkezik. Pontosan ezt láttuk.

Tegyük fel, hogy a kaliforniai tejtermékek termelési információinak egy másik idősorozata látható, hogy milyen jól működik a szezonális modell. Felvettem a következő kódot a Azure Machine Learning Studio (klasszikus) [R-szkript végrehajtásával][execute-r-script] a modell létrehozásához és egy mintaterület készítéséhez.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

A kód Azure Machine Learning Studioban (klasszikus) való futtatása a 24. ábrán látható ábrát állítja elő.

![Kaliforniai tejtermékek gyártása a modellel, beleértve az idényjellegű hatásokat](./media/r-quickstart/unnamed-chunk-20.png)

*24. ábra. Kalifornia tejtermékek gyártása modellekkel, beleértve az idényjellegű hatásokat.*

A 24. ábrán látható adathoz való igazodás inkább biztató. A trend és a szezonális effektus (havi változat) is ésszerűnek tűnik.

A modell egy másik beadásával nézzük meg a maradványokat. A következő kód kiszámítja a két modell előre jelzett értékeit, kiszámítja az idényjellegű modell maradékait, majd kirajzolja ezeket a fennmaradó értékeket a betanítási adatokhoz.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

A fennmaradó mintaterület a 25. ábrán látható.

![A betanítási adatmennyiségre vonatkozó szezonális modell maradványai](./media/r-quickstart/unnamed-chunk-21.png)

*25. ábra. A betanítási adattípushoz tartozó szezonális modell maradványai.*

Ezek a maradványok ésszerűnek tűnnek. Az 2008-2009-es recesszió hatásának hiányában nincs különleges struktúra, amely a modellünk esetében nem számít különösen jól.

A 25. ábrán látható ábra hasznos lehet a maradékokban lévő időfüggő minták észlelésére. A számítási és a megmaradt maradványok kirajzolásának explicit megközelítése, amelyet a maradékok időbeli sorrendbe helyeznek a parcellán. Ha viszont a `milk.lm$residuals`ábrázolta volna, a mintaterület nem lett volna időbeli sorrend.

A `plot.lm()` használatával diagnosztikai mintaterületek sorozatát is létrehozhatja.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Ez a kód a 26. ábrán látható diagnosztikai mintaterületek sorozatát eredményezi.

![A szezonális modellhez tartozó diagnosztikai mintaterületek első része](./media/r-quickstart/unnamed-chunk-221.png)

![A szezonális modellhez tartozó diagnosztikai mintaterületek másodpercenkénti száma](./media/r-quickstart/unnamed-chunk-222.png)

![A szezonális modellhez tartozó diagnosztikai mintaterületek harmadik része](./media/r-quickstart/unnamed-chunk-223.png)

![A szezonális modellhez tartozó diagnosztikai mintaterületek negyedik része](./media/r-quickstart/unnamed-chunk-224.png)

*26. ábra. Diagnosztikai telkek az idényjellegű modellhez.*

Ezekben a mintaterületeken néhány nagy befolyású pont van azonosítva, de semmi sem okoz gondot. Emellett láthatjuk a normál Q-Q ábrán, hogy a maradványok általában kiterjesztve, a lineáris modellek esetében pedig fontos feltételezések.

### <a name="forecasting-and-model-evaluation"></a>Előrejelzés és modell kiértékelése

A példához csak még egy dolgot kell végrehajtani. Az előrejelzéseket ki kell számítani, és a tényleges adatok alapján kell mérni a hibát. Az előrejelzések a 2013-es 12 hónapig érvényesek. Ezt az előrejelzést a betanítási adatkészletbe nem tartozó tényleges adatokra is kiszámíthatja. Emellett a 18 éves betanítási adatmennyiséget is összehasonlíthatja a 12 hónapig tartó tesztelési adattal.  

Az idősorozat-modellek teljesítményének méréséhez számos metrika használatos. Ebben az esetben a root Mean Square (RMS) hibát fogjuk használni. A következő függvény két adatsorozat között kiszámítja az RMS-hibát.  

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

Ahogy az "érték-átalakítások" szakaszban ismertetett `log.transform()` függvényhez hasonlóan, a függvény számos hiba-ellenőrzési és kivétel-helyreállítási kódot tartalmaz. Az alkalmazott elvek ugyanazok. A munka két helyen történik, `tryCatch()`ba csomagolva. Először is exponentiated az idősorozat, mert az értékek naplóival dolgozunk. Másodszor, a tényleges RMS-hiba számítása.  

Az RMS-hiba mérésére szolgáló függvénnyel kell létrehozni és kiadni az RMS-hibákat tartalmazó dataframe. A trend modelhez és a teljes modellhez szezonális tényezőket tartalmazó feltételek is beletartoznak. A következő kód végzi el a feladatot a létrehozott két lineáris modell használatával.

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

A kód futtatásával az eredmény-adatkészlet kimeneti portjának 27. ábráján látható kimenet jön létre.

![Az RMS-hibák összehasonlítása a modellekhez](./media/r-quickstart/fig26.png)

*27. ábra. Az RMS-hibák összehasonlítása a modellekhez.*

Ezekből az eredményekből láthatjuk, hogy a modellhez tartozó szezonális tényezők hozzáadása jelentősen csökkenti az RMS-hibát. Nem meglepő, hogy a betanítási adatmennyiséghez tartozó RMS-hiba egy kicsit kisebb az előrejelzésnél.

## <a id="appendixa"></a>Útmutató a RStudio dokumentációhoz

A RStudio elég jól dokumentálva. Íme néhány hivatkozás a RStudio dokumentációjának főbb részeire, hogy megismerkedjen az első lépésekkel.

* **Projektek létrehozása** – a RStudio használatával rendszerezheti és kezelheti az R-kódját projektjeiben. További részletekért lásd: [projektek használata](https://support.rstudio.com/hc/articles/200526207-Using-Projects) . Azt javasoljuk, hogy kövesse ezeket az utasításokat, és hozzon létre egy projektet az R Code-példákhoz ebben a cikkben.  
* Az **r Code-RStudio szerkesztése és végrehajtása** integrált környezetet biztosít az r-kód szerkesztéséhez és végrehajtásához. Részletekért lásd: [kód szerkesztése és végrehajtása](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) .
* **Hibakeresés** – a RStudio hatékony hibakeresési képességeket tartalmaz. A funkciókkal kapcsolatos további információkért tekintse meg a [RStudio hibakeresését](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) ismertető témakört. A Töréspont hibaelhárítási funkcióiról a [töréspont hibaelhárítása](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)című témakörben talál további információt.

## <a id="appendixb"></a>További olvasnivalók

Ez az R-programozási oktatóanyag ismerteti az alapismereteket, amelyekkel a Azure Machine Learning Studio (klasszikus) R nyelvét kell használnia. Ha nem ismeri az R-t, két bevezetést kell elérhetővé tennie a CRANban:

* A [kezdőknek](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) az Emmanuel Paradis jó kiindulópontja az R.  
* [Az R bevezetője](https://cran.r-project.org/doc/manuals/R-intro.html) a W. N használatával. Venables et. al. egy kicsit mélyebbre kerül.

Az R-ben számos könyv található, amely segítséget nyújt az első lépésekhez. Itt talál néhány hasznosat:

* Az **r-programozás művészete: a Norman Matloff által a statisztikai szoftverek tervezésének bemutatója** kiváló bevezetést mutat az r-ben való programozáshoz.  
* A Paul Teetor **r szakácskönyve** problémát és megoldást kínál az r használatára.  
* Az **R in Action** by Robert Kabacoff egy másik hasznos bevezető könyv. A Companion [gyors R-webhely](https://www.statmethods.net/) hasznos erőforrás.
* Az **r Inferno** Patrick Burns egy meglepően vicces könyv, amely számos olyan trükkös és bonyolult témakört tartalmaz, amelyek az R-ben való programozás során fordulhatnak elő. A könyv ingyenesen elérhető az [R infernonél](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Ha szeretné, hogy az R-vel kapcsolatos speciális témakörök részletesen megtekintsenek, tekintse meg a **speciális r** -t a Hadley Wickham. A könyv online verziója ingyenesen elérhető a következő címen: [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/).

Az R idősorozat-csomagok katalógusa a Cran- [feladat nézetében található: idősorozat-elemzés](https://cran.r-project.org/web/views/TimeSeries.html). Az adott idősorozat-objektum csomagjaival kapcsolatos információkért tekintse meg az adott csomag dokumentációját.

A könyv **bevezető idősorozata** , az r Paul Cowpertwait és Andrew Metcalfe, bevezetést nyújt az r használatának idősorozat-elemzéséhez. Számos további elméleti szöveg R-példákat tartalmaz.

Íme néhány nagyszerű internetes erőforrás:

* A DataCamp megtanítja az R-t a böngésző kényelmében a videók és a kódolási gyakorlatok révén. A legújabb R-technikák és csomagok interaktív oktatóanyagokkal rendelkeznek. Használja ki az ingyenes [interaktív R-oktatóanyagot](https://www.datacamp.com/courses/introduction-to-r).
* [Ismerkedjen meg az R programozással, a végleges útmutatóval](https://www.programiz.com/r-programming) a Programiz.
* Egy gyors [R-oktatóanyag](https://www.cyclismo.org/tutorial/R/) : Kelly Black, Clark University.
* Több mint 60 R erőforrás szerepel a [legnépszerűbb r nyelvi erőforrásokban az adatkezelési képességek javítása érdekében](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
