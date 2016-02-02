<properties 
    pageTitle="如何使用 SendGrid 電子郵件服務 (Node.js) | Microsoft Azure" 
    description="了解如何在 Azure 使用 SendGrid 電子郵件服務傳送電子郵件。程式碼範例以 Node.js API 撰寫。" 
    services="" 
    documentationCenter="nodejs" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/31/2015" 
    ms.author="erikre"/>

# 如何使用 SendGrid 透過 Node.js 傳送電子郵件

本指南示範如何使用 SendGrid 電子郵件服務在 Azure 上
執行常見的程式設計工作。 相關範例是以
Node.js API 撰寫。 涵蓋的案例包括 **建構電子郵件**,，
**傳送電子郵件**, ，**新增附件**, ，**使用篩選器**, ，並
**更新屬性**。 如需有關 SendGrid 及傳送電子郵件的詳細資訊，
電子郵件，請參閱 [下一步](#next-steps) 一節。

## 什麼是 SendGrid 電子郵件服務？

SendGrid 是 [雲端架構電子郵件服務]，能提供可靠
[交易式電子郵件傳遞] 擴充性和即時分析，以及有彈性的 Api
讓使用者輕鬆進行自訂整合。 常見的 SendGrid 使用案例
包括：

-   自動傳送回條給客戶
-   管理為客戶傳送電子傳單和特別優惠的
    通訊群組清單
-   收集封鎖的電子郵件、客戶的回應情形等項目的
    即時度量
-   產生報表，協助找出趨勢
-   轉寄客戶查詢
-   透過電子郵件從您的應用程式傳送通知

如需詳細資訊，請參閱 [https://sendgrid.com](https://sendgrid.com)。

## 建立 SendGrid 帳戶

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## 參考 SendGrid Node.js 模組

適用於 Node.js 的 SendGrid 模組可透過節點
封裝管理員 (npm) 來安裝 (使用下列命令)：

    npm install sendgrid

安裝之後，您可以使用下列程式碼要求您
應用程式中的模組：

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

SendGrid 模組會匯出 **SendGrid** 和 **Email** 函數。
**SendGrid** 負責透過 Web API 傳送電子郵件 
雖然 **電子郵件** 封裝電子郵件訊息。

## 如何：建立電子郵件

要使用 SendGrid 模組建立電子郵件，首先必須
使用電子郵件函數建立電子郵件訊息，然後使用 SendGrid 函數加以傳送
。 以下是使用 Email 函數建立新郵件
的範例：

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

您也可以設定 html 屬性，為支援 HTML 訊息的用戶端指定
一個 HTML 訊息。 例如：

    html: This is a sample <b>HTML<b> email message.

設定 text 和 html 屬性，可為
無法支援 HTML 訊息的用戶端提供文字內容的非失誤性後援。

如需有關 Email 函數所支援之所有屬性的詳細資訊，
請參閱 [sendgrid-nodejs []][]。

## 如何：傳送電子郵件

使用電子郵件函數建立電子郵件訊息後，您可以
使用 SendGrid 所提供的 Web API 加以傳送。

### Web API

    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [AZURE.NOTE] 雖然上述範例顯示傳入 email 物件和
callback 函數，您也可以直接叫用 send
函數，方法是直接指定電子郵件屬性。 例如：  
>
>`````
sendgrid.send({
    若要: 'john@contoso.com'，
    從: 'anna@contoso.com'，
    主旨: 測試郵件，
    文字: 「 This is 範例電子郵件訊息 」。
});
`````

## 如何：新增附件

您可以透過在
在路徑 **檔案** 屬性。 下列範例示範如何
傳送附件：

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [AZURE.NOTE] 使用 **files** 屬性時，必須要能夠透過
透過 [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile)。 如果您想要附加的檔案裝載於 Azure 儲存體中 (例如 Blob 容器中)，您就必須先將該檔案複製到本機儲存體或 Azure 磁碟機，才能使用 **files** 屬性以附件形式傳送它。

## 如何：使用篩選器來啟用頁尾和追蹤

SendGrid 提供了運用「篩選器」的其他電子郵件
篩選。 這些設定可以新增至電子郵件訊息，
以啟用特定功能，例如啟用追蹤、Google
分析、訂閱追蹤等。 如需完整的篩選器清單，
請參閱 [[篩選器設定]][]。

您可以使用 **filters** 屬性在訊息套用篩選器。
每個篩選器都是由包含篩選器特定設定的雜湊來指定。
下列範例示範頁尾和點選追蹤篩選器：

### 頁尾

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });
    
    sendgrid.send(email);

### 點選追蹤

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });
    
    sendgrid.send(email);

## 如何：更新電子郵件屬性

某些電子郵件屬性將會覆寫使用 **設定 * 屬性 *** 或
附加使用 **加入 * 屬性 ***。 例如，您可以新增其他
收件者 (使用下列方式)

    email.addTo('jeff@contoso.com');

或使用下列方式設定篩選器：

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

如需詳細資訊，請參閱 [sendgrid-nodejs []][]。

## 如何：使用其他 SendGrid 服務

SendGrid 提供網頁式 API，可讓您透過 Azure 應用程式使用其他
SendGrid 功能。 如需完整的
詳細資訊，請參閱 [SendGrid API 文件 []][]。

## 後續步驟

了解 SendGrid 電子郵件服務的基本概念後，請參考
下列連結以取得更多資訊。

-   SendGrid Node.js 模組儲存機制: [sendgrid-nodejs []][]
-   SendGrid API 文件：
    <https://sendgrid.com/docs>
-   Azure 客戶的 SendGrid 特別優惠：
    [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html 
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs 
[filter settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html 
[sendgrid api documentation]: https://sendgrid.com/docs 
[cloud-based email service]: https://sendgrid.com/email-solutions 
[transactional email delivery]: https://sendgrid.com/transactional-email 

