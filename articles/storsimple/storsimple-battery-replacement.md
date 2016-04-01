<properties 
   pageTitle="更換 StorSimple 裝置上的電池 | Microsoft Azure"
   description="描述如何取下、更換和維護 StorSimple 裝置上的備份電池模組。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# 更換 StorSimple 裝置上的備份電池模組

## 概觀

Microsoft Azure StorSimple 裝置上的主要機箱電源和冷卻模組 (PCM) 具有額外的電池組。 這個電池組會提供電源，以便如果主要機箱失去 AC 電源，StorSimple 裝置可以儲存資料。 這個電池組稱為 *備份電池模組*。 備份電池模組僅針對 StorSimple 裝置中的主要機箱而存在 (EBOD 機箱未包含備份電池模組) 。 

本教學課程說明如何：

- 取下備份電池模組 
- 安裝新的備份電池模組
- 維護備份電池模組

>[AZURE.IMPORTANT] 移除及更換備份電池模組中的安全資訊之前請 [簡介 StorSimple 硬體元件更換](storsimple-hardware-component-replacement.md)。

## 取下備份電池模組

Microsoft Azure StorSimple 裝置的備份電池模組是現場可置換裝置。 在 PCM 中安裝它之前，電池模組應該儲存在其原始包裝中。

#### 若要取下備份電池模組

1. 在 Azure 傳統入口網站，瀏覽至 **裝置** > **維護** > **硬體狀態**。 在 **共用元件**, ，查看電池狀態。

2. 識別電池故障的 PCM。 圖 1 顯示 StorSimple 裝置的背面。

    ![裝置主要機箱模組的後擋板](./media/storsimple-battery-replacement/IC740994.png)

    **[圖 1** 顯示 PCM 和控制器模組的主要裝置背面

  	|標籤|說明|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|控制器 0|
  	|4|控制器 1|

    [圖 2 中的編號 3 所示的監控指示器 LED PCM 0 對應到上 **電池故障** 應該亮起。

    ![裝置後擋板 PCM 監視 LED 指示燈](./media/storsimple-battery-replacement/IC740992.png)

    **[圖 2** 顯示監控指示器 Led 的 PCM 背面

  	|標籤|說明|
  	|:---|:-----------|
  	|1|AC 電源故障|
  	|2|風扇故障|
  	|3|電池故障|
  	|4|PCM 正常|
  	|5|DC 電源故障|
  	|6|電池狀況良好|

3. 若要移除電池故障的 PCM，依照 [Pcm](storsimple-power-cooling-module-replacement.md#remove-a-pcm)。

4. 一旦取下 PCM，請提起並向上旋轉電池模組把手 (如下圖中所示)，並將它拔出以取下電池。

    ![從 pcm 取出電池](./media/storsimple-battery-replacement/IC741019.png)

    **[圖 3** 從 PCM 取出電池

5. 將模組放置在現場可更換裝置包裝中。

6. 將故障裝置退回給 Microsoft，以取得適當的服務和處理。

## 安裝新的備份電池模組

執行下列步驟，以在 StorSimple 裝置的主要機箱中安裝更換電池模組。

#### 若要安裝電池模組

1. 以適當的方向將備份電池模組放入 PCM 中。

2. 全力按壓電池模組把手以固定連接器。

3. 中的指導方針來取代主要機箱 PCM [更換 StorSimple 裝置上的電源和冷卻模組](storsimple-power-cooling-module-replacement.md)。

4. 更換已完成後，存取 Azure 傳統入口網站，並瀏覽至 **裝置** > **維護** > **硬體狀態**, ，並檢查並確定已成功安裝電池的狀態。 如果 LED 顯示綠色狀態，則表示電池狀況良好。

## 維護備份電池模組

在裝置中，備份電池模組會在停電期間提供電源給控制器。 它可讓 StorSimple 裝置在以控制方式關閉之前儲存重要資料。 由於 PCM 中有兩個完全充電的電池，系統可以處理兩個連續停電事件。

在 Azure 傳統入口網站， **硬體狀態** 上 **維護** 頁面會指出電池無法正常運作或已接近生命結束。 電池狀態由 **PCM 0 中的電池** 或 **PCM 1 中的電池** 下 **共用元件**。 此頁面會顯示 **降級** 狀態生命週期結束接近，和 **失敗** 結束壽命達到的。 

>[AZURE.NOTE] 電池可以報告 **失敗** 它可能只需收取任何費用。
 
如果 **降級** 狀態出現，我們建議您採取下列動作 ︰

- 系統最近可能遭遇停電，或電池可能正在進行定期維護。 觀察系統 12 小時，再繼續進行。

    - 如果狀態仍為 **降級** 12 小時持續連接至 AC 電源，而且控制器與 Pcm 執行之後，則電池需要更換。 請 [連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md) 取得更換備份電池模組。

    - 如果狀態在 12 小時之後變成良好，則電池可操作，而且只需維護費用。

- 如果沒有相關聯的 AC 電源喪失，而且 PCM 已開啟並連接至 AC 電源，則電池需要更換。 [請連絡 Microsoft 支援](storsimple-contact-microsoft-support.md) 以訂購更換備份電池模組。

>[AZURE.IMPORTANT] 處置故障電池依據國家及地區法規。 

## 後續步驟

深入了解 [StorSimple 硬體元件更換](storsimple-hardware-component-replacement.md)。


