>[AZURE.NOTE] 變更 StorSimple Adapter for SharePoint 的 RBS 組態時，您必須利用屬於 Domain Admins 群組的使用者帳戶登入。 此外，您必須從瀏覽器 (在和管理中心相同的主機上執行) 存取組態頁面。

#### 設定 RBS

1. 開啟 [SharePoint 管理中心] 頁面，並瀏覽至 [系統設定]****。

2. 在 [Azure StorSimple]**** 區段中，按一下 [設定 StorSimple Adapter]****。

    ![設定 StorSimple Adapter](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png)

3. 在 [設定 StorSimple Adapter]**** 頁面上：

 1. 請確定已選取 [啟用編輯路徑]**** 核取方塊。

 2. 在文字方塊中，輸入 BLOB 存放區的通用命名慣例 (UNC) 路徑。

       >[AZURE.NOTE] The BLOB store volume must be hosted on an iSCSI volume configured on the StorSimple device.

 3. 按一下每個您想要設定遠端存放的內容資料庫下方的 [啟用]**** 按鈕。

    >[AZURE.NOTE] The BLOB store must be shared and reachable by all web front-end (WFE) servers, and the user account that is configured for the SharePoint server farm must have access to the share.
    
    ![Enable the RBS provider](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
    
     When you enable or disable RBS, you will also see the following message.
    
    ![Configure StorSimple Adapter Enable Disable](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

 4. 按一下 [更新]**** 按鈕以套用組態。 當您按一下 [更新]**** 按鈕時，所有 WFE 伺服器上的 RBS 組態狀態將會更新，且整個伺服器陣列將會啟用 RBS 功能。 下列訊息隨即出現。

     ![Adapter configuration message](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
    
     >[AZURE.NOTE] If you are configuring RBS for a SharePoint farm with a very large number of databases (greater than 200), the SharePoint Central Administration web page might time out. If that occurs, refresh the page. This does not affect the configuration process.


4. 驗證組態：

    1. 登入 SharePoint 管理中心網站，並瀏覽至 [設定 StorSimple Adapter]**** 頁面。

    2. 請檢查組態詳細資料以確定它們符合您所輸入的設定。

5. 確認 RBS 正確地運作：

    1. 將文件上傳至 SharePoint。

    2. 瀏覽至您所設定的 UNC 路徑。 請確定已建立 RBS 目錄結構，且它包含已上傳的物件。

6. (選擇性)您可以使用 Microsoft RBS `Migrate()` SharePoint 移轉現有的 BLOB 內容至 StorSimple 裝置隨附的 PowerShell 指令程式。 如需詳細資訊，請參閱 [送入或送出 RBS SharePoint 2013 ][6] 或 [送入或送出 RBS (SharePoint Foundation 2010) ][7]。

7. (選擇性) 在測試安裝上，您可以確認已將 Blob 移出內容資料庫，如下所示：

    1. 啟動 SQL Management Studio

    2. 執行 ListBlobsInDB_2010.sql 或 ListBlobsInDB_2013.sql 查詢，如下所示。

     **ListBlobsInDB_2013.sql**

         使用 WSS_Content
         GO

         選取 DocStreams.DocId，
                LeafName AS 名稱
                內容，
                AllDocs.Size AS OrigSizeOfContent
                LEN (轉型 (內容 AS VARBINARY(MAX))) AS SizeOfContentInDB，
                DocStreams.RbsId，
                TimeLastModified

         從 DocStreams
              內部聯結 AllDocs ON DocStreams.DocId = AllDocs.Id
         ORDER BY TimeLastModified DESC
         GO

     **ListBlobsInDB_2010.sql**

         使用 WSS_Content
         GO

         選取 AllDocStreams.Id，
                LeafName AS 名稱
                內容，
                AllDocs.Size AS OrigSizeOfContent
                LEN (轉型 (內容 AS VARBINARY(MAX))) AS SizeOfContentInDB，
                RbsId，
                TimeLastModified
         從 AllDocStreams
              內部聯結 AllDocs ON AllDocStreams.Id = AllDocs.Id
         ORDER BY TimeLastModified DESC
         GO

     如果已正確設定 RBS，NULL 值應該會出現在已上傳並使用 RBS 順利外部化之任何物件的 SizeOfContentInDB 資料行。

8. (選擇性) 設定 RBS 並將所有 BLOB 內容都移至 StorSimple 裝置之後，您可以將內容資料庫移至裝置。 如果您選擇移動內容資料庫，建議您在裝置上將內容資料庫儲存體設定為主要磁碟區。 然後使用以建立的 SQL Server 最佳作法，將內容資料庫移轉至 StorSimple 裝置。
     >[AZURE.NOTE] 只有 StorSimple 8000 系列支援將內容資料庫移至裝置 (5000 或 7000 系列並不支援)。

     如果您在 StorSimple 裝置上的個別磁碟區中儲存 BLOB 和內容資料庫，建議您在相同的磁碟區容器中設定它們。 這樣可確保它們將一起進行備份。

       >[AZURE.WARNING] If you have not enabled RBS, we do not recommend moving the content database to the StorSimple device. This is an untested configuration.

9. 移至下一個步驟: [設定記憶體回收](#configure-garbage-collection)。


[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx 
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx 

