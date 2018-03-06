---
title: "Bevezetés az Azure Blob Storage használatába | Microsoft Docs"
description: "Az Azure Blob Storage bemutatása"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 09/28/2017
ms.author: tamram
ms.openlocfilehash: aabc10b1c1c501e4fa0eb31835c2818cfb8942c6
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="introduction-to-blob-storage"></a>A Blob Storage bemutatása

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

* **Tárfiók:** Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. Ez a tárfiók lehet **általános célú tárfiók (v1 vagy v2)** vagy **Blob Storage-fiók**. További információk: [Tudnivalók az Azure Storage-fiókokról](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* **Tároló:** A tárolók blobkészletek csoportosítását biztosítják. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható. Vegye figyelembe, hogy a tároló neve csak kisbetűket tartalmazhat.

* **Blob:** Bármilyen típusú és bármekkora méretű fájl. Az Azure Storage háromféle blobot biztosít: blokkblobokat, [lapblobokat](storage-blob-pageblob-overview.md) és hozzáfűző blobokat.
  
    A *blokkblobok* ideálisak szövegek és bináris fájlok, például dokumentumok és médiafájlok tárolására. A *hozzáfűző blobok* a blokkblobokhoz hasonlóan blokkokból épülnek fel, de műveletek hozzáfűzésére vannak optimalizálva, ezért hasznosak lehetnek a naplózási forgatókönyvekben. Egyetlen blokkblob akár 50 000, egyenként 100 MB méretű blokkot is tartalmazhat, a maximális méretük ezért valamivel több mint 4,75 TB (100 MB X 50 000 blokk). Egyetlen hozzáfűző blob akár 50 000, egyenként 4 MB méretű blokkot is tartalmazhat, a maximális méretük ezért valamivel több mint 195 GB (4 MB X 50 000 blokk).
  
    A *lapblobok* akár 8 TB méretűek is lehetnek, és a gyakori írási/olvasási műveletek esetén hatékony a használatuk. Az Azure virtuális gépek lapblobokat használnak operációs rendszerként és adatlemezként.
  
    A tárolók és blobok elnevezésével kapcsolatos részletekért lásd: [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Tárolók, blobok és metaadatok elnevezése és hivatkozása).

## <a name="next-steps"></a>További lépések

* [Tárfiók létrehozása](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel](storage-dotnet-how-to-use-blobs.md)
* [.NET-keretrendszert használó Azure Storage-minták](../common/storage-samples-dotnet.md)
* [Javát használó Azure Storage-minták](../common/storage-samples-java.md)
