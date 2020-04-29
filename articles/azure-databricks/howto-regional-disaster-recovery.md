---
title: Regionális katasztrófa-helyreállítási Azure Databricks
description: Ez a cikk a Azure Databricks vész-helyreállításának módszerét ismerteti.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 2604d5b357feacce3493b4a4ded971144262611d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77161936"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regionális vész-helyreállítás Azure Databricks-fürtökhöz

Ez a cikk a Azure Databricks fürtökhöz hasznos vész-helyreállítási architektúrát, valamint a terv megvalósításának lépéseit ismerteti.

## <a name="azure-databricks-architecture"></a>Azure Databricks architektúra

Magas szinten, amikor létrehoz egy Azure Databricks munkaterületet a Azure Portalból, a [felügyelt készülék](../azure-resource-manager/managed-applications/overview.md) Azure-erőforrásként lesz üzembe helyezve az előfizetésben, a kiválasztott Azure-régióban (például az USA nyugati régiójában). Ezt a készüléket egy [Azure-Virtual Network](../virtual-network/virtual-networks-overview.md) helyezi üzembe, egy [hálózati biztonsági csoporttal](../virtual-network/manage-network-security-group.md) és egy Azure Storage-fiókkal, amely elérhető az előfizetésében. A virtuális hálózat peremhálózati szintű biztonságot nyújt a Databricks-munkaterülethez, és hálózati biztonsági csoporton keresztül védett. A munkaterületen belül Databricks-fürtöket hozhat létre a feldolgozó és az illesztőprogram virtuálisgép-típusának és Databricks-futtatókörnyezetének megadásával. A megőrzött adatai elérhetők a Storage-fiókban, amely lehet Azure Blob Storage vagy Azure Data Lake Storage. A fürt létrehozása után a feladatokat jegyzetfüzetek, REST API-k, ODBC/JDBC végpontok segítségével futtathatja, ha egy adott fürthöz csatolja őket.

A Databricks vezérlő síkja felügyeli és figyeli a Databricks-munkaterület környezetét. Minden felügyeleti művelet, például a fürt létrehozása a vezérlési síkon lesz kezdeményezve. Az összes metaadatot, például az ütemezett feladatokat egy Azure-adatbázisban tárolja a rendszer a hibatűrés érdekében a Geo-replikációval.

![Databricks architektúra](media/howto-regional-disaster-recovery/databricks-architecture.png)

Ennek az architektúrának az egyik előnye, hogy a felhasználók a fiókjában bármilyen tárolási erőforráshoz kapcsolódhatnak Azure Databricks. Ennek egyik legfőbb előnye, hogy mind a számítás (Azure Databricks), mind a tárterület egymástól függetlenül méretezhető.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Regionális katasztrófa-helyreállítási topológia létrehozása

Ahogy az architektúra leírásában is látható, számos összetevőt használhat a Big adatfolyamatokhoz Azure Databricks: Azure Storage, az Azure Database és más adatforrásokkal. Azure Databricks a Big adatfolyamatok *számítási* felépítése. Elmúló *ephemeral* jellegű, ami azt jelenti, hogy miközben az adatok továbbra is elérhetők az Azure Storage-ban, a *számítási* (Azure Databricks fürt) leállíthatók, így nem kell fizetnie a számításért, ha nincs rá szükség. A *számítási* (Azure Databricks) és a tárolási forrásoknak ugyanabban a régióban kell lenniük, hogy a feladatok ne tapasztalják a nagy késést.  

A saját regionális katasztrófa-helyreállítási topológiájának létrehozásához kövesse az alábbi követelményeket:

   1. Több Azure Databricks munkaterület kiépítése különálló Azure-régiókban. Hozza létre például az elsődleges Azure Databricks munkaterületet a Kelet-RÉGIÓJA. Hozza létre a másodlagos katasztrófa-helyreállítási Azure Databricks munkaterületet egy külön régióban, például az USA nyugati régiójában.

   2. [Geo-redundáns tárolás](../storage/common/storage-redundancy.md)használata. A rendszer alapértelmezés szerint az Azure Storage-ban tárolja az Azure Databricks társított adatmennyiséget. Az Databricks-feladatok eredményeit az Azure Blob Storage is tárolja, így a feldolgozott adatok tartósak maradnak, és a fürt leállítása után is nagyon elérhetők maradnak. Mivel a Storage és a Databricks fürt közös elhelyezésű, a földrajzilag redundáns tárolást kell használnia, hogy az adatelérést a másodlagos régióban lehessen elérni, ha az elsődleges régió már nem érhető el.

   3. A másodlagos régió létrehozása után át kell telepítenie a felhasználókat, a felhasználói mappákat, a jegyzetfüzeteket, a fürtkonfiguráció, a feladatok konfigurációját, a kódtárakat, a tárolót, az inicializálási parancsfájlokat és a hozzáférés-vezérlés újrakonfigurálását. A következő szakaszban további részleteket is ismertetünk.

## <a name="detailed-migration-steps"></a>Részletes migrálási lépések

1. **A Databricks parancssori felület beállítása a számítógépen**

   Ez a cikk számos olyan kódrészletet mutat be, amely a parancssori felületet használja az automatikus lépések többségéhez, mivel ez egy könnyen használható burkoló a Azure Databricks REST API.

   Az áttelepítési lépések elvégzése előtt telepítse a databricks-CLI-t az asztali számítógépére vagy egy olyan virtuális gépre, ahol a munkát tervezi. További információ: [install DATABRICKS CLI](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > A cikkben megadott Python-szkriptek a Python 2.7 + < 3. x használatával működnek.

2. **Két profil konfigurálása.**

   Konfigurálja az egyiket az elsődleges munkaterülethez, és egy másikat a másodlagos munkaterülethez:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   Az ebben a cikkben szereplő kódrészletek az egyes későbbi lépésekben a megfelelő munkaterület parancs használatával válthatnak a profilok között. Ügyeljen arra, hogy az Ön által létrehozott profilok nevei minden kódrészletbe legyenek helyettesítve.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Szükség esetén manuálisan is átválthat a parancssorba:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Azure Active Directory-felhasználók migrálása**

   Manuálisan adja hozzá ugyanazt a Azure Active Directory felhasználót az elsődleges munkaterületen található másodlagos munkaterülethez.

4. **A felhasználói mappák és jegyzetfüzetek migrálása**

   A következő Python-kód használatával áttelepítheti a munkaterületet tartalmazó felhasználói környezeteket, amelyek tartalmazzák a beágyazott mappák struktúráját és a jegyzetfüzeteket felhasználónként.

   > [!NOTE]
   > Ez a lépés nem másolja át a kódtárakat, mivel a mögöttes API nem támogatja ezeket.

   Másolja és mentse a következő Python-szkriptet egy fájlba, és futtassa azt a Databricks parancssorában. Például: `python scriptname.py`.

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

5. **A fürt konfigurációjának migrálása**

   A jegyzetfüzetek migrálása után igény szerint áttelepítheti a fürt konfigurációit az új munkaterületre. Ez majdnem egy teljesen automatizált lépés a databricks-CLI használatával, ha nem az összes számára szeretné elvégezni a szelektív fürtkonfiguráció-konfiguráció áttelepítését.

   > [!NOTE]
   > Sajnos a fürt konfigurációs végpontja nem hozható létre, és ez a szkript azonnal megpróbál létrehozni minden fürtöt. Ha nincs elegendő mag az előfizetésben, a fürt létrehozása sikertelen lehet. A hiba figyelmen kívül hagyható, amíg a konfiguráció sikeresen át lett adva.

   A következő parancsfájl a régiről az új fürt-azonosítóra való leképezést jeleníti meg, amelyet később felhasználhat a feladat áttelepítésére (a meglévő fürtök használatára konfigurált feladatok esetében).

   Másolja és mentse a következő Python-szkriptet egy fájlba, és futtassa azt a Databricks parancssorában. Például: `python scriptname.py`.

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

6. **A feladatok konfigurációjának migrálása**

   Ha az előző lépésben áttelepítette a fürt konfigurációit, a feladatok konfigurációit az új munkaterületre is áttelepítheti. Ez egy teljesen automatizált lépés a databricks-CLI használatával, ha nem az összes feladathoz, hanem szelektív feladat-konfiguráció áttelepítését szeretné elvégezni.

   > [!NOTE]
   > Egy ütemezett feladathoz tartozó konfiguráció az "ütemezés" információkat is tartalmazza, így alapértelmezés szerint a rendszer az áttelepítést követően a beállított időzítéssel kezdi meg a munkát. Ezért az alábbi kódrészlet eltávolítja az összes ütemezett információt az áttelepítés során (így elkerülhető, hogy a duplikált elemek a régi és az új munkaterületeken fussanak). Konfigurálja az ilyen feladatokhoz tartozó ütemterveket, ha készen áll a átváltás.

   A feladatok konfigurációjának új vagy meglévő fürt beállításait kell megadnia. Ha meglévő fürtöt használ, az alábbi szkript ás kód megkísérli a régi fürt AZONOSÍTÓjának cseréjét új fürt-AZONOSÍTÓval.

   Másolja és mentse a következő Python-szkriptet egy fájlba. Cserélje le a `old_cluster_id` és `new_cluster_id`a értékét a fürt áttelepítésének kimenetére az előző lépésben. Futtassa a parancsot a databricks parancssori felületen, például: `python scriptname.py`.

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

7. **Tárak migrálása**

   Jelenleg nincs egyszerű mód arra, hogy az egyik munkaterületről a másikra telepítse át a kódtárakat. Ehelyett manuálisan telepítse újra ezeket a kódtárakat az új munkaterületre. A [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) kombinációjának használatával automatizálható az egyéni kódtárak feltöltése a munkaterületre és a [kódtárak parancssori](https://github.com/databricks/databricks-cli#libraries-cli)felületére.

8. **Azure Blob Storage és Azure Data Lake Storage csatlakoztatások migrálása**

   Manuálisan csatlakoztassa az összes [Azure Blob Storage-tárolót](/azure/databricks/data/data-sources/azure/azure-storage) , és [Azure Data Lake Storage (2. generációs)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) csatlakoztatási pontokat egy jegyzetfüzet-alapú megoldás használatával. A tárolási erőforrások az elsődleges munkaterülethez lettek csatlakoztatva, és ezt meg kell ismételni a másodlagos munkaterületen. Nincs külső API a csatlakoztatásokhoz.

9. **A fürt inicializálási parancsfájljainak migrálása**

   A fürt inicializálási parancsfájljai a [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples)használatával a régiről az új munkaterületre is áttelepíthetők. Először másolja a szükséges parancsfájlokat `dbfs:/dat abricks/init/..` a helyi asztali vagy virtuális gépre. Ezután másolja ezeket a parancsfájlokat az új munkaterületre ugyanazon az elérési úton.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Konfigurálja manuálisan a hozzáférés-vezérlést, és alkalmazza újra.**

    Ha a meglévő elsődleges munkaterülete a prémium szint (SKU) használatára van konfigurálva, akkor valószínű, hogy a [Access Control funkciót](/azure/databricks/administration-guide/access-control/index)is használja.

    Ha a Access Control funkciót használja, manuálisan alkalmazza újra a hozzáférés-vezérlést az erőforrásokra (jegyzetfüzetek, fürtök, feladatok, táblák).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Vész-helyreállítás az Azure-ökoszisztéma esetében

Ha más Azure-szolgáltatásokat használ, mindenképpen hajtsa végre a vész-helyreállítási ajánlott eljárásokat ezen szolgáltatások esetében is. Ha például külső Hive-metaadattár-példányt szeretne használni, érdemes megfontolnia a vész-helyreállítást az [azure SQL Server](../sql-database/sql-database-disaster-recovery.md), az [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)és/vagy a [Azure Database for MySQL](../mysql/concepts-business-continuity.md). A vész-helyreállítással kapcsolatos általános információkért lásd: vész- [helyreállítás Azure-alkalmazásokhoz](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>További lépések

További információ: [Azure Databricks dokumentáció](index.yml).