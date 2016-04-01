<properties 
    pageTitle="設定 Elemental Live 編碼器來傳送單一位元速率的即時串流" 
    description="本主題會示範如何設定 Elemental Live 編碼器，藉此將單一位元速率的即時串流傳送到 AMS 通道，其已針對即時編碼而啟用。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako,cenkdin,anilmur" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/15/2015"  
    ms.author="juliako"/>

#使用 Elemental Live 編碼器來傳送單一位元速率的即時串流

> [AZURE.SELECTOR]
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

本主題說明如何設定 [元素 Live](http://www.elementaltechnologies.com/products/elemental-live) 為即時編碼啟用傳送單一位元速率串流來 AMS 通道的編碼器。  如需詳細資訊，請參閱 [使用通道來以 Azure 媒體服務執行即時編碼啟用之](media-services-manage-live-encoder-enabled-channels.md)。

本教學課程示範如何使用 Azure 媒體服務總管 (AMSE) 工具管理 Azure 媒體服務 (AMS)。 此工具只會在 Windows 電腦上執行。 如果您是 Mac 或 Linux 上，使用 Azure 傳統入口網站建立 [通道](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) 和 [程式](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program)。

##必要條件

- 必須具備使用 Elemental Live Web 介面來建立即時事件的工作知識。
- [建立 Azure 媒體服務帳戶](media-services-create-account.md)
- 請確定執行的串流端點至少有一個配置的串流單位。 如需詳細資訊，請參閱 [管理串流端點在媒體服務帳戶](media-services-manage-origins.md)

- 安裝最新版 [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) 工具。
- 啟動工具並連接到您的 AMS 帳戶。

##秘訣

- 請盡可能使用實體的有線網際網路連線。
- 判斷頻寬需求的一項法則是將串流位元速率加倍。 雖然這不是強制性需求，卻有助於減輕網路阻塞的影響。
- 使用軟體式編碼器時，請關閉任何不必要的程式。

## Elemental Live 與 RTP 嵌入

本節說明如何設定可透過 RTP 傳送單一位元速率的即時串流 Elemental Live 編碼器。  如需詳細資訊，請參閱 [透過 RTP MPEG-TS 串流](media-services-manage-live-encoder-enabled-channels.md#channel)。

### 建立通道

1.  在 [AMSE] 工具中，瀏覽至 **Live** 索引標籤，然後以滑鼠右鍵按一下 [通道] 區域中。 選取 **建立通道...** 從功能表中。

![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. 指定通道名稱，描述欄位為選填。 在 [通道設定] 下選取 **標準** 即時編碼選項時，輸入通訊協定設為 **RTP (MPEG-TS)**。 您可以將所有其他設定保留現狀。


請確定 **立即開始新的通道** 已選取。

3. 按一下 [ **建立通道**。
![元素](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

>[AZURE.NOTE] 通道可能需要長達 20 分鐘的時間才能啟動。

您可以在通道啟動時 [將編碼器設定](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp)。

>[AZURE.IMPORTANT] 請注意，便會開始計費，通道便會進入就緒狀態。 如需詳細資訊，請參閱 [通道的狀態](media-services-manage-live-encoder-enabled-channels.md#states)。

###<a id=configure_elemental_rtp></a>設定 Elemental Live 編碼器 

在本教學課程中會使用下列輸出設定。 本章節的其餘部分將詳細說明組態步驟。 

**視訊**:
 
- 轉碼器：H.264 
- 設定檔：高 (層級 4.0) 
- 位元速率：5000 kbps 
- 主要畫面格：2 秒 (60 秒) 
- 畫面播放速率：30
 
**音訊**:

- 轉碼器：AAC (LC) 
- 位元速率：192 kbps 
- 取樣速率：44.1 kHz


####組態步驟

1. 瀏覽至 **元素 Live** 網站介面，並設定好的編碼器 **UDP/TS** 資料流。 

2. 一旦建立新的事件，向下輸出群組捲動，並新增 **UDP/TS** 輸出群組。 

3. 選取 [建立新的輸出 **新的資料流** ，然後按一下 [ **加入輸出**。  
    
    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)
    
    >[AZURE.NOTE] 建議元素事件已設為 「 系統時鐘 」 協助編碼器在資料流失敗的情況下重新連線時。

4. 既然已建立的輸出，按一下 **新增的資料流**。 現在可以設定輸出設定。 
5. 向下捲動到 「 串流處理 1"已建立，請按一下 [ **視訊** 左側索引標籤，展開 **進階** 設定] 區段中。 

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    雖然 Elemental Live 有各種不同的可用自訂，建議對串流至 AMS 入門使用下列設定。 
    
    - 解析度：1280 x 720 
    - 畫面播放速率：30 
    - GOP 大小：60 個畫面格 
    - 交錯式模式：漸進式 
    - 位元速率：5000000 位元/秒 (這可以根據網路限制調整) 
    

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

6. 取得通道的輸入 URL。
    
    瀏覽回 AMSE 工具，並檢查通道的完成狀態。 一旦狀態變更從 **起始** 到 **執行**, ，您可以取得輸入的 URL。
      
    當執行通道時，通道名稱上按一下滑鼠右鍵，透過瀏覽到動態顯示 **複製到剪貼簿的輸入 URL** ，然後選取 **主要輸入
   URL**。  
    
    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
    
1. 貼上這項資訊 **主要目的** 元素的欄位。 所有其他設定可以保留預設值。
    
    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    如需額外的備援，請對次要輸入 URL 重複這些步驟，方法是為 UDP/TS 串流建立個別的「輸出」索引標籤。
    
7. 按一下 [ **建立** （如果已建立新的事件） 或 **更新** （如果正在編輯現有事件），然後繼續進行啟動編碼器。 

>[AZURE.IMPORTANT] 然後按 **啟動** 元素 Live web 介面上您 **必須** 確保通道已就緒。 
>此外，請確保不要讓通道處於就緒狀態而無任何事件超過 15 分鐘。

在串流已經執行了 30 秒後，導覽回 AMSE 工具和測試播放。  

###測試播放
  
1. 瀏覽至 AMSE 工具，然後以滑鼠右鍵按一下要測試的通道。 從功能表中，將滑鼠停留在 **播放預覽** ，然後選取 **與 Azure Media Player**。  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

如果播放器中出現串流，則編碼器已妥善設定為連接到 AMS。 

如果收到錯誤，則必須重設通道，且編碼器設定需要調整。 請參閱 [疑難排解](media-services-troubleshooting-live-streaming.md) 指引的主題。   

###建立程式

1. 一旦確認通道播放沒問題後，請建立程式。 在 **Live** AMSE 工具索引標籤，以滑鼠右鍵按一下程式區域內，然後選取 **建立新的程式**。  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)

2. 命名程式，並有需要則調整 **封存時間長度** （預設為 4 小時）。 您也可以指定儲存體位置，或保留為預設值。  
3. 檢查 **現在啟動程式** 方塊。
4. 按一下 [ **建立程式**。  
  
    注意：建立程式時所使用的時間會比建立通道時更少。    
 
5. 一旦應用程式，以確認播放程式上按一下滑鼠右鍵，瀏覽至 **播放程式** ，然後選取 [ **與 Azure Media Player**。  
6. 確認，以滑鼠右鍵按一下程式並選取 [ **輸出 URL 複製到剪貼簿** (或擷取這項資訊從 **程式資訊和設定** 功能表選項)。 

串流現在已經可以內嵌於播放程式中，或散發給某個對象，以供即時檢視。  

## 疑難排解

請參閱 [疑難排解](media-services-troubleshooting-live-streaming.md) 指引的主題。 


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


