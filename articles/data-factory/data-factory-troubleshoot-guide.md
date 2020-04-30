---
title: A Azure Data Factory hibáinak megoldása | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory külső vezérlési tevékenységeit.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: af0c95c197a70291e0fc863f3256be0e9a1da7f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133211"
---
# <a name="troubleshoot-azure-data-factory"></a>Hibakeresés Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory külső ellenőrzési tevékenységeinek gyakori hibaelhárítási módszereit vizsgálja.

## <a name="connector-and-copy-activity"></a>Összekötő és másolási tevékenység

Összekötői problémák esetén, például a másolási tevékenység használatával kapcsolatos hiba esetén tekintse át az [Azure Data Factory összekötők hibaelhárítása](connector-troubleshoot-guide.md)című témakört.
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Hibakód: 3200

- **Üzenet**: 403-es hiba.

- **OK**:`The Databricks access token has expired.`

- **Javaslat**: alapértelmezés szerint a Azure Databricks hozzáférési jogkivonat 90 napig érvényes. Hozzon létre egy új jogkivonatot, és frissítse a társított szolgáltatást.


### <a name="error-code--3201"></a>Hibakód: 3201

- **Üzenet**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **OK**:`Bad authoring: Notebook path not specified correctly.`

- **Javaslat**: a jegyzetfüzet elérési útjának megadása a Databricks tevékenységben.

<br/>  

- **Üzenet**:`Cluster... does not exist.`

- **OK**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Javaslat**: Ellenőrizze, hogy létezik-e a Databricks-fürt.

<br/>  

- **Üzenet**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **OK**:`Bad authoring.`

- **Javaslat**: adjon meg abszolút elérési utakat a munkaterület-címzési sémák vagy `dbfs:/folder/subfolder/foo.py` a Databricks-fájlrendszerben tárolt fájlok számára.

<br/>  

- **Üzenet**:`{0} LinkedService should have domain and accessToken as required properties.`

- **OK**:`Bad authoring.`

- **Javaslat**: Ellenőrizze a [társított szolgáltatás definícióját](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Üzenet**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **OK**:`Bad authoring.`

- **Javaslat**: Ellenőrizze a [társított szolgáltatás definícióját](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Üzenet**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **OK**:`Bad authoring.`

- **Javaslat**: Tekintse meg a hibaüzenetet.

<br/>

### <a name="error-code--3202"></a>Hibakód: 3202

- **Üzenet**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **OK**:`Too many Databricks runs in an hour.`

- **Javaslat**: a Databricks-munkaterületet használó összes folyamat kiértékelése a feladatok létrehozási arányára.  Ha a folyamatok túl sok Databricks-t futtatnak összesítésben, telepítsen át bizonyos folyamatokat egy új munkaterületre.

<br/>  

- **Üzenet**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **OK**:`Authoring error: No value provided for the parameter.`

- **Javaslat**: vizsgálja meg a folyamat JSON-fájlját, és győződjön meg arról, hogy a baseParameters-jegyzetfüzet összes paramétere nem üres értéket ad meg.

<br/>  

- **Üzenet**: `User: `SimpleUserContext {userId =..., név =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **OK**: a hozzáférési tokent létrehozó felhasználó nem férhet hozzá a társított szolgáltatásban megadott Databricks-fürthöz.

- **Javaslat**: Ellenőrizze, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a munkaterületen.


### <a name="error-code--3203"></a>Hibakód: 3203

- **Üzenet**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **OK**: a fürt meg lett szakítva. Az interaktív fürtök esetében ez lehet a verseny feltétele.

- **Javaslat**: a hiba elkerülésének legjobb módja a feladatok fürtök használata.


### <a name="error-code--3204"></a>Hibakód: 3204

- **Üzenet**:`Job execution failed.`

- **OK**: a hibaüzenetek különböző problémákat jeleznek, például egy váratlan fürt állapotát vagy egy adott tevékenységet. Leggyakrabban egyetlen hibaüzenet sem jelenik meg.

- **Javaslat**: N/A
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

A következő táblázat az U-SQL-re vonatkozik.
      
### <a name="error-code--2709"></a>Hibakód: 2709

- **Üzenet**:`The access token is from the wrong tenant.`

- **OK**: helytelen Azure Active Directory (Azure ad) bérlő.

- **Javaslat**: helytelen Azure Active Directory (Azure ad) bérlő.

<br/>

- **Üzenet**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **OK**: ezt a hibát a Data Lake Analytics szabályozása okozza.

- **Javaslat**: csökkentse Data Lake Analytics elküldött feladatok számát a tevékenységek Data Factory eseményindítók és egyidejűségi beállítások módosításával. Vagy növelje Data Lake Analytics korlátozásait.

<br/>  

- **Üzenet**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **OK**: ezt a hibát a Data Lake Analytics szabályozása okozza.

- **Javaslat**: csökkentse Data Lake Analytics elküldött feladatok számát a tevékenységek Data Factory eseményindítók és egyidejűségi beállítások módosításával. Vagy növelje Data Lake Analytics korlátozásait.


### <a name="error-code--2705"></a>Hibakód: 2705

- **Üzenet**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: gondoskodjon arról, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány hozzáférhessen az Data Lake Analytics fiókhoz és a gyökérmappa alapértelmezett Data Lake Storage példányához.


### <a name="error-code--2711"></a>Hibakód: 2711

- **Üzenet**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: gondoskodjon arról, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány hozzáférhessen az Data Lake Analytics fiókhoz és a gyökérmappa alapértelmezett Data Lake Storage példányához.

<br/>  

- **Üzenet**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **OK**: az U-SQL-fájl elérési útja helytelen, vagy a társított szolgáltatás hitelesítő adatai nem rendelkeznek hozzáféréssel.

- **Javaslat**: Ellenőrizze a társított szolgáltatásban megadott elérési utat és a hitelesítő adatokat.


### <a name="error-code--2704"></a>Hibakód: 2704

- **Üzenet**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: gondoskodjon arról, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány hozzáférhessen az Data Lake Analytics fiókhoz és a gyökérmappa alapértelmezett Data Lake Storage példányához.


### <a name="error-code--2707"></a>Hibakód: 2707

- **Üzenet**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **OK**: a társított szolgáltatás Data Lake Analytics fiókja nem megfelelő.

- **Javaslat**: Ellenőrizze, hogy a megfelelő fiók van-e megadva.


### <a name="error-code--2703"></a>Hibakód: 2703

- **Üzenet**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **OK**: a hiba Data Lake Analytics.

- **Javaslat**: a példában szereplő hiba például azt jelenti, hogy a feladatot elküldték Data Lake Analyticsba, és a parancsfájl sikertelen volt. Data Lake Analytics vizsgálata. A portálon lépjen a Data Lake Analytics fiókra, és keresse meg a feladatot a Data Factory tevékenység futtatási AZONOSÍTÓjának használatával (nem a folyamat futtatási AZONOSÍTÓját). A feladattal kapcsolatban további információ található a hibával kapcsolatban, és segítséget nyújt a hibák megoldásához. Ha a megoldás nem egyértelmű, lépjen kapcsolatba a Data Lake Analytics támogatási csapatával, és adja meg a feladatokhoz tartozó URL-címet, amely tartalmazza a fiók nevét és a AZONOSÍTÓJÚ feladatot.
          

## <a name="azure-functions"></a>Azure-függvények

### <a name="error-code--3602"></a>Hibakód: 3602

- **Üzenet**:`Invalid HttpMethod: '%method;'.`

- **OK**: az Azure Function tevékenység nem támogatja a tevékenységhez tartozó adattartalomban megadott http-metódust.

- **Javaslat**: a támogatott http-metódusok az Put, a post, a Get, a DELETE, a Options, a Head és a trace.


### <a name="error-code--3603"></a>Hibakód: 3603

- **Üzenet**:`Response Content is not a valid JObject.`

- **OK**: a hívott Azure-függvény nem adott vissza JSON-adattartalmat a válaszban. Az ADF Azure Function tevékenység csak a JSON-válaszok tartalmát támogatja.

- **Javaslat**: frissítse az Azure-függvényt egy érvényes JSON-adattartalom visszaküldéséhez, például egy C#-függvény visszatérhet (\"ActionResult\")\"új\"OkObjectResult ("{id: 123}");


### <a name="error-code--3606"></a>Hibakód: 3606

- **Üzenet**: az Azure Function tevékenysége hiányzik a funkcióbillentyű.

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója rendelkezik-e "functionKey" nevű tulajdonsággal.


### <a name="error-code--3607"></a>Hibakód: 3607

- **Üzenet**:`Azure function activity missing function name.`

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója "függvénynév" nevű tulajdonsággal rendelkezik-e.


### <a name="error-code--3608"></a>Hibakód: 3608

- **Üzenet**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **OK**: az Azure-függvény részletei a tevékenység-definícióban helytelenek lehetnek.

- **Javaslat**: javítsa ki az Azure-függvény adatait, és próbálkozzon újra.


### <a name="error-code--3609"></a>Hibakód: 3609

- **Üzenet**:`Azure function activity missing functionAppUrl.`

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója rendelkezik-e "functionAppUrl" nevű tulajdonsággal.


### <a name="error-code--3610"></a>Hibakód: 3610

- **Üzenet**:`There was an error while calling endpoint.`

- **OK**: lehet, hogy a függvény URL-címe helytelen.

- **Javaslat**: Ellenőrizze, hogy helyes-e a "functionAppUrl" érték a tevékenység JSON-fájljában, majd próbálkozzon újra.


### <a name="error-code--3611"></a>Hibakód: 3611

- **Üzenet**:`Azure function activity missing Method in JSON.`

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója "Method" nevű tulajdonsággal rendelkezik-e.


### <a name="error-code--3612"></a>Hibakód: 3612

- **Üzenet**:`Azure function activity missing LinkedService definition in JSON.`

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója rendelkezik-e társított szolgáltatás részleteivel.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Hibakód: 4101

- **Üzenet**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **OK**: helytelen formátumú vagy hiányzó definíció a (z)% propertyName; tulajdonsághoz.

- **Javaslat**: Ellenőrizze, hogy a (z)% activityName; tevékenység megfelelő adatokkal van-e megadva a (z)% propertyName; tulajdonsággal.


### <a name="error-code--4110"></a>Hibakód: 4110

- **Üzenet**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **OK**: a AzureMLExecutePipeline tevékenység definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREMLEXECUTEPIPELINE tevékenység JSON-definíciója rendelkezik-e társított szolgáltatás részleteivel.


### <a name="error-code--4111"></a>Hibakód: 4111

- **Üzenet**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **OK**: helytelen tevékenység-definíció.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREMLEXECUTEPIPELINE tevékenység JSON-definíciója rendelkezik-e megfelelő társított szolgáltatás részleteivel.


### <a name="error-code--4112"></a>Hibakód: 4112

- **Üzenet**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **OK**: helytelen formátumú vagy hiányzó definíció a (z)% propertyName; tulajdonsághoz.

- **Javaslat**: Ellenőrizze, hogy a társított szolgáltatás megfelelő adatokkal van-e megadva a (z)% propertyName; tulajdonsággal.


### <a name="error-code--4121"></a>Hibakód: 4121

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: a Azure Machine learning eléréséhez használt hitelesítő adatok lejártak.

- **Javaslat**: Ellenőrizze a hitelesítő adatok érvényességét, és próbálkozzon újra


### <a name="error-code--4122"></a>Hibakód: 4122

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: Azure Machine learning társított szolgáltatásban megadott hitelesítő adat érvénytelen vagy nem rendelkezik engedéllyel a művelethez.

- **Javaslat**: Ellenőrizze, hogy érvényes-e a hitelesítő adatok a társított szolgáltatásban, és jogosult-e a Azure Machine learning elérésére.


### <a name="error-code--4123"></a>Hibakód: 4123

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: a tevékenység (például a pipelineParameters) tulajdonságai az Azure ml-folyamat esetében érvénytelenek.

- **Javaslat**: Ellenőrizze a tevékenység tulajdonságainak értékét, hogy megegyezzen a társított szolgáltatásban megadott közzétett Azure ml-folyamat várt hasznos adattartalmával.


### <a name="error-code--4124"></a>Hibakód: 4124

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: a közzétett Azure ml-folyamat végpontja nem létezik.

- **Javaslat**: Ellenőrizze, hogy a társított szolgáltatásban megadott közzétett Azure Machine learning folyamat-végpont létezik-e Azure Machine Learningban.


### <a name="error-code--4125"></a>Hibakód: 4125

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: kiszolgálóhiba Azure Machine Learningkor.

- **Javaslat**: próbálkozzon újra később. Ha a probléma továbbra is fennáll, lépjen kapcsolatba Azure Machine Learning csapatával.


### <a name="error-code--4126"></a>Hibakód: 4126

- **Üzenet**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **OK**: az Azure ml-folyamat futtatása nem sikerült.

- **Javaslat**: ellenőrizze a Azure Machine learning a további hibák naplózásához és a ml-folyamat javításához.



## <a name="common"></a>Közös

### <a name="error-code--2103"></a>Hibakód: 2103

- **Üzenet**:`Please provide value for the required property '%propertyName;'.`

- **OK**: a tulajdonság értéke nem lett megadva, azonban a forgatókönyvben kötelező megadni.

- **Javaslat**: adja meg az értéket az üzenetből, és próbálkozzon újra.


### <a name="error-code--2104"></a>Hibakód: 2104

- **Üzenet**:`The type of the property '%propertyName;' is incorrect.`

- **OK**: a megadott tulajdonság típusa nem a várt érték.

- **Javaslat**: javítsa ki a tulajdonság típusát, és próbálkozzon újra.


### <a name="error-code--2105"></a>Hibakód: 2105

- **Üzenet**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **OK**: a tulajdonság értéke érvénytelen, vagy nem rendelkezik a várt formátummal.

- **Javaslat**: keresse meg a tulajdonság dokumentációját, és győződjön meg arról, hogy a megadott érték formátuma és típusa várható.


### <a name="error-code--2106"></a>Hibakód: 2106

- **Üzenet**:`The storage connection string is invalid. %errorMessage;`

- **OK**: a tárolóhoz tartozó kapcsolatok karakterlánca érvénytelen vagy helytelen formátumú.

- **Javaslat**: lépjen a Azure Portalra, keresse meg a tárhelyét, másolja a kapcsolati karakterláncot, és illessze be a társított szolgáltatásba, majd próbálkozzon újra.


### <a name="error-code--2108"></a>Hibakód: 2108

- **Üzenet**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **OK**: a kérelem végrehajtása egy mögöttes probléma miatt meghiúsult, például a hálózati kapcsolat, a DNS-hiba, a kiszolgálói tanúsítvány érvényesítése vagy időtúllépés.

- **Javaslat**: a Hegedűs/Poster használatával ellenőrizze a kérést.


### <a name="error-code--2110"></a>Hibakód: 2110

- **Üzenet**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **OK**: a tevékenységben megadott társított szolgáltatás helytelen.

- **Javaslat**: Ellenőrizze, hogy a társított szolgáltatás típusa a tevékenység által támogatott típusok egyike-e. Például a HDI-tevékenységekhez a társított szolgáltatás típusa lehet HDInsight vagy HDInsightOnDemand.


### <a name="error-code--2111"></a>Hibakód: 2111

- **Üzenet**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **OK**: a megadott tulajdonság típusa nem a várt érték.

- **Javaslat**: javítsa ki a tulajdonság típusát, és próbálkozzon újra.


### <a name="error-code--2112"></a>Hibakód: 2112

- **Üzenet**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **OK**: a felhő típusa nem támogatott, vagy nem határozható meg a tárolóhoz a EndpointSuffix.

- **Javaslat**: használjon egy másik Felhőbeli tárolót, és próbálkozzon újra.


### <a name="error-code--2128"></a>Hibakód: 2128

- **Üzenet**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **OK**: hálózati kapcsolat, DNS-hiba, kiszolgálói tanúsítvány ellenőrzése vagy időtúllépés.

- **Javaslat**: Ellenőrizze, hogy a elérni kívánt végpont válaszol-e a kérelmekre. Használhat olyan eszközöket is, mint a Hegedűs vagy a Poster.



## <a name="custom"></a>Egyéni

A következő táblázat a Azure Batchra vonatkozik.
      
### <a name="error-code--2500"></a>Hibakód: 2500

- **Üzenet**:`Hit unexpected exception and execution failed.`

- **OK**:`Can't launch command, or the program returned an error code.`

- **Javaslat**: Ellenőrizze, hogy létezik-e a végrehajtható fájl. Ha a program elindult, győződjön meg róla, hogy az *StdOut. txt* és a *stderr. txt fájl* fel lett töltve a Storage-fiókba. Érdemes lehet bőséges naplókat kibocsátani a kódban a hibakereséshez.


### <a name="error-code--2501"></a>Hibakód: 2501

- **Üzenet**:`Cannot access user batch account; please check batch account settings.`

- **OK**: helytelen a Batch-hozzáférési kulcs vagy a készlet neve.

- **Javaslat**: Ellenőrizze a készlet nevét és a Batch-elérési kulcsot a társított szolgáltatásban.


### <a name="error-code--2502"></a>Hibakód: 2502

- **Üzenet**:`Cannot access user storage account; please check storage account settings.`

- **OK**: helytelen a Storage-fiók neve vagy elérési kulcsa.

- **Javaslat**: Ellenőrizze a Storage-fiók nevét és a hozzáférési kulcsot a társított szolgáltatásban.


### <a name="error-code--2504"></a>Hibakód: 2504

- **Üzenet**:`Operation returned an invalid status code 'BadRequest'.`

- **OK**: túl sok fájl van az egyéni tevékenység folderPath. A resourceFiles teljes mérete nem lehet hosszabb 32 768 karakternél.

- **Javaslat**: távolítsa el a szükségtelen fájlokat. Vagy zip, és adjon hozzá egy unzip parancsot a kinyeréséhez. Használja például a következőt:`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Hibakód: 2505

- **Üzenet**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **OK**: az egyéni tevékenységek csak a hozzáférési kulcsot használó tárolási fiókokat támogatják.

- **Javaslat**: Tekintse meg a hiba leírását.


### <a name="error-code--2507"></a>Hibakód: 2507

- **Üzenet**:`The folder path does not exist or is empty: ...`

- **OK**: a megadott elérési úton egyetlen fájl sem található a Storage-fiókban.

- **Javaslat**: a mappa elérési útjának tartalmaznia kell a futtatni kívánt végrehajtható fájlokat.


### <a name="error-code--2508"></a>Hibakód: 2508

- **Üzenet**:`There are duplicate files in the resource folder.`

- **OK**: több azonos nevű fájl található a folderPath különböző almappáiban.

- **Javaslat**: az egyéni tevékenységek lelapulják a folderPath a mappa szerkezetét.  Ha meg kell őriznie a mappa szerkezetét, zip-fájlt kell kibontania Azure Batch egy unzip paranccsal. Használja például a következőt:`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Hibakód: 2509

- **Üzenet**:`Batch   url ... is invalid; it must be in Uri format.`

- **OK**: a Batch URL-címeinek hasonlónak kell lenniük`https://mybatchaccount.eastus.batch.azure.com`

- **Javaslat**: Tekintse meg a hiba leírását.


### <a name="error-code--2510"></a>Hibakód: 2510

- **Üzenet**:`An   error occurred while sending the request.`

- **OK**: a Batch URL-címe érvénytelen.

- **Javaslat**: Ellenőrizze a Batch URL-címét.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Hibakód: 200

- **Üzenet**:`Unexpected error happened: '%error;'.`

- **OK**: belső szolgáltatási probléma van.

- **Javaslat**: további segítségért forduljon az ADF-támogatáshoz.


### <a name="error-code--201"></a>Hibakód: 201

- **Üzenet**:`JobType %jobType; is not found.`

- **OK**: Az ADF nem támogatja az új feladattípust.

- **Javaslat**: további segítségért forduljon az ADF támogatási csapatához.


### <a name="error-code--202"></a>Hibakód: 202

- **Üzenet**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **OK**: a hibaüzenetből megtudhatja, hogy mi volt a hiba.

- **Javaslat**: a hibaüzenetnek segítenie kell a probléma megoldásában. Ha nincs elegendő információ, további segítségért forduljon az ADF-támogatáshoz.


### <a name="error-code--203"></a>Hibakód: 203

- **Üzenet**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **OK**: a hibaüzenetből megtudhatja, hogy mi volt a hiba.

- **Javaslat**: a hibaüzenetnek segítenie kell a probléma megoldásában. Ha nincs elegendő információ, további segítségért forduljon az ADF-támogatáshoz.


### <a name="error-code--204"></a>Hibakód: 204

- **Üzenet**:`The resumption token is missing for runId '%runId;'.`

- **OK**: belső szolgáltatási probléma van.

- **Javaslat**: további segítségért forduljon az ADF-támogatáshoz.


### <a name="error-code--205"></a>Hibakód: 205

- **Üzenet**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **OK**: hiba történt a HDI igény szerinti fürt létrehozásakor.

- **Javaslat**: további segítségért forduljon az ADF-támogatáshoz.


### <a name="error-code--206"></a>Hibakód: 206

- **Üzenet**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **OK**: belső probléma történt az ezt kiváltó szolgáltatással.

- **Javaslat**: ez lehet átmeneti probléma. Próbálkozzon újra a feladatokkal, és ha a probléma továbbra is fennáll, további segítségért forduljon az ADF-támogatáshoz.


### <a name="error-code--207"></a>Hibakód: 207

- **Üzenet**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **OK**: belső hiba történt a régió elsődleges Storage-fiókból való megállapítására tett kísérlet során.

- **Javaslat**: próbálkozzon egy másik tárolóval. Ha ez nem elfogadható megoldás, további segítségért forduljon az ADF támogatási csapatához.


### <a name="error-code--208"></a>Hibakód: 208

- **Üzenet**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **OK**: belső hiba történt a szolgáltatásnév beolvasására tett kísérlet során vagy az MSI-hitelesítés példányának létrehozásakor.

- **Javaslat**: érdemes megfontolni egy egyszerű szolgáltatás megadását, amely rendelkezik egy HDInsight-fürt létrehozásához szükséges engedélyekkel a megadott előfizetésben, majd próbálkozzon újra. Győződjön meg arról [, hogy az identitások kezelése helyesen van beállítva](https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities). Ha ez nem elfogadható megoldás, további segítségért forduljon az ADF támogatási csapatához.


### <a name="error-code--2300"></a>Hibakód: 2300

- **Üzenet**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **OK**: Ha a hibaüzenetben a "a távoli név nem oldható fel." üzenet jelenik meg, akkor ez azt jelentheti, hogy a megadott fürt URI-ja érvénytelen.


- **Javaslat**: Győződjön meg arról, hogy a fürt nem lett törölve, és hogy a megadott URI helyes. Amikor megnyitja az URI-t egy böngészőben, megjelenik a Ambari felhasználói felülete. Ha a fürt virtuális hálózatban található, az URI-nak a privát URI-nak kell lennie. A megnyitásához használjon egy azonos virtuális hálózat részét képező virtuális GÉPET. [További információ:.](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)
                  

<br>

- **OK**: Ha a hibaüzenet olyan üzenetet tartalmaz, amely az "a feladat megszakítva" üzenethez hasonlít. Ez azt jelenti, hogy a feladat elküldése túllépte az időkorlátot.

- **Javaslat**: a probléma lehet általános HDInsight-kapcsolat vagy hálózati kapcsolat. Először ellenőrizze, hogy a HDInsight Ambari felhasználói felülete elérhető-e bármely böngészőből. Ellenőrizze, hogy a hitelesítő adatai még érvényesek-e. További információkért olvassa el a [Ambari webes felhasználói felületét](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-manage-ambari#ambari-web-ui). Ha saját üzemeltetésű integrált futtatókörnyezetet (IR) használ, ügyeljen arra, hogy azt a virtuális gépet vagy gépet használja, ahol a saját üzemeltetésű integrációs modul telepítve van. Ezután próbálja meg újra elküldeni a feladatot Data Factory újra. Ha továbbra sem sikerül, forduljon a Data Factory csapatához.

<br>

- **OK**: Ha a hibaüzenet olyan üzenetet tartalmaz, amely a "felhasználói rendszergazda számára ki van zárva a Ambari" vagy a "jogosulatlan: Ambari Felhasználónév vagy jelszó helytelen", ez azt jelenti, hogy a HDInsight hitelesítő adatai helytelenek vagy lejártak.

- **Javaslat**: javítsa ki a hitelesítő adatokat, és telepítse újra a társított szolgáltatást. Először győződjön meg arról, hogy a hitelesítő adatok HDInsight működnek a fürt URI-ja megnyitásával bármely böngészőben, és próbáljon bejelentkezni. Ha a hitelesítő adatok nem működnek, alaphelyzetbe állíthatja azokat a Azure Portal. Az ESP-fürt esetében [az önkiszolgáló jelszó-visszaállítás használatával állíthatja alaphelyzetbe a jelszót](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password).

<br>

- **OK**: Ha a hibaüzenet olyan üzenetet tartalmaz, amely a "502 – webkiszolgáló számára érvénytelen választ kapott az átjáróként vagy proxykiszolgálóként való működés közben", ezt a hibát a HDInsight szolgáltatás adja vissza.

- **Javaslat**: 502-es hiba esetén a legtöbb esetben ez azért van, mert a Ambari-kiszolgáló folyamata le lett állítva. A Ambari-szolgáltatásokat a fő csomópont újraindításával indíthatja újra.  

    1. Kapcsolódjon a Hdinsight egyik csomópontjához az SSH használatával.
    2. Azonosítsa az aktív fő csomópont-gazdagépet a "ping headnodehost" parancs futtatásával.
    3. Csatlakozzon az aktív főcsomóponthoz, mert a Ambari-kiszolgáló az SSH használatával az aktív főcsomóponton található.  
    4. Indítsa újra az aktív fő csomópontot.

        További információ: Tekintse át az Azure HDInsight hibaelhárítási dokumentációját, például: [AMBARI UI 502 hiba](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [a RpcTimeoutException for Apache Spark takarékossági kiszolgáló](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception), a [hibás átjárókkal kapcsolatos hibák elhárítása Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br>

- **OK**: Ha a hibaüzenet olyan üzenetet tartalmaz, amely a "nem tudja kiszolgálni a küldési feladat kérését, mert a Templeton szolgáltatás túl sok beküldési feladathoz vagy" üzenetsor gyökeréhez tartozik. a joblauncher már rendelkezik 500 alkalmazással, nem fogadhatja el az alkalmazás beküldését ", ez azt jelenti, hogy túl sok feladat van elküldve a HDInsight.

- **Javaslat**: érdemes lehet korlátozni a HDInsight küldött egyidejű feladatok számát. Ha ugyanaz a tevékenység küldi el a feladatokat, tekintse meg Data Factory tevékenység egyidejűségét. Módosítsa az eseményindítókat úgy, hogy az egyidejű folyamat-futtatások egyszerre legyenek kiosztva. A hiba feltételeit a `templeton.parallellism.job.submit` [HDInsight dokumentációjában](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors) találja.


### <a name="error-code--2301"></a>Hibakód: 2301

- **Üzenet**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **OK**: a HDInsight-fürt vagy-szolgáltatás problémái vannak.


- **Javaslat**: Ez a hiba akkor fordul elő, ha az ADF nem válaszol a HDInsight-fürttől a futó feladatokhoz tartozó állapot beolvasására tett kísérlet során. Előfordulhat, hogy a fürtön problémák merülhetnek fel, vagy ha a HDInsight szolgáltatás leállás miatt leáll. Tekintse meg a HDInsight hibaelhárítási https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guidedokumentációját, vagy forduljon a támogatási szolgálathoz további segítségért.
                


### <a name="error-code--2302"></a>Hibakód: 2302

- **Üzenet**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **OK**: a feladatot elküldték a HDI-fürtnek, és ott nem sikerült.

- **Javaslat**: kövesse a fonál naplók hivatkozását a tevékenység futtatása kimenetben, és keresse meg a HDI kimenetben előforduló hibákat. Ha szükséges, forduljon a HDInsight csapatához.


### <a name="error-code--2303"></a>Hibakód: 2303

- **Üzenet**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **OK**: a feladatot elküldték a HDI-fürtnek, és ott nem sikerült.

- **Javaslat**: kövesse a fonál naplók hivatkozását a tevékenység futtatása kimenetben, és keresse meg a HDI kimenetben előforduló hibákat. Ha szükséges, próbálkozzon újra, vagy forduljon a HDInsight csapatához.


### <a name="error-code--2304"></a>Hibakód: 2304

- **Üzenet**:`MSI authentication is not supported on storages for HDI activities.`

- **OK**: a HDI társított szolgáltatásban vagy a HDI-tevékenységben használt Storage társított szolgáltatás (ok) MSI-hitelesítéssel vannak konfigurálva, ami nem támogatott.

- **Javaslat**: adjon meg teljes kapcsolati karakterláncokat a HDI társított szolgáltatásban vagy a HDI-tevékenységben használt Storage-fiókok számára.


### <a name="error-code--2305"></a>Hibakód: 2305

- **Üzenet**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **OK**: a HDI-fürthöz tartozó kapcsolati információk helytelenek, a megadott felhasználónak nincs engedélye a szükséges művelet végrehajtására, vagy a HDInsight szolgáltatás az ADF-től érkező kérésekre válaszol.

- **Javaslat**: Ügyeljen arra, hogy a felhasználói adatok helyesek legyenek. Azt is ellenőrizze, hogy a HDI-fürt Ambari felhasználói felülete megnyitható-e egy böngészőben a virtuális gépen, ahol az IR a saját üzemeltetésű integrációs modul esetében van telepítve, vagy ha Azure IR, akkor bármelyik gépről megnyithatja.


### <a name="error-code--2306"></a>Hibakód: 2306

- **Üzenet**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **OK**: a parancsfájl műveletéhez megadott JSON érvénytelen.


- **Javaslat**: a hibaüzenetnek segítséget kell nyújtania a probléma azonosításához. Javítsa ki a JSON-konfigurációt, és próbálkozzon újra. További https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service információkért keresse fel a következőt:.
                

### <a name="error-code--2310"></a>Hibakód: 2310

- **Üzenet**:`Failed to submit Spark job. Error: '%message;'`

- **OK**: Az ADF megpróbált létrehozni egy köteget egy Spark-fürtön a Livy API (Livy/batch) használatával, de hibaüzenetet kapott.

- **Javaslat**: a probléma megoldásához kövesse a hibaüzenetet. Ha nem áll rendelkezésre elegendő információ a megoldás megszerzéséhez, lépjen kapcsolatba az HDI csapatával, és adja meg nekik a Batch-azonosítót és a feladatot, amely a tevékenység futtatása kimenete ADF-figyelés oldalon található.


### <a name="error-code--2312"></a>Hibakód: 2312

- **Üzenet**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **OK**: a HDInsight Spark-fürtön a feladatok sikertelenek voltak.

- **Javaslat**: a HDInsight Spark-fürtön futtatott futtatási hibák megoldásához kövesse a tevékenység futtatása kimenetben az ADF figyelése oldalon található hivatkozásokat. További segítségért forduljon a HDInsight támogatási csapatához.


### <a name="error-code--2313"></a>Hibakód: 2313

- **Üzenet**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **OK**: a köteg törölve lett a HDInsight Spark-fürtön.

- **Javaslat**: kötegek hibakeresése a HDInsight Spark-fürtön. További segítségért forduljon a HDInsight támogatási szolgálatához. 


### <a name="error-code--2328"></a>Hibakód: 2328

- **Üzenet**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **OK**:`The error message should show the details of what went wrong.`

- **Javaslat**: a hibaüzenetnek segítenie kell a probléma megoldásában.


### <a name="error-code--2329"></a>Hibakód: 2329

- **Üzenet**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **OK**: a hibaüzenetből megtudhatja, hogy mi volt a hiba.

- **Javaslat**: a hibaüzenetnek segítenie kell a probléma megoldásában.


### <a name="error-code--2331"></a>Hibakód: 2331

- **Üzenet**:`The file path should not be null or empty.`

- **OK**: a megadott fájl elérési útja üres.

- **Javaslat**: adjon meg egy elérési utat a létező fájlhoz.


### <a name="error-code--2340"></a>Hibakód: 2340

- **Üzenet**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **OK**: a HDInsightOnDemand társított szolgáltatás nem támogatja a végrehajtást a SelfHosted IR-n keresztül.

- **Javaslat**: válasszon ki egy Azure IR, majd próbálkozzon újra.


### <a name="error-code--2341"></a>Hibakód: 2341

- **Üzenet**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **OK**: a megadott URL-cím nem megfelelő formátumú.

- **Javaslat**: javítsa ki a fürt URL-címét, és próbálkozzon újra.


### <a name="error-code--2342"></a>Hibakód: 2342

- **Üzenet**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **OK**: a megadott hitelesítő adatok nem megfelelőek a fürthöz, vagy hálózati konfiguráció vagy kapcsolódási probléma lépett fel, vagy az IR problémákba ütközik a fürthöz való csatlakozás során.

- **Javaslat**:  
      1. Ellenőrizze, hogy a hitelesítő adatok helyesek-e. Ehhez nyissa meg a HDInsight-fürt Ambari felhasználói felületét egy böngészőben.
      2. Ha a fürt VNet és saját üzemeltetésű IR használatban van, a HDI URL-címnek a virtuális hálózatok privát URL-címének kell lennie, ami azt jelenti, hogy a fürt neve után "-int" értékűnek kell lennie. Példáulhttps://mycluster.azurehdinsight.net/a "" értékrehttps://mycluster-int.azurehdinsight.net/kell módosítani a következőt: "".
      2. Ha a fürt VNet található, a rendszer saját üzemeltetésű IR-t használ, és a magánhálózati URL-címet használta, és a kapcsolat továbbra is meghiúsult, akkor az a virtuális gép, amelyen az IR telepítve volt, problémák léptek fel a HDI-hez való csatlakozás során. Kapcsolódjon ahhoz a virtuális géphez, amelyen az IR telepítve van, és nyissa meg a Ambari felhasználói felületét egy böngészőben. Használja a fürt magánhálózati URL-címét. A hálózatnak a böngészőben kell működnie. Ha nem, további segítségért lépjen kapcsolatba a HDInsight támogatási csapatával.
      3. Ha a saját üzemeltetésű integrációs modult nem használja, akkor a HDI-fürtnek nyilvánosan elérhetőnek kell lennie. Nyissa meg a Ambari felhasználói felületét egy böngészőben, és győződjön meg róla, hogy megnyílik. Ha problémák merülnek fel a fürttel vagy a szolgáltatásokkal kapcsolatban, kérjen segítséget a HDInsight támogatási csapattól.
      Így általában az ADF-hez társított szolgáltatásban használt HDI-fürt URL-címének elérhetőnek kell lennie az ADF-hez (helyi vagy Azure), hogy a kapcsolaton keresztüli teszt és a futtatások működjenek. Ezt egyszerűen ellenőrizheti, ha megnyitja az URL-címet egy böngészőből a virtuális gépen vagy bármely nyilvános gépről.
    


### <a name="error-code--2343"></a>Hibakód: 2343

- **Üzenet**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **OK**: a Felhasználónév vagy a jelszó üres.

- **Javaslat**: adjon meg megfelelő hitelesítő adatokat a HDI-hez való kapcsolódáshoz, és próbálkozzon újra.


### <a name="error-code--2345"></a>Hibakód: 2345

- **Üzenet**:`Failed to read the content of the hive script. Error: '%message;'`

- **OK**: a parancsfájl nem létezik, vagy az ADF nem tudott kapcsolódni a parancsfájl helyéhez.

- **Javaslat**: Ellenőrizze, hogy létezik-e a parancsfájl, és hogy a társított társított szolgáltatás rendelkezik-e megfelelő hitelesítő adatokkal a kapcsolathoz.


### <a name="error-code--2346"></a>Hibakód: 2346

- **Üzenet**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **OK**: Az ADF megpróbált létrehozni egy ODBC-kapcsolatot a HDI-fürthöz, és hiba miatt meghiúsult.

- **Javaslat**: a hibaüzenet és a hibakód segítséget nyújt az ODBC-kapcsolatok hibáinak elhárításában. Ha nem elég a probléma megoldásához, kérjen segítséget az Azure HDInsight csapatának.


### <a name="error-code--2347"></a>Hibakód: 2347

- **Üzenet**:`Hive execution through ODBC failed with error message '%message;'.`

- **OK**: Az ADF elküldte a kaptár-szkriptet a HDI-fürthöz való végrehajtáshoz az ODBC-kapcsolatok segítségével, a parancsfájl pedig nem sikerült a HDI-n.

- **Javaslat**: a kaptár-parancsfájl végrehajtása nem sikerült a HDI-fürtön, és hibaüzenetet kap, és a hibakód segítséget nyújt a hibaelhárításban. Ha nem elég a probléma megoldásához, kérjen segítséget az Azure HDInsight csapatának.


### <a name="error-code--2348"></a>Hibakód: 2348

- **Üzenet**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **OK**: a Storage társított szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat**: csak a teljes kapcsolati karakterláncok támogatottak a fő tárolóhoz társított szolgáltatásban a HDI-tevékenységekhez. Győződjön meg arról, hogy nem használ MSI-hitelesítést vagy-alkalmazásokat.


### <a name="error-code--2350"></a>Hibakód: 2350

- **Üzenet**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **OK**: a megadott hitelesítő adatok nem megfelelőek ahhoz a tárolóhoz, ahol a fájlok találhatók, vagy a fájlok nem léteznek.

- **Javaslat**: Ez a hiba akkor fordul elő, ha az ADF a HDI-tevékenységek előkészítési lépéseit végzi. Megpróbál fájlokat másolni a fő tárolóba, mielőtt elküldené a feladatot a HDI-nek. Győződjön meg arról, hogy a fájl létezik a megadott helyen, a tárolási kapcsolatok helyesek. Az ADF HDI-tevékenységek nem támogatják az MSI-hitelesítést a HDI-tevékenységekhez kapcsolódó tárolási fiókoknál, ezért győződjön meg arról, hogy ezek a társított szolgáltatások teljes kulccsal rendelkeznek, vagy a Azure Key Vault használják.


### <a name="error-code--2351"></a>Hibakód: 2351

- **Üzenet**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **OK**: a fájl nem létezik a megadott elérési úton.

- **Javaslat**: Ellenőrizze, hogy a fájl valóban létezik-e, és a társított szolgáltatás a fájlra mutató kapcsolódási információkkal rendelkezik-e a megfelelő hitelesítő adatokkal.


### <a name="error-code--2352"></a>Hibakód: 2352

- **Üzenet**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **OK**: a file Storage társított szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat**: Ellenőrizze, hogy a file Storage társított szolgáltatásának tulajdonságai megfelelően vannak-e konfigurálva.


### <a name="error-code--2353"></a>Hibakód: 2353

- **Üzenet**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **OK**: a parancsfájl-tárolási társított szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat**: Ellenőrizze, hogy a parancsfájl-tárolási társított szolgáltatás tulajdonságai megfelelően vannak-e konfigurálva.


### <a name="error-code--2354"></a>Hibakód: 2354

- **Üzenet**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **OK**: a tevékenység nem támogatja a Storage-beli társított szolgáltatás típusát.

- **Javaslat**: Ellenőrizze, hogy a kiválasztott társított szolgáltatás rendelkezik-e a tevékenység támogatott típusaival. A HDI-tevékenységek támogatják a AzureBlobStorage és a AzureBlobFSStorage társított szolgáltatásokat. További információért olvassa el az [Azure HDInsight-fürtökkel való használatra vonatkozó tárolási lehetőségek összehasonlítása](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options) című témakört.


### <a name="error-code--2355"></a>Hibakód: 2355

- **Üzenet**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **OK**: a commandEnvironment megadott értéke helytelen.

- **Javaslat**:  
      Győződjön meg arról, hogy a megadott érték a \"következőhöz\"hasonló: \"commandEnvironment: [\" variableName = variableValue], és mindegyik változó csak egyszer jelenik meg a listában.
    


### <a name="error-code--2356"></a>Hibakód: 2356

- **Üzenet**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **OK**: a változót kétszer adták meg a commandEnvironment.

- **Javaslat**:  
      Győződjön meg arról, hogy a megadott érték a \"következőhöz\"hasonló: \"commandEnvironment: [\" variableName = variableValue], és mindegyik változó csak egyszer jelenik meg a listában.
    


### <a name="error-code--2357"></a>Hibakód: 2357

- **Üzenet**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **OK**: a megadott hitelesítő adatok helytelenek.

- **Javaslat**: Ellenőrizze a kapcsolati adatokat a ADLS Gen 1 társított szolgáltatásban, és győződjön meg arról, hogy a kapcsolat tesztelése sikeres volt.


### <a name="error-code--2358"></a>Hibakód: 2358

- **Üzenet**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **OK**: a szükséges "TimeToLive" tulajdonsághoz megadott érték formátuma érvénytelen. 

- **Javaslat**: frissítse az értéket a javasolt tartományba, és próbálkozzon újra.


### <a name="error-code--2359"></a>Hibakód: 2359

- **Üzenet**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **OK**: a "roles" tulajdonság megadott értéke érvénytelen.

- **Javaslat**: frissítse az értéket úgy, hogy az egyik javaslat legyen, és próbálkozzon újra.


### <a name="error-code--2360"></a>Hibakód: 2360

- **Üzenet**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **OK**: a megadott HCatalogLinkedService-karakterlánc érvénytelen.

- **Javaslat**: frissítse az értéket egy megfelelő Azure SQL-kapcsolódási sztringre, és próbálkozzon újra.


### <a name="error-code--2361"></a>Hibakód: 2361

- **Üzenet**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **OK**: a fürt létrehozása nem sikerült, és az ADF nem adott vissza hibát a HDInsight szolgáltatástól.

- **Javaslat**: Nyissa meg a Azure Portal, és próbálja meg megkeresni a megadott névvel rendelkező HDI-erőforrást, és ellenőrizze a kiépítési állapotot. További segítségért forduljon a HDInsight támogatási csapatához.


### <a name="error-code--2362"></a>Hibakód: 2362

- **Üzenet**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **OK**: a megadott további tárterület nem Azure Blob Storage.

- **Javaslat**: adja meg az Azure Blob Storage-fiókot további tárolóként az igény szerinti társított szolgáltatás HDInsight.



## <a name="web-activity"></a>Webes tevékenység

### <a name="error-code--2128"></a>Hibakód: 2128

- **Üzenet**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **OK**: hálózati kapcsolat, DNS-hiba, kiszolgálói tanúsítvány ellenőrzése vagy időtúllépés.

- **Javaslat**: Ellenőrizze, hogy a elérni kívánt végpont válaszol-e a kérelmekre. Használhat olyan eszközöket is, mint a Hegedűs vagy a Poster.


### <a name="error-code--2108"></a>Hibakód: 2108

- **Üzenet**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **OK**: a kérelem végrehajtása egy mögöttes probléma miatt meghiúsult, például a hálózati kapcsolat, a DNS-hiba, a kiszolgálói tanúsítvány érvényesítése vagy időtúllépés.

- **Javaslat**: a Hegedűs/Poster használatával ellenőrizze a kérést.
<br>


#### <a name="more-details"></a>További részletek
A Hegedűs használata a figyelt webalkalmazás HTTP-munkamenetének létrehozásához:

1. A [Hegedűs](https://www.telerik.com/download/fiddler)letöltése, telepítése és megnyitása.

1. Ha a webalkalmazás HTTPS protokollt használ, lépjen az **eszközök** > **Hegedűs beállítások** > **https**elemre. Válassza a HTTPS-forgalom **összekapcsolása** és a **https-forgalom visszafejtése**lehetőséget.

   ![Hegedűs beállításai](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Ha az alkalmazás TLS/SSL-tanúsítványokat használ, adja hozzá a Hegedűs tanúsítványát az eszközhöz. Nyissa meg az **eszközök** > **a Hegedűs beállításai** > **https** > -**műveletek** > **Főtanúsítvány exportálása az asztalra**lehetőséget.

1. Kapcsolja ki a rögzítést a **fájl** > **rögzítési forgalmának**kikapcsolásával. Vagy nyomja le az **F12**billentyűt.

1. Törölje a böngésző gyorsítótárát úgy, hogy minden gyorsítótárazott elem el legyen távolítva, és újra le kell tölteni.

1. Kérelem létrehozása:

   1. Válassza a **zeneszerző** fület.

   1. Állítsa be a HTTP-metódust és az URL-címet.
   
   1. Ha szükséges, adjon hozzá fejléceket és egy kérés törzsét.

   1. Válassza a **Végrehajtás** lehetőséget.

1. Kapcsolja be újra a forgalom rögzítését, és fejezze be a problémás tranzakciót az oldalon.

1. Válassza a **fájl** > **Mentés** > **minden munkamenet**lehetőséget.

További információ: [Bevezetés a Hegedűs](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)használatába.

## <a name="next-steps"></a>További lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)


            
