---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/05/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 09b120838c27f8fa6b4f7bdf8617ce806f740e3c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007298"
---
Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem felelnek meg egy adott adatmodellnek vagy definíciónak, például szöveges vagy bináris adatoknak.

## <a name="about-blob-storage"></a>Az Azure Blob Storage névjegye

A Blob Storage használati lehetőségei:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Írás naplófájlokba.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

A felhasználók vagy ügyfélalkalmazások a blob Storage-ban lévő objektumokat HTTP/HTTPS használatával érhetik el a világ bármely pontján. A blob Storage-ban lévő objektumok az [Azure storage REST API](/rest/api/storageservices/blob-service-rest-api), az [Azure PowerShell](/powershell/module/az.storage), az [Azure CLI](/cli/azure/storage)vagy egy Azure Storage ügyféloldali kódtár használatával érhetők el. Az ügyféloldali kódtárak különböző nyelvekhez érhetők el, beleértve a következőket:

* [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
* [Java](/java/api/overview/azure/storage)
* [Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)
* [Python](../articles/storage/blobs/storage-quickstart-blobs-python.md)
* [Ugrás](https://github.com/azure/azure-storage-blob-go/)
* [PHP](https://azure.github.io/azure-storage-php/)
* [Ruby](https://azure.github.io/azure-storage-ruby)

## <a name="about-azure-data-lake-storage-gen2"></a>Tudnivalók Azure Data Lake Storage Gen2

A Blob Storage támogatja az Azure Data Lake Storage Gen2-t, amely a Microsoft felhőbeli, vállalati adatok big data koncepción alapuló elemzési megoldása. Azure Data Lake Storage Gen2 hierarchikus fájlrendszert biztosít, valamint a blob Storage előnyeit, beleértve a következőket:

* Alacsony árú, többrétegű tárolás
* Magas rendelkezésre állás
* Erős konzisztenciát
* Vész-helyreállítási lehetőségek

További információ a Data Lake Storage Gen2ről: a [Azure Data Lake Storage Gen2 bemutatása](../articles/storage/blobs/data-lake-storage-introduction.md).