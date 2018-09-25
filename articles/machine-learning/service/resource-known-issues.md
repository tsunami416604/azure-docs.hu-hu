---
title: Ismert problémák és hibaelhárítás az Azure Machine Learning szolgáltatás
description: Az ismert problémák kerülő listáját, és hibaelhárítás
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 27e73bc75c5f04190bad3dab49c1d46782982a18
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034138"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Ismert problémák és hibaelhárítás az Azure Machine Learning szolgáltatás
 
Ez a cikk segít keresse meg és javítsa ki a hibákat vagy hibákat észlelt, amikor az Azure Machine Learning szolgáltatás használatával. 


## <a name="databricks-and-azure-machine-learning"></a>Databricks és az Azure Machine Learning

**Databricks-fürt javaslat:** 

Az Azure Databricks-fürt létrehozása a Python 3 v4.x. Azt javasoljuk, hogy a nagy feldolgozási fürt.
 
**AML SDK databricksen telepítési hiba, ha további csomagok telepítése** néhány a csomagokat, mint például `psutil upgrade libs`, ütközéseket okozhat. Telepítési hibák elkerülése érdekében fagyasztási lib verzió csomagok telepítéséhez. A probléma Databricks kapcsolatos és AML SDK nem kapcsolódik. Példa:
```
pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
```


## <a name="gather-diagnostics-information"></a>Diagnosztikai adatainak összegyűjtése
Egyes esetekben hasznos lehet, ha a diagnosztikai adatok segítség kérése során megadhatja. Itt látható, ahol a naplófájlok élő:

## <a name="resource-quotas"></a>Erőforráskvóták

További információ a [erőforráskvóták](how-to-manage-quotas.md) az Azure Machine Learning használata során találkozhat.

## <a name="get-more-support"></a>További támogatás

Küldje el a támogatási kéréseket, és kérjen segítséget a technikai támogatási, fórumok és más. [tudj meg többet...](support-for-aml-services.md)
