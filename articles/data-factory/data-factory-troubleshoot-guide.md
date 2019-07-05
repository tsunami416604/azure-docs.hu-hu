---
title: Az Azure Data Factory hibaelhárítása |} A Microsoft Docs
description: Az Azure Data Factory hibaelhárítása. Minden külső, a vezérlési tevékenységek közös dokumentumában.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshoot
ms.subservice: troubleshoot
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 8d6ab565098e1ea40ede5c650f05e670a1edc7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452682"
---
# <a name="troubleshooting-azure-data-factory"></a>Az Azure Data Factory hibaelhárítása
Ez a cikk a gyakori hibaelhárítási kérdések listája.

- [Az Azure Databricks (jegyzetfüzet, JAR-fájlok kivételével, python)](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Azure Functions](#azure-functions)
- [Egyéni (az Azure Batch)](#custom-azure-batch)
- [HDInsight (Spark, Hive, MapReduce, a Pig, Hadoop-Stream)](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [Webes tevékenység](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| Hibakód | Hibaüzenet                                          | A probléma leírása                             | Lehetséges javítás / javasolt művelet                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | 403-as hiba                                                    | Databricks-hozzáférési token lejárt.                         | Alapértelmezés szerint Databricks hozzáférési jogkivonat érvénytelen 90 napig.  Hozzon létre egy új jogkivonatot, és a társított szolgáltatás frissítése. |
| 3201           | Missing   required field: settings.task.notebook_task.notebook_path | Szerzői hibás: Jegyzetfüzet útvonalat megfelelően megadva. | Jegyzetfüzet útvonalat adja meg a Databricks-tevékenység. |
| 3201           | Fürt... nem létezik                                 | Létrehozási hiba: Databricks-fürt nem létezik, vagy törölve lett. | Győződjön meg arról, hogy létezik-e a Databricks-fürtön. |
| 3201           | Érvénytelen python fájl URI:... Tekintse meg a Databricks felhasználói útmutatója a támogatott URI sémák. | Szerzői hibás                                                | Adjon meg abszolút elérési utakat a munkaterület címzési séma, vagy "dbfs:/folder/subfolder/foo.py" DBFS-tárolt fájlokhoz. |
| 3201           | {0}   LinkedService kell rendelkeznie, tartomány és a hozzáférési tokent mint kötelező tulajdonságai | Szerzői hibás                                                | Ellenőrizze, hogy [társított szolgáltatás meghatározásában](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | {0}   LinkedService kell adja meg a meglévő fürt azonosítója vagy új fürtinformációkat létrehozásához | Szerzői hibás                                                | Ellenőrizze, hogy [társított szolgáltatás meghatározásában](compute-linked-services.md#azure-databricks-linked-service). |
| 3201           | Standard_D16S_v3 csomóponttípus nem támogatott. A támogatott típusok:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard D3 v2, Standard D8 v3, Standard D16 v3, Standard d32 v3, Standard D64 v3, Standard D12 v2, Standard D13 v2, Standard D14 v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard H16 méretű, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard nc12, Standard nc24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2 | Szerzői hibás                                                | Tekintse meg a hibaüzenet                                          |
| 3201           | Érvénytelen notebook_path:... Jelenleg csak abszolút elérési utak támogatottak. Elérési utakat a következővel kell kezdődnie (/). | Szerzői hibás                                                | Tekintse meg a hibaüzenet                                          |
| 3202           | Már voltak az elmúlt 3600 másodperc, költési korlát túllépése létrehozott 1000 feladatot:   1000 feladat létrehozás / 3600 másodperc. | Túl sok Databricks egy órán belül fut.                         | Ellenőrizze az összes olyan folyamatok, amelyek a Databricks-munkaterületet a feladat létrehozásának sebessége.  A folyamatok összesítve túl sok Databricks futtatások indul el, ha olyan folyamatok át egy új munkaterületet. |
| 3202           | Nem sikerült elemezni a támogatásikérelem-objektum: Várt "key" és "értéke" JSON térkép mező base_parameters állítható be itt van a "kulcs:"..."" helyette. | Létrehozási hiba: Nincs a paraméternek megadott érték         | Nézze meg a folyamat JSON-fájlja, és minden paramétereket a Notebook baseParameters legyen egy nem üres értéket a megadott. |
| 3202           | Felhasználó: SimpleUserContext{userId=..., name=user@company.com, orgId=…} nem jogosult a fürt eléréséhez | A hozzáférési jogkivonat létrehozó felhasználó nem engedélyezett a hivatkozott szolgáltatásban található meg a Databricks-fürt eléréséhez. | Győződjön meg arról, felhasználói a szükséges engedélyekkel rendelkezik a munkaterületen.   |
| 3203           | A fürt állapotban van kilépett, nem érhető el, a feladatok fogadására. Javítsa ki a fürtöt, vagy próbálkozzon újra később. | Fürt meg lett szakítva.    Interaktív-fürt esetében ez lehet a versenyhelyzet. | Ez elkerülésének legjobb módja, hogy feladat-fürtökkel.             |
| 3204           | Feladat végrehajtása nem sikerült. Tetszőleges számú hibaüzeneteket, a tevékenység-specifikus üzenethez váratlan fürt állapota lehet.  A leggyakoribb egyáltalán nincs hibaüzenet. | –                                                          | –                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| Hibakód:         | Hibaüzenet                                                | A probléma leírása                                          | Lehetséges javítás / javasolt művelet                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | A hozzáférési jogkivonat rossz bérlőhöz származik.                    | Helytelen AAD-bérlő                                         | Az egyszerű szolgáltatás az ADLA eléréséhez használt AAD-bérlő tartozik. Hozzon létre új egyszerű szolgáltatást ADLA-fiók ugyanabban a bérlőben. |
| 2711,   2705,   2704 | Forbidden. ACL-ellenőrzése nem sikerült. Az erőforrás nem létezik, vagy a felhasználó nem jogosult a kért művelet végrehajtásához<br/><br/>Felhasználó nem sikerül datalake-Store eléréséhez  <br/><br/>Felhasználó nem jogosult a data lake analytics | A szolgáltatásnév vagy a megadott tanúsítvány nem rendelkezik hozzáféréssel a file storage-ban | Győződjön meg arról, hogy az egyszerű szolgáltatás, vagy ADLA feladatokhoz nyújtanak tanúsítvány hozzáfér ADLA fiók és az alapértelmezett ADLS tároló is, a gyökérmappa. |
| 2711                 | A "Az Azure Data Lake Store" fájl vagy mappa nem található       | A USQL fájl elérési útja vagy helytelenek, vagy nincs hozzáférése a hitelesítő adatokat a társított szolgáltatás | Ellenőrizze, hogy az elérési utat, és a társított szolgáltatásban megadott hitelesítő adatok |
| 2707                 | A fiókja AzureDataLakeAnalytics nem oldható fel. Ellenőrizze: AccountName' és "DataLakeAnalyticsUri". | Az ADLA fiókhoz társított szolgáltatás nem megfelelő.                  | Győződjön meg arról, hogy a megfelelő fiókot adott-e             |
| 2703                 | Hiba azonosítója: E_CQO_SYSTEM_INTERNAL_ERROR vagy bármilyen hiba történt a kezdődik "hiba azonosítója:" | Hiba történt az ADLA érkezik                                    | Bármilyen hiba, amely úgy tűnik, ez a példa azt jelenti, hogy a feladat el lett küldve ADLA, és ott a parancsfájl sikertelen. A vizsgálat során az ADLA kell elvégezni. Ha nyissa meg a portálon, és lépjen az ADLA-fiókra, keresse meg a feladat futásának azonosítóját (nem a folyamatfuttatási azonosító) ADF-tevékenység használatával. A feladat nem fog rendelkezni a hibával kapcsolatos további információkat, és elhárítása segítségével. Ha a megoldás nem egyértelmű, lépjen kapcsolatba az ADLA támogatási csapatával, és adja meg a feladat URL-CÍMÉT, amely magában foglalja a fiók neve és a feladat azonosítóját. |
| 2709                 | A feladat jelenleg nem fogadunk. A fiókja várakozási sorban lévő feladatok maximális száma 200. | Az ADLA-szabályozás                                           | A beküldött feladatok számának csökkentése az ADLA ADF-eseményindítók és a tevékenységek a párhuzamossági beállítások módosításával, vagy növelje az ADLA vonatkozó korlátozások |
| 2709                 | Ez a feladat el lett utasítva, mert a szükséges 24 au-k, és ez a fiók rendelkezik egy rendszergazda által meghatározott szabályzattal, amely megakadályozza, hogy egy feladat több mint 5 au-k. | Az ADLA-szabályozás                                           | A beküldött feladatok számának csökkentése az ADLA ADF-eseményindítók és a tevékenységek a párhuzamossági beállítások módosításával, vagy növelje az ADLA vonatkozó korlátozások |



## <a name="azure-functions"></a>Azure Functions

| Hibakód: | Hibaüzenet                           | Leírás                                                  | Lehetséges javítás / javasolt művelet                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | Válasz tartalma nem egy érvényes JObject | Ez azt jelenti, hogy az Azure-függvény, amelynek a neve van nem adott vissza egy JSON-adattartalom a válaszban. ADF-Azure-függvény tevékenység csak a JSON-válasz tartalma támogatja. | Azure-függvényt ad vissza egy érvényes JSON-adattartalom például frissítése egy C# függvény visszaadhat (ActionResult) új < OkObjectResult ("{`\"Id\":\"123\"`}"); |
| 3600         | Érvénytelen HttpMethod: "..".               | Ez azt jelenti, hogy a tevékenység hasznos adatainak a megadott Http-metódus az Azure-függvény tevékenység által nem támogatott. | A támogatott Http-metódusok a következők:  <br/>PUT, POST, BEOLVASÁSA, BEÁLLÍTÁSOK, HEAD, NYOMKÖVETÉSI TÖRLÉSÉHEZ |



## <a name="custom-azure-batch"></a>Egyéni (az Azure Batch)
| Hibakód: | Hibaüzenet                                                | Leírás                                                  | Lehetséges javítás / javasolt művelet                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | A találatok váratlan kivétel és a végrehajtás sikertelen.             | Nem tudja elindítani a parancsot, vagy a program hibakódot adott vissza. | Ellenőrizze, hogy a végrehajtható fájl létezik-e. Ha a program elindul, ellenőrizze a stdout.txt és a storage-fiókhoz feltöltött stderr.txt. Tanácsos hozzon létre bőséges naplókat a kódban a hibakereséshez. |
| 2501         | Nem lehet elérni a batch-fiók felhasználó, ellenőrizze a batch-fiók beállításait. | Helytelen Batch hozzáférési kulcs vagy a készlet megadott név.            | Szeretné megtudni, hogy a készlet nevét és a Batch hozzáférési kulcsot a hivatkozott szolgáltatásban található. |
| 2502         | Is nem hozzáférési felhasználói tárfiók, kérjük tárfiók beállításainak ellenőrzése. | Helytelen tárfiók neve vagy a hozzáférési kulcsát megadott.       | Szeretné megtudni, hogy a tárfiók nevét és kulcsát társított szolgáltatásban. |
| 2504         | A művelet adott vissza egy érvénytelen állapotkód: "BadRequest"     | Túl sok fájl a folderPath Ha az egyéni tevékenység.  (A resourceFiles teljes mérete nem lehet több, mint 32768 karaktert.) | Távolítsa el a felesleges fájlok, vagy a zip-őket és a kinyerési, például egy unzip-parancs hozzáadását: powershell.exe - nologo - noprofile-parancs "& {Add-Type - A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;  $folder\yourProgram.exe |
| 2505         | Nem hozható létre a közös hozzáférésű Jogosultságkód, kivéve, ha a Fiókkulcs hitelesítő adatokat használja. | Egyéni tevékenységek csak egy hozzáférési kulcsot használó storage-fiókok támogatják. | Tekintse meg a leírást                                            |
| 2507         | A mappa elérési útja nem létezik vagy üres:...            | A tárfiók a megadott elérési úton található fájlokat.       | A folderPath tartalmaznia kell a futtatni kívánt végrehajtható fájlok. |
| 2508         | Hiba az Ön által használt Másolatfájlok erőforrásmappa.               | Az azonos nevű másik almappáiban folderPath több fájlok vannak. | Egyéni tevékenységek folderPath mapparendszert simítja egybe.  Ha mappastruktúra kell őrizni, zip-fájlokat, és ki tudják nyerni azokat az Azure Batch-unzip paranccsal, például: powershell.exe - nologo - noprofile-parancs "& {Add-Type - A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;   $folder\yourProgram.exe |
| 2509         | A Batch URL-címet... az érvénytelen, kell lennie az Uri-formátum.         | A Batch URL-címek hasonlónak kell lennie. https://mybatchaccount.eastus.batch.azure.com | Tekintse meg a leírást                                            |
| 2510         | Hiba történt a kérés küldése során.               | A Batch URL-címe érvénytelen                                         | Ellenőrizze a kötegelt URL-címet.                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight (Spark, Hive, MapReduce, a Pig, Hadoop-Stream)

| Hibakód: | Hibaüzenet                                                | Leírás                                                  | Lehetséges javítás / javasolt művelet                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Hadoop-feladat küldése nem sikerült. Hiba: A távoli név nem oldható fel. <br/><br/>A fürt nem található. | A megadott fürt URI-ja érvénytelen.                              | Győződjön meg arról, hogy a fürt nem lett törölve, és a megadott URI-azonosító helyességéről. Az URI-t bármely böngészőben megnyithatja és megtekintheti az Ambari felhasználói felületén. Ha a fürt egy virtuális hálózaton, majd az URI-t kell-e a titkos URI-t, és nyissa meg a virtuális gép ugyanazon a Vneten részét képező próbál meg. További információ a [HDInsight virtuális hálózat](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services). |
| 2300         | Hadoop-feladat küldése nem sikerült. Feladat:..., fürt:... /. Hiba: A feladatot törölték. | A Küldés a feladat túllépte az időkorlátot.                         | Ez lehet általános HDInsight kapcsolódási probléma vagy hálózati kapcsolati probléma. Először győződjön meg arról, hogy a HDInsight az Ambari felhasználói felületén keresztül bármely böngészőben érhető el, és a hitelesítő adatai továbbra is érvényesek. Ellenőrizze, hogy ehhez a virtuális gép, ahol a saját üzemeltetésű integrációs modul telepítve van-e saját üzemeltetésű használatakor Ismételje meg a feladat az ADF újra elküldése. Ha továbbra is sikertelen, forduljon az ADF csapatával. |
| 2300         | Nem engedélyezett:   Az Ambari felhasználónév vagy jelszó helytelen  <br/><br/>Nem engedélyezett:   Az Ambari ki lett zárva a felhasználó rendszergazda   <br/><br/>403 – Tiltott: A hozzáférés megtagadva | A HDInsight a megadott hitelesítő adatok helytelenek, vagy lejárt | Javítsa ki azokat, és telepítse újra a társított szolgáltatást. Győződjön meg arról, hogy a hitelesítő adatokkal dolgozni, HDInsight először nyissa meg a fürt URI-t minden böngészővel és a bejelentkezés közben. Ha azok nem működnek, visszaállíthatja őket az Azure Portalról. |
| 2300,   2310 | 502 – webkiszolgáló érvénytelen választ kapott-átjáróval vagy proxyval-kiszolgálóként       <br/>Hibás átjáró | Hiba történt a HDInsight származik                               | Ez a hiba a HDInsight-fürt származik. Tekintse meg [HDInsight hibaelhárító](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) a gyakori hibák.    <br/>A Spark-fürtök esetében is lehetséges, hogy miatt [ez](https://hdinsight.github.io/spark/spark-thriftserver-errors.html). <br/><br/>[Hivatkozás](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2300         | Hadoop-feladat küldése nem sikerült. Feladat:..., fürt:... Hiba: {\"hiba\":\"tudja kiszolgálni a feladat-kérés elküldése, mivel templeton eszközön keresztül végzett szolgáltatás küldési feladat túl sok kérelem lefoglalja nem. Kérjük, várjon egy kis ideig, mielőtt megpróbálná megismételni a műveletet. Tekintse meg a konfigurációs templeton.parallellism.job.submit egyidejű kérelmek konfigurálása. \  <br/><br/>Hadoop-feladat küldése nem sikerült. Feladat: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, fürt: https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/.   Hiba: {\"hiba\":\"java.io.IOException: org.apache.hadoop.yarn.exceptions.YarnException: Nem sikerült elküldeni a YARN application_1561147195099_3730: org.apache.hadoop.security.AccessControlException: Várólista root.joblauncher már 500 alkalmazások futnak, nem fogadja el az alkalmazás beküldése: application_1561147195099_3730\ | Túl sok feladatok a HDInsight egy időben folyamatban elküldése | Vegye figyelembe, hogy nem továbbíthatók a HDI egyidejű feladatok számát. Tekintse meg az ADF tevékenység egyidejűségi Ha ugyanazt a tevékenységet a folyamatban van rájuk. Az egyidejű folyamatfuttatások idővel szétterítik, így az eseményindítók módosítása Emellett tekintse meg a HDInsight docs annak érdekében, hogy a "templeton.parallellism.job.submit" Teljesítménybeállítások hiba javasol. |
| 2303,   2347 | Hadoop-feladat meghiúsult kilépési kód "5". Lásd: "wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr" További részletekért.  <br/><br/>Hive végrehajtás sikertelen volt, hibakód: "UserErrorHiveOdbcCommandExecutionFailure".   Lásd: "wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out" kapcsolatos további részletek | A feladat el lett küldve HDInsight és a HDInsight sikertelen volt | A feladat sikeresen elküldte a HDInsight. Nem sikerült a fürtön. . Nyissa meg a feladatot a HDInsight az Ambari felhasználói felületén, és nyissa meg a naplókban hiba vagy megnyitni a fájlt a storage-ból ki hiba üzenet pontként. A hiba részleteit, az adott fájlban lesz. |
| 2328         | Belső kiszolgálóhiba történt a kérés feldolgozása közben. Próbálkozzon újra a kérelmet, vagy forduljon az ügyfélszolgálathoz | Igény szerinti HDInsight történik.                              | Ez a hiba HDInsight szolgáltatáshoz érkezik, amikor a HDInsight kiépítése sikertelen. A HDInsight csapat, és adja meg, őket igény szerint a fürt nevét. |
| 2310         | java.lang.NullPointerException                               | Hiba történt, amikor a Spark-fürthöz a feladat elküldése      | Ehhez a kivételhez HDInsight származik, és a rendszer elrejti a tényleges problémát.   Támogatás igényléséhez lépjen kapcsolatba a HDInsight csapat és a fürt neve és a tevékenységfuttatás időtartomány biztosítható a szükséges. |
|              | Minden egyéb hibák                                             |                                                              | Tekintse meg [HDInsight hibaelhárító](../hdinsight/hdinsight-troubleshoot-guide.md) és [HDInsight – gyakori kérdések](https://hdinsight.github.io/) |



## <a name="web-activity"></a>Webes tevékenység

| Hibakód: | Hibaüzenet                                                | Leírás                                                  | Lehetséges javítás / javasolt művelet                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | Érvénytelen HttpMethod: "..".                                    | Ez azt jelenti, hogy a tevékenység hasznos adatainak a megadott Http-metódus webes tevékenység által nem támogatott. | A támogatott Http-metódusok a következők: <br/>PUT, POST, BEOLVASÁSA, TÖRLÉSE |
| 2108         | Érvénytelen kiszolgálóhiba 500                                     | Belső hiba történt a végpont                               | Ellenőrizze az a funkciók köre a az URL-cím (a Fiddler vagy postman használatával): [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 401-es nem engedélyezett                                             | Hiányzó érvényes hitelesítési kérés                      | Érvényes hitelesítési módot (jogkivonat érvényessége).   <br/><br/>Ellenőrizze az a funkciók köre a az URL-cím (a Fiddler vagy postman használatával): [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Forbidden 403                                                | Hiányzik a szükséges engedélyek                                 | Az elért erőforrás felhasználói engedélyeinek ellenőrzése.   <br/><br/>Ellenőrizze az a funkciók köre a az URL-cím (a Fiddler vagy postman használatával): [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 400 – Hibás kérés                                              | Érvénytelen Http-kérelem                                         | Ellenőrizze az URL-címe, műveletet és a kérelem törzsében.   <br/><br/>A kérelem érvényesítéséhez a Fiddler vagy Postman használatával: [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 404 nem található                                                | Erőforrás nem található                                       | A kérelem érvényesítéséhez a Fiddler vagy Postman használatával: [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Szolgáltatás nem érhető el                                          | A szolgáltatás nem érhető el                                       | A kérelem érvényesítéséhez a Fiddler vagy Postman használatával: [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | Nem támogatott adathordozó-típus                                       | A webes tevékenység szervezethez eltérő Content-Type           | Adja meg a megfelelő Content-Type, amely megfelel az adattartalom formátuma használja a Fiddler vagy Postman a kérelem ellenőrzése: [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | A keresett erőforrás el lett távolítva, megváltoztatták a nevét, vagy átmenetileg nem érhető el. | Az erőforrás nem érhető el.                                | A Fiddler vagy Postman használatával ellenőrizze a végpont: [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | A keresett lap nem jeleníthető meg, mert érvénytelen metódust (HTTP-művelet) használja. | A kérésben megadott helytelen webes tevékenység metódus   | A Fiddler vagy Postman használatával ellenőrizze a végpont: [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | A webes tevékenység törzsében helytelen.                       | A Fiddler vagy Postman használatával ellenőrizze a végpont: [Hogyan hozhat létre egy HTTP-munkamenetben a Fiddler segítségével](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>A Fiddler használatával hozzon létre egy HTTP-munkamenetben a figyelt webalkalmazás

1. Töltse le és telepítse [Fiddler](https://www.telerik.com/download/fiddler)

2. Ha a webalkalmazás HTTPS PROTOKOLLT használ:

   1. Nyissa meg a Fiddler

   2. Lépjen a **eszközök > Fiddler beállítások** , és válassza ki az alábbi képernyőképen. 

      ![fiddler-beállítások](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. Ha az alkalmazás SSL-tanúsítványokat használ, hozzá kell adnia is a Fiddler tanúsítványt az eszközre.

4. A Fiddler tanúsítványt az eszköz hozzáadásához lépjen a **eszközök** > **Fiddler beállítások** > **HTTPS**  >   **Műveletek** > **főtanúsítvány exportálása az asztalra** a Fiddler-tanúsítvány beszerzése.

5. Kapcsolja ki, hogy a böngésző gyorsítótárát törölni kell ahhoz, hogy egy friss nyomkövetés indítása rögzítése.

6. 1. Lépjen a **fájl** > **forgalom rögzítése** vagy nyomja le az **F12**.
   2. Törölje a jelet a böngésző gyorsítótárát, hogy minden gyorsítótárazott elemek el lesznek távolítva, és újra letöltött kell lennie.

7. Kérés létrehozása: 

8. 1. Kattintson a Composer fülre.
   2. Adja meg a Http-metódus és egy URL-címe
   3. Fejlécek hozzáadása, és a kérés törzsének, ha szükséges
   4. Kattintson a végrehajtás

9. Indítsa el újra a forgalom rögzítése, és végezze el a problémás tranzakció az oldalon.

10. Ha befejeződött, nyissa meg **fájl** > **mentése** > **minden munkamenet**.

További információ a Fiddler [Itt](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>További lépések

További segítségre van szüksége a megoldás a problémára Íme néhány más erőforrások, próbálja meg.

*  [Blogok](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Funkciókérések](https://feedback.azure.com/forums/270578-data-factory)
*  [Videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



