---
title: Az Azure Data Lake Storage Gen2 előzetes URI használata
description: Az Azure Data Lake Storage Gen2 előzetes URI használata
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 71c3343db592162bcfa7ec46a29e0d680ca83079
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900706"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Használja az Azure Data Lake Storage Gen2 URI

A [Hadoop-fájlrendszer](http://www.aosabook.org/en/hdfs.html) illesztőprogramot, amely kompatibilis az Azure Data Lake Storage Gen2 előzetes verziója a rendszer-azonosító alapján van azonosítva `abfs` (Azure Blob-fájlrendszer). Más Hadoop-fájlrendszer illesztőprogramok összhangban a ABFS illesztőprogram alkalmaz egy URI-formátum cím fájlok és könyvtárak egy Data Lake Storage Gen2 képes a fiókon belül.

## <a name="uri-syntax"></a>URI-szintaxis

A Data Lake Storage Gen2 URI szintaxisa függ-e beállítása a tárfiókhoz, Data Lake Storage Gen2 az alapértelmezett fájlrendszerként van.

Ha a Data Lake Storage Gen2 képes a fiókot szeretné cím **nem** állítja be az alapértelmezett fájlrendszer fiók létrehozása során, akkor az URI szintaxisának gyorsírás:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Sémaazonosítót**: A `abfs` protokoll a séma azonosítóként van használatban. Lehetősége van csatlakozni, vagy a secure socket layer (SSL) kapcsolat nélkül. Használat `abfss` secure socket layer-kapcsolattal csatlakozni.

2. **Fájlrendszer**: A szülő hely neve, amely tárolja a fájlokat és mappákat. Ez megegyezik a tárolók az Azure Storage Blobs szolgáltatásban.

3. **Fiók neve**: név, a storage-fiók létrehozása során.

4. **Elérési utak**: tagolt perjellel (`/`) a könyvtárstruktúra ábrázolása.

5. **Fájlnév**: az egyes fájl nevét. Ez a paraméter nem kötelező, ha éppen aktuális címtár.

Azonban ha a cím kívánt fiók be van állítva az alapértelmezett fájlrendszerként fiók létrehozása során, majd a gyorsírás URI szintaxisának a következő:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Elérési út**: tagolt perjellel (`/`) a könyvtárstruktúra ábrázolása.

2. **Fájlnév**: az egyes fájl nevét.


## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök](use-hdi-cluster.md)
