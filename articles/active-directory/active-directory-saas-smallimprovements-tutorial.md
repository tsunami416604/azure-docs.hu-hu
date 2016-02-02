<properties
    pageTitle="教學課程：Azure Active Directory 與 Small Improvements 整合| Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 Small Improvements 之間的單一登入。"
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/14/2015"
    ms.author="jeedes"/>



# 教學課程：Azure Active Directory 與 Small Improvements 整合



- 您可在 Azure AD 中控制可存取 Small Improvements 的人員
- 您可以讓使用者使用其 Azure AD 帳戶自動登入 Small Improvements (單一登入)
- 您可以在 Azure 傳統入口網站中集中管理您的帳戶



## 必要條件

若要設定 Azure AD 與 Small Improvements 的整合作業，需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Small Improvements 單一登入功能的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 


## 案例描述




1. 從資源庫新增 Small Improvements
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 Small Improvements

若要設定 Small Improvements 與 Azure AD 的整合作業，您需要從資源庫將 Small Improvements 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Small Improvements，請執行下列步驟：**

1. 


2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 

4. 

5. 

6. 

7. 
<br><br>

## 設定並測試 Azure AD 單一登入

本節目標是示範如何根據名為「Britta Simon」的測試使用者，使用 Small Improvements 設定及測試 Azure AD 單一登入功能。

若要讓單一登入作用，Azure AD 必須知道 Small Improvements 與 Azure AD 中互相對應的使用者。
建立此連結關聯性的方法，就是將 Azure AD 中 [使用者名稱]**** 的值，指派為 Small Improvements 中 [Username]**** 的值。

若要使用 Small Improvements 設定及測試 Azure AD 單一登入功能，您需要完成下列建置組塊：

1. 
2. 
4. 
5. 
5. 

### 設定 Azure AD 單一登入

本節目標是在 Azure 傳統入口網站中啟用 Azure AD 單一登入功能，並在您的 Small Improvements 應用程式中設定單一登入功能。



**若要使用 Small Improvements 設定 Azure AD 單一登入功能，請執行下列步驟：**

1. 


2. 


3. 在 [設定 App 設定]**** 對話方塊頁面執行下列步驟：

 > [AZURE.NOTE] 單一登入必須要有這項作業才能運作。

 a.  
 b. 按 [下一步]****。

4. 


 a. 按一下 [下載憑證]****，然後將檔案儲存在您的電腦上。

 b. 按 [下一步]****。

1. 在另一個瀏覽器視窗中，以系統管理員身分登入您的 Small Improvements 公司網站。

1. 


1. 


1. 


 a. 在 Azure 傳統入口網站的 [設定在 Small Improvements 單一登入]**** 對話方塊頁面上，複製 [SAML SSO URL]**** 的值，然後將它貼到 [HTTP 端點]**** 文字方塊中。

 b. 在記事本中開啟您下載的憑證，然後複製憑證內容，再把該內容貼到 [x509 憑證]**** 文字方塊中。

 c. 如果您想讓使用者能夠使用 SSO 和登入表單驗證，請選取 [啟用也能透過登入/密碼來存取]**** 選項。

 d. 在 [SAML 提示]**** 文字方塊中，輸入適當的值來為 SSO 登入按鈕命名。

 e. 按一下 [儲存]****。

6. 


7. 在 [單一登入確認]**** 頁面上，按一下 [完成]****。  
  




### 建立 Azure AD 測試使用者




**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 傳統入口網站**中，按一下左方瀏覽窗格的 [Active Directory]****。


2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 


4. 


5. 在 **告訴我們這位使用者** 對話方塊頁面上，執行下列步驟:


 a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

 b. 在 [使用者名稱]**** 文字方塊中，輸入 **BrittaSimon**。

 c. 按 [下一步]****。

6.  在 **使用者設定檔** 對話方塊頁面上，執行下列步驟:


a. 在 [名字]**** 文字方塊中，輸入 **Britta**。

b. 在 [姓氏]**** 文字方塊中，輸入 **Simon**。

c. 在 [顯示名稱]**** 文字方塊中，輸入 **Britta Simon**。

d. 在 [角色]**** 清單中選取 [使用者]****。
e. 按 [下一步]****。

7. 在 **取得暫時密碼** 對話方塊頁面上，按一下 [ **建立**。


8. 在 **取得暫時密碼** 對話方塊頁面上，執行下列步驟:


 a. 記下 [新密碼]**** 的值。

 b. 按一下 [完成]****。



### 建立 Small Improvements 的測試使用者

本節目標是在 Small Improvements 中建立名為 Britta Simon 的使用者。
您已啟用在 [設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)。


**若要在 Small Improvements 中建立名為 Britta Simon 的使用者，請執行下列步驟：**

1. 以系統管理員身分登入您的 Small Improvements 公司網站。

1. 按一下首頁左側功能表中的 [系統管理]****。

1. 按一下 [ **使用者目錄** 使用者管理] 區段的按鈕。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) <br>

1. <br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) <br>

1. 型別 **名字**, ，**姓氏** 和 **電子郵件地址** 您想要佈建到相關文字方塊之有效使用者。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png) <br>

1. 您也可以選擇在 [傳送通知電子郵件]**** 方塊中，輸入您個人的訊息。 如果您不想傳送通知，則取消選取此核取方塊。

1. 按一下 [建立使用者]****。

### 指派 Azure AD 測試使用者

本節目標是讓 Britta Simon 授與他們存取權的小改良使用 Azure 單一登入。
<br><br>![將使用者指派][200] <br>

**若要將 Britta Simon 指派到 Small Improvements，請執行下列步驟：**

1. 在 Azure 傳統入口網站中，若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。
<br><br>![將使用者指派][201] <br>

2. 在應用程式清單中，選取 **小改良**。


1. 在頂端的功能表中，按一下 [使用者]****。
<br><br>![將使用者指派][203] <br>

1. 在 [使用者] 清單中，選取 [Britta Simon]****。

2. 在底部工具列中，按一下 [ **指派**。
<br><br>![指派給使用者][205]



### 測試單一登入

本節目標是要測試您的 Azure AD 單一登入組態使用存取面板。<br>
當您在存取面板中按一下 [Small Improvements] 磚時，應該會自動登入 Small Improvements 應用程式。


## 其他資源

* [如何使用 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory?](active-directory-appssoaccess-whatis.md)





[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png 
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png 
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png 
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png 
[6]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_05.png 
[10]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_06.png 
[11]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_07.png 
[20]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png 
[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png 
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png 
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png 
[204]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_204.png 
[205]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_205.png 

