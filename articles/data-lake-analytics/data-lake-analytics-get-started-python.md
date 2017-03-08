---
title: "Az Azure Data Lake Analytics használatának első lépései a Pythonnal | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja a Pythont Data Lake Store-fiók létrehozásához és feladatok elküldéséhez. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4
ms.lasthandoff: 02/16/2017


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>Oktatóanyag: Az Azure Data Lake Analytics használatának első lépései a Pythonnal
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Ebből a cikkből megtudhatja, hogyan használhatja a Pythont Azure Data Lake Analytics-fiókok létrehozásához, Data Lake Analytics-feladatok definiálásához [U-SQL](data-lake-analytics-u-sql-get-started.md) nyelven, valamint feladatok elküldéséhez Data Lake Analytics-fiókokba. További információk a Data Lake Analyticsről: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).

Az oktatóanyag során elkészít egy feladatot, amely beolvas egy tabulátorral tagolt (TSV) adatfájlt, és azt vesszővel tagolt (CSV) adatfájllá konvertálja. Ha ugyanezt az oktatóanyagot más támogatott eszközök használatával szeretné elvégezni, kattintson a szakasz tetején található fülekre.

##<a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

- **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
- **Egy Azure Active Directory-alkalmazás**. A Data Lake Store alkalmazás Azure AD-val történő hitelesítéséhez az Azure AD alkalmazást kell használni. Az Azure AD-val többféle módon is lehet hitelesítést végezni. Ezek a következők: végfelhasználói hitelesítés vagy szolgáltatások közötti hitelesítés. A hitelesítéssel kapcsolatos útmutatást és további információkat a [Authenticate with Data Lake Store using Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) (Hitelesítés a Data Lake Store-ral az Azure Active Directoryt használva).
- **[Python](https://www.python.org/downloads/)**. A 64 bites verziót kell használnia. Ez a cikk a Python 3.5.2-es verzióját használja.


## <a name="install-azure-python-sdk"></a>Az Azure Python SDK telepítése

A Data Lake Store a Pythonnal való használatához három modult kell telepítenie.

Az azure-mgmt-datalake-store modul az Azure Data Lake Store fiókkezelési-műveleteit tartalmazza. Az azure-mgmt-resource modul egyéb Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz. Az azure-datalake-store modul az Azure Data Lake Store fájlrendszer-műveleteit tartalmazza. Az azure-datalake-analytics modul az Azure Data Lake Analytics-műveleteket tartalmazza. A modulok telepítéséhez használja a következő parancsokat.

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>Python-alkalmazás létrehozása

1. Használja a választott IDE-t egy új Python-alkalmazás, például a mysample.py létrehozásához.

2. Adja hozzá a következő sorokat a szükséges modulok importálásához.

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeAnalyticsAccountProperties, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Mentse a Python-alkalmazásfájl módosításait.

## <a name="authentication"></a>Authentication

Kövesse az alábbi hitelesítési módszerek egyikét:

### <a name="end-user-authentication-for-account-management"></a>Végfelhasználói hitelesítés fiókkezeléshez

Használja ezt a módszert az Azure AD-val való hitelesítésre a fiókkezelési műveleteknél (Data Lake Analytics-fiók létrehozása/törlése stb). Az Azure AD-felhasználók számára meg kell adni egy felhasználónevet és egy jelszót. A felhasználói fiókot nem lehet többtényezős hitelesítésre beállítani, és a fiók nem lehet Microsoft-fiók/Live ID, például @outlook.com, vagy @live.com..

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés a fiókkezeléshez

Használja ezt a módszert az Azure AD-val való hitelesítésre a fiókkezelési műveleteknél (Data Lake Analytics-fiók létrehozása/törlése stb). A következő kódrészlet használható az alkalmazás nem interaktív hitelesítéséhez, az alkalmazás/egyszerű szolgáltatás titkos ügyfélkódjának használatával. Ezt meglévő „webes” Azure AD-alkalmazással használhatja.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>Erőforrás-kezelési csoport létrehozása

Azure-erőforráscsoport létrehozásához használja a következő kódrészletet:

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Data Lake Store-fiók létrehozása

Minden Data Lake Analytics-fiókhoz szükség van egy Data Lake Store-fiókra. Útmutatásért lásd: [Data Lake Store-fiók létrehozása](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).



## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Data Lake Analytics-feladatok küldése

A Data Lake Analytics-feladatok nyelve a U-SQL. További információk a U-SQL-ről: [U-SQL nyelv – első lépések](data-lake-analytics-u-sql-get-started.md) és [U-SQL nyelvi referencia](http://go.microsoft.com/fwlink/?LinkId=691348).

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>Következő lépések

- Ha ugyanezt az oktatóanyagot más eszközök használatával szeretné megtekinteni, kattintson az oldal tetején található lapválasztókra.
- Egy összetettebb lekérdezés megtekintéséhez lásd: [Analyze Website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) (Webhelyek naplóinak elemzése az Azure Data Lake Analytics használatával).
- Ismerkedés a U-SQL-alkalmazások fejlesztésével: [Develop U-SQL scripts using Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) (U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával).
- A U-SQL nyelv megismerése: [Get started with Azure Data Lake Analytics U-SQL language](data-lake-analytics-u-sql-get-started.md) (Ismerkedés az Azure Data Lake Analytics U-SQL nyelvével).
- Felügyeleti feladatok: [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md) (Az Azure Data Lake Analytics kezelése az Azure Portallal).
- A Data Lake Analytics áttekintése: [Azure Data Lake Analytics overview](data-lake-analytics-overview.md) (Az Azure Data Lake Analytics áttekintése).


