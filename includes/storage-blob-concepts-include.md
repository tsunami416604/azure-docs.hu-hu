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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136018"
---
Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. Strukturálatlan adatok olyan adatok, amelyek nem felelnek meg egy adott adatmodellhez vagy definícióhoz, például szöveges vagy bináris adatokhoz.

## <a name="about-blob-storage"></a>Az Azure Blob Storage névjegye

A Blob Storage használati lehetőségei:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Írás naplófájlokba.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A felhasználók vagy az ügyfélalkalmazások http/HTTPS-en keresztül érhetik el a Blob storage objektumait a világ bármely pontjáról. A Blob storage-ban lévő objektumok az [Azure Storage REST API-n,](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)az Azure [PowerShellen,](https://docs.microsoft.com/powershell/module/azure.storage)az [Azure CLI-n](https://docs.microsoft.com/cli/azure/storage)vagy egy Azure Storage-ügyfélkódtáron keresztül érhetők el. Az ügyféltárak különböző nyelveken érhetők el, többek között a következőkben:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Node.js](https://azure.github.io/azure-storage-node) 
* [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)
* [Ugrás](https://github.com/azure/azure-storage-blob-go/)
* [Php](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2- ről

A Blob Storage támogatja az Azure Data Lake Storage Gen2-t, amely a Microsoft felhőbeli, vállalati adatok big data koncepción alapuló elemzési megoldása. Az Azure Data Lake Storage Gen2 hierarchikus fájlrendszert, valamint a Blob storage előnyeit kínálja, többek között a következőket:

* Alacsony költségű, többszintű tárolás
* Magas rendelkezésre állás
* Erős konzisztencia
* Vész-helyreállítási képességek

A Data Lake Storage Gen2 szolgáltatásról további információt az [Azure Data Lake Storage Gen2 – Bevezetés](../articles/storage/data-lake-storage/introduction.md)című témakörben talál.
