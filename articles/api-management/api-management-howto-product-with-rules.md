<properties
    pageTitle="使用 Azure API 管理保護 API| Microsoft Azure"
    description="了解如何使用配額和節流 (頻率限制) 原則保護您的 API。"
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
    ms.date="12/07/2015"
    ms.author="sdanie"/>

# 使用 Azure API 管理以頻率限制保護 API

本指南說明藉由使用 Azure API 管理設定頻率限制和配額原則，來為後端 API 增加保護是多麼簡單容易的工作。

在本教學課程中，您會建立「免費試用」的 API 產品，讓開發人員對您的 API 每分鐘最多可以呼叫 10 次，而每週最多呼叫 200 次。 您接著會發佈此 API，並測試頻率限制原則。

>[AZURE.NOTE] 如果您已經安裝產品設定，想要用於本教學課程，您可以跳至 [設定呼叫頻率限制和配額原則][] ，並從該處使用您的產品來免費試用產品取代遵循本教學課程。

## <a name="create-product"> </a>若要建立產品

在本步驟中，您將建立不需核准訂閱的免費試用產品。

若要開始使用，請按一下 [ **管理** 在 Azure 傳統 API 管理服務。 這會帶您前往 API 管理發行者入口網站。

![發佈者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

按一下 [ **產品** 中 **API 管理** 左邊顯示的功能表 **產品** 頁面。

![Add product][api-management-add-product]

按一下 [ **加入產品** 顯示 **加入新的產品** 對話方塊。

![Add new product][api-management-new-product-window]

在 **標題** 方塊中，輸入 **免費試用版**。

在 **描述** 方塊中，輸入下列文字:
 **訂閱者可以執行 10 個呼叫每分鐘最多 200 呼叫週之後存取被拒。**

API 管理中的產品可以是受保護或開放的。 受保護的產品必須先訂閱才能使用。 開放產品不需要訂用帳戶即可使用。 請確認 **需要訂閱** 選取要建立需要訂用帳戶的受保護的產品。 這是預設設定。

如果您希望管理員檢閱並接受或拒絕訂閱嘗試對此產品，請選取 **需核准訂閱**。 如果未選取此核取方塊，將會自動核准訂閱嘗試。 在此範例中會自動核准訂閱，因此請勿選取此方塊。

若要允許開發人員帳戶多次訂閱新產品，請選取 **允許多項同時訂閱** 核取方塊。 本教學課程不會使用多項同時訂閱，所以維持未核取即可。

輸入所有值之後，請按一下 **儲存** 來建立產品。

![Product added][api-management-product-added]

根據預設，新產品使用者都看得到在 **管理員** 群組。 我們即將加入 **開發人員** 群組。 按一下 [ **免費試用版**, ，然後按一下 [ **可視性** ] 索引標籤。

>在 API 管理中，群組的作用是管理產品對於開發人員的可見度。 產品會將可見度授與群組，而開發人員可檢視並訂閱其所屬群組可見的產品。 如需詳細資訊，請參閱 [如何建立和使用 Azure API 管理中的群組][]。

![Add developers group][api-management-add-developers-group]

選取 **開發人員** 核取方塊，，然後按一下 [ **儲存**。

## <a name="add-api"> </a>若要將 API 加入產品

在教學課程的這個步驟中，我們會將 Echo API 加入至新的「免費試用」產品。

>每個 API 管理服務執行個體隨附預先設定的範例 Echo API，可供您試驗與了解 API 管理。 如需詳細資訊，請參閱 [開始使用 Azure API 管理][]。

按一下 [ **產品** 從 **API 管理** 左邊的功能表，然後按一下 **免費試用版** 來設定產品。

![Configure product][api-management-configure-product]

按一下 [ **加入 API 至產品**。

![Add API to product][api-management-add-api]

選取 **Echo API**, ，然後按一下 [ **儲存**。

![Add Echo API][api-management-add-echo-api]

## <a name="policies"> </a>若要設定呼叫費率限制和配額原則

費率限制和配額是在原則編輯器中設定。 按一下 [ **原則** 下 **API 管理** 左邊功能表。 在 **產品** 清單中，按一下 **免費試用版**。

![Product policy][api-management-product-policy]

按一下 [ **新增原則** 來匯入原則範本，並開始建立費率限制和配額原則。

![Add policy][api-management-add-policy]

若要插入原則，將游標放到 [ **輸入** 或 **輸出** 原則範本的區段。 費率限制和配額原則為輸入原則，因此將游標放置在輸入元素中。

![Policy editor][api-management-policy-editor-inbound]

本教學課程中，我們要加入的兩個原則 [限制呼叫費率][] 和 [設定使用量配額][] 原則。

![Policy statements][api-management-limit-policies]

游標停在之後 **輸入** 原則項目，按一下旁邊的箭號 **限制呼叫費率** 來插入其原則範本。

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**限制呼叫費率** 可用在產品層級，而且也可用在 API 和個別作業名稱層級。 在本教學課程中，會使用僅產品層級原則，請刪除 **api** 和 **作業** 項目從 **速率限制** 項目，因此只有外部 **速率限制** 項目會維持，如下列範例所示。

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

在免費試用產品中，允許的呼叫費率為每分鐘 10 個呼叫，因此請輸入 **10** 做為值 **呼叫** 屬性，以及 **60** 的 **更新期間** 屬性。

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

若要設定 **設定使用量配額** 原則，游標放在正下方新增 **速率限制** 內的項目 **輸入** 項目，然後按一下左側的箭號 **設定使用量配額**。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

此原則也意欲用於產品層級，因為刪除 **api** 和 **作業** 名稱元素，如下列範例所示。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

配額可以基於每一間隔的呼叫數、頻寬或兩者。 在本教學課程中，我們不會節流根據的頻寬，因此請刪除 **頻寬** 屬性。

    <quota calls="number" renewal-period="seconds">
    </quota>

在免費試用產品中，配額為每週 200 個呼叫。 指定 **200** 做為值 **呼叫** 屬性，然後再指定 **604800** 做為值 **更新期間** 屬性。

    <quota calls="200" renewal-period="604800">
    </quota>

>原則間隔是依秒來指定。 若要計算一週的間隔，您可以將天數 (7) x 一天的小時數 (24) x 每小時的分鐘數 (60) x 每分鐘的秒數 (60)：7 * 24 * 60 * 60 = 604800。

完成設定原則之後，應該符合以下範例。

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

設定需要的原則之後，請按一下 **儲存**。

![Save policy][api-management-policy-save]

## <a name="publish-product"> </a> 若要發行產品

現在已加入 API，也已設定原則，產品必須發佈才能供開發人員使用。 按一下 [ **產品** 從 **API 管理** 左邊的功能表，然後按一下 **免費試用版** 來設定產品。

![Configure product][api-management-configure-product]

按一下 [ **發行**, ，然後按一下 [ **是，將它發行** 確認。

![Publish product][api-management-publish-product]

## <a name="subscribe-account"> </a>若要訂閱產品的開發人員帳戶

現在已發佈產品，產品即可供開發人員訂閱和使用。

>API 管理的管理員執行個體會自動訂閱每個產品。 在這個教學課程步驟中，我們會將其中一個非系統管理員開發人員帳戶訂閱「免費試用」產品。 如果您的開發人員帳戶隸屬於系統管理員角色，即使您已訂閱，也可以遵循此步驟。

按一下 [ **使用者** 上 **API 管理** 左邊的功能表，然後按一下您的開發人員帳戶的名稱。 在此範例中，我們會使用 **Clayton Gragg** 開發人員帳戶。

![Configure developer][api-management-configure-developer]

按一下 [ **新增訂閱**。

![Add subscription][api-management-add-subscription-menu]

選取 **免費試用版**, ，然後按一下 [ **訂閱**。

![Add subscription][api-management-add-subscription]

>[AZURE.NOTE] 在本教學課程中，不會啟用多項同時訂閱的免費試用產品。 如果有啟用，則系統會提示您為訂閱項命名，如以下範例所示。

![Add subscription][api-management-add-subscription-multiple]

按一下後 **訂閱**, ，產品會出現在 **訂閱** 使用者清單。

![Subscription added][api-management-subscription-added]

## <a name="test-rate-limit"> </a>若要呼叫作業並測試費率限制

現在免費試用產品已設定和發佈，我們可以呼叫一些作業並測試費率限制原則。
切換到開發人員入口網站中，依序按一下 **開發人員入口網站** 右上方功能表中。

![開發人員入口網站][api-management-developer-portal-menu]

按一下 [ **Api** 中最上層功能表，然後按一下 **Echo API**。

![開發人員入口網站][api-management-developer-portal-api-menu]

按一下 [ **GET Resource**, ，然後按一下 [ **開啟主控台**。

![Open console][api-management-open-console]

保留預設的參數值，然後選取「免費試用」產品的訂用帳戶金鑰。

![Subscription key][api-management-select-key]

>[AZURE.NOTE] 如果您有多個訂閱，務必選取的索引鍵 **免費試用版**, ，或否則在先前步驟中所設定的原則將不會生效。

按一下 [ **HTTP Get**, ，然後檢視回應。 請注意 **回應狀態** 的 **200 確定**。

![Operation results][api-management-http-get-results]

按一下 [ **HTTP Get** 速率大於每分鐘 10 個呼叫的費率限制原則。 超出費率限制原則之後，回應狀態 **429 過多要求** 傳回。

![Operation results][api-management-http-get-429]

 **回應標頭** 和 **回應內容** 區域指出剩餘的間隔時間，再重試會成功。

每分鐘 10 次呼叫的頻率限制原則生效時，在超出頻率限制之前，從最初成功呼叫產品 10 次後需要經過 60 秒，否則後續的呼叫會失敗。 在此範例中，剩餘的間隔時間為 43 秒。

## <a name="next-steps"> </a>後續步驟

-   查看其他主題中 [開始使用進階 API 組態][] 教學課程。
-   請觀看以下影片中設定頻率限制和配額的示範。

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit
[Get started with advanced API configuration]: api-management-get-started-advanced.md

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

