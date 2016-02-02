<properties
    pageTitle="教學課程：將 Azure Active Directory 與 Kindling 整合 | Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 Kindling 之間的單一登入。"
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="prasannas"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/01/2015"
    ms.author="jeedes"/>



# 教學課程：Azure Active Directory 與 Kindling 整合

本教學課程的目標在於告訴您如何使用 Azure Active Directory (Azure AD) 整合 Kindling。<br>與 Azure AD 整合 Kindling 為您提供下列優點:

- 您可以在 Azure AD 中管控可存取 Kindling 的人員
- 您可以讓使用者透過其 Azure AD 帳戶自動登入 Kindling (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

如果您想要知道更多詳細與 Azure AD 的 SaaS 應用程式整合，請參閱 [什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## 必要條件

若要設定 Azure AD 與 Kindling 的整合作業，需要下列項目：

- Azure AD 訂用帳戶
- 啟用 Kindling 單一登入功能的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以取得一個月試用 [這裡](https://azure.microsoft.com/pricing/free-trial/)。


## 案例描述

本教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。 <br>
本教學課程中說明的案例包含兩個主要建置區塊:

1. 從資源庫加入 Kindling
2. 設定並測試 Azure AD 單一登入


## 從資源庫加入 Kindling

若要設定 Kindling 與 Azure AD 的整合作業，您需要從資源庫將 Kindling 新增至受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Kindling，請執行下列步驟：**

1. 在 **Azure 管理入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。 <br><br>
![Active Directory][1]<br>

2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。<br><br>
![應用程式][2]<br>
4. 按一下 [ **新增** 頁面的底部。<br><br>
![應用程式][3]<br>
5. 在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。<br><br>
![應用程式][4]<br>
6. 在 [搜尋] 方塊中，輸入 **Kindling**。<br><br>
![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_01.png)<br>
7. 在 [結果] 窗格中，選取 **Kindling**, ，然後按一下 [ **完成** 加入應用程式。
<br><br>
![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_02.png)<br>

## 設定並測試 Azure AD 單一登入

本節目標是示範如何根據名為「Britta Simon」的測試使用者，使用 Kindling 設定並測試 Azure AD 單一登入。

若要使單一登入生效，Azure AD 必須能識別 Kindling 與 Azure AD 中互相對應的使用者。換句話說，必須建立 Azure AD 使用者和 Kindling 中的相關的使用者之間的連結關聯性。<br>
建立此連結關聯性的方法，是將 Azure AD 中**使用者名稱**的值指派為 Kindling 中 **Username** 的值。

若要使用 Kindling 設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. * *[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)* *-若要讓使用者可以使用這項功能。
2. * *[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)* *-Azure AD 單一登入與 Britta Simon 測試。
4. * *[建立 Kindling 測試使用者](#creating-a-kindling-test-user)* *-若要連結到她在 Azure AD 代表 Kindling 中有對應的 Britta Simon。
5. * *[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)* *-讓 Britta Simon，若要使用 Azure AD 單一登入。
6. * *[測試單一登入](#testing-single-sign-on)* *-若要確認設定是否可以運作。

### 設定 Azure AD 單一登入

本節目標是在 Azure AD 入口網站中啟用 Azure AD 單一登入功能，並在 Kindling 應用程式中設定單一登入。 在此程序中，您必須建立 base-64 編碼的憑證檔案。 如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

若要為 Kindling 設定單一登入，您需要已註冊的網域。 如果您沒有註冊的網域，請連絡 Kindling 支援小組透過 [support@flatterfiles.com](mailto:support@flatterfiles.com)。



**若要使用 Kindling 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站上 **Kindling** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 **設定單一登入**  ] 對話方塊。
<br><br> ![設定單一登入][6] <br>

2. 在 **您希望使用者如何登入 Kindling** 頁面上，選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。
<br><br> ![設定單一登入](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_03.png) <br>

3. 在 **設定應用程式設定** 對話方塊頁面上，執行下列步驟:。
<br><br>![設定單一登入](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_04.png) <br>

    a. 在 **登入 URL** 文字方塊中，輸入您的使用者用於登入 Kindling 應用程式使用的 URL (例如: *https://fabrikam.Kindling.com/*)。

    b. 請連絡您的 Kindling 支援小組，以取得 [簽發者]**** 和 [回覆 URL]**** 值。

    c. 按 [下一步]****。

4. 在 **Kindling 在設定單一登入** 頁面上，執行下列步驟:
<br><br>![設定單一登入](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_05.png) <br>

    a. 按一下 [下載憑證]****，然後將檔案儲存在您的電腦上。

    b. 請連絡您的 Kindling 支援小組，並要求他們為您的應用程式設定 SSO。 您需要將下載的憑證提供給支援小組，[簽發者 URL]**** 值對應到 Kindling 的 [實體識別碼]****、[單一登入服務 URL]**** 對應到 Kindling 的 [SSO 登入 URL]****，以及 [單一登出服務 URL]**** 對應到 Kindling 的 [SSO 登出 URL]****。

    c. 按 [下一步]****。

6. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按 [下一步]****。 
<br><br>![Azure AD 單一登入][10]<br>

7. 在 [單一登入確認]**** 頁面上，按一下 [完成]****。  
  <br><br>![Azure AD 單一登入][11]




### 建立 Azure AD 測試使用者

本節的目標是名為 Britta Simon 在 Azure 入口網站中建立測試使用者。<br>
在 [使用者] 清單中選取 **Britta Simon**。<br><br>![建立 Azure AD 使用者][20]<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Active Directory]****。
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/create_aaduser_09.png) <br>

2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示在上方功能表中的使用者，清單中，按一下 [ **使用者**。
<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/create_aaduser_03.png) <br>

4. 若要開啟 **新增使用者** ] 對話方塊的底部工具列上，按一下 [ **新增使用者**。 
<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/create_aaduser_04.png) <br>

5. 在 **告訴我們這位使用者** 對話方塊頁面上，執行下列步驟: 
<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/create_aaduser_05.png) <br>

    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

    b. 在 [使用者名稱]**** 文字方塊中，輸入 **BrittaSimon**。

    c. 按 [下一步]****。

6.  在 **使用者設定檔** 對話方塊頁面上，執行下列步驟: 
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/create_aaduser_06.png) <br>

    a. 在 [名字]**** 文字方塊中，輸入 **Britta**。

    b. 在 [姓氏]**** 文字方塊中，輸入 **Simon**。

    c. 在 [顯示名稱]**** 文字方塊中，輸入 **Britta Simon**。

    d. 在 [角色]**** 清單中選取 [使用者]****。
    e. 按 [下一步]****。

7. 在 **取得暫時密碼** 對話方塊頁面上，按一下 [ **建立**。
<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/create_aaduser_07.png) <br>

8. 在 **取得暫時密碼** 對話方塊頁面上，執行下列步驟:
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-kindling-tutorial/create_aaduser_08.png) <br>

    a. 記下 [新密碼]**** 的值。

    b. 按一下 [完成]****。



### 建立 Kindling 測試使用者

本節目標是在 Kindling 中建立名為 Britta Simon 的使用者。
Kindling 支援 Just-in-Time 佈建。 您已啟用在 [設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)。

在這一節沒有您需要進行的動作項目。




### 指派 Azure AD 測試使用者

本節目標是讓 Britta Simon，授予其存取權 Kindling 使用 Azure 單一登入。
<br><br>![將使用者指派][200] <br>

**若要將 Britta Simon 指派至 Kindling，請執行下列步驟：**

1. 在 Azure 入口網站中，若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。
<br><br>![將使用者指派][201] <br>

2. 在應用程式清單中，選取 **Kindling**。
<br><br>![設定單一登入](./media/active-directory-saas-kindling-tutorial/tutorial_kindling_50.png) <br>

1. 在頂端的功能表中，按一下 [使用者]****。
<br><br>![將使用者指派][203] <br>

1. 在 [使用者] 清單中，選取 [Britta Simon]****。

2. 在底部工具列中，按一下 [ **指派**。
<br><br>![指派給使用者][205]



### 測試單一登入

本節目標是要測試您的 Azure AD 單一登入組態使用存取面板。<br>
當您在存取面板中按一下 [Kindling] 圖示時，應該會自動登入 Kindling 應用程式。


## 其他資源

* [如何使用 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory?](active-directory-appssoaccess-whatis.md)





[1]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_01.png 
[2]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_02.png 
[3]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_03.png 
[4]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_04.png 
[6]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_05.png 
[10]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_06.png 
[11]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_07.png 
[20]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_100.png 
[200]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_200.png 
[201]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_201.png 
[203]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_203.png 
[204]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_204.png 
[205]: ./media/active-directory-saas-kindling-tutorial/tutorial_general_205.png 

