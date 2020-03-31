---
title: Regionális vészhelyreállítás az Azure Databricks számára
description: Ez a cikk az Azure Databricks vész-helyreállítási műveletek et ismerteti.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 2604d5b357feacce3493b4a4ded971144262611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161936"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regionális vészhelyreállítás az Azure Databricks-fürtökhöz

Ez a cikk ismerteti az Azure Databricks-fürtök számára hasznos vész-helyreállítási architektúrát, és a tervezés végrehajtásának lépéseit.

## <a name="azure-databricks-architecture"></a>Azure Databricks architektúra

Magas szinten, amikor létrehoz egy Azure Databricks munkaterületet az Azure Portalon, egy [felügyelt berendezés](../azure-resource-manager/managed-applications/overview.md) azure-erőforrásként üzembe helyezve az előfizetésben, a kiválasztott Azure-régióban (például AZ USA nyugati régiójában). Ez a készülék egy [Azure virtuális hálózatban](../virtual-network/virtual-networks-overview.md) van telepítve egy [hálózati biztonsági csoporttal](../virtual-network/manage-network-security-group.md) és egy Azure Storage-fiókkal, amely az előfizetésben érhető el. A virtuális hálózat peremhálózati biztonságot nyújt a Databricks munkaterületnek, és a hálózati biztonsági csoporton keresztül védett. A munkaterületen belül databricks-fürtök et hozhat létre a dolgozó és az illesztőprogram virtuális gép típusának és databricks futásidejű verziójának biztosításával. A megőrzött adatok a tárfiókban érhetők el, amely lehet Az Azure Blob Storage vagy az Azure Data Lake Storage. A fürt létrehozása után jegyzetfüzeteken, REST API-kon, ODBC/JDBC-végpontokon keresztül futtathatja a feladatokat egy adott fürthöz csatolva.

A Databricks vezérlősík kezeli és figyeli a Databricks munkaterületi környezetet. Minden felügyeleti művelet, például a fürt létrehozása a vezérlősíkról indul. Minden metaadat, például az ütemezett feladatok, egy Azure-adatbázisban tárolják a hibatűrés georeplikációval.

![Databricks architektúra](media/howto-regional-disaster-recovery/databricks-architecture.png)

Az architektúra egyik előnye, hogy a felhasználók csatlakoztathatják az Azure Databricks-t a fiókjuk bármely tárerőforrásához. A legfontosabb előnye, hogy mind a számítási (Azure Databricks) és a storage egymástól függetlenül skálázható.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Hogyan hozzunk létre egy regionális katasztrófa-helyreállítási topológia

Amint azt az előző architektúra leírása, számos összetevő azure Databricks Azure Databricks: Azure Storage, Azure Database és más adatforrások a Big Data-folyamat azure Databricks: Azure Storage, Azure Database és egyéb adatforrások. Az Azure Databricks a Big Data-folyamat *számítási.* Ez *a rövid élettartamú* jellegű, ami azt jelenti, hogy míg az adatok továbbra is elérhető az Azure Storage-ban, a *számítási* (Azure Databricks-fürt) lehet leállítani, így nem kell fizetni a számítási, ha nincs rá szüksége. A *számítási* (Azure Databricks) és a tárolási források ugyanabban a régióban kell lennie, hogy a feladatok nem tapasztalnak nagy késést.  

Saját regionális vész-helyreállítási topológia létrehozásához kövesse az alábbi követelményeket:

   1. Több Azure Databricks-munkaterület kiépítése külön Azure-régiókban. Létrehozhatja például az elsődleges Azure Databricks-munkaterületet AZ USA keleti részén2. Hozza létre a másodlagos vész-helyreállítási Azure Databricks munkaterületet egy külön régióban, például az USA nyugati régiójában.

   2. [Georedundáns tárolás használata.](../storage/common/storage-redundancy.md) Az Azure Databricks-hez kapcsolódó adatok alapértelmezés szerint az Azure Storage-ban tárolódnak. A Databricks-feladatok eredményeit az Azure Blob Storage is tárolja, így a feldolgozott adatok tartósak, és a fürt leállítása után is magas rendelkezésre állású marad. Mivel a Storage és databricks fürt közös helyen található, georedundáns tárolást kell használnia, hogy az adatok elérhetők legyenek a másodlagos régióban, ha az elsődleges régió már nem érhető el.

   3. A másodlagos régió létrehozása után át kell telepítenie a felhasználókat, a felhasználói mappákat, a jegyzetfüzeteket, a fürtkonfigurációt, a feladatok konfigurációját, a könyvtárakat, a tárolót, az init parancsfájlokat, és újra kell konfigurálnia a hozzáférés-vezérlést. A további részleteket a következő szakasz ismerteti.

## <a name="detailed-migration-steps"></a>Részletes migrálási lépések

1. **A Databricks parancssori felület beállítása a számítógépen**

   Ez a cikk számos kódpéldát mutat be, amelyek a parancssori felületet használják a legtöbb automatikus lépéshez, mivel ez egy könnyen felhasználóra bontható az Azure Databricks REST API felett.

   Az áttelepítési lépések végrehajtása előtt telepítse a databricks-cli-t az asztali számítógépre vagy egy virtuális gépre, ahol a munkát tervezi. További információ: [Databricks CLI telepítése](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Az ebben a cikkben található python-parancsfájlok várhatóan a Python 2.7+ < 3.x-szel fognak működni.

2. **Konfiguráljon két profilt.**

   Konfiguráljon egyet az elsődleges munkaterülethez, és egy másikat a másodlagos munkaterülethez:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   A cikkben található kódblokkok a megfelelő munkaterületi paranccsal váltanak a profilok között minden további lépésben. Győződjön meg arról, hogy a létrehozott profilok nevei minden kódblokkba bekerülnek.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Szükség esetén manuálisan válthat a parancssorból:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Az Azure Active Directory felhasználóinak áttelepítése**

   Manuálisan adja hozzá ugyanazokat az Azure Active Directory-felhasználókat az elsődleges munkaterületen létező másodlagos munkaterülethez.

4. **Felhasználói mappák és jegyzetfüzetek áttelepítése**

   A következő python-kód használatával telepítse át a sandboxed felhasználói környezetek, amelyek magukban foglalják a beágyazott mappastruktúra és notebookok felhasználónként.

   > [!NOTE]
   > A függvénytárak nem másolhatók át ebben a lépésben, mivel az alapul szolgáló API nem támogatja azokat.

   Másolja és mentse a következő python parancsfájlt egy fájlba, és futtassa a Databricks parancssorában. Például: `python scriptname.py`.

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

5. **A fürtkonfigurációk áttelepítése**

   A jegyzetfüzetek áttelepítése után szükség esetén áttelepítheti a fürtkonfigurációkat az új munkaterületre. Ez szinte egy teljesen automatizált lépés a databricks-cli használatával, hacsak nem szeretne szelektív fürtkonfigurációs áttelepítést végezni, nem pedig mindenki számára.

   > [!NOTE]
   > Sajnos nincs létrehozási fürt konfigurációs végpontja, és ez a parancsfájl azonnal megpróbálja létrehozni az egyes fürtöket. Ha nincs elegendő mag az előfizetésben, a fürt létrehozása sikertelen lehet. A hiba figyelmen kívül hagyható, amíg a konfiguráció átvitele sikeresen.

   A következő parancsfájl a régitől az új fürtazonosítókig történő hozzárendelést nyomtatja ki, amely később használható a feladatok áttelepítéséhez (a meglévő fürtök használatára konfigurált feladatokhoz).

   Másolja és mentse a következő python parancsfájlt egy fájlba, és futtassa a Databricks parancssorában. Például: `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json, os

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE])
   clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append(cluster_info.split(None, 1)[0])

   for cluster_info in clusters_info_list: 
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]

   print(str(len(clusters_list)) + " clusters found in the primary site" )

   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " + cluster)
      cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
      print ("Got cluster config from old workspace")

       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' : 
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

      for key in cluster_json_keys:
         if key not in cluster_req_elems:
            cluster_req_json.pop(key, None)

      # Create the cluster, and store the mapping from old to new cluster ids

      #Create a temp file to store the current cluster info as JSON
      strCurrentClusterFile = "tmp_cluster_info.json" 

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

      fClusterJSONtmp = open(strCurrentClusterFile,"w+")
      fClusterJSONtmp.write(json.dumps(cluster_req_json))
      fClusterJSONtmp.close()

      #cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE])
      cluster_create_out = check_output(["databricks", "clusters", "create", "--json-file", strCurrentClusterFile , "--profile", IMPORT_PROFILE])
      cluster_create_out_json = json.loads(cluster_create_out)
      cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

      print ("Cluster create request sent to secondary site workspace successfully")
      print ("---------------------------------------------------------")

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

   print ("Cluster mappings: " + json.dumps(cluster_old_new_mappings))
   print ("All done")
   print ("P.S. : Please note that all the new clusters in your secondary site are being started now!")
   print ("       If you won't use those new clusters at the moment, please don't forget terminating your new clusters to avoid charges")
   ```

6. **A feladatok konfigurációjának áttelepítése**

   Ha az előző lépésben telepítette át a fürtkonfigurációkat, választhatja a feladatkonfigurációk áttelepítését az új munkaterületre. Ez egy teljesen automatizált lépés segítségével databricks-cli, kivéve, ha azt szeretné, hogy nem szelektív munkát config migráció helyett csinálja az összes munkát.

   > [!NOTE]
   > Az ütemezett feladat konfigurációja tartalmazza az "ütemezés" adatait is, így alapértelmezés szerint, amely a konfigurált időzítésszerint fog működni, amint az áttelepítéstörténik. Ezért a következő kódblokk eltávolítja az ütemezési adatokat az áttelepítés során (hogy elkerülje a régi és az új munkaterületeken való duplikált futtatásokat). Konfigurálja az ilyen feladatok ütemezését, ha készen áll az átvágásra.

   A feladat konfigurációja új vagy meglévő fürt beállításait igényli. Meglévő fürt használata esetén az alábbi parancsfájl /kód megpróbálja lecserélni a régi fürtazonosítót új fürtazonosítóra.

   Másolja és mentse a következő python parancsfájlt egy fájlba. Cserélje le `old_cluster_id` az `new_cluster_id`értéket, és a, a kimenet a fürt áttelepítése végzett az előző lépésben. Futtassa a databricks-cli parancssorában, `python scriptname.py`például .

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

7. **Tárak áttelepítése**

   Jelenleg nincs egyszerű módja a könyvtárak áttelepítésének egyik munkaterületről a másikra. Ehelyett telepítse újra ezeket a könyvtárakat manuálisan az új munkaterületre. Lehetőség van automatizálni kombinációja [segítségével DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) feltölteni egyéni könyvtárak a munkaterület és [könyvtárak CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Az Azure blobstorage és az Azure Data Lake Storage csatlakoztatásai**

   Manuálisan telepítse újra az összes [Azure Blob storage-ot](/azure/databricks/data/data-sources/azure/azure-storage) és [az Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) csatlakoztatási pontjait egy jegyzetfüzet-alapú megoldás használatával. A tárolóerőforrások az elsődleges munkaterületen lettek volna csatlakoztatva, és ezt meg kell ismételni a másodlagos munkaterületen. Nincs külső API a csatlakoztatások.

9. **Fürtinit parancsfájlok áttelepítése**

   A fürtinicializálási parancsfájlok áttelepíthetők a régi munkaterületről az új munkaterületre a [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples)segítségével. Először másolja a szükséges `dbfs:/dat abricks/init/..` parancsfájlokat a helyi asztalra vagy virtuális gépre. Ezután másolja ezeket a parancsfájlokat az ugyanazon az útvonalon lévő új munkaterületre.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Manuálisan konfigurálja újra és alkalmazza újra a hozzáférés-vezérlést.**

    Ha a meglévő elsődleges munkaterület a prémium szint (SKU) használatára van konfigurálva, akkor valószínűleg a [hozzáférés-vezérlési szolgáltatást](/azure/databricks/administration-guide/access-control/index)is használja.

    Ha a hozzáférés-vezérlés szolgáltatást használja, manuálisan alkalmazza újra a hozzáférés-vezérlést az erőforrásokra (jegyzetfüzetek, fürtök, feladatok, táblázatok).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Katasztrófa utáni helyreállítás az Azure-ökoszisztémában

Ha más Azure-szolgáltatásokat is használ, győződjön meg arról, hogy a vész-helyreállítási ajánlott eljárásokat is valósítja meg. Ha például egy külső Hive metastore-példányt használ, fontolja meg az [Azure SQL Server,](../sql-database/sql-database-disaster-recovery.md) [az Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)és/vagy az Azure Database for MySQL vészutáni [helyreállítását.](../mysql/concepts-business-continuity.md) A vész-helyreállítási általános tudnivalókat lásd: [Vész-helyreállítási Azure-alkalmazások.](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)

## <a name="next-steps"></a>További lépések

További információ: [Azure Databricks dokumentáció.](index.yml)