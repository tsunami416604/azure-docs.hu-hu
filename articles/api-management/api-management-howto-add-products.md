<properties 
    pageTitle="如何在 Azure API 管理中建立及發行產品" 
    description="了解如何在 Azure API 管理中建立及發行產品。" 
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
    ms.date="12/07/2015" 
    ms.author="sdanie"/>

# 如何在 Azure API 管理中建立及發行產品

在 Azure API 管理中，產品包含一或多個 API，以及使用量配額與使用規定。 發行產品之後，開發人員便可訂閱產品，並開始使用產品的 API。 本主題提供建立產品、加入 API 和發行供開發人員使用的指引。

## <a name="create-product"> </a>建立產品

請在發行者入口網站新增和設定 API 的作業。 若要存取發行者入口網站，按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。

![發行者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

按一下 [ **產品** 在左側以顯示功能表 **產品** 頁面，然後按一下 **加入產品**。

![產品][api-management-products]

![New product][api-management-add-new-product]

輸入的產品的描述性名稱 **名稱** 欄位和產品的描述 **描述** 欄位。

在 API 管理中的產品可以是 **開啟** 或 **受保護**。 受保護產品必須先擁有訂用帳戶才能使用，開放產品則可以使用而不需訂用帳戶。 檢查 **需要訂閱** 建立需要訂用帳戶的受保護的產品。 這是預設設定。

檢查 **需核准訂閱** 如果您希望管理員檢閱並接受或拒絕對此產品的訂閱嘗試。 如果未核取方塊，將會自動核准訂閱嘗試。 如需有關訂閱的詳細資訊，請參閱 [檢視產品的訂閱者][]。

若要允許開發人員帳戶訂閱產品多次，請檢查 **允許多項訂閱** 核取方塊。 如果未核取此方塊，則每個開發人員帳戶只能訂閱產品一次。

![無限制的多項訂閱][api-management-unlimited-multiple-subscriptions]

若要限制多項同時訂閱數目，請檢查 **同時訂閱數目限制** 核取方塊，然後輸入訂閱限制。 在以下範例中，同時訂閱數已限制為每個開發人員帳戶四個。

![四個多項訂閱][api-management-four-multiple-subscriptions]

一旦設定所有新產品的選項，按一下 **儲存** 來建立新的產品。

![產品][api-management-products-page]

>依預設，新的產品不會發行，且只有  **管理員** 群組。

若要設定產品，按一下 [產品名稱中 **產品** ] 索引標籤。

## <a name="add-apis"> </a>將 API 加入至產品

 **產品** 頁面包含四個組態連結 ︰ **摘要**, ，**設定**, ，**可視性**, ，和 **訂閱者**。  **摘要** ] 索引標籤是將 Api 加入和發佈或取消發行產品。

![摘要][api-management-new-product-summary]

發行產品之前，您必須加入一或多個 API。 若要這樣做，請按一下 [ **加入 API 至產品**。

![Add APIs][api-management-add-apis-to-product]

選取想要的 Api，然後按一下 **儲存**。

## <a name="add-description"> </a>將描述性資訊加入至產品

 **設定** ] 索引標籤可讓您提供的產品，例如用途、 它提供存取的 Api 及其他有用的資訊的詳細的資訊。 內容以將會呼叫 API 的開發人員為對象，可使用純文字或 HTML 標記來撰寫。

![Product settings][api-management-product-settings]

檢查 **需要訂閱** 來建立受保護的產品，需要使用，或清除核取方塊，以建立可呼叫但沒有訂閱的開放產品的訂閱。

選取 **需核准訂閱** 如果您想要手動核准所有產品訂閱要求。 依預設會自動同意所有產品訂閱。

若要允許開發人員帳戶訂閱產品多次，請檢查 **允許多項訂閱** 核取方塊，並選擇性地指定限制。 如果未核取此方塊，則每個開發人員帳戶只能訂閱產品一次。

選擇性填寫 **使用條款** 欄位，描述訂閱者必須接受才可使用該產品的產品使用規定。

## <a name="publish-product"> </a>發行產品

產品必須發行，才能呼叫產品中的 API。 在 **摘要** 產品] 索引標籤上，按一下 **發行**, ，然後按一下 [ **是，將它發行** 確認。 私用先前發行的產品，請按一下 **Unpublish**。

![Publish product][api-management-publish-product]

## <a name="make-visible"> </a>讓開發人員看見產品

 **可視性** 索引標籤可讓您選擇哪些角色可開發人員入口網站上看見產品及訂閱產品。

![Product visibility][api-management-product-visiblity]

若要啟用或停用群組中的開發人員看見產品，核取或取消核取群組旁邊的核取方塊，然後按一下 **儲存**。

>如需詳細資訊，請參閱 [如何建立和使用群組來管理在 Azure API 管理開發人員帳戶][]。

## <a name="view-subscribers"> </a>檢視產品的訂閱者

 **「 訂閱者 」** 索引標籤會列出已訂閱產品的開發人員。 按一下開發人員的名稱，即可檢視開發人員的詳細資料和設定。 在此範例中，還沒有開發人員訂閱產品。

![開發人員][api-management-developer-list]

## <a name="next-steps"> </a>後續步驟

加入想要的 API 並發行產品之後，開發人員就可以訂閱產品並開始呼叫 API。 如需示範這些項目，以及進階的產品組態的教學課程，請參閱 [如何建立和設定進階的產品設定，在 Azure API 管理][]。

如需關於使用產品的詳細資訊，請觀看以下影片。

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[How to create and use groups to manage developer accounts in Azure API Management]: api-management-howto-create-groups.md
[How create and configure advanced product settings in Azure API Management]: api-management-howto-product-with-rules.md 

