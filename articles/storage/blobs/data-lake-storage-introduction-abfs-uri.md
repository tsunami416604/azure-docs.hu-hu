---
title: Használja az Azure Data Lake Storage Gen2 URI
description: Használja az Azure Data Lake Storage Gen2 URI
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e4f148ac6f66cfbd7d89eff3623418197c2e0a33
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681182"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Használja az Azure Data Lake Storage Gen2 URI

A [Hadoop-fájlrendszer](https://www.aosabook.org/en/hdfs.html) illesztőprogramot, amely kompatibilis az Azure Data Lake Storage Gen2 a rendszer-azonosító alapján van azonosítva `abfs` (Azure Blob-fájlrendszer). Más Hadoop-fájlrendszer illesztőprogramok összhangban a ABFS illesztőprogram alkalmaz egy URI-formátum cím fájlok és könyvtárak egy Data Lake Storage Gen2 képes a fiókon belül.

## <a name="uri-syntax"></a>URI-szintaxis

A Data Lake Storage Gen2 URI szintaxisa függ-e beállítása a tárfiókhoz, Data Lake Storage Gen2 az alapértelmezett fájlrendszerként van.

Ha a Data Lake Storage Gen2 képes a fiókot szeretné cím **nem** állítja be az alapértelmezett fájlrendszer fiók létrehozása során, akkor az URI szintaxisának gyorsírás:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Sémaazonosítót**: A `abfs` protokoll a séma azonosítóként van használatban. Lehetősége van csatlakozni, vagy a secure socket layer (SSL) kapcsolat nélkül. Használat `abfss` secure socket layer-kapcsolattal csatlakozni.

2. **Fájlrendszer**: A szülő hely neve, amely tárolja a fájlokat és mappákat. Ez megegyezik a tárolók az Azure Storage Blobs szolgáltatásban.

3. **Fiók neve**: A storage-fiók létrehozása során adott név.

4. **Elérési utak**: Tagolt perjellel (`/`) a könyvtárstruktúra ábrázolása.

5. **Fájlnév**: Az egyes fájl neve. Ez a paraméter nem kötelező, ha éppen aktuális címtár.

Azonban ha a cím kívánt fiók be van állítva az alapértelmezett fájlrendszerként fiók létrehozása során, majd a gyorsírás URI szintaxisának a következő:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Elérési út**: Tagolt perjellel (`/`) a könyvtárstruktúra ábrázolása.

2. **Fájlnév**: Az egyes fájl neve.


## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
