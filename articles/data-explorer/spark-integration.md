---
title: Az Apache Spark az Azure Data Explorer-összekötő
description: Ez a cikk bemutatja, hogyan használható az Azure Data Explorer-összekötő az Apache Spark.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 6f115df97d0b790c94d6dc07f3f40feeceb1a7cc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824215"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Az Apache Spark az Azure Data Explorer-összekötő

Használja az adatkezelőt az Azure és az Apache Spark, adatvezérelt forgatókönyvek, például a machine learning (gépi tanulás), a kinyerési, átalakítási-betöltési (ETL) és a Log Analytics célzó gyors, skálázható alkalmazásokat hozhat létre.

## <a name="prerequisites"></a>Előfeltételek

Az összekötő használatára, az alkalmazásnak kell rendelkeznie:

* Java 1.8 SDK
* Maven 3.x
* Spark-verziójú 2.3.2 vagy újabb

## <a name="link-to-data-explorer"></a>Az adatkezelő mutató hivatkozás

A Maven használatával szeretné Scala és Java-alkalmazások projektet a definíciókat, az alkalmazás a következő összetevő-hivatkozás:

```java
groupId = com.microsoft.azure
artifactId = spark-kusto-connector
version = 1.0.0-Beta-01 
```

A Mavenben a következő hivatkozásra:

```Maven
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-kusto-connector</artifactId>
    <version>1.0.0-Beta-01</version>
  </dependency>
```

## <a name="build-commands"></a>Parancsok létrehozása

Jar hozhat létre, és az összes vizsgálat futtatása:

```
mvn clean package
```

Jar hozhat létre, az összes teszt futtatása, és telepítse a jar a helyi Maven tárházra:

```
mvn clean install
```