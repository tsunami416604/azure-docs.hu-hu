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
ms.openlocfilehash: 02cee5a3e088c919ec94aee6f46ef6f428b9bb48
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249417"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Ismert problémák és hibaelhárítás az Azure Machine Learning szolgáltatás
 
Ez a cikk segít keresse meg és javítsa ki a hibákat vagy hibákat észlelt, amikor az Azure Machine Learning szolgáltatás használatával. 

## <a name="sdk-installation-issues"></a>SDK telepítésével kapcsolatos problémák

**Hibaüzenet: "PyYAML" nem lehet eltávolítani.** 

PyYAML érhető el distutils telepített. Ezért azt nem tudja pontosan meghatározni mely fájlok tartoznak, egy részleges eltávolítás esetén. Az SDK telepítése során a rendszer figyelmen kívül hagyja ezt a hibát a folytatáshoz használja:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

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

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Egyes esetekben hasznos lehet, ha a diagnosztikai adatok segítség kérése során megadhatja. Itt látható, ahol a naplófájlok élő:

## <a name="resource-quotas"></a>Erőforráskvóták

További információ a [erőforráskvóták](how-to-manage-quotas.md) az Azure Machine Learning használata során találkozhat.

## <a name="get-more-support"></a>További támogatás

Küldje el a támogatási kéréseket, és kérjen segítséget a technikai támogatási, fórumok és más. [tudj meg többet...](support-for-aml-services.md)
