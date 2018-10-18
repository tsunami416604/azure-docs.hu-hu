---
title: Adatok áthelyezése Azure Blob Storage szolgáltatásba vagy onnan |} A Microsoft Docs
description: Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 717fdd2053cae28234458e197f8211ef25cf7f9d
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394391"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Adatok importálására és az Azure Blob Storage-ból
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

A forgatókönyvtől függ, hogy melyik módszer a legjobb az Ön számára. A [az Azure Machine Learning speciális elemzési forgatókönyvek](plan-sample-scenarios.md) a cikknek a segítségével meghatározhatja, hogy a fejlett analitikai folyamat során használt adatok adatelemzési munkafolyamatainak különböző a szükséges erőforrásokat.

> [!NOTE]
> Egy teljes körű Bevezetés az Azure blob storage, tekintse meg a [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob Service-ben](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Alternatív megoldásként használható [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) való: 

* Hozzon létre és ütemezhet egy folyamatot, amely adatokat tölt le az Azure blob storage-ban 
* Adja át azt egy közzétett Azure Machine Learning webszolgáltatás 
* a prediktív elemzési eredményeket és 
* Töltse fel az eredmények tárolása. 

További információkért lásd: [hozhatók létre az Azure Data Factory és az Azure Machine Learning prediktív adatcsatornák](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Előfeltételek
Jelen dokumentum céljából feltételezzük, hogy rendelkezik Azure-előfizetéssel, egy storage-fiókot és a kapcsolódó tárfiók-kulcsot az adott fiók. Adatok feltöltése/letöltése, előtt ismernie kell az Azure storage-fiók tárfióknév és fiókkulcs.

* Azure-előfizetés beállításával kapcsolatban lásd: [ingyenes egy hónapos próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* Storage-fiók létrehozásával és az első fiók és a kulcsadatokat: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

