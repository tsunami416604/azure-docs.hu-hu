---
title: ScaleR és SparkR használata Azure HDInsight
description: ScaleR és SparkR használata a HDInsight Machine Learning-szolgáltatások
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2017
ms.openlocfilehash: da486b25a9a35cb4f00d6e5a4689d5be3d270e36
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013275"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>ScaleR és SparkR a HDInsight egyesítése

Ez a dokumentum bemutatja, hogy miként jelezhetők előre a járatok érkezési késésének használatával egy **ScaleR** logisztikai regressziós modellt. A példában repülési késleltetés és az időjárási adatok, illesztve **SparkR**.

Bár mindkét csomagot futtatja a Hadoop Spark-végrehajtó motor, azok hozzáférése a memóriában lévő adatok megosztása, tesztelhető, mindegyik a saját megfelelő Spark-munkamenetet. Ezzel a problémával ML Server egy jövőbeli verziójában, amíg a megoldás, hogy egymást nem átfedő Spark-munkameneteket, valamint az exchange-adatok köztes fájlok között. Az itt leírt utasításokat mutatja, hogy ezek a követelmények egyszerű eléréséhez.

Ebben a példában először megosztott egy előadás Strata 2016 a Mario Inchiosa és Roni Burd. Ez az előadás címen található [létrehozása egy méretezhető adatelemzési Platform r](http://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio).

A kód eredetileg íródott ML Server Spark futtat egy HDInsight-fürtön az Azure-ban. Azonban egy parancsfájl ScaleR és SparkR használatát keverése fogalma is érvényes a helyszíni környezetekben kontextusában.

Ez a dokumentum lépései azt feltételezik, hogy egy köztes szint alatt, és az R ismerete a [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) ML Server könyvtárában. Jelennek meg [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) közben ebben a forgatókönyvben.

## <a name="the-airline-and-weather-datasets"></a>A légitársaság és időjárási adatkészletek

A flight data érhető el a [US government archívumok](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Emellett érhető el a zip [AirOnTimeCSV.zip](http://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip).

Az időjárási adatok letölthető tömörített fájlként nyers formában, havonta, a [együttműködési és a felügyelet alá tárház](http://www.ncdc.noaa.gov/orders/qclcd/). Ebben a példában töltse le az adatokat a május 2007 – a 2012. December. Az óránkénti adatfájlokat használjon, és `YYYYMMMstation.txt` fájlt a zips belül. 

## <a name="setting-up-the-spark-environment"></a>A Spark környezet beállítása

Használja a következő kódot a Spark környezet beállításához:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAcccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Ezután adjon hozzá `Spark_Home` az R-csomagoknak a keresési elérési utat. A keresési útvonalat adásával lehetővé teszi SparkR használja, és a egy SparkR munkamenet inicializálása:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Az időjárási adatok előkészítésével

Az időjárási adatok előkészítéséhez, részhalmazát, hogy az oszlopok szükséges modellezés: 

- "Visibility"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "Szélsebesség."
- "Magasságmérő"

Ezután adjon hozzá egy repülőtér kódja a időjárásjelző társított, és a mértékek konvertálása helyi idő (UTC).

Első lépésként hozzon létre egy fájlt a időjárásjelző (WBAN) adatok leképezése egy repülőtér kódja. Az alábbi kód beolvassa az óránkénti nyers időjárási adatok fájlok, részhalmazainak azt kell, egyesíti a időjárásjelző leképezési fájl, beállítja az dátum időpontokat az UTC Időzóna mérések és a fájl új verziója, majd írja az oszlopokhoz:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>A légitársaság és időjárási az adatok importálása a Spark adatkerettípusokat jelölhet.

Most használjuk a SparkR [read.df()](https://docs.databricks.com/spark/1.6/sparkr/functions/read.df.html#read-df) függvényt, hogy az adatok importálása az időjárási és légitársaság Spark adatkerettípusokat jelölhet. Ez a funkció számos más Spark módszerrel, például végrehajtása ráérősen, ami azt jelenti, hogy a végrehajtási várólistára, de nem hajtotta végre, amíg nem szükséges.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Adattisztítás és átalakítása

Ezután végzünk néhány karbantartást az légitársaság adatok importálása után az oszlopok átnevezése. Azt csak hagyja meg a szükséges változókat, és ütemezett indító alkalommal le a legfrissebb időjárás adatokkal induláskor az egyesítés a legközelebbi óra kerekíteni:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Most már az időjárási adatok hasonló műveletek elvégzése:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Az időjárási és légitársaság adatok csatlakoztatása

Most használjuk a SparkR [join()](https://docs.databricks.com/spark/1.6/sparkr/functions/join.html#join) függvény bal külső illesztéssel az indító AirportID által légitársaság és időjárási adatokat és a dátum és idő tennie. A külső illesztés lehetővé teszi számunkra, hogy a légitársaság adatfelderítési rekordok megőrizni, akkor is, ha nem tartoznak megfelelő időjárási adatok. A csatlakozás a következő azt távolítsa el néhány felesleges oszlopot, és nevezze át a megőrzött oszlopot távolítsa el a csatlakozás által bemutatott bejövő DataFrame előtagot.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

Hasonló módon hogy csatlakozzon a érkezési AirportID és a dátum és idő alapján az időjárási és légitársaság adatokat:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Az eredmények mentése CSV ScaleR az exchange-hez

Ezzel befejezte az illesztések SparkR mindenre kell. Az adatok mentése a végső Spark DataFrame a bemenet egy CSV kötethez "joinedDF5" ScaleR azt, és zárja be a SparkR munkamenet ki. Explicit módon biztosítunk SparkR menteni a eredő CSV 80 külön partíciókon tárolják a ScaleR feldolgozása elegendő párhuzamosság engedélyezése:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>ScaleR által használható XDF importálása

Használjuk a csatlakoztatott légitársaság és időjárási adatok, a CSV-fájlt sikerült – a modellezési szöveg ScaleR adatforrás-n keresztül. De azt azok importálása az XDF először, mivel azt a hatékonyabb, ha az adatkészlet több műveletet:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Felosztása tanítási és tesztelési adatait

RxDataStep tesztelési 2012 adatok felosztása és a többi képzéshez használjuk:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Taníthat vagy tesztelhet egy logisztikai regressziós modell

Most már készen állunk modell létrehozásához. A késleltetés az időjárási adatok hatását a érkezési ideje látható, a ScaleR a logisztikai regressziós rutin használjuk. Használjuk-e egy érkezési késleltetése nagyobb, mint 15 perccel az időjárás az indulási és érkezési repülőtereken befolyásol modellek:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Most nézzük meg, hogyan képes minderre a Tesztadatok az egyes előrejelzéseket és ROC és AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>A pontozás máshol

Azt is használhatja a modell pontozási adatok egy másik platformon. Mentése folyamatban van egy távoli asztali fájl és átvitel, és a cél környezetekben, például az SQL Server R Services pontozási való importálásakor, a távoli asztali szolgáltatások. Fontos győződjön meg arról, hogy az adatok pontozandó tényező szintek egyeznek, amelyen a modell lett létrehozva. Megfelelést kiváltó elérhető kinyeréséhez és az oszlopadatokat a ScaleR-n keresztül a modellezési adatok társított `rxCreateColInfo()` függvény és előrejelzési bemeneti adatok forrásának majd alkalmazza az adott oszlopra vonatkozó információ. A következő azt menteni a tesztelési adatkészletnél néhány sornyi és bontsa ki és használja ezt a mintát oszlop adatait az előrejelzési szkriptben:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Összefoglalás

Ez a cikk mutattuk be, hogyan kombinálhatók az adatkezelés a ScaleR modell fejlesztéséhez, a Hadoop Spark SparkR használata lehetőség. Ehhez a forgatókönyvhöz szükséges, hogy karbantartása külön Spark-munkameneteket, csak egy időben futnak egy munkamenetet, és exchange-adatok CSV-fájlok használatával. Bár ez egyszerűnek tűnik, ez a folyamat lehet még egyszerűbbé teszi a Machine Learning Services egy soron következő kiadásban SparkR és ScaleR is megosztani egy Spark-munkamenetet, és így a Spark DataFrames megosztása.

## <a name="next-steps-and-more-information"></a>Következő lépések és további információ

- A Spark ML-kiszolgáló használatára további információkért lásd: a [útmutató az első lépésekhez](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)

- A Machine Learning-kiszolgáló általános információkért lásd: a [R – első lépések](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node) cikk.

- A Machine Learning szolgáltatások a HDInsight további információkért lásd: [áttekintése, gépi Tanulási szolgáltatásokat, a HDInsight](r-server/r-server-overview.md) és [Machine Learning-szolgáltatások az Azure HDInsight – első lépések](r-server/r-server-get-started.md).

SparkR használata további információkat lásd:

- [Az Apache SparkR dokumentum](https://spark.apache.org/docs/2.1.0/sparkr.html)

- [SparkR áttekintése](https://docs.databricks.com/spark/latest/sparkr/overview.html) databricksből
