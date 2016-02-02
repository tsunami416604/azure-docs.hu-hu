
在此程序中，您將會：

1. [準備執行維護程式可執行檔](#to-prepare-to-run-the-maintainer) 。

2. [為立即刪除被遺棄的 Blob 準備內容資料庫和資源回收筒](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)。

3. [執行 Maintainer.exe](#to-run-the-maintainer)。

4. [還原內容資料庫和資源回收筒設定](#to-revert-the-content-database-and-recycle-bin-settings)。

#### 準備執行維護程式

1. 在 Web 前端伺服器上，以系統管理員身分開啟 SharePoint 2013 管理命令介面。

2. 瀏覽至資料夾 <boot drive>: files\microsoft SQL Remote Blob Storage 10.50\Maintainer\。

3. 將 **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** 重新命名為 **web.config**。

4. 使用 `aspnet_regiis-pdf connectionStrings` 解密 web.config 檔案。

5. 在解密的 web.config 檔案中，在 * *<connectionStrings>* *] 節點，加入您的 SQL server 執行個體和內容資料庫名稱的連接字串。請參閱下列範例。

    `< 新增名稱 ="RBSMaintainerConnectionWSSContent"connectionString ="資料來源 = shrpt13-sql12\shrpt13; 初始目錄 = WSS_Content; 整合式安全性 = True;應用程式名稱 = & q u o t;遠端 Blob 儲存維護程式 WSS_Content & q u o t; 「 providerName="System.Data.SqlClient 」 / >`

6. 使用 `aspnet_regiis – pef-ndis-packetcapture connectionStrings` 重新加密 web.config 檔案。

7. 將 web.config 重新命名為 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config。

#### 為立即刪除被遺棄的 BLOB 準備內容資料庫和資源回收筒

1. 在 SQL Server 上的 SQL Management Studio 中，為目標內容資料庫執行下列更新查詢：

    `use WSS_Content`
    
    `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
    
    `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. 在 Web 前端伺服器上的**管理中心**下，為所需的內容資料庫編輯 **Web 應用程式一般設定**以暫時停用資源回收筒。 這個動作將同時清空任何相關網站集合的資源回收筒。 若要這樣做，請按一下 [管理中心]**** -> [應用程式管理]**** -> [Web 應用程式 (管理 Web 應用程式)]**** -> [SharePoint - 80]**** -> [一般應用程式設定]****。 將**資源回收筒狀態**設為 **關閉**。

    ![Web 應用程式一般設定](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### 執行維護程式

- 在 web 前端伺服器上的 SharePoint 2013 管理命令介面中，執行維護程式，如下所示：

      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`

    >[AZURE.NOTE] 只有 `GarbageCollection` StorSimple 支援作業這一次。 此外請注意，針對 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe 所發出的參數會區分大小寫。 

#### 還原內容資料庫和資源回收筒設定

1. 在 SQL Server 上的 SQL Management Studio 中，為目標內容資料庫執行下列更新查詢：

      `使用 WSS_Content`

      `exec mssqlrbs.rbs_sp_set_config_value 'garbage_collection_time_window'、 ' days 30'`

      `exec mssqlrbs.rbs_sp_set_config_value '隔 delete_scan_period'、 ' days 30'`

      `exec mssqlrbs.rbs_sp_set_config_value '隔 orphan_scan_period'、 ' days 30'`

2. 在 Web 前端伺服器上的**管理中心**中，為所需的內容資料庫編輯 **Web 應用程式一般設定**以重新啟用資源回收筒。 若要這樣做，請按一下 [管理中心]**** -> [應用程式管理]**** -> [Web 應用程式 (管理 Web 應用程式)]**** -> [SharePoint - 80]**** -> [一般應用程式設定]****。 將資源回收筒狀態設為 **開啟**。





