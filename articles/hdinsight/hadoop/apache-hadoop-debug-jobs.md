---
title: 'Az Apache hadoop-keretrendszer hibakeresése: A naplók megtekintéséhez és a hibaüzenetek – Azure HDInsight értelmezése'
description: További tudnivalók a hibaüzenetek kaphat, ha a PowerShell használatával HDInsight felügyelete és helyreállítása lépéseket.
services: hdinsight
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: d52a9411863962528eb0693f735ca0c4ee4c9991
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166506"
---
# <a name="analyze-hadoop-logs"></a>Hadoop-naplók elemzése

Minden egyes Azure HDInsight az Apache Hadoop-fürtöt az alapértelmezett fájlrendszerként használt Azure storage-fiókkal rendelkezik. A storage-fiókot az alapértelmezett tárfiók neve. Fürt használja az Azure Table storage és a Blob storage az alapértelmezett tárfiókot a naplók tárolásához.  Az alapértelmezett tárfiókot, a fürt számára, lásd: [kezelése Hadoop-fürtök a HDInsight](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account). A naplók megőrzése a tárfiókban, a fürt törlése után is.

## <a name="logs-written-to-azure-tables"></a>Azure-beli táblák írt naplók

A naplók az Azure-beli táblák írt abba, mi történik egy HDInsight-fürtöt egy szintjének adja meg.

Amikor létrehoz egy HDInsight-fürtöt, hat táblára automatikusan létrejönnek az alapértelmezett Table storage-ban a Linux-alapú fürtök:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

A tábla fájlnevek **u<ClusterName>DDMonYYYYatHHMMSSsss<TableName>**.

Ezek a táblák a következő mezőket tartalmazzák:

* ClusterDnsName
* ComponentName
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
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>A naplók eléréséhez szükséges eszközök
Számos eszköz áll rendelkezésre a táblák elérése:

* Visual Studio
* Azure Storage Explorer
* A Power Query az Excel programhoz

#### <a name="use-power-query-for-excel"></a>Használja a Power Query az Excel programhoz
A Power Query telepíthető [Excelhez készült Microsoft Power Query](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Tekintse meg a letöltési oldalon a rendszerkövetelményeket.

**Nyissa meg, és a napló elemzése a Power Query használatával**

1. Nyissa meg **Microsoft Excel**.
2. Az a **Power Query** menüben kattintson a **az Azure**, és kattintson a **a Microsoft Azure Table storage**.
   
    ![HDInsight Hadoop Excel PowerQuery nyissa meg az Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Adja meg a tárfiók nevét, rövid nevét vagy teljes Tartománynevét.
4. Adja meg a tárfiók-kulcsot. Táblák listáját kell jelenik meg:
   
    ![Az Azure Table storage szolgáltatásban tárolt HDInsight Hadoop-naplók](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Kattintson a jobb gombbal a hadoopservicelog táblájában a **kezelő** ablaktáblán, és válassza ki **szerkesztése**. Négy oszlopot kell megjelennie. Szükség esetén törölje a **partíciókulcs**, **Sorkulcs**, és **időbélyeg** oszlopok őket, majd kattintson a **oszlopok eltávolítása** a a beállítások a menüszalagon.
6. Kattintson a Kibontás ikonra a tartalom oszlopában az Excel-munkafüzetbe importálni kívánt oszlopok kiválasztásához. Ebben a bemutatóban a kiválasztott TraceLevel és ComponentName: Témakörébe me néhány alapvető információ, amelyre összetevők problémák rendelkezett.
   
    ![HDInsight Hadoop-naplók oszlopok kiválasztása](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Kattintson a **OK** az adatok importálásához.
8. Válassza ki a **TraceLevel**, szerepkör, és **ComponentName** oszlopokat, és kattintson **Group By** vezérlőelem a menüszalagon.
9. Kattintson a **OK** a Group By párbeszédpanel
10. Kattintson a ** a alkalmazni & Bezárás **.

Mostantól használhatja az Excel, a szűrési és rendezési szükség szerint. Érdemes felvenni a többi oszlop (mint például a hibaüzenet) annak érdekében, hogy részletesen megvizsgálhatja a problémákat úgy fordulnak elő, de kiválasztásával és az oszlopok a fent leírt csoportosítási vonalakban finomat, hogy mi történik, a Hadoop-szolgáltatásokhoz. Az azonos ötlet a setuplog és hadoopinstalllog táblákra alkalmazhatók.

#### <a name="use-visual-studio"></a>A Visual Studio használata
**A Visual Studio használata**

1. Nyissa meg a Visual Studiót.
2. Az a **nézet** menüben kattintson a **Cloud Explorer**. Vagy egyszerűen csak kattintson **CTRL +\, CTRL + X**.
3. A **Cloud Explorer**válassza **erőforrástípusok**.  A másik elérhető lehetőség **erőforráscsoportok**.
4. Bontsa ki a **Tárfiókok**, az alapértelmezett tárfiókot, a fürt számára, majd **táblák**.
5. Kattintson duplán a **hadoopservicelog**.
6. Adjon hozzá egy szűrőt. Példa:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop-naplók oszlopok kiválasztása](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Szűrők létrehozásával kapcsolatos további információkért lásd: [szűrő karakterláncok hozhatnak létre a tábla Designer](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Az Azure Blob Storage-írt naplók
[A naplók az Azure-beli táblák írt](#log-written-to-azure-tables) abba, mi történik egy HDInsight-fürtöt egy szintjének megadása. Ezek a táblák azonban nem biztosítanak tevékenységszintű naplókat, amelyek akkor hasznosak, részletes elemzések kibontásáról további felmerülő problémákat. Adja meg a következő részletességi szintje, a HDInsight fürtök úgy vannak konfigurálva, a Blob Storage-fiók minden olyan feladat templeton eszközön keresztül végzett keresztül elküldött feladat naplók írni. Gyakorlatilag ez azt jelenti, hogy a Microsoft Azure PowerShell-parancsmagok vagy a .NET projekt beküldése API-k, nem az RDP/parancssori hozzáférést a fürthöz keresztül elküldött feladatok használatával elküldött feladatok. 

A naplók megtekintéséhez lásd: [hozzáférés YARN-alkalmazásnaplók a Linux-alapú HDInsight](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Protokoly aplikací kapcsolatos további információkért lásd: [leegyszerűsíti a felhasználó-naplók és a YARN hozzáférés](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

## <a name="view-cluster-health-and-job-logs"></a>Fürt állapotának és a feladat naplóinak megtekintése
### <a name="access-the-ambari-ui"></a>Az Ambari felhasználói felületén eléréséhez
Az Azure Portalon kattintson egy HDInsight-fürt nevét, a fürt panel megnyitásához. A fürt panelen kattintson a **irányítópult**.

![Indítsa el a fürt irányítópultja](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Hozzáférés a Yarn felhasználói felületén
Az Azure Portalon kattintson egy HDInsight-fürt nevét, a fürt panel megnyitásához. A fürt panelen kattintson a **irányítópult**. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Az Ambari, válassza ki a **YARN** a bal oldali szolgáltatások listájából. A megjelenő lapon válassza ki a **Gyorshivatkozások**, majd válassza ki az aktív átjárócsomópont be- és erőforrás-kezelő felhasználói felületén.

A YARN felhasználói felületén segítségével a következőket:

* **A fürt állapotának lekérése**. A bal oldali ablaktáblán, bontsa ki a **fürt**, és kattintson a **kapcsolatos**. A jelen fürt állapot részletei, például a teljes kiosztott memória, a használt, mag állapotát a fürterőforrás-kezelő fürt verziója és így tovább.
  
    ![Indítsa el a fürt irányítópultja](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Csomópont állapotának lekérése**. A bal oldali ablaktáblán, bontsa ki a **fürt**, és kattintson a **csomópontok**. Ez felsorolja az összes a csomópontok a fürtben, a HTTP-cím az egyes csomópontok osztja ki minden egyes csomópontot, és így tovább.
* **Feladat állapotának figyelése**. A bal oldali ablaktáblán, bontsa ki a **fürt**, és kattintson a **alkalmazások** , a fürt összes feladat listázása. Ha azt szeretné, tekintse meg (például az új, elküldött, futó, stb.) meghatározott állapotban lévő feladatok, alapján a megfelelő hivatkozásra kattintva **alkalmazások**. További kattinthat a feladat nevét tudjon meg többet a feladat ilyen, beleértve a kimenetet, naplók, stb.

### <a name="access-the-hbase-ui"></a>A HBase felhasználói felület eléréséhez
Az Azure Portalon kattintson egy HDInsight HBase-fürt nevét, a fürt panel megnyitásához. A fürt panelen kattintson a **irányítópult**. Amikor a rendszer kéri, adja meg a fürt rendszergazdai hitelesítő adatait. Az Ambari válassza ki a HBase a szolgáltatások listájából. Válassza ki **Gyorshivatkozások** elemre a lap tetején mutasson az aktív Zookeeper-csomópont hivatkozására, és kattintson a HBase-Mesterfelület.

## <a name="hdinsight-error-codes"></a>HDInsight-hibakódok
Az ebben a szakaszban részletezett hibaüzenetek segítségével a felhasználók az Azure HDInsight Hadoop lehetséges hibaállapotok, hogy azok merülhetnek fel, ha az Azure PowerShell-lel szolgáltatás felügyeletére megértéséhez, és azokat a lépéseket, amelyek elvégezhet tanácsokkal találhatók. a hiba helyreállítása.

Bizonyos hibaüzenetek sikerült is látható az Azure Portalon kezelheti a HDInsight-fürtök használatakor. Egyéb hibaüzeneteit, esetleg előforduló, de vannak kevésbé részletes lehetséges ebben a környezetben a helyreigazító műveletek korlátozásai miatt. Más hibaüzenetek a környezetekben, ahol a megoldás az nyilvánvaló vannak megadva. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Leírás**: Adja meg az Azure SQL database adatokat legalább egy összetevő annak érdekében, hogy az egyéni beállítások használatához az Oozie és a Hive-metaadattárak.
* **Kockázatcsökkentési**: A felhasználónak van szüksége, adjon meg egy érvényes SQL Azure-metaadattár, és ismételje meg a kérelmet.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Leírás**: Nem sikerült létrehozni a fürtöt a régióban *nameOfYourRegion*. Egy érvényes HDInsight régiót használni, és próbálkozzon újra a kérelmet.
* **Kockázatcsökkentési**: Ügyfél kell létrehozni az jelenleg támogatja az ilyen fürt régió: Délkelet-Ázsia, Nyugat-Európa, Észak-Európa, USA keleti RÉGIÓJA vagy USA nyugati RÉGIÓJA.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Leírás**: A kiszolgáló nem található a kért fürt rekord.  
* **Kockázatcsökkentési**: Próbálja megismételni a műveletet.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Leírás**: Fürt DNS-név *yourDnsName* je neplatná. Ellenőrizze, hogy a neve kezdődik és végződik alfanumerikus, és csak "-" speciális karakter  
* **Kockázatcsökkentési**: Ellenőrizze, hogy használja a egy érvényes DNS-nevet a fürt, amely elindítja és alfanumerikus végződik, és nincsenek különleges tartalmazza a kötőjelet más karaktereket az '-', és próbálkozzon újra a művelettel.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Leírás**: Fürt neve *yourClusterName* nem érhető el. Válasszon egy másik nevet.  
* **Kockázatcsökkentési**: A felhasználó kell adjon meg egy egyedi clustername és nem létezik, és ismételje meg. Ha a felhasználó nem használja a portálon, a felhasználói felület értesíteni fogjuk őket, ha a fürt neve már használatban a létrehozás lépések során.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Leírás**: Fürt jelszó érvénytelen. Jelszó legalább 10 karakter hosszúságúnak kell lennie, és tartalmaznia kell legalább egy szám, nagybetű, kisbetű és speciális karaktert a szóközök nélküli, és nem tartalmazhatja a felhasználónevet, részeként.  
* **Kockázatcsökkentési**: Adjon meg egy érvényes fürttel jelszót, és próbálja megismételni a műveletet.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Leírás**: Fürt felhasználónév érvénytelen. Ellenőrizze, hogy felhasználónév nem tartalmazhat különleges karaktereket.  
* **Kockázatcsökkentési**: Adjon meg egy érvényes fürttel felhasználónévvel, és próbálja megismételni a műveletet.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Leírás**: Fürt DNS-név *yourDnsClusterName* je neplatná. Ellenőrizze, hogy a neve kezdődik és végződik alfanumerikus, és csak "-" speciális karakter  
* **Kockázatcsökkentési**: Adjon meg egy érvényes DNS-fürt felhasználónevet, és próbálja megismételni a műveletet.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Leírás**: URI-Tárolónév *yourcontainerURI* és DNS-név *yourDnsName* a kérelem törzsében meg kell egyeznie.  
* **Kockázatcsökkentési**: Győződjön meg arról, hogy a tároló neve és a DNS-név azonos, és próbálja megismételni a műveletet.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Leírás**: Érvénytelen a fürt konfigurációját. Nem található minden adat csomópont definíciókat a csomópont méretét.  
* **Kockázatcsökkentési**: Próbálja megismételni a műveletet.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Leírás**: Nem sikerült telepíteni a fürt törlése  
* **Kockázatcsökkentési**: A törlési művelettel próbálkozhat újra.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Leírás**: Konfigurációs hiba. Szükséges DNS-hozzárendelést az információk nem találhatók.  
* **Kockázatcsökkentési**: Fürt törlése, és hozzon létre egy új fürtöt.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Leírás**: Ismétlődő fürt tároló létrehozási kísérlet. Bejegyzés létezik *nameOfYourContainer* , de az ETag nem egyezik.
* **Kockázatcsökkentési**: Adjon meg egy egyedi nevet a tároló, és ismételje meg a létrehozási művelet.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Leírás**: Üzemeltetett szolgáltatás *nameOfYourHostedService* már tartalmaz egy fürtöt. Üzemeltetett szolgáltatás nem tartalmazhat több fürt  
* **Kockázatcsökkentési**: A gazdagép a fürt egy másik üzemeltetett szolgáltatásban.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Leírás**: A kiszolgáló nem tudta frissíteni a fürt központi telepítésének állapotát.  
* **Kockázatcsökkentési**: Próbálja megismételni a műveletet. Ha ez történik, hogy többször is feldolgozza, lépjen kapcsolatba a CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Leírás**: Fürt *yourClusterName* karbantartás részeként törölve lett. Hozza létre újra a fürtöt.
* **Kockázatcsökkentési**: Hozza létre újra a fürtöt.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Leírás**: Érvénytelen a fürt konfigurációját. Nem található csomópontok méretét a fő csomópont-konfiguráció szükséges.
* **Kockázatcsökkentési**: Próbálja megismételni a műveletet.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Leírás**: Nem sikerült létrehozni az üzemeltetett szolgáltatás *nameOfYourHostedService*. Ismételje meg a kérelmet.  
* **Kockázatcsökkentési**: Ismételje meg a kérelmet.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Leírás**: Üzemeltetett szolgáltatás *nameOfYourHostedService* már rendelkezik az éles környezet. Üzemeltetett szolgáltatás nem tartalmazhat több éles környezetekben üzemelő példányok. Próbálja megismételni a kérelmet egy másik fürtnevet.
* **Kockázatcsökkentési**: Használjon egy másik fürtnevet, és ismételje meg a kérelmet.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Leírás**: Üzemeltetett szolgáltatás *nameOfYourHostedService* esetében a fürt nem található.  
* **Kockázatcsökkentési**: Ha a fürt hibás állapotú, törölje azt, és próbálkozzon újra.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Leírás**: Üzemeltetett szolgáltatás *nameOfYourHostedService* nincs társított központi telepítés nem.  
* **Kockázatcsökkentési**: Ha a fürt hibás állapotú, törölje azt, és próbálkozzon újra.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Leírás**: A SubscriptionId *yourSubscriptionId* hozhat létre fürtöt nincs magok balra *yourClusterName*. Kötelező: *resourcesRequired*, elérhető: *resourcesAvailable*.  
* **Kockázatcsökkentési**: Az előfizetésében erőforrásokat szabadíthat fel, vagy növelje az előfizetés számára elérhető erőforrások, és próbálja meg ismét létrehozni a fürtöt.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Leírás**: Előfizetés-azonosító *yourSubscriptionId* nem rendelkezik egy új üzemeltetett szolgáltatás létrehozása a fürt kvótával *yourClusterName*.  
* **Kockázatcsökkentési**: Az előfizetésében erőforrásokat szabadíthat fel, vagy növelje az előfizetés számára elérhető erőforrások, és próbálja meg ismét létrehozni a fürtöt.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Leírás**: Belső kiszolgálóhiba történt. Ismételje meg a kérelmet.  
* **Kockázatcsökkentési**: Ismételje meg a kérelmet.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Leírás**: Az Azure tárolási hely *dataRegionName* út nem egy érvényes hely. Ellenőrizze, hogy a régió megfelelő, és próbálkozzon újra a kérelmet.
* **Kockázatcsökkentési**: Válasszon tárolási helyet, amely támogatja a HDInsight, ellenőrizze, hogy a fürt ugyanott, és próbálja megismételni a műveletet.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Leírás**: Érvénytelen a virtuális gép mérete adatcsomópontok számára. Csak a "Nagy virtuális gép" mérete adatok minden csomópont esetében támogatott.  
* **Kockázatcsökkentési**: Adja meg a támogatott csomópont méretét, az adatok csomóponthoz, és próbálja megismételni a műveletet.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Leírás**: Érvénytelen Virtuálisgép-méretét fő csomópontot. Csak a "ExtraLarge VM" mérete fő csomópontja esetében támogatott.  
* **Kockázatcsökkentési**: Adja meg az átjárócsomópont támogatott csomópont méretét, és próbálja megismételni a műveletet

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Leírás**: Előfizetés-azonosító *yourSubscriptionId* használt nem rendelkezik megfelelő engedélyekkel a fürt delete műveletet végrehajtani *yourClusterName*.  
* **Kockázatcsökkentési**: Ha a fürt hibás állapotú, akkor dobja el, és próbálkozzon újra.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Leírás**: Külső tárfiók a blob tároló neve *yourContainerName* je neplatná. Ellenőrizze, hogy nevének betűvel kezdődik, és csak kisbetűket, számokat és kötőjelet tartalmaz.  
* **Kockázatcsökkentési**: Adja meg a tárfiók érvényes blob tároló nevének, és próbálja megismételni a műveletet.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Leírás**: Külső tárfiók konfigurációja *yourStorageAccountName* kell rendelkeznie a titkos kulcs adatai kell beállítani.  
* **Kockázatcsökkentési**: Adja meg a tárfiók érvényes titkos kulcsot, és próbálja megismételni a műveletet.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Leírás**: Verzió fejléce *yourVersionHeader* nem szerepel az érvényes formátum az éééé-hh-nn.  
* **Kockázatcsökkentési**: Adja meg a verziófejléc érvényes formátumú, és ismételje meg a kérelmet.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Leírás**: Érvénytelen a fürt konfigurációját. Egynél több fő csomópont-konfiguráció található.  
* **Kockázatcsökkentési**: Upraví konfiguraci úgy, hogy csak egy fő csomópontja van megadva.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Leírás**: A művelet nem fejeződött be a rendelkezésre álló időn belül, vagy az újrapróbálkozási kísérletek maximális száma lehetséges. Ismételje meg a kérelmet.  
* **Kockázatcsökkentési**: Ismételje meg a kérelmet.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Leírás**: A paraméter *yourParameterName* nem lehet null értékű vagy üres.  
* **Kockázatcsökkentési**: Adjon meg egy érvényes értéket a paraméterhez.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Leírás**: Egy vagy több fürt létrehozási kérés bemenet érvénytelen. Győződjön meg arról, hogy a bemeneti értékek helyesek, és próbálkozzon újra a kérelmet.  
* **Kockázatcsökkentési**: Győződjön meg arról, hogy a bemeneti értékek helyesek, és próbálkozzon újra a kérelmet.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Leírás**: Régió funkció nem érhető el a régióban *yourRegionName* és előfizetés-azonosító *yourSubscriptionId*.  
* **Kockázatcsökkentési**: Adjon meg egy régióban, amely a HDInsight-fürtöket támogatja. A nyilvánosan támogatott régiók a következők: Délkelet-Ázsia, Nyugat-Európa, Észak-Európa, USA keleti RÉGIÓJA vagy USA nyugati RÉGIÓJA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Leírás**: Storage-fiók *yourStorageAccountName* régióban található *currentRegionName*. Ugyanaz, mint a fürt a régióban kell lennie *yourClusterRegionName*.  
* **Kockázatcsökkentési**: Adja meg a storage-fiók ugyanabban a régióban, amely a fürt, vagy ha az adatok már a storage-fiókban, új fürt létrehozása a meglévő tárfiókot ugyanabban a régióban. A portált használja, ha a felhasználói felület előzetes értesíteni fogjuk őket a problémát.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Leírás**: A megadott előfizetés-azonosító *yourSubscriptionId* még nem aktív.  
* **Kockázatcsökkentési**: Az előfizetés újraaktiválásához, vagy készítsen egy új, érvényes előfizetéssel.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Leírás**: Előfizetés-azonosító *yourSubscriptionId* nem található.  
* **Kockázatcsökkentési**: Ellenőrizze, hogy az előfizetés-azonosító érvényes, és próbálja megismételni a műveletet.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Leírás**: Nem oldható fel DNS *yourDnsUrl*. Ellenőrizze, hogy a teljes URL-címet a blob-végpont.  
* **Kockázatcsökkentési**: Adjon meg egy érvényes blob URL-címet. Teljesen érvényes, beleértve a kezdve az URL-címet kell lennie *http://* és a záró *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Leírás**: Nem sikerült ellenőrizni az erőforráscsoport helyét *yourDnsUrl*. Ellenőrizze, hogy a teljes URL-címet a blob-végpont.  
* **Kockázatcsökkentési**: Adjon meg egy érvényes blob URL-címet. Teljesen érvényes, beleértve a kezdve az URL-címet kell lennie *http://* és a záró *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Leírás**: Verzió funkció nem érhető el a verzió *specifiedVersion* és előfizetés-azonosító *yourSubscriptionId*.  
* **Kockázatcsökkentési**: Válasszon egy olyanra, amely érhető el, és próbálja megismételni a műveletet.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Leírás**: Verzió *specifiedVersion* nem támogatott.
* **Kockázatcsökkentési**: Válasszon egy támogatott verzióra, és próbálja megismételni a műveletet.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Leírás**: Verzió *specifiedVersion* nem érhető el az Azure-régiókban *specifiedRegion*.  
* **Kockázatcsökkentési**: Válasszon egy olyanra, amely a megadott régióban támogatott, és próbálja megismételni a műveletet.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Leírás**: Érvénytelen a fürt konfigurációját. Szükséges WASB fiók konfigurációja nem található a külső fiókokat.  
* **Kockázatcsökkentési**: Ellenőrizze az, hogy a fiók létezik és megfelelően megadva a konfigurációban, és próbálja megismételni a műveletet.

## <a name="next-steps"></a>További lépések

* [Ambari-nézetek használata a HDInsight a Tez-feladatok hibakereséséhez](../hdinsight-debug-ambari-tez-view.md)
* [Halomürítések engedélyezése a Linux-alapú HDInsight a Hadoop-szolgáltatásokhoz](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [HDInsight-fürtök kezelése az Ambari webes felületével](../hdinsight-hadoop-manage-ambari.md)
