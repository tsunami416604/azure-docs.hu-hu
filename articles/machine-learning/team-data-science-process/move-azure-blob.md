---
title: Adatok importálására és az Azure Blob storage - csoportos adatelemzési folyamat
description: Adatok áthelyezése Azure Blob storage szolgáltatásba vagy onnan
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ca5a75ec61a0f75b3a008762561fed8231fce33d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453756"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Adatok áthelyezése Azure Blob storage szolgáltatásba vagy onnan

A csoportos adatelemzési folyamat szükséges adatok betöltött vagy betölti a dolgozhatók fel és elemzi a legmegfelelőbb módon, a folyamat minden egyes szakaszhoz különböző tárolási környezetben.

## <a name="different-technologies-for-moving-data"></a>Adatok áthelyezése a különböző technológiák

Az alábbi cikkek ismertetik az adatok áthelyezése a különböző technológiák használatával Azure Blob storage szolgáltatásba vagy onnan.

* [Az Azure Storage Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](move-data-to-azure-blob-using-azcopy.md)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

A forgatókönyvtől függ, hogy melyik módszer a legjobb az Ön számára. A [az Azure Machine Learning speciális elemzési forgatókönyvek](plan-sample-scenarios.md) a cikknek a segítségével meghatározhatja, hogy a fejlett analitikai folyamat során használt adatok adatelemzési munkafolyamatainak különböző a szükséges erőforrásokat.

> [!NOTE]
> Egy teljes körű Bevezetés az Azure blob storage, tekintse meg a [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob Service-ben](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="using-azure-data-factory"></a>Az Azure Data Factory használata

Alternatív megoldásként használható [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) való: 

* Hozzon létre és ütemezhet egy folyamatot, amely adatokat tölt le az Azure blob storage-ban 
* Adja át azt egy közzétett Azure Machine Learning webszolgáltatás 
* a prediktív elemzési eredményeket és 
* Töltse fel az eredmények tárolása. 

További információkért lásd: [hozhatók létre az Azure Data Factory és az Azure Machine Learning prediktív adatcsatornák](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy rendelkezik Azure-előfizetéssel, egy storage-fiókot és a kapcsolódó tárfiók-kulcsot az adott fiók. Adatok feltöltése/letöltése, előtt ismernie kell az Azure storage-fiók tárfióknév és fiókkulcs.

* Azure-előfizetés beállításával kapcsolatban lásd: [ingyenes egy hónapos próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* Storage-fiók létrehozásával és az első fiók és a kulcsadatokat: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

