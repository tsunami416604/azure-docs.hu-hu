---
title: Az Azure Machine Learning számítási feladatokat futtatni, hogy automatizált a machine learning (AutoML) az Apache Spark on Azure HDInsight
description: Ismerje meg, hogyan futtathat az Azure Machine Learning számítási feladatok automatizált a machine learning (AutoML) az Apache Spark on Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917036"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Az Azure Machine Learning számítási feladatokat futtatni, hogy automatizált a machine learning (AutoML) az Apache Spark on Azure HDInsight

Az Azure Machine Learning leegyszerűsíti és felgyorsítja a létrehozását, a képzés és a machine learning-modellek üzembe. A Machine learning (AutoML) automatizált betanítási adatok, amelyek egy meghatározott célhoz funkció kezdődhet, és majd iterálódnak algoritmusok és a szolgáltatások kiválasztása ki automatikusan a legoptimálisabb modellt, a képzési pontszámok alapján az adatok kombinációját. HDInsight lehetővé teszi, hogy az ügyfelek számára, hogy több száz csomópont-fürtök kiépítése. A Spark HDInsight-fürtben futó AutoML lehetővé teszi a felhasználóknak betanítási feladatok futtatása a kibővíthető módon, és párhuzamosan futnak több betanítási feladat a számítási kapacitást ezek a csomópontok között. Ez lehetővé teszi a felhasználók AutoML kísérletek során a számítási osztanak meg más big data számítási feladataik végrehajtásához.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Telepítse az Azure Machine Learning egy HDInsight-fürtön

Automatizált gépi tanulás általános oktatóanyagokban talál [oktatóanyag: Automatizált a gépi tanulási a regressziós modell létrehozásához](../../machine-learning/service/tutorial-auto-train-models.md).
Minden új HDInsight-alapú Spark-fürtök előre telepített AzureML-AutoML SDK-val rendelkeznek. Megkezdheti az AutoML a HDInsight a [minta Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). A Jupyter Notebook használata egy automatizált machine learning-osztályozó egy egyszerű osztályozási probléma mutatja be.

> [!Note]
> Az Azure Machine Learning-csomagok települnek Python3 conda-környezetbe. A telepített Jupyter notebookot a PySpark3 kernel használatával kell futtatni.

Zeppelin notebookok használatával AutoML is használhatja azt is megteheti.

> [!Note]
> A Zeppelin rendelkezik egy [ismert hiba](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) ahol PySpark3 nem válasszon a Python megfelelő verzióját. A dokumentált megkerülő használja.

## <a name="authentication-for-workspace"></a>Hitelesítés a munkaterülethez

Munkaterület létrehozása és a kísérlet elküldése szükséges egy hitelesítési tokent. Ez a token használatával hozható létre egy [Azure AD-alkalmazás](../../active-directory/develop/app-objects-and-service-principals.md). Egy [az Azure AD-felhasználó](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) is segítségével hozza létre a szükséges hitelesítési jogkivonatot, ha a multi-factor authentication szolgáltatás nincs engedélyezve a fiókhoz.  

Az alábbi kódrészlet létrehoz egy hitelesítési tokent a egy **Azure AD-alkalmazás**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
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

Az a [automatizált machine learning configuration](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), a tulajdonság `spark_context` kell beállítani, a csomag elosztott módban fusson. A tulajdonság `concurrent_iterations`, azaz végrehajtható párhuzamosan, az ismétlések maximális számának kell megadni, kisebb a végrehajtó magok, a Spark-alkalmazás.

## <a name="next-steps"></a>További lépések

* A motiváció mögötti automatikus gépi tanulási további információkért lásd: [kiadási modellek lépést használatával a Microsoft machine learning automatikus!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Az Azure Machine Learning automatizált gépi Tanulási képességek használata további részletekért lásd: [új automatizált gépi tanulási funkciók az Azure Machine Learning szolgáltatásban.](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [A Microsoft Research AutoML projekt](https://www.microsoft.com/research/project/automl/)
