---
title: Az Azure Data Lake Storage Gen2 URI használata
description: Az Azure Data Lake Storage Gen2 URI használata
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: fa0f67e0d72ee5710a42b6de744ddae98e20220a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437135"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Az Azure Data Lake Storage Gen2 URI használata

A Azure Data Lake Storage Gen2 kompatibilis [Hadoop fájlrendszer](https://www.aosabook.org/en/hdfs.html) -illesztőprogramot a séma-azonosítója `abfs` (Azure Blob fájlrendszer) ismeri. Más Hadoop fájlrendszer-illesztőprogramokkal összhangban a ABFS-illesztőprogram egy URI-formátumot alkalmaz a fájlok és könyvtárak egy Data Lake Storage Gen2 képes fiókon belüli kezelésére.

## <a name="uri-syntax"></a>URI-szintaxis

Data Lake Storage Gen2 URI-szintaxisa attól függ, hogy a Storage-fiók úgy van-e beállítva, hogy az alapértelmezett fájlrendszerként Data Lake Storage Gen2.

Ha a kezelni kívánt Data Lake Storage Gen2-fiók nem alapértelmezett fájlrendszerként **van** beállítva a fiók létrehozása során, akkor a Gyorsírás URI-szintaxisa a következő:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Séma azonosítója**: a `abfs` rendszer a protokollt használja séma-azonosítóként. Lehetősége van a kapcsolódásra Transport Layer Security (TLS), korábbi nevén SSL (SSL) kapcsolattal vagy anélkül. TLS `abfss` -kapcsolattal való kapcsolódáshoz használható.

2. **Fájlrendszer**: a fájlokat és mappákat tároló szülő hely. Ez ugyanaz, mint az Azure Storage-Blobok szolgáltatás tárolói.

3. **Fiók neve**: a létrehozás során a Storage-fióknak adott név.

4. **Elérési utak**: a címtár struktúrájának`/`perjel tagolt () ábrázolása.

5. **Fájlnév**: az egyedi fájl neve. Ez a paraméter nem kötelező, ha egy könyvtárat intéz.

Ha azonban a kezelni kívánt fiók alapértelmezett fájlrendszerként van beállítva a fiók létrehozása során, akkor a Gyorsírás URI-szintaxisa a következő:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Elérési út**: a címtár struktúrájának`/`perjel tagolt () ábrázolása.

2. **Fájlnév**: az egyedi fájl neve.


## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
