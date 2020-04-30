---
title: 'R-szkript végrehajtása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az r-parancsfájl végrehajtása Azure Machine Learning az R-kód futtatásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: eb778c8d24639320b60927438de76a29de724ac2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684715"
---
# <a name="execute-r-script"></a>R-szkript végrehajtása

Ez a cikk azt ismerteti, hogyan használható az r- **parancsfájl végrehajtása** az r-kód futtatásához a Azure Machine learning Designer (előzetes verzió) folyamatában.

Az R használatával olyan feladatokat hajthat végre, amelyeket jelenleg nem támogat a meglévő modulok, például a következők: 
- Egyéni adatátalakítások létrehozása
- Saját mérőszámok használata az előrejelzések kiértékeléséhez
- Modellek létrehozása a tervezőben nem önálló modulként megvalósított algoritmusok használatával

## <a name="r-version-support"></a>R-verziók támogatása

A Azure Machine Learning Designer a CRAN (átfogó R Archive Network) R-eloszlását használja. A jelenleg használt verzió a CRAN 3.5.1-es verziója.

## <a name="supported-r-packages"></a>Támogatott R-csomagok

Az R-környezet előre telepítve van több mint 100 csomaggal. A teljes listát az [előre telepített R-csomagok](#pre-installed-r-packages)című szakaszban találja.

A következő kódot is hozzáadhatja bármelyik **Execute R script** modulhoz, és megtekintheti a telepített csomagokat.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R-csomagok telepítése
További R-csomagok telepítéséhez használja a `install.packages()` metódust. A csomagok minden **végrehajtási r script** modulhoz telepítve vannak, és nem oszthatók meg más **végrehajtási r-parancsfájl** -modulok között.

> [!NOTE]
> Adja meg a CRAN-tárházat olyan csomagok telepítésekor, mint például a`install.packages("zoo",repos = "http://cran.us.r-project.org")`

Ez a példa bemutatja az állatkert telepítését:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
  > Győződjön meg arról, hogy a csomag már létezik a telepítés előtt, hogy elkerülje az ismételt telepítést. Például `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` a fenti mintakód esetében. Előfordulhat, hogy a telepítés megismétlése webszolgáltatási kérés időtúllépését okozhatja.     

## <a name="upload-files"></a>Fájlok feltöltése
Az **r-szkript végrehajtása** támogatja a fájlok feltöltését Azure Machine learning R SDK használatával.

Az alábbi példa bemutatja, hogyan tölthet fel egy képfájlt az **R-szkript végrehajtása**során:
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

A folyamat futásának befejezése után a rendszerképet a modul jobb oldali paneljén tekintheti meg

> [!div class="mx-imgBorder"]
> ![Feltöltött – rendszerkép](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Az R-szkript végrehajtásának konfigurálása

Az **R-szkript végrehajtása** modul olyan mintakód-kódot tartalmaz, amelyet kiindulási pontként használhat. Az R- **parancsfájl végrehajtása** modul konfigurálásához adja meg a végrehajtandó bemeneteket és kódokat.

![R-modul](media/module/execute-r-script.png)

A tervezőben tárolt adatkészletek automatikusan egy R-adatkeretre lesznek konvertálva, ha ezzel a modullal vannak feltöltve.

1.  Adja hozzá az **R-szkript végrehajtása** modult a folyamathoz.

  

1. Csatlakoztasson a parancsfájlhoz szükséges összes bemenetet. A bemenetek nem kötelezőek, és tartalmazhatnak adatokat és további R-kódokat is.

    * **DataSet1 elemet**: az első bemenetre hivatkozik `dataframe1`. A bemeneti adatkészletet CSV-ként, TSV-ként, ARFF-ként, vagy egy Azure Machine Learning adatkészlet összekapcsolásával kell formázni.

    * **Dataset2**: a második bemenetre hivatkozik `dataframe2`. Ezt az adatkészletet CSV-ként, TSV-ként, ARFF-fájlként vagy Azure Machine Learning-adatkészletként kell formázni.

    * **Parancsfájl-csomag**: a harmadik bemenet a ZIP-fájlokat is elfogadja. A tömörített fájl több fájlt és több fájltípust is tartalmazhat.

1. Az **r-szkript** szövegmezőbe írja be vagy illessze be az érvényes R-szkriptet.

    > [!NOTE]
    > Kérjük, legyen nagyon körültekintő a parancsfájl írásakor, és ellenőrizze, hogy nincs-e szintaktikai hiba, például egy nem deklarált változó vagy egy nem importált modul vagy függvény használata. A dokumentum végén külön figyelmet kell fordítani az előre telepített csomagok listájára is. A nem felsorolt csomagok használatához telepítse azokat a parancsfájlba, például:`install.packages("zoo",repos = "http://cran.us.r-project.org")`
    
    > [!NOTE]
    > A függvények az X11 könyvtártól függenek, például a "View" nem támogatott, mert az X11-tár nincs előre telepítve.
    
    Az első lépésekhez az R- **szkript** szövegmező előre ki van töltve a mintakód segítségével, amelyet szerkeszthet vagy lecserélheti.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main
    # which is the entry point for this module.

    # Please note that functions dependant on X11 library
    # such as "View" are not supported because X11 library
    # is not pre-installed.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a zip file is connected to the third input port, it is
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * A szkriptnek tartalmaznia kell egy nevű `azureml_main`függvényt, amely a modul belépési pontja.

 * A belépési pont függvénynek két bemeneti argumentummal `Param<dataframe1>` kell `Param<dataframe2>`rendelkeznie: és, még akkor is, ha a függvény nem használja ezt a két argumentumot.

   > [!NOTE]
    > A `dataframe1` `dataframe2` **végrehajtási R-parancsfájlnak** átadott adatok a következőre hivatkoznak: és, amely eltér a Azure Machine learning designertől (a `dataset1`tervezői hivatkozástól `dataset2`). Győződjön meg arról, hogy a bemeneti adatok megfelelően vannak referneced a szkriptben.  
 
    > [!NOTE]
    >  Előfordulhat, hogy a meglévő R-kódnak kisebb módosításokra van szüksége a tervezői folyamatokban való futtatáshoz. Például a CSV formátumban megadott bemeneti adatokat explicit módon át kell alakítani egy adatkészletbe, mielőtt használni lehetne a kódban. Az R nyelvben használt adatok és oszlopok különböző módokon különböznek a tervezőben használt adatok és oszlopok típusaitól.

1.  **Véletlenszerű mag**: adjon meg egy értéket, amelyet az R-környezetben kell használni véletlenszerű mag értékként. Ez a paraméter egyenértékű az R `set.seed(value)` -kódban való meghívással.  

1. A folyamat elküldése.  

## <a name="results"></a>Results (Eredmények)

Az **r-szkriptek végrehajtása** modulok több kimenetet is visszaadhatnak, de r-adatkeretként kell megadni őket. Az adatkereteket a rendszer automatikusan átalakítja a tervezőben lévő adatkészletekbe a más modulokkal való kompatibilitás érdekében.

Az R standard üzeneteit és hibáit a rendszer visszaadja a modul naplójába.

Ha az eredményeket az R-szkriptben kell kinyomtatnia, a kinyomtatott eredményeket a modul jobb oldali paneljének **kimenet + naplók** lapján található **70_driver_log** alatt találja.

## <a name="sample-scripts"></a>Mintaparancsfájlok

A folyamatokat többféleképpen is kiterjesztheti egyéni R-szkriptek használatával.  Ez a szakasz a gyakori feladatokhoz tartalmaz mintakód-kódot.


### <a name="add-r-script-as-an-input"></a>R-szkript hozzáadása bemenetként

Az **r-szkript végrehajtása** modul bemenetként támogatja a tetszőleges r-parancsfájlokat. Ehhez a ZIP-fájl részeként fel kell tölteni őket a munkaterületre.

1. Ha R-kódot tartalmazó ZIP-fájlt szeretne feltölteni a munkaterületre, nyissa meg az **adatkészletek** eszközcsoport lapot, kattintson az **adatkészlet létrehozása**elemre, majd válassza a **helyi fájl** és a **fájl** adatkészlet típusa lehetőséget.  

1. Ellenőrizze, hogy a zip-fájl elérhető- **e a bal** oldali modul faszerkezetek **kategóriájának adatkészletek** listájában.

1.  Kapcsolja össze az adatkészletet a **parancsfájl-csomag** bemeneti portjával.

1. A ZIP-fájlban található összes fájl elérhető a folyamat futási ideje alatt. 

    Ha a parancsfájl-köteg fájlja tartalmaz egy címtár-struktúrát, a rendszer megőrzi a struktúrát. Azonban módosítania kell a kódot, hogy a könyvtár **./script csomagot** adja meg az elérési útra.

### <a name="process-data"></a>Adatok feldolgozása

Az alábbi példa bemutatja, hogyan méretezhető és normalizálható a bemeneti adatok:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>ZIP-fájl beolvasása bemenetként

Ez a minta bemutatja, hogyan használható a ZIP-fájlban található adatkészlet bemenetként az **R-szkript végrehajtása** modulba.

1. Hozzon létre egy CSV formátumú adatfájlt, és nevezze el "mydatafile. csv" néven.
1. Hozzon létre egy ZIP-fájlt, és adja hozzá a CSV-fájlt az archívumhoz.
1. Töltse fel a tömörített fájlt a Azure Machine Learning munkaterületre. 
1. Az eredményül kapott adatkészlet csatlakoztatása az R- **szkript végrehajtásához** tartozó modul **ScriptBundle** -bemenetéhez.
1. A következő kód használatával olvassa be a CSV-adatait a tömörített fájlból.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Sorok replikálása

Ez a minta bemutatja, hogyan replikálhat pozitív rekordokat egy adatkészletben a minta kiegyensúlyozásához:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R-objektumok átadása az R-parancsfájl-modulok végrehajtása között

Az r-objektumokat a belső szerializálási mechanizmus használatával lehet átadni az **r szkript végrehajtása** modul példányai között. Ez a példa feltételezi, hogy át kívánja helyezni az R `A` -objektumot két **végrehajtási r parancsfájl** -modul között.

1. Adja hozzá az első **végrehajtási R-parancsfájlt** a folyamathoz, és írja be a következő kódot az **r-szkript** szövegmezőbe egy szerializált `A` objektum létrehozásához a modul kimeneti adattáblájában:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Az egész típusra történő explicit konverzió történik, mivel a szerializálási függvény az R `Raw` formátumban jeleníti meg az adatokat, amelyet a tervező nem támogat.

1. Vegyen fel egy második példányt az **R szkript végrehajtása** modulhoz, és kapcsolja az előző modul kimeneti portjához.

1. Írja be a következő kódot az **R-szkript** szövegmezőbe az objektum `A` bemeneti adatok táblából való kinyeréséhez. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Előre telepített R-csomagok

A használható előre telepített R-csomagok aktuális listája:

|              |            | 
|--------------|------------| 
| Csomag      | Verzió    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0 – 1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| indítás         | 1.3 – 22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0 – 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| osztály        | 7.3 – 15     | 
| cli          | 1.1.0      | 
| Cliper        | 0.6.0      | 
| cluster      | 2.0.7 – 1    | 
| codetools    | 0.2 – 16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| adathalmazok     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| értékelés     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0,8 – 71     | 
| FS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generikus     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| Grafikák     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9 – 9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| labeling     | 0.3        | 
| lattice      | 0,20 – 38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3 – 51.4   | 
| Matrix       | 1.2 – 17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8 – 28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1 – 140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8 – 1.1 | 
| openssl      | 1.4        | 
| párhuzamos     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quanteda     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1 – 15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44 – 1.1   | 
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| eszközök        | 3.5.1      | 
| tseries      | 0,10 – 47    | 
| TTR          | 0.23 – 4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0,11 – 2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
