<properties 
    pageTitle="如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式 | Microsoft Azure" 
    description="了解如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式 " 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/18/2015" 
    ms.author="asmalser" />


# 如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式

當偵錯 SAML 型應用程式整合，經常是很好用工具 [Fiddler](http://www.telerik.com/fiddler) SAML 要求、 SAML 回應，以及實際的 SAML 權杖發行給應用程式。 您可以藉由檢查 SAML 權杖，確保所有必要的屬性、SAML 主體中的使用者名稱和簽發者 URI 都能如預期通過。

![][1]

來自 Azure AD，其中包含 SAML 權杖的回應是通常發生於 HTTP 302 重新導向之後，從 https://login.windows.net，並傳送至已設定 **回覆 URL** 應用程式。

您可以選取這一行，然後在右窗格中選取 [偵測器] > [WebForms]****，檢視 SAML 權杖。 在這裡以滑鼠右鍵按一下 [SAMLResponse]**** 值並選取 [傳送至 TextWizard]****。 然後選取 [轉換]**** 功能表的 [從 Base64]****，解碼權杖並查看其內容。

**注意**：為查看這個 HTTP 要求的內容，Fiddler 可能會提示您設定解密 HTTPS 流量，請務必這麼做。



[1]: ./media/active-directory-saml-debugging/fiddler.png 

