---
title: Az Azure HDInsight gyakori kérdései
description: Gyakori kérdések a HDInsightról
keywords: gyakori kérdések, gyik
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 8a37e1b9bc4a0b953dc727dbab2813dd938ed576
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652222"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: gyakori kérdések

Ez a cikk az [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight-fürtök létrehozása vagy törlése

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Hogyan tudok kiépíteni egy HDInsight-fürtöt?

Az elérhető HDInsight-fürtök típusainak és a kiépítési módszereknek a [megtekintéséhez lásd: Fürtök beállítása a HDInsightban az Apache Hadoop, az Apache Spark, az Apache Kafka és egyebek](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)segítségével.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hogyan törölhetek egy meglévő HDInsight-fürtöt?

Ha többet szeretne tudni afürt törléséről, ha már nincs használatban, olvassa el [a HDInsight-fürt törlése ..](hdinsight-delete-cluster.md)

Hagyjon legalább 30–60 percet a létrehozási és törlési műveletek között. Ellenkező esetben a művelet a következő hibaüzenettel sikertelen lehet:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hogyan választhatom ki a megfelelő számú magok vagy csomópontok a munkaterhelés?

A magok és egyéb konfigurációs lehetőségek megfelelő száma különböző tényezőktől függ.

További információt a [HDInsight-fürtök kapacitástervezése című témakörben talál.](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Mi a teendő, ha a fürtök üzembe helyezése kapacitási probléma miatt meghiúsul?

Ebben a szakaszban gyakori kapacitáskibocsátási hibák és kockázatcsökkentési technikák találhatók.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Hiba: A központi telepítés túllépné a "800" kvótát

Az Azure kvótakorlátja 800 üzemelő példány egy erőforráscsoportban. Erőforráscsoportonként, előfizetésenként, fiókonként vagy más hatókörökönként különböző kvóták vonatkoznak. Lehetséges például, hogy az előfizetése úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Ha olyan virtuális gépet próbál üzembe helyezni, amely több maggal rendelkezik, mint az engedélyezett mennyiség, hibaüzenet jelenik meg, amely arról értesíti a kvótát.

A probléma megoldásához törölje azokat a központi telepítéseket, amelyekre már nincs szükség az Azure Portal, a CLI vagy a PowerShell használatával.

További információ: [Erőforráskvótákkal kapcsolatos hibák elhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Hiba: A maximális csomópont meghaladta a rendelkezésre álló magokat ebben a régióban

Lehetséges, hogy az előfizetés úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Ha olyan erőforrást próbál üzembe helyezni, amely több maggal rendelkezik, mint az engedélyezett összeg, hibaüzenet jelenik meg, amely szerint a kvóta túllépése.

A kvóta növelésének kéréséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és válassza a **Súgó + támogatás**lehetőséget.
   
1. Válassza **az Új támogatási kérelem lehetőséget.**
   
1. Az **Új támogatási kérelem** lap **Alapjai** lapján adja meg a következő információkat:
   
   - **Probléma típusa:** Válassza a **Szolgáltatás- és előfizetési korlátokat (kvótákat).**
   - **Előfizetés:** Jelölje ki a módosítani kívánt előfizetést.
   - **Kvóta típusa:** Válassza a **HDInsight**lehetőséget.

További információ: [Támogatási jegy létrehozása a magok számának növeléséhez](hdinsight-capacity-planning.md#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Milyen típusú csomópontok egy HDInsight-fürtben?

Az Azure HDInsight-fürtök különböző típusú virtuális gépekkel vagy csomópontokkal rendelkeznek. Minden csomóponttípus szerepet játszik a rendszer működésében.

További információ: [Erőforrástípusok az Azure HDInsight-fürtökben.](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)

## <a name="individual-components"></a>Különálló összetevők

### <a name="can-i-install-additional-components-on-my-cluster"></a>Telepíthetek további összetevőket a fürtömre?

Igen. További összetevők telepítéséhez vagy a fürt konfigurációjának testreszabásához használja a következőket:

- Parancsfájlok létrehozása közben vagy után. A parancsfájlok [meghívása parancsfájl-művelettel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)történik, amely az Azure Portalon, a HDInsight Windows PowerShell-parancsmagokban vagy a HDInsight .NET SDK-ból használható konfigurációs beállítás. Ez a konfigurációs beállítás az Azure Portalon, a HDInsight Windows PowerShell-parancsmagokban vagy a HDInsight .NET SDK-ból használható.

- [HDInsight alkalmazásplatform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) az ökoszisztéma-alkalmazások telepítéséhez.

A támogatott összetevők listáját a [Következő kban, mint az Apache Hadoop-összetevők és -verziók a HDInsight segítségével elérhetők?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Frissíthetem a fürtre előre telepített egyes összetevőket?

Ha olyan beépített összetevőket vagy alkalmazásokat frissít, amelyek előre telepítve vannak a fürtön, a Microsoft nem támogatja az eredményül kapott konfigurációt. Ezeket a rendszerkonfigurációkat a Microsoft nem tesztelte. Próbálja meg a HDInsight-fürt egy másik verzióját használni, amely már rendelkezik az összetevő frissített verziójával.

Például a Hive frissítése egyedi összetevőként nem támogatott. A HDInsight egy felügyelt szolgáltatás, és számos szolgáltatás integrálva van az Ambari kiszolgálóval és tesztelt. Ha saját maga frissíti a Hive-ot, a többi összetevő indexelt bináris fájljai megváltoznak, és összetevő-integrációs problémákat okoznak a fürtön.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>A Spark és a Kafka ugyanazon a HDInsight-fürtön futtatható?

Nem, az Apache Kafka és az Apache Spark nem futtathat ugyanazon a HDInsight-fürtön. Hozzon létre külön fürtöket a Kafka és a Spark számára az erőforrás-versengési problémák elkerülése érdekében.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hogyan változtathatom meg az időzónát Ambari ban?

1. Nyissa meg az Ambari `https://CLUSTERNAME.azurehdinsight.net`Web felhasználói felületét a területén, ahol a CLUSTERNAME a fürt neve.
2. A jobb felső sarokban válassza az admin | Beállítások. 

   ![Ambari beállítások](media/hdinsight-faq/ambari-settings.png)

3. A Felhasználói beállítások ablakban válassza ki az új időzónát az Időzóna legördülő menüből, majd kattintson a Mentés gombra.

   ![Ambari felhasználói beállítások](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metaadattár

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Hogyan telepíthetők át a meglévő metatárolóból az Azure SQL Serverkiszolgálóra? 

Az SQL Server kiszolgálóról az Azure SQL Serverkiszolgálóra való áttelepítéshez olvassa el az [Oktatóanyag: Sql Server áttelepítése egyetlen adatbázisba vagy az Azure SQL Database készletes adatbázisába kapcsolat nélküli módban a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md)című témakört.

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>A Hive metastore törlődik a fürt törlésekor?

Ez a fürt által használt metatároló típusától függ.

Alapértelmezett metatároló esetén: Az alapértelmezett metatár a fürt életciklusának része. Fürt törlésekor a megfelelő metatár és metaadatok is törlődnek.

Egyéni metatár: A metatároló életciklusa nem kötődik a fürt életciklusához. Ezért a metaadatok elvesztése nélkül is létrehozhat és törölhet fürtöket. Metaadatok, például a Hive-séma továbbra is fennáll, még a HDInsight-fürt törlése után, és újra létrehozza a HDInsight-fürt.

További információ: [Külső metaadat-tárolók használata az Azure HDInsightban című témakörben.](hdinsight-use-external-metadata-stores.md)

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>A Hive metastore áttelepítése is áttelepíti a Ranger-adatbázis alapértelmezett házirendjeit?

Nem, a házirend-definíció a Ranger adatbázisban van, így a Ranger-adatbázis áttelepítése áttelepíti a házirendet.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Át tud migrálni egy Hive metastore-t egy vállalati biztonsági csomag (ESP) fürtről egy nem ESP-fürtre, és fordítva?

Igen, áttelepítheti a Hive metastore-t egy ESP-ről egy nem ESP-fürtre.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hogyan becsülhetem meg egy Hive metastore adatbázis méretét?

A Hive metatároló a Hive-kiszolgáló által használt adatforrások metaadatainak tárolására szolgál. A méretkövetelmények részben a Hive adatforrások számától és összetettségétől függenek, és nem becsülhetők meg előre. A [Hive metastore irányelveiben](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)vázolt módon elkezdheti egy S2-réteggel, amely 50 DTU-t és 250 GB tárhelyet biztosít, és ha szűk keresztmetszetet lát, felskálázhatja az adatbázist.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Az Azure SQL Database-en kívül más adatbázist is támogat külső metatárolóként?

Nem, a Microsoft csak az Azure SQL Database-t támogatja külső egyéni metatárolóként.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Megoszthatok egy metatárolót több fürtön keresztül?

Igen, megoszthatja az egyéni metatárolót több fürtön keresztül, feltéve, hogy a HDInsight ugyanazon verzióját használják.

## <a name="connectivity-and-virtual-networks"></a>Kapcsolat és virtuális hálózatok  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Milyen következményekkel jár a 22-es és 23-as portok blokkolása a hálózaton?

Ha blokkolja a 22-es és a 23-as portot, nem lesz SSH-hozzáférése a fürthöz. Ezeket a portokat a HDInsight szolgáltatás nem használja.

További információ a következő dokumentumokban található:

- [Hálózati forgalom szabályozása](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Bejövő forgalom biztonságossá tétele a HDInsight-fürtök befelé irányuló, privát végpontot rendelkező virtuális hálózatban](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight-kezelés IP-címei](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Telepíthetek egy további virtuális gépet ugyanazon az alhálózaton belül, mint egy HDInsight-fürt?

Igen, egy további virtuális gépet telepíthet ugyanazon az alhálózaton belül, mint egy HDInsight-fürt. A következő konfigurációk lehetségesek:

- Peremhálózati csomópontok: Hozzáadhat egy másik peremhálózati csomópontot a fürthöz, ahogy azt az [Apache Hadoop-fürtök üres peremhálózati csomópontjainak használata a HDInsightban](hdinsight-apps-use-edge-node.md)című részben leírtak szerint.

- Önálló csomópontok: Hozzáadhat egy önálló virtuális gépet ugyanahhoz az alhálózathoz, és hozzáférhet a fürthöz a saját végpont `https://<CLUSTERNAME>-int.azurehdinsight.net`használatával. További információt a [Hálózati forgalom szabályozása](hdinsight-plan-virtual-network-deployment.md#networktraffic)című témakörben talál.

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Tároljam az adatokat egy peremhálózati csomópont helyi lemezén?

Nem, az adatok helyi lemezen történő tárolása nem jó ötlet. Ha a csomópont meghibásodik, a helyileg tárolt összes adat elvész. Javasoljuk, hogy az azure Data Lake Storage Gen2 vagy az Azure Blob storage-ban tárolja az adatokat, vagy az adatok tárolásához azure-fájlok megosztást szereljen fel.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Hozzáadhatok egy meglévő HDInsight-fürtöt egy másik virtuális hálózathoz?

Nem, nem lehet. A virtuális hálózatot a kiépítés időpontjában meg kell adni. Ha a kiépítés során nincs megadva virtuális hálózat, a központi telepítés olyan belső hálózatot hoz létre, amely kívülről nem érhető el. További információt a [HDInsight hozzáadása meglévő virtuális hálózathoz](hdinsight-plan-virtual-network-deployment.md#existingvnet)című témakörben talál.

## <a name="security-and-certificates"></a>Biztonság és tanúsítványok

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Milyen javaslatok at tartalmaz a kártevők védelmére az Azure HDInsight-fürtökön?

A kártevők elleni védelemről a [Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../security/fundamentals/antimalware.md)című témakörben talál további információt.

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hogyan hozhatok létre kulcslapot egy HDInsight ESP-fürthöz?

Hozzon létre egy Kerberos-billentyűlapot a tartomány felhasználónevéhez. Ezt a billentyűt később jelszó megadása nélkül is hitelesítheti távoli tartományhoz csatlakozó fürtökszámára. A tartománynév nagybetűs:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Használhatok egy meglévő Azure Active Directory-bérlőt egy OLYAN HDInsight-fürt létrehozásához, amely rendelkezik az ESP-vel?

HdInsight-fürt létrehozása az ESP-vel való létrehozása előtt engedélyeznie kell az Azure Active Directory tartományi szolgáltatásokat (Azure AD DS). A nyílt forráskódú Hadoop a Kerberos hitelesítésre támaszkodik (szemben az OAuth-tal).

Ahhoz, hogy virtuális gépeket csatlakoztasson egy tartományhoz, tartományvezérlővel kell rendelkeznie. Az Azure AD DS a felügyelt tartományvezérlő, és az Azure Active Directory olyan bővítményének tekinti, amely biztosítja a Biztonságos Hadoop-fürt felügyelt módon történő létrehozásához szükséges összes Kerberos-követelményt. A HDInsight felügyelt szolgáltatásként integrálható az Azure AD DS-szel, hogy teljes körű biztonságot nyújtson.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Használhatok önaláírt tanúsítványt egy AAD-DS biztonságos LDAP-beállításban, és kiépíthetek egy ESP-fürtöt?

A hitelesítésszolgáltató által kiállított tanúsítvány használata ajánlott, de az ESP-ben is támogatott az önaláírt tanúsítvány használata. További információkért lásd:

- [Az Active Directory Domain Services engedélyezése](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Oktatóanyag: Biztonságos LDAP konfigurálása egy Azure Active Directory tartományi szolgáltatások felügyelt tartományához](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hogyan tudom lehúzni a Ranger-ben látható bejelentkezési aktivitást?

Naplózási követelmények esetén a Microsoft azt javasolja, hogy engedélyezzék az Azure Monitor naplóinak engedélyezését az Azure Monitor naplók használata a [HDInsight-fürtök figyeléséhez](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)című részen leírtak szerint.

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Letilthatom a Clamscan a fürtömön?

A Clamscan a HDInsight-fürtön futó víruskereső szoftver, amelyet az Azure security (azsecd) használ a fürtök vírustámadások elleni védelmére. A Microsoft nyomatékosan javasolja, hogy a felhasználók tartózkodjanak az alapértelmezett Clamscan-konfiguráció módosításától.

Ez a folyamat nem zavarja, vagy vesz egy ciklust távol más folyamatok. Ez mindig enged más folyamatnak. Cpu tüskék clamscan kell látni, csak akkor, ha a rendszer tétlen.  

Azokban az esetekben, amikor szabályozhatja az ütemezést, a következő lépéseket használhatja:

1. Tiltsa le az automatikus végrehajtást a következő paranccsal:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Adjon hozzá egy Cron-feladatot, amely a következő parancsot futtatja gyökérként:
   
   `/usr/local/bin/azsecd manual -s clamav`

A cron-feladat beállításáról és futtatásáról a [Hogyan állíthatok be Cron-feladatot?](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Miért érhető el az LLAP a Spark ESP-fürtökön?
Az ESP Spark-fürtökön az LLAP biztonsági okokból (azaz Apache Ranger) engedélyezve van, nem teljesítmény miatt. Az LLAP erőforrás-használatához (pl. minimális D13V2) nagyobb csomóponti virtuális gépeket kell használnia. 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Hogyan vehetek fel további AAD-csoportokat esp-fürt létrehozása után?
Ezt kétféleképpen lehet elérni: 1- Újra létrehozhatja a fürtöt, és hozzáadhatja a további csoportot a fürt létrehozásakor. Ha hatókörrel szinkronizált szinkronizálást használ az AAD-DS-ben, győződjön meg arról, hogy a B csoport szerepel a hatókörrel szinkronizált szinkronizálásban.
2- Adja hozzá a csoportot az ESP-fürt létrehozásához használt előző csoport beágyazott alcsoportjaként. Ha például létrehozott egy ESP-fürtöt a csoporttal, `A` `B` később a csoport egymásba ágyazott alcsoportjaként is hozzáadhat, `A` és körülbelül egy óra elteltével a rendszer automatikusan szinkronizálja és elérhetővé teszi a fürtben. 

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Hozzáadhatok egy Azure Data Lake Storage Gen2-t egy meglévő HDInsight-fürthöz további tárfiókként?

Nem, jelenleg nem lehet azure Data Lake Storage Gen2 tárfiókot hozzáadni egy olyan fürthöz, amely elsődleges tárolóként blob storage-t használ. További információt a [Tárolási beállítások összehasonlítása](hdinsight-hadoop-compare-storage-options.md)című témakörben talál.

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hogyan találhatom meg a Data Lake tárfiók aktuálisan összekapcsolt Szolgáltatásnévit?

A beállításokat a **Data Lake Storage Gen1 access** a fürt tulajdonságai az Azure Portalon. További információt a [Fürtbeállítás ellenőrzése című](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)témakörben talál.
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Hogyan számíthatom ki a tárfiókok és a blobtárolók használatát a HDInsight-fürtjeimhez?

Tegye a következők egyikét:

- [A PowerShell használata](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Keresse meg a */user/hive/ méretét. Kuka/* mappa a HDInsight-fürtön, a következő parancssorhasználatával:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hogyan állíthatom be a blob storage-fiók naplózását?

Blob storage-fiókok naplózásához konfigurálja a figyelést az [Azure Portalon egy tárfiók figyelése](../storage/common/storage-monitor-storage-account.md)című eljárással. A HDFS-naplózási napló csak a helyi HDFS fájlrendszer naplózási adatait tartalmazza (hdfs://mycluster).  Nem tartalmazza a távtárolón végzett műveleteket.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hogyan vihetek át fájlokat egy blobtároló és egy HDInsight-főcsomópont között?

Futtasson egy, a következő rendszerhéj-parancsfájlhoz hasonló parancsfájlt a főcsomóponton:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> A *filenames.txt* fájl a blobtárolókban lévő fájlok abszolút elérési útját fogja eljárni.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Vannak-e Ranger plugins tárolására?

Jelenleg nem létezik Ranger-bővítmény a blobstorage hoz és az Azure Data Lake Storage Gen1 vagy Gen2 szolgáltatáshoz. ESP-fürtök esetén az Azure Data Lake Storage-t kell használnia, mert legalább manuálisan beállíthatja a finomkodó engedélyeket a fájlrendszer szintjén a HDFS-eszközök használatával. Emellett az Azure Data Lake Storage használatakor az ESP-fürtök az Azure Active Directory fürtszintjén az Azure Active Directory használatával a fájlrendszer-hozzáférés-vezérlés egy részét hajtják végre. 

Adatelérési szabályzatokat rendelhet a felhasználók biztonsági csoportjaihoz az Azure Storage Explorer használatával. További információkért lásd:

- [Hogyan állíthatok be engedélyeket az Azure AD-felhasználók számára az adatok lekérdezéséhez a Data Lake Storage Gen2-ben a Hive vagy más szolgáltatások használatával?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Fájl- és könyvtárszintű engedélyek beállítása az Azure Storage Explorer használatával az Azure Data Lake Storage Gen2 segítségével](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Növelhetem a HDFS-tárolók at a fürtön a munkavégző csomópontok lemezméretének növelése nélkül?

Nem, nem növelheti a munkavégző csomópontok lemezméretét, így a lemezméret növelésének egyetlen módja a fürt ledobása és nagyobb feldolgozó virtuális gépeken való újbóli létrehozása. Ne használja a HDFS-t a HDInsight-adatok tárolására, mert az adatok törlődnek, ha törli a fürtöt. Ehelyett tárolja az adatokat az Azure-ban. A fürt felskálázása további kapacitást is hozzáadhat a HDInsight-fürthöz.

## <a name="edge-nodes"></a>Határcsomópontok

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Hozzáadhatok egy peremcsomópontot a fürt létrehozása után?

HDInsight-fürthöz vagy egy új fürthöz a fürt létrehozásakor. További információt az [üres határcsomópontok a HDInsightban az Apache Hadoop-fürtökön való használatát](hdinsight-apps-use-edge-node.md) ismertető útmutatóban talál.

### <a name="how-can-i-connect-to-an-edge-node"></a>Hogyan csatlakozhatok egy peremcsomóponthoz?

Miután létrehozott egy peremhálózati csomópontot, a 22-es porton az SSH használatával csatlakozhat hozzá. A peremhálózati csomópont nevét a fürtportálon találja. A nevek általában *-ed végződéssel*végződnek.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Miért nem futnak automatikusan a megőrzött parancsfájlok az újonnan létrehozott peremhálózati csomópontokon?

A megőrzött parancsfájlok segítségével testreszabhatja a fürthöz méretezési műveleteken keresztül hozzáadott új munkavégző csomópontokat. A megőrzött parancsfájlok nem vonatkoznak a peremhálózati csomópontokra.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Melyek a REST API-hívások lekérése a Tez lekérdezési nézet a fürtből?

A következő REST-végpontok segítségével json formátumban lehívhatja a szükséges információkat. A kérelmek teljesítéséhez használjon egyszerű hitelesítési fejléceket.

- Tez lekérdezésnézet: *https:\//\<fürtnév>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- Tez Dag Nézet: *https:\//\<fürtnév>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hogyan kérhetem le a konfigurációs adatokat a HDI-fürtből egy Azure Active Directory-felhasználó használatával?

Ha megfelelő hitelesítési jogkivonatokat szeretne egyeztetni az AAD-felhasználóval, menjen át az átjárón a következő formátumban:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hogyan használhatom az Ambari Restful API-t a YARN teljesítményének figyelésére?

Ha ugyanabban a virtuális hálózatban vagy társviszonyt létesített virtuális hálózatban hívja meg a Curl parancsot, a parancs a következő:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Ha a parancsot a virtuális hálózaton kívülről vagy nem társviszony-létesített virtuális hálózatról hívja fel, a parancsformátum a következő:

- Nem ESP-fürt esetén:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- ESP-fürt esetén:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> A Curl jelszót kér. Meg kell adnia egy érvényes jelszót a fürt bejelentkezési felhasználónevéhez.

## <a name="billing"></a>Számlázás

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Mennyibe kerül egy HDInsight-fürt üzembe helyezése?

A számlázással kapcsolatos díjszabásról és gyakori kérdésekről az [Azure HDInsight díjszabási](https://azure.microsoft.com/pricing/details/hdinsight/) lapján talál további információt.

### <a name="when-does-hdinsight-billing-start--stop"></a>Mikor kezdődik a HDInsight-számlázás, & leáll?

A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percenkénti arányos.

### <a name="how-do-i-cancel-my-subscription"></a>Hogyan mondhatom le az előfizetésemet?

Az előfizetés lemondásáról az [Azure-előfizetés lemondása](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)című témakörben talál további információt.

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>A felosztó-kiosztó előfizetések esetén mi történik az előfizetés lemondása után?

Az előfizetés lemondása után a Mi [történik az előfizetés lemondása után című témakörben talál.](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Miért jelenik meg a Hive-verzió 1.2.1000-ként az Ambari felhasználói felületén a 2.1 helyett, annak ellenére, hogy HDInsight 3.6-os fürtöt futtatok?

Bár csak 1.2 jelenik meg az Ambari felhasználói felületen, a HDInsight 3.6 tartalmazza a Hive 1.2 és a Hive 2.1.

## <a name="other-faq"></a>Egyéb gyakori kérdések

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Mit kínál a HDInsight a valós idejű adatfolyam-feldolgozási képességek tekintetében?

Az Azure HDInsight adatfolyam-feldolgozásának integrációs képességeiről az [Adatfolyam-feldolgozási technológia kiválasztása az Azure-ban című témakörben](/azure/architecture/data-guide/technology-choices/stream-processing)talál további információt.

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Van-e módja annak, hogy dinamikusan megszünteti a fő csomópont a fürt, ha a fürt tétlen egy adott időszakban?

Ezt HDInsight-fürtökkel nem teheti meg. Ezekhez a forgatókönyvekhez használhatja az Azure Data Factory-t.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Milyen megfelelőségi ajánlatokat kínál a HDInsight?

A megfelelőségről a [Microsoft Adatvédelmi központban](https://www.microsoft.com/trust-center) és a Microsoft [Azure-megfelelőség áttekintésében](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)olvashat.
