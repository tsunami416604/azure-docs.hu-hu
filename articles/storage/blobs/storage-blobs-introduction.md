---
title: "Azure Blob storage bemutatása |} Microsoft Docs"
description: "Azure Blob storage bemutatása"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: tamram
ms.openlocfilehash: 0097f1c02b88343a135b6489130a6e0d35cf6fba
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="introduction-to-blob-storage"></a>A Blob storage bemutatása

Az Azure Blob Storage szolgáltatás strukturálatlan adatok, például szövegek vagy bináris adatok nagy mennyiségben történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolaton keresztül a világon bárhonnan elérhetők. A Blob Storage segítségével bárki számára nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat alkalmazásadatokat.

A Blob Storage gyakori használati módjai többek között:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngésző számára
* Fájlok tárolása megosztott hozzáférésre
* Video- és hangtartalom online továbbítása
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz
* Adattárolás egy helyszíni vagy egy Azure által üzemeltetett szolgáltatás elemzéseihez

## <a name="blob-service-concepts"></a>A Blob szolgáltatással kapcsolatos fogalmak

A Blob szolgáltatás az alábbi összetevőkből áll:

![Blob-architektúra](./media/storage-blobs-introduction/blob1.png)

* **Tárfiók:** Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. Ez a tárfiók lehet egy **(v1 vagy v2) az általános célú tárfiók** vagy **Blob storage-fiókok**. További információk: [Tudnivalók az Azure Storage-fiókokról](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* **Tároló:** A tárolók blobkészletek csoportosítását biztosítják. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható. Vegye figyelembe, hogy a tároló neve csak kisbetűket tartalmazhat.

* **Blob:** Bármilyen típusú és bármekkora méretű fájl. Az Azure Storage háromféle blobot biztosít: blokkblobokat, [lapblobokat](storage-blob-pageblob-overview.md), és a hozzáfűző blobokhoz.
  
    A *blokkblobok* ideálisak szövegek és bináris fájlok, például dokumentumok és médiafájlok tárolására. A *hozzáfűző blobok* a blokkblobokhoz hasonlóan blokkokból épülnek fel, de műveletek hozzáfűzésére vannak optimalizálva, ezért hasznosak lehetnek a naplózási forgatókönyvekben. Egyetlen blokkblob akár 50 000, egyenként 100 MB méretű blokkot is tartalmazhat, a maximális méretük ezért valamivel több mint 4,75 TB (100 MB X 50 000 blokk). Egyetlen hozzáfűző blob akár 50 000, egyenként 4 MB méretű blokkot is tartalmazhat, a maximális méretük ezért valamivel több mint 195 GB (4 MB X 50 000 blokk).
  
    *Lapblobok* legfeljebb 8 TB méretű is lehet, és hatékonyabbak a gyakori olvasási/írási műveletek. Az Azure virtuális gépek lapblobokat használnak operációs rendszerként és adatlemezként.
  
    A tárolók és blobok elnevezésével kapcsolatos részletekért lásd: [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Tárolók, blobok és metaadatok elnevezése és hivatkozása).

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Ismerkedés a Blob storage .NET használatával](storage-dotnet-how-to-use-blobs.md)
* [Azure Storage-minták .NET használatával](../common/storage-samples-dotnet.md)
* [Azure Storage-minták javás környezetekben](../common/storage-samples-java.md)
