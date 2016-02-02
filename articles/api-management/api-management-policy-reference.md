<properties 
    pageTitle="Azure API 管理原則參考文件" 
    description="了解可用來設定 API 管理的原則。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/16/2015" 
    ms.author="sdanie"/>


# Azure API 管理原則參考文件

本節提供索引中的原則 [API 管理原則參考 []][]。 如需新增和設定原則，請參閱 [[API 管理] 中的原則][]。

如果原則不另行指定，則可以在任何 API 管理原則中，使用原則運算式做為屬性值或文字值。 例如，某些原則 [控制流程]][] 和 [設定 ][] 原則以原則運算式為基礎。 如需詳細資訊，請參閱 [進階原則]][] 和 [原則運算式 []][]

## 原則參考索引

-   [存取限制原則]][]
    -   [檢查 HTTP 標頭 []][] -存在和/或 HTTP 標頭的值會強制執行。
    -   [由 ][] -限制呼叫費率，依照每個訂閱防止 API 使用量暴增。
    -   [依索引鍵限制呼叫費率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) -限制呼叫費率，每個索引鍵為基礎防止 API 使用量暴增。
    -   [限制呼叫端 Ip]][] -篩選 (允許/拒絕) 來自特定 IP 位址及/或位址範圍的呼叫。
    -   [設定使用量配額的 ][] -可讓您強制可更新或存留期呼叫量和/或頻寬配額，請依照每個訂閱。
    -   [依索引鍵設定使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) -可讓您強制可更新或存留期呼叫量和/或頻寬配額，以每個索引鍵為基礎。
    -   [驗證 JWT []][] -強制存在且有效的 jwt 必須擷取自指定的 HTTP 標頭或指定的查詢參數。
-   [[進階的原則]][]
    -   [控制流程]][] -有條件地套用原則陳述式，根據評估的布林值結果 [[運算式]][]。
    -   [轉寄要求 []][] -將要求轉寄到後端服務。
    -   [記錄到事件中心 []][] -會以指定的格式傳送訊息所定義的訊息目標 [記錄器](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) 實體。
    -   [傳回回應](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) -中止管線執行，並傳回給呼叫者直接指定的回應。
    -   [傳送單向要求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) -將要求傳送至指定的 URL，而不需等待回應。
    -   [傳送要求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) -將要求傳送至指定的 URL。
    -   [Set 要求方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) -可讓您變更要求的 HTTP 方法。
    -   [設定狀態](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) -變更為指定值的 HTTP 狀態碼。
    -   [設定 ][] -保存具名的值 [[內容]][] 供日後存取變數。
-   [驗證原則]][]
    -   [與 ][] -驗證使用基本驗證的後端服務。
    -   [驗證與用戶端憑證 []][] -後端服務使用用戶端憑證驗證。
-   [快取原則]][]
    -   [從快取 [] 取得][] -執行快取查閱並傳回有效的快取回的應的話。
    -   [儲存至快取 []][] -快取回應根據指定的快取控制組態。
    -   [取得值，從快取](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) -依索引鍵擷取快取的項目。
    -   [值儲存在快取](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) -快取中儲存的項目索引鍵。
-   [跨網域原則]][]
    -   [允許跨網域呼叫 []][] -從 Adobe Flash 和 Microsoft Silverlight 的瀏覽器型用戶端，讓 API 存取。
    -   [CORS []][] -將跨原始來源資源分享 (CORS) 支援至操作或 API，以允許來自瀏覽器為基礎的用戶端的跨網域呼叫。
    -   [JSONP []][] -將 JSON 與補充至操作或 API，以允許來自 JavaScript 瀏覽器型用戶端的跨網域呼叫的 (jsonp) 支援。
-   [轉換原則]][]
    -   [將 JSON 轉換成 ][] -將要求或回應內文從 JSON 為 XML。
    -   [將 XML 轉換成 JSON []][] -將要求或回應內文從 XML 至 JSON。
    -   [尋找和取代字串中的 ][] -尋找要求或回應子字串，並以不同的子字串取代。
    -   [[內容] 中的遮罩 Url][] -重寫 (遮罩) 回應主體中和連結位置標頭，使其指向透過閘道器同等的連結。
    -   [設定後端服務 []][] -變更傳入要求的後端服務。
    -   [設定 ][] -設定傳入和傳出要求的訊息本文。
    -   [設定 HTTP 標頭 []][] -指派值給現有的回應及/或要求標頭，或加入新的回應及/或要求標頭。
    -   [組查詢字串參數 []][] -新增、 取代值或刪除要求查詢字串參數。
    -   [重寫 URL []][] -將要求 URL 從公用格式轉換成 web 服務所需的格式。

## 後續步驟

如需原則運算式的詳細資訊，請觀看以下影片。

> [AZURE.VIDEO policy-expressions-in-azure-api-management]


[access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx 
[check http header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader 
[limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate 
[restrict caller ips]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs 
[set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota 
[validate jwt]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT 
[advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx 
[control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose 
[set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable 
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx 
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables 
[forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest 
[log to event hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub 
[authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx 
[authenticate with basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic 
[authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate 
[caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx 
[get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache 
[store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache 
[cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx 
[allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls 
[cors]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS 
[jsonp]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP 
[transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx 
[convert json to xml]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML 
[convert xml to json]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON 
[find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody 
[mask urls in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent 
[set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService 
[set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody 
[set http header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader 
[set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter 
[rewrite url]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL 
[policies in api management]: api-management-howto-policies.md 
[api management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx 
[policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx 

