<properties
    pageTitle="在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告 | Microsoft Azure"
    description="了解如何在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/18/2015"
    ms.author="asmalser"/>

#在 Azure Active Directory 中為預先整合的應用程式自訂在 SAML 權杖中發出的宣告

目前 Azure Active Directory 支援 Azure AD 應用程式庫中數千個預先整合的應用程式，有 150 個以上的應用程式支援使用 SAML 2.0 通訊協定的單一登入。 當使用者使用 SAML 透過 Azure AD 向應用程式驗證時，Azure AD 會傳送權杖給應用程式 (透過 HTTP 302 重新導向)，應用程式接著會驗證並使用該權杖將使用者登入，而非提示使用者輸入使用者名稱和密碼。 這些 SAML 權杖包含關於使用者的資訊片段 (稱為「宣告」)。

在身分識別交談中，「宣告」是身分識別提供者在為使用者發出的權杖中關於使用者說明的資訊。 在 [SAML 權杖](http://en.wikipedia.org/wiki/SAML_2.0), 、 這項資料通常會包含在 SAML 屬性陳述式，以及使用者的唯一識別碼通常表示在 SAML 主體。

根據預設，Azure AD 會發出 SAML 權杖給您的應用程式，其包含一個 NameIdentifier 宣告，具有使用者在 Azure AD 中的使用者名稱的值 (此值可唯一識別使用者)。 SAML 權杖也包含額外的宣告，其包含使用者的電子郵件地址、名字和姓氏。

若要檢視或編輯應用程式所發出 SAML 權杖中的宣告，請開啟應用程式記錄在 Azure 管理入口網站，然後選取 **屬性** 下應用程式] 索引標籤。

![][1]

有兩個可能的原因，您可能需要編輯 SAML 權杖中發出的宣告 ︰
已寫入 • 應用程式需要一組不同的宣告 Uri 或宣告的值 
已將 •Your 應用程式部署需要 NameIdentifier 宣告，是儲存在 Azure Active Directory 中的使用者名稱 （也稱為使用者主體名稱） 以外的方式。 

您可以編輯任一個預設的宣告值，方法是選取每當您的滑鼠游標移到 SAML 權杖屬性表格的其中一個資料列上方時會顯示於右側的鉛筆形狀圖示。 您也可以移除使用 （非 NameIdentifier) 宣告 **X** 圖示，並加入新的宣告使用 **加入使用者屬性** ] 按鈕。

##編輯 NameIdentifier 宣告

要解決使用不同的使用者名稱部署應用程式的問題，請按一下 NameIdentifier 宣告旁的鉛筆圖示。 這會提供包含數個不同選項的對話方塊：

![][2]

在 **屬性值** 功能表上，選取 **user.mail** 設在目錄中，使用者的電子郵件地址，或選取的 NameIdentifier 宣告 **user.onpremisessamaccountname** 設為使用者的 SAM 帳戶名稱已從內部部署上的 Azure AD 已同步處理。 

您也可以使用特殊的 ExtractMailPrefix() 函式來移除導致通過 (例如 「 joesmith 」 而不是 joesmith@contoso.com) 的使用者名稱的第一個部分的使用者主體名稱或電子郵件地址的網域尾碼。

![][3]

##新增宣告

加入新的宣告時，您可以指定屬性名稱 (不會嚴格需要按照 SAML 規格的 URI 模式)，以及將該值設為儲存在目錄中的任何使用者屬性。

![][4]

比方說，如果您需要傳送做為宣告其組織內的使用者所屬的部門 （例如銷售），則您可以輸入任何宣告值是預期應用程式，然後選取 **user.department** 做為值。

如果指定的使用者沒有針對選取的屬性儲存的值，則該宣告不會發出在權杖中。

**注意 ︰**  **user.onpremisesecurityidentifier** 和 **user.onpremisesamaccountname** 同步處理使用者資料從內部 Active Directory 使用 AAD Connect 工具的最新的預覽時，才支援。 您可以在下面的連結下載 Connect 工具預覽版：  

http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=53949
    
<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ./media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ./media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ./media/active-directory-saml-claims-customization/claimscustomization4.png


