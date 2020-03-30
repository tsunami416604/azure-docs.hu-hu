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
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68855557"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Az Azure Data Lake Storage Gen2 URI használata

Az Azure Data Lake Storage Gen2-vel kompatibilis [Hadoop fájlrendszer-illesztőprogram](https://www.aosabook.org/en/hdfs.html) a rendszerazonosítója `abfs` (Azure Blob File System) alapján ismert. A Hadoop fájlrendszer többi illesztőprogramjával összhangban az ABFS illesztőprogram URI-formátumot alkalmaz a Data Lake Storage Gen2 képes fiókon belüli fájlok és könyvtárak címzésére.

## <a name="uri-syntax"></a>URI szintaxis

A Data Lake Storage Gen2 URI-szintaxisa attól függ, hogy a tárfiók beállítása a Data Lake Storage Gen2 az alapértelmezett fájlrendszer.

Ha a címezni kívánt Data Lake Storage Gen2 képes fiók **nincs** beállítva alapértelmezett fájlrendszerként a fiók létrehozása során, akkor a gyorsírási URI szintaxis a következő:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Sémaazonosító**: A `abfs` protokoll rendszerazonosítóként szolgál. Lehetősége van arra, hogy biztonságos aljzatú réteggel (SSL) vagy anélkül csatlakozzon. Ezzel `abfss` csatlakozhat biztonságos aljzatréteg-kapcsolathoz.

2. **Fájlrendszer**: A fájlokat és mappákat tartalmazó szülőhely. Ez megegyezik az Azure Storage Blobs szolgáltatás tárolóival.

3. **Fióknév**: A tárfióknak a létrehozás során megadott név.

4. **Elérési utak**: A könyvtárstruktúra perjellel (`/`) tagolt ábrázolása.

5. **Fájlnév**: Az egyes fájl neve. Ez a paraméter nem kötelező, ha egy könyvtárat címez.

Ha azonban a címezni kívánt fiók van beállítva alapértelmezett fájlrendszerként a fiók létrehozása során, akkor a gyorsírásuri szintaxisa a következő:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Elérési út**: A`/`könyvtárstruktúra perjellel ( ) tagolt ábrázolása.

2. **Fájlnév**: Az egyes fájl neve.


## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
