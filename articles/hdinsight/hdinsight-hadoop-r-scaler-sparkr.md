---
title: "ScaleR és SparkR használata az Azure HDInsight |} Microsoft Docs"
description: "R Server és a HDInsight ScaleR és SparkR használata"
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: b84c365defbaadbc83c86e6e387c15a63e0f17ce
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>ScaleR és SparkR a Hdinsightban

Ez a cikk bemutatja, hogyan repülési érkezési késések használatával megjósolható egy **ScaleR** logisztikai regresszió modell repülési késést és időjárási adatokból csatlakoztatni a **SparkR**. Ebben a forgatókönyvben a Spark használt Microsoft R Server analytics adatkezelési ScaleR lehetőségeit mutatja be. Ezek a technológiák együttesen a legújabb funkciókat az elosztott feldolgozási alkalmazását teszi lehetővé.

Bár mindkét csomagot Hadoop a Spark végrehajtási motorján futnak, akkor sem, minden egyes van szükségük a saját megfelelő Spark-munkamenetek megosztása a memóriában levő. Ezzel a problémával az R Server egy jövőbeli verziójában, amíg a megoldás, Spark-munkameneteket mozaikként, átfedés nélkül, és az exchange-adatok keresztül közbülső fájlok. Az utasítások mutatja, hogy ezek a követelmények egyszerű eléréséhez.

Itt először egy, a rétegek 2016 előadás a által megosztott Mario Inchiosa és Roni Burd, amely egyúttal a válaszok webes szemináriumhoz keresztül elérhető például használjuk [egy méretezhető tudományos Adatplatform r felépítése](http://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio). A példa SparkR való csatlakozáshoz a jól ismert légitársaság érkezési késleltetés adatkészlet indulási és érkezési repülőtereken időjárási adatokkal. Az adatok csatlakoztatott ScaleR logisztikai regresszió modell bemeneteként majd használja a repülési érkezési késleltetés előrejelzéséhez.

A kód azt forgatókönyv eredetileg készült az R Serverhez fut a Spark on Azure HDInsight-fürtöt. De a SparkR és ScaleR használatát egy parancsfájlban keverése fogalma is a helyszíni környezetben környezetében érvényes. Az alábbiakban azt feltételezik, a közbenső szintű Tudásbázis R és a [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) az R Server könyvtárban. Azt is vezethet használatát [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) ebben a forgatókönyvben közben.

## <a name="the-airline-and-weather-datasets"></a>A légitársaság és időjárási adatkészletek

A **AirOnTime08to12CSV** légitársaság nyilvános dataset repülési érkezési és indító adatait az USA, a December 2012. októberi 1987 belül minden kereskedelmi repülőútra információkat tartalmaz. Ez a nagy adatbázisból: nincsenek majdnem 150 millió rekordot összesen. Csomagolva csak a 4 GB. Az elérhető a [Egyesült államokbeli kormányzati archívumokat](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). Több kényelmesen, mint egy zip-fájl (AirOnTimeCSV.zip) tartalmazó 303 külön havi CSV-fájlok rendelkezésre áll a [fordulat Analytics dataset tárház](http://packages.revolutionanalytics.com/datasets/AirOnTime87to12/)

Repülési késések eredő időjárási megtekintéséhez a repülőtéren mindegyik időjárási adatok is kell. Ezek az adatok letölthető zip fájlt nyers formátumban, hónap, a [nemzeti óceáni és légköri felügyeleti tárház](http://www.ncdc.noaa.gov/orders/qclcd/). Ebben a példában az alkalmazásában azt olvasnak be adatokat időjárási, előfordulhat, hogy 2007 – December 2012 és a 68 havi zips belül az óránkénti adatfájlok használt. A havi zip-fájlok leképezéseket (YYYYMMstation.txt) is tartalmaz az időjárási állomás azonosítója (WBAN), a repülőtéren (hívójel) tartozik, és a repülőtéren időzóna eltolás az UTC (időzóna) között. Ezen információk van szükség, ha a légitársaság késleltetés és időjárási adatokkal való csatlakozás.

## <a name="setting-up-the-spark-environment"></a>A Spark-környezet létrehozása

Az első lépés a Spark környezet beállítása. Az első lépések a bemeneti adatok könyvtárak tartalmazó könyvtárra mutat, egy Spark számítási környezet létrehozásakor, és a konzol tájékoztató naplózás naplózási függvény létrehozása:

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

Ezután azt hozzáadása "Spark_Home" R csomagok a keresési elérési útját, hogy azt SparkR használja, és egy SparkR munkamenet inicializálása:

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

## <a name="preparing-the-weather-data"></a>Az időjárási adatok előkészítése

Időjárás létrehozása azt részét úgy, hogy az oszlopok modellezési szükséges: 

- "Látható"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "Szélsebesség"
- "Magasságmérő"

Majd azt egy időjárási állomás társított repülőtéri kódot, és a mértékek konvertálása helyi idő az egyezményes világidőhöz.

Az első lépések a időjárási állomáson (WBAN) adatait hozzárendelése egy repülőtéri kódot fájl létrehozásával. Azt a korrelációs sikerült beszerezni a leképezési fájlban az időjárási adatokat tartalmazza. Leképezi a *hívójel* (például LAX) mező mellett a időjárási adatfájl *származási* légitársaság adatban. Azonban azt csak történt egy másik leképezési aktuális rendelik hozzá *WBAN* való *AirportID* (például 12892 LAX), és tartalmazza *időzóna* , hogy mentette a CSV-fájl neve "wban-az-repülőtéri-azonosító-tz. Fürt megosztott kötetei szolgáltatás", amely is használhatók. Példa:

| AirportID | WBAN | Időzóna
|-----------|------|---------
| 10685 | 54831 | -6
| 14871 | 24232 | -8
| .. | .. | ..

Az alábbi kód beolvassa az óránkénti nyers időjárási adatok fájlok, igazolnia kell, a időjárási állomás leképezési fájlban egyesíti, állítja be az időpontok UTC mérések és a fájl egy új verzióját, majd írja oszlopokra részhalmazának:

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

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>A légitársaság és időjárási adatok importálása a Spark DataFrames

Most a SparkR használjuk [read.df()](https://docs.databricks.com/spark/latest/sparkr/functions/read.df.html) működnek, mint a időjárása és a légitársaság adatok importálása a Spark DataFrames. Ez a funkció számos más Spark módszerek, például végrehajtása lazily, ami azt jelenti, hogy a végrehajtási sorba állított, de nem hajtotta végre a kötelező.

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

## <a name="data-cleansing-and-transformation"></a>Adatok tisztítására és átalakítás

Ezután néhány tisztítás azt importált légitársaság adatokon végezzük oszlop átnevezése. Jelenleg csak megőrzése a változók szükséges, és le a legfrissebb időjárási adatokkal induláskor az egyesítés a legközelebbi óra ütemezett indulási idő kerekíteni:

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

Most végezzük az időjárási adatokon hasonló műveletek:

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

## <a name="joining-the-weather-and-airline-data"></a>A időjárása és a légitársaság adatok csatlakoztatása

Használja a SparkR [join()](https://docs.databricks.com/spark/latest/sparkr/functions/join.html) függvény elvégzéséhez a bal oldali külső illesztések az indító AirportID légitársaság és időjárási adatait és a dátum és idő. A külső illesztés lehetővé teszi a légitársaság adatrekordot megőrizni, akkor is, ha nincsenek egyező időjárási adatok. A való csatlakozást követően azt néhány felesleges oszlopok eltávolítása, és nevezze át a megtartva oszlopok eltávolítása az illesztés által bemutatott bejövő DataFrame előtag.

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

Hasonló módon csatlakoztassa azt érkezési AirportID és a dátum és idő alapján az időjárási és légitársaság adatokat:

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

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>A CSV-eredményeket menteni ScaleR az exchange-hez

Ezzel befejezte az illesztések kell elvégezni a segítségével SparkR. Az adatok mentése a végső Spark DataFrame a bemeneti egy CSV kötethez "joinedDF5" ScaleR azt, és zárja be a SparkR munkamenet ki. Explicit módon biztosítunk SparkR menteni a eredő CSV 80 külön partíciók elegendő párhuzamos ScaleR feldolgozásának engedélyezése:

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

## <a name="import-to-xdf-for-use-by-scaler"></a>ScaleR általi használatra XDF importálása

A CSV-fájl illesztett légitársaság és időjárási adataikat sikerült használjuk-a modellezési ScaleR szöveg adatforrás keresztül. De azt importálja XDF először, mert ez sokkal hatékonyabb az adatkészlettel több művelet futtatásakor:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for R Server 
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

## <a name="splitting-data-for-training-and-test"></a>Felosztása tanítási és tesztelési adatokat

RxDataStep ossza fel a tesztelési 2012 adatai és a többi képzési használjuk:

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

## <a name="train-and-test-a-logistic-regression-model"></a>Betanítása és logisztikai regresszió modell teszteléséhez

Most azt készen áll a modell létrehozása. Késleltetés a érkezésének ideje a időjárási adatok hatását megtekintéséhez ScaleR tartozó logisztikai regresszió rutin használjuk. Használjuk, hogy egy érkezési késés nagyobb, mint 15 percig befolyásolja a indulási és érkezési repülőtereken időjárási modellek:

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

Most nézzük meg, hogyan végez el a a tesztadatokat néhány előrejelzéseket és ROC és AUC megnézi.

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

## <a name="scoring-elsewhere"></a>A pontozási máshol

Azt is használható a modell más platformon pontozási adatokat. Egy távoli asztali szolgáltatások fájlt mentse és átvitele, és a távoli asztali szolgáltatások importál egy cél, például az SQL Server R Services környezet pontozási. Fontos, hogy az adatok pontozni tényező szintjének meg kell azokat, amelyeken a modell lett létrehozva. Megfelelő legyen elérhető kinyeréséhez, valamint a társított a modellezési keresztül ScaleR tartozó oszlop adatok mentése `rxCreateColInfo()` függvény és majd, hogy oszlopra vonatkozó információ alkalmazása az előrejelzés bemeneti adatforrását. A következő azt a tesztelési adatkészletnél néhány sornyi mentése, bontsa ki és olvassa el oszlop ettől a példától az előrejelzés parancsfájl:

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

Ez a cikk azt már látható hogyan is lehet kombinálni SparkR használható modell fejlesztési Hadoop Spark ScaleR adatok módosítását. Ebben a forgatókönyvben csak fut egyszerre csak egy munkamenet külön Spark munkamenetek, karbantartása és exchange-adatok CSV-fájlok keresztül igényel. Egyszerű, bár ez a folyamat lehet egy jövőbeli R Server a kiadásban még egyszerűbbé SparkR és ScaleR is megosztani egy Spark-munkamenetet, és így megosztani a Spark DataFrames.

## <a name="next-steps-and-more-information"></a>Következő lépések és további információ

- Spark R-kiszolgáló használatára vonatkozó további információkért lásd: a [kezdeti lépések útmutatóban az MSDN webhelyen](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)

- R Server általános információkért tekintse meg a [Ismerkedés az R](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node) cikk.

- Az R Server on HDInsight információkért lásd: [az R Server on Azure HDInsight áttekintése](r-server/r-server-overview.md) és [az R Server on Azure HDInsight](r-server/r-server-get-started.md).

SparkR használatára vonatkozó további információkért lásd:

- [Apache SparkR dokumentum](https://spark.apache.org/docs/2.1.0/sparkr.html)

- [SparkR áttekintése](https://docs.databricks.com/spark/latest/sparkr/overview.html) a Databricks
