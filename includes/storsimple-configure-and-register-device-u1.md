<!--author=alkohli last changed: 12/01/15-->


### 設定和註冊裝置

1. 存取 StorSimple 裝置序列主控台上的 Windows PowerShell 介面。 請參閱 [使用 PuTTY 來連接至裝置序列主控台](#use-putty-to-connect-to-the-device-serial-console) 如需相關指示。 **請務必確實依照此程序，否則將無法存取主控台。**

2. 在開啟的工作階段中，按 Enter 鍵一次以取得命令提示字元。 

3. 系統將提示您選擇想要為裝置設定的語言。 指定語言，然後按 Enter 鍵。 

    ![StorSimple 設定和註冊裝置 1](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice1-U1-include.png)

4. 在顯示的序列主控台功能表中，選擇選項 1 以使用完整存取權進行登入。 

    ![StorSimple 註冊裝置 2](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice2_U1-include.png)
  
     完成步驟 5-12，為裝置設定最小的必要網路設定。 **這些設定步驟必須在裝置的主動控制器上執行。** 序列主控台功能表會在橫幅訊息中指出控制站狀態。 如果您未連接到主動控制器，請中斷連線，然後連接到主動控制器。

5. 在命令提示字元中，輸入您的密碼。 預設裝置密碼是 **Password1**。

6. 輸入以下命令：`Invoke-HcsSetupWizard`。 

7. 安裝精靈將協助您設定裝置的網路設定。 請提供下列資訊： 
   - 適用於 DATA 0 網路介面的 IP 位址
   - 子網路遮罩
   - 閘道器
   - 適用於主要 DNS 伺服器的 IP 位址
    
        請注意，系統在此過程中會於完成每個步驟後便檢查網路設定一次。
   
      > [AZURE.NOTE] 您可能要等候幾分鐘的子網路遮罩和 DNS 設定要套用。 如果您收到「檢查 Data 0 的網路連線」錯誤訊息，請檢查主動控制器之 DATA 0 網路介面上的實體網路連線。

8. (選用) 設定 Web Proxy 伺服器。 雖然 web proxy 設定是選擇性的 **請注意，如果您使用 web proxy，您可以只設定它這裡**。 如需詳細資訊，請移至 [設定裝置的 web proxy](../articles/storsimple/storsimple-configure-web-proxy.md)。

9. 為裝置設定主要 NTP 伺服器。 NTP 伺服器是必要項目，因為您的裝置必須同步處理時間，才能使用您的雲端服務提供者進行驗證。 請確定您的網路允許 NTP 流量從您的資料中心通過網際網路。 如果不可行，請指定內部 NTP 伺服器。 
 
10. 基於安全性理由，裝置系統管理員密碼會在第一個工作階段之後過期，您必須立即變更。 出現提示時，請提供裝置系統管理員密碼。 有效的裝置系統管理員密碼長度必須介於 8 到 15 個字元。 密碼必須包含下列其中三項：大寫、小寫、數字和特殊字元。

    <br/>![StorSimple 註冊裝置 5](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice5_U1-include.png)

11. 安裝精靈的最後一個步驟是向 StorSimple Manager 服務註冊您的裝置。 基於此因素，您需要使用在步驟 2 中取得的服務註冊金鑰。 提供註冊金鑰之後，您可能需要等待 2-3 分鐘，才能註冊裝置。

      > [AZURE.NOTE] 您可以按 Ctrl + C 以結束安裝精靈的任何時候。 如果您輸入所有網路設定 (Data 0 的 IP 位址、子網路遮罩和閘道器)，則會保留您的項目。

    ![StorSimple 註冊裝置 6](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice6_U1-include.png)

12. 註冊裝置之後，隨即會出現服務資料加密金鑰。 複製這個金鑰，並將它儲存在安全的位置。 **這個金鑰需要與服務註冊金鑰搭配使用，來向 StorSimple Manager 服務註冊其他裝置。** 請參閱 [StorSimple 安全性](../articles/storsimple/storsimple-security.md) 如需有關這個機碼。
    
    ![StorSimple 註冊裝置 7](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice7_U1-include.png)    

      > [AZURE.NOTE] 若要從序列主控台視窗複製文字，只需選取的文字。 然後您應該能夠將它貼到剪貼簿或任何文字編輯器中。 請勿使用 Ctrl + C 來複製服務資料加密金鑰。 使用 Ctrl + C 將導致安裝精靈結束。 如此一來，裝置系統管理員密碼將不會變更，而裝置將還原為預設密碼。

13. 結束序列主控台。

14. 返回 Azure 傳統入口網站，並完成下列步驟：
  1. 按兩下 StorSimple Manager 服務以存取 **快速入門** 頁面。
  2. 按一下 [ **檢視連接的裝置**。
  3. 在 **裝置** 頁面上，確認裝置已成功連接至服務藉由查閱狀態。 裝置狀態應該是 **線上**。
   
        ![StorSimple 裝置頁面](./media/storsimple-configure-and-register-device-u1/HCS_DevicesPageM_U1-include.png) 
  
        如果裝置狀態是 **離線**, ，等待數分鐘讓裝置上線。 
      
        如果裝置依然為離線狀態後幾分鐘的時間，則您必須先確認您的防火牆網路已設定中所述 [StorSimple 裝置的網路需求](../articles/storsimple/storsimple-system-requirements.md)。 

        如果您的裝置不支援 HTTP 1.1，請檢查連接埠 9354 以確定此連接埠可開放做為傳出通訊之用。 此連接埠用於 StorSimple Manager 服務及 StorSimple 裝置間的通訊。
     
       

