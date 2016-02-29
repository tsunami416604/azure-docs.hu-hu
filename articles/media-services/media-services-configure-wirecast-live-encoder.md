<properties 
    pageTitle="設定 Telestream Wirecast 編碼器來傳送單一位元速率的即時串流 " 
    description="本主題示範如何設定 Wirecast 即時編碼器，藉此將單一位元速率的即時串流傳送到已啟用即時編碼的 AMS 通道。 " 
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

#使用 Wirecast 編碼器來傳送單一位元速率的即時串流

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Elemental Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

本主題說明如何設定 [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) 即時編碼器來傳送單一位元速率串流來 AMS 通道，啟用即時編碼。  如需詳細資訊，請參閱 [使用通道來以 Azure 媒體服務執行即時編碼啟用之](media-services-manage-live-encoder-enabled-channels.md)。

本教學課程示範如何使用 Azure 媒體服務總管 (AMSE) 工具管理 Azure 媒體服務 (AMS)。 此工具只會在 Windows 電腦上執行。 如果您是 Mac 或 Linux 上，使用 Azure 傳統入口網站建立 [通道](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) 和 [程式](media-services-portal-creating-live-encoder-enabled-channel#create-and-manage-a-program)。


##先決條件

- [建立 Azure 媒體服務帳戶](media-services-create-account.md)
- 請確定執行的串流端點至少有一個配置的串流單位。 如需詳細資訊，請參閱 [管理串流端點在媒體服務帳戶](media-services-manage-origins.md)
- 安裝最新版 [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) 工具。
- 啟動工具並連接到您的 AMS 帳戶。

##秘訣

- 請盡可能使用實體的有線網際網路連線。
- 判斷頻寬需求的一項法則是將串流位元速率加倍。 雖然這不是強制性需求，卻有助於減輕網路阻塞的影響。
- 使用軟體型編碼器時，請關閉任何不必要的程式。


## 建立通道

1.  在 [AMSE] 工具中，瀏覽至 **Live** 索引標籤，然後以滑鼠右鍵按一下 [通道] 區域中。 選取 **...建立通道** 從功能表。

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. 指定通道名稱，描述欄位是選擇性的。 在 [通道設定] 下選取 **標準** 即時編碼選項時，輸入通訊協定設為 **RTMP**。 您可以將所有其他設定保留現狀。


請確定 **立即開始新的通道** 已選取。

3. 按一下 [ **建立通道**。
![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] 通道可能需要長達 20 分鐘的時間才能啟動。

您可以在通道啟動時 [將編碼器設定](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp)。

>[AZURE.IMPORTANT] 請注意，便會開始計費，通道便會進入就緒狀態。 如需詳細資訊，請參閱 [通道的狀態](media-services-manage-live-encoder-enabled-channels.md#states)。

##<a id=configure_wirecast_rtmp></a>設定 Telestream wirecast 編碼程式

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


###組態步驟

1. 在使用的機器上開啟 Telestream Wirecast 應用程式，並設定 RTMP 串流。
2. 設定瀏覽至輸出 **輸出** ] 索引標籤，然後選取 **輸出設定...**。
    
    請確定 **輸出目的地** 設為 **RTMP 伺服器**。
3. 按一下 [ **確定**。
4. 在 [設定] 頁面上設定 **目的地** 欄位 **Azure 媒體服務**。
 
    編碼設定檔是以預先選取 **Azure H.264 720 p 16:9 (1280 x 720)**。 若要自訂這些設定，選取齒輪圖示右邊的下拉式清單中，然後選擇 **新預設**。

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. 設定編碼器預設。

    為預設命名，並檢查下列建議的設定：

    **影片**
    
    - 編碼器：MainConcept H.264
    - 每秒畫面格數：30
    - 平均位元速率：5000 kbit/秒 (可視網路限制加以調整)
    - 設定檔：主要
    - 畫面間隔：60 個畫面

    **音訊**

    - 目標位元速率：192 kbit/秒
    - 取樣速率：44.100 kHz
     
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. 按下 **儲存**。

    [編碼] 欄位現在會有可供選取的新建立設定檔。 

    請確定已選取新的設定檔。

7. 取得通道的輸入 URL，以便將它指派給 Wirecast **RTMP 端點**。
    
    瀏覽回到 AMSE 工具，並檢查通道的完成狀態。 一旦狀態變更從 **起始** 到 **執行**, ，您可以取得輸入的 URL。
      
    當執行通道時，通道名稱上按一下滑鼠右鍵，透過瀏覽到動態顯示 **複製到剪貼簿的輸入 URL** ，然後選取 **主要輸入
   URL**。  
    
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. 在 Wirecast **輸出設定** ] 視窗中，貼上這項資訊 **位址** 程式輸出區段中，並指派資料流名稱的欄位。 


    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. 選取 **確定**。

10. 在主要 **Wirecast** 畫面上，確認輸入的來源的視訊和音訊準備就緒後，然後按 enter **資料流** 左上角。

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT] 然後按 **資料流**, ，您 **必須** 確保通道已就緒。 
>此外，請務必不要讓通道在沒有輸入比重摘要的情況下，處於就緒狀態超過 15 分鐘。

##測試播放
  
1. 瀏覽至 AMSE 工具，然後以滑鼠右鍵按一下要測試的通道。 從功能表中，將滑鼠停留在 **播放預覽** ，然後選取 **與 Azure Media Player**。  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

如果播放器中出現串流，則編碼器已妥善設定為連接到 AMS。 

如果收到錯誤，則必須重設通道，且編碼器設定需要調整。 請參閱 [疑難排解](media-services-troubleshooting-live-streaming.md) 指引的主題。  

##建立程式

1. 一旦確認通道播放沒問題後，請建立程式。 在 **Live** AMSE 工具索引標籤，以滑鼠右鍵按一下程式區域內，然後選取 **建立新的程式**。  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

2. 命名程式，並有需要則調整 **封存時間長度** (預設為 4 小時)。 您也可以指定儲存體位置，或保留為預設值。  
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

