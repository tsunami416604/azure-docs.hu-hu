---
title: Regionális vészhelyreállítás az Azure Databricksnek
description: Ez a cikk ismerteti az Azure Databricksben vész-helyreállítási előkészítésétől megközelítés.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 354f6014e3230b65a0c4f1cd7507e58ca94474dd
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188101"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Az Azure Databricks-fürtök esetén a regionális vészhelyreállítás

Ez a cikk ismerteti a vész helyreállítási-architektúra az Azure Databricks-fürtök esetén hasznos, és a lépések elvégzéséhez a tervező.

## <a name="azure-databricks-architecture"></a>Az Azure Databricks-architektúra

Magas szintű, az Azure Portalról az Azure Databricks-munkaterület létrehozásakor egy [kezelt készülék](../managed-applications/overview.md) van üzembe helyezve egy Azure-erőforrást az előfizetésében, a kiválasztott Azure-régióban (például USA nyugati RÉGIÓJA). A készülék üzemel egy [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) együtt egy [hálózati biztonsági csoport](../virtual-network/manage-network-security-group.md) és a egy Azure Storage-fiókot, az előfizetésben elérhető. A virtuális hálózat szegélyhálózat-alapú biztonság a Databricks-munkaterülethez biztosít, és a hálózati biztonsági csoport használatával védett. A munkaterületen belül a Databricks-fürtök azáltal, hogy a feldolgozói és VM-típus illesztőprogram és Databricks futtatókörnyezet-verziója hozhat létre. A megőrzött adatok a storage-fiók, amely lehet az Azure Blob Storage vagy az Azure Data Lake Store érhető el. A fürt létrehozása után futtathat feladatokat notebookokat, a REST API-k, ODBC/JDBC végpontok keresztül az adott fürt csatolásával.

A Databricks vezérlősík kezeli, és figyeli a Databricks-munkaterület környezet. Minden felügyeleti művelet, például a fürt létrehozása kezdődik, a vezérlősík. Minden metaadatait, ütemezett feladatok, például az Azure-adatbázis, a georeplikáció hibatűrése tárolódik.

![Databricks-architektúra](media/howto-regional-disaster-recovery/databricks-architecture.png)

Ez az architektúra előnye egyik célja, hogy felhasználók csatlakozhatnak az Azure Databricks bármely tárolási erőforrás-fiókjukban. Egy fő előnye, hogy mindkettő (az Azure Databricks) számítási és a tárolás egymástól függetlenül skálázhatók.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>A regionális katasztrófa utáni helyreállítás topológiák létrehozása

Megfigyelheti, hogy az előző architektúra leírásában, mivel számos Big Data-adatcsatornák az Azure Databricks használt összetevők:  Az Azure Storage, Azure Database és más adatforrásokhoz. Az Azure databricks a *számítási* a Big Data-folyamat. Ez *rövid élettartamú* jellegűek, ami azt jelenti, hogy közben továbbra is elérhetők az Azure Storage, a *számítási* (az Azure Databricks-fürt) állíthatók le, hogy nem kell fizetnie a számítási mikor, nincs szükség. A *számítási* (az Azure Databricks) és tárolóforrás ugyanabban a régióban kell lennie, hogy a feladatok nagy a késés nem működik.  

A saját regionális katasztrófa utáni helyreállítás topológia létrehozásához hajtsa végre ezeket a követelményeket:

   1. Több Azure Databricks-munkaterületek külön Azure-régióban üzembe helyezheti. Például hozzon létre az elsődleges Azure Databricks-munkaterület az USA 2. keleti régiója. A vész-helyreállítási Azure Databricks-munkaterület létrehozása egy külön régióban, például az USA nyugati RÉGIÓJA.

   2. Használat [georedundáns tárolás](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). A társított Azure Databricks az Azure Storage-ban alapértelmezés szerint tárolt adatokat. Az eredmények a Databricks-feladatok is tárolása az Azure Blob Storage, hogy a feldolgozott adatok tartós és magas rendelkezésre állású marad, miután a fürt megszakad. A Storage és a Databricks-fürt vannak elhelyezve, georedundáns tárolás úgy, hogy adatokat is elérhető a másodlagos régióba, ha az elsődleges régióban nem érhető el kell használnia.

   3. A másodlagos régió létrehozása után a felhasználók, felhasználói mappákat, notebookok, fürtkonfiguráció, konfigurációs feladatok, könyvtárak, tárolási, init parancsfájlok migrálhatja, és az konfigurálja újra a hozzáférés-vezérlés. További részleteket a következő részben ismertetett eljárásokat.

## <a name="detailed-migration-steps"></a>Részletes áttelepítési lépéseit

1. **A számítógépen a Databricks parancssori felület beállítása**

   Ez a cikk egy hitelesítésikód-példák, mivel ez egy egyszerű felhasználói burkoló keresztül az Azure Databricks REST API-t használja az automatizált lépéseket a legtöbb a parancssori felület számát mutatja.

   Áttelepítési lépések végrehajtása előtt telepítse a databricks parancssori felület az asztali számítógépen vagy egy virtuális gépet, ha azt tervezi, hogy ezt a munkát. További információkért lásd: [Databricks CLI telepítése](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > A cikkben minden olyan python-szkriptek a működését a Python 2.7 + várhatóan < 3.x.

2. **Két profilok konfigurálása.**

   Az egyik az elsődleges munkaterület és egy másik, a másodlagos munkaterület konfigurálása:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   A kód tiltása, ha ez a cikk kapcsolóban minden későbbi lépés, használja a megfelelő munkaterület-parancsot a profilok közötti. Győződjön meg arról, hogy a létrehozott profilok nevei, mindegyik blokk helyettesítik.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Manuálisan, válthat a parancssorból, szükség esetén:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Azure Active Directory-felhasználók migrálása**

   Az azonos Azure Active Directory – felhasználók manuális felvétele a másodlagos munkaterületen lévő elsődleges munkaterülete.

4. **A felhasználói mappák és jegyzetfüzetek áttelepítése**

   A következő python-kód segítségével áttelepítheti az elkülönített felhasználói környezeteket, amelyek tartalmazzák a beágyazott mappastruktúra és jegyzetfüzetek felhasználónként.

   > [!NOTE]
   > Könyvtárak nem másolódnak át keresztül ebben a lépésben, a mögöttes API nem támogatja azokat.

   Másolja ki és a következő python-szkript mentése fájlba, és futtassa azt a Databricks parancssori. Például: `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **A fürt konfigurációjának áttelepítése**

   Miután áttelepítette a notebookok, igény szerint áttelepítheti a fürtkonfigurációkat az új munkaterülethez. Majdnem egy databricks-felületről, teljes mértékben automatizált lépés, kivéve, ha szeretné, hogy szelektív cluster config az áttelepítés végrehajtásához az összes helyett.

   > [!NOTE]
   > Sajnos nem nincs létrehozás fürtvégpont konfigurációs, és ez a szkript megpróbálja azonnal minden egyes fürt létrehozásához. Ha nincs elég mag az előfizetésben elérhető, a fürt létrehozása sikertelen lehet. A hiba figyelmen kívül hagyható, mindaddig, amíg a konfiguráció sikeresen átvitele esetén.

   A következő parancsfájl a megadott jelenít meg leképezése a régiről az új fürtön azonosítóval, amely lehet használni a feladat áttelepítési később (feladatokhoz, amelyek a meglévő fürtök használatára vannak konfigurálva).

   Másolja ki és a következő python-szkript mentése fájlba, és futtassa azt a Databricks parancssori. Például: `python scriptname.py`.

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **A feladatok konfiguráció áttelepítéséhez**

   Ha át az előző lépésben szereplő fürtkonfigurációkat, kikapcsolhatja az feladatkonfigurációkat áttelepíteni az új munkaterülethez. Ez lépése egy teljesen automatizált használatával a databricks parancssori felület, kivéve, ha szeretné, hogy szelektív feladat konfigurációs az áttelepítés végrehajtásához végezze el az összes feladat helyett.

   > [!NOTE]
   > Ütemezett feladat konfigurációját is, az "ütemezés" információkat tartalmaz, ezért a alapértelmezés szerint, melynek a kezdő konfigurált időzítési megfelelően működik, amint azt a lett migrálva. Ezért a következő kódblokk eltávolítja minden ütemezési információkat (korábbi és új munkaterületek között ismétlődő futtatások elkerülésére) az áttelepítés során. Az ilyen feladatok ütemezésének konfigurálása után készen áll a megoldásról.

   A feladat konfigurációs beállításai egy új vagy meglévő fürthöz kell rendelkeznie. Ha meglévő fürtöt használ, az alábbi parancsfájl /code megkísérli cserélje le a régi fürt azonosító új fürtöt.

   Másolja és mentse a következő python szkript fájlba. Cserélje le az értéket a `old_cluster_id` és `new_cluster_id`, az előző lépés kész, a fürt áttelepítése kimenete. Futtassa azt a databricks-cli parancssori, például `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Szalagtárak áttelepítése**

   Jelenleg nincs egyszerű mód kódtárak áttelepíteni egy munkaterületről egy másikra. Ehelyett telepítse újra ezeket a kódtárakat az új munkaterületre manuálisan. Lehetséges együttes használatával automatizálható [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) egyéni kódtárak feltöltéséhez a munkaterületre, és [kódtárak CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Az Azure blob storage-bA migrálhatja, és csatlakoztatja az Azure Data Lake Store**

   Manuálisan csatlakoztassa újra az összes [Azure Blob storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) és [Azure Data Lake Store (1. generációs)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html) csatlakoztatási pontok Jegyzetfüzet-alapú megoldások használata. A tárolási erőforrások lenne van csatlakoztatva az elsődleges munkaterület, és a másodlagos munkaterületen kell ismételni, amely rendelkezik. Nincsenek külső API csatlakoztatása a van nem.

9. **Fürt init parancsfájlok áttelepítése**

   Minden olyan fürt inicializációs szkriptjeinek telepíthető át régi új munkaterület használatával a [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Először másolja a szükséges szkriptek a `dbfs:/dat abricks/init/..` a helyi számítógépére vagy virtuális gép. Ezután másolja ezeket a szkripteket, ugyanazt az utat az új munkaterülethez.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Manuálisan konfigurálja újra, és alkalmazza újra a hozzáférés-vezérlés.**

    Ha a meglévő elsődleges munkaterület a prémium csomag (SKU) használatára van konfigurálva, akkor valószínű is használ a [hozzáférés-vezérlés funkció](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control).

    A hozzáférés-vezérlés szolgáltatását használja, ha manuálisan adja meg újból a hozzáférés-vezérlést az erőforrások (notebookok, fürtök, feladatok, táblákat).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Az Azure-ökoszisztéma vészhelyreállítása

Ha más Azure-szolgáltatásokat használ, mindenképpen végrehajtása túl a katasztrófa utáni helyreállítás ajánlott eljárások az ezeket a szolgáltatásokat. Például ha egy külső Hive-metaadattár példány használatát választja, fontolja meg a vész-helyreállítási [Azure SQL Server](../sql-database/sql-database-disaster-recovery.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md), és/vagy [, Azure Database for MySQL ](../mysql/concepts-business-continuity.md). Vész-helyreállítási kapcsolatos általános információkért lásd: [vészhelyreállítása az Azure-alkalmazások](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>További lépések

További információkért lásd: [Azure Databricks dokumentációja](https://docs.azuredatabricks.net/user-guide/index.html).
