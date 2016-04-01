<properties 
    pageTitle="如何使用 SendGrid 電子郵件服務 (Node.js) | Microsoft Azure" 
    description="了解如何在 Azure 使用 SendGrid 電子郵件服務傳送電子郵件。 程式碼範例以 Node.js API 撰寫。" 
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
**正在更新內容**。 如需有關 SendGrid 及傳送電子郵件的詳細資訊，
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

SendGrid 模組會匯出 **SendGrid** 和 **電子郵件** 函式。
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
請參閱 [sendgrid-nodejs] []。

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
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
`````

## How to: Add an Attachment

Attachments can be added to a message by specifying the file name(s) and
path(s) in the **files** property. The following example demonstrates
sending an attachment:

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

> [AZURE.NOTE] When using the **files** property, the file must be accessible
through [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). If the file you wish to attach is hosted in Azure Storage, such as in a Blob container, you must first copy the file to local storage or to an Azure drive before it can be sent as an attachment using the **files** property.

## How to: Use Filters to Enable Footers and Tracking

SendGrid provides additional email functionality through the use of
filters. These are settings that can be added to an email message to
enable specific functionality such as enabling click tracking, Google
analytics, subscription tracking, and so on. For a full list of filters,
see [Filter Settings][].

Filters can be applied to a message by using the **filters** property.
Each filter is specified by a hash containing filter-specific settings.
The following examples demonstrate the footer and click tracking filters:

### Footer

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

### Click Tracking

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

## How to: Update Email Properties

Some email properties can be overwritten using **set*Property*** or
appended using **add*Property***. For example, you can add additional
recipients by using

    email.addTo('jeff@contoso.com');

or set a filter by using

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

For more information, see [sendgrid-nodejs][].

## How to: Use Additional SendGrid Services

SendGrid offers web-based APIs that you can use to leverage additional
SendGrid functionality from your Azure application. For full
details, see the [SendGrid API documentation][].

## Next Steps

Now that you've learned the basics of the SendGrid Email service, follow
these links to learn more.

-   SendGrid Node.js module repository: [sendgrid-nodejs][]
-   SendGrid API documentation:
    <https://sendgrid.com/docs>
-   SendGrid special offer for Azure customers:
    [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)
  [special offer]: https://sendgrid.com/windowsazure.html
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email


