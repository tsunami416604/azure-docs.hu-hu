---
title: 'R-parancsfájl végrehajtása: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az R-parancsfájl végrehajtása modult az Azure Machine Learning r-kód futtatásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: f038293b48956ac89314e426df3f5dc491954df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064217"
---
# <a name="execute-r-script"></a>R-szkript végrehajtása

Ez a cikk ismerteti, hogyan használhatja az **R-parancsfájl végrehajtása** modul r-kód futtatásához az Azure Machine Learning designer (előzetes verzió) folyamat.

Az R segítségével olyan feladatokat hajthat végre, amelyeket jelenleg nem támogatnak a meglévő modulok, például: 
- Egyéni adatátalakítások létrehozása
- Az előrejelzések kiértékeléséhez használja saját mutatóit
- Modellek et hozhat létre olyan algoritmusok használatával, amelyek nincsenek önálló modulként megvalósítva a tervezőben

## <a name="r-version-support"></a>R verzió támogatása

Az Azure Machine Learning tervezője az R CRAN (Comprehensive R Archive Network) disztribúcióját használja. A jelenleg használt verzió a CRAN 3.5.1.

## <a name="supported-r-packages"></a>Támogatott R-csomagok

Az R környezet előre telepítve van, több mint 100 csomaggal. A teljes listát az Előre telepített R csomagok című [szakaszban található.](#pre-installed-r-packages)

A következő kódot bármely **R-parancsfájl-végrehajtás** modulhoz hozzáadhatja, és megtekintheti a telepített csomagokat.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R csomagok telepítése
További R-csomagok telepítéséhez `install.packages()` használja a módszert. Ügyeljen arra, hogy adja meg a CRAN repository. A csomagok minden **R-parancsfájl-végrehajtás** modulhoz telepítve vannak, és nem vannak megosztva más **R-parancsfájl-műveletek végrehajtása** moduljai között.

Ez a minta bemutatja, hogyan kell telepíteni Zoo:
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
 > [!NOTE]
  > Ellenőrizze, hogy a telepítés előtt létezik-e a csomag, hogy elkerülje az ismételt telepítést. Mint `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` a fenti minta kód. Az ismételt telepítés a webszolgáltatás-kérelmek időeltelését okozhatja.     

## <a name="upload-files"></a>Fájlok feltöltése
Az **R-parancsfájl végrehajtása** támogatja a fájlok feltöltését az Azure Machine Learning R SDK használatával.

A következő példa bemutatja, hogyan tölthet fel képfájlt az **R parancsfájl végrehajtása funkcióban:**
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
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

A folyamat futtatása után megtekintheti a kép előnézetét a modul jobb oldali paneljén

> [!div class="mx-imgBorder"]
> ![Feltöltött kép](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Az R-parancsfájl végrehajtásának konfigurálása

Az **R-parancsfájl végrehajtása** modul mintakódot tartalmaz, amely kiindulási pontként használható. Az **R-parancsfájl végrehajtása** modul konfigurálásához adjon meg egy bemeneti és kódkészletet.

![R-modul](media/module/execute-r-script.png)

A tervezőben tárolt adatkészletek automatikusan R adatkeretté alakulnak, amikor ezzel a modullal vannak betöltve.

1.  Adja hozzá az **R-parancsfájl végrehajtása** modult a folyamathoz.

  

1. Csatlakoztassa a parancsfájl által szükséges bemeneteket. A bemenetek nem kötelezőek, és tartalmazhatnak adatokat és további R-kódot.

    * **Adatkészlet1**: Hivatkozzon az `dataframe1`első bemenetre . A bemeneti adatkészletet CSV, TSV, ARFF formátumban kell formázni, vagy csatlakozhat egy Azure Machine Learning-adatkészlethez.

    * **Adatkészlet2**: A második `dataframe2`bemenetre hivatkozik . Ezt az adatkészletet CSV-, TSV-fájlként vagy Azure Machine Learning-adatkészletként is kell formázni.

    * **Script Bundle**: A harmadik bemenet elfogadja zip fájlokat. A tömörített fájl több fájlt és több fájltípust tartalmazhat.

1. Az **R parancsfájl** szövegmezőjébe írja be vagy illessze be az érvényes R parancsfájlt.

    Az első lépések érdekében az **R-parancsfájl** szövegmezője előre ki van töltve mintakóddal, amelyet szerkeszthet vagy lecserélhet.
    
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

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * A parancsfájlnak tartalmaznia `azureml_main`kell egy függvényt, amely a modul belépési pontja.

 * A belépési pont függvény legfeljebb két `Param<dataframe1>` bemeneti argumentumot tartalmazhat: és`Param<dataframe2>`
 
   > [!NOTE]
    > Az **R-parancsfájl végrehajtása** modulnak átadott `dataframe1` `dataframe2`adatokra a program az Azure Machine Learning `dataset1` `dataset2`tervezőjétől (a tervezői hivatkozás , a ,) eltérő néven hivatkozik. Ellenőrizze, hogy a bemeneti adatok helyesen hivatkoznak-e a parancsfájlba.  
 
    > [!NOTE]
    >  A meglévő R-kód kisebb módosításokat igényelhet a tervezői folyamatban való futtatáshoz. A CSV formátumban megadott bemeneti adatokat például explicit módon adatkészletgé kell konvertálni, mielőtt a kódot használhatná. Az R nyelvben használt adat- és oszloptípusok bizonyos szempontból eltérnek a tervezőben használt adatoktól és oszloptípusoktól.

1.  **Véletlen mag:** Írjon be egy értéket, amelyet az R környezeten belül véletlenszerű magértékként szeretne használni. Ez a paraméter `set.seed(value)` egyenértékű az R-kódban való hívássel.  

1. Küldje el a folyamatot.  

## <a name="results"></a>Results (Eredmények)

Az **R-parancsfájl végrehajtása** modulok több kimenetet is visszaadhatnak, de r-adatkeretként kell megadni őket. Az adatkeretek automatikusan adatkészletekké alakulnak át a tervezőben, hogy kompatibilisek más modulokkal.

Az R szabványos üzenetei és hibái visszakerülnek a modul naplójába.

Ha az R parancsfájlban kell kinyomtatnia az eredményeket, a nyomtatott eredményeket a **70_driver_log** a modul jobb oldali panelén található **Kimenetek+naplók** lapon találja.

## <a name="sample-scripts"></a>Mintaszkriptek

Számos módon kiterjesztheti a folyamatot az egyéni R-parancsfájl használatával.  Ez a szakasz mintakódot tartalmaz a gyakori feladatokhoz.


### <a name="add-r-script-as-an-input"></a>R parancsfájl hozzáadása bemenetként

Az **R-parancsfájl végrehajtása** modul tetszőleges R parancsfájlokat támogat bemenetként. Ehhez a ZIP-fájl részeként fel kell tölteni őket a munkaterületre.

1. Ha R-kódot tartalmazó ZIP-fájlt szeretne feltölteni a munkaterületre, nyissa meg az **Adatkészletek** eszközlapját, kattintson az **Adatkészlet létrehozása gombra,** majd válassza **a Helyi fájlból** lehetőséget, és a **Fájl** adatkészlet típusa lehetőséget.  

1. Ellenőrizze, hogy a tömörített fájl elérhető-e a Bal oldali modulfa **Adatkészletek** kategóriájának **Saját adatkészletek** listájában.

1.  Csatlakoztassa az adatkészletet a **Script Bundle beviteli** portjához.

1. A ZIP-fájlban található összes fájl elérhető a folyamat futási ideje alatt. 

    Ha a parancsfájlkötegfájl könyvtárstruktúrát tartalmaz, a struktúra megmarad. A könyvtár **./Script Bundle** elérési útját azonban módosítania kell a kód módosításához.

### <a name="process-data"></a>Adatok feldolgozása

A következő minta bemutatja, hogyan méretezhető késedelmi adatok:

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

### <a name="read-a-zip-file-as-input"></a>ZIP-fájl olvasása bemenetként

Ez a minta bemutatja, hogyan használható egy adatkészlet egy ZIP-fájlban az **R-parancsfájl végrehajtása** modul bemeneteként.

1. Hozza létre az adatfájlt CSV formátumban, és nevezze el "mydatafile.csv" néven.
1. Hozzon létre egy ZIP-fájlt, és adja hozzá a CSV-fájlt az archívumhoz.
1. Töltse fel a tömörített fájlt az Azure Machine Learning-munkaterületre. 
1. Csatlakoztassa az eredményül kapott adatkészletet az **R-parancsfájl végrehajtása** modul **ScriptBundle** bemenetéhez.
1. A következő kód segítségével olvassa be a CSV adatokat a tömörített fájlból.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Sorok replikálása

Ez a minta bemutatja, hogyan replikálható a pozitív rekordok egy adatkészletben a minta egyensúlyának kiegyenlítéséhez:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R-objektumok átkapcsolása R-parancsfájl-modulok végrehajtása között

R-objektumokat adhat át az **R-parancsfájl végrehajtása** modul példányai között a belső szerializálási mechanizmus használatával. Ez a példa feltételezi, hogy a `A` két **R-parancsfájl-végrehajtás** modul között elnevezett R-objektumot szeretné áthelyezni.

1. Adja hozzá az első **R-parancsfájl-végrehajtás** modult a folyamathoz, és írja be `A` a következő kódot az R **parancsfájl** mezőbe, ha szerializált objektumot hoz létre oszlopként a modul kimeneti adattáblájában:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Az explicit átalakítás egész típusra azért történik, mert a szerializálási függvény az R `Raw` formátumban adja ki az adatokat, amelyet a tervező nem támogat.

1. Adja hozzá az **R-parancsfájl végrehajtása** modul második példányát, és csatlakoztassa az előző modul kimeneti portjához.

1. Írja be a következő kódot az R `A` **script** szövegmezőbe az objektum beviteli adattáblából való kinyeréséhez. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Előre telepített R csomagok

Az előre telepített R csomagok aktuális listája:

|              |            | 
|--------------|------------| 
| Csomag      | Verzió    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| indítás         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| osztály        | 7.3-15     | 
| cli          | 1.1.0      | 
| vágó        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
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
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Generikus     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
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
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
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
| Ps           | 1.3.0      | 
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
| rpart        | 4.1-15     | 
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
| survival     | 2.44-1.1   | 
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex között      | 0.13       | 
| eszközök        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun között         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
