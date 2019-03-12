---
title: Az Azure Machine Learning számítási feladatokat futtatni, hogy automatizált a machine learning (AutoML) az Apache Spark on Azure HDInsight
description: Ismerje meg, hogyan futtathat az Azure Machine Learning számítási feladatok automatizált a machine learning (AutoML) az Apache Spark on Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 896cae9b7fc43765e340ba3b92351e04b5512efd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762551"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Az Azure Machine Learning számítási feladatokat futtatni, hogy automatizált a machine learning (AutoML) az Apache Spark on Azure HDInsight

Az Azure Machine Learning egy olyan használatával hozhat létre, tesztelheti és üzembe helyezése az adataihoz prediktív elemzési megoldások által biztosított együttműködési környezettel, fogd és vidd eszköz. Az Azure Machine Learning modellek egyszerűen egyéni alkalmazások vagy Üzletiintelligencia-eszközökkel, mint például az Excel által igénybe vehető webszolgáltatásként teszi közzé. Automatizált a machine learning (AutoML) segítségével hozzon létre magas színvonalú machine learning-modellek az intelligens automatizálás és optimalizálás segítségével. AutoML úgy dönt, hogy a megfelelő algoritmus és adott Problématípusok használandó hyper paramétereket.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Telepítse az Azure Machine Learning egy HDInsight-fürtön

> [!Note]
> Az Azure ML-munkaterület érhető el jelenleg a következő régiókban: USA keleti régiója, eastus2 és régiója. A HDInsight-fürt is ezek a régiók egyikében kell létrehozni.

Lásd: általános oktatóanyagok az Azure Machine Learning és az automatizált gépi tanulási és [oktatóanyag: Az első adatelemzési kísérlet létrehozása az Azure Machine Learning Studio](../../machine-learning/studio/create-experiment.md) és [oktatóanyag: Automatizált a gépi tanulási a regressziós modell létrehozásához](../../machine-learning/service/tutorial-auto-train-models.md).
Az AzureML telepíthető az Azure HDInsight-fürtöt, futtatja a parancsfájlművelet - [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) – fő csomópontból és a egy (ajánlott) HDInsight 3.6-os Spark 2.3.0-át fürt munkavégző csomópontjaira. A parancsprogram-művelet futtatható a fürt létrehozását, vagy egy meglévő fürt részeként az Azure Portalon keresztül.

A Parancsfájlműveletek kapcsolatos további információkért olvassa el a [testreszabása Linux-alapú HDInsight-fürtök parancsfájlműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md). Az Azure Machine Learning-csomagok és függőségek telepítése, valamint a parancsfájl is letölt egy mintául szolgáló Jupyter Notebook (útvonalon `HdiNotebooks/PySpark` az alapértelmezett tároló). A Jupyter Notebook használata egy automatizált machine learning-osztályozó egy egyszerű osztályozási probléma mutatja be.

> [!Note]
> Az Azure Machine Learning-csomagok települnek Python3 conda-környezetbe. A telepített Jupyter notebookot a PySpark3 kernel használatával kell futtatni.

## <a name="authentication-for-workspace"></a>Hitelesítés a munkaterülethez

Munkaterület létrehozása és a kísérlet elküldése szükséges egy hitelesítési tokent. Ez a token használatával hozható létre egy [Azure AD-alkalmazás](../../active-directory/develop/app-objects-and-service-principals.md). Egy [az Azure AD-felhasználó](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) is segítségével hozza létre a szükséges hitelesítési jogkivonatot, ha a multi-factor authentication szolgáltatás nincs engedélyezve a fiókhoz.  

Az alábbi kódrészlet létrehoz egy hitelesítési tokent a egy **Azure AD-alkalmazás**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
                )
```
Az alábbi kódrészlet létrehoz egy hitelesítési tokent a egy **az Azure AD-felhasználó**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Adatkészlet betöltése

Gépi tanulás a Spark-felhasználás automatikus **adatfolyamok**, amelyeket adatokon ráérősen kiértékelt, nem módosítható műveletet.  Egy adatfolyam egy adatkészletet betöltheti, nyilvános olvasási hozzáféréssel rendelkező, vagy a blob URL-címet egy SAS-jogkivonat használatával.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Az adattár is regisztrálhatja az személyenként egy regisztrációt a munkaterülethez.

## <a name="experiment-submission"></a>Kísérlet beküldése

Az automatikus machine learning konfigurációjában, a tulajdonság `spark_context` kell beállítani, a csomag elosztott módban fusson. A tulajdonság `concurrent_iterations`, azaz végrehajtható párhuzamosan, az ismétlések maximális számának kell megadni, kisebb a végrehajtó magok, a Spark-alkalmazás.

## <a name="next-steps"></a>További lépések

* A motiváció mögötti automatikus gépi tanulási további információkért lásd: [kiadási modellek lépést használatával a Microsoft machine learning automatikus!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/).
* [A Microsoft Research AutoML projekt](https://www.microsoft.com/research/project/automl/)