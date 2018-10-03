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
ms.date: 10/01/2018
ms.openlocfilehash: d4910eb3dfacb46efe3f85aea3a441bdaaeb1392
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236412"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Ismert problémák és hibaelhárítás az Azure Machine Learning szolgáltatás
 
Ez a cikk segít keresse meg és javítsa ki a hibákat vagy hibákat észlelt, amikor az Azure Machine Learning szolgáltatás használatával. 

## <a name="image-building-failure"></a>Lemezkép létrehozása sikertelen

Kép készítése hiba a webszolgáltatás üzembe helyezésekor. Megkerülő megoldás az, hogy hozzáadása "pynacl 1.2.1-es ==" Conda-fájlba a rendszerkép-konfiguráció pip függőségként.  

## <a name="pipelines"></a>Folyamatok
Fordul elő hiba történt a PythonScriptStep többször is feldolgozza a egy sort a szkript vagy a paraméterek módosítása nélkül. Megkerülő megoldás, hogy építse újra a PipelineData objektum.

## <a name="fpgas"></a>FPGA-kban
Nem lesz képes FPGA-kban a modellek üzembe helyezése, amíg nem kérte, és az FPGA kvóta jóvá lett hagyva. Hozzáférés kérése, töltse ki az űrlap kvóta: https://aka.ms/aml-real-time-ai

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
