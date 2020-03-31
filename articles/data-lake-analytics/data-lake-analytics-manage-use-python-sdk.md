---
title: Az Azure Data Lake Analytics kezelése python használatával
description: Ez a cikk bemutatja, hogyan használhatja a Pythont a Data Lake Analytics-fiókok, adatforrások, felhasználók & feladatok kezelésére.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d40658e1510c9ae8a2e3e1f865df7ac95f61abfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68355983"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Az Azure Data Lake Analytics kezelése python használatával
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti, hogyan kezelheti az Azure Data Lake Analytics-fiókok, adatforrások, felhasználók és feladatok python használatával.

## <a name="supported-python-versions"></a>Támogatott Python-verziók

* A Python 64 bites verzióját használja.
* Használhatja a szabványos Python disztribúció található **[Python.org letöltések](https://www.python.org/downloads/)**. 
* Sok fejlesztő kényelmesnek találja az **[Anaconda Python disztribúció használatát.](https://www.anaconda.com/download/)**  
* Ezt a cikket python 3.6-os verzióval írták a szabványos Python disztribúcióból

## <a name="install-azure-python-sdk"></a>Az Azure Python SDK telepítése

Telepítse a következő modulokat:

* Az **azure-mgmt-erőforrás** modul más Azure-modulokat tartalmaz az Active Directoryhoz, stb.
* Az **azure-datalake-store** modul tartalmazza az Azure Data Lake Store fájlrendszer-műveleteket. 
* Az **azure-mgmt-datalake-store** modul tartalmazza az Azure Data Lake Store fiókkezelési műveleteket.
* Az **azure-mgmt-datalake-analytics** modul tartalmazza az Azure Data Lake Analytics-műveleteket. 

Először győződjön meg `pip` róla, hogy a legújabb a következő parancs futtatásával:

```
python -m pip install --upgrade pip
```

Ezt a dokumentumot a segítségével `pip version 9.0.1`írták.

A modulok `pip` parancssorból történő telepítéséhez használja a következő parancsokat:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Új Python-parancsfájl létrehozása

Illessze be a következő kódot a parancsfájlba:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Futtassa ezt a parancsfájlt annak ellenőrzéséhez, hogy a modulok importálhatók-e.

## <a name="authentication"></a>Hitelesítés

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktív felhasználói hitelesítés előugró ablakkal

Ez a módszer nem támogatott.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktív felhasználói hitelesítés eszközkóddal

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Nem interaktív hitelesítés SPI-vel és titkos

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Nem interaktív hitelesítés API-val és tanúsítvánnyal

Ez a módszer nem támogatott.

## <a name="common-script-variables"></a>Gyakori parancsfájl-változók

Ezeket a változókat használják a mintákban.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Az ügyfelek létrehozása

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

Először hozzon létre egy áruházi fiókot.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Ezután hozzon létre egy ADLA-fiókot, amely az adott tárolót használja.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Feladat beküldése

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

## <a name="wait-for-a-job-to-end"></a>Várjon, amíg a feladat véget ér

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Folyamatok és ismétlődések listázása
Attól függően, hogy a feladatok hoznak-e csővezeték- vagy ismétlődési metaadatokat, listázhatja a folyamatokat és az ismétlődéseket.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Számítási házirendek kezelése

A DataLakeAnalyticsManagementClient objektum módszereket biztosít a Data Lake Analytics-fiók számítási szabályzatainak kezeléséhez.

### <a name="list-compute-policies"></a>Számítási házirendek listázása

A következő kód beolvassa a Data Lake Analytics-fiók számítási szabályzatainak listáját.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Új számítási házirend létrehozása

A következő kód létrehoz egy új számítási szabályzatot a Data Lake Analytics-fiókhoz, a megadott felhasználó számára elérhető maximális Atus-t 50-re, a minimális feladatprioritást pedig 250-re állítva.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>További lépések

- Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
- A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
- Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md).

