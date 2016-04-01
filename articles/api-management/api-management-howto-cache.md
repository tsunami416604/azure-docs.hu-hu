<properties
    pageTitle="新增快取以改善 Azure API 管理的效能 | Microsoft Azure"
    description="了解如何改善 API 管理服務呼叫的延遲、頻寬耗用量和 Web 服務負載。"
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
    ms.topic="get-started-article"
    ms.date="12/16/2015"
    ms.author="sdanie"/>

# 新增快取以改善 Azure API 管理的效能

可以設定 API 管理中的作業進行回應快取。 對於不常變更的資料，回應快取可大幅降低 API 延遲、頻寬耗用量和 Web 服務負載。

本指南說明如何新增 API 的回應快取，以及設定範例 Echo API 作業的原則。 您之後可以從開發人員入口網站呼叫作業，確認快取作用中。

>[AZURE.NOTE] 如需使用原則運算式的索引鍵的快取項目，請參閱 [自訂 Azure API 管理中的快取](api-management-sample-cache-by-key.md)。

## 必要條件

遵循本指南中的步驟之前，您必須擁有已設定 API 和產品的 API 管理服務執行個體。 如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

## <a name="configure-caching"> </a>設定要快取的作業

在此步驟中，您將檢閱的快取設定 **GET Resource (cached)** 範例 Echo API 的作業。

>[AZURE.NOTE] 每個 API 管理服務執行個體隨附預先設定的範例 Echo api，可用來試驗與了解 API 管理的。 如需詳細資訊，請參閱 [開始使用 Azure API 管理][]。

若要開始使用，請按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。 這會帶您前往 API 管理發行者入口網站。

![發行者入口網站][api-management-management-console]

按一下 [ **Api** 從 **API 管理** 左邊的功能表，然後按一下 **Echo API**。

![Echo API][api-management-echo-api]

按一下 [ **作業** 索引標籤，然後再按一下 **GET Resource (cached)** 作業 **作業** 清單。

![Echo API operations][api-management-echo-api-operations]

按一下 [ **快取** 索引標籤來檢視這項作業的快取設定。

![Caching tab][api-management-caching-tab]

若要啟用快取作業，請選取 **啟用** ] 核取方塊。 此範例中已啟用快取。

識別每一個操作回應，根據值 **依查詢字串參數改變** 和 **Vary 標頭所** 欄位。 如果您想要根據查詢字串參數或標頭來快取多個回應，您可以在這兩個欄位中設定。

**持續時間** 指定快取回應的到期間隔。 在此範例中，間隔是 **3600** 秒，相當於一小時。

在此範例中，第一個要求中使用的快取組態 **GET Resource (cached)** 作業從後端服務傳回的回應。 此回應將被快取，並依指定的標頭和查詢字串參數來識別。 後續使用相符的參數呼叫此操作時，將傳回快取的回應，直到快取期間間隔到期為止。

## <a name="caching-policies"> </a>檢閱快取原則

在此步驟中，您可以檢閱的快取設定 **GET Resource (cached)** 範例 Echo API 的作業。

當快取設定作業上設定 **快取** 索引標籤上，快取原則會加入操作。 您可以在原則編輯器中檢閱和編輯這些原則。

按一下 [ **原則** 從 **API 管理** 左邊的功能表，然後選取 **Echo API / GET Resource (cached)** 從 **作業** 下拉式清單。

![Policy scope operation][api-management-operation-dropdown]

這樣會在原則編輯器中顯示此操作的原則。

![API Management policy editor][api-management-policy-editor]

這項作業的原則定義包含使用檢閱定義快取組態的原則 **快取** 上一個步驟中的索引標籤。

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] 在原則編輯器中的快取原則所做的變更會反映在 **快取** ] 索引標籤的作業，並且反之亦然。

## <a name="test-operation"> </a>呼叫作業和測試快取

為了瞭解快取的運作方式，我們可以從開發人員入口網站呼叫操作。 按一下 [ **開發人員入口網站** 右上方功能表中。

![開發人員入口網站][api-management-developer-portal-menu]

按一下 [ **Api** 上方的功能表，然後選取 **Echo API**。

![Echo API][api-management-apis-echo-api]

>如果您的帳戶只設定或只看見一個 API，按一下 API 將帶您直接前往該 API 的作業。

選取 **GET Resource (cached)** 作業，然後再按一下 **開啟主控台**。

![Open console][api-management-open-console]

主控台可讓您直接從開發人員入口網站叫用操作。

![主控台][api-management-console]

保留預設值為 **param1** 和 **param2**。

選取所需的索引鍵，從 **訂閱金鑰** 下拉式清單。 若您的帳戶只有一個訂用帳戶，則系統會自動選取它。

輸入 **sampleheader: value1** 中 **要求標頭** 文字方塊。

按一下 [ **HTTP Get** 並記下回應標頭。

輸入 **sampleheader: value2** 中 **要求標頭** 文字方塊，然後再按一下 [ **HTTP Get**。

請注意，值 **sampleheader** 仍 **value1** 在回應中。 請試驗一些不同的值，可發現傳回第一次呼叫的快取回應。

輸入 **25** 到 **param2** 欄位，，然後按一下 **HTTP Get**。

請注意，值 **sampleheader** 在回應現在是 **value2**。 因為操作結果是依查詢字串來識別，所以不會傳回前一個快取回應。

## <a name="next-steps"> </a>後續步驟

-   查看其他主題中 [開始使用進階 API 組態][] 教學課程。
-   如需有關快取原則的詳細資訊，請參閱 [快取原則][] 中 [API 管理原則參考文件][]。
-   如需使用原則運算式的索引鍵的快取項目，請參閱 [自訂 Azure API 管理中的快取](api-management-sample-cache-by-key.md)。

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps


