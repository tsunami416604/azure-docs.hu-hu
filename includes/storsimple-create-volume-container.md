<!--author=SharS last changed: 9/17/15-->

#### 建立磁碟區容器

1. 在裝置 **快速入門** 頁面上，按一下 **新增磁碟區容器**。  **建立磁碟區容器** ] 對話方塊隨即出現。

    ![建立磁碟區容器](./media/storsimple-create-volume-container/HCS_CreateVolumeContainerM-include.png)

2. 在 **建立磁碟區容器** ] 對話方塊中:
  1. 提供 **名稱** 磁碟區容器。 名稱長度必須是 3 到 32 個字元。
  2. 選取 **儲存體帳戶** 與此磁碟區容器相關聯。 您可以選擇服務建立時所產生的預設帳戶。 您也可以使用 **新增** 選項來指定未連結到此服務訂用帳戶的儲存體帳戶。
  3. 選取 **啟用雲端儲存體加密** 啟用從裝置傳送至雲端的資料加密。
  4. 提供並確認 **雲端儲存體加密金鑰** 也就是 8 到 32 個字元。 裝置會使用這個金鑰來存取加密的資料。
  5. 選取 **Unlimited** 中 **指定頻寬** 下拉式清單，如果您想要耗用所有可用的頻寬。 您也可以設定此選項 **自訂** 使用頻寬控制項，並指定介於 1 到 1000 Mbps 之間的值。 
  如果您有可用的頻寬使用量資訊，您可以藉由指定根據排程配置頻寬 **選取頻寬範本**。 如需逐步程序，請移至 [新增頻寬範本](storsimple-manage-bandwidth-templates.md#add-a-bandwidth-template)。
  6. 按一下核取圖示 ![核取圖示](./media/storsimple-create-volume-container/HCS_CheckIcon-include.png) 若要儲存這個磁碟區容器並結束精靈。 

  新建立的磁碟區容器將列於 **磁碟區容器** 頁面。

![可用的視訊](./media/storsimple-create-volume-container/Video_icon.png) **可用的視訊**

若要看影片，示範如何在 StorSimple 方案中建立磁碟區容器，按一下 [ [這裡](http://azure.microsoft.com/documentation/videos/create-a-volume-container-in-your-storsimple-solution/)。
