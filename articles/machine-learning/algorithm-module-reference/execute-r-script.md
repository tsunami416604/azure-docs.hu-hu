---
title: 'Hajtsa végre az R-szkript: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, az R-szkript végrehajtása modul használata az Azure Machine Learning szolgáltatás R-kód futtatásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterclu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bfddcd3db4825ea1875474aa16544aa15412bdea
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67518052"
---
# <a name="execute-r-script"></a>R-szkript végrehajtása

Ez a cikk ismerteti, hogyan használható a **R-szkript végrehajtása** modul R-kód futtatása a vizuális felhasználói felületet kísérlet során.

Az r nyelv jelenleg nem támogatottak a meglévő modulok például feladatokat hajthat végre: 
- Hozzon létre egyéni adatátalakítások
- A saját mérőszámok segítségével kiértékelheti az előrejelzések
- Modelleket algoritmusokat használnak a nem vizuális felületen önálló modulok használata

## <a name="r-version-support"></a>R-Verziótámogatás

Az Azure Machine Learning szolgáltatás vizuális felhasználói felületet kínálnak a CRAN (Comprehensive R Archive Network) terjesztési használja A jelenleg használt verziót CRAN 3.5.1.

## <a name="supported-r-packages"></a>Támogatott R-csomagok

Az R-környezetben a több mint 100 csomagok előre telepítve. Teljes listáját lásd a szakasz [alkalmazáshiba R-csomagok](#pre-installed-r-packages).

A következő kódot adja hozzá bármelyik **R-szkript végrehajtása** modul, és tekintse meg a telepített csomagokat.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R-csomagok telepítése
További R csomagokat telepíteni, használja a `install.packages()` metódust. Ne felejtse megadni a CRAN tárból. Az egyes csomagok telepítése **R-szkript végrehajtása** modult, más nincsenek megosztva **R-szkript végrehajtása** modulok.

Ez a példa bemutatja, hogyan állatkert telepítéséhez:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
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

## <a name="how-to-configure-execute-r-script"></a>Hajtsa végre az R-szkriptek konfigurálása

A **R-szkript végrehajtása** modult tartalmaz, amelyek a kiindulási pontként használhatja. Konfigurálhatja a **R-szkript végrehajtása** modult, bemeneti és a programkód végrehajtásához biztosítanak.

![R-module](media/module/execute-r-script.png)

Vizuális felhasználói felületet tárolt adatkészletek automatikusan alakulnak át az R adatkeretbe, ez a modul a betöltésekor.

1.  Adja hozzá a **R-szkript végrehajtása** modult a kísérletvászonra.

    > [!NOTE]
    > Az átadott összes adatot a **R-szkript végrehajtása** modul alakítja át az R `data.frame` formátumban.

1. Csatlakozás a parancsfájl által igényelt bemenetet. Bemenetek megadása nem kötelező, és adatokat és további R-kód.

    * **Dataset1**: Hivatkozik, az első bemeneti `dataframe1`. A bemeneti adatkészlet formátumban kell lenniük egy CSV, TSV, ARFF, vagy csatlakozhat az Azure Machine Learning-adatkészlet.

    * **Dataset2**: Hivatkoznak, a második bemenet `dataframe2`. Ez az adatkészlet is formátumban kell lenniük egy CSV, TSV, ARFF fájlt, vagy az Azure Machine Learning-adatkészlet.

    * **Parancsfájl-csomag**: A harmadik bemeneti fogadja el a ZIP-fájlokat. A tömörített fájlt tartalmazhat egyszerre több fájlt vagy több fájltípus.

1. Az a **R-szkript** szövegbeviteli mezőben írja be vagy illessze be az érvényes R-szkriptet.

    Segítséget nyújt az első lépésekről a **R-szkript** szövegmező mintakódot szerkesztheti, vagy cserélje le az előre összeállított.

    * A parancsfájl tartalmaznia kell egy nevű függvény `azureml_main`, amely Ez a modul belépési pontját.

    * A belépési pont függvényben legfeljebb két bemeneti argumentummal tartalmazhat: `Param<dataframe1>` és `Param<dataframe2>`
 
    > [!NOTE]
    >  Meglévő R-kódokat futtathat egy vizuális felhasználói felületet kísérletet a kisebb módosításokat kell. Például bemeneti adatok CSV formátumban biztosító explicit módon alakítható át egy adatkészlet a kód használata előtt. Az R nyelv használt adat- és típusok is eltérő bizonyos értelemben az adat- és a vizuális felületen használt típusok közül.

1.  **Véletlenszám-generálás kezdőértéke**: Adja meg az R-környezeten belül véletlenszerű seed értéket használni. Ez a paraméter 03T00 hívása `set.seed(value)` az R-kód.  

1. Futtassa a kísérletet.  

## <a name="results"></a>Results (Eredmények)

A **R-szkript végrehajtása** modulok több kimenetek lépjen vissza, de azok R jelölhet meg kell adni. Adatkeretek automatikusan alakulnak vizuális felhasználói felületet adatkészletek más modulokkal való kompatibilitást.

Standard üzenetek és a hibákat az R, a modul napló adott vissza.

## <a name="sample-scripts"></a>Mintaszkriptek

Számos módon egyéni R-parancsfájl használatával kiterjesztheti a kísérlet során.  Ez a témakör mintakód általános feladatokhoz.


### <a name="add-r-script-as-an-input"></a>Adja hozzá az R-szkript bemeneteként

A **R-szkript végrehajtása** modul támogatja az R parancsfájlok tetszőleges bemenetként. Ehhez az szükséges, akkor fel kell tölteni a munkaterülethez, a ZIP-fájl részeként.

1. R-kód a munkaterületre tartalmazó ZIP-fájl feltöltése, kattintson a **új**, kattintson a **adatkészlet**, majd válassza ki **helyi fájlból** és a **Zip-fájl**lehetőséget.  

1. Ellenőrizze, hogy elérhető-e a tömörített fájlt a **mentett adatkészletek** listája.

1.  Az adatkészlet csatlakoztatása a **parancsfájl csomag** bemeneti porthoz.

1. A ZIP-fájlban található összes fájl kísérlet ideje futtatása közben érhetők el. 

    Ha a parancsfájl nyalábfájl könyvtárstruktúra, a rendszer megőrzi a struktúrát. Azonban meg kell változtatnia a kód a könyvtár illesztenie **. / Script csomagot** az elérési úthoz.

### <a name="process-data"></a>Adatok feldolgozása

A következő minta bemutatja, hogyan méretezhet, és a bemeneti adatok normalizálása:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
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

### <a name="read-a-zip-file-as-input"></a>Bemenetként egy ZIP-fájl olvasása

Ez a példa bemutatja, hogyan használja egy adatkészlet egy ZIP-fájlban bemeneteként a **R-szkript végrehajtása** modul.

1. Az adatfájl létrehozása CSV formátumban, és nevezze el "mydatafile.csv".
1. Hozzon létre egy ZIP-fájlt, és adja hozzá a CSV-fájl az archívumot.
1. Töltse fel a tömörített fájlt az Azure Machine Learning-munkaterületet. 
1. Csatlakozás az eredményül kapott adatkészletet a **ScriptBundle** a bemeneti a **R-szkript végrehajtása** modul.
1. A fürt megosztott kötetei szolgáltatás adatokat olvasni a tömörített fájlt az alábbi kód használatával.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Sorok replikálása

Ez a példa bemutatja, hogyan replikálják pozitív kiegyenlítése a minta adatkészlet:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R-szkript végrehajtása moduljai közötti pass R-objektumok

R-objektumok példányai között adhat át a **R-szkript végrehajtása** modul használatával a belső sorba rendezési mechanizmust. Ez a példa feltételezi, hogy szeretné-e helyezni az R-objektum nevű `A` két közötti **R-szkript végrehajtása** modulok.

1. Adja hozzá az első **R-szkript végrehajtása** modul a kísérletben, és írja be az alábbi kódot a **R-szkript** szerializált objektum létrehozása a szövegmező `A` , egy oszlopot a modul kimeneti adattábla:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Az egész típusra való explicit konverzió történik, mivel az R az adatokat a szerializálási függvény `Raw` formátum, amely nem támogatja a vizuális felhasználói felületet.

1. Adjon hozzá egy második példányt a **R-szkript végrehajtása** modult, és csatlakoztassa az előző modul kimeneti portjával.

1. Írja be a következő kódot a **R-szkript** szövegbeviteli mező kinyerése objektum `A` a bemeneti adatok táblából. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Előre telepített R-csomagok

Az aktuális előre telepített csomagok listáját, R is használható:

|              |            | 
|--------------|------------| 
| Csomag      | Version    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| portolások    | 1.1.4      | 
| Alapja         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| indítás         | 1.3-22     | 
| Seprű        | 0.5.2      | 
| callr        | 3.2.0      | 
| jel        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| Osztály        | 7.3-15     | 
| parancssori felület          | 1.1.0      | 
| clipr        | 0.6.0      | 
| Fürt      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| színtérrel   | 1.4-1      | 
| compiler     | 3.5.1      | 
| Zsírkréta       | 1.3.4      | 
| A curl         | 3.3        | 
| Data.TABLE   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| értékelés     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| Foreach      | 1.4.4      | 
| külső      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| typy.     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| Kapcsolás         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafikus     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| Rács         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| régen        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterátorok    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| Címkézés     | 0.3        | 
| Rácsos      | 0.20-38    | 
| Lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| Módszerek      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| Munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| párhuzamos     | 3.5.1      | 
| Pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| Folyamatban van     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| receptek      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| méretezhető       | 1.0.0      | 
| selectr      | 0.4-1      | 
| Térbeli      | 7.3-11     | 
| egyszerű görbék      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| Stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| túlélési     | 2.44-1.1   | 
| sys.          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| eszközök        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| Utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| kanóc      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| az xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| állatkert          | 1.8-6      | 

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 