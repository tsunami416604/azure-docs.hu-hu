遵循下列步驟，在執行 Windows Server 的虛擬機器上安裝並執行 MongoDB。

> [AZURE.IMPORTANT] MongoDB 安全性功能，例如驗證和 IP 位址繫結，不會預設啟用。 安全性功能應該在將 MongoDB 部署到生產環境前加以啟用。  請參閱 [安全性和驗證](http://www.mongodb.org/display/DOCS/Security+and+Authentication) 如需詳細資訊。

1. 您已連接至虛擬機器使用遠端桌面之後，請開啟 Internet Explorer 從 **開始** 虛擬機器上的功能表。

2. 選取 **工具** 右上角的按鈕。  中 **網際網路選項**, ，請選取 **安全性** 索引標籤，然後選取 **受信任的網站** 圖示，最後按一下 **站台** ] 按鈕。 新增 _http://\*.mongodb.org_ 至受信任的網站清單。

3. 移至 [下載-MongoDB] [MongoDownloads]。

4. 尋找 **目前的穩定版本**, ，選取最新 **64 位元** 版本中的 Windows，下載並執行 MSI 安裝程式。

5. MongoDB 通常會安裝在 C:\Program Files\MongoDB。 在桌面上搜尋環境變數，然後將 MongoDB 二進位檔路徑新增至 PATH 變數。 例如，您可能會在電腦的 C:\Program Files\MongoDB\Server\3.0\bin 發現二進位檔。

6. 資料磁碟中建立 MongoDB 資料和記錄目錄 (磁碟機 **f:**, ，例如) 您在上述步驟中建立。 從 **開始**, ，請選取 **命令提示字元** 開啟 [命令提示字元] 視窗。  輸入：

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs

7. 若要執行資料庫，執行：

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    所有記錄訊息會被都導向至 *F:\MongoLogs\mongolog.log* mongod.exe 伺服器啟動和預先配置日誌檔案時的檔案。 MongoDB 可能需要花費數分鐘來預先配置日誌檔案，並開始接聽連線。

8. 若要啟動 MongoDB 管理殼層，請開啟 [從另一個命令視窗 **啟動** 並輸入下列命令:

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    資料庫由插入項目建立。

9. 或者，您可以利用服務形式安裝 mongod.exe：

        C:\mongodb\bin>mongod --logpath F:\MongoLogs\mongolog.log --logappend --dbpath F:\MongoData\ --install

    這樣會建立一個名為 Mongo DB 的服務，其說明為 "Mongo DB"。  **--Logpath** 選項必須用來指定記錄檔，因為執行中的服務沒有命令視窗可以顯示輸出。   **-Logappend** 選項指出重新啟動服務會導致輸出附加至現有的記錄檔。   **--Dbpath** 選項指出資料目錄的位置。 如需更多服務相關的命令列選項，請參閱 [服務相關的命令列選項] [MongoWindowsSvcOptions]。

    若要啟動服務，請執行此命令：

        C:\mongodb\bin>net start MongoDB

10. 現在，MongoDB 已安裝並正在執行，您必須在 Windows 防火牆中開啟一個連接埠，才能遠端連線至 MongoDB。  從 **開始** 功能表上，選取 **系統管理工具** 然後 **具有進階安全性的 Windows 防火牆**。

11. 在左窗格中，選取 **輸入規則**。  在 **動作** 窗格上，選取 **新增規則...**。

    ![Windows 防火牆][Image1]

    在 **新增輸入規則精靈**, ，請選取 **連接埠** 然後按一下 [ **下一步**。

    ![Windows 防火牆][Image2]

    選取 **TCP** 然後 **特定本機連接埠**。  指定連接埠為"27017"(MongoDB 接聽的預設連接埠)，然後按一下 **下一步**。

    ![Windows 防火牆][Image3]

    選取 **允許連線** 按一下 **下一步**。

    ![Windows 防火牆][Image4]

    按一下 [ **下一步** 一次。

    ![Windows 防火牆][Image5]

    指定規則名稱，例如"MongoPort"，然後按一下 [ **完成**。

    ![Windows 防火牆][Image6]

12. 如果您在建立虛擬機器時沒有設定 MongoDB 的端點，您現在可以進行此動作。 您需要防火牆規則和端點兩者，才能遠端連線至 MongoDB。 在管理入口網站中，按一下 [ **虛擬機器**, ，按一下新的虛擬機器的名稱，然後按一下 **端點**。

    ![端點][Image7]

13. 按一下 [ **新增** 頁面的底部。 選取 **新增獨立端點** 按一下 **下一步**。

    ![端點][Image8]

14. 新增名為"Mongo"，通訊協定的端點 **TCP**, ，且 **公用** 和 **私人** 連接埠均設為"27017"。 這樣可允許 MongoDB 遠端存取。

    ![端點][Image9]

> [AZURE.NOTE] 連接埠 27017 是 MongoDB 使用的預設連接埠。 您可以變更此值由 _-連接埠_ 子命令啟動 mongod.exe 伺服器時。 請務必為上述指示中的防火牆和 "Mongo" 端點指定相同的連接埠號碼。


[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png

