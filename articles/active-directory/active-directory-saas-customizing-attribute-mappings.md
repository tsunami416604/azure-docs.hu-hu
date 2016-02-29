<properties
    pageTitle="自訂屬性對應 | Microsoft Azure"
    description="了解 Azure Active Directory 中 SaaS 應用程式有哪些屬性對應，以及如何修改屬性對應來應付業務需求。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2015"
    ms.author="markusvi"/>


# 自訂屬性對應


Microsoft Azure AD 支援使用者佈建到例如 Salesforce、Google Apps 等等的協力廠商 SaaS 應用程式。 如果您啟用了協力廠商 SaaS 應用程式的使用者佈建，Azure 管理入口網站會以一種稱為「屬性對應」的組態方式控制其屬性值。

在 Azure AD 使用者物件和每個 SaaS app 的使用者物件之間，有一組預先設定的屬性對應。 有些 app 則管理其他類型的物件，例如群組或連絡人。 <br> 
 您可以根據企業需求自訂預設的屬性對應。 這表示您可以變更或刪除現有的屬性對應，或建立新的屬性對應。

在 Azure AD 入口網站中，您可以在 SaaS 應用程式工具列中按一下 [屬性] 存取這項功能。

> [AZURE.NOTE]  **屬性** 連結，才可以使用，如果您有個 SaaS 應用程式啟用使用者佈建。 


![] Salesforce[] 1 


當您按一下工具列的 [屬性] 時，就會出現目前為 SaaS 應用程式設定的對應清單。

以下螢幕擷取畫面顯示上述的範例︰



![] Salesforce[] 2  


在上述範例中，您可以看到， **firstName** 在 Salesforce 中的受管理物件的屬性會填入 **givenName** de Azure AD 物件。

如果您想要自訂屬性對應，或如果您想要將自訂設定還原為預設組態，可以依序按一下應用程式底部工具列中的相關按鈕。


![] Salesforce[] 3  


SaaS 應用程式需要有幾個屬性對應才能正確運作。 
 在屬性表中，有相關的屬性對應 **是** 值為 **需要** 屬性。 如果某個屬性對應是必要的，您就無法刪除它。 在此情況下， **刪除** 功能就無法使用。

若要修改現有的屬性對應，只要選取對應，以及 [ **編輯**。 此時會顯示一個對話方塊頁面，讓您修改選取的屬性對應。


![編輯屬性對應][] 4  



## 了解屬性對應類型


有了屬性對應，您就可以控制屬性在協力廠商 SaaS 應用程式中填入的方式。 
支援四種不同的對應類型：

- **直接** – 目標屬性在 Azure AD 時填入連結物件的屬性值。


- **常數** – 目標屬性會填入您所指定的特定字串。


- **運算式** -目標屬性根據類似指令碼運算式的結果填入。 
如需詳細資訊，請參閱 [屬性對應，Azure Active Directory 中撰寫運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)。


- **無** -目標屬性保留未修改。 不過，如果目標屬性是空的，就會填入您所指定的預設值。



除了這四個基本屬性對應類型，自訂屬性對應還支援的概念 **預設** 值指派。 預設值指派可確保當 Azure AD 中和目標物件都沒有值時，目標屬性會填入某個值。

Microsoft Azure AD 提供非常有效率的同步處理程序實作。 
 在初始化環境中，同步處理期間只會處理需要更新的物件。 
 更新屬性對應會影響同步處理期間的效能。 
 這是因為更新屬性對應組態需要重新評估所有受管理的物件。 
 因此，建議您最好不要經常變更屬性對應。



[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png

