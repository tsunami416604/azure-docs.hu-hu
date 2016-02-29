<!--author=SharS last changed: 9/17/15-->

#### 透過 Windows PowerShell for StorSimple 安裝一般 Hotfix

1. 連接到裝置序列主控台。 如需詳細資訊，請參閱 [步驟 1: 連接至序列主控台](storsimple-update-device.md#step1)。

2. 在序列主控台功能表中，選取選項 1 **完整存取權登入**。 輸入密碼。 預設密碼是 **Password1**。

3. 在命令提示字元中，輸入：

    `Start-HcsHotfix`

       >[AZURE.IMPORTANT]
       >
       >- This command applies only to regular hotfixes. You run this command on only one controller, but both controllers will be updated.
       >- You may notice a controller failover during the update process; however, the failover will not affect system availability or operation.

4. 出現提示時，請提供包含 Hotfix 檔案的網路共用資料夾所在路徑。

5. 系統將提示您進行確認。 型別 **Y** 繼續安裝 hotfix。

