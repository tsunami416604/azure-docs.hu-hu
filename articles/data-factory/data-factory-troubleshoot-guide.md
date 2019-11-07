---
title: Hibakeresés Azure Data Factory
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory külső vezérlési tevékenységeit.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 66590e40ff2440a166310ec3d57026f5a2cdbd1d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676849"
---
# <a name="troubleshoot-azure-data-factory"></a>Hibakeresés Azure Data Factory

Ez a cikk a Azure Data Factory külső ellenőrzési tevékenységeinek gyakori hibaelhárítási módszereit vizsgálja.

## <a name="connector-and-copy-activity"></a>Összekötő és másolási tevékenység

Összekötői problémák esetén, például a másolási tevékenység használatával kapcsolatos hiba esetén tekintse át az [Azure Data Factory összekötők hibaelhárítása](connector-troubleshoot-guide.md)című témakört.

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Hibakód: 3200

- **Üzenet**: 403-es hiba.

- **OK**: `The Databricks access token has expired.`

- **Javaslat**: alapértelmezés szerint a Azure Databricks hozzáférési jogkivonat 90 napig érvényes. Hozzon létre egy új jogkivonatot, és frissítse a társított szolgáltatást.


### <a name="error-code--3201"></a>Hibakód: 3201

- **Üzenet**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **OK**: `Bad authoring: Notebook path not specified correctly.`

- **Javaslat**: a jegyzetfüzet elérési útjának megadása a Databricks tevékenységben.

<br/>

- **Üzenet**: `Cluster   ... does not exist.`

- **OK**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Javaslat**: Ellenőrizze, hogy létezik-e a Databricks-fürt.

<br/>

- **Üzenet**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **OK**: `Bad authoring.`

- **Javaslat**: adjon meg abszolút elérési utat a munkaterület-címzési sémák számára, vagy `dbfs:/folder/subfolder/foo.py` a Databricks-fájlrendszerben tárolt fájlokhoz.

<br/>

- **Üzenet**: `{0} LinkedService should have domain and accessToken as required properties.`

- **OK**: `Bad authoring.`

- **Javaslat**: Ellenőrizze a [társított szolgáltatás definícióját](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Üzenet**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **OK**: `Bad authoring.`

- **Javaslat**: Ellenőrizze a [társított szolgáltatás definícióját](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Üzenet**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **OK**: `Bad authoring.`

- **Javaslat**: Tekintse meg a hibaüzenetet. 

<br/>

### <a name="error-code--3202"></a>Hibakód: 3202

- **Üzenet**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **OK**: `Too many Databricks runs in an hour.`

- **Javaslat**: a Databricks-munkaterületet használó összes folyamat kiértékelése a feladatok létrehozási arányára.  Ha a folyamatok túl sok Databricks-t futtatnak összesítésben, telepítsen át bizonyos folyamatokat egy új munkaterületre.

<br/>

- **Üzenet**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **OK**: `Authoring error: No value provided for the parameter.`

- **Javaslat**: vizsgálja meg a folyamat JSON-fájlját, és győződjön meg arról, hogy a baseParameters-jegyzetfüzet összes paramétere nem üres értéket ad meg.

<br/>

- **Üzenet**: `User: `SimpleUserContext {userId =..., név =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **OK**: a hozzáférési tokent létrehozó felhasználó nem férhet hozzá a társított szolgáltatásban megadott Databricks-fürthöz.

- **Javaslat**: Ellenőrizze, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a munkaterületen.


### <a name="error-code--3203"></a>Hibakód: 3203

- **Üzenet**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **OK**: a fürt meg lett szakítva. Az interaktív fürtök esetében ez lehet a verseny feltétele.

- **Javaslat**: a hiba elkerülésének legjobb módja a feladatok fürtök használata.


### <a name="error-code--3204"></a>Hibakód: 3204

- **Üzenet**: `Job execution failed.`

- **OK**: a hibaüzenetek különböző problémákat jeleznek, például egy váratlan fürt állapotát vagy egy adott tevékenységet. Leggyakrabban egyetlen hibaüzenet sem jelenik meg. 

- **Javaslat**: N/A


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

A következő táblázat az U-SQL-re vonatkozik.


### <a name="error-code--2709"></a>Hibakód: 2709

- **Üzenet**: `The access token is from the wrong tenant.`

- **OK**: helytelen Azure Active Directory (Azure ad) bérlő.

- **Javaslat**: helytelen Azure Active Directory (Azure ad) bérlő.

<br/>

- **Üzenet**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **OK**: ezt a hibát a Data Lake Analytics szabályozása okozza.

- **Javaslat**: csökkentse Data Lake Analytics elküldött feladatok számát a tevékenységek Data Factory eseményindítók és egyidejűségi beállítások módosításával. Vagy növelje Data Lake Analytics korlátozásait.

<br/>

- **Üzenet**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **OK**: ezt a hibát a Data Lake Analytics szabályozása okozza. 

- **Javaslat**: csökkentse Data Lake Analytics elküldött feladatok számát a tevékenységek Data Factory eseményindítók és egyidejűségi beállítások módosításával. Vagy növelje Data Lake Analytics korlátozásait.


### <a name="error-code--2705"></a>Hibakód: 2705

- **Üzenet**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: gondoskodjon arról, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány hozzáférhessen az Data Lake Analytics fiókhoz és a gyökérmappa alapértelmezett Data Lake Storage példányához.


### <a name="error-code--2711"></a>Hibakód: 2711

- **Üzenet**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: gondoskodjon arról, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány hozzáférhessen az Data Lake Analytics fiókhoz és a gyökérmappa alapértelmezett Data Lake Storage példányához.

<br/>

- **Üzenet**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **OK**: az U-SQL-fájl elérési útja helytelen, vagy a társított szolgáltatás hitelesítő adatai nem rendelkeznek hozzáféréssel.

- **Javaslat**: Ellenőrizze a társított szolgáltatásban megadott elérési utat és a hitelesítő adatokat.


### <a name="error-code--2704"></a>Hibakód: 2704

- **Üzenet**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: gondoskodjon arról, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány hozzáférhessen az Data Lake Analytics fiókhoz és a gyökérmappa alapértelmezett Data Lake Storage példányához.


### <a name="error-code--2707"></a>Hibakód: 2707

- **Üzenet**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **OK**: a társított szolgáltatás Data Lake Analytics fiókja nem megfelelő.

- **Javaslat**: Ellenőrizze, hogy a megfelelő fiók van-e megadva.


### <a name="error-code--2703"></a>Hibakód: 2703

- **Üzenet**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **OK**: a hiba Data Lake Analytics. 

- **Javaslat**: a példában szereplő hiba például azt jelenti, hogy a feladatot elküldték Data Lake Analyticsba, és a parancsfájl sikertelen volt. Data Lake Analytics vizsgálata. A portálon lépjen a Data Lake Analytics fiókra, és keresse meg a feladatot a Data Factory tevékenység futtatási AZONOSÍTÓjának használatával (nem a folyamat futtatási AZONOSÍTÓját). A feladattal kapcsolatban további információ található a hibával kapcsolatban, és segítséget nyújt a hibák megoldásához. Ha a megoldás nem egyértelmű, lépjen kapcsolatba a Data Lake Analytics támogatási csapatával, és adja meg a feladatokhoz tartozó URL-címet, amely tartalmazza a fiók nevét és a AZONOSÍTÓJÚ feladatot.



## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Hibakód: 3602

- **Üzenet**: `Invalid HttpMethod: {method}.`

- **OK**: az Azure Function tevékenység nem támogatja a tevékenységhez tartozó adattartalomban megadott http-metódust. 

- **Javaslat**: a támogatott http-metódusok az Put, a post, a Get, a DELETE, a Options, a Head és a trace.


### <a name="error-code--3603"></a>Hibakód: 3603

- **Üzenet**: `Response content is not a valid JObject.`

- **OK**: a hívott Azure-függvény nem adott vissza JSON-adattartalmat a válaszban. Az Data Factory Azure functions tevékenység csak a JSON-válaszok tartalmát támogatja. 

- **Javaslat**: frissítse az Azure-függvényt egy érvényes JSON-adattartalom visszaküldéséhez. Egy C# függvény például visszaadhatja `(ActionResult)new<OkObjectResult("{`\"azonosítóját\":\"123\"`}");`.


### <a name="error-code--3606"></a>Hibakód: 3606

- **Üzenet**: `Azure function activity missing function key.`

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be. 

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója rendelkezik-e "functionKey" nevű tulajdonsággal.


### <a name="error-code--3607"></a>Hibakód: 3607

- **Üzenet**: `Azure function activity missing function name.`

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be. 

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója "függvénynév" nevű tulajdonsággal rendelkezik-e.


### <a name="error-code--3608"></a>Hibakód: 3608

- **Üzenet**: `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **OK**: az Azure-függvény részletei a tevékenység-definícióban helytelenek lehetnek. 

- **Javaslat**: javítsa ki az Azure-függvény adatait, és próbálkozzon újra.


### <a name="error-code--3609"></a>Hibakód: 3609

- **Üzenet**: `Azure function activity missing functionAppUrl.` 

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be. 

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója rendelkezik-e "functionAppUrl" nevű tulajdonsággal.


### <a name="error-code--3610"></a>Hibakód: 3610

- **Üzenet**: `There was an error while calling endpoint.`

- **OK**: lehet, hogy a függvény URL-címe helytelen.

- **Javaslat**: Ellenőrizze, hogy helyes-e a "functionAppUrl" érték a tevékenység JSON-fájljában, majd próbálkozzon újra.


### <a name="error-code--3611"></a>Hibakód: 3611

- **Üzenet**: `Azure function activity missing Method in JSON.` 

- **OK**: az Azure Function tevékenység-definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója "Method" nevű tulajdonsággal rendelkezik-e.


### <a name="error-code--3612"></a>Hibakód: 3612

- **Üzenet**: `Azure function activity missing LinkedService definition in JSON.`

- **OK**: Előfordulhat, hogy az Azure Function tevékenység definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREFUNCTION tevékenység JSON-definíciója rendelkezik-e társított szolgáltatás részleteivel.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Hibakód: 4101

- **Üzenet**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **OK**: a tulajdonság helytelen formátumú vagy hiányzó definíciója.

- **Javaslat**: Ellenőrizze, hogy a tevékenység a megfelelő adatokkal van-e definiálva.


### <a name="error-code--4110"></a>Hibakód: 4110

- **Üzenet**: a AzureMLExecutePipeline-tevékenység LinkedService-definíciója hiányzik a JSON-ben.

- **OK**: a AzureMLExecutePipeline tevékenység definíciója nem fejeződött be.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREMLEXECUTEPIPELINE tevékenység JSON-definíciója rendelkezik-e társított szolgáltatás részleteivel.


### <a name="error-code--4111"></a>Hibakód: 4111

- **Üzenet**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **OK**: helytelen tevékenység-definíció.

- **Javaslat**: Ellenőrizze, hogy a bemeneti AZUREMLEXECUTEPIPELINE tevékenység JSON-definíciója rendelkezik-e megfelelő társított szolgáltatás részleteivel.


### <a name="error-code--4112"></a>Hibakód: 4112

- **Üzenet**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **OK**: a tulajdonság helytelen formátumú vagy hiányzó definíciója.

- **Javaslat**: Ellenőrizze, hogy a társított szolgáltatás definíciója megfelelő adatokkal rendelkezik-e.


### <a name="error-code--4121"></a>Hibakód: 4121

- **Üzenet**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **OK**: az Azure ml szolgáltatás elérésére használt hitelesítő adatok lejártak.

- **Javaslat**: Ellenőrizze a hitelesítő adatok érvényességét, és próbálkozzon újra


### <a name="error-code--4122"></a>Hibakód: 4122

- **Üzenet**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **OK**: a AzureML szolgáltatáshoz társított szolgáltatásban megadott hitelesítő adat érvénytelen vagy nem rendelkezik engedéllyel a művelethez.

- **Javaslat**: Ellenőrizze, hogy érvényes-e a hitelesítő adatok a társított szolgáltatásban, és jogosult-e a AzureML szolgáltatás elérésére.


### <a name="error-code--4123"></a>Hibakód: 4123

- **Üzenet**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **OK**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Javaslat**: Ellenőrizze a tevékenység tulajdonságainak értékét, hogy megegyezzen a társított szolgáltatásban megadott közzétett Azure ml-folyamat várt hasznos adattartalmával.


### <a name="error-code--4124"></a>Hibakód: 4124

- **Üzenet**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **OK**: a közzétett Azure ml-folyamat végpontja nem létezik.

- **Javaslat**: Ellenőrizze, hogy a társított szolgáltatásban megadott közzétett Azure ml pipeline-végpont létezik-e az Azure ml szolgáltatásban.


### <a name="error-code--4125"></a>Hibakód: 4125

- **Üzenet**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **OK**: kiszolgálói hiba az Azure ml szolgáltatásban.

- **Javaslat**: próbálkozzon újra később. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot az Azure ML Service csapatával.


### <a name="error-code--4126"></a>Hibakód: 4126

- **Üzenet**: `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **OK**: a AzureML folyamat futtatása nem sikerült.

- **Javaslat**: Ellenőrizze a AzureMLService a további hibák naplózása és a ml-folyamat javítása érdekében.


## <a name="custom"></a>Egyéni

A következő táblázat a Azure Batchra vonatkozik.


### <a name="error-code--2500"></a>Hibakód: 2500

- **Üzenet**: `Hit unexpected exception and execution failed.`

- **OK**: `Can't launch command, or the program returned an error code.`

- **Javaslat**: Ellenőrizze, hogy létezik-e a végrehajtható fájl. Ha a program elindult, győződjön meg róla, hogy az *StdOut. txt* és a *stderr. txt fájl* fel lett töltve a Storage-fiókba. Érdemes lehet bőséges naplókat kibocsátani a kódban a hibakereséshez.


### <a name="error-code--2501"></a>Hibakód: 2501

- **Üzenet**: `Cannot access user batch account; please check batch account settings.`

- **OK**: helytelen a Batch-hozzáférési kulcs vagy a készlet neve.

- **Javaslat**: Ellenőrizze a készlet nevét és a Batch-elérési kulcsot a társított szolgáltatásban.


### <a name="error-code--2502"></a>Hibakód: 2502

- **Üzenet**: `Cannot access user storage account; please check storage account settings.`

- **OK**: helytelen a Storage-fiók neve vagy elérési kulcsa.

- **Javaslat**: Ellenőrizze a Storage-fiók nevét és a hozzáférési kulcsot a társított szolgáltatásban.


### <a name="error-code--2504"></a>Hibakód: 2504

- **Üzenet**: `Operation returned an invalid status code 'BadRequest'.` 

- **OK**: túl sok fájl van az egyéni tevékenység folderPath. A resourceFiles teljes mérete nem lehet hosszabb 32 768 karakternél.

- **Javaslat**: távolítsa el a szükségtelen fájlokat. Vagy zip, és adjon hozzá egy unzip parancsot a kinyeréséhez. Használja például a `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Hibakód: 2505

- **Üzenet**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **OK**: az egyéni tevékenységek csak a hozzáférési kulcsot használó tárolási fiókokat támogatják.

- **Javaslat**: Tekintse meg a hiba leírását.


### <a name="error-code--2507"></a>Hibakód: 2507

- **Üzenet**: `The folder path does not exist or is empty: ....`

- **OK**: a megadott elérési úton egyetlen fájl sem található a Storage-fiókban.

- **Javaslat**: a mappa elérési útjának tartalmaznia kell a futtatni kívánt végrehajtható fájlokat.


### <a name="error-code--2508"></a>Hibakód: 2508

- **Üzenet**: `There are duplicate files in the resource folder.`

- **OK**: több azonos nevű fájl található a folderPath különböző almappáiban.

- **Javaslat**: az egyéni tevékenységek lelapulják a folderPath a mappa szerkezetét.  Ha meg kell őriznie a mappa szerkezetét, zip-fájlt kell kibontania Azure Batch egy unzip paranccsal. Használja például a `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Hibakód: 2509

- **Üzenet**: `Batch   url ... is invalid; it must be in Uri format.` 

- **OK**: a Batch URL-címeinek a `https://mybatchaccount.eastus.batch.azure.com`hoz hasonlónak kell lenniük

- **Javaslat**: Tekintse meg a hiba leírását.


### <a name="error-code--2510"></a>Hibakód: 2510

- **Üzenet**: `An   error occurred while sending the request.`

- **OK**: a Batch URL-címe érvénytelen. 

- **Javaslat**: Ellenőrizze a Batch URL-címét.


## <a name="hdinsight"></a>HDInsight

A következő táblázat a Spark, a kaptár, a MapReduce, a Pig és a Hadoop streaming rendszerre vonatkozik.


### <a name="error-code--2300"></a>Hibakód: 2300

- **Üzenet**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **OK**: a megadott fürt URI azonosítója érvénytelen. 

- **Javaslat**: Győződjön meg arról, hogy a fürt nem lett törölve, és hogy a megadott URI helyes. Amikor megnyitja az URI-t egy böngészőben, megjelenik a Ambari felhasználói felülete. Ha a fürt virtuális hálózatban található, az URI-nak a privát URI-nak kell lennie. A megnyitásához használjon egy azonos virtuális hálózat részét képező virtuális GÉPET. További információ: [közvetlen kapcsolódás Apache Hadoop szolgáltatásokhoz](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Üzenet**: `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **OK**: a feladatok beküldése időtúllépés miatt megvolt. 

- **Javaslat**: a probléma lehet általános HDInsight-kapcsolat vagy hálózati kapcsolat. Először ellenőrizze, hogy a HDInsight Ambari felhasználói felülete elérhető-e bármely böngészőből. Ellenőrizze, hogy a hitelesítő adatai még érvényesek-e. Ha saját üzemeltetésű integrált futtatókörnyezetet (IR) használ, ügyeljen arra, hogy azt a virtuális gépet vagy gépet használja, ahol a saját üzemeltetésű integrációs modul telepítve van. Ezután próbálja meg újra elküldeni a feladatot Data Factory újra. Ha továbbra sem sikerül, forduljon a Data Factory csapatához.


- **Üzenet**: `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **OK**: a HDInsight hitelesítő adatai helytelenek vagy lejártak.

- **Javaslat**: javítsa ki a hitelesítő adatokat, és telepítse újra a társított szolgáltatást. Először győződjön meg arról, hogy a hitelesítő adatok HDInsight működnek a fürt URI-ja megnyitásával bármely böngészőben, és próbáljon bejelentkezni. Ha a hitelesítő adatok nem működnek, alaphelyzetbe állíthatja azokat a Azure Portal.

<br/>

- **Üzenet**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **OK**: Ez a hiba a HDInsight.

- **Javaslat**: Ez a hiba a HDInsight-fürtből származik. További információ: [Ambari felhasználói felület 502 hiba](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 hiba a Spark](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)-beli szolgáltatáshoz való csatlakozáshoz, [502-hibák a Spark-hoz való csatlakozáshoz](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)és [a Application Gateway hibás átjárókkal kapcsolatos hibák elhárítása](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Üzenet**: `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **OK**: túl sok feladat van elküldve a HDInsight egyszerre.

- **Javaslat**: érdemes lehet korlátozni a HDInsight küldött egyidejű feladatok számát. Ha ugyanaz a tevékenység küldi el a feladatokat, tekintse meg Data Factory tevékenység egyidejűségét. Módosítsa az eseményindítókat úgy, hogy az egyidejű folyamat-futtatások egyszerre legyenek kiosztva. Tekintse át a HDInsight dokumentációját, hogy a hiba esetén a `templeton.parallellism.job.submit` módosítsa.


### <a name="error-code--2303"></a>Hibakód: 2303

- **Üzenet**: `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **OK**: a rendszer elküldte a feladatot a HDInsight, és a HDInsight-on meghiúsult.

- **Javaslat**: a feladatot sikeresen elküldte a HDInsight. Sikertelen volt a fürtön. Nyissa meg a feladatot és a naplókat a HDInsight Ambari felhasználói felületén, vagy nyissa meg a fájlt a tárolóból, ha a hibaüzenetet javasolja. A fájl a hiba részleteit jeleníti meg.


### <a name="error-code--2310"></a>Hibakód: 2310

- **Üzenet**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **OK**: a megadott fürt URI azonosítója érvénytelen. 

- **Javaslat**: Győződjön meg arról, hogy a fürt nem lett törölve, és hogy a megadott URI helyes. Amikor megnyitja az URI-t egy böngészőben, megjelenik a Ambari felhasználói felülete. Ha a fürt virtuális hálózatban található, az URI-nak a privát URI-nak kell lennie. A megnyitásához használjon egy azonos virtuális hálózat részét képező virtuális GÉPET. További információ: [közvetlen kapcsolódás Apache Hadoop szolgáltatásokhoz](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Üzenet**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **OK**: Ez a hiba a HDInsight.

- **Javaslat**: Ez a hiba a HDInsight-fürtből származik. További információ: [Ambari felhasználói felület 502 hiba](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 hiba a Spark](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)-beli szolgáltatáshoz való csatlakozáshoz, [502-hibák a Spark-hoz való csatlakozáshoz](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)és [a Application Gateway hibás átjárókkal kapcsolatos hibák elhárítása](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Üzenet**: `java.lang.NullPointerException`

- **OK**: Ez a hiba akkor fordul elő, ha a feladatot egy Spark-fürtbe küldi a rendszer. 

- **Javaslat**: Ez a kivétel a HDInsight származik. Elrejti a tényleges problémát. Segítségért forduljon a HDInsight csapatához. Adja meg a fürt nevét és a tevékenység futtatási időtartományát.


### <a name="error-code--2347"></a>Hibakód: 2347

- **Üzenet**: `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **OK**: a rendszer elküldte a feladatot a HDInsight, és a HDInsight-on meghiúsult.

- **Javaslat**: a feladatot sikeresen elküldte a HDInsight. Sikertelen volt a fürtön. Nyissa meg a feladatot és a naplókat a HDInsight Ambari felhasználói felületén, vagy nyissa meg a fájlt a tárolóból, ha a hibaüzenetet javasolja. A fájl a hiba részleteit jeleníti meg.


### <a name="error-code--2328"></a>Hibakód: 2328

- **Üzenet**: `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **OK**: Ez a hiba az igény szerinti HDInsight történik.

- **Javaslat**: Ez a hiba a HDInsight szolgáltatásból származik, amikor a HDInsight kiépítés sikertelen. Forduljon a HDInsight csapathoz, és adja meg az igény szerinti fürt nevét.



## <a name="web-activity"></a>Webes tevékenység

### <a name="error-code--2108"></a>Hibakód: 2108

- **Üzenet**: `Invalid HttpMethod: '...'.`

- **OK**: a webes tevékenység nem támogatja a tevékenység adattartalmaban megadott http-metódust.

- **Javaslat**: a támogatott http-módszerek a Put, a post, a Get és a DELETE.

<br/>

- **Üzenet**: `Invalid Server Error 500.`

- **OK**: belső hiba a végponton.

- **Javaslat**: a Hegedűs vagy a Poster használatával keresse meg a funkciót az URL-címen.

<br/>

- **Üzenet**: `Unauthorized 401.`

- **OK**: hiányzó érvényes hitelesítés a kérelemben.

- **Javaslat**: lehetséges, hogy a jogkivonat lejárt. Adjon meg egy érvényes hitelesítési módszert. A Hegedűs vagy a Poster használatával keresse meg a funkciót az URL-címen.

<br/>

- **Üzenet**: `Forbidden 403.`

- **OK**: hiányzó szükséges engedélyek.

- **Javaslat**: a hozzáférési erőforrás felhasználói engedélyeinek megtekintése. A Hegedűs vagy a Poster használatával keresse meg a funkciót az URL-címen.

<br/>

- **Üzenet**: `Bad Request 400.`

- **OK**: érvénytelen HTTP-kérelem.

- **Javaslat**: keresse meg a kérelem URL-címét, műveletét és törzsét. A kérelem érvényesítéséhez a Hegedűs vagy a Poster használatával.

<br/>

- **Üzenet**: `Not found 404.` 

- **OK**: az erőforrás nem található.   

- **Javaslat**: a Hegedűs vagy a Poster használatával ellenőrizze a kérést.

<br/>

- **Üzenet**: `Service unavailable.`

- **OK**: a szolgáltatás nem érhető el.

- **Javaslat**: a Hegedűs vagy a Poster használatával ellenőrizze a kérést.

<br/>

- **Üzenet**: `Unsupported Media Type.`

- **OK**: a tartalom típusa nem egyezik a webes tevékenység Törzsével.

- **Javaslat**: adja meg a hasznos adatok formátumának megfelelő tartalomtípust. A kérelem érvényesítéséhez a Hegedűs vagy a Poster használatával.

<br/>

- **Üzenet**: `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **OK**: az erőforrás nem érhető el. 

- **Javaslat**: Hegedűs vagy Poster használatával keresse meg a végpontot.

<br/>

- **Üzenet**: `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **OK**: helytelen webes tevékenységi metódus lett megadva a kérelemben.

- **Javaslat**: Hegedűs vagy Poster használatával keresse meg a végpontot.

<br/>

- **Üzenet**: `invalid_payload`

- **OK**: a webes tevékenység törzse helytelen.

- **Javaslat**: Hegedűs vagy Poster használatával keresse meg a végpontot.


### <a name="error-code--2208"></a>Hibakód: 2208

- **Üzenet**: `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **OK**: a célhely sikertelen állapotot adott vissza.

- **Javaslat**: a Hegedűs/Poster használatával ellenőrizze a kérést.


### <a name="error-code--2308"></a>Hibakód: 2308

- **Üzenet**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **OK**: több oka is lehet ennek a hibának, például a hálózati kapcsolatnak, a DNS-meghibásodásnak, a kiszolgálói tanúsítvány ellenőrzésének vagy időtúllépésének.

- **Javaslat**: a Hegedűs/Poster használatával ellenőrizze a kérést.


A Hegedűs használata a figyelt webalkalmazás HTTP-munkamenetének létrehozásához:

1. A [Hegedűs](https://www.telerik.com/download/fiddler)letöltése, telepítése és megnyitása.

1. Ha a webalkalmazás HTTPS protokollt használ, lépjen az **eszközök** > a **Hegedűs beállításai** > **https**elemre. Válassza a HTTPS-forgalom **összekapcsolása** és a **https-forgalom visszafejtése**lehetőséget. 
   
   ![Hegedűs beállításai](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Ha az alkalmazás SSL-tanúsítványokat használ, adja hozzá a Hegedűs tanúsítványát az eszközhöz. Nyissa meg az **eszközök** > a **Hegedűs beállításai** > **https** > **műveleteket** > **a főtanúsítvány exportálása az asztalra**lehetőséget.

1. A rögzítés kikapcsolásához lépjen a **fájl** > **rögzítési forgalom**elemre. Vagy nyomja le az **F12**billentyűt.

1. Törölje a böngésző gyorsítótárát úgy, hogy minden gyorsítótárazott elem el legyen távolítva, és újra le kell tölteni.

1. Kérelem létrehozása: 

   a. Válassza a **zeneszerző** fület.

   b. Állítsa be a HTTP-metódust és az URL-címet.

   c. Ha szükséges, adjon hozzá fejléceket és egy kérés törzsét.

   d. Válassza a **Végrehajtás** lehetőséget.

9. Kapcsolja be újra a forgalom rögzítését, és fejezze be a problémás tranzakciót az oldalon.

10. Válassza a **fájl** > **Mentés** > **minden munkamenet**lehetőséget.

További információ: [Bevezetés a Hegedűs](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)használatába.

## <a name="next-steps"></a>További lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory-blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)



