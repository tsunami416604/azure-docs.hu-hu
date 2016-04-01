<properties 
   pageTitle="StorSimple Snapshot Manager 備份工作 | Microsoft Azure"
   description="描述如何使用 StorSimple Snapshot Manager MMC 嵌入式管理單元來檢視和管理已排程、目前執行中和已完成的備份作業。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/15/2015"
   ms.author="v-sharos" />


# 使用 StorSimple Snapshot Manager 來檢視和管理備份作業

## 概觀

 **工作** 節點 **範圍** 窗格顯示 **排程**, ，**過去 24 小時**, ，和 **執行** 備份以互動方式或依據設定的原則，您起始的工作。 

本教學課程說明如何使用 **工作** 節點以顯示已排程、 最近和目前執行的備份作業的相關資訊。 (工作及對應的資訊清單會出現在 **結果** 窗格。)此外，您也可以以滑鼠右鍵按一下列出的作業，並查看內容功能表，其中列出可用的動作。

## 檢視已排程的作業

請使用下列程序來檢視已排程的備份作業。

#### 若要檢視已排程的作業

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。 

2. 在 **範圍** ] 窗格中，展開 **工作** 節點，然後按一下 [ **排程**。 下列資訊會出現在 **結果** 窗格 ︰

    - **名稱** – 已排定之快照的名稱

    - **接下來，執行** – 下次排定之快照的時間與日期

    - **上次執行** – 最近排定之快照的時間與日期

    >[AZURE.NOTE] 僅一次快照， **下次執行** 和 **上次執行** 都是一樣。 
 
    ![排定的備份工作](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. 在特定工作上執行其他動作，請以滑鼠右鍵按一下中的作業名稱 **結果** 窗格，然後選取功能表選項。

## 檢視最近的作業

請使用下列程序，來檢視過去 24 小時內完成的備份和還原作業。

#### 若要檢視最近的作業

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 **範圍** ] 窗格中，展開 **工作** 節點，然後按一下 [ **過去 24 小時**。  **結果** ] 窗格會顯示過去 24 小時 （最多 64 個工作） 的備份工作。 下列資訊會出現在 **結果** ] 窗格中，視 **檢視** 您指定的選項 ︰

    - **名稱** – 已排定之快照的名稱。
 
    - **啟動** – 快照開始的時間與日期。

    - **停止** – 快照完成或終止的時間與日期。

    - **經過** – 之間的時間量 **已啟動** 和 **已停止** 時間。

    - **狀態** – 最近完成之工作的狀態。 **成功** 指出備份已順利建立。 **無法** 表示作業無法順利執行。

    - **資訊** – 失敗的原因。

    - **位元組數 (MB)** – 來自磁碟區群組 （以 mb 為單位） 已處理的資料量。 

    ![過去 24 小時內執行的工作](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. 在特定工作上執行其他動作，請以滑鼠右鍵按一下中的作業名稱 **結果** 窗格，然後選取功能表選項。

    ![刪除工作](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## 檢視目前執行中的作業

請使用下列程序來檢視目前執行中的作業。

#### 若要檢視目前執行中的工作

1. 按一下桌面圖示，以啟動 StorSimple Snapshot Manager。

2. 在 **範圍** ] 窗格中，展開 **工作** 節點，然後按一下 [ **執行**。 取決於 **檢視** 選項指定，下列資訊會出現在 **結果** 窗格 ︰ 

    - **名稱** – 已排定之快照的名稱。

    - **啟動** – 快照開始的時間與日期。

    - **檢查點** – 備份的目前動作。

    - **狀態** – 完成百分比。
    
    - **經過** – 從備份開始後所經過的時間量。 

    - **平均輸送量 (MB)** – 中間值的資料量傳送，以 mb 表示。

    - **位元組數 (MB)** – 來自磁碟區群組 （以 mb 為單位） 已處理的資料量。

    - **寫入的位元組 (MB)** – 已寫入至備份 （以 mb 為單位） 的資料量。

    ![目前執行中的工作](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. 在特定工作上執行其他動作，請以滑鼠右鍵按一下中的作業名稱 **結果** 窗格，然後選取功能表選項。

## 後續步驟

- 了解如何 [使用 StorSimple Snapshot Manager 來管理您的 StorSimple 解決方案](storsimple-snapshot-manager-admin.md)。
- 了解如何 [使用 StorSimple Snapshot Manager 來管理備份目錄](storsimple-snapshot-manager-manage-backup-catalog.md)。















            


 



