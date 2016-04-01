<properties 
    pageTitle="使用通道，從內部部署編碼器接收多位元速率即時串流" 
    description="本主題描述如何讓通道接收內部部署編碼器的多位元速率即時資料流。 串流可以隨即透過一或多個串流端點傳遞給用戶端播放應用程式，使用下列其中一個自動調整串流通訊協定：HLS、Smooth Streaming、MPEG DASH、HDS。" 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin,Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="12/15/2015"  
    ms.author="juliako"/>

#使用通道，從內部部署編碼器接收多位元速率即時串流

##概觀

在 Azure Media Services **通道** 代表處理即時串流內容的管線。 A **通道** 以下列方式接收即時輸入的串流 ︰

- 內部部署即時編碼器會傳送多位元速率 **RTMP** 或 **Smooth Streaming** (分散的 MP4) 到通道。 您可以使用下列輸出多位元速率 Smooth Streaming 的即時編碼器：Elemental、Envivio、Cisco。  下列即時編碼器會輸出 RTMP：Adobe Flash Live、Telestream Wirecast 和 Tricaster 轉錄器。 內嵌的串流會通過 **通道** 而不需任何進一步處理。 您的即時編碼器也會傳送單一位元速率串流，但是不建議。 接到要求時，媒體服務會傳遞串流給客戶。


下圖顯示一個即時資料流工作流程，這個流程利用即時編碼器輸出多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 資料流。 

![即時工作流程][live-overview]

本主題包含下列項目：

- [常見即時串流案例](media-services-manage-channels-overview.md#scenario)
- [通道和其相關元件的說明](media-services-manage-channels-overview.md#channel)
- [考量](media-services-manage-channels-overview.md#considerations)
- [與即時串流相關的工作](media-services-manage-channels-overview.md#tasks)

##<a id="scenario"></a>常見即時串流案例
下列步驟描述當我們建立一般即時資料流應用程式時，會涉及到的各種工作。

1. 將攝影機連接到電腦。 啟動並設定內部部署即時編碼器，讓它輸出多位元速率 RTMP 或 Fragmented MP4 (Smooth Streaming) 資料流。 如需詳細資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。
    
    此步驟也可以在您建立通道之後執行。

1. 建立並啟動通道。
1. 擷取通道內嵌 URL。 

    內嵌 URL 可供即時編碼器用來傳送串流到通道。
1. 擷取通道預覽 URL。 

    使用此 URL 來確認您的通道會正確接收即時串流。

3. 建立程式。 

    使用 Azure 傳統入口網站時，建立程式也會建立資產。 

    使用 .NET SDK 或 REST 時，您必須建立資產並指定要在建立程式時使用此資產。 
1. 發行與程式相關聯的資產。   

    請確定在您想串流內容的串流端點上至少有一個串流保留單元。
1. 當您準備好開始串流和封存時，請啟動程式。
2. 即時編碼器會收到啟動公告的信號 (選擇性)。 公告會插入輸出串流中。
1. 每當您想要停止串流處理和封存事件時，請停止程式。
1. 刪除程式 (並選擇性地刪除資產)。     

 [即時串流工作](media-services-manage-channels-overview.md#tasks) 一節將示範如何達成上述工作的主題連結。

##<a id="channel"></a>通道和其相關元件的說明

###<a id="channel_input"></a>頻道輸入 (內嵌) 組態

####<a id="ingest_protocols"></a>嵌入串流通訊協定

媒體服務支援各種採用下列資料流通訊協定的內嵌即時訊息來源： 

- **多位元速率 Fragmented MP4**
 
- **多位元速率 RTMP** 

    當 **RTMP** 內嵌串流通訊協定已選取，兩個 ingest （input） 端點建立通道 ︰ 
    
    **主要 URL**︰ 指定完整的 URL 的通道主要 RTMP 內嵌端點。

    **次要 URL** （選用） ︰ 指定完整的 URL 的通道次要 RTMP 內嵌端點。 


    Use the secondary URL if you want to improve the durability and fault tolerance of your ingest stream as well as encoder failover and fault-tolerance, especially for the following scenarios.

    - Single encoder double pushing to both Primary and Secondary URLs:
    
        The main purpose of this is to provide more resiliency to network fluctuations and jitters. Some RTMP encoders do not handle network disconnects well. When a network disconnect happens, an encoder may stop encoding and will not send the buffered data when reconnect happens, this causes discontinuities and data lost. Network disconnects can happen because of a bad network or a maintenance on Azure side. Primary/secondary URLs reduce the network issues and also provide a controlled upgrade process. Each time a scheduled network disconnect happens, Media Services manages the primary and secondary disconnect and provides a delayed disconnect between the two which gives time for encoders to keep sending data and reconnect again. The order of the disconnects can be random, but there will be always a delay between primary/secondary or secondary/primary. In this scenario encoder is still the single point of failure.
     
    - Multiple encoders each encoder pushing to dedicated point:
        
        This scenario provides both encoder and ingest redundancy. In this scenario encoder1 pushes to the primary URL and encoder2 pushes to secondary URL. When there is an encoder failure other encoder can still keep sending data. Data redundancy can still be maintained because Media Services does not disconnect primary and secondary at the same time. This scenario assumes encoders are time sync and provides exactly same data.  
 
    - Multiple encoder double pushing to both primary and secondary URLs:
    
        In this scenario both encoders push data to both primary and secondary URLs. This provides the best reliability and fault tolerance as well as data redundancy. It can tolerate both encoder failures and also disconnects even if one encoder stops working. This scenario assumes encoders are time sync and provides exactly same data.  

如需 RTMP 即時編碼器的相關資訊，請參閱 [Azure 媒體服務 RTMP 支援和即時編碼器](http://go.microsoft.com/fwlink/?LinkId=532824)。

您必須考量下列事項：

- 確定您的網際網路速度夠快，足以將資料傳送至內嵌點。 
- 使用次要內嵌 URL 時會佔用額外的頻寬。 
- 內送的多位元速率資料流最多可以有 10 個視訊品質等級 (也稱為圖層)，以及最多 5 個音軌。
- 任何視訊品質等級或圖層的最高平均位元速率，應低於 10 Mbps。
- 所有視訊和音訊串流的平均位元速率彙總，應低於 25 Mbps。
- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。 如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。 
- 您可以內嵌單一位元速率至您的通道，不過由於通道尚未處理串流，所以用戶端應用程式也會收到單一位元速率串流 (不建議這個選項)。

####內嵌 URL (端點) 

通道提供一個輸入端點 (內嵌 URL)，您可以在即時編碼器中指定這個端點，這樣編碼器就可以將資料流推播到您的通道。   

當您建立通道時，您可以取得內嵌 URL。 若要取得這些 Url，通道不會不一定要中 **執行** 狀態。 當您準備好開始將資料推入通道時，通道必須處於 **執行** 狀態。 一旦通道開始內嵌資料後，您可以透過預覽 URL 來預覽資料流。

您可以選擇透過 SSL 連線來內嵌 Fragmented MP4 (Smooth Streaming) 即時資料流。 若要透過 SSL 擷取，請務必將擷取 URL 更新為 HTTPS。 目前，您無法內嵌 RTMP over SSL。 

####<a id="keyframe_interval"></a>主要畫面格間隔

當使用內部部署即時編碼器來產生多位元速率資料流時，主要畫面格間隔會指定 GOP 持續期間 (供該外部編碼器使用)。 一旦通道收到此內送串流時，然後您可以再將即時串流傳遞至下列任一形式的用戶端播放應用程式：Smooth Streaming、DASH 及 HLS。 在執行即時資料流時，會一律動態封裝 HLS。 依預設，媒體服務會根據從即時編碼器收到的主要畫面格間隔 (也稱為圖片群組 – GOP)，自動計算 HLS 區段封裝比例 (每一個區段的片段)。 

下表顯示如何計算區段持續時間：

主要畫面格間隔|HLS 區段封裝比例 (FragmentsPerSegment)|範例
---|---|---
小於或等於 3 秒|3:1|如果 KeyFrameInterval (或 GOP) 為 2 秒鐘，則預設 HLS 區段封裝比例就會是 3 比 1，這將會建立 6 秒鐘的 HLS 區段。
3 到 5 秒|2:1|如果 KeyFrameInterval (或 GOP) 為 4 秒鐘，則預設 HLS 區段封裝比例就會是 2 比 1，這將會建立 8 秒鐘的 HLS 區段。
大於 5 秒|1:1|如果 KeyFrameInterval (或 GOP) 為 6 秒鐘，則預設 HLS 區段封裝比例就會是 1 比 1，這將會建立 6 秒鐘的 HLS 區段。


您可以設定通道的輸出並在 ChannelOutputHls 上設定 FragmentsPerSegment，即可變更每個區段比例的片段。 

您也可以在 ChanneInput 上設定 KeyFrameInterval 屬性，即可變更主要畫面格間隔值。 

如果您明確設定 KeyFrameInterval，就會使用上述的規則計算 FragmentsPerSegment HLS 區段封裝比率。  

如果您明確設定 KeyFrameInterval 和 FragmentsPerSegment，媒體服務會使用您所設定的值。 


####允許的 IP 位址

您可以定義允許將視訊發行到這個通道的 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如'10.0.0.1/22’)，或使用 IP 位址和以點分隔十進位子網路遮罩的 IP 範圍 (例如‘10.0.0.1(255.255.252.0)’)。 

如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。

###通道預覽 

####預覽 URL

通道會在進一步處理和傳遞之前，提供您用來預覽及驗證串流的預覽端點 (預覽 URL)。

當您建立通道時，您可以取得預覽 URL。 若要取得 URL，通道不會不一定要在 **執行** 狀態。 

通道開始內嵌資料後，您就可以預覽您的資料流。

請注意，無論指定的輸入類型為何，預覽資料流目前只能利用 Fragmented MP4 (Smooth Streaming) 格式傳遞。 您可以使用 [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor) 播放器測試 Smooth Stream。 您也可以使用裝載於 Azure 傳統入口網站中的播放器來檢視您的串流。


####允許的 IP 位址

您可以定義允許連接到預覽端點的 IP 位址。 如果沒有指定 IP 位址，將會允許任何 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如'10.0.0.1')、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如'10.0.0.1/22’)，或使用 IP 位址和以點分隔十進位子網路遮罩的 IP 範圍 (例如‘10.0.0.1(255.255.252.0)’)。

###通道輸出

如需詳細資訊，請參閱 [設定主要畫面格間隔](#keyframe_interval) 一節。


###通道的程式

通道與程式相關聯，而程式可讓您控制即時串流中區段的發行和儲存。 「通道」會管理「程式」。 「通道」與「程式」的關係與傳統媒體十分類似，其中，通道具有固定的內容串流，而且程式的範圍是該通道上的某個計時事件。

您可以指定您想要藉由設定保留程式之錄製的內容的時數 **封存時間範圍** 長度。 此值可以設為最少 5 分鐘到最多 25 個小時。 封存時間範圍長度也會指出用戶端可以從目前即時位置及時往回搜尋的最大時間量。 程式在超過指定的時間量後還是可以執行，但是會持續捨棄落後時間範圍長度的內容。 此屬性的這個值也會決定用戶端資訊清單可以成長為多長的時間。

每個程式都與儲存串流內容的資產相關聯。 資產會對應到 Azure 儲存體帳戶中的 blob 容器，且資產中的檔案會儲存為該容器中的 blob。 若要發行程式讓您的客戶檢視串流，您必須建立相關聯資產的隨選定位器。 擁有此定位器，可讓您建立可以提供給用戶端的串流 URL。

通道支援最多三個同時執行的程式，因此您可以建立相同內送串流的多個封存。 這可讓您視需要發佈和封存事件的不同部分。 例如，您的商務需求是封存 6 小時的程式，但只廣播最後 10 分鐘。 為了達成此目的，您必須建立兩個同時執行的程式。 其中一個程式設定為封存 6 小時的事件，但是未發行該程式。 另一個程式則設定為封存 10 分鐘，並發行程式。

您不應該將現有程式重複用於新的事件。 而是為每個事件建立並啟動新的程式 (如＜程式設計即時串流應用程式＞一節中所述)。

當您準備好開始串流和封存時，請啟動程式。 每當您想要停止串流處理和封存事件時，請停止程式。 

若要刪除封存的內容，請停止並刪除程式，然後刪除相關聯的資產。 如果程式使用資產，則無法刪除資產；必須先刪除程式。 

只要您未刪除資產，即使在停止並刪除程式之後，使用者還是可以視需求將封存的內容串流為視訊。

如果想要保留封存的內容，但不要讓它可進行串流處理，請刪除串流定位器。

##<a id="states"></a>通道狀態和狀態如何對應至計費模式 

通道的目前狀態。 可能的值包括：

- **停止**。 這是通道建立後的初始狀態。 在此狀態下，通道屬性可以更新，但是不允許串流。
- **啟動**。 正在啟動通道。 在此狀態期間允許任何更新或串流。 如果發生錯誤，通道會回到已停止狀態。
- **執行**。 通道能夠處理即時串流。
- **停止**。 正在停止通道。 在此狀態期間允許任何更新或串流。
- **刪除**。 正在刪除通道。 在此狀態期間允許任何更新或串流。

下表顯示通道狀態如何對應至計費模式。 
 
通道 state|入口網站 UI Indicators|計費？
---|---|---|---
Starting|Starting|無 （暫時性狀態）
Running|就緒 （沒有執行的程式）<p>或<p>串流 （至少一個執行計畫） |[是]
Stopping|Stopping|無 （暫時性狀態）
Stopped|Stopped|否

##<a id="cc_and_ads"></a>隱藏式字幕和廣告插入 

下表示範支援的隱藏式字幕和廣告插入標準。

標準|注意事項
---|---
CEA-708 和 EIA-608 (708/608)|CEA-708 和 EIA-608 是美國和加拿大的隱藏式字幕標準。<p><p>目前只有編碼的輸入資料流附帶字幕時，才能播放字幕。 您使用的即時媒體編碼器，必須可以將 608 或 708 字幕插入至已傳送至媒體服務的已編碼資料流。 媒體服務會將內含字幕的內容傳遞給您的檢視器。
.ismt 裡面附帶字幕 (Smooth Streaming 文字播放軌)|媒體服務動態封裝功能可讓您的用戶端傳送以下任何格式的內容：MPEG DASH、HLS 或 Smooth Streaming。 不過，如果您內嵌 Fragmented MP4 (Smooth Streaming) 而且在 .ismt 裡面附帶字幕 (Smooth Streaming 文字播放軌)，您就只能將資料流傳遞至 Smooth Streaming 用戶端。
SCTE-35|數位訊號系統，用來提示廣告插入。 下游接收端會使用信號並根據分配的時間，將廣告切割成資料流。 SCTE 35 必須以鬆散播放軌的形式傳送至輸入資料流中。<p><p>請注意，目前唯一支援附帶廣告訊號的輸入資料流格式是 Fragmented MP4 (Smooth Streaming)。 唯一支援的輸出格式也是 Smooth Streaming。


##<a id="Considerations"></a>考量

使用內部部署即時編碼器並將多位元速率資料流傳送到通道時，請注意以下限制：

- 確定您的網際網路速度夠快，足以將資料傳送至內嵌點。 
- 內送的多位元速率資料流最多可以有 10 個視訊品質等級 (10 個圖層)，以及最多 5 個音軌。
- 任何視訊品質等級或圖層的最高平均位元速率，應低於 10 Mbps。
- 所有視訊和音訊串流的平均位元速率彙總，應低於 25 Mbps。
- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。 如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。 


以下是其他與通道和相關元件應用有關的注意事項：

- 每次您重新設定即時編碼器，請呼叫 **重設** 通道上的方法。 重設通道之前，您必須停止程式。 重設通道之後，請重新啟動程式。 
- 只有當通道處於執行中的狀態，且通道上的所有程式皆已停止時，才能停止通道。
- 根據預設，您只能加入最多 5 個通道到媒體服務帳戶。 如需詳細資訊，請參閱 [配額和限制](media-services-quotas-and-limitations.md)。
- 通道或其相關聯程式正在執行時，您無法變更輸入通訊協定。 如果您需要不同的通訊協定，則應該為每個輸入通訊協定建立個別的通道。 
- 您會在您的通道時才需要付費 **執行** 狀態。 如需詳細資訊，請參閱 [這](media-services-manage-channels-overview.md#states) 一節。

##如何建立從內部部署編碼器接收多位元速率即時串流的通道

如需在內部部署即時編碼器的詳細資訊，請參閱 [使用第 3 個廠商即時編碼器與 Azure 媒體服務](https://azure.microsoft.com/blog/azure-media-services-rtmp-support-and-live-encoders/)。

選擇 **入口網站**, ，**.NET**, ，**REST API** 以了解如何建立及管理通道和程式。

[AZURE.INCLUDE [media-services-selector-manage-channels](../../includes/media-services-selector-manage-channels.md)]



##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##相關主題

[Azure 媒體服務的分散 MP4 即時內嵌規格](media-services-fmp4-live-ingest-overview.md)

[使用 Azure 媒體服務傳遞即時串流事件](media-services-live-streaming-workflow.md)

[媒體服務概念](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
 


