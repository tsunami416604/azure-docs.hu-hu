---
title: Adatok áthelyezése az Azure Blob-tárhelyre és az Azure Blob-tárhelyre – Csapatadat-elemzési folyamat
description: Adatok áthelyezése az Azure Blob storage-ba és onnan az Azure Storage Explorer, az AzCopy, Python és SSIS használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717573"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Adatok áthelyezése az Azure Blob tárhelyre és az Azure Blob tárhelyéről

A csapat adatelemzési folyamat megköveteli, hogy az adatok at kell betölteni, vagy betölteni a különböző tárolási környezetekben kell feldolgozni vagy elemezni a legmegfelelőbb módon a folyamat minden szakaszában.

## <a name="different-technologies-for-moving-data"></a>Különböző technológiák az adatok mozgatásához

Az alábbi cikkek ismertetik, hogyan helyezheti át az adatokat, és az Azure Blob storage különböző technológiák használatával.

* [Azure Storage-Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)
* [Python](move-data-to-azure-blob-using-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Melyik módszer a legjobb az Ön számára attól függ, hogy a forgatókönyv. Az [Azure Machine Learning-környezetben a speciális elemzésekhez készült forgatókönyvek](plan-sample-scenarios.md) segítségével meghatározhatja a speciális elemzési folyamatsorán használt adatelemzési munkafolyamatokhoz szükséges erőforrásokat.

> [!NOTE]
> Az Azure blob storage teljes bemutatását az Azure Blob Alapjai és az Azure Blob Service című dokumentumban [tájékformulmában](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) [tájékot](https://msdn.microsoft.com/library/azure/dd179376.aspx)yajzat.
> 
> 

## <a name="using-azure-data-factory"></a>Az Azure Data Factory használata

Alternatív megoldásként az [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) segítségével: 

* olyan folyamat létrehozása és ütemezése, amely adatokat tölt le az Azure blob storage-ból, 
* adja át egy közzétett Azure Machine Learning webszolgáltatásnak, 
* megkapja a prediktív elemzési eredményeket, és 
* feltölteni az eredményeket a tárolóba. 

További információ: [Prediktív folyamatok létrehozása az Azure Data Factory és az Azure Machine Learning használatával című témakörben.](../../data-factory/transform-data-using-machine-learning.md)

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik egy Azure-előfizetéssel, egy tárfiókkal és a fiók megfelelő tárkulcsával. Az adatok feltöltése/letöltése előtt ismernie kell az Azure Storage-fiók nevét és a fiókkulcsot.

* Azure-előfizetés beállításáról az [Ingyenes egyhónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* A tárfiók létrehozásával, valamint a fiók- és kulcsinformációk beszerzésével kapcsolatos tudnivalókat [az Azure Storage-fiókok – tudnivalók.](../../storage/common/storage-create-storage-account.md)

