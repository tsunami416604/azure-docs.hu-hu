---
title: Ismert problémák és hibaelhárítás
titleSuffix: Azure Machine Learning service
description: Az ismert problémák kerülő listáját és az Azure Machine Learning szolgáltatás hibaelhárítása.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: d7542909df336555e17aea9b0e680879b25dc17f
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791745"
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

## <a name="deployment-failure"></a>Központi telepítési problémái

Ha az erőforrásigények `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, a Termékváltozat módosítása a virtuális gépek, hogy a rendszer több memória áll rendelkezésre a központi telepítésben használja.

## <a name="fpgas"></a>FPGA-k

Nem lesz képes FPGA-kban a modellek üzembe helyezése, amíg nem kérte, és az FPGA kvóta jóvá lett hagyva. Hozzáférés kérése, töltse ki az űrlap kvóta: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatizált gépi tanulás

Tensor Flow automatikus machine learning jelenleg nem támogatja a tensor flow verzió 1.13. Ez telepítése miatt nem működik a csomagfüggőségek. Dolgozunk a probléma megoldásához egy későbbi kiadásban. 


## <a name="databricks"></a>Databricks

Databricks és az Azure Machine Learning problémákat.

### <a name="failure-when-installing-packages"></a>Ha a csomagok telepítése sikertelen

Az Azure databricks szolgáltatásban Azure Machine Learning SDK telepítése sikertelen lesz, ha további csomagok telepítése. Egyes csomagokat, mint például `psutil`, ütközéseket okozhat. Telepítési hibák elkerülése érdekében telepítse csomagok fagy a tár verzióját. A probléma a databricks és az Azure Machine Learning szolgáltatás SDK-t, nem kapcsolódik. A probléma, további kódtárak túl Ön is szembesülhet. Példa:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Azt is megteheti parancsprogramokkal init Ha, tartsa telepítési problémák Python-kódtárakat. Ez a megközelítés hivatalosan nem támogatott. További információkért lásd: [fürthatókörös init parancsfájlok](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Egy automatizált machine learning-Futtatás megszakítása

Automatizált machine learning-funkciók az Azure Databricks használata esetén egy Futtatás megszakítása, és futtatja, új kísérlet indításához indítsa újra az Azure Databricks-fürt.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 ismétlések automatizált machine Learning

Automatizált machine learning beállításait, ha több mint 10 ismétlések állítsa `show_output` való `False` amikor közzétételre küld be a Futtatás.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Az Azure Machine Learning SDK/automatikus gépi tanulási widget

Az Azure Machine Learning SDK widget nem támogatott a Databricks-jegyzetfüzet, mert a notebookok nem tudja értelmezni a HTML-widgetek. A widget a portálon tekintheti meg az Azure Databricks-jegyzetfüzet cella a Python-kód használatával:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Importálási hiba: Nincs "pandas.core.indexes" nevű modul

Ha ezt a hibaüzenetet használatakor automatikus gépi tanulás:

1. Két csomag telepítése az Azure Databricks-fürt a következő parancs futtatásával: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Válassza le, és mellékelje a notebookot a fürt. 

Ha ez nem oldja meg a probléma, indítsa újra a fürtöt.

## <a name="azure-portal"></a>Azure Portal

Ha közvetlenül a munkaterületet egy megosztás hivatkozás az SDK-t vagy a portálon megtekintheti, nem kell az előfizetési adatok normál Áttekintés lapján megtekintheti a bővítmény a. Még nem tud váltani egy másik munkaterületre. Megtekintheti egy másik munkaterülethez van szüksége, a megoldás-e közvetlenül a [az Azure portal](https://portal.azure.com) , és keresse meg a munkaterület nevét.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

Egyes esetekben hasznos lehet, ha a diagnosztikai adatok segítség kérése során megadhatja. Néhány a naplók megtekintéséhez keresse fel [az Azure portal](https://portal.azure.com) , és lépjen a workpsace, és válassza **munkaterület > kísérlet > Futtatás > Naplók**.

## <a name="resource-quotas"></a>Erőforráskvóták

További információ a [erőforráskvóták](how-to-manage-quotas.md) az Azure Machine Learning használata során találkozhat.

## <a name="authentication-errors"></a>Hitelesítési hibák

Ha egy felügyeleti műveletet egy számítási célnak a távoli feladatokat hajt végre, kapni fog a hibák a következők egyikét:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Például egy hibaüzenetet fog kapni, ha megpróbálja hozzon létre vagy csatlakoztasson egy számítási célnak, egy gépi Tanulási folyamatot, amely a távoli végrehajtás céljából elküldésekor.

## <a name="get-more-support"></a>További támogatás

Küldje el a támogatási kéréseket, és kérjen segítséget a technikai támogatási, fórumok és más. [tudj meg többet...](support-for-aml-services.md)
