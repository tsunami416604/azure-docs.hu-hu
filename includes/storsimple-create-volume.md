<!--author=SharS last changed: 11/16/15-->

#### 建立磁碟區

1. 在裝置上 **快速入門** 頁面上，按一下 **新增磁碟區**。 這樣會啟動 [新增磁碟區] 精靈。

2. 在 [新增磁碟區精靈] 中，在 **基本設定**, ，執行下列動作 ︰
   1. 提供 **名稱** 磁碟區。
   2. 指定 **佈建的容量** GB 或 TB 磁碟區。 實體裝置的磁碟區容量必須介於 1 GB 到 64 TB 之間。
   3. 在下拉式清單中，選取 **使用類型** 磁碟區。 
   4. 如果您使用此磁碟區的封存資料，選取 **用於較不常存取的封存資料的這個磁碟區** 核取方塊。 針對所有其他使用案例，只需選取 **層的磁碟區**。 (階層式磁碟區之前稱為主要磁碟區)。
   4. 按一下箭號圖示 ![arrow-icon](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) 若要移至下一個頁面。

        ![新增磁碟區](./media/storsimple-create-volume/AddVolume1-include.png)

3. 在 **其他設定** 對話方塊的 [加入新的存取控制記錄 (ACR):
   1. 提供 **名稱** acr。
   2. 在 **iSCSI 啟動器名稱**, ，提供 iSCSI 合格名稱 (IQN) 的 Windows 主機。 如果您沒有 IQN，請移至 [取得 Windows Server 主機的 IQN](#get-the-iqn-of-a-windows-server-host)。
   3. 我們建議您選取 [啟用預設備份 **啟用此磁碟區的預設備份** ] 核取方塊。 預設備份將會建立原則，在每天的 22:30 (裝置時間) 執行，並建立此磁碟區的雲端快照。

        > [AZURE.NOTE] 備份在此處啟用後，即無法回復。 您必須編輯磁碟區，才能修改此設定。

        ![新增磁碟區](./media/storsimple-create-volume/AddVolume2-include.png)

4. 按一下核取圖示 ![核取圖示](./media/storsimple-create-volume/HCS_CheckIcon-include.png). 使用指定的設定來建立磁碟區。

![可用的視訊](./media/storsimple-create-volume/Video_icon.png) **可用的視訊**

若要看影片，示範如何建立 StorSimple 磁碟區，按一下 [ [這裡](http://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/)。



