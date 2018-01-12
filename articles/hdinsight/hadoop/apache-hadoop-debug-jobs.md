---
title: "A HDInsight Hadoop Debug: naplók megtekintése és hibaüzenetek - Azure értelmezhetők |} Microsoft Docs"
description: "Ismerje meg a hibaüzeneteket kaphat, ha a HDInsight a PowerShell felügyelete és helyreállítása menetéről."
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: ashishthaps
documentationcenter: 
ms.assetid: 7e6ceb0e-8be8-4911-bc80-20714030a3ad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: ashish
ms.openlocfilehash: 682b73aefff2ac20cbd38f6780b73cde859378ed
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="analyze-hadoop-logs"></a>Hadoop-naplóinak elemzése

Minden Azure hdinsight Hadoop-fürt rendelkezik egy Azure storage-fiók alapértelmezett fájlrendszert használja. A tárfiók kezeli az alapértelmezett tárfiók. Fürt használja az Azure Table storage és a Blob-tároló az alapértelmezett tárfiók a naplók tárolásához.  Az alapértelmezett tárfiókot, a fürt számára, lásd: [Hadoop kezelése a HDInsight-fürtök](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account). A naplók a tárfiókban lévő megőrzése mellett, a fürtök törlése után is.

## <a name="logs-written-to-azure-tables"></a>Azure-táblákban írt naplók

A naplók írni az Azure-táblákban betekintést HDInsight-fürtöt, mi történik egy szintjének biztosítására.

Amikor HDInsight fürtöt hoz létre, a hat táblázatok automatikusan jönnek létre Linux-alapú fürtökhöz az alapértelmezett Table storage-ban:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

A tábla fájlnevek **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Ezek a táblázatok a következő mezőket tartalmazhatja:

* ClusterDnsName
* Most letölthető a KomponensNév
* eventTimestamp
* Gazdagép
* MALoggingHash
* Üzenet
* N
* PreciseTimeStamp
* Szerepkör
* A RowIndex
* Bérlő
* IDŐBÉLYEG
* A TraceLevel

### <a name="tools-for-accessing-the-logs"></a>A naplók eléréséhez eszközök
Számos eszköz áll rendelkezésre egyes táblák adatait:

* Visual Studio
* Azure Storage Explorer
* A Power Query az Excel programhoz

#### <a name="use-power-query-for-excel"></a>Használja a Power Query az Excel programhoz
A Power Query telepíthető [Microsoft Power Query az Excel](http://www.microsoft.com/en-us/download/details.aspx?id=39379). A rendszerkövetelményeket a letöltési oldal jelenik meg.

**A Power Query segítségével nyissa meg és elemezheti a szolgáltatás bejelentkezési**

1. Nyissa meg **Microsoft Excel**.
2. Az a **Power Query** menüben kattintson a **az Azure**, és kattintson a **a Microsoft Azure Table storage**.
   
    ![HDInsight Hadoop Excel PowerQuery nyissa meg az Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Adja meg a tárfiók nevét, a rövid név vagy teljes Tartománynevét.
4. Adja meg a tárfiók kulcsára. A táblák listáját kell jelenik meg:
   
    ![Azure Table storage-ban tárolt HDInsight Hadoop-naplói](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Kattintson a jobb gombbal a hadoopservicelog tábla a **Navigator** ablaktáblán, és válassza **szerkesztése**. Ekkor megjelenik a négy oszlopot. Esetlegesen, törölje a **partíciókulcs**, **Sorkulcsa**, és **időbélyeg** oszlopok kiválasztva, majd **oszlopok eltávolítása** a a beállítások a szalagon.
6. Kattintson a kibontás, kiválaszthatja az oszlopokat, amelyeket szeretne importálni az Excel-táblázatba tartalom oszlop alapján. Az ebben a bemutatóban elfogadása TraceLevel, és most letölthető a KomponensNév: azt biztosíthat me néhány alapvető információ, amelyre összetevők kellett problémákat.
   
    ![HDInsight Hadoop-naplói oszlopok kiválasztása](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Kattintson a **OK** adatok importálásához.
8. Válassza ki a **TraceLevel**, szerepkör, és **most letölthető a KomponensNév** oszlopok, és kattintson **Group By** vezérlő a szalagon.
9. Kattintson a **OK** a Group By párbeszédpanel
10. Kattintson ** alkalmazása & Bezárás **.

Excel segítségével mostantól szűrési és rendezési szükség szerint. Érdemes lehet más oszlopokra (például üzenet) ahhoz, hogy a részletekbe menően tárhatják fel problémák jelentkeznek, de kiválasztásával, és a fent leírt oszlopok csoportosítási biztosít a Hadoop-szolgáltatás, mi történik egy decent képe. Az azonos ötlet a setuplog és hadoopinstalllog táblákra alkalmazhatók.

#### <a name="use-visual-studio"></a>A Visual Studio használata
**Visual Studio használata**

1. Nyissa meg a Visual Studiót.
2. Az a **nézet** menüben kattintson a **Cloud Explorer**. Vagy egyszerűen kattintson **CTRL +\, CTRL + X**.
3. A **Cloud Explorer**, jelölje be **erőforrástípusok**.  A más elérhető lehetőség az **erőforráscsoportok**.
4. Bontsa ki a **Tárfiókok**, az alapértelmezett tárfiókot, a fürt számára, majd **táblák**.
5. Kattintson duplán a **hadoopservicelog**.
6. Adjon hozzá egy szűrőt. Példa:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop-naplói oszlopok kiválasztása](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Szűrők létrehozásával kapcsolatos további információkért lásd: [Szűrőkarakterláncokban hozható létre a tábla Designer](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>A naplók az Azure Blob Storage írása
[A naplók írni az Azure-táblák](#log-written-to-azure-tables) betekintést HDInsight-fürtöt, mi történik egy szintjének biztosítására. Azonban ezek a táblázatok nem biztosítanak tevékenység szintű naplófájlokban, lehet megjeleníthetők, tovább kapcsolatos hibák elhárításának előfordulásuk időpontjában naplózhatja őket. A következő részletességi biztosítása érdekében a HDInsight-fürtök feladat naplók írni a Blob Storage-fiók lépni a Templeton keresztül elküldött feladathoz szükséges vannak konfigurálva. Gyakorlatilag ez azt jelenti, hogy a Microsoft Azure PowerShell-parancsmagokkal vagy a .NET feladat küldésének API-k, nem az RDP/parancssor-line a hozzáférést a fürt keresztül feladatok feladat. 

A naplók megtekintéséhez lásd: [Linux-alapú HDInsight bejelentkezik hozzáférést YARN alkalmazás](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Az alkalmazás naplóiban kapcsolatos további információkért lásd: [egyszerűsítése a felhasználó-naplók kezelése és hozzáférés a YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

## <a name="view-cluster-health-and-job-logs"></a>Fürt állapotának és a feladat naplók megtekintése
### <a name="access-the-ambari-ui"></a>Hozzáférés az Ambari felhasználói felület
Azure-portálról kattintson az HDInsight a fürt nevét, a fürt ablaktábla megnyitása. Kattintson a fürt ablak **irányítópult**.

![Indítsa el a fürt-irányítópult](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Hozzáférés a Yarn felhasználói felületen
Azure-portálról kattintson az HDInsight a fürt nevét, a fürt ablaktábla megnyitása. Kattintson a fürt ablak **irányítópult**. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatokat. Az Ambari, válassza ki a **YARN** a bal oldali szolgáltatások közül. Válassza az oldal jelenik meg, **Gyorshivatkozások**, majd válassza ki az aktív átjárócsomópont be- és erőforrás-kezelő felhasználói felületén.

A YARN felhasználói felület segítségével tegye a következőket:

* **Fürt állapotának beolvasása**. A bal oldali ablaktáblán bontsa ki a **fürt**, és kattintson a **kapcsolatos**. A jelen fürt állapot adatait, például teljes kiosztott memória, magot használja, a fürt erőforrás-kezelő, a fürt verziója és a stb.
  
    ![Indítsa el a fürt-irányítópult](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Csomópont állapota**. A bal oldali ablaktáblán bontsa ki a **fürt**, és kattintson a **csomópontok**. A parancs megjeleníti a fürt összes csomópontjának a, HTTP-cím az egyes csomópontok, hogy minden csomópont, stb.
* **Feladat állapotának figyelése**. A bal oldali ablaktáblán bontsa ki a **fürt**, és kattintson a **alkalmazások** a fürt a feladatok listázásához. Ha azt szeretné, nézze meg (például az új, elküldött, futó, stb.) meghatározott állapotban lévő feladatok, a megfelelő hivatkozásra a **alkalmazások**. További kattinthat a feladatnevet többet szeretne tudni a feladat ilyen, beleértve a kimeneti, naplói, stb.

### <a name="access-the-hbase-ui"></a>A HBase felhasználói felület eléréséhez
Azure-portálról kattintson az HDInsight hbase eszközhöz a fürt nevét, a fürt ablaktábla megnyitása. Kattintson a fürt ablak **irányítópult**. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatokat. Ambari jelölje ki a HBase szolgáltatások listájában. Válassza ki **Gyorshivatkozások** az oldal tetején az aktív Zookeeper csomópont hivatkozásra mutat, és kattintson a HBase fő felhasználói felületén.

## <a name="hdinsight-error-codes"></a>HDInsight hibakódok
Az ebben a szakaszban részletezett hibaüzenetek biztosított biztosíthat a felhasználóknak az Azure HDInsight Hadoop megérteni, hogy képes a szolgáltatás az Azure PowerShell felügyeletekor lehetséges hibaállapotok, és tanácsokkal azokat a lépéseket, amelyek akkor van szükség a hiba helyreállítása.

Bizonyos hibaüzenetek sikerült is látható az Azure-portálon kezelheti a HDInsight-fürtök használata esetén. Egyéb hibaüzeneteit, akkor léphetnek fel, de kevésbé részletes, az ebben a környezetben lehetséges javító műveleteket korlátai miatt van. Más hibaüzenetek a környezetben, ahol a megoldás az nyilvánvaló találhatók. 

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
* **Leírás**: Adja meg a Hive és az Oozie metastores egyéni beállítások használatához legalább egy összetevő Azure SQL adatbázis részletei.
* **Megoldás**: A felhasználónak van szüksége, adjon meg egy érvényes SQL Azure metaadattárhoz, és próbálkozzon újra a kéréssel.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Leírás**: nem hozható létre fürt a régióban *nameOfYourRegion*. Egy érvényes HDInsight-régió, és ismételje meg a kérelmet.
* **Megoldás**: ügyfél kell létrehoznia, amely jelenleg támogatja ezeket a fürt régió: Délkelet-Ázsiában, Nyugat-Európában, Észak-Európa, USA keleti régiója vagy USA nyugati régiója.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Leírás**: A kiszolgáló nem található a kért fürt rekord.  
* **Megoldás**: próbálja megismételni a műveletet.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Leírás**: fürt DNS-név *yourDnsName* érvénytelen. Győződjön meg arról, neve kezdődik alfanumerikus végződik, és csak "-" speciális karakter  
* **Megoldás**: Győződjön meg arról, hogy egy érvényes DNS-nevet használta, az a fürt és végződik alfanumerikus és tartalmaz semmilyen különleges karaktereket a kötőjel nem "-", és próbálkozzon újra a művelettel.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Leírás**: fürtnév *yourClusterName* nem érhető el. Válasszon másik nevet.  
* **Megoldás**: A felhasználó adjon meg egy egyedi fürtnév, és nem létezik és próbálja meg újra. Ha a felhasználó a portál által használt, a felhasználói felület fog értesítést küldhet nekik, ha a fürt neve már használatban van a létrehozás lépések során.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Leírás**: fürt jelszó érvénytelen. Jelszó legalább 10 karakter hosszúságúnak kell lennie, és tartalmaznia kell legalább egy számot, nagybetű, kisbetű és speciális karaktert, szóközök nélkül, és nem tartalmazhatja a felhasználónevet, részeként.  
* **Megoldás**: Adjon meg egy érvényes fürttel jelszót, majd próbálja megismételni a műveletet.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Leírás**: fürt felhasználónév érvénytelen. Győződjön meg arról, felhasználónév nem állhat, különleges karaktereket vagy a szóközt.  
* **Megoldás**: Adjon meg egy érvényes fürttel felhasználónévvel, és próbálja megismételni a műveletet.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Leírás**: fürt DNS-név *yourDnsClusterName* érvénytelen. Győződjön meg arról, neve kezdődik alfanumerikus végződik, és csak "-" speciális karakter  
* **Megoldás**: Adjon meg egy érvényes DNS-fürt felhasználónevet, majd próbálja megismételni a műveletet.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Leírás**: az URI Tárolónév *yourcontainerURI* és a DNS-név *yourDnsName* a kérelem törzsében meg kell egyeznie.  
* **Megoldás**: Győződjön meg arról, hogy a tároló neve és a DNS-neve megegyezik, és próbálja megismételni a műveletet.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Leírás**: Érvénytelen fürtkonfiguráció. Nem található egyetlen csomópont adatmeghatározások csomópont-méretet.  
* **Megoldás**: próbálja megismételni a műveletet.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Leírás**: a fürt nem sikerült törölni a központi telepítés  
* **Megoldás**: ismételje meg a törlési műveletet.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Leírás**: Hiba történt a konfiguráció szolgáltatás. Szükséges DNS-hozzárendelése információ nem található.  
* **Megoldás**: törli a fürtöt, és hozzon létre egy új fürtöt.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Leírás**: duplikált fürt tároló létrehozása kísérlet. Létezik rekord *nameOfYourContainer* de ETag-EK nem egyeznek.
* **Megoldás**: Adjon meg egy egyedi nevet a tárolót, majd próbálja megismételni a létrehozási művelet.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Leírás**: üzemeltetett szolgáltatás *nameOfYourHostedService* már tartalmaz egy fürt. Üzemeltetett szolgáltatás nem tartalmazhat több fürt  
* **Megoldás**: a fürt egy másik üzemeltetett szolgáltatás üzemeltetéséhez.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Leírás**: A kiszolgáló nem tudta frissíteni a fürt központi telepítésének állapotát.  
* **Megoldás**: próbálja megismételni a műveletet. Ilyen esetben többször forduljon az.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Leírás**: fürt *yourClusterName* karbantartás részeként törölve lett. Hozza létre a fürt.
* **Megoldás**: hozza létre újra a fürtöt.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Leírás**: Érvénytelen fürtkonfiguráció. Nem található a csomópont méretű központi csomópont-konfiguráció szükséges.
* **Megoldás**: próbálja megismételni a műveletet.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Leírás**: nem sikerült létrehozni az üzemeltetett szolgáltatás *nameOfYourHostedService*. Próbálja meg újra a kérelmet.  
* **Megoldás**: próbálkozzon újra a kéréssel.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Leírás**: üzemeltetett szolgáltatás *nameOfYourHostedService* már az éles környezet. Üzemeltetett szolgáltatás nem tartalmazhat több éles központi telepítését. Próbálja megismételni a kérelmet egy másik fürtnevet.
* **Megoldás**: használja egy másik fürtnevet, majd próbálja megismételni a kérelmet.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Leírás**: üzemeltetett szolgáltatás *nameOfYourHostedService* a fürt nem található.  
* **Megoldás**: Ha a fürt hiba állapotban, törölje azt, és próbálkozzon újra.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Leírás**: üzemeltetett szolgáltatás *nameOfYourHostedService* nincs társított központi telepítéshez van.  
* **Megoldás**: Ha a fürt hiba állapotban, törölje azt, és próbálkozzon újra.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Leírás**: az előfizetés-azonosító *yourSubscriptionId* fürt létrehozásához nem rendelkezik a magok balra *yourClusterName*. Kötelező: *resourcesRequired*, elérhető: *resourcesAvailable*.  
* **Megoldás**: Szabadítson fel erőforrást az előfizetésében, vagy növelje az előfizetés számára elérhető erőforrások, majd próbálja meg újra létrehozni a fürtöt.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Leírás**: előfizetés-azonosító *yourSubscriptionId* nem rendelkezik a fürt létrehozásához egy új üzemeltetett kvótája *yourClusterName*.  
* **Megoldás**: Szabadítson fel erőforrást az előfizetésében, vagy növelje az előfizetés számára elérhető erőforrások, majd próbálja meg újra létrehozni a fürtöt.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Leírás**: A kiszolgáló belső hibába ütközött. Próbálja meg újra a kérelmet.  
* **Megoldás**: próbálkozzon újra a kéréssel.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Leírás**: Azure tárolási helye *dataRegionName* már nem egy érvényes helyet. Ellenőrizze, hogy a régió megfelelő, majd próbálja megismételni a kérelmet.
* **Megoldás**: Válasszon tárolási helyet, amely támogatja a HDInsight, ellenőrizze, hogy a fürt ugyanott, és próbálja megismételni a műveletet.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Leírás**: adatcsomópontokat érvénytelen Virtuálisgép-méretet. Csak a "Nagy méretű" mérete csomópontjaihoz adatok esetén támogatott.  
* **Megoldás**: Adja meg az adatcsomóponton a támogatott csomópont méretét, majd próbálja megismételni a műveletet.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Leírás**: átjárócsomópont érvénytelen Virtuálisgép-méretet. Átjárócsomópont használható csak a "ExtraLarge VM" méret.  
* **Megoldás**: Adja meg az átjárócsomópont támogatott csomópont méretét, majd próbálja megismételni a műveletet

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Leírás**: előfizetés-azonosító *yourSubscriptionId* használt nem rendelkezik megfelelő engedélyekkel a fürt törlési művelet végrehajtása *yourClusterName*.  
* **Megoldás**: Ha a fürt állapota hibás, dobja el, és próbálkozzon újra.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Leírás**: külső tárfiók a blob tároló neve *yourContainerName* érvénytelen. Győződjön meg arról, nevének betűvel kezdődik, és csak kisbetűket, számokat és kötőjelet tartalmazza.  
* **Megoldás**: Adjon meg egy érvényes tárfiók a blob tároló neve, majd próbálja megismételni a műveletet.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Leírás**: külső Tárkonfigurációt *yourStorageAccountName* kell rendelkeznie a titkos kulcs adatai kell beállítani.  
* **Megoldás**: Adjon meg egy érvényes titkos kulcsot a tárfiók, és próbálja megismételni a műveletet.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Leírás**: Version fejlécnek *yourVersionHeader* nincs érvényes formátuma éééé-hh-nn.  
* **Megoldás**: Adjon meg érvényes formátumot a verzió-fejléc, és próbálkozzon újra a kéréssel.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Leírás**: Érvénytelen fürtkonfiguráció. Egynél több központi csomópont-konfiguráció található.  
* **Megoldás**: az konfigurációjának a szerkesztésével úgy, hogy csak egy központi csomópont van megadva.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Leírás**: A művelet nem sikerült a rendelkezésre álló időn belül, vagy az újrapróbálkozási kísérletek lehetséges. Próbálja meg újra a kérelmet.  
* **Megoldás**: próbálkozzon újra a kéréssel.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Leírás**: paraméter *yourParameterName* nem lehet null vagy üres.  
* **Megoldás**: Adjon meg egy érvényes értéket a paraméter.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Leírás**: egy vagy több a fürt létrehozási kérelem bemenet érvénytelen. Győződjön meg arról, hogy a bemeneti értékek megfelelőek, majd próbálja megismételni a kérelmet.  
* **Megoldás**: Ellenőrizze a bemeneti értékek megfelelőek, majd próbálja megismételni a kérelmet.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Leírás**: régió funkció nem érhető el a régióhoz *yourRegionName* és előfizetés-azonosító *yourSubscriptionId*.  
* **Megoldás**: Adja meg, amely támogatja a HDInsight-fürtök egy régiót. A nyilvánosan támogatott régiók a következők: Délkelet-Ázsiában, Nyugat-Európában, Észak-Európa, USA keleti régiója vagy USA nyugati régiója.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Leírás**: tárfiók *yourStorageAccountName* régióban *currentRegionName*. Meg kell egyeznie a fürt régió *yourClusterRegionName*.  
* **Megoldás**: vagy adjon meg egy tárfiókot, amely a fürt szerepel ugyanabban a régióban, vagy ha az adatok már a tárfiókban lévő, egy új fürt létrehozása a meglévő tárfiók ugyanabban a régióban. Ha a portált használja, a felhasználói felület fog értesítést küldhet nekik ennek a problémának előre.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Leírás**: megadott előfizetés-azonosító *yourSubscriptionId* addig nem aktív.  
* **Megoldás**: az előfizetés újraaktiválásához, vagy szerezzen be új érvényes előfizetést.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Leírás**: előfizetés-azonosító *yourSubscriptionId* nem található.  
* **Megoldás**: Ellenőrizze, hogy az előfizetés-azonosító érvényes, majd próbálja megismételni a műveletet.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Leírás**: nem oldható fel DNS *yourDnsUrl*. Ellenőrizze, hogy a teljes URL-címe a blob végpontja biztosítja.  
* **Megoldás**: Adjon meg egy érvényes blob URL-címet. Az URL-címet kell teljesen érvényes, beleértve a kezdve *http://* , és oda *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Leírás**: nem sikerült ellenőrizni az erőforrás helye *yourDnsUrl*. Ellenőrizze, hogy a teljes URL-címe a blob végpontja biztosítja.  
* **Megoldás**: Adjon meg egy érvényes blob URL-címet. Az URL-címet kell teljesen érvényes, beleértve a kezdve *http://* , és oda *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Leírás**: verzió funkció nem érhető el a verzió *specifiedVersion* és előfizetés-azonosító *yourSubscriptionId*.  
* **Megoldás**: válassza a rendelkezésre álló, majd próbálja megismételni a műveletet.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Leírás**: verzió *specifiedVersion* nem támogatott.
* **Megoldás**: Válasszon egy támogatott verzióra, majd próbálja megismételni a műveletet.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Leírás**: verzió *specifiedVersion* nem áll rendelkezésre az Azure-régió *specifiedRegion*.  
* **Megoldás**: Válasszon egy támogatott verziót határoz meg a régióban, majd próbálja megismételni a műveletet.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Leírás**: Érvénytelen fürtkonfiguráció. Szükséges WASB fiók konfigurációja nem található a külső fiókok.  
* **Megoldás**: Ellenőrizze az, hogy a fiók létezik és megfelelően megadva a konfigurációban, és próbálja megismételni a műveletet.

## <a name="next-steps"></a>További lépések

* [Az Ambari nézetek használata a HDInsight-on Tez feladatokhoz](../hdinsight-debug-ambari-tez-view.md)
* [Halommemória memóriaképek a Linux-alapú HDInsight Hadoop-szolgáltatások engedélyezése](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)

<!--
TODO  * [Manage HDInsight clusters by using the Ambari Web UI](hdinsight-hadoop-manage-ambari.md)
-->
