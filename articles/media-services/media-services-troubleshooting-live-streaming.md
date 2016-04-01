<properties 
    pageTitle="針對即時資料流進行疑難排解的指南" 
    description="本主題提供有關如何疑難排解即時資料流問題的建議。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/05/2015"    
    ms.author="juliako"/>

#針對即時資料流進行疑難排解的指南

本主題提供有關如何疑難排解某些即時資料流問題的建議。

## 內部部署編碼器的相關問題 

本節提供如何疑難排解內部部署編碼器相關問題的建議，而編碼器的設定是傳送單一位元速率資料流到啟用即時編碼的 AMS 通道。

###問題：沒有輸出漸進式資料流的選項

- **可能的問題**︰ 所用的編碼器不會自動非交錯。 

    **疑難排解步驟**︰ 尋找編碼器介面中的取消交錯式選項。 一旦啟用非交錯處理，請再次檢查漸進式的輸出設定。 
 
###問題：已嘗試數種編碼器輸出設定，但仍然無法連線。 

- **可能的問題**: Azure 編碼的通道未正確重設。 

    **疑難排解步驟**︰ 請確定編碼器不會再推送到 AMS、 停止和重設通道。 再次執行後，請嘗試使用新的設定連線至您的編碼器。 有時候，通道在經過幾次失敗的嘗試後可能會損毀，如果這樣仍無法更正問題，請嘗試建立新通道。  

- **可能的問題**: GOP 大小或主要畫面格設定不佳。 

    **疑難排解步驟**︰ 建議的 GOP 大小或主要畫面格間隔為 2 秒。 某些編碼器以畫面數目計算此設定，某些則使用秒。 例如：輸出 30fps 時，GOP 大小會是 60 個畫面，相當於 2 秒。  
     
- **可能的問題**︰ 關閉的連接埠會封鎖資料流。 

    **疑難排解步驟**: RTMP 透過資料流時檢查以確認 1935年和 1936年的輸出連接埠已開啟防火牆及/或 proxy 設定。 使用 RTP 資料流時，請確認輸出連接埠 2010 已開啟。 


###問題：設定編碼器使用 RTP 通訊協定串流處理時，沒有輸入主機名稱的位置。 

- **可能的問題**︰ 許多 RTP 編碼器不允許的主機名稱和 IP 位址必須取得。  

    **疑難排解步驟**︰ 若要尋找 IP 位址，開啟 [命令提示字元的任何電腦上。 若要在 Windows 中執行此操作，請開啟「執行」啟動程式 (WIN + R) 並輸入 "cmd" 來開啟。  

    命令提示字元開啟後，輸入 "Ping [AMS 主機名稱]"。 

    藉由省略 Azure 內嵌 URL 中的連接埠號碼，即可衍生主機名稱，如以下範例中反白的部分所示： 

    rtp://test2-amstest009.rtp.channel.mediaservices.windows.net:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###問題：無法播放發行的資料流。
 
- **可能的問題**︰ 沒有串流端點可執行，或沒有配置任何串流處理單元 （縮放單位）。 

    **疑難排解步驟**︰ 瀏覽至 [串流端點] 索引標籤，在 AMSE 工具中，並確認沒有執行一個串流單元的串流端點。 
    
>[AZURE.NOTE] 如果疑難排解步驟後您仍無法成功地串流處理，要提交支援票證，使用 Azure 傳統入口網站。

##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


