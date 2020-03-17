---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e90e750bf248bdcc8e50c6ddc6e9fa0273660195
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136018"
---
Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy -definíciót, például szöveges vagy bináris adatok.

## <a name="about-blob-storage"></a>Az Azure Blob Storage névjegye

A Blob Storage használati lehetőségei:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Írás naplófájlokba.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A felhasználók és az ügyfélalkalmazások a Blob Storage-ban lévő objektumokat a világon bárhonnan elérhetik HTTP- vagy HTTPS-kapcsolaton keresztül. A Blob Storage-ban lévő objektumok az [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), az [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), az [Azure CLI](https://docs.microsoft.com/cli/azure/storage) vagy az Azure Storage-ügyfélkódtár segítségével érhetőek el. Az ügyfélkódtárak több nyelvhez is elérhetők, többek között a következőkhöz:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Go](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Tudnivalók az Azure Data Lake Storage Gen2-ről

A Blob Storage támogatja az Azure Data Lake Storage Gen2-t, amely a Microsoft felhőbeli, vállalati adatok big data koncepción alapuló elemzési megoldása. Az Azure Data Lake Storage Gen2 hierarchikus fájlrendszert, valamint a Blob Storage előnyeit biztosítja, például a következőket:

* Alacsony költségű, többszintű tárolás
* Magas rendelkezésre állás
* Erős konzisztencia
* Vészhelyreállítási funkciók

A Data Lake Storage Gen2-vel kapcsolatos további információkért tekintse meg az [Azure Data Lake Storage Gen2 bemutatását](../articles/storage/data-lake-storage/introduction.md).
