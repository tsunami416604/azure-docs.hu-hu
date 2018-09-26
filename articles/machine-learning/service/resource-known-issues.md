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
ms.openlocfilehash: d84040dc440c373ae9bae6dbac7a95109a387ba7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162746"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Ismert problémák és hibaelhárítás az Azure Machine Learning szolgáltatás
 
Ez a cikk segít keresse meg és javítsa ki a hibákat vagy hibákat észlelt, amikor az Azure Machine Learning szolgáltatás használatával. 


## <a name="databricks"></a>Databricks

Databricks és az Azure Machine Learning problémákat.

1. Databricks-fürt javaslat:
   
   Az Azure Databricks-fürt létrehozása a Python 3 v4.x. Azt javasoljuk, hogy a nagy feldolgozási fürt.
 
1. AML SDK databricksen telepítési hiba, amikor további csomagok telepítése.

   Egyes csomagokat, mint például `psutil upgrade libs`, ütközéseket okozhat. Telepítési hibák elkerülése érdekében fagyasztási lib verzió csomagok telepítéséhez. A probléma Databricks kapcsolatos és AML SDK nem kapcsolódik. Példa:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython=2.2.0
   ```

## <a name="gather-diagnostics-information"></a>Diagnosztikai adatainak összegyűjtése
Egyes esetekben hasznos lehet, ha a diagnosztikai adatok segítség kérése során megadhatja. Itt látható, ahol a naplófájlok élő:

## <a name="resource-quotas"></a>Erőforráskvóták

További információ a [erőforráskvóták](how-to-manage-quotas.md) az Azure Machine Learning használata során találkozhat.

## <a name="get-more-support"></a>További támogatás

Küldje el a támogatási kéréseket, és kérjen segítséget a technikai támogatási, fórumok és más. [tudj meg többet...](support-for-aml-services.md)
