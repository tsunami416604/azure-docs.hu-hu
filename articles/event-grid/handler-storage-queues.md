---
title: Tárolási várólista Azure Event Grid eseményekhez tartozó eseménykezelőként
description: Leírja, hogyan használhatja az Azure Storage-várólistákat Azure Event Grid eseményekhez tartozó eseménykezelőként.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598541"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Tárolási várólista Azure Event Grid eseményekhez tartozó eseménykezelőként
Az eseménykezelő az a hely, ahol az esemény elküldése történik. A kezelő további műveletet hajt végre az esemény feldolgozásához. Számos Azure-szolgáltatás automatikusan van konfigurálva az események kezelésére, és az **azure Queue Storage** az egyik. 

**Queue Storage** használatával fogadhat olyan eseményeket, amelyeket le kell húzni. A várólista-tárolót akkor használhatja, ha olyan hosszú ideig futó folyamattal rendelkezik, amely túl sokáig tart a válaszadáshoz. Ha eseményeket küld az üzenetsor-tárolóba, az alkalmazás lekérheti és feldolgozhatja az eseményeket a saját ütemtervén.

## <a name="tutorials"></a>Oktatóanyagok
Tekintse meg a következő oktatóanyagot, amely példát mutat be a várólista-tárolás eseménykezelőként való használatára. 

|Cím  |Leírás  |
|---------|---------|
| [Gyors útmutató: egyéni események irányítása az Azure üzenetsor-tárolóba az Azure CLI-vel és a Event Grid](custom-event-to-queue-storage.md) | Útmutató egyéni események üzenetsor-tárolóba való küldéséhez. |

## <a name="next-steps"></a>További lépések
A támogatott eseménykezelők listáját az [eseménykezelők](event-handlers.md) című cikkben tekintheti meg. 
