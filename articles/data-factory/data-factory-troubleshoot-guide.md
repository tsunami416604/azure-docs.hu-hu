---
title: Azure Data Factory hibaelhárítása | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el a külső vezérlési tevékenységeket az Azure Data Factoryban.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: c9a1ac831c4300c0523717fddc1fa53417068b89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416577"
---
# <a name="troubleshoot-azure-data-factory"></a>Azure Data Factory – problémamegoldás
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az Azure Data Factory külső vezérlési tevékenységeinek gyakori hibaelhárítási módszereit ismerteti.

## <a name="connector-and-copy-activity"></a>Összekötő és másolási tevékenység

Az összekötővel kapcsolatos problémákat, például a másolási tevékenység használatával kapcsolatos hibákat lásd az [Azure Data Factory-összekötők hibaelhárítása című témakörben.](connector-troubleshoot-guide.md)
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Hibakód: 3200

- **Üzenet**: 403-as hiba.

- **Ok:**`The Databricks access token has expired.`

- **Javaslat:** Alapértelmezés szerint az Azure Databricks hozzáférési jogkivonat 90 napig érvényes. Hozzon létre egy új jogkivonatot, és frissítse a csatolt szolgáltatást.


### <a name="error-code--3201"></a>Hibakód: 3201

- **Üzenet**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Ok:**`Bad authoring: Notebook path not specified correctly.`

- **Javaslat:** Adja meg a jegyzetfüzet elérési útját a Databricks tevékenységben.

<br/>  

- **Üzenet**:`Cluster... does not exist.`

- **Ok:**`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Javaslat**: Ellenőrizze, hogy a Databricks-fürt létezik-e.

<br/>  

- **Üzenet**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Ok:**`Bad authoring.`

- **Javaslat**: Adja meg a munkaterület-címzési `dbfs:/folder/subfolder/foo.py` sémák abszolút elérési útjait, vagy a Databricks fájlrendszerben tárolt fájlokat.

<br/>  

- **Üzenet**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Ok:**`Bad authoring.`

- **Javaslat**: Ellenőrizze a [csatolt szolgáltatásdefiníciót](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Üzenet**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Ok:**`Bad authoring.`

- **Javaslat**: Ellenőrizze a [csatolt szolgáltatásdefiníciót](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Üzenet**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Ok:**`Bad authoring.`

- **Javaslat**: Olvassa el a hibaüzenetet.

<br/>

### <a name="error-code--3202"></a>Hibakód: 3202

- **Üzenet**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Ok:**`Too many Databricks runs in an hour.`

- **Javaslat:** Ellenőrizze az összes folyamatot, amely ezt a Databricks munkaterületet használja a munkahelyteremtési arányhoz.  Ha a folyamatok túl sok Databricks fut összesítve, néhány folyamatok áttelepítése egy új munkaterületre.

<br/>  

- **Üzenet**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Ok:**`Authoring error: No value provided for the parameter.`

- **Javaslat:** Vizsgálja meg a JSON folyamatot, és győződjön meg arról, hogy a baseParameters notebook összes paramétere nem üres értéket ad meg.

<br/>  

- **Üzenet** `User: `: SimpleUserContext{userId=...,user@company.comname=, orgId=...}` is not   authorized to access cluster.`

- **Ok:** A hozzáférési jogkivonatot létrehozó felhasználó nem férhet hozzá a linked szolgáltatásban megadott Databricks-fürthöz.

- **Javaslat:** Győződjön meg arról, hogy a felhasználó rendelkezik a szükséges engedélyekkel a munkaterületen.


### <a name="error-code--3203"></a>Hibakód: 3203

- **Üzenet**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Ok**: A fürt leállt. Az interaktív fürtök esetében ez versenyhelyzet lehet.

- **Javaslat**: A hiba elkerülésének legjobb módja a feladatfürtök használata.


### <a name="error-code--3204"></a>Hibakód: 3204

- **Üzenet**:`Job execution failed.`

- **Ok:** A hibaüzenetek különböző problémákat jeleznek, például egy váratlan fürtállapotot vagy egy adott tevékenységet. Leggyakrabban egyáltalán nem jelenik meg hibaüzenet.

- **Ajánlás**: N/A
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Az alábbi táblázat az U-SQL-re vonatkozik.
      
### <a name="error-code--2709"></a>Hibakód: 2709

- **Üzenet**:`The access token is from the wrong tenant.`

- **Ok:** Helytelen Azure Active Directory (Azure AD) bérlő.

- **Javaslat**: Helytelen Azure Active Directory (Azure AD) bérlő.

<br/>

- **Üzenet**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Ok:** Ezt a hibát a Data Lake Analytics szabályozása okozza.

- **Javaslat:** Csökkentse a Data Lake Analytics beküldött feladatok számát a Data Factory eseményindítók és a tevékenységek egyidejűségi beállításainak módosításával. Vagy növelje a Data Lake Analytics korlátait.

<br/>  

- **Üzenet**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Ok:** Ezt a hibát a Data Lake Analytics szabályozása okozza.

- **Javaslat:** Csökkentse a Data Lake Analytics beküldött feladatok számát a Data Factory eseményindítók és a tevékenységek egyidejűségi beállításainak módosításával. Vagy növelje a Data Lake Analytics korlátait.


### <a name="error-code--2705"></a>Hibakód: 2705

- **Üzenet**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Ok:** Az egyszerű szolgáltatás vagy a tanúsítvány nem fér hozzá a tárolóban lévő fájlhoz.

- **Javaslat:** Győződjön meg arról, hogy a felhasználó által a Data Lake Analytics-feladatok hoz biztosított szolgáltatásnév vagy tanúsítvány hozzáfér a Data Lake Analytics-fiókhoz és a gyökérmappából származó alapértelmezett Data Lake Storage-példányhoz.


### <a name="error-code--2711"></a>Hibakód: 2711

- **Üzenet**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Ok:** Az egyszerű szolgáltatás vagy a tanúsítvány nem fér hozzá a tárolóban lévő fájlhoz.

- **Javaslat:** Győződjön meg arról, hogy a felhasználó által a Data Lake Analytics-feladatok hoz biztosított szolgáltatásnév vagy tanúsítvány hozzáfér a Data Lake Analytics-fiókhoz és a gyökérmappából származó alapértelmezett Data Lake Storage-példányhoz.

<br/>  

- **Üzenet**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Ok:** Az U-SQL fájl elérési útja hibás, vagy a csatolt szolgáltatás hitelesítő adatai nem rendelkeznek hozzáféréssel.

- **Javaslat**: Ellenőrizze a csatolt szolgáltatásban megadott elérési utat és hitelesítő adatokat.


### <a name="error-code--2704"></a>Hibakód: 2704

- **Üzenet**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Ok:** Az egyszerű szolgáltatás vagy a tanúsítvány nem fér hozzá a tárolóban lévő fájlhoz.

- **Javaslat:** Győződjön meg arról, hogy a felhasználó által a Data Lake Analytics-feladatok hoz biztosított szolgáltatásnév vagy tanúsítvány hozzáfér a Data Lake Analytics-fiókhoz és a gyökérmappából származó alapértelmezett Data Lake Storage-példányhoz.


### <a name="error-code--2707"></a>Hibakód: 2707

- **Üzenet**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Ok:** A Data Lake Analytics-fiók a csatolt szolgáltatás hibás.

- **Javaslat**: Ellenőrizze, hogy a megfelelő fiók van-e megadva.


### <a name="error-code--2703"></a>Hibakód: 2703

- **Üzenet**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Ok:** A hiba a Data Lake Analytics.

- **Javaslat:** A példahoz hasonló hiba azt jelenti, hogy a feladat elküldve lett a Data Lake Analytics-nek, és az ottani parancsfájl nem sikerült. Vizsgálja meg a Data Lake Analytics. A portálon nyissa meg a Data Lake Analytics-fiókot, és keresse meg a feladatot a Data Factory tevékenység futtatási azonosítójával (nem a folyamat futtatási azonosítójával). Az ottani feladat további információt nyújt a hibáról, és segít a hibaelhárításban. Ha a megoldás nem egyértelmű, lépjen kapcsolatba a Data Lake Analytics támogatási csapatával, és adja meg a feladat URL-címét, amely tartalmazza a fiók nevét és a feladatazonosítót.
          

## <a name="azure-functions"></a>Azure-függvények

### <a name="error-code--3602"></a>Hibakód: 3602

- **Üzenet**:`Invalid HttpMethod: '%method;'.`

- **Ok:** A tevékenység hasznos adatában megadott Http metódust az Azure Function Activity nem támogatja.

- **Javaslat**: A támogatott http-módszerek: PUT, POST, GET, DELETE, OPTIONS, HEAD és TRACE.


### <a name="error-code--3603"></a>Hibakód: 3603

- **Üzenet**:`Response Content is not a valid JObject.`

- **Ok:** Az Azure-függvény, amely nek hívták, nem adott vissza JSON-hasznos adat a válaszban. Az ADF Azure függvénytevékenység csak a JSON-választartalmat támogatja.

- **Javaslat**: Az Azure-függvény frissítése érvényes JSON-tartalom visszaadásához, pl. egy C# függvény\"visszaadhat (ActionResult)új OkObjectResult("{ Id\":\"123\"}");


### <a name="error-code--3606"></a>Hibakód: 3606

- **Üzenet:** Az Azure-függvénytevékenység hiányzik a függvénykulcsból.

- **Ok:** Az Azure-függvény tevékenységdefiníciója nem teljes.

- **Javaslat:** Ellenőrizze a bemeneti AzureFunction tevékenység JSON-definíció "functionKey" nevű tulajdonsággal rendelkezik.


### <a name="error-code--3607"></a>Hibakód: 3607

- **Üzenet**:`Azure function activity missing function name.`

- **Ok:** Az Azure-függvény tevékenységdefiníciója nem teljes.

- **Javaslat:** Ellenőrizze a bemeneti AzureFunction tevékenység JSON-definíció "functionName" nevű tulajdonság.


### <a name="error-code--3608"></a>Hibakód: 3608

- **Üzenet**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Ok:** Előfordulhat, hogy az Azure-függvény részletei a tevékenységdefinícióban helytelenek.

- **Javaslat**: Javítsa ki az azure függvény részleteit, és próbálkozzon újra.


### <a name="error-code--3609"></a>Hibakód: 3609

- **Üzenet**:`Azure function activity missing functionAppUrl.`

- **Ok:** Az Azure-függvény tevékenységdefiníciója nem teljes.

- **Javaslat:** Ellenőrizze a bemeneti AzureFunction tevékenység JSON-definíció "functionAppUrl" nevű tulajdonsággal rendelkezik.


### <a name="error-code--3610"></a>Hibakód: 3610

- **Üzenet**:`There was an error while calling endpoint.`

- **Ok**: Lehet, hogy a függvény URL-címe helytelen.

- **Javaslat:** Győződjön meg arról, hogy a JSON tevékenység "functionAppUrl" értéke helyes, és próbálkozzon újra.


### <a name="error-code--3611"></a>Hibakód: 3611

- **Üzenet**:`Azure function activity missing Method in JSON.`

- **Ok:** Az Azure-függvény tevékenységdefiníciója nem teljes.

- **Javaslat:** Ellenőrizze a bemeneti AzureFunction tevékenység JSON-definíció "metódus" nevű tulajdonság.


### <a name="error-code--3612"></a>Hibakód: 3612

- **Üzenet**:`Azure function activity missing LinkedService definition in JSON.`

- **Ok:** Az Azure-függvény tevékenységdefiníciója nem teljes.

- **Javaslat:** Ellenőrizze a bemeneti AzureFunction tevékenység JSON-definíció csatolt szolgáltatás adatait.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Hibakód: 4101

- **Üzenet**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Ok**: A(z) '%propertyName;' tulajdonság rossz formátumú vagy hiányzó definíciója.

- **Javaslat**: Ellenőrizze, hogy a(z) '%activityName;' tevékenységnek van-e "%propertyName"' tulajdonsága, amely helyes adatokkal van definiálva.


### <a name="error-code--4110"></a>Hibakód: 4110

- **Üzenet**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Ok:** Az AzureMLExecutePipeline tevékenységdefiníciója nem teljes.

- **Javaslat:** Ellenőrizze, hogy a bemeneti AzureMLExecutePipeline-tevékenység JSON-definíció jattt csatolt szolgáltatás adatait.


### <a name="error-code--4111"></a>Hibakód: 4111

- **Üzenet**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Ok**: Helytelen tevékenységdefiníció.

- **Javaslat:** Ellenőrizze, hogy a bemeneti AzureMLExecutePipeline-tevékenység JSON-definíciója helyes csatolt szolgáltatásadatokat tartalmaz-e.


### <a name="error-code--4112"></a>Hibakód: 4112

- **Üzenet**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Ok**: A(z) '%propertyName;' tulajdonság rossz formátumú vagy hiányzó definíciója.

- **Javaslat**: Ellenőrizze, hogy az összekapcsolt szolgáltatás rendelkezik-e a ('%propertyName;) tulajdonsággal, amely helyes adatokkal van definiálva.


### <a name="error-code--4121"></a>Hibakód: 4121

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ok:** Az Azure Machine Learning eléréséhez használt hitelesítő adatok lejárt.

- **Javaslat**: Ellenőrizze, hogy a hitelesítő adatok érvényesek-e, és próbálja meg újra


### <a name="error-code--4122"></a>Hibakód: 4122

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ok:** Az Azure Machine Learning csatolt szolgáltatásban megadott hitelesítő adat érvénytelen, vagy nincs engedélye a művelethez.

- **Javaslat:** Ellenőrizze, hogy a csatolt szolgáltatás hitelesítő adatai érvényesek-e, és rendelkezik-e engedéllyel az Azure Machine Learning eléréséhez.


### <a name="error-code--4123"></a>Hibakód: 4123

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ok:** A tevékenység tulajdonságai, például a pipelineParameters érvénytelenek az Azure ML-folyamathoz.

- **Javaslat:** Ellenőrizze a tevékenység tulajdonságainak értékét, hogy megfeleljen a linked szolgáltatásban megadott közzétett Azure ML-folyamat várható hasznos terhelésének.


### <a name="error-code--4124"></a>Hibakód: 4124

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ok:** A közzétett Azure ML-folyamat végpont nem létezik.

- **Javaslat:** Ellenőrizze, hogy a közzétett Azure Machine Learning-folyamat végpont, amely a Csatolt szolgáltatásban található az Azure Machine Learningben.


### <a name="error-code--4125"></a>Hibakód: 4125

- **Üzenet**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Ok**: Kiszolgálóhiba az Azure Machine Learningen.

- **Javaslat**: Próbálkozzon később. Ha probléma továbbra is problémát talál, forduljon az Azure Machine Learning csapatához.


### <a name="error-code--4126"></a>Hibakód: 4126

- **Üzenet**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Ok:** Az Azure ML-folyamat futtatása nem sikerült.

- **Javaslat:** Ellenőrizze az Azure Machine Learning további hibanaplókat, és javítsa ki a gépi tanulási folyamat.



## <a name="common"></a>Közös

### <a name="error-code--2103"></a>Hibakód: 2103

- **Üzenet**:`Please provide value for the required property '%propertyName;'.`

- **Ok**: A tulajdonság értéke nincs megadva, de a forgatókönyvben szükséges.

- **Javaslat**: Adja meg az üzenet értékét, majd próbálkozzon újra.


### <a name="error-code--2104"></a>Hibakód: 2104

- **Üzenet**:`The type of the property '%propertyName;' is incorrect.`

- **Ok**: A megadott tulajdonság típusa nem a várt módon történik.

- **Javaslat**: Javítsa ki a tulajdonság típusát, majd próbálkozzon újra.


### <a name="error-code--2105"></a>Hibakód: 2105

- **Üzenet**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Ok**: A tulajdonság értéke érvénytelen, vagy nem rendelkezik a várt formátummal.

- **Javaslat**: Kérjük, keresse meg a tulajdonság dokumentációját, és győződjön meg arról, hogy a megadott érték rendelkezik a várt formátummal és típussal.


### <a name="error-code--2106"></a>Hibakód: 2106

- **Üzenet**:`The storage connection string is invalid. %errorMessage;`

- **Ok**: A tároló kapcsolati karakterlánca érvénytelen vagy nem megfelelő formátumú.

- **Javaslat:** Tekintse meg az Azure Portalon, keresse meg a tárhelyet, másolja a kapcsolati karakterláncot, és illessze be a csatolt szolgáltatásba, és próbálkozzon újra.


### <a name="error-code--2108"></a>Hibakód: 2108

- **Üzenet**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Ok**: A kérés egy mögöttes probléma, például hálózati kapcsolat, DNS-hiba, kiszolgálói tanúsítvány-érvényesítés vagy időkérés miatt sikertelen.

- **Ajánlás**: A kérelem érvényesítéséhez használja a Hegedűs/Postman.Recommendation : Use Fiddler/Postman to validate the request.


### <a name="error-code--2110"></a>Hibakód: 2110

- **Üzenet**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Ok**: A tevékenységben megadott csatolt szolgáltatás hibás volt.

- **Javaslat:** Győződjön meg arról, hogy a csatolt szolgáltatástípus a tevékenység támogatott típusai közé tartozik. HDI-tevékenységek esetén például a csatolt szolgáltatás típusa lehet HDInsight vagy HDInsightOnDemand.


### <a name="error-code--2111"></a>Hibakód: 2111

- **Üzenet**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Ok**: A megadott tulajdonság típusa nem a várt módon történik.

- **Javaslat**: Javítsa ki a tulajdonság típusát, majd próbálkozzon újra.


### <a name="error-code--2112"></a>Hibakód: 2112

- **Üzenet**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Ok**: A felhőtípus nem támogatott, vagy nem határozható meg az EndpointSuffix tárolójának.

- **Javaslat**: Használja a tárhelyet egy másik felhőben, és próbálkozzon újra.


### <a name="error-code--2128"></a>Hibakód: 2128

- **Üzenet**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Ok**: Hálózati kapcsolat, DNS-hiba, kiszolgálói tanúsítvány érvényesítése vagy időmeghosszabbítás.

- **Javaslat:** Ellenőrizze, hogy a leadni kívánt végpont válaszol-e a kérésekre. Használhatja eszközök, mint a Hegedűs / Postman.



## <a name="custom"></a>Egyéni

Az alábbi táblázat az Azure Batch-re vonatkozik.
      
### <a name="error-code--2500"></a>Hibakód: 2500

- **Üzenet**:`Hit unexpected exception and execution failed.`

- **Ok:**`Can't launch command, or the program returned an error code.`

- **Javaslat**: Győződjön meg arról, hogy a végrehajtható fájl létezik. Ha a program elindult, győződjön meg arról, hogy *az stdout.txt* és *az stderr.txt* fájl fel lett töltve a tárfiókba. Ez egy jó gyakorlat, hogy bőséges naplókat bocsát ki a kódot a hibakeresés.


### <a name="error-code--2501"></a>Hibakód: 2501

- **Üzenet**:`Cannot access user batch account; please check batch account settings.`

- **Ok**: Helytelen kötegelt hozzáférési kulcs vagy készletnév.

- **Javaslat:** Ellenőrizze a készlet nevét és a Batch hozzáférési kulcsot a csatolt szolgáltatásban.


### <a name="error-code--2502"></a>Hibakód: 2502

- **Üzenet**:`Cannot access user storage account; please check storage account settings.`

- **Ok**: Helytelen tárfiók név vagy hozzáférési kulcs.

- **Javaslat:** Ellenőrizze a tárfiók nevét és a hozzáférési kulcsot a csatolt szolgáltatásban.


### <a name="error-code--2504"></a>Hibakód: 2504

- **Üzenet**:`Operation returned an invalid status code 'BadRequest'.`

- **Ok**: Túl sok fájl van a mappábanAz egyéni tevékenység útvonala. A resourceFiles teljes mérete nem lehet több 32 768 karakternél.

- **Javaslat**: Távolítsa el a felesleges fájlokat. Vagy zip őket, és adjunk hozzá egy csomagolja parancsot kivonat őket. Például, használja`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Hibakód: 2505

- **Üzenet**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Ok:** Az egyéni tevékenységek csak a hozzáférési kulcsot használó tárfiókokat támogatják.

- **Javaslat**: Olvassa el a hiba leírását.


### <a name="error-code--2507"></a>Hibakód: 2507

- **Üzenet**:`The folder path does not exist or is empty: ...`

- **Ok:** A megadott elérési úton lévő tárfiókban nincsenek fájlok.

- **Javaslat**: A mappa elérési útjának tartalmaznia kell a futtatni kívánt végrehajtható fájlokat.


### <a name="error-code--2508"></a>Hibakód: 2508

- **Üzenet**:`There are duplicate files in the resource folder.`

- **Ok**: Több azonos nevű fájl található a FolderPath különböző almappáiban.

- **Javaslat**: Az egyéni tevékenységek összeolvasztják a mappastruktúrát a FolderPath alatt.  Ha meg kell őriznie a mappaszerkezetet, zip a fájlokat, és kibontja őket az Azure Batch egy csomagolja parancsot. Például, használja`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Hibakód: 2509

- **Üzenet**:`Batch   url ... is invalid; it must be in Uri format.`

- **Ok**: A kötegelt URL-eknek hasonlónak kell lenniük a`https://mybatchaccount.eastus.batch.azure.com`

- **Javaslat**: Olvassa el a hiba leírását.


### <a name="error-code--2510"></a>Hibakód: 2510

- **Üzenet**:`An   error occurred while sending the request.`

- **Ok**: A köteg URL-címe érvénytelen.

- **Javaslat**: Ellenőrizze a köteg URL-címét.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Hibakód: 200

- **Üzenet**:`Unexpected error happened: '%error;'.`

- **Ok**: Belső szolgáltatási probléma van.

- **Ajánlás**: További segítségért forduljon az ADF ügyfélszolgálatához.


### <a name="error-code--201"></a>Hibakód: 201

- **Üzenet**:`JobType %jobType; is not found.`

- **Ok**: Az ADF nem támogatja az új feladattípust.

- **Ajánlás**: További segítségért forduljon az ADF támogatási csapatához.


### <a name="error-code--202"></a>Hibakód: 202

- **Üzenet**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Ok**: A hibaüzenetnek meg kell jelenlennie a hiba részleteiről.

- **Javaslat**: A hibaüzenet segíthet a probléma megoldásában. Ha nincs elég információ, további segítségért forduljon az ADF ügyfélszolgálatához.


### <a name="error-code--203"></a>Hibakód: 203

- **Üzenet**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Ok**: A hibaüzenetnek meg kell jelenlennie a hiba részleteiről.

- **Javaslat**: A hibaüzenet segíthet a probléma megoldásában. Ha nincs elég információ, további segítségért forduljon az ADF ügyfélszolgálatához.


### <a name="error-code--204"></a>Hibakód: 204

- **Üzenet**:`The resumption token is missing for runId '%runId;'.`

- **Ok**: Belső szolgáltatási probléma van.

- **Ajánlás**: További segítségért forduljon az ADF ügyfélszolgálatához.


### <a name="error-code--205"></a>Hibakód: 205

- **Üzenet**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Ok**: Hiba történt az igény szerinti HDI-fürt létrehozásakor.

- **Ajánlás**: További segítségért forduljon az ADF ügyfélszolgálatához.


### <a name="error-code--206"></a>Hibakód: 206

- **Üzenet**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Ok**: Belső probléma volt a szolgáltatást okozó szolgáltatással.

- **Ajánlás**: Ez lehet egy átmeneti kérdés. Próbálkozzon újra a feladattal, és ha a probléma továbbra is fennáll, további segítségért forduljon az ADF támogatási szolgálatához.


### <a name="error-code--207"></a>Hibakód: 207

- **Üzenet**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Ok**: Belső hiba történt, miközben megpróbálta meghatározni a régiót az elsődleges tárfiókból.

- **Javaslat**: Próbálkozzon egy másik tárolóval. Amennyiben ez nem elfogadható megoldás, további segítségért forduljon az ADF támogatási csapatához.


### <a name="error-code--208"></a>Hibakód: 208

- **Üzenet**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Ok**: Belső hiba történt a szolgáltatásegyszerű olvasása vagy az MSI-hitelesítés példányosítása közben.

- **Javaslat:** Fontolja meg egy egyszerű szolgáltatás, amely rendelkezik engedéllyel egy HDInsight-fürt létrehozásához a megadott előfizetésben, és próbálja újra. Ha ez nem elfogadható megoldás, további segítségért forduljon az ADF támogatási csapatához.


### <a name="error-code--2300"></a>Hibakód: 2300

- **Üzenet**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Ok:** Ha a hibaüzenet a következőhöz hasonló üzenetet tartalmaz: "A távoli név nem oldható fel.", ez azt jelentheti, hogy a megadott fürt URI-je érvénytelen.


- **Javaslat:** Győződjön meg arról, hogy a fürt nem lett törölve, és hogy a megadott URI helyes. Amikor böngészőben nyitja meg az URI-t, látnia kell az Ambari felhasználói felületét. Ha a fürt egy virtuális hálózat, az URI kell a magán URI. A megnyitásához használjon olyan virtuális gépet, amely ugyanannak a virtuális hálózatnak a része. További információt [ebben](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)a témakörben talál.
                  

<br>

- **Ok:** Ha a hibaüzenet az "Egy feladat megszakítása" üzenethez hasonló üzenetet tartalmaz, ez azt jelenti, hogy a feladat beküldése időtúlosebben lett meg.

- **Javaslat:** A probléma lehet általános HDInsight-kapcsolat vagy hálózati kapcsolat. Először ellenőrizze, hogy a HDInsight Ambari felhasználói felülete bármely böngészőből elérhető-e. Ellenőrizze, hogy a hitelesítő adatok továbbra is érvényesek-e. Ha önkiszolgáló integrált futásidejű (IR) használatával, győződjön meg róla, hogy ezt a virtuális gép vagy a gép, ahol a saját üzemeltetésű infravörös telepítve van. Ezután próbálja meg újra beküldeni a feladatot a Data Factory-ból. Ha továbbra is sikertelen, forduljon a Data Factory csapatához segítségért.

<br>

- **Ok:** Ha a hibaüzenet a "Felhasználói rendszergazda az Ambariban zárolva van" vagy a "Jogosulatlan: Ambari felhasználónév vagy jelszó helytelen" üzenethez hasonló üzenetet tartalmaz, ez azt jelenti, hogy a HDInsight hitelesítő adatai helytelenek vagy lejártak.

- **Javaslat**: Javítsa ki a hitelesítő adatokat, és telepítse újra a csatolt szolgáltatást. Először győződjön meg arról, hogy a hitelesítő adatok működnek a HDInsight-on, ha megnyitja a fürt URI-ját bármely böngészőben, és megpróbál bejelentkezni. Ha a hitelesítő adatok nem működnek, alaphelyzetbe állíthatja őket az Azure Portalon.

<br>

- **Ok:** Ha a hibaüzenet az "502 – webkiszolgálóhoz hasonló üzenetet tartalmaz, amely átjáróként vagy proxykiszolgálóként való működés közben érvénytelen választ kapott", a HDInsight szolgáltatás ezt a hibát adja vissza.


- **Javaslat:** Tekintse át az Azure HDInsight https://hdinsight.github.io/ambari/ambari-ui-502-error.htmlhibaelhárítási dokumentációját, például , https://hdinsight.github.io/spark/spark-thriftserver-errors.html, https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502.
                  

<br>

- **Ok:** Ha a hibaüzenet a következőhöz hasonló üzenetet tartalmaz: "Nem lehet kiszolgálni a küldési feladatkérést, mivel a templeton szolgáltatás túl sok feladatkéréssel van elfoglalva" vagy a "Queue root.joblauncher már rendelkezik 500 alkalmazással, nem tudja elfogadni az alkalmazás elküldését", ez azt jelenti, hogy túl sok feladat van elküldve a HDInsight-nak egyszerre.

- **Javaslat:** Fontolja meg a HDInsight-nak küldött egyidejű feladatok számának korlátozását. Tekintse meg a Data Factory tevékenység egyidejűség, ha a feladatok által küldött ugyanaz a tevékenység. Módosítsa az eseményindítókat úgy, hogy az egyidejű folyamatfuttatások elosztva az idő múlásával. Tekintse meg a HDInsight dokumentációját a templeton.parallellism.job.submit módosításához, ahogy azt a hiba sugallja.


### <a name="error-code--2301"></a>Hibakód: 2301

- **Üzenet**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Ok:** A HDInsight-fürt vagy -szolgáltatás problémákat okoz.


- **Javaslat:** Ez a hiba akkor fordul elő, ha az ADF nem kap választ a HDInsight-fürttől, amikor megpróbálja leadni a futó feladat állapotát. Ez lehet az oka a problémák a fürtön is, vagy HDInsight szolgáltatás előfordulhat, hogy kimaradás. További segítségért olvassa el https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guidea HDInsight hibaelhárítási dokumentációját, vagy forduljon a támogatásukhoz.
                


### <a name="error-code--2302"></a>Hibakód: 2302

- **Üzenet**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Ok:** A feladat elküldve hdi-fürtbe, és ott nem sikerült.

- **Javaslat**: Kövesse a fonalnaplók hivatkozást a tevékenység futtatási kimenetben, és keresse meg a HDI-kimenet hibáit. Szükség esetén forduljon a HDInsight-csapathoz.


### <a name="error-code--2303"></a>Hibakód: 2303

- **Üzenet**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Ok:** A feladat elküldve hdi-fürtbe, és ott nem sikerült.

- **Javaslat**: Kövesse a fonalnaplók hivatkozást a tevékenység futtatási kimenetben, és keresse meg a HDI-kimenet hibáit. Próbálja újra, vagy szükség esetén forduljon a HDInsight-csapathoz.


### <a name="error-code--2304"></a>Hibakód: 2304

- **Üzenet**:`MSI authentication is not supported on storages for HDI activities.`

- **Ok:** A HDI-alapú szolgáltatásban vagy HDI-tevékenységben használt tárolóhoz kötött szolgáltatás(ok) az MSI-hitelesítéssel vannak konfigurálva, amely nem támogatott.

- **Ajánlás:** Adjon meg teljes kapcsolati karakterláncokat a HDI-kapcsolattal összekapcsolt szolgáltatásban vagy HDI-tevékenységben használt tárfiókokhoz.


### <a name="error-code--2305"></a>Hibakód: 2305

- **Üzenet**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Ok:** A HDI-fürt kapcsolati adatai helytelenek, a megadott felhasználó nem rendelkezik a szükséges művelet végrehajtásához szükséges engedélyekkel, vagy a HDInsight szolgáltatásnak problémái voltak az ADF-től érkező kérésekre való válaszadással.

- **Javaslat**: Kérjük, ellenőrizze, hogy a felhasználói adatok helyesek-e. Ellenőrizze azt is, hogy a HDI-fürt Ambari felhasználói felülete megnyitható-e a virtuális gépről egy böngészőben, ahol az infravörös rendszer telepítve van a saját üzemeltetésű infravörös szolgáltatás esetén, vagy bármely gépről megnyitható, ha az Azure IR.


### <a name="error-code--2306"></a>Hibakód: 2306

- **Üzenet**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Ok**: A parancsfájlművelethez megadott json érvénytelen.


- **Javaslat**: A hibaüzenet nek segítenie kell a probléma azonosításában. Javítsa ki a json konfigurációt, majd próbálkozzon újra. További https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service információkért ellenőrizze.
                


### <a name="error-code--2310"></a>Hibakód: 2310

- **Üzenet**:`Failed to submit Spark job. Error: '%message;'`

- **Ok:** Az ADF megpróbált létrehozni egy köteget egy Spark-fürtön a Livy API használatával (livy/batch), de hibaüzenetet kapott.

- **Javaslat**: A probléma megoldásához kövesse a hibaüzenetet. Ha nincs elegendő információ a megoldáshoz, lépjen kapcsolatba a HDI-csapattal, és adja meg nekik a kötegazonosítót és a feladatazonosítót, amely megtalálható az ADF figyelési lapján futó kimenet i tevékenységben.


### <a name="error-code--2312"></a>Hibakód: 2312

- **Üzenet**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Ok:** A feladat nem sikerült a HDInsight Spark-fürtön.

- **Javaslat:** Kövesse a tevékenységek futtatásához vezető Kimenet az ADF figyelési lapon a HDInsight Spark-fürtön való futtatás hibaelhárításához. További segítségért forduljon a HDInsight támogatási csapatához.


### <a name="error-code--2313"></a>Hibakód: 2313

- **Üzenet**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Ok:** A köteg et törölték a HDInsight Spark-fürtön.

- **Javaslat:** A HDInsight Spark-fürt kötegek hibaelhárítása. További segítségért forduljon a HDInsight támogatási szolgálatához. 


### <a name="error-code--2328"></a>Hibakód: 2328

- **Üzenet**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Ok:**`The error message should show the details of what went wrong.`

- **Javaslat**: A hibaüzenet segíthet a probléma megoldásában.


### <a name="error-code--2329"></a>Hibakód: 2329

- **Üzenet**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Ok**: A hibaüzenetnek meg kell jelenlennie a hiba részleteiről.

- **Javaslat**: A hibaüzenet segíthet a probléma megoldásában.


### <a name="error-code--2331"></a>Hibakód: 2331

- **Üzenet**:`The file path should not be null or empty.`

- **Ok**: A megadott fájlelérési út üres.

- **Javaslat**: Adjon meg elérési utat egy létező fájlhoz.


### <a name="error-code--2340"></a>Hibakód: 2340

- **Üzenet**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Ok:** A HDInsightOnDemand csatolt szolgáltatás nem támogatja a SelfHosted ir-n keresztüli végrehajtást.

- **Javaslat:** Válasszon ki egy Azure IR-t, és próbálkozzon újra.


### <a name="error-code--2341"></a>Hibakód: 2341

- **Üzenet**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Ok**: A megadott URL formátum nem megfelelő formátumú.

- **Javaslat**: Javítsa ki a fürt URL-címét, majd próbálkozzon újra.


### <a name="error-code--2342"></a>Hibakód: 2342

- **Üzenet**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Ok**: A megadott hitelesítő adatok hibásak a fürthöz, vagy hálózati konfigurációs vagy csatlakozási probléma merült fel, vagy az infravörös kapcsolat problémái vannak a fürthöz való csatlakozással.

- **Ajánlás**:  
      1. Ellenőrizze, hogy a hitelesítő adatok helyesek-e a HDInsight-fürt Ambari felhasználói felületének böngészőben való megnyitásával.
      2. Ha a fürt virtuális hálózatban van, és saját üzemeltetésű infravörös állapotot használ, a HDI URL-címnek a virtuális hálózatok privát URL-címének kell lennie, ami azt jelenti, hogy a fürt neve után "int" -nek kell lennie. Példáulhttps://mycluster.azurehdinsight.net/a " " "-t "https://mycluster-int.azurehdinsight.net/"-ra kell változtatni.
      2. Ha a fürt virtuális hálózatban van, saját üzemeltetésű infravörös kapcsolat van használatban, és a magánURL-t használják, és a kapcsolat továbbra is sikertelen, akkor a virtuális gép, ahol az infravörös telepítve van, problémái voltak a HDI-hez való csatlakozással. Csatlakozzon ahhoz a virtuális géphez, amelyen az infravörös rendszer telepítve van, és nyissa meg az Ambari felhasználói felületet egy böngészőben. Használja a fürt privát URL-címét. Ennek a kapcsolatnak a böngészőből kell működnie. Ha nem, további segítségért forduljon a HDInsight támogatási csapatához.
      3. Ha a saját üzemeltetésű infravörös nem használatos, majd a HDI-fürt nyilvánosan elérhetőnek kell lennie. Nyissa meg az Ambari felhasználói felületet a böngészőben, és győződjön meg róla, hogy megnyílik. Ha bármilyen probléma merül fel a fürttel vagy a rajta lévő szolgáltatásokkal kapcsolatban, forduljon segítségért a HDInsight támogatási csapatához.
      Így általában az ADF-alapú szolgáltatásban használt HDI-fürt URL-címének elérhetőnek kell lennie az ADF IR (saját üzemeltetésű vagy Azure) számára ahhoz, hogy a tesztkapcsolat áthaladjon, és a futtatások működjenek. Ez könnyen ellenőrizhető, ha megnyitja az URL-címet egy böngészőből virtuális gépről vagy bármely nyilvános gépről.
    


### <a name="error-code--2343"></a>Hibakód: 2343

- **Üzenet**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Ok:** A felhasználónév vagy a jelszó üres.

- **Javaslat:** Adja meg a megfelelő hitelesítő adatokat a HDI-hez való csatlakozáshoz, és próbálkozzon újra.


### <a name="error-code--2345"></a>Hibakód: 2345

- **Üzenet**:`Failed to read the content of the hive script. Error: '%message;'`

- **Ok**: A parancsfájl nem létezik, vagy az ADF nem tudott csatlakozni a parancsfájl helyéhez.

- **Javaslat**: Ellenőrizze, hogy a parancsfájl létezik-e, és a társított csatolt szolgáltatás megfelelő hitelesítő adatokkal rendelkezik-e a kapcsolathoz.


### <a name="error-code--2346"></a>Hibakód: 2346

- **Üzenet**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Ok**: Az ADF odbc-kapcsolatot próbált létesíteni a HDI-fürttel, de hiba miatt nem sikerült.

- **Javaslat**: A hibaüzenet és a hibakód segíthet az ODBC csatlakozási hibák elhárításában. Abban az esetben, ha nem elegendőek a probléma megoldásához, forduljon az Azure HDInsight csapatához támogatásért.


### <a name="error-code--2347"></a>Hibakód: 2347

- **Üzenet**:`Hive execution through ODBC failed with error message '%message;'.`

- **Ok:** Az ADF a hive-parancsfájlt odbc-kapcsolaton keresztül küldte el a HDI-fürtnek a HDI-fürtnek, és a parancsfájl nem sikerült HDI-n.

- **Javaslat**: A hive-parancsfájl végrehajtása nem sikerült a HDI-fürtön, és a hibaüzenetek és a hibakód segíthet a hibaelhárításban. Abban az esetben, ha nem elegendőek a probléma megoldásához, forduljon az Azure HDInsight csapatához támogatásért.


### <a name="error-code--2348"></a>Hibakód: 2348

- **Üzenet**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Ok**: A tárolóhoz csatolt szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat:** Csak a teljes kapcsolati karakterláncok támogatottak a hdi-tevékenységek fő, tároláshoz kapcsolódó szolgáltatásában. Ellenőrizze, hogy nem msi hitelesítést vagy alkalmazásokat használ.Please make sure you are not using MSI auth or applications.


### <a name="error-code--2350"></a>Hibakód: 2350

- **Üzenet**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Ok**: A fájlokat tárolóhoz való csatlakozáshoz megadott hitelesítő adatok helytelenek, vagy a fájlok nem léteznek ott.

- **Javaslat**: Ez a hiba akkor fordul elő, ha az ADF előkészíti a HDI-tevékenységek előkészítését. Megpróbálja másolni a fájlokat a fő tárolóba, mielőtt elküldi a feladatot a HDI-nek. Győződjön meg arról, hogy a fájlok a megadott helyen vannak, a tárolókapcsolat helyes. Az ADF HDI-tevékenységek nem támogatják az MSI-hitelesítést a HDI-tevékenységekhez kapcsolódó tárfiókokon, ezért győződjön meg arról, hogy ezek a csatolt szolgáltatások teljes kulcsokkal rendelkeznek, vagy az Azure Key Vault ot használják.


### <a name="error-code--2351"></a>Hibakód: 2351

- **Üzenet**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Ok**: A fájl nem létezik a megadott elérési úton.

- **Javaslat**: Ellenőrizze, hogy a fájl valóban létezik-e, és a fájlra mutató kapcsolatadatokkal rendelkező csatolt szolgáltatás megfelelő hitelesítő adatokkal rendelkezik.


### <a name="error-code--2352"></a>Hibakód: 2352

- **Üzenet**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Ok**: A fájltárolóhoz csatolt szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat**: Ellenőrizze, hogy a fájltárolóhoz csatolt szolgáltatás tulajdonságai megfelelően vannak-e konfigurálva.


### <a name="error-code--2353"></a>Hibakód: 2353

- **Üzenet**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Ok**: A parancsfájltárolóhoz csatolt szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat: Győződjön**meg arról, hogy a parancsfájltároló-kapcsolt szolgáltatás tulajdonságai megfelelően vannak konfigurálva.


### <a name="error-code--2354"></a>Hibakód: 2354

- **Üzenet**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Ok**: A tevékenység nem támogatja a tárolóhoz csatolt szolgáltatástípust.

- **Javaslat:** Győződjön meg arról, hogy a kiválasztott összekapcsolt szolgáltatás rendelkezik a tevékenység hez támogatott típusok egyikével. A HDI-tevékenységek támogatják az AzureBlobStorage és az AzureBlobFSStorage kapcsolódó szolgáltatásokat.


### <a name="error-code--2355"></a>Hibakód: 2355

- **Üzenet**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Ok**: A commandEnvironment számára megadott helytelen.

- **Ajánlás**:  
      Ellenőrizze, hogy a megadott érték \"hasonló-e a következőhöz: commandEnvironment\": [ \"variableName=variableValue\" ] És minden változó csak egyszer jelenik meg a listában.
    


### <a name="error-code--2356"></a>Hibakód: 2356

- **Üzenet**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Ok**: A változó kétszer lett megadva a commandEnvironment parancsban.

- **Ajánlás**:  
      Ellenőrizze, hogy a megadott érték \"hasonló-e a következőhöz: commandEnvironment\": [ \"variableName=variableValue\" ] És minden változó csak egyszer jelenik meg a listában.
    


### <a name="error-code--2357"></a>Hibakód: 2357

- **Üzenet**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Ok**: A megadott hitelesítő adatok helytelenek.

- **Javaslat**: Ellenőrizze a kapcsolatadatait az ADLS Gen 1 kapcsolt szolgáltatásban, és győződjön meg arról, hogy a tesztkapcsolat sikeres.


### <a name="error-code--2358"></a>Hibakód: 2358

- **Üzenet**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Ok**: A "TimeToLive" kötelező tulajdonsághoz megadott érték formátuma érvénytelen. 

- **Javaslat**: Frissítse az értéket a javasolt tartományban, majd próbálkozzon újra.


### <a name="error-code--2359"></a>Hibakód: 2359

- **Üzenet**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Ok**: A "szerepkörök" tulajdonsághoz megadott érték érvénytelen.

- **Javaslat**: Frissítse az értéket, hogy az egyik javaslat legyen, majd próbálkozzon újra.


### <a name="error-code--2360"></a>Hibakód: 2360

- **Üzenet**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Ok**: A HCatalogLinkedService megadott kapcsolati karakterlánca érvénytelen.

- **Javaslat:** Frissítse az értéket a megfelelő Azure SQL-kapcsolati karakterláncra, majd próbálkozzon újra.


### <a name="error-code--2361"></a>Hibakód: 2361

- **Üzenet**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Ok**: A fürt létrehozása nem sikerült, és az ADF nem kapott hibaüzenetet a HDInsight szolgáltatástól.

- **Javaslat:** Nyissa meg az Azure Portalon, és próbálja megtalálni a HDI-erőforrás megadott nevet, és ellenőrizze a kiépítési állapotát. További segítségért forduljon a HDInsight támogatási csapatához.


### <a name="error-code--2362"></a>Hibakód: 2362

- **Üzenet**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Ok:** A megadott további tárhely nem volt Azure Blob storage.

- **Javaslat:** Az Azure Blob-tárfiók biztosítása további tárként HDInsight igény szerint kapcsolt szolgáltatás.



## <a name="web-activity"></a>Webes tevékenység

### <a name="error-code--2128"></a>Hibakód: 2128

- **Üzenet**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Ok**: Hálózati kapcsolat, DNS-hiba, kiszolgálói tanúsítvány érvényesítése vagy időmeghosszabbítás.

- **Javaslat:** Ellenőrizze, hogy a leadni kívánt végpont válaszol-e a kérésekre. Használhatja eszközök, mint a Hegedűs / Postman.


### <a name="error-code--2108"></a>Hibakód: 2108

- **Üzenet**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Ok**: A kérés egy mögöttes probléma, például hálózati kapcsolat, DNS-hiba, kiszolgálói tanúsítvány-érvényesítés vagy időkérés miatt sikertelen.

- **Ajánlás**: A kérelem érvényesítéséhez használja a Hegedűs/Postman.Recommendation : Use Fiddler/Postman to validate the request.
<br>


#### <a name="more-details"></a>További részletek
A Fiddler használata a figyelt webalkalmazás HTTP-munkamenetének létrehozásához:

1. Töltse le, telepítse és nyissa meg [a Fiddler](https://www.telerik.com/download/fiddler).

1. Ha a webalkalmazás HTTPS-t használ, nyissa meg **az Eszközök** > **hegedűs beállításai** > **HTTPS lehetőséget.** Válassza **a HTTPS CONNECT-ek rögzítése** és **a HTTPS-forgalom visszafejtése lehetőséget.**

   ![Hegedűs opciók](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Ha az alkalmazás TLS/SSL-tanúsítványokat használ, adja hozzá a Fiddler tanúsítványt az eszközhöz. Nyissa meg **az Eszközök** > **hegedűeszköz beállításai** > **HTTPS-műveletek** > **HTTPS** > **A főtanúsítvány exportálása az asztalra**lehetőséget.

1. Kapcsolja ki a rögzítést a > **Fájlrögzítési forgalom oldalon.** **File** Vagy nyomja meg **az F12 gombot.**

1. Törölje a böngésző gyorsítótárát, hogy az összes gyorsítótárazott elem eltávolításra kerüljön, és újra le kell tölteni.

1. Kérelem létrehozása:

   1. Válassza a **Zeneszerző** lapot.

   1. Állítsa be a HTTP metódust és az URL-címet.
   
   1. Szükség esetén adjon hozzá fejléceket és egy kérelemtörzset.

   1. Válassza a **Végrehajtás** lehetőséget.

1. Kapcsolja be újra a forgalom rögzítését, és hajtsa végre a problémás tranzakciót az oldalon.

1. Nyissa meg az Összes munkamenet **fájlmentése** > **( Fájlmentése az összes munkamenetet**) ( Fájlmentése ) (**Fájlmentés az** > 

További információ: [Első lépések a Hegedűsrel.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>További lépések

További hibaelhárítási segítségért próbálkozzon az alábbi forrásokkal:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatáskérések](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack túlcsordulás fórum Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter információk a Data Factory-ról](https://twitter.com/hashtag/DataFactory)


            
