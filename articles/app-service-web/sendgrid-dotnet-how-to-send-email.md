<properties 
    pageTitle="如何使用 SendGrid 電子郵件服務 (.NET) (.NET) | Microsoft Azure" 
    description="了解如何在 Azure 使用 SendGrid 電子郵件服務傳送電子郵件。程式碼範例是以 C# 撰寫並使用 .NET API。" 
    services="app-service\web" 
    documentationCenter=".net" 
    authors="thinkingserious" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/12/2015" 
    ms.author="team-pi@sendgrid.com"/>






# 如何在 Azure 上使用 SendGrid 傳送電子郵件

## 概觀

本指南示範如何使用 SendGrid 電子郵件服務在 Azure 上
執行常見的程式設計工作。
並使用.NET API。

如需有關 SendGrid 及傳送電子郵件的詳細資訊，請參閱


## 什麼是 SendGrid 電子郵件服務？



讓使用者輕鬆進行自訂整合。 常見的 SendGrid 使用案例
包括：

-   自動傳送回條給客戶。
-   管理通訊群組清單，以便將每個月將電子傳單和特別優惠
    傳送給客戶。
-   
    即時度量。
-   產生報表，協助找出趨勢。
-   轉寄客戶查詢。
-   處理內送電子郵件。



## 建立 SendGrid 帳戶

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## 參考 SendGrid .NET 類別庫


NuGet 是


> [AZURE.NOTE] 

若要在應用程式中安裝 SendGrid NuGet 封裝，請執行下列動作：

1.  建立新專案。

    ![建立新專案][create-new-project]

2.  選取範本。

    ![選取範本][select-a-template]

3.  


4.  
    。

    ![SendGrid NuGet 封裝][sendgrid-nuget-package]

5.  
    對話方塊。

SendGrid 的 .NET 類別庫稱為 **SendGridMail**。 它包含
下列命名空間：

-   **SendGridMail** 可供建立和處理電子郵件項目。
-   


將下列程式碼命名空間宣告加入至任何 C\# 檔案的頂端





    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## 如何：建立電子郵件







下列範例示範如何建立完全填入的電子郵件
物件：

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();
    
    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");
    
    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };
    
    myMessage.AddTo(recipients);
    
    myMessage.Subject = "Testing the SendGrid Library";
    
    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

如需 SendGrid 所支援之所有屬性和方法的詳細資訊，


## 如何：傳送電子郵件




傳送電子郵件需要您提供
API 金鑰為慣用方法。




 ![Azure 應用程式設定][azure_app_settings]

 然後，您可以使用下列方式進行存取：

    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

使用認證：

    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";
    
    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

使用 API 金鑰：

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);

下列範例顯示如何使用 Web API 傳送郵件。

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";
    
    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");
    
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);
    
    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);
    
    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## 如何：新增附件







    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";
    
    myMessage.AddAttachment(@"C:\file1.txt");

您也可以從資料的**串流**來新增附件。 您可呼叫與上述相同的 **AddAttachment** 方法來進行此作業，只是要傳入該資料串流以及您要檔案在郵件中顯示的名稱。 在此情況下，您必須新增 System.IO 程式庫。

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";
    
    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

## 如何：使用應用程式來啟用頁尾、追蹤和分析

SendGrid 提供了運用「篩選器」的其他電子郵件
這些設定可以新增至電子郵件訊息，

如需完整的應用程式清單，請參閱






filters:

### 頁尾

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";
    
    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### 點選追蹤

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## 如何：使用其他 SendGrid 服務


SendGrid 功能。 如需完整的


## 後續步驟

了解 SendGrid 電子郵件服務的基本概念後，請參考
下列連結以取得更多資訊。

*   
*   
*   


[next steps]: #next-steps 
[what is the sendgrid email service?]: #whatis 
[create a sendgrid account]: #createaccount 
[reference the sendgrid .net class library]: #reference 
[how to: create an email]: #createemail 
[how to: send an email]: #sendemail 
[how to: add an attachment]: #addattachment 
[how to: use filters to enable footers, tracking, and analytics]: #usefilters 
[how to: use additional sendgrid services]: #useservices 
[special offer]: https://www.sendgrid.com/windowsazure.html 
[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png 
[select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png 
[sendgrid-nuget-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png 
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png 
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp 
[smtp vs. web api]: https://sendgrid.com/docs/Integrate/index.html 
[app settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html 
[sendgrid api documentation]: https://sendgrid.com/docs 
[cloud-based email service]: https://sendgrid.com/email-solutions 
[transactional email delivery]: https://sendgrid.com/transactional-email 

