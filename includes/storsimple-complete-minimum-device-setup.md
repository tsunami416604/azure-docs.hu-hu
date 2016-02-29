<!--author=alkohli last changed: 9/17/15-->

#### 完成最小的 StorSimple 裝置安裝

1. 在 **裝置** 頁面上，選取裝置，按一下裝置名稱，以移至特定的裝置] 頁面的箭頭。 

    ![裝置上線時的裝置頁面](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 

2. 按一下 [快速啟動圖示 ![快速啟動圖示](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) 存取裝置的快速入門] 頁面。 按一下 [ **完成裝置設定** 啟動 **設定裝置** 精靈。

    ![StorSimple 快速啟動頁面](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)

2. 在 **基本設定** 頁面上，執行下列動作:
  1. 提供 **易記名稱** 為您的裝置。 預設的裝置名稱會反映裝置型號及序號等資訊。 您最多可為易記名稱指定 64 個字元來管理您的裝置。
  2. 設定 **時區** 根據部署裝置的地理位置。 裝置將針對所有排程的操作使用這個時區。
  3. 在 **DNS 設定**, ，提供的地址您 **次要 DNS 伺服器**。 如果您使用的是 IPv6，將根據 Windows PowerShell 介面中提供的 IPv6 首碼來填入欄位。 
  如果未設定次要 DNS 伺服器，您將無法儲存裝置設定。
  4. 在啟用 iSCSI 的介面下方，至少啟用一個適用於 iSCSI 的網路。 至少有一個網路介面必須具備雲端功能，而且有一個介面必須是已啟用 iSCSI 的介面。 DATA 0 會自動具備雲端功能。
 
      ![StorSimple 最小裝置設定基本設定](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)

3. 按一下箭頭圖示。 ![StorSimple 箭頭圖示](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4. 在 **網路介面** 頁面上，提供控制器 0 及控制器 1 固定的 IP 位址。 如果 DATA 0 介面是針對 IPv4 所設定，就必須以 IPv4 格式提供固定的 IP 位址。 如果您提供適用於 IPv6 設定的首碼，則固定的 IP 位址將自動填入這些欄位中。


    > [AZURE.NOTE] 
    > 
    > - The controller fixed IP addresses need to be free IPs within the subnet accessible by the device IP address.
    > - The fixed IP addresses for the controller are used for servicing the updates to the device, and therefore the fixed IPs must be routable and able to connect to the Internet.

    ![StorSimple minimum device setup network interfaces](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

5. 按一下核取圖示 ![StorSimple 核取圖示](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png)。
  您將會回到裝置 **快速入門** 頁面。

 > [AZURE.NOTE] 您可以隨時修改所有其他裝置設定，藉由存取 **設定** 頁面。

![可用的視訊](./media/storsimple-complete-minimum-device-setup/Video_icon.png) **可用的視訊**

若要看影片，示範如何完成最小量裝置設定，按一下 [ [這裡](http://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/)。
