---
title: Ismert problémák és hibaelhárítás
titleSuffix: Azure Machine Learning service
description: Az ismert problémák kerülő listáját és az Azure Machine Learning szolgáltatás hibaelhárítása.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 25131fdbc7a3633bf4ba9af05fdff9163f41f26b
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265107"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Ismert problémák és hibaelhárítás az Azure Machine Learning szolgáltatás
 
Ez a cikk segít keresse meg és javítsa ki a hibákat vagy hibákat észlelt, amikor az Azure Machine Learning szolgáltatás használatával. 

## <a name="sdk-installation-issues"></a>SDK telepítésével kapcsolatos problémák

**Hibaüzenet: Nem lehet eltávolítani a "PyYAML"** 

Az Azure Machine Learning SDK Pythonhoz készült: PyYAML érhető el distutils telepített. Ezért azt nem tudja pontosan meghatározni mely fájlok tartoznak, egy részleges eltávolítás esetén. Az SDK telepítése során a rendszer figyelmen kívül hagyja ezt a hibát a folytatáshoz használja:
```Python 
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Hiba történt az Azure Machine Learning Compute létrehozása
Nincs ritka előfordulhat, hogy néhány az általánosan elérhető kiadás előtt az Azure Portalról az Azure Machine Learning-munkaterületet létrehozó felhasználó nem feltétlenül tudja hozni az Azure Machine Learning Compute munkaterület. Emelje egy támogatási kérést a megfelelő szolgáltatás, vagy hozzon létre egy új munkaterületet a Portalon vagy az SDK azonnal feloldásának saját magának. 

## <a name="image-building-failure"></a>Lemezkép létrehozása sikertelen

Kép készítése hiba a webszolgáltatás üzembe helyezésekor. Megkerülő megoldás az, hogy hozzáadása "pynacl 1.2.1-es ==" Conda-fájlba a rendszerkép-konfiguráció pip függőségként.  

## <a name="fpgas"></a>FPGA-kban
Nem lesz képes FPGA-kban a modellek üzembe helyezése, amíg nem kérte, és az FPGA kvóta jóvá lett hagyva. Hozzáférés kérése, töltse ki az űrlap kvóta: https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Databricks és az Azure Machine Learning problémákat.

1. Databricks-fürt javaslat:
   
   Az Azure Databricks-fürt létrehozása a Python 3 v4.x. Azt javasoljuk, hogy a nagy feldolgozási fürt.
 
2. AML SDK databricksen telepítési hiba, amikor további csomagok telepítése.

   Egyes csomagokat, mint például `psutil`, ütközéseket okozhat. Telepítési hibák elkerülése érdekében fagyasztási lib verzió csomagok telepítéséhez. A probléma Databricks kapcsolatos, és nem kapcsolódik az Azure Machine Learning-SDK - fellépő, a többi libs túl. Példa:
   ```python
   pstuil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
   ```
   Azt is megteheti parancsprogramokkal init Ha, tartsa install Python libs kapcsolatos problémák. Ez a módszer nem hivatalosan támogatott megközelítést. Olvassa el [feljegyzett](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

3. Machine Learning automatizált és használatánál Databricks, ha lát `Import error: numpy.core.multiarray failed to import`

   Megkerülő megoldás: a Python-kódtár importálása `numpy==1.14.5` a databricks fürt használatával hozzon létre egy könyvtárat [telepítse, és csatolja](https://docs.databricks.com/user-guide/libraries.html#create-a-library).

## <a name="azure-portal"></a>Azure Portal
Ha közvetlenül a munkaterületet egy megosztás hivatkozás az SDK-t vagy a portálon megtekintheti, nem kell az előfizetési adatok normál Áttekintés lapján megtekintheti a bővítmény a. Még nem tud váltani egy másik munkaterületre. Megtekintheti egy másik munkaterülethez van szüksége, a megoldás-e közvetlenül a [az Azure portal](https://portal.azure.com) , és keresse meg a munkaterület nevét.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók
Egyes esetekben hasznos lehet, ha a diagnosztikai adatok segítség kérése során megadhatja. Itt látható, ahol a naplófájlok élő:

## <a name="resource-quotas"></a>Erőforráskvóták

További információ a [erőforráskvóták](how-to-manage-quotas.md) az Azure Machine Learning használata során találkozhat.

## <a name="get-more-support"></a>További támogatás

Küldje el a támogatási kéréseket, és kérjen segítséget a technikai támogatási, fórumok és más. [tudj meg többet...](support-for-aml-services.md)
