<properties
   pageTitle="Azure Active Directory B2B 共同作業預覽版本的詳細逐步解說 | Microsoft Azure"
   description="Azure Active Directory B2B 可讓企業合作夥伴選擇性地存取您的公司應用程式，以支援公司間的關係"
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Azure Active Directory (Azure AD) B2B 共同作業預覽版本的詳細逐步解說
本逐步解說概述如何使用 Azure AD B2B 共同作業。 身為 Contoso 的 IT 系統管理員，我們想要與三家合作夥伴公司的員工共用應用程式。 所有合作夥伴公司都不需要有 Azure AD。

- 來自簡單合作夥伴組織的 Alice
- 來自中型合作夥伴組織的 Bob 需要存取一組應用程式
- 來自複雜合作夥伴組織的 Carol 需要存取一組應用程式，以及 Contoso 群組的成員資格

將邀請送給合作夥伴使用者之後，我們可以在 Azure AD 中設定他們，以透過 Azure 入口網站授與應用程式的存取權和群組的成員資格。 我們就從新增 Alice 著手。

## 將 Alice 新增至 Contoso 的目錄
1. 建立.csv 檔案包含標頭所示，填入只有 Alice **電子郵件**, ，**DisplayName**, ，和 **InviteContactUsUrl**。 **DisplayName** 時的名稱會出現在邀請，並且也會出現在 Contoso 的 Azure AD 目錄的名稱。 **InviteContactUsUrl** 是連絡 Contoso 的 Alice 的方法。 在以下範例中，其指定 Contoso 的 LinkedIn 設定檔。 .csv 檔案的第一列中一定要有相同順序的標籤，其拼法如同所示。 請參閱以下 「 CSV 格式一節。  
![林麗莉的範例 CSV 檔案](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. 在 Azure 入口網站中，將使用者新增到 Contoso 目錄中 (Active Directory > Contoso > 使用者 > 新增使用者)。 在 [使用者類型] 下拉式清單中，選取 [合作夥伴公司中的使用者]。 上傳 .csv 檔案。 請確定此.csv 檔案已上傳之前關閉。  
![Alice 的 CSV 檔案上傳](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice 現在被以外部使用者 Contoso 的 Azure AD 目錄中。  
![Alice 已列在 Azure AD 中](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. 從 Alice 的觀點來看，她將會收到下列電子郵件。  
![Alice 的邀請電子郵件](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice 按一下連結，系統會提示她接受邀請並使用其工作認證進行登入。 如果 Alice 不是 Azure AD 目錄中，Alice 會提示您登入。  
![在邀請 Alice 之後註冊](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice 會重新導向至應用程式存取面板時，空白，直到她授與存取應用程式。  
![Alice 的存取面板](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

這是最簡單的 B2B 共同作業形式。 身為 Contoso 的 Azure AD 目錄中的使用者，Alice 可透過 Azure 入口網站獲得應用程式和群組的存取權。 我們現在就來新增 Bob，他需要 Moodle 和 Salesforce 應用程式的存取權。

## 將 Bob 新增至 Contoso 的目錄並授與應用程式的存取權
1. 使用已安裝 Azure AD 模組的 Windows PowerShell 來尋找 Moodle 和 Salesforce 的應用程式識別碼。 可以使用此指令程式擷取 Id: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` 這時會出現在 Contoso 和其 AppPrincialIds 中所有可用的應用程式的清單。  
![Bob 擷取識別碼](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. 建立.csv 檔案中，填入 Bob 的電子郵件、 DisplayName、 **InviteAppID**, ，**InviteAppResources**, ，和 InviteContactUsUrl。 **InviteAppResources** 是填入 Moodle 的 AppPrincipalIds 和 Salesforce 找到從 PowerShell 中，以空格分隔。 這是以綠框和藍框醒目提示的 ID，其對應於上面的 PowerShell 螢幕擷取畫面。 **InviteAppId** 會填入相同 AppPrincipalId 的 Moodle 來標示電子郵件，並登入頁面。  
![Bob 的範例 CSV 檔案](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. 如同 Alice 所進行的步驟，透過 Azure 入口網站上傳 .csv 檔案。 Bob 現在是 Contoso 的 Azure AD 目錄中的外部使用者。

4. Bob 將會收到下列電子郵件。  
![Bob 的邀請電子郵件](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Bob 按一下連結，系統會提示他接受邀請。 登入之後，他會被導向到存取面板，並已可使用 Moodle 和 Salesforce。  
![Bob 的存取面板](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

我們接下來會新增 Carol，她需要應用程式的存取權以及 Contoso 目錄中的群組成員資格。

## 將 Carol 加入至 Contoso 的目錄，授與應用程式的存取權，並提供群組成員資格

1. 使用已安裝 Azure AD 模組的 Windows PowerShell 來尋找 Contoso 內的應用程式識別碼和群組識別碼。
 - 如同對待 Bob 一樣，使用 `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Cmdlet 擷取 AppPrincipalId。
 - 使用 `Get-MsolGroup | fl DisplayName, ObjectId` Cmdlet 擷取群組的 ObjectId。這會顯示 Contoso 中的所有群組清單及其 ObjectId。 群組識別碼也可以擷取做為 Azure 入口網站中的群組中的 [內容] 索引標籤中的物件識別碼。  
![擷取收件者識別碼和群組](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. 建立.csv 檔案中，填入收件者的電子郵件、 DisplayName、 InviteAppID、 InviteAppResources， **InviteGroupResources**, ，和 InviteContactUsUrl。 **InviteGroupResources** MyGroup1 和外部，以空格分隔的群組的 objectid 將要會填入。  
![Carol 的範例 CSV 檔案](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. 透過 Azure 入口網站上傳 .csv 檔案。

4. Carol 是 Contoso 目錄中的使用者同時也是 MyGroup1 及外部，群組的成員在 Azure 入口網站中所示。  
![Carol 已列在 Azure AD 的群組中](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carol 將會收到一封電子郵件，其中包含可接受邀請的連結。 登入之後，她將會被重新導向至 [應用程式存取面板]，以獲得 Moodle 和 Salesforce 的存取權。  

以上就是如何在 Azure AD B2B 共同作業中新增來自合作夥伴企業的使用者。 本逐步解說示範新增 Alice、 Bob 和 Carol 中三個個別的.csv 檔案，但事實上才能新增一起起見到單一的.csv 檔案中。  
![Alice、Bob 和 Carol 的範例 CSV 檔案](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## 相關文章
請瀏覽有關 Azure AD B2B 共同作業的其他文章：

- [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [運作方式](active-directory-b2b-how-it-works.md)
- [CSV 檔案格式參考](active-directory-b2b-references-csv-file-format.md)
- [外部使用者權杖格式](active-directory-b2b-references-external-user-token-format.md)
- [外部使用者物件屬性變更](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [目前的預覽版本限制](active-directory-b2b-current-preview-limitations.md)


