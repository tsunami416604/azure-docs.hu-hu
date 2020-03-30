---
title: Azure Machine Learning-számítási feladatok futtatása az Apache Sparkon a HDInsightban
description: Ismerje meg, hogyan futtathat Azure Machine Learning-számítási feladatokat automatizált gépi tanulással (AutoML) az Apache Sparkon az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638884"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Azure Machine Learning-számítási feladatok futtatása automatizált gépi tanulással az Apache Sparkon a HDInsightban

Az Azure Machine Learning leegyszerűsíti és felgyorsítja a gépi tanulási modellek készítését, betanítását és üzembe helyezését. Az automatikus gépi tanulás (AutoML) kezdődik betanítási adatok, amelyek egy meghatározott cél funkcióval rendelkezik, majd végighalad nak algoritmusok és szolgáltatásválasztások segítségével automatikusan válassza ki a legjobb modellaz adatok alapján a betanítási pontszámok. A HDInsight lehetővé teszi az ügyfelek számára, hogy fürtöket létesítsenek több száz csomós csomópontokkal. A Sparkon egy HDInsight-fürtön futó AutoML lehetővé teszi a felhasználók számára, hogy ezeken a csomópontokon számítási kapacitást használjanak a betanítási feladatok horizontális felskálázási módon történő futtatásához, és párhuzamosan több betanítási feladatot futtassanak. Ez lehetővé teszi a felhasználók számára, hogy AutoML-kísérleteket futtassanak, miközben megosztják a számítást a többi big data-munkaterheléssel.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Az Azure Machine Learning telepítése HDInsight-fürtre

Az automatizált gépi tanulás általános oktatóanyagai az [Oktatóanyag: Automatikus gépi tanulás használata a regressziós modell létrehozásához.](../../machine-learning/tutorial-auto-train-models.md)
Minden új HDInsight-Spark-fürt előre telepítve van az AzureML-AutoML SDK-val.

> [!Note]
> Az Azure Machine Learning-csomagok python3 conda környezetbe vannak telepítve. A telepített Jupyter notebook ot a PySpark3 kernel használatával kell futtatni.

A Zeppelin notebookok segítségével autoML-t is használhat.

> [!Note]
> A Zeppelinnek van egy [ismert problémája,](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) ahol a PySpark3 nem a Python megfelelő verzióját választja. Kérjük, használja a dokumentált kerülő összekerülést.

## <a name="authentication-for-workspace"></a>Munkaterület hitelesítése

A munkaterület létrehozása és a kísérletek beküldése hitelesítési jogkivonatot igényel. Ez a jogkivonat azure [AD-alkalmazás](../../active-directory/develop/app-objects-and-service-principals.md)használatával is létrehozható. Az [Azure AD-felhasználók](/azure/python/python-sdk-azure-authenticate) is használható a szükséges hitelesítési jogkivonat létrehozásához, ha a többtényezős hitelesítés nincs engedélyezve a fiókban.  

A következő kódrészlet egy **azure-beli AD-alkalmazás**használatával hoz létre hitelesítési jogkivonatot.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

A következő kódrészlet létrehoz egy hitelesítési jogkivonatot egy **Azure AD-felhasználó**használatával.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Adatkészlet betöltése

A Spark automatikus gépi **tanulása adatfolyamokat**használ, amelyek lustán kiértékelhető, adatokon nem módosítható műveleteket használnak.  Az adatfolyam betölthetegy adatkészletet egy nyilvános olvasási hozzáféréssel rendelkező blobból, vagy egy SAS-jogkivonattal rendelkező blob URL-címből.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Az adattarándadat regisztrálható a munkaterülettel egy egyszeri regisztráció használatával.

## <a name="experiment-submission"></a>Kísérlet beküldése

Az [automatikus gépi tanulási konfigurációban](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)a tulajdonságot `spark_context` be kell állítani a csomag elosztott módban való futtatásához. A `concurrent_iterations`tulajdonságot , amely a párhuzamosan végrehajtott ismétlések maximális száma, a Spark-alkalmazás végrehajtómagjainál kisebb számra kell állítani.

## <a name="next-steps"></a>További lépések

* Az automatizált gépi tanulás motivációjáról további információt a [Modellek kiadása a Microsoft automatizált gépi tanulásával című témakörben talál!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Az Azure ML automatikus ml-es képességeinek használatával kapcsolatos további információkért olvassa el [az Új automatizált gépi tanulási képességek az Azure Machine Learningben című témakört.](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [AutoML-projekt a Microsoft Research től](https://www.microsoft.com/research/project/automl/)
