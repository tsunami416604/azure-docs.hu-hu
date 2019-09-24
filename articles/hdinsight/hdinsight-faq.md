---
title: Azure HDInsight – gyakori kérdések
description: Áttekintést nyújt az Azure HDInsight
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: v-miegge
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4accd8c2f58dd89fb5f918ca6e58e71caeaed57f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212749"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Gyakori kérdések

Ez a cikk az [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)futtatásával kapcsolatos leggyakoribb kérdésekre ad választ.

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight-fürtök létrehozása vagy törlése

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Hogyan kiépíteni egy HDInsight-fürtöt?

A rendelkezésre álló HDInsight-fürtök és a létesítési módszerek áttekintését lásd: [fürtök beállítása a HDInsight-ben Apache Hadoop, Apache Spark, Apache Kafka](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)stb.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hogyan törölni egy meglévő HDInsight-fürtöt?

Ha többet szeretne megtudni a fürt törléséről, ha már nincs használatban, tekintse meg a [HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-delete-cluster)című részt. Javasoljuk, hogy a létrehozás és törlés művelet között legalább 30 – 60 percet válasszon egyetlen fürtön. Ellenkező esetben előfordulhat, hogy a művelet meghiúsul, és a következő hibaüzenetet adja vissza:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hogyan válassza ki a számítási feladathoz tartozó magok vagy csomópontok megfelelő számát?

A magok és egyéb konfigurációs lehetőségek megfelelő száma különböző tényezőktől függ.

További információ: HDInsight- [fürtök kapacitásának megtervezése](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Mi a teendő, ha a fürt kiépítése nem sikerül a kapacitási probléma miatt?

Ebben a szakaszban az általános kapacitással kapcsolatos problémák és a kockázatcsökkentő technikák találhatók.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Hiba: Az üzembe helyezés meghaladja a (z) "800" kvótáját

Az Azure kvótakorlátja 800 üzemelő példány egy erőforráscsoportban. A rendszer a különböző kvótákat erőforráscsoport, előfizetés, fiók vagy más hatókörök alapján alkalmazza. Lehetséges például, hogy az előfizetése úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Ha olyan virtuális gépet próbál telepíteni, amely a megengedettnél több magot tartalmaz, hibaüzenet jelenik meg, amely jelzi, hogy túllépte a kvótát.

A probléma megoldásához törölje a már nem szükséges központi telepítéseket a Azure Portal, a CLI vagy a PowerShell használatával.

További információ: [Erőforráskvótákkal kapcsolatos hibák elhárítása](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Hiba: A maximális csomópont túllépte az elérhető magokat ebben a régióban

Lehetséges, hogy az előfizetés úgy van konfigurálva, hogy korlátozza a régiónként elérhető magok számát. Ha olyan erőforrást próbál telepíteni, amely a megengedettnél több magot tartalmaz, hibaüzenet jelenik meg, amely jelzi, hogy túllépte a kvótát.

A kvóta növelésének kéréséhez kövesse az alábbi lépéseket:

1. Lépjen a Azure Portalra, és válassza a Súgó + támogatás lehetőséget.
2. Válassza az új támogatási kérelem lehetőséget.
3. Az új támogatási kérelem lapon válassza ki az alábbi beállításokat az alapok lapon:

   * Probléma típusa: Szolgáltatási és előfizetési korlátok (kvóták)
   * Előfizetés: A módosítani kívánt előfizetés
   * Kvóta típusa: HDInsight

További információ: [támogatási jegy létrehozása a magok növelésére](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Milyen típusú csomópontok vannak egy HDInsight-fürtben?

Az Azure HDInsight-fürtök különböző típusú virtuális gépekkel vagy csomópontokkal rendelkeznek. Minden csomópont-típus a rendszer működésében játszik szerepet.

További információ: [erőforrástípusok az Azure HDInsight-fürtökben](https://docs.microsoft.com/azure/hdinsight/hdinsight-virtual-network-architecture#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Egyedi összetevők

### <a name="can-i-install-additional-components-on-my-cluster"></a>Telepíthetek további összetevőket a fürtön?

Igen, a következő módszerek bármelyikével telepítheti a további összetevőket, vagy testreszabhatja a fürt konfigurációját:

* Parancsfájlok használata a létrehozás során vagy után. Ezeket a parancsfájlokat egy parancsfájl- [művelet](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)hívja meg. Ezt a konfigurációs lehetőséget a Azure Portal, a HDInsight Windows PowerShell-parancsmagok vagy a HDInsight .NET SDK használatával lehet használni. 
* A sudo vagy más metódusok használata a fürt kiépítés után.
* Az [HDInsight alkalmazás platform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) használata az ökoszisztéma-alkalmazások telepítéséhez.

Microsoft ügyfélszolgálata csapatok azonban csak a következő helyzetekben nyújthatnak támogatást:

* A parancsfájl betöltésekor előforduló problémák vagy hibák. Az egyéni parancsfájlok végrehajtása során felmerülő hibák a támogatási jegy hatókörén kívül esnek. 
* A fürt létrehozási folyamatának részét képező további alkalmazások. 

A támogatott összetevők listáját lásd: [Mik a Apache Hadoop összetevők és verziók érhetők el a HDInsight-ben?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

Az egyes összetevők támogatása fürttípustól függően is változhat. Például a Spark nem támogatott egy Kafka-fürtön, és fordítva.

A fürt létrehozási folyamatán kívüli alkalmazásokhoz vagy szolgáltatásokhoz forduljon a megfelelő gyártóhoz vagy szolgáltatóhoz a támogatáshoz. Ezen műveletek támogatásához a közösségi helyeket is használhatja. Számos közösségi webhely elérhető. Ilyenek például [az MSDN-fórum a HDInsight és a](https://social.msdn.microsoft.com/Forums/home?forum=hdinsight) [stack overflow](https://stackoverflow.com/questions/tagged/hdinsight). Az Apache-projektek az [Apache webhelyén](https://apache.org/)is rendelkeznek Project-webhelyekkel. Az egyik példa a [Hadoop](https://hadoop.apache.org/).

 Az Azure-támogatással kapcsolatos további kérdésekért tekintse át az [Azure-támogatással kapcsolatos gyakori](https://azure.microsoft.com/en-us/support/faq/)kérdéseket.

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Frissíthetem a fürtön előre telepített egyes összetevőket?

Ha a fürtön előre telepített beépített összetevőket vagy alkalmazásokat frissít, az eredményül kapott konfigurációt a Microsoft nem fogja támogatni. Ezeket a rendszerkonfigurációkat a Microsoft nem tesztelte. Próbálja meg a HDInsight-fürt egy másik verzióját használni, amelyen már telepítve van az összetevő frissített verziója.

Például a struktúra frissítése önálló összetevőként nem támogatott. A HDInsight egy felügyelt szolgáltatás, és számos szolgáltatás integrálva van a Ambari-kiszolgálóval és teszteléssel. A struktúra saját maga általi frissítése a más összetevők indexelt bináris fájljait is megváltoztatja, és az összetevő-integrációs problémákat okoz a fürtön.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>A Spark és a Kafka ugyanazon a HDInsight-fürtön fut?

Nem, nem lehet futtatni Apache Kafka és Apache Spark ugyanazon a HDInsight-fürtön. A felhasználóknak külön fürtöket kell létrehozniuk, hogy elkerülje az erőforrás-tartalommal kapcsolatos problémákat.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hogyan változás időzónája a Ambari-ben?

1. Nyissa meg a Ambari webes https://CLUSTERNAME.azurehdinsight.net felhasználói felületét, ahol a CLUSTERNAME a fürt neve.
2. A jobb felső sarokban válassza a rendszergazda elemet | Beállítások. 

   ![Ambari-beállítások](media/hdinsight-faq/ambari-settings.png)

3. A felhasználói beállítások ablakban válassza ki az új időzónát az időzóna legördülő listából, majd kattintson a Mentés gombra.

   ![Ambari-felhasználói beállítások](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metaadattár

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Hogyan telepíthetek át a meglévő metaadattár az Azure SQL Serverba? 

Az SQL Serverról az Azure SQL Serverba való áttelepítéshez lásd [: oktatóanyag: SQL Server migrálása önálló adatbázisba vagy készletezett adatbázisba Azure SQL Database offline módban a DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)használatával.

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Törlődik a Hive-metaadattár a fürt törlésekor?

Ez attól függ, hogy a fürt milyen metaadattár használatára van konfigurálva.

Alapértelmezett metaadattár: Az alapértelmezett metaadattár a fürt életciklusának része. Amikor töröl egy fürtöt, a rendszer a megfelelő metaadattár és metaadatokat is törli.

Egyéni metaadattár esetén: A metaadattár életciklusa nem kötődik a fürt életciklusához. Ezért a fürtöket a metaadatok elvesztése nélkül hozhatja létre és törölheti. Az olyan metaadatok, mint például a struktúra sémái a HDInsight-fürt törlése és újbóli létrehozása után is megmaradnak.

További információ: [külső metaadat-tárolók használata az Azure HDInsight-ben](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Áttelepíti a Hive-metaadattár a Ranger-adatbázis alapértelmezett házirendjeit is?

Nem, a házirend-definíció a Ranger-adatbázisban található. Ezért a Ranger-adatbázis áttelepítése nem telepíti át a szabályzatot.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Áttelepíthet Hive-metaadattár egy Enterprise Security Package (ESP) fürtről egy nem ESP-fürtre, és fordítva?

Igen, áttelepíthet egy Hive-metaadattár ESP-ről egy nem ESP-fürtre.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hogyan lehet megbecsülni egy Hive-metaadattár adatbázis méretét?

Hive-metaadattár a rendszer a kaptár-kiszolgáló által használt adatforrások metaadatainak tárolására szolgál. Ezért a méretre vonatkozó követelményeket a Kaptárhoz használni kívánt adatforrások száma, valamint az adatforrások összetettsége befolyásolja. Ezért a méret nem becsülhető meg előre. Az [Hive-metaadattár ajánlott eljárások](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores#hive-metastore-best-practices)szerint az S2-es szinten kezdődően 50 DTU és 250 GB tárterületet biztosít. Szűk keresztmetszet esetén az adatbázis vertikális felskálázását is elvégezheti.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Más adatbázisokat is támogat, mint a külső metaadattár Azure SQL Database?

Nem, a Microsoft csak a külső egyéni metaadattár Azure SQL Database támogatja.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Megoszthatok egy metaadattár több fürt között?

Igen, az egyéni metaadattár több fürtön is megoszthatja, feltéve, hogy a HDInsight azonos verzióját használják.

## <a name="connectivity-and-virtual-networks"></a>Kapcsolat és virtuális hálózatok  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Mik a hálózatán a 22-es és a 23-as portok blokkolásának következményei?

Ha letiltja a 22-es és a 23-as portot, nem fog SSH-hozzáféréssel rendelkezni a fürthöz. Ezeket a portokat a HDInsight szolgáltatás nem használja.

További információ a következő dokumentumokban található:

* [Hálózati forgalom szabályozása](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)
* [Biztonságos bejövő forgalom HDInsight-fürtökhöz privát végponttal rendelkező virtuális hálózaton](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
* [HDInsight-felügyeleti IP-címek](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Telepíthetek egy további virtuális gépet ugyanazon az alhálózaton belül, mint HDInsight-fürtöt?

Igen, egy további virtuális gépet is telepíthet a HDInsight-fürttel azonos alhálózaton belül. A következő konfigurációk lehetségesek:

* Peremhálózati csomópontok: Egy másik peremhálózati csomópontot is hozzáadhat a fürthöz, a következő témakörben leírtak szerint: az [üres peremhálózati csomópontok használata Apache Hadoop fürtökön a HDInsight-ben](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node). 
* Önálló csomópontok:  Hozzáadhat egy önálló virtuális gépet ugyanahhoz az alhálózathoz, és elérheti a fürtöt a virtuális gépről a privát végpont `https://<CLUSTERNAME>-int.azurehdinsight.net`használatával. További információ: a [hálózati forgalom szabályozása](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic).

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Hozzáadhatok egy meglévő HDInsight-fürtöt egy másik virtuális hálózathoz?

Nem, meglévő HDInsight-fürtöt nem adhat hozzá másik virtuális hálózathoz. A virtuális hálózatot a kiépítés időpontjában kell megadni. Ha a kiépítés során nincs megadva virtuális hálózat, a központi telepítés olyan belső hálózatot hoz létre, amely kívülről nem érhető el. További információ: [HDInsight hozzáadása meglévő virtuális hálózathoz](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#existingvnet).

## <a name="security-and-certificates"></a>Biztonság és tanúsítványok

Mik azok a Microsoft-javaslatok a kártevők elleni védelemhez az Azure HD Insight-fürtökön?

További információ a kártevők elleni védelemről: a [Microsoft antimalware for Azure Cloud Services és Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hogyan hozzon létre egy keytab kiterjesztésű egy HDInsight ESP-fürthöz?

A tartomány felhasználónevéhez létrehozhat egy Kerberos-keytab kiterjesztésű. Ezt a keytab kiterjesztésű később is használhatja a távoli tartományhoz csatlakoztatott fürtök hitelesítéséhez jelszó megadása nélkül. Figyelje meg a tartománynév nagybetűs karaktereit:

```
o ktutil 
o ktutil: addent -password -p stXX@ DOMAIN.COM -k 1 -e RC4-HMAC 
o Password for stXX@DOMAIN.COM: provided by lab instructors 
o ktutil: wkt stXX.keytab 
o ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Használhatok meglévő Azure Active Directory bérlőt egy ESP-vel rendelkező HDInsight-fürt létrehozásához?

A Azure Active Directory Domain Services (HRE-DS) engedélyezése az előfeltétele, mielőtt olyan HDInsight-fürtöt hozna létre, amely ESP-t tartalmaz. A nyílt forráskódú Hadoop a Kerberos hitelesítésre támaszkodik (a OAuth szemben).

A virtuális gépek tartományhoz való csatlakoztatásához tartományvezérlőre van szükség. A HRE – DS a felügyelt tartományvezérlő. Ez a HRE kiterjesztését képezi, amely biztosítja az összes Kerberos-követelményt, hogy felügyelt módon hozzon létre egy biztonságos Hadoop-fürtöt. A HDInsight felügyelt szolgáltatás a HRE-DS-vel integrálható a teljes körű biztonság biztosításához.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Használhatok önaláírt tanúsítványt egy HRE-DS Secure LDAP-beállításban, és kiépíteni egy ESP-fürtöt?

Bár a hitelesítésszolgáltató (CA) által kiállított tanúsítvány használata ajánlott, az ESP-ben az önaláírt tanúsítvány használata is támogatott.

További információkért lásd:

* [Az Azure AD-DS engedélyezése](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#enable-azure-ad-ds)
* [Oktatóanyag: Biztonságos LDAP konfigurálása Azure Active Directory Domain Services felügyelt tartományhoz](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hogyan hívhatom le a Rangerben látható bejelentkezési tevékenységet?

A naplózási követelményekhez a Microsoft javasolja, hogy engedélyezze az Azure Monitor-naplókat a [HDInsight-fürtök figyelése Azure monitor naplók használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)című témakörben leírtak

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Le lehet tiltani a ClamScan a fürtön?

A ClamScan a HDInsight-fürtön futó víruskereső szoftver, amelyet az Azure Security (azsecd) használ a fürtök vírusfertőzés elleni védelme érdekében. A Microsoft nyomatékosan javasolja, hogy a felhasználók ne végezzenek változást az alapértelmezett ClamScan-konfigurációban.

Ez a folyamat nem zavarja vagy nem hajtja végre a többi folyamattól eltérő ciklusokat. Ez mindig más folyamatnak fog történni. A ClamScan származó CPU-tüskéket csak akkor kell látni, ha a rendszer üresjáratban van.  

Olyan helyzetekben, amelyekben meg kell határoznia az ütemtervet, a következő lépéseket hajthatja végre:

1. Tiltsa le az automatikus végrehajtást a következő parancs használatával:

   ``/usr/local/vbin/azsecd config -s clamav -d Disabled``

2. Hozzon létre egy cron-feladatot, amely root-ként futtatja ezt a parancsot:

   ``/usr/local/bin/azsecd manual -s clamav``

A cron-feladatok beállításával és futtatásával kapcsolatos további információkért lásd: [Hogyan cron-feladatok beállítása](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Hozzáadhatok Azure Data Lake Storage Gen2 egy meglévő HDInsight-fürthöz további Storage-fiókként?

Nem. Jelenleg nem adhat hozzá Azure Data Lake Storage Gen2t egy meglévő HDInsight-fürthöz, amely a blob Storage-ot használja elsődleges tárolóként. További információ: a [tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options). 

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hogyan találhatom meg a jelenleg társított egyszerű szolgáltatást egy Data Lake Storage-fiókhoz?

A beállítások ellenőrzéséhez tekintse át Data Lake Storage Gen1 hozzáférését a fürt tulajdonságai között a portálon. További információ: a [fürt beállításának ellenőrzése](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#verify-cluster-set-up).
 
Hogyan számítható ki a Storage-fiókok és a blob-tárolók használata a HDInsight-fürtökhöz?
Az alábbi eljárások egyikét használhatja:

* [A PowerShell használata](https://docs.microsoft.com/azure/storage/scripts/storage-blobs-container-calculate-size-powershell)
* A/User/Hive/. méretét is megtalálhatja A következő parancs használatával a HDI-fürtökön található Kuka/mappa:

   ``hdfs dfs -du -h /user/hive/.Trash/``

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hogyan állíthatok be naplózást a blob Storage-fiókomhoz?

A blob Storage-fiókok naplózásához konfigurálnia kell a blob Storage-fiók figyelését a [Azure Portal Storage-fiók figyelése](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)című témakörben ismertetett eljárással. Egy HDFS-naplózási napló csak a helyi HDFS fájlrendszer (hdfs://mycluster) naplózási információit tartalmazza.  Nem tartalmazza a távoli tárolón végzett műveleteket.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hogyan vihetők át fájlok egy blob-tároló és egy HDInsight-csomópont között?

A blob-tárolók és a HDInsight közötti fájlokat átviheti egy rendszerhéj-parancsfájl futtatásával a fő csomóponton, az alábbi példához hasonlóan:

```
for i in cat filenames.txt

do
         hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> A *fájlnév. txt* fájl a blob-tárolókban lévő fájlok abszolút elérési útját fogja tartalmazni.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Vannak-e a Storage-beli Ranger-beépülő modulok?

Jelenleg nem létezik Ranger beépülő modul a blob Storage-hoz, Azure Data Lake Storage (ADLS) Gen1 vagy Azure Data Lake Storage Gen2hoz. Az ESP-fürtök esetében ajánlott eljárásként használja a ADLS-t, és a HDFS-eszközök használatával manuálisan állítsa be a részletes kiőrlési engedélyeket a fájlrendszer szintjén. Az ESP-fürtök emellett a fájlrendszer-hozzáférés-vezérlést is elvégzik a HRE használatával a ADLS használatakor. 

Az alábbi cikkekben ismertetett eljárásokkal az Adathozzáférési házirendek biztonsági csoportokhoz való hozzárendeléséhez használhatja a Azure Storage Explorer.

* [Hogyan az Azure AD-felhasználók számára a kaptár vagy más szolgáltatások használatával történő adatlekérdezésre vonatkozó engedélyeket Data Lake Storage Gen2.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
* [Fájl-és könyvtár-szintű engedélyek beállítása Azure Storage Explorer és Azure Data Lake Storage Gen2 használatával](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Megnövelhető a fürt HDFS-tárolója a munkavégző csomópontok lemezének méretének növelése nélkül?

Nem, a munkavégző csomópontok lemezének mérete nem növelhető. A lemez méretének növelésének egyetlen módja a fürt eldobása, majd újbóli létrehozása a nagyobb munkavégző virtuális gépeken.

A Microsoft nem javasolja, hogy a HDFS használatával tárolja a HDInsight tárolt adatait, mert az adatai törlődnek a fürt törlésekor. Ehelyett azt javasoljuk, hogy az adatait az Azure-ban tárolja. A fürt vertikális felskálázása további kapacitást is hozzáadhat a HDInsight-fürthöz.

## <a name="edge-nodes"></a>Határcsomópontok

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Hozzá lehet adni egy Edge-csomópontot a fürt létrehozása után?

Igen, hozzáadhat egy üres peremhálózati csomópontot egy meglévő HDInsight-fürthöz vagy egy új fürthöz a fürt létrehozásakor.

További információt az [üres határcsomópontok a HDInsightban az Apache Hadoop-fürtökön való használatát](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node) ismertető útmutatóban talál.

### <a name="how-can-i-connect-to-an-edge-node"></a>Hogyan lehet csatlakozni egy peremhálózati csomóponthoz?

Az Edge-csomópont létrehozása után az SSH-val a 22-es porton keresztül kapcsolódhat a peremhálózati csomóponthoz. A peremhálózati csomópont nevét a fürt portálon találja (a nevek általában "-ED" végződéssel vannak kiválasztva).

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Miért nem futnak automatikusan az újonnan létrehozott Edge-csomópontokon a megőrzött parancsfájlok?

A megőrzött parancsfájlok segítségével testre szabhatók a fürthöz hozzáadott új munkavégző csomópontok a skálázási műveletek révén, és nem alkalmazhatók az Edge-csomópontokra.

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Mik a REST API hívások a TEZ lekérdezési nézetének lekéréséhez a fürtből?

A következő REST-végpontok segítségével lekérheti a szükséges információkat, ahol a válasz JSON formátumú lesz. Ezeket a kérelmeket az alapszintű hitelesítési fejlécek segítségével teheti meg.

* "TEZ lekérdezés" nézet – https://`<cluster name>`. azurehdinsight.net/ws/v1/Timeline/HIVE_QUERY_ID/

* "TEZ Dag" nézet – https://`<cluster name>`. azurehdinsight.net/ws/v1/Timeline/TEZ_DAG_ID/

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hogyan lekérni a konfigurációs adatokat a HDI-fürtből egy Azure Active Directory felhasználó használatával?

Ha a HRE-felhasználóval egyeztetni szeretné a megfelelő hitelesítési jogkivonatokat, az átjárót a következő formátumban kell megtennie:

* https://`<cluster dnsname>`. azurehdinsight.NET/API/v1/Clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hogyan a Ambari REST-API-t használja a fonal teljesítményének figyeléséhez?

Ha a curl parancs ugyanabban a VNet vagy egyenrangú VNet van meghívva, használja a következő parancsot:

``curl -u <cluster login username> -sS -G http://headnodehost:8080/api/v1/clusters/anssenllap/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
Ha a parancs hívása a VNet kívülről vagy nem társ VNet történik, a parancs formátuma a következő:
 
* Nem ESP-fürt esetén:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
* ESP-fürt esetén:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``

> [!NOTE]
> A curl kérni fogja a jelszó megadását. Érvényes jelszót kell megadnia a fürt bejelentkezési felhasználónevéhez.

## <a name="billing"></a>Számlázás

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Mennyibe kerül a HDInsight-fürt üzembe helyezése?

A számlázással kapcsolatos díjszabásról és gyakori kérdésekért lásd az [Azure HDInsight díjszabását](https://azure.microsoft.com/pricing/details/hdinsight/) ismertető oldalt.

### <a name="how-do-i-cancel-my-subscription"></a>Hogyan lemondani az előfizetést?

További információ az előfizetés lemondásáról: [Az Azure-előfizetés megszakítása](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Az utólagos elszámolású előfizetések esetén mi történik az előfizetés megszakítása után?

Az előfizetéssel kapcsolatos további információkért lásd: [Mi történik az előfizetés megszakítása után?](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription#what-happens-after-i-cancel-my-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Miért jelenik meg a struktúra verziója 2,1 helyett 1.2.1000 néven a Ambari felhasználói felületen, bár HDInsight 3,6-fürtöt futtatok?

Bár a Ambari felhasználói felületén csak 1,2 jelenik meg, a HDInsight 3,6 a kaptár 1,2 és a kaptár 2,1-t is tartalmazza.

## <a name="other-faq"></a>Egyéb gyakori kérdések

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Mit kínál a HDInsight ajánlat a valós idejű stream-feldolgozási képességek tekintetében?

További információ a stream feldolgozásának az Azure HDInsight való kezelésének integrálási lehetőségeiről: [stream Processing Technology kiválasztása az Azure-ban](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Van mód a fürt fő csomópontjának dinamikus leállítására, ha a fürt egy adott időszakban tétlen?

Nem, nem lehet dinamikusan megszakítani a fürt fő csomópontját. Ehhez a forgatókönyvhöz Azure Data Factory is használhatja.
