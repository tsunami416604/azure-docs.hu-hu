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

#如何偵錯 SAML 型單一登入 Azure Active Directory 中的應用程式

當偵錯 SAML 型應用程式整合，經常是很好用工具 [Fiddler](http://www.telerik.com/fiddler) SAML 要求、 SAML 回應，以及實際的 SAML 權杖發行給應用程式。 您可以藉由檢查 SAML 權杖，確保所有必要的屬性、SAML 主體中的使用者名稱和簽發者 URI 都能如預期通過。

![][1]

來自 Azure AD，其中包含 SAML 權杖的回應是通常發生於 HTTP 302 重新導向之後，從 https://login.windows.net，並傳送至已設定 **回覆 URL** 應用程式。 
 
您可以選取這一行，然後選取檢視的 SAML 語彙基元 **偵測器 > WebForms** 右邊窗格中的索引標籤。 從該處，以滑鼠右鍵按一下 **SAMLResponse** 值，然後選取 **傳送至 TextWizard**。 然後選取 **從 Base64** 從 **轉換** 解碼語彙基元，並查看其內容功能表。
 
**請注意**︰ 若要查看此 HTTP 要求的內容，Fiddler 可能會提示您設定的 HTTPS 流量，您必須進行解密。

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png

