---
title: 'Hibakeresési Apache Hadoop: Naplók megtekintése és a hibaüzenetek értelmezése – Azure HDInsight'
description: Ismerje meg, hogy milyen hibaüzeneteket kaphat a HDInsight a PowerShell használatával történő felügyeletekor, valamint a helyreállításhoz szükséges lépéseket is.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 751d5b47006f5c99a747503ad4f052b3e03a043c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882429"
---
# <a name="analyze-apache-hadoop-logs-in-azure-hdinsight"></a>Apache Hadoop naplók elemzése az Azure HDInsight

Az Azure HDInsight minden Apache Hadoop-fürtje rendelkezik alapértelmezett fájlrendszerként használt Azure Storage-fiókkal. A Storage-fiókot az alapértelmezett Storage-fióknak nevezzük. A fürt az Azure Table Storage-t és a blob Storage-ot használja az alapértelmezett Storage-fiókban a naplók tárolásához.  A fürt alapértelmezett Storage-fiókjának megkereséséhez lásd: [Apache Hadoop-fürtök kezelése a HDInsight-ben](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). A naplók a fürt törlése után is megmaradnak a Storage-fiókban.

## <a name="logs-written-to-azure-tables"></a>Az Azure-táblákba írt naplók

Az Azure-táblákba írt naplók egy szintű betekintést biztosítanak a HDInsight-fürttel kapcsolatos eseményekbe.

HDInsight-fürt létrehozásakor a rendszer hat táblázatot hoz létre automatikusan a Linux-alapú fürtökhöz az alapértelmezett tábla-tárolóban:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

A táblázat fájlnevei: **u\<ClusterName > DDMonYYYYatHHMMSSsss\<táblanév >** .

Ezek a táblák a következő mezőket tartalmazzák:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Gazdagép
* MALoggingHash
* Message
* N
* PreciseTimeStamp
* Role
* RowIndex
* Bérlő
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>A naplók eléréséhez szükséges eszközök
A táblákban lévő adatok eléréséhez számos eszköz érhető el:

* Visual Studio
* Azure Storage Explorer
* Excel-Power Query

#### <a name="use-power-query-for-excel"></a>Az Excel Power Query használata
Power Query telepíthető [Excelhez készült Microsoft Power Queryból](https://www.microsoft.com/en-us/download/details.aspx?id=39379). A rendszerkövetelményekért tekintse meg a letöltési oldalt.

**A Power Query használata a szolgáltatás naplójának megnyitásához és elemzéséhez**

1. Nyissa meg a **Microsoft Excelt**.
2. A **Power Query** menüben kattintson az **Azure-ból**, majd a **Microsoft Azure Table Storage**elemre.
   
    ![HDInsight Hadoop Excel PowerQuery az Azure Table Storage megnyitása](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Adja meg a Storage-fiók nevét, vagy a rövid nevet vagy a teljes tartománynevet.
4. Adja meg a Storage-fiók kulcsát. A táblák listáját a következő táblázat tartalmazza:
   
    ![Az Azure Table Storage-ban tárolt HDInsight Hadoop-naplók](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Kattintson a jobb gombbal a hadoopservicelog táblára a **navigátor** ablaktáblán, és válassza a **Szerkesztés**lehetőséget. Négy oszlopot kell látnia. Ha kívánja, törölje a **partíció kulcsát**, a **sor kulcsát**és az **időbélyegző** oszlopokat úgy, hogy kiválasztja őket, majd az **oszlopok eltávolítása** lehetőségre kattint a menüszalagon.
6. Kattintson a Kibontás ikonra a tartalom oszlopban, és válassza ki az Excel-számolótáblába importálni kívánt oszlopokat. Ebben a bemutatóban úgy döntöttem, TraceLevel, és ComponentName: Olyan alapszintű információkat adhat meg, amelyekkel az összetevőkkel kapcsolatban problémák léptek fel.
   
    ![HDInsight Hadoop-naplók az Oszlopok kiválasztása Excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "HDInsight Hadoop-naplók az Oszlopok kiválasztása Excel")
7. Az adatimportáláshoz kattintson **az OK** gombra.
8. Válassza ki a **TraceLevel**, a szerepkört és a **ComponentName** oszlopot, majd kattintson a menüszalagon a Group By Control ( **csoport szerinti csoportosítás** ) elemre.
9. Kattintson az **OK** gombra a csoportosítási lista párbeszédpanelen
10. Kattintson a * * alkalmazás & Bezárás * * elemre.

Mostantól szükség szerint szűrheti és rendezheti az Excel programot. Előfordulhat, hogy más oszlopokat (például üzenetet) is szeretne felvenni a problémákra, de a fent ismertetett oszlopok kiválasztásával és csoportosításával a Hadoop-szolgáltatásokkal kapcsolatos újdonságokról szóló információk láthatók. Ugyanez a gondolat alkalmazható a Setuplog és a hadoopinstalllog táblákra is.

#### <a name="use-visual-studio"></a>A Visual Studio használata
**A Visual Studio használata**

1. Nyissa meg a Visual Studiót.
2. A **nézet** menüben kattintson a **Cloud Explorer**elemre. Vagy egyszerűen kattintson a **CTRL\, + CTRL + X billentyűkombinációra**.
3. A **Cloud Explorerben**válassza az **erőforrástípusok**lehetőséget.  A másik lehetőség az **erőforráscsoportok**.
4. Bontsa ki a **Storage-fiókok**, a fürt alapértelmezett Storage-fiókja, majd a **táblák**elemet.
5. Kattintson duplán a **hadoopservicelog**elemre.
6. Adjon hozzá egy szűrőt. Példa:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop-naplók az Oszlopok kiválasztása vs](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "HDInsight Hadoop-naplók az Oszlopok kiválasztása vs")
   
    A szűrők létrehozásával kapcsolatos további információkért lásd: [szűrési karakterláncok létrehozása a Táblatervezőhoz](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Az Azure Blob Storageba írt naplók
Az Azure-táblákba írt naplók egy szintű betekintést biztosítanak a HDInsight-fürttel kapcsolatos eseményekbe. Ezek a táblák azonban nem biztosítanak feladat-szintű naplókat, amelyek hasznosak lehetnek a problémák további befúrásakor. A következő részletességi szint megadásához a HDInsight-fürtök úgy vannak konfigurálva, hogy a Templeton-en keresztül küldött feladatokhoz minden feladathoz feljegyezzenek a Blob Storage fiókjába. Gyakorlatilag ez azt jelenti, hogy az Microsoft Azure PowerShell-parancsmagokkal vagy a .NET Job beküldési API-kkal elküldött feladatok nem a fürthöz RDP/parancssori hozzáférésen keresztül küldött feladatok. 

A naplók megtekintéséhez tekintse meg a következő témakört: [hozzáférés Apache HADOOP fonalas alkalmazás naplófájljaihoz a Linux-alapú HDInsight](../hdinsight-hadoop-access-yarn-app-logs-linux.md).


Az alkalmazás naplóival kapcsolatos további információkért lásd: [a felhasználói naplók kezelésének és elérésének egyszerűsítése Apache Hadoop-fonalban](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).


## <a name="view-cluster-health-and-job-logs"></a>A fürt állapotának és a feladatok naplóinak megtekintése
### <a name="access-the-ambari-ui"></a>Hozzáférés a Ambari felhasználói felületéhez
A Azure Portal a fürt ablaktábla megnyitásához kattintson egy HDInsight-fürt nevére. A fürt ablaktáblán kattintson az **irányítópult**elemre.

![A fürt irányítópultjának elindítása](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>A fonal felhasználói felületének elérése
A Azure Portal a fürt ablaktábla megnyitásához kattintson egy HDInsight-fürt nevére. A fürt ablaktáblán kattintson az **irányítópult**elemre. Ha a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. A Ambari-ben válassza a **fonal** lehetőséget a bal oldalon található szolgáltatások listájából. A megjelenő oldalon válassza a **gyors hivatkozások**lehetőséget, majd válassza ki az aktív fő csomópont-és erőforrás-kezelő felhasználói felületét.

A fonal felhasználói felületét a következő műveletek elvégzésére használhatja:

* **Fürt állapotának beolvasása**. A bal oldali ablaktáblán bontsa ki a **fürt**csomópontot, majd kattintson **a névjegy**elemre. Ez a fürt állapotának részleteit, például az összes lefoglalt memóriát, a felhasznált magokat, a fürterőforrás-kezelő állapotát, a fürt verzióját és így tovább.
  
    A ![fürt irányítópult-szálának elindítása] A (./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "fürt irányítópult-szálának elindítása")
* **Csomópont állapotának beolvasása**. A bal oldali ablaktáblán bontsa ki a **fürt**csomópontot, majd kattintson a **csomópontok**elemre. Ez felsorolja a fürt összes csomópontját, az egyes csomópontok HTTP-címeit, az egyes csomópontokhoz lefoglalt erőforrásokat stb.
* A **feladatok állapotának figyelése**. A bal oldali ablaktáblán bontsa ki a **fürt**csomópontot, majd kattintson az **alkalmazások** elemre a fürt összes feladatának listázásához. Ha egy adott állapotban szeretné megtekinteni a feladatokat (például új, elküldött, futó stb.), kattintson a megfelelő hivatkozásra az **alkalmazások**területen. A feladatokra kattintva további információkat kaphat a feladatokról, például a kimenetről, a naplókról stb.

### <a name="access-the-hbase-ui"></a>Hozzáférés a HBase felhasználói felületéhez
A Azure Portal a fürt ablaktábla megnyitásához kattintson egy HDInsight HBase-fürt nevére. A fürt ablaktáblán kattintson az **irányítópult**elemre. Ha a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. A Ambari területen válassza a HBase elemet a szolgáltatások listájából. Válassza a **gyors hivatkozások** lehetőséget az oldal tetején, mutasson az aktív Zookeeper csomópontra, majd kattintson HBase Master felhasználói felület elemre.

## <a name="hdinsight-error-codes"></a>HDInsight-hibakódok
Az ebben a szakaszban részletezett hibaüzenetek segítséget nyújtanak az Azure HDInsight lévő Hadoop felhasználói számára a szolgáltatásnak a Azure PowerShell használatával történő felügyeletekor felmerülő lehetséges hibák megismeréséhez, valamint a szükséges lépésekkel kapcsolatos tanácsadáshoz a hiba miatti helyreállításhoz.

Ezek a hibaüzenetek egy részét a Azure Portal is láthatják, amikor a HDInsight-fürtök kezelésére használják. Az esetlegesen előforduló egyéb hibaüzenetek azonban kevésbé részletesek, mert az ebben a kontextusban lehetséges javító műveletekre vonatkozó korlátozások is előfordulhatnak. Más hibaüzenetek a kontextusban vannak megadva, ahol a mérséklés nyilvánvaló. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Leírás**: Adja meg legalább egy összetevő számára az Azure SQL Database adatait, hogy egyéni beállításokat lehessen használni a kaptár és a Oozie metaadattárak.
* **Enyhítés**: A felhasználónak érvényes SQL Azure metaadattár kell megadnia, és újra kell próbálkoznia a kéréssel.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Leírás**: Nem lehetett létrehozni a fürtöt a *nameOfYourRegion*régióban. Érvényes HDInsight-régiót használjon, és próbálkozzon újra a kérelemmel.
* **Enyhítés**: Az ügyfélnek létre kell hoznia a jelenleg támogatott fürt régióját: Délkelet-Ázsia, Nyugat-Európa, Észak-Európa, az USA keleti régiója vagy az USA nyugati régiója.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Leírás**: A kiszolgáló nem találta a kért fürtözött rekordot.  
* **Enyhítés**: Próbálja meg újra a műveletet.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Leírás**: A fürt DNS-neve *yourDnsName* érvénytelen. Győződjön meg arról, hogy a név elindul és alfanumerikus karakterrel végződik, és csak "-" speciális karaktert tartalmazhat  
* **Enyhítés**: Győződjön meg arról, hogy a fürthöz érvényes DNS-nevet használt, amely alfanumerikus karakterrel kezdődik és végződik, és nem tartalmaz speciális karaktereket a kötőjeltől (-), majd próbálja megismételni a műveletet.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Leírás**: A fürt neve *yourClusterName* nem érhető el. Válasszon másik nevet.  
* **Enyhítés**: A felhasználónak olyan clustername kell megadnia, amely egyedi, és nem létezik, és próbálkozzon újra. Ha a felhasználó a portált használja, akkor a felhasználói felület értesíti a felhasználót, ha a létrehozási lépések során már használatban van egy fürt neve.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Leírás**: A fürt jelszava érvénytelen. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, nagybetűt, kisbetűt és egy szóközök nélküli speciális karaktert, és nem tartalmazhatja a felhasználónevet a részeként.  
* **Enyhítés**: Adjon meg egy érvényes fürtöt, és ismételje meg a műveletet.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Leírás**: A fürt felhasználóneve érvénytelen. Győződjön meg arról, hogy a Felhasználónév nem tartalmaz speciális karaktereket vagy szóközöket.  
* **Enyhítés**: Adja meg a fürt érvényes felhasználónevét, majd próbálja megismételni a műveletet.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Leírás**: A fürt DNS-neve *yourDnsClusterName* érvénytelen. Győződjön meg arról, hogy a név elindul és alfanumerikus karakterrel végződik, és csak "-" speciális karaktert tartalmazhat  
* **Enyhítés**: Adjon meg egy érvényes DNS-fürtöt, és ismételje meg a műveletet.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Leírás**: A *YOURDNSNAME* URI- *yourcontainerURI* és a kérelem törzsében szereplő DNS-névnek egyeznie kell a tároló nevével.  
* **Enyhítés**: Győződjön meg arról, hogy a tároló neve és a DNS-neve azonos, majd próbálja megismételni a műveletet.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Leírás**: A fürtkonfiguráció érvénytelen. Nem található az adatcsomópont-definíciók a csomópontok méretében.  
* **Enyhítés**: Próbálja meg újra a műveletet.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Leírás**: Sikertelen volt az üzemelő példány törlése a fürtön  
* **Enyhítés**: Próbálja megismételni a törlési műveletet.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Leírás**: Szolgáltatás-konfigurációs hiba. A kötelező DNS-megfeleltetési információk nem találhatók.  
* **Enyhítés**: Törölje a fürtöt, és hozzon létre egy új fürtöt.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Leírás**: Duplikált fürt tároló-létrehozási kísérlet. A rekord létezik a *nameOfYourContainer* , de a etagek nem egyeznek.
* **Enyhítés**: Adja meg a tároló egyedi nevét, majd próbálja megismételni a létrehozási műveletet.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Leírás**: Az üzemeltetett szolgáltatás *nameOfYourHostedService* már tartalmaz fürtöt. Egy üzemeltetett szolgáltatás nem tartalmazhat több fürtöt  
* **Enyhítés**: A fürt üzemeltetése egy másik üzemeltetett szolgáltatásban.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Leírás**: A kiszolgáló nem tudja frissíteni a fürt központi telepítésének állapotát.  
* **Enyhítés**: Próbálja meg újra a műveletet. Ha ez többször is előfordul, forduljon a CSS-hez.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Leírás**: A fürt *yourClusterName* a karbantartás részeként törölte. Hozza létre újra a fürtöt.
* **Enyhítés**: Hozza létre újra a fürtöt.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Leírás**: A fürtkonfiguráció érvénytelen. A szükséges fő csomópont-konfiguráció nem található a csomópontok méreteiben.
* **Enyhítés**: Próbálja meg újra a műveletet.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Leírás**: Az üzemeltetett szolgáltatás *nameOfYourHostedService*nem hozható létre. Próbálkozzon újra a kérelemmel.  
* **Enyhítés**: Próbálja megismételni a kérelmet.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Leírás**: Az üzemeltetett szolgáltatás *nameOfYourHostedService* már rendelkezik éles üzembe helyezéssel. Egy üzemeltetett szolgáltatás nem tartalmazhat több éles környezetben üzemelő példányt. Próbálja megismételni a kérelmet egy másik fürt nevével.
* **Enyhítés**: Használjon másik fürtöt, majd próbálja megismételni a kérelmet.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Leírás**: Nem található a fürthöz tartozó üzemeltetett szolgáltatás *nameOfYourHostedService* .  
* **Enyhítés**: Ha a fürt hibás állapotban van, törölje, majd próbálkozzon újra.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Leírás**: Az üzemeltetett szolgáltatás *nameOfYourHostedService* nincs társított központi telepítés.  
* **Enyhítés**: Ha a fürt hibás állapotban van, törölje, majd próbálkozzon újra.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Leírás**: A SubscriptionId *yourSubscriptionId* nem rendelkezik bal oldali maggal a fürt *yourClusterName*létrehozásához. Kötelező: *resourcesRequired*, elérhető: *resourcesAvailable*.  
* **Enyhítés**: Szabadítson fel erőforrásokat az előfizetésben, vagy növelje az előfizetés számára elérhető erőforrásokat, és próbálja meg újból létrehozni a fürtöt.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Leírás**: Az előfizetés-azonosító *yourSubscriptionId* nem rendelkezik kvótával egy új üzemeltetett számára a fürt *yourClusterName*létrehozásához.  
* **Enyhítés**: Szabadítson fel erőforrásokat az előfizetésben, vagy növelje az előfizetés számára elérhető erőforrásokat, és próbálja meg újból létrehozni a fürtöt.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Leírás**: A kiszolgáló belső hibát észlelt. Próbálkozzon újra a kérelemmel.  
* **Enyhítés**: Próbálja megismételni a kérelmet.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Leírás**: Az Azure Storage helye *dataRegionName* nem érvényes hely. Győződjön meg arról, hogy a régió helyes, és próbálkozzon újra a kérelemmel.
* **Enyhítés**: Válassza ki a HDInsight támogató tárolási helyet, győződjön meg arról, hogy a fürt közös helyen található, majd próbálja megismételni a műveletet.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Leírás**: Az adatcsomópontok virtuálisgép-mérete érvénytelen. Az összes adatcsomópont esetében csak a "nagyméretű virtuális gépek" méret támogatott.  
* **Enyhítés**: Határozza meg az adatcsomópont támogatott csomópontjának méretét, majd próbálja megismételni a műveletet.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Leírás**: A fő csomóponthoz tartozó virtuálisgép-méret érvénytelen. A fő csomópont csak a "ExtraLarge VM" méretet támogatja.  
* **Enyhítés**: Határozza meg a csomópont csomópontjának támogatott méretét, majd ismételje meg a műveletet.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Leírás**: A használt előfizetés-azonosító *yourSubscriptionId* nem rendelkezik megfelelő engedélyekkel a fürt *yourClusterName*tartozó törlési művelet végrehajtásához.  
* **Enyhítés**: Ha a fürt hibás állapotban van, dobja el, majd próbálkozzon újra.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Leírás**: A külső Storage-fiók blob-tárolójának neve *yourContainerName* érvénytelen. Győződjön meg arról, hogy a név betűvel kezdődik, és csak kisbetűket, számokat és kötőjelet tartalmaz.  
* **Enyhítés**: Adjon meg egy érvényes tárolási fiók blob-tárolójának nevét, majd próbálja megismételni a műveletet.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Leírás**: A külső Storage-fiók *yourStorageAccountName* konfigurálása szükséges a titkos kulcs részleteinek beállításához.  
* **Enyhítés**: Érvényes titkos kulcsot kell megadni a Storage-fiókhoz, majd újra kell próbálkoznia a művelettel.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Leírás**: A verzió fejlécének *yourVersionHeader* formátuma nem érvényes éééé-hh-nn formátumban.  
* **Enyhítés**: Érvényes formátumot kell megadnia a verzió fejlécéhez, és újra kell próbálkoznia a kéréssel.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Leírás**: A fürtkonfiguráció érvénytelen. Több fő csomópont-konfiguráció található.  
* **Enyhítés**: Szerkessze a konfigurációt úgy, hogy csak egy fő csomópont legyen megadva.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Leírás**: A művelet nem hajtható végre a megengedett időn belül, vagy a maximális újrapróbálkozási kísérletek lehetségesek. Próbálkozzon újra a kérelemmel.  
* **Enyhítés**: Próbálja megismételni a kérelmet.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Leírás**: A *yourParameterName* paraméter nem lehet null értékű vagy üres.  
* **Enyhítés**: Érvényes értéket kell megadnia a paraméterhez.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Leírás**: Egy vagy több fürt létrehozási kérelmének bemenete érvénytelen. Győződjön meg arról, hogy a bemeneti értékek helyesek, majd próbálja megismételni a kérelmet.  
* **Enyhítés**: Győződjön meg arról, hogy a bemeneti értékek helyesek, majd próbálja megismételni a kérelmet.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Leírás**: A régiós képesség nem érhető el a *yourRegionName* és az előfizetés-azonosító *yourSubscriptionId*.  
* **Enyhítés**: Olyan régiót válasszon, amely támogatja a HDInsight-fürtöket. A nyilvánosan támogatott régiók a következők: Délkelet-Ázsia, Nyugat-Európa, Észak-Európa, az USA keleti régiója vagy az USA nyugati régiója.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Leírás**: A Storage-fiók *yourStorageAccountName* a *currentRegionName*régióban található. A fürt *yourClusterRegionName*azonosnak kell lennie.  
* **Enyhítés**: Adjon meg egy olyan Storage-fiókot, amely ugyanabban a régióban található, mint a fürt, vagy ha az adatai már szerepelnek a Storage-fiókban, hozzon létre egy új fürtöt a meglévő Storage-fiókkal azonos régióban. Ha a portált használja, a felhasználói felületen előre értesítjük a problémát.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Leírás**: A megadott előfizetés-azonosító *yourSubscriptionId* nem aktív.  
* **Enyhítés**: Aktiválja újra az előfizetést, vagy szerezzen be egy új érvényes előfizetést.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Leírás**: Nem található az előfizetés-azonosító *yourSubscriptionId* .  
* **Enyhítés**: Győződjön meg arról, hogy az előfizetés-azonosító érvényes, majd próbálja megismételni a műveletet.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Leírás**: Nem sikerült feloldani a DNS- *yourDnsUrl*. Győződjön meg arról, hogy a blob-végponthoz tartozó teljes URL-cím meg van-e biztosítva.  
* **Enyhítés**: Adjon meg egy érvényes blob URL-címet. Az URL-címnek teljes mértékben érvényesnek kell lennie, beleértve a *http://* kezdődő és a *. com*végződést.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Leírás**: Nem sikerült ellenőrizni az erőforrás- *yourDnsUrl*helyét. Győződjön meg arról, hogy a blob-végponthoz tartozó teljes URL-cím meg van-e biztosítva.  
* **Enyhítés**: Adjon meg egy érvényes blob URL-címet. Az URL-címnek teljes mértékben érvényesnek kell lennie, beleértve a *http://* kezdődő és a *. com*végződést.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Leírás**: A verzió *specifiedVersion* és az előfizetés-azonosító *yourSubscriptionId*nem érhető el.  
* **Enyhítés**: Válasszon egy elérhető verziót, majd próbálja megismételni a műveletet.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Leírás**: A verzió *specifiedVersion* nem támogatott.
* **Enyhítés**: Válasszon egy támogatott verziót, majd próbálja megismételni a műveletet.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Leírás**: A *specifiedVersion* verziója nem érhető el az Azure region *specifiedRegion*.  
* **Enyhítés**: Válassza ki a megadott régióban támogatott verziót, és ismételje meg a műveletet.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Leírás**: A fürtkonfiguráció érvénytelen. A szükséges WASB-fiók konfigurációja nem található a külső fiókokban.  
* **Enyhítés**: Győződjön meg arról, hogy a fiók létezik, és megfelelően van megadva a konfigurációban, majd próbálja megismételni a műveletet.

## <a name="next-steps"></a>További lépések

* [Halom-memóriaképek engedélyezése Apache Hadoop-szolgáltatásokhoz Linux-alapú HDInsight](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [HDInsight-fürtök kezelése az Apache Ambari webes felületével](../hdinsight-hadoop-manage-ambari.md)
