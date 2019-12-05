---
title: Azure Machine Learning munkaterhelések futtatása Apache Spark HDInsight
description: Megtudhatja, hogyan futtathat Azure Machine Learning számítási feladatokat automatikus gépi tanulással (AutoML) az Azure HDInsight Apache Spark.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 4e6b648ed70f6ff57a2d11cde43b8168b800fcb3
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806915"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Azure Machine Learning számítási feladatok futtatása automatikus gépi tanulással a HDInsight-ben Apache Spark

Azure Machine Learning leegyszerűsíti és felgyorsítja a gépi tanulási modellek kialakítását, betanítását és üzembe helyezését. Az automatikus gépi tanulás (AutoML) szolgáltatásban olyan betanítási adatmennyiségeket indít el, amelyek egy meghatározott cél funkcióval rendelkeznek, majd megismétlik az algoritmusok és a szolgáltatások kiválasztásával, hogy automatikusan kiválassza a legjobb modellt az adataihoz a képzési pontszámok alapján. A HDInsight lehetővé teszi, hogy az ügyfelek több száz csomóponttal építsenek be fürtöket. A HDInsight-fürtön futó AutoML lehetővé teszi a felhasználók számára, hogy számítási kapacitást használjanak ezen csomópontok között, hogy kibővíthető módon futtassák a betanítási feladatokat, és párhuzamosan futtassanak több betanítási feladatot. Ez lehetővé teszi a felhasználók számára, hogy AutoML kísérleteket futtassanak a számítási feladatok más big data munkaterhelésekkel való megosztása során.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Azure Machine Learning telepítése HDInsight-fürtön

Az automatizált gépi tanulással kapcsolatos általános útmutatókért lásd [: oktatóanyag: automatikus gépi tanulás használata a regressziós modell létrehozásához](../../machine-learning/service/tutorial-auto-train-models.md).
Minden új HDInsight-Spark-fürt előre telepítve van a AzureML-AutoML SDK-val. A AutoML a HDInsight-on való használatával megkezdheti ezt a [minta Jupyter notebookot](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). Ez a Jupyter Notebook bemutatja, hogyan használható egy automatizált gépi tanulási osztályozó egy egyszerű besorolási probléma megoldásához.

> [!Note]
> Azure Machine Learning csomagok települnek a Python3 Conda-környezetbe. A telepített Jupyter-jegyzetfüzetet a PySpark3 kernel használatával kell futtatni.

Azt is megteheti, hogy a Zeppelin jegyzetfüzeteket is használhatja a AutoML használatához.

> [!Note]
> A Zeppelin egy [ismert hibával](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) rendelkezik, ahol a PySpark3 nem választja ki a Python megfelelő verzióját. Kérjük, használja a dokumentált munkát.

## <a name="authentication-for-workspace"></a>A munkaterület hitelesítése

A Munkaterületek létrehozása és a kísérlet elküldése hitelesítési jogkivonatot igényel. Ez a jogkivonat [Azure ad-alkalmazással](../../active-directory/develop/app-objects-and-service-principals.md)hozható létre. Az [Azure ad-felhasználó](/azure/python/python-sdk-azure-authenticate) a szükséges hitelesítési jogkivonat létrehozásához is használható, ha a többtényezős hitelesítés nincs engedélyezve a fiókban.  

A következő kódrészlet létrehoz egy hitelesítési tokent egy **Azure ad-alkalmazás**használatával.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```
Az alábbi kódrészlet egy **Azure ad-felhasználó**használatával hoz létre hitelesítési jogkivonatot.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Adatkészlet betöltése

Az automatizált gépi tanulás a Sparkban a **adatfolyamok**-t használja, amely a lustán kiértékelt, adatokon alapuló, megváltoztathatatlan műveleteket végez.  A adatfolyam betölthetnek egy adatkészletet egy nyilvános olvasási hozzáféréssel rendelkező blobból vagy egy SAS-tokent tartalmazó blob URL-címről.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Az adattárt egyszeri regisztrációval is regisztrálhatja a munkaterületen.

## <a name="experiment-submission"></a>Kísérlet elküldése

Az [automatikus gépi tanulás konfigurációjában](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)a `spark_context` tulajdonságot úgy kell beállítani, hogy a csomag elosztott módban fusson. Az `concurrent_iterations`tulajdonság, amely a párhuzamosan végrehajtott ismétlések maximális számát adja meg, a Spark-alkalmazás végrehajtó magoknál kisebb számra kell állítani.

## <a name="next-steps"></a>Következő lépések

* További információ az automatizált gépi tanulás mögötti motivációról: [modellek kiadása a Microsoft automatizált gépi tanulásával!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Az Azure ML automatizált ML-funkcióival kapcsolatos további információkért lásd: [az új automatizált gépi tanulási képességek Azure Machine learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [AutoML-projekt a Microsoft Research-ből](https://www.microsoft.com/research/project/automl/)
