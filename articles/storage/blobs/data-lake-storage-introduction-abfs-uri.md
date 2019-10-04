---
title: A Azure Data Lake Storage Gen2 URI használata
description: A Azure Data Lake Storage Gen2 URI használata
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855557"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>A Azure Data Lake Storage Gen2 URI használata

A Azure Data Lake Storage Gen2 kompatibilis [Hadoop fájlrendszer](https://www.aosabook.org/en/hdfs.html) -illesztőprogramot a séma-azonosítója `abfs` (Azure Blob fájlrendszer) ismeri. Más Hadoop fájlrendszer-illesztőprogramokkal összhangban a ABFS-illesztőprogram egy URI-formátumot alkalmaz a fájlok és könyvtárak egy Data Lake Storage Gen2 képes fiókon belüli kezelésére.

## <a name="uri-syntax"></a>URI-szintaxis

Data Lake Storage Gen2 URI-szintaxisa attól függ, hogy a Storage-fiók úgy van-e beállítva, hogy az alapértelmezett fájlrendszerként Data Lake Storage Gen2.

Ha a kezelni kívánt Data Lake Storage Gen2-fiók nem alapértelmezett fájlrendszerként **van** beállítva a fiók létrehozása során, akkor a Gyorsírás URI-szintaxisa a következő:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Séma azonosítója**: A `abfs` protokoll a séma-azonosítóként szolgál. Az SSL-kapcsolat használatával vagy anélkül is csatlakozhat. A `abfss` használatával csatlakozhat egy biztonságos szoftvercsatorna-kapcsolathoz.

2. **Fájlrendszer**: A fájlokat és mappákat tároló szülő hely. Ez ugyanaz, mint az Azure Storage-Blobok szolgáltatás tárolói.

3. **Fiók neve**: A Storage-fióknak a létrehozás során megadott neve.

4. **Elérési utak**: A címtár struktúrájának perjel tagolt (`/`) ábrázolása.

5. **Fájlnév**: Az egyedi fájl neve. Ez a paraméter nem kötelező, ha egy könyvtárat intéz.

Ha azonban a kezelni kívánt fiók alapértelmezett fájlrendszerként van beállítva a fiók létrehozása során, akkor a Gyorsírás URI-szintaxisa a következő:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Elérési út**: A címtár struktúrájának perjel tagolt (`/`) ábrázolása.

2. **Fájlnév**: Az egyedi fájl neve.


## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
