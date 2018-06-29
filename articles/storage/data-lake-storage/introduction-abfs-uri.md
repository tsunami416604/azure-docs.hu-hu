---
title: Az Azure Data Lake tárolási Gen2 Preview URI használata
description: Az Azure Data Lake tárolási Gen2 Preview URI használata
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
manager: jahogg
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 75edf6dc7382a8a2ece7c25edd09aeacfe1c5189
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060059"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Használja az Azure Data Lake tárolási Gen2 URI

A [Hadoop-fájlrendszer](http://www.aosabook.org/en/hdfs.html) illesztőprogramot, amely kompatibilis a Azure Data Lake tárolási Gen2 Preview ismert a rendszer azonosítóval `abfs` (Azure Blob-fájlrendszer). Konzisztens más Hadoop-fájlrendszer illesztőprogramokat, a ABFS illesztőprogram alkalmaz egy URI-formátum történő címet fájlok és könyvtárak egy Data Lake tárolási Gen2 képes a fiókon belül.

## <a name="uri-syntax"></a>URI-szintaxis

A Data Lake tárolási Gen2 URI szintaxisa függ-e a tárfiók van beállítva Data Lake tárolási Gen2 alapértelmezett fájlrendszer rendelkezik.

Ha a Data Lake tárolási Gen2 képes cím kívánja beállítása az alapértelmezett fájlrendszer számítógépfiók létrehozása közben, majd a rövid szintaxist URI-szintaxis van:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Elérési út**: tagolt perjellel (`/`) a könyvtárstruktúra ábrázolását.

2. **Fájlnév**: az egyes fájl nevét.

Ha a Data Lake tárolási Gen2 képes a fiókot szeretné cím *nem* az alapértelmezett fájlrendszer, a URI-szintaxis a következő:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Sémaazonosítót**: A `abfs` protokoll a rendszer azonosítóként van használatban. Lehetősége van csatlakozni, vagy a secure socket layer (SSL) kapcsolat nélkül. Használjon `abfss` biztonságos szoftvercsatorna-réteg kapcsolaton keresztül csatlakozni.

2. **Fájlrendszer**: A szülő hely, amely tárolja a fájlokat és mappákat. Ez megegyezik a tárolókat az Azure Storage Blobs szolgáltatásban.

3. **Fióknév**: név, a tárfiók létrehozása során.

4. **Elérési utak**: tagolt perjellel (`/`) a könyvtárstruktúra ábrázolását.

5. **Fájlnév**: az egyes fájl nevét. Ez a paraméter nem kötelező, ha éppen aktuális könyvtár.

## <a name="next-steps"></a>További lépések

- [Azure Data Lake tárolási Gen2 használata Azure HDInsight-fürtök](use-hdi-cluster.md)