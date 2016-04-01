<properties
    pageTitle="在 Azure API 管理中自訂開發人員入口網站 | Microsoft Azure"
    description="了解如何在 Azure API 管理中自訂開發人員入口網站。"
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

# 在 Azure API 管理中自訂開發人員入口網站

本指南示範如何在 Azure API 管理中修改開發人員入口網站的外觀及操作，以便與您的品牌保持一致。

## <a name="change-page-headers"> </a>變更頁首的文字或標誌

入口網站自訂的一個主要層面，便是將所有頁面頂端的文字以您的公司名稱或標誌取代。

開發人員入口網站內的內容，是透過經 Azure 傳統入口網站存取的發佈者入口網站加以修改。 若要存取 API 發行者入口網站，按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。

![發行者入口網站][api-management-management-console]

開發人員入口網站是以內容管理系統 (CMS) 為基礎。 每個頁面上出現的頁首是特殊類型的內容，也稱為 Widget。 若要編輯該 widget 的內容，請按一下 [ **Widget** 從 **開發人員入口網站** 左邊的功能表，然後選取 **標頭** 從清單中的 widget。

![Widgets header][api-management-widgets-header]

標頭的內容是編輯從 **主體** 欄位。 將文字變更為"Fabrikam Developer Portal"，然後按一下 **儲存** 頁面的底部。

現在，您應該會在開發人員入口網站內的每個頁面上看到新的頁首。

> 若要開啟開發人員入口網站，在發行者入口網站中的，按一下 [ **開發人員入口網站** 頂端列中。

## <a name="change-headers-styling"> </a>變更頁首的樣式

入口網站上任何頁面的色彩、字型、大小、空格和其他樣式相關的元素，都會透過樣式規則加以定義。 若要編輯樣式，請按一下 [ **外觀** 從 **開發人員入口網站** 中發行者入口網站，然後按一下功能表 **開始自訂** 來啟用樣式編輯器。

您的瀏覽器將切換為開發人員入口網站內的隱藏頁面，其中包含內容範例，以及可在網站上任意處使用的所有樣式規則範例。 若要開啟樣式編輯器，請將游標移至頁面最左側的細長灰色垂直線上。 應該會出現編輯器工具列。

![Customization toolbar][api-management-customization-toolbar]

有兩個編輯樣式規則的主要模式- **編輯所有規則** 會顯示一份所有樣式規則的任何地方使用，而 **挑選項目** 可讓您從您所在頁面選取元素，並顯示該元素的樣式。

在本節中，我們只想變更頁首的樣式。 按一下 [ **挑選項目** 選項從樣式編輯器工具列，然後再按一下 **選取自訂的項目**。 現在，當您將滑鼠移至元素上方時，元素會變成反白顯示狀態，以象徵您按一下就會開始編輯的元素樣式。 將滑鼠移至頁首中代表公司名稱的文字上方 (如果您遵循上一節中的指示，即為 "Fabrikam Developer Portal")，然後按一下該文字。 樣式編輯器中會出現一組已具名且分類的樣式規則。

每個規則均代表所選取元素的樣式屬性。 例如，針對以上選取的頁首文字，文字的大小在 @font-size-h1，而替代字型的名稱則在 @headings-font-family。

> 如果您熟悉 [啟動程序][], ，這些規則，實際上是 [變數較少][] 內開發人員入口網站所使用的 bootstrap 佈景主題。

讓我們變更頁首文字的色彩。 選取的項目 **@headings-color** 欄位，然後輸入 **#000000**。 這是黑色的十六進位碼。 執行此動作時，您會看見文字方塊末端出現方形色彩指示器。 如果您按一下此指示器，即會出現色彩選擇器，讓您能夠選擇色彩。

![Color picker][api-management-customization-toolbar-color-picker]

當您完成變更所選元素的樣式時，按一下 [ **預覽變更** 畫面上查看結果。 目前只有管理員能夠看見這些結果。 若要讓所有人都能看見這些變更，按一下 [ **發行** 按鈕樣式編輯器，並確認變更。

![Publish menu][api-management-customization-toolbar-publish-form]

> 若要變更套用至頁面上其他元素的樣式規則，請遵循您用以變更標頭的相同程序。 按一下 [ **挑選項目** 從樣式編輯器中，選取您感興趣，並開始修改畫面上顯示之樣式規則的值的項目。

## <a name="edit-page-contents"> </a>編輯頁面的內容

開發人員入口網站是由自動產生的頁面所組成，例如 API、產品、應用程式、問題，以及手動編寫的內容。 由於它是以內容管理系統為基礎，因此您可以視需要建立這類內容。

若要查看所有現有的清單，內容頁面，請按一下 [ **內容** 從 **開發人員入口網站** 發行者入口網站中的功能表。

![Manage content][api-management-customization-manage-content]

按一下 [ **歡迎** 頁面，即可編輯開發人員入口網站的首頁上顯示的內容。 進行的變更、 預覽如有必要，然後按一下 **立即發行** 使其以供其他人。

> 首頁會使用特殊的版面配置，使其得以在上方顯示橫幅。 橫幅是無法編輯從 **內容** 一節。 若要編輯此橫幅，請按一下 [ **Widget** 從 **開發人員入口網站** 功能表上，選取 **首頁** 從 **目前圖層** 下拉式清單中，然後再開啟 **橫幅** 項目底下 **精選區段**。 如同其他任何頁面一般，此 Widget 的內容可供編輯。

## <a name="next-steps"> </a>後續步驟

-   查看其他主題中 [開始使用進階 API 組態][] 教學課程。

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[Get started with advanced API configuration]: api-management-get-started-advanced.md
[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/


