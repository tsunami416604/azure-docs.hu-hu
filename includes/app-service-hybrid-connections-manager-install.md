
1. 在 **混合式連線** 刀鋒視窗中，按一下 [混合式連線，您剛建立的然後按一下 [ **接聽程式設定**。
    
    ![Click Listener Setup](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
    
4.  **混合式連線屬性** 分頁隨即開啟。 在 **內部部署混合式連線管理員**, ，選擇 [ **手動下載及設定**, ，並儲存下載的 HybridConnectionManager.msi 封裝，複製的閘道連接字串。
    
    ![Click here to install](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
    
5. 從系統管理員命令提示字元中，輸入下列命令以啟動安裝程式：

        start HybridConnectionManager.msi
 
7. 執行安裝程式之後，請按一下 **不是現在**, 然後瀏覽至 %ProgramFiles%\Microsoft\HybridConnectionManager 資料夾，並執行 HCMConfigWizard.exe，按一下 **是** 中 **使用者帳戶控制** ] 對話方塊。
        
7. 貼上您先前複製的混合式連接字串，然後按一下 **確定**。 
    
    ![安裝](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
    
8. 安裝完成後，按一下 [ **關閉**。
    
    ![Click Close](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
    
    在 **混合式連線** 刀鋒視窗中， **狀態** 欄此時會顯示 **連線**。 
    
    ![Connected Status](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)
