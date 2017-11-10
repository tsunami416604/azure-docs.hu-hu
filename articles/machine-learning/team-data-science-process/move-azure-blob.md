---
title: "Adatok áthelyezése az és az Azure Blob Storage |} Microsoft Docs"
description: "Adatok áthelyezése Azure Blob Storage-tárolóba vagy onnan máshová"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d6681e30-ab45-45ea-a9fb-ac8acefe544d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev;sachouks
ms.openlocfilehash: f282705b6d5d1f6867ea87737f19b5550054789a
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Adatok áthelyezése, és az Azure-Blobtárolóba
[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

<!-- just in case, adding this to separate these two include references -->

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Az ideális módszer attól függ, hogy a forgatókönyv. A [forgatókönyvek az Azure Machine Learning speciális elemzésekre](plan-sample-scenarios.md) cikk segít meghatározni, a különböző adatok tudományos munkafolyamatokat a speciális elemzés folyamat használja a szükséges erőforrások.

> [!NOTE]
> Az Azure blob storage teljes bevezetéséhez hivatkozik [Azure Blob alapjai](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) és [Azure Blob szolgáltatás](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

Alternatív megoldásként használható [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) számára: 

* Hozzon létre, és egy folyamatot, amely letölti az adatokat az Azure blob storage ütemezése 
* Adja át azt egy közzétett Azure Machine Learning webszolgáltatás 
* a prediktív elemzési eredményeket, és 
* Töltse fel az eredményeket a tároló. 

További információkért lásd: [létrehozása az Azure Data Factory és az Azure Machine Learning a prediktív folyamatok](../../data-factory/v1/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Előfeltételek
Jelen dokumentum céljából feltételezzük, hogy az Azure-előfizetéssel, a tárfiók és a megfelelő kulcsot adott fiók rendelkezik. Adatok feltöltése/letöltése, előtt ismernie kell az Azure storage-fiók nevét és a fiók kulcs.

* Állítsa be Azure-előfizetéssel, lásd: [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* A storage-fiók létrehozásával és az első fiók és a fontos információkat lásd: [tudnivalók az Azure storage-fiókok](../../storage/common/storage-create-storage-account.md).

