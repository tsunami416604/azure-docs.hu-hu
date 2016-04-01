<!--author=alkohli last changed: 9/17/15-->

#### 在 StorSimple 8000 系列 Update 1.0 中新增儲存體帳戶

1. 在 StorSimple Manager 服務登陸頁面上選取您的服務，然後按兩下該服務。 這樣會帶您到 **快速入門** 頁面。 選取 **設定** 頁面。

2. 按一下 [ **新增/編輯儲存體帳戶**。

3. 在 **新增/編輯儲存體帳戶** 對話方塊中，按一下 [ **新增**。

4. 在 **提供者** 欄位中，選取適當的雲端服務提供者。 支援的提供者為 Azure、Amazon S3、具有 RRS 的 Amazon S3、HP 及 OpenStack。 指定與您的雲端服務提供者之儲存體帳戶相關聯的認證和位置。 針對認證顯示的欄位將會不同，取決於您指定的雲端服務提供者。 
  - 如果您已選取 Azure 做為您的雲端服務提供者，提供 **名稱** 和主要 **便捷鍵** 做為 Microsoft Azure 儲存體帳戶。 如果是 Azure 帳戶，會自動填入位置。

        ![Add Azure storage account](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)

 - 如果您選取 Amazon S3 或具有 RRS 的 Amazon S3，提供易記 **儲存體帳戶名稱**, ，**便捷鍵**, ，和 **秘密金鑰**。 如果是 Amazon S3 和具有 RRS 的 Amazon S3，支援下列位置：

        - US Standard
        - US West (Oregon)
        - US West (Northern California)
        - EU (Ireland)
        - Asia Pacific (Singapore)
        - Asia Pacific (Sydney)
        - Asia Pacific (Tokyo)
        - South America (Sao Paulo)

        ![Add Amazon storage account](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
            
 - 如果您選取 HP 做為您的雲端服務提供者，提供易記 **儲存體帳戶名稱**, ，**租用戶識別碼**, ，**Username**, ，和 **密碼**。 如果是 HP，支援下列位置：

        - US East
        - US West
      
        ![Add HP storage account](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
            
 - 如果您已選取 **Openstack** 雲端服務提供者，提供 **Hostname**, ，**便捷鍵**, ，和 **秘密金鑰**。

        > [AZURE.NOTE] 所有雲端服務提供者，不包括 Azure、 都允許的易記名稱。 您可以使用不同的易記名稱，並使用相同認證集合建立一個以上的儲存體帳戶。

        ![Add Openstack storage account](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)

5. 選取 **啟用 SSL 模式** 來建立您的裝置與雲端之間網路通訊的安全通道。 清除 **啟用 SSL 模式** 核取方塊，只有當您在私人雲端內進行操作。

      > [AZURE.NOTE] 如果您使用 HP 做為您提供者，將一律啟用 SSL。
        
6. 按一下核取圖示 ![核取圖示](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png). 成功建立儲存體帳戶之後，系統將會通知您。

7. 新建立的儲存體帳戶將會顯示在 **設定** 頁面下 **儲存體帳戶**。 按一下 [ **儲存** 以儲存新的儲存體帳戶。 按一下 [ **確定** 當系統提示您確認。


