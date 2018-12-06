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
ms.openlocfilehash: 32b9b12c2adf03a4cb0616a5da48dd33fc81fb4f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52973089"
---
Az Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. A BLOB storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. Strukturálatlan adatok olyan adat, amelyet nem követi a egy adott adatmodellhez vagy egy definíciót, például szöveg vagy bináris adatok. 

## <a name="about-blob-storage"></a>Blob storage-ról

A BLOB storage készült:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngészők számára.
* Fájlok tárolása megosztott hozzáféréshez.
* Video- és hangtartalom streamelése.
* Írás naplófájlokba.
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz.
* Adattárolás helyszíni vagy az Azure-ban üzemeltetett szolgáltatásban való elemzéshez.

Felhasználók vagy ügyfélalkalmazások érhető objektumok Blob Storage-via HTTP/HTTPS, bárhol a világon. Blob Storage-objektumok keresztül érhető el a [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell-lel](https://docs.microsoft.com/powershell/module/azure.storage), [Azure CLI-vel](https://docs.microsoft.com/cli/azure/storage), vagy egy Azure Storage ügyféloldali kódtára. Ügyfélkódtárak érhetők el a különböző nyelveken, köztük [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](http://azure.github.io/azure-storage-node), [Python](https://docs.microsoft.com/python/azure/), [Go ](https://github.com/azure/azure-storage-blob-go/), [PHP](http://azure.github.io/azure-storage-php/), és [Ruby](http://azure.github.io/azure-storage-ruby).

## <a name="about-azure-data-lake-storage-gen2"></a>Tudnivalók az Azure Data Lake Storage Gen2 

BLOB storage támogatja az Azure Data Lake Storage Gen2, a Microsoft vállalati big data analytics megoldás a felhőben. Az Azure Data Lake Storage Gen2 ajánlatok hierarchikus egy fájlt a rendszer, valamint a Blob storage, beleértve az alacsony költségű, többrétegű tárolást; előnyeit magas rendelkezésre állású; erős konzisztencia; és a vész-helyreállítási lehetőségei. 

További információ a Data Lake Storage Gen2: [bemutatása az Azure Data Lake Storage Gen2 előzetes verzió](../articles/storage/data-lake-storage/introduction.md).