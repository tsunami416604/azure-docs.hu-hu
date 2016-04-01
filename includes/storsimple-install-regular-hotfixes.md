<!--author=SharS last changed: 9/17/15-->

#### 透過 Windows PowerShell for StorSimple 安裝一般 Hotfix

1. 連接到裝置序列主控台。 如需詳細資訊，請參閱 [步驟 1 ︰ 連接至序列主控台](storsimple-update-device.md#step1)。

2. 在序列主控台功能表中，選取選項 1 **完整存取權登入**。 輸入密碼。 預設密碼是 **Password1**。

3. 在命令提示字元中，輸入：

    `Start-HcsHotfix`

       >[AZURE.IMPORTANT]
       >
       >- 此命令僅適用於一般的 hotfix。 您只需在某一個控制站上執行此命令，就會更新這兩個控制站。
       >- 您可能在更新程序期間注意到控制站容錯移轉。不過，容錯移轉並不會影響系統的可用性或運作。

4. 出現提示時，請提供包含 Hotfix 檔案的網路共用資料夾所在路徑。

5. 系統將提示您進行確認。 型別 **Y** 繼續安裝 hotfix。


