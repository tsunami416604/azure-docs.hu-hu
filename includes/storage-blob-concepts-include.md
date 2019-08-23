---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 1950e55cf669ea7502e8523d7f8fe429c2caeb49
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903956"
---
Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem felelnek meg egy adott adatmodellnek vagy definíciónak, például szöveges vagy bináris adatoknak.

## <a name="about-blob-storage"></a>Tudnivalók a blob Storage-ról

A Blob Storage használati lehetőségei:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Írás naplófájlokba.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A felhasználók vagy ügyfélalkalmazások a blob Storage-ban lévő objektumokat HTTP/HTTPS használatával érhetik el a világ bármely pontján. A blob Storage-ban lévő objektumok az [Azure storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), az [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), az [Azure CLI](https://docs.microsoft.com/cli/azure/storage)vagy egy Azure Storage ügyféloldali kódtár használatával érhetők el. Az ügyféloldali kódtárak számos nyelven elérhetők, többek között a [.net](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), a [Java](https://docs.microsoft.com/java/api/overview/azure/storage), a [Node. js](https://azure.github.io/azure-storage-node), a [Python](https://docs.microsoft.com/python/azure/), a [Go](https://github.com/azure/azure-storage-blob-go/), a [php](https://azure.github.io/azure-storage-php/)és a [Ruby](https://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Tudnivalók Azure Data Lake Storage Gen2

A Blob Storage támogatja az Azure Data Lake Storage Gen2-t, amely a Microsoft felhőbeli, vállalati adatok big data koncepción alapuló elemzési megoldása. Az Azure Data Lake Storage Gen2 hierarchikus fájlrendszert, valamint a Blob Storage előnyeit biztosítja, így például az alacsony költséget, a rétegzett tárolást, a magas rendelkezésre állást, az erős konzisztenciát és a vészhelyreállítási képességeket.

További információ a Data Lake Storage Gen2ről: a [Azure Data Lake Storage Gen2 bemutatása](../articles/storage/data-lake-storage/introduction.md).
