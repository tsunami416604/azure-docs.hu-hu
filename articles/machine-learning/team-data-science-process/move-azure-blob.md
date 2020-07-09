---
title: Adatok áthelyezése az Azure Blob Storage-ba, illetve onnan onnan – csoportos adatelemzési folyamat
description: Adatok áthelyezése az Azure Blob Storage-ba és onnan az Azure Storage Explorer, a AzCopy, a Python és a SSIS használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc58651bcb3b266b981fb953fd7341427d47fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76717573"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Adatok áthelyezése Azure Blob Storage-ba és-ból

A csoportos adatelemzési folyamat megköveteli, hogy az adatmennyiség különböző tárolási környezetbe kerüljön, illetve a folyamat egyes szakaszaiban a legmegfelelőbb módon legyen feldolgozható vagy elemezhető.

## <a name="different-technologies-for-moving-data"></a>Különböző technológiák az adatáthelyezéshez

Az alábbi cikkek azt ismertetik, hogyan helyezhetők át adatok az Azure Blob Storage-ba, illetve onnan onnan különböző technológiák használatával.

* [Azure Storage – Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Az Ön számára legmegfelelőbb módszer a forgatókönyvtől függ. Azure Machine Learning cikkben a [speciális elemzési forgatókönyvek](plan-sample-scenarios.md) segítségével meghatározhatja a speciális elemzési folyamat során használt különböző adatelemzési munkafolyamatokhoz szükséges erőforrásokat.

> [!NOTE]
> Az Azure Blob Storage teljes körű bevezetéséhez tekintse meg az [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és az [Azure Blob Service](https://msdn.microsoft.com/library/azure/dd179376.aspx)témakört.
> 
> 

## <a name="using-azure-data-factory"></a>Az Azure Data Factory használata

Alternatív megoldásként a következőkre használhatja a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) : 

* Az Azure Blob Storage-ból származó adatok letöltését szolgáló folyamat létrehozása és beütemezhet 
* adja át egy közzétett Azure Machine Learning webszolgáltatásnak, 
* fogadja a prediktív elemzési eredményeket, és 
* Töltse fel az eredményeket a Storage-ba. 

További információ: [prediktív folyamatok létrehozása Azure Data Factory és Azure Machine learning használatával](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy rendelkezik Azure-előfizetéssel, egy Storage-fiókkal és az adott fiókhoz tartozó tárolási kulccsal. Az adatfeltöltés/-letöltés előtt ismernie kell az Azure Storage-fiók nevét és a fiók kulcsát.

* Azure-előfizetés beállításához tekintse meg az [ingyenes egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/).
* A Storage-fiók létrehozásával, valamint a fiók-és a kulcsfontosságú információk beszerzésével kapcsolatos útmutatásért lásd: [Tudnivalók az Azure Storage-fiókokról](../../storage/common/storage-create-storage-account.md).

