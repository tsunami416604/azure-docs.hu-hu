---
title: A Azure Data Factory hibáinak megoldása | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory külső vezérlési tevékenységeit.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 1995ce2a91bfbc115f80c99687cc84b52ef614ec
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950112"
---
# <a name="troubleshoot-azure-data-factory"></a>Hibakeresés Azure Data Factory

Ez a cikk a Azure Data Factory külső ellenőrzési tevékenységeinek gyakori hibaelhárítási módszereit vizsgálja.

## <a name="azure-databricks"></a>Azure Databricks

| Hibakód | Hibaüzenet                                          | Leírás                             | Ajánlás                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | 403-es hiba.                                                    | Az Databricks hozzáférési jogkivonat lejárt.                         | Alapértelmezés szerint a Databricks hozzáférési token 90 napig érvényes.  Hozzon létre egy új jogkivonatot, és frissítse a társított szolgáltatást. |
| 3201           | Hiányzó kötelező mező: Settings. Task. notebook_task. notebook_path | Helytelen szerzői műveletek: A jegyzetfüzet elérési útja nincs megfelelően megadva. | A jegyzetfüzet elérési útjának megadása a Databricks tevékenységben. |
| 3201           | Fürt... nem létezik.                                 | Szerzői hiba: A Databricks-fürt nem létezik vagy törölték. | Ellenőrizze, hogy létezik-e a Databricks-fürt. |
| 3201           | A Python-fájl URI-ja érvénytelen.... Tekintse meg a Databricks felhasználói útmutatóját a támogatott URI-sémák számára. | Rossz szerzői műveletek.                                                | Adjon meg abszolút elérési utakat a munkaterület-címzési sémák vagy `dbfs:/folder/subfolder/foo.py` a Databricks fájlrendszerben tárolt fájlokhoz. |
| 3201           | {0}A LinkedService-nek kötelező tulajdonságokkal kell rendelkeznie a tartomány-és accessToken. | Rossz szerzői műveletek.                                                | Ellenőrizze a [társított szolgáltatás definícióját](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}A LinkedService meg kell adni a meglévő fürt AZONOSÍTÓját vagy az új fürt adatait a létrehozáshoz. | Rossz szerzői műveletek.                                                | Ellenőrizze a [társított szolgáltatás definícióját](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | A csomópont típusú Standard_D16S_v3 nem támogatott. Támogatott csomópont-típusok:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, standard D3 v2, standard D8 v3, standard D16 v3, standard d32 v3, standard D64 v3, standard D12 v2, standard D13 v2, Standard D14 v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, standard H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, standard nc12, standard nc24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | Rossz szerzői műveletek.                                                | Tekintse meg a hibaüzenetet.                                          |
| 3201           | Érvénytelen notebook_path:... Jelenleg csak az abszolút elérési utak támogatottak. Az elérési útnak a következővel kell kezdődnie: "/". | Rossz szerzői műveletek.                                                | Tekintse meg a hibaüzenetet.                                          |
| 3202           | Az elmúlt 3600 másodpercben már 1000 feladat lett létrehozva, ami túllépte a maximálisan megengedett sebességet:   1000 feladatok létrehozása 3600 másodpercenként. | Túl sok Databricks fut egy órán belül.                         | A Databricks-munkaterületet használó összes folyamatnál keresse meg a feladatok létrehozási arányát.  Ha a folyamatok túl sok Databricks-t futtatnak összesítésben, telepítsen át bizonyos folyamatokat egy új munkaterületre. |
| 3202           | Nem sikerült elemezni a kérelem objektumát: A rendszer a "Key" és a "value" kifejezést várta a JSON-Térkép mező base_parameters, "Key:"... " helyett. | Szerzői hiba: A paraméterhez nincs megadva érték.         | Vizsgálja meg a folyamat JSON-fájlját, és győződjön meg arról, hogy a baseParameters-jegyzetfüzet összes paramétere nem üres értéket ad meg. |
| 3202           | Felhasználó: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` nincs jogosultsága a fürt elérésére. | A hozzáférési jogkivonatot létrehozó felhasználó nem férhet hozzá a társított szolgáltatásban megadott Databricks-fürthöz. | Győződjön meg arról, hogy a felhasználó rendelkezik a szükséges engedélyekkel a munkaterületen.   |
| 3203           | A fürt leállított állapotban van, nem érhető el a feladatok fogadásához. Javítsa ki a fürtöt, vagy próbálkozzon újra később. | A fürt meg lett szakítva.    Az interaktív fürtök esetében ez lehet a verseny feltétele. | A hiba elkerülésének legjobb módja a feladatok fürtök használata.             |
| 3204           | A feladatok végrehajtása nem sikerült.  | A hibaüzenetek különböző problémákat jeleznek, például egy váratlan fürt állapotát vagy egy adott tevékenységet. Leggyakrabban egyetlen hibaüzenet sem jelenik meg.                                                          | –                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

A következő táblázat az U-SQL-re vonatkozik.

| Hibakód         | Hibaüzenet                                                | Leírás                                          | Ajánlás                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | A hozzáférési jogkivonat nem a megfelelő bérlőtől származik.                    | Helytelen Azure Active Directory (Azure AD) bérlő.                                         | Az Azure Data Lake Analyticshoz való hozzáféréshez használt egyszerű szolgáltatásnév egy másik Azure AD-bérlőhöz tartozik. Hozzon létre egy új szolgáltatásnevet ugyanabban a bérlőben, mint a Data Lake Analytics fiókot. |
| 2711, 2705, 2704 | Tilos. Az ACL-ellenőrzés sikertelen volt. Vagy az erőforrás nem létezik, vagy a felhasználó nem jogosult a kért művelet végrehajtására.<br/><br/>A felhasználó nem fér hozzá Data Lake Storehoz.  <br/><br/>A felhasználó nem rendelkezik jogosultsággal a Data Lake Analytics használatára. | Az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban. | Győződjön meg arról, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott egyszerű szolgáltatásnév vagy tanúsítvány hozzáfér-e a Data Lake Analytics fiókhoz és a gyökérmappa alapértelmezett Data Lake Storage példányához. |
| 2711                 | Nem található a (z) "Azure Data Lake Store" fájl vagy mappa.       | Az U-SQL-fájl elérési útja helytelen, vagy a társított szolgáltatás hitelesítő adatai nem rendelkeznek hozzáféréssel. | Ellenőrizze a társított szolgáltatásban megadott elérési utat és hitelesítő adatokat. |
| 2707                 | Nem oldható fel a AzureDataLakeAnalytics fiókja. Tekintse meg a "AccountName" és a "DataLakeAnalyticsUri". | A társított szolgáltatás Data Lake Analytics fiókja nem megfelelő.                  | Ellenőrizze, hogy a megfelelő fiók van-e megadva.             |
| 2703                 | Hiba azonosítója: E_CQO_SYSTEM_INTERNAL_ERROR (vagy bármely olyan hiba, amely a következővel kezdődik: "Error ID:"). | A hiba Data Lake Analytics.                                    | A példához hasonló hiba azt jelenti, hogy a feladatot elküldték Data Lake Analyticsba, és a szkript sikertelen volt. Data Lake Analytics vizsgálata. A portálon lépjen a Data Lake Analytics fiókra, és keresse meg a feladatot a Data Factory tevékenység futtatási AZONOSÍTÓjának használatával (nem a folyamat futtatási AZONOSÍTÓját). A feladattal kapcsolatban további információ található a hibával kapcsolatban, és segítséget nyújt a hibák megoldásához. Ha a megoldás nem egyértelmű, lépjen kapcsolatba a Data Lake Analytics támogatási csapatával, és adja meg a feladatokhoz tartozó URL-címet, amely tartalmazza a fiók nevét és a AZONOSÍTÓJÚ feladatot. |
| 2709                 | Jelenleg nem fogadjuk el a feladatot. A fiókhoz tartozó várólistán lévő feladatok maximális száma 200. | Ezt a hibát a Data Lake Analytics szabályozása okozta.                                           | Csökkentse Data Lake Analytics elküldött feladatok számát a tevékenységek Data Factory eseményindítók és Egyidejűség beállításainak módosításával. Vagy növelje Data Lake Analytics korlátozásait. |
| 2709                 | A rendszer elutasította a feladatot, mert 24 au-t igényel. Ez a fiók rendszergazda által definiált házirendje megakadályozza, hogy a feladatok több mint 5 au-t használjanak. | Ezt a hibát a Data Lake Analytics szabályozása okozta.                                           | Csökkentse Data Lake Analytics elküldött feladatok számát a tevékenységek Data Factory eseményindítók és Egyidejűség beállításainak módosításával. Vagy növelje Data Lake Analytics korlátozásait. |



## <a name="azure-functions"></a>Azure Functions

| Hibakód | Hibaüzenet                           | Leírás                                                  | Ajánlás                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | A válasz tartalma nem érvényes JObject. | A hívott Azure-függvény nem adott vissza JSON-adattartalmat a válaszban. Az Data Factory Azure functions tevékenység csak a JSON-válaszok tartalmát támogatja. | Frissítse az Azure-függvényt egy érvényes JSON-adattartalom visszaküldéséhez. Például C# egy `(ActionResult)new<OkObjectResult("{`függvény visszatérhet\"\"\"a következő azonosítóval:123`}");`. \" |
| 3600         | Érvénytelen HttpMethod: "...".               | Az Azure Function tevékenység nem támogatja a tevékenység-adattartalomban megadott HTTP-metódust. | Használjon támogatott HTTP-metódust, például PUT, POST, GET, DELETE, OPTIONs, HEAD vagy TRACE. |



## <a name="custom"></a>Egyéni

A következő táblázat a Azure Batchra vonatkozik.

| Hibakód | Hibaüzenet                                                | Leírás                                                  | Ajánlás                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | Váratlan kivétel történt, és a végrehajtás nem sikerült.             | A parancs nem indítható el, vagy a program hibakódot adott vissza. | Győződjön meg arról, hogy a végrehajtható fájl létezik. Ha a program elindult, győződjön meg róla, hogy az *StdOut. txt* és a *stderr. txt fájl* fel lett töltve a Storage-fiókba. Érdemes lehet bőséges naplókat kibocsátani a kódban a hibakereséshez. |
| 2501         | Nem lehet hozzáférni a felhasználói batch-fiókhoz; Ellenőrizze a Batch-fiók beállításait. | A Batch-hozzáférési kulcs vagy a készlet neve helytelen.            | Ellenőrizze a készlet nevét és a Batch-elérési kulcsot a társított szolgáltatásban. |
| 2502         | A felhasználói Storage-fiók nem érhető el; Ellenőrizze a Storage-fiók beállításait. | Helytelen a Storage-fiók neve vagy a hozzáférési kulcs.       | Ellenőrizze a Storage-fiók nevét és a hozzáférési kulcsot a társított szolgáltatásban. |
| 2504         | A művelet érvénytelen állapotkódot adott vissza: "BadRequest".     | Túl sok fájl szerepel az egyéni tevékenység folderPath. A resourceFiles teljes mérete nem lehet hosszabb 32 768 karakternél. | Távolítsa el a szükségtelen fájlokat. Vagy zip, és adjon hozzá egy unzip parancsot a kinyeréséhez. Használja például a következőt:`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | A közös hozzáférési aláírás nem hozható létre, kivéve, ha a rendszer a fiók kulcsának hitelesítő adatait használja. | Az egyéni tevékenységek csak a hozzáférési kulcsot használó tárolási fiókokat támogatják. | Tekintse meg a hiba leírását.                                            |
| 2507         | A mappa elérési útja nem létezik vagy üres:...            | A megadott elérési úton egyetlen fájl sem található a Storage-fiókban.       | A mappa elérési útjának tartalmaznia kell a futtatni kívánt végrehajtható fájlokat. |
| 2508         | Az erőforrás mappájában Duplikált fájlok találhatók.               | Több azonos nevű fájl található a folderPath különböző almappáiban. | Az egyéni tevékenységek a folderPath alatt lelapulják a mappák struktúráját.  Ha meg kell őriznie a mappa szerkezetét, zip-fájlt kell kibontania Azure Batch egy unzip paranccsal. Használja például a következőt:`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | Batch URL-címe... Érvénytelen; URI formátumúnak kell lennie.         | A Batch URL-címeinek a következőhöz hasonlónak kell lenniük`https://mybatchaccount.eastus.batch.azure.com` | Tekintse meg a hiba leírását.                                            |
| 2510         | Hiba történt a kérelem elküldésekor.               | A Batch URL-címe érvénytelen.                                         | Ellenőrizze a Batch URL-címét.                                            |

## <a name="hdinsight"></a>HDInsight

A következő táblázat a Spark, a kaptár, a MapReduce, a Pig és a Hadoop streaming rendszerre vonatkozik.

| Hibakód | Hibaüzenet                                                | Leírás                                                  | Ajánlás                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | A Hadoop-feladatok beküldése sikertelen volt. Hiba: A távoli név nem oldható fel. <br/><br/>A fürt nem található. | A megadott fürt URI azonosítója érvénytelen.                              | Győződjön meg arról, hogy a fürt nem lett törölve, és hogy a megadott URI helyes. Amikor megnyitja az URI-t egy böngészőben, megjelenik a Ambari felhasználói felülete. Ha a fürt virtuális hálózatban található, az URI-nak a privát URI-nak kell lennie. A megnyitásához használjon egy azonos virtuális hálózat részét képező virtuális GÉPET. További információ: [közvetlen kapcsolódás Apache Hadoop szolgáltatásokhoz](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | A Hadoop-feladatok beküldése sikertelen volt. Job:..., fürt:.../. Hiba: Egy feladat meg lett szakítva. | A feladatok beküldésének időkorlátja lejárt.                         | A probléma lehet általános HDInsight-kapcsolat vagy hálózati kapcsolat. Először ellenőrizze, hogy a HDInsight Ambari felhasználói felülete elérhető-e bármely böngészőből. Ellenőrizze, hogy a hitelesítő adatai még érvényesek-e. Ha saját üzemeltetésű integrált futtatókörnyezetet (IR) használ, ügyeljen arra, hogy azt a virtuális gépet vagy gépet használja, ahol a saját üzemeltetésű integrációs modul telepítve van. Ezután próbálja meg újra elküldeni a feladatot Data Factory újra. Ha továbbra sem sikerül, forduljon a Data Factory csapatához. |
| 2300         | Jogosulatlan   A Ambari Felhasználónév vagy a jelszó helytelen.  <br/><br/>Jogosulatlan   A felhasználói rendszergazda ki van zárva a Ambari.   <br/><br/>403 – Tiltott: A hozzáférés megtagadva. | A HDInsight hitelesítő adatai helytelenek vagy lejártak. | Javítsa ki a hitelesítő adatokat, és telepítse újra a társított szolgáltatást. Először győződjön meg arról, hogy a hitelesítő adatok HDInsight működnek a fürt URI-ja megnyitásával bármely böngészőben, és próbáljon bejelentkezni. Ha a hitelesítő adatok nem működnek, alaphelyzetbe állíthatja azokat a Azure Portal. |
| 2300,   2310 | 502 – Az átjárói vagy proxykiszolgálói feladatokat ellátó webkiszolgáló érvénytelen választ kapott.       <br/>Hibás átjáró. | Ez a hiba a HDInsight.                               | Ez a hiba a HDInsight-fürtből származik. További információ: [Ambari felhasználói felület 502 hiba](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 hiba a Spark](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)-beli szolgáltatáshoz való csatlakozáshoz, [502-hibák a Spark-hoz](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)való csatlakozáshoz és [a Application Gateway hibás átjárókkal kapcsolatos hibák elhárítása](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502). |
| 2300         | A Hadoop-feladatok beküldése sikertelen volt. Job:..., fürt:... Hiba: {\"Error\":\"nem lehet kiszolgálni a küldési feladattípust, mert a Templeton szolgáltatás túl sok küldési feladattípust foglal le. Várjon egy ideig, mielőtt újrapróbálkozik a művelettel. A párhuzamos kérelmek konfigurálásához tekintse meg a config Templeton. parallellism. job. Submit fájlt.  <br/><br/>A Hadoop-feladatok beküldése sikertelen volt. Feladat 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, fürt: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   Hiba: {\"Error\":\"Java. IO. IOException: org. Apache. Hadoop. fonál. kivételek. YarnException: Nem sikerült elküldeni a application_1561147195099_3730 a következő FONALba: org. Apache. Hadoop. Security. AccessControlException: A várólista gyökérkönyvtára. a joblauncher már rendelkezik 500-alkalmazással, nem fogadhatja el az alkalmazás beküldését: application_1561147195099_3730 \ | Túl sok feladat van elküldve a HDInsight egyszerre. | Érdemes lehet korlátozni a HDInsight küldött egyidejű feladatok számát. Ha ugyanaz a tevékenység küldi el a feladatokat, tekintse meg Data Factory tevékenység egyidejűségét. Módosítsa az eseményindítókat úgy, hogy az egyidejű folyamat-futtatások egyszerre legyenek kiosztva. A hiba feltételeit a `templeton.parallellism.job.submit` HDInsight dokumentációjában találja. |
| 2303, 2347 | A Hadoop feladatainak végrehajtása sikertelen volt. kilépési kód: "5". További részletekért tekintse meg a következőt: "wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr".  <br/><br/>A kaptár végrehajtása a következő hibakóddal meghiúsult: "UserErrorHiveOdbcCommandExecutionFailure".   További részletekért tekintse meg a következőt: "wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out". | A rendszer elküldte a feladatot a HDInsight-nek, és az HDInsight-on meghiúsult. | A feladatot sikeresen elküldte a HDInsight. Sikertelen volt a fürtön. Nyissa meg a feladatot és a naplókat a HDInsight Ambari felhasználói felületén, vagy nyissa meg a fájlt a tárolóból, ha a hibaüzenetet javasolja. A fájl a hiba részleteit jeleníti meg. |
| 2328         | Belső kiszolgálóhiba történt a kérelem feldolgozása során. Próbálkozzon újra a kéréssel, vagy forduljon az ügyfélszolgálathoz. | Ez a hiba az igény szerinti HDInsight történik.                              | Ez a hiba a HDInsight szolgáltatásból származik, amikor a HDInsight kiépítés sikertelen. Forduljon a HDInsight csapathoz, és adja meg az igény szerinti fürt nevét. |
| 2310         | java.lang.NullPointerException                               | Ez a hiba akkor fordul elő, ha a feladatot egy Spark-fürthöz küldi el a rendszer.      | Ez a kivétel a HDInsight származik. Elrejti a tényleges problémát. Segítségért forduljon a HDInsight csapatához. Adja meg a fürt nevét és a tevékenység futtatási időtartományát. |
|              | Minden egyéb hiba                                             |                                                              | A HDInsight és a [HDInsight gyakori kérdések](https://hdinsight.github.io/)című témakörben talál [segítséget](../hdinsight/hdinsight-troubleshoot-guide.md) . |



## <a name="web-activity"></a>Webes tevékenység

| Hibakód | Hibaüzenet                                                | Leírás                                                  | Ajánlás                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Érvénytelen HttpMethod: "...".                                    | A webes tevékenység nem támogatja a tevékenység-adattartalomban megadott HTTP-metódust. | A támogatott HTTP-metódusok a következők: PUT, POST, GET és DELETE. |
| 2108         | Érvénytelen kiszolgálóhiba: 500.                                     | Belső hiba történt a végponton.                               | A Hegedűs vagy a Poster használatával keresse meg a funkciót az URL-címen. |
| 2108         | Jogosulatlan 401.                                             | Hiányzó érvényes hitelesítés a kérelemben.                      | Lehet, hogy a jogkivonat lejárt. Adjon meg egy érvényes hitelesítési módszert. A Hegedűs vagy a Poster használatával keresse meg a funkciót az URL-címen. |
| 2108         | Tiltott 403.                                                | Hiányzó szükséges engedélyek.                                 | A hozzáfért erőforráshoz tartozó felhasználói engedélyek megtekintése. A Hegedűs vagy a Poster használatával keresse meg a funkciót az URL-címen.  |
| 2108         | Hibás kérelem 400.                                              | Érvénytelen HTTP-kérelem.                                         | Keresse meg a kérelem URL-címét, műveletét és törzsét. A kérelem érvényesítéséhez a Hegedűs vagy a Poster használatával.  |
| 2108         | Nem található 404.                                                | Az erőforrás nem található.                                       | A kérelem érvényesítéséhez a Hegedűs vagy a Poster használatával.  |
| 2108         | A szolgáltatás nem érhető el.                                          | A szolgáltatás nem érhető el.                                       | A kérelem érvényesítéséhez a Hegedűs vagy a Poster használatával.  |
| 2108         | Nem támogatott adathordozó-típus.                                       | A tartalom típusa nem egyezik a webes tevékenység Törzsével.           | Adja meg a hasznos adatok formátumának megfelelő tartalomtípust. A kérelem érvényesítéséhez a Hegedűs vagy a Poster használatával. |
| 2108         | A keresett erőforrás el lett távolítva, a neve megváltozott, vagy átmenetileg nem érhető el. | Az erőforrás nem érhető el.                                | Hegedűs vagy Poster használatával keresse meg a végpontot. |
| 2108         | A keresett lap nem jeleníthető meg, mert érvénytelen metódust (HTTP-műveletet) használt. | Helytelen webes tevékenységi metódus lett megadva a kérelemben.   | Hegedűs vagy Poster használatával keresse meg a végpontot. |
| 2108         | invalid_payload                                              | A webes tevékenység törzse helytelen.                       | Hegedűs vagy Poster használatával keresse meg a végpontot. |

A Hegedűs használata a figyelt webalkalmazás HTTP-munkamenetének létrehozásához:

1. A [Hegedűs](https://www.telerik.com/download/fiddler)letöltése, telepítése és megnyitása.

1. Ha a webalkalmazás HTTPS protokollt használ, lépjen az **eszközök** > **Hegedűs beállítások** > **https**elemre. Válassza a **https** -forgalom összekapcsolása és a **https-forgalom visszafejtése**lehetőséget. 
   
   ![Hegedűs beállításai](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Ha az alkalmazás SSL-tanúsítványokat használ, adja hozzá a Hegedűs tanúsítványát az eszközhöz. Nyissa meg az **eszközök** > a**Hegedűs beállításai** > **https** > -**műveletek** > **Főtanúsítvány exportálása az asztalra**lehetőséget.

1. Kapcsolja ki a rögzítést a **fájl** > **rögzítési forgalmának**kikapcsolásával. Vagy nyomja le az **F12**billentyűt.

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

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)



