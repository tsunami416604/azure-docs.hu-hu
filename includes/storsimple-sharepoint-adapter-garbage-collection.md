<!--author=SharS last changed: 9/17/15-->

Ezzel az eljárással tartalma:

1. [Készítse elő a karbantartó végrehajtható fájl futtatásához](#to-prepare-to-run-the-maintainer) .
2. [A tartalom-adatbázist és a Lomtár előkészítése árva Blobok azonnali törlését](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Futtassa a Maintainer.exe](#to-run-the-maintainer).
4. [A tartalom-adatbázist és a beállítások visszaállításához](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>A karbantartó futtatásához
1. Az előtér-webkiszolgálón nyissa meg rendszergazdaként a SharePoint 2013 felügyeleti rendszerhéjat.
2. Lépjen abba a mappába *rendszerindítási meghajtó*: \Program Files\Microsoft SQL távoli Blob Storage 10.50\Maintainer\.
3. Nevezze át **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** való **web.config**.
4. Használjon `aspnet_regiis -pdf connectionStrings` visszafejtése a web.config fájlt.
5. A visszafejtett web.config fájlban az a `connectionStrings` csomópont, az SQL server-példányt és a tartalom-adatbázis neve a kapcsolati karakterlánc hozzáadása. Tekintse meg a következő példát.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Használjon `aspnet_regiis –pef connectionStrings` a web.config fájl újbóli titkosítására. 
7. Nevezze át a web.config Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>A tartalom előkészítése, adatbázis, és azonnal törli a Lomtár árva Blobok
1. Az SQL Server az SQL Management Studio eszközben a célként megadott tartalom-adatbázist a következő frissítés-lekérdezések futtatása: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Az előtér-webkiszolgálón a **központi adminisztrációs**, szerkesztése a **webalkalmazás általános beállítások** számára a kívánt tartalom-adatbázist ideiglenesen letilthatja a Lomtárban van. Ez a művelet fog is Lomtár az esetleges kapcsolódó webhelycsoportok. Ehhez kattintson **központi adminisztrációs** -> **Alkalmazáskezelés** -> **webalkalmazások (kezelése webalkalmazások)**  ->  **SharePoint - 80** -> **általános Alkalmazásbeállítások**. Állítsa be a **Recycle Bin állapot** való **OFF**.
   
    ![Webalkalmazás általános beállításai](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>A karbantartó futtatni
* Az előtér-webkiszolgálón a SharePoint 2013 felügyeleti rendszerhéjat, futtassa a karbantartó az alábbiak szerint:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Csak a `GarbageCollection` művelet StorSimple jelenleg támogatott. Ne feledje, hogy ki Microsoft.Data.SqlRemoteBlobs.Maintainer.exe paraméterei és a nagybetűk között. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>A tartalom-adatbázist és a beállítások visszaállításához
1. Az SQL Server az SQL Management Studio eszközben a célként megadott tartalom-adatbázist a következő frissítés-lekérdezések futtatása:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Az előtér-webkiszolgálón a **központi adminisztrációs**, szerkesztheti a **webalkalmazás általános beállítások** számára kívánja újból engedélyezni a Lomtár a kívánt tartalom-adatbázist. Ehhez kattintson **központi adminisztrációs** -> **Alkalmazáskezelés** -> **webalkalmazások (kezelése webalkalmazások)**  ->  **SharePoint - 80** -> **általános Alkalmazásbeállítások**. A Lomtár Bin állapotra **ON**.

