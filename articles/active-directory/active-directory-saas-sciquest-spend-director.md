<properties
    pageTitle="教學課程：Azure Active Directory 與 SciQuest Spend Director 整合 | Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 SciQuest Spend Director 之間的單一登入。"
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



# 教學課程：Azure Active Directory 與 SciQuest Spend Director 整合



- 您可以在 Azure AD 中控制可存取 SciQuest Spend Director 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SciQuest Spend Director (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。



## 必要條件

若要使用 SciQuest Spend Director 設定 Azure AD 整合，您需要以下項目：

- Azure AD 訂用帳戶
- 啟用 SciQuest Spend Director 單一登入的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 


## 案例描述




1. 從資源庫加入 SciQuest Spend Director
2. 設定並測試 Azure AD 單一登入


## 從資源庫加入 SciQuest Spend Director

若要設定 SciQuest Spend Director 與 Azure AD 整合，您需要從資源庫將 SciQuest Spend Director 加入 受管理 SaaS app 的清單。

**若要從資源庫新增 SciQuest Spend Director，請執行下列步驟：**

1. 
![Active Directory][1]

2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 
![應用程式][2]
4. 
![應用程式][3]
5. 
![應用程式][4]
6. 
![應用程式][5]
7. 
![應用程式][6]


## 設定並測試 Azure AD 單一登入

本節的目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 SciQuest Spend Director 來設定及測試 Azure AD 單一登入。

單一登入若要運作，Azure AD 必須知道 SciQuest Spend Director 與 Azure AD 中互相對應的使用者。
建立此連結關聯性的方法是將 Azure AD 中的 **使用者名稱** 的值指定為 SciQuest Spend Director 中**使用者名稱**的值。

若要使用 SciQuest Spend Director 設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. 
2. 
4. 
5. 
5. 

### 設定 Azure AD 單一登入



**若要使用 SciQuest Spend Director 設定 Azure AD 單一登入，請執行下列步驟：**

1. 
![設定單一登入][8]

2. 
![Azure AD 單一登入][9]

3. 

     3.1. 

     3.2. 在 [回覆 URL]**** 文字方塊中，輸入您在 [登入 URL]**** 文字方塊所輸入的相同值。

     3.3. 按 [下一步]****。

4. 

5. 連絡 SciQuest 支援來啟用使用上述下載的中繼資料進行驗證的方法。

6. 在 Azure AD 入口網站上，選取單一登入組態確認，然後按一下 [完成]**** 以關閉 [設定單一登入]**** 對話方塊。
10. 在 [單一登入確認]**** 頁面上，按一下 [完成]****。




### 建立 Azure AD 測試使用者

本節的目標是在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Active Directory]****。

2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。
3. 

4.  

5. 

  1. 針對 [使用者類型]****，選取 [您組織中的新使用者]****。
  2. 在 [使用者名稱]**** 文字方塊中輸入 **Britta Simon**。
  3. 按一下 [下一步]。
6.   

  1. 在 [名字]**** 文字方塊中，輸入 **Britta**。
  2. 在 [姓氏]**** 文字方塊中輸入 **Simon**。
  3. 在 [顯示名稱]**** 文字方塊中輸入 **Britta Simon**。
  4. 在 [角色]**** 清單中選取 [使用者]****。
  5. 按 [下一步]****。
7. 

8. 

  1. 記下 [新密碼]**** 的值。
  2. 按一下 [完成]****。


### 建立 SciQuest Spend Director 測試使用者

本節目標是在 SciQuest Spend Director 中建立名為 Britta Simon 的使用者。

您需要連絡您的 SciQuest Spend Director 支援小組，並提供他們與您的測試帳戶有關的詳細資料來建立帳戶。


使用此功能時就不需要手動建立單一登入對應使用者。

若要啟用 Just-In-Time 佈建，您需要連絡您的 SciQuest Spend Director 支援小組。


### 指派 Azure AD 測試使用者




**若要指派 Britta Simon 到 SciQuest Spend Director，請執行以下步驟：**

1. 

2. 

1. 

1. 在 [使用者] 清單中，選取 [Britta Simon]****。

2. 




### 測試單一登入


當您在 [存取面板] 中按一下 [SciQuest Spend Director] 磚時，您應該會自動登入您的 SciQuest Spend Director 應用程式。


## 其他資源

* 
* 



[1]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_01.png 
[2]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_02.png 
[3]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_03.png 
[4]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_04.png 
[5]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_01.png 
[6]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_05.png 
[8]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_06.png 
[9]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_07.png 
[10]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_08.png 
[11]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_03.png 
[15]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_04.png 
[100]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director/tutorial_sciquest_spend_director_06.png 
[203]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_18.png 
[204]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_19.png 
[205]: ./media/active-directory-saas-sciquest-spend-director/tutorial_general_20.png 

