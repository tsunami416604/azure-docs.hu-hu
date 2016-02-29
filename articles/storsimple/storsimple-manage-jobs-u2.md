<properties 
   pageTitle="檢視和管理 StorSimple 工作 | Microsoft Azure"
   description="說明 StorSimple Manager 服務工作頁面，以及如何使用該頁面來追蹤最近、當前和排程的備份工作。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/14/2015"
   ms.author="alkohli" />

# 使用 StorSimple Manager 服務來檢視和管理 StorSimple 工作

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## 概觀

 **工作** 頁面提供單一中央入口網站檢視和管理裝置啟動的工作連接到您的 StorSimple Manager 服務。 您可以針對多個裝置，檢視已排程、執行中、完成和失敗的工作。 結果會以表格式格式呈現。 

![[工作] 頁面](./media/storsimple-manage-jobs-u2/HCS_JobsPage.png)

您可以透過篩選欄位，快速找到您所感興趣的工作，例如：

- **狀態** – 工作可以執行中、 已完成、 取消、 失敗、 取消中或已完成但發生錯誤。
- **From 和 To** – 工作可以根據日期和時間範圍篩選。
- **型別** – 工作類型可以是備份、 手動備份、 還原、 複製、 裝置容錯移轉，建立在本機固定磁碟區、 修改磁碟區、 更新、 支援封裝，或佈建虛擬裝置。

- **裝置** – 連線到您的服務的特定裝置上起始工作。
篩選的工作接著會根據下列屬性製成表格：

    - **型別** – 備份、 手動備份、 還原、 複製、 裝置容錯移轉，建立在本機固定磁碟區、 修改磁碟區、 更新、 支援封裝，或佈建虛擬裝置。

    - **狀態** – 執行、 已完成、 取消、 失敗、 取消中或已完成但發生錯誤。

    - **實體** – 工作可以與磁碟區、 備份原則或裝置相關聯。 例如，複製工作與磁碟區相關聯，而排程的備份工作則與備份原則相關聯。 裝置工作是透過災害復原 (DR) 或還原作業而建立。

    - **裝置** – 啟動工作的裝置名稱。

    - **在啟動** – 啟動工作的時間。

    - **進度** – 執行中工作的完成百分比。 對於完成的工作，百分比應該永遠是 100%。

工作清單每隔 30 秒會重新整理。

您可以在此頁面上執行下列工作相關的動作：

- 檢視工作詳細資料

- 取消工作

## 檢視工作詳細資料

執行下列步驟來檢視任何工作的詳細資料。

#### 若要檢視工作詳細資料

1. 在 **工作** 頁面上，顯示您所感興趣執行查詢搭配適當的篩選器的工作。 您可以搜尋完成、執行中或已取消工作。

2. 選取一個工作。

3. 在頁面底部，按一下 [ **詳細資料**。

4. 在 **備份工作詳細資料** 對話方塊中，您可以檢視狀態、 詳細資料、 時間統計資料和資料統計資料。
 
    ![工作詳細資料頁面](./media/storsimple-manage-jobs-u2/JobDetails.png)

## 取消工作

執行下列步驟來取消執行中工作。

>[AZURE.NOTE] 無法取消一些工作，例如修改磁碟區變更的磁碟區類型，或展開磁碟區。

### 取消工作

1. 在 **工作** 頁面上，顯示您想要用來取消執行查詢搭配適當的篩選器的執行工作。

1. 選取工作。

1. 在頁面底部，按一下 [ **取消**。

1. 當系統提示您確認，按一下 [ **是**。

即會取消此工作。

## 後續步驟

- 了解如何 [管理 StorSimple 備份原則](storsimple-manage-backup-policies.md)。

- 了解如何 [使用 StorSimple Manager 服務來管理 StorSimple 裝置](storsimple-manager-service-administration.md)。
