---
title: Python használata Azure Data Lake Analytics kezelése
description: Ez a cikk ismerteti, hogyan használhatja a Pythont Data Lake Analytics-fiókok, adatforrások, felhasználók & feladatok kezelésére.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: f73ef118efbdfc94d8cb9b7d81717bd13511c785
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048282"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Python használata Azure Data Lake Analytics kezelése
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti az Azure Data Lake Analytics-fiókok, adatforrások, felhasználók és feladatok kezelése Python használatával.

## <a name="supported-python-versions"></a>Támogatott Python-verziók

* Egy Python 64 bites verzióját használja.
* Használhatja a Python elosztási címen található standard  **[Python.org letölti](https://www.python.org/downloads/)**. 
* Sok fejlesztő található is használhatók a  **[Anaconda Python elosztási](https://www.anaconda.com/download/)**.  
* Ez a cikk a Python 3.6-os verziója a normál terjesztési Python használatával készült

## <a name="install-azure-python-sdk"></a>Az Azure Python SDK telepítése

A következő modulok telepítéséhez:

* A **azure-mgmt-resource** modul egyéb Azure-modulokat tartalmaz az Active Directory és más eszközökhöz.
* A **azure-datalake-store** modul az Azure Data Lake Store fájlrendszer-műveleteit tartalmazza. 
* A **azure-mgmt-datalake-store** modul az Azure Data Lake Store fiókkezelési műveleteit tartalmazza.
* A **azure-mgmt-datalake-analytics** modul az Azure Data Lake Analytics-műveleteit tartalmazza. 

Először is győződjön meg arról, hogy a legújabb `pip` a következő parancs futtatásával:

```
python -m pip install --upgrade pip
```

Ez a dokumentum használatával készült `pip version 9.0.1`.

Használja a következő `pip` parancsok a modulok telepítése a parancssor:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Hozzon létre egy új Python-szkript

Illessze be a parancsfájlt a következő kódot:

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Futtassa ezt a szkriptet, ellenőrizze, hogy a modulok importálhatók.

## <a name="authentication"></a>Hitelesítés

### <a name="interactive-user-authentication-with-a-pop-up"></a>Egy előugró ablak az interaktív felhasználói hitelesítéssel

Ez a metódus nem támogatott.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktív felhasználói hitelesítéssel rendelkező eszköz

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Nem interaktív hitelesítéssel ÜTI vagy titkos kulcs

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Az API-t és a egy tanúsítványt a nem interaktív hitelesítés

Ez a metódus nem támogatott.

## <a name="common-script-variables"></a>Parancsfájl-változókat

Ezeket a változókat a mintákat használják.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Az ügyfelek létrehozása

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

Először hozzon létre egy store-fiókot.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Ezután hozzon létre egy ADLA-fiókot, amely ezt a tárolót használja.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Feladat elküldése

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Várjon, amíg a feladat befejezése

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Lista folyamatok és ismétlődések
Attól függően, hogy a feladatok rendelkeznek-e a csatlakoztatott folyamat vagy ismétlődési metaadatok, listázhatja a folyamatok és ismétlődések.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Compute-szabályzatok kezelése

A DataLakeAnalyticsAccountManagementClient objektum tartalmazza a számítási házirendek a Data Lake Analytics-fiók kezelése módszerei.

### <a name="list-compute-policies"></a>Compute-házirendek felsorolása

A következő kód lekéri a Data Lake Analytics-fiók számítási szabályzatok listája.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Új számítási szabályzat létrehozása

Az alábbi kód létrehoz egy Data Lake Analytics-fiók, a megadott felhasználó 50-re, és a minimális feladat prioritása a 250 beállítást a maximális au-k rendelkezésre álló számítási egy új szabályzatot.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>További lépések

- Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
- A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
- Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).

