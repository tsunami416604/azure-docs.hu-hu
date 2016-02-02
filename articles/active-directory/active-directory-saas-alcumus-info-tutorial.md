<properties
    pageTitle="教學課程：Azure Active Directory 與 Alcumus Info Exchange 整合 | Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 Alcumus Info Exchange 之間的單一登入。"
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
    ms.date="10/06/2015"
    ms.author="markusvi"/>



# 教學課程：Azure Active Directory 與 Alcumus Info Exchange 整合



- 您可以在 Azure AD 中控制可存取 Alcumus Info Exchange 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Alcumus Info Exchange (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。



## 必要條件

若要設定 Azure AD 與 Alcumus Info Exchange 整合，您需要下列項目：

- 
- 


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 


## 案例描述




1. 從資源庫新增 Alcumus Info Exchange
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 Alcumus Info Exchange

若要設定 Alcumus Info Exchange 與 Azure AD 整合，您需要從資源庫將 Alcumus Info Exchange 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Alcumus Info Exchange，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Active Directory]****。 


2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。


4. 按一下頁面底部的 [新增]****。


5. 在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。


6. 


7. 




## 設定並測試 Azure AD 單一登入

本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 Alcumus Info Exchange 來設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Alcumus Info Exchange 與 Azure AD 中互相對應的使用者。
建立此連結關聯性的方法是將 Azure AD 中的 **user name** 的值指定為 Alcumus Info Exchange 中 **Username** 的值。

若要使用 Alcumus Info Exchange 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. 
2. 
4. 
5. 
5. 

### 設定 Azure AD 單一登入



**若要使用 Alcumus Info Exchange 設定 Azure AD 單一登入，請執行下列步驟：**

1. 
![設定單一登入][6]

2. 
![Azure AD 單一登入][7]

3. 在 [設定 App 設定]**** 對話方塊頁面執行下列步驟： 


  3.
  > [AZURE.NOTE] 

  3.2. 按 [下一步]****。

4. 

5. 

6. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按 [下一步]****。

7. 在 [單一登入確認]**** 頁面上，按一下 [完成]****。




### 建立 Azure AD 測試使用者




**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 
![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png)

2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 

4. 

5. 
  1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
  2. 在 [使用者名稱]**** 文字方塊中輸入 **BrittaSimon**。
  3. 按一下 [下一步]。

6.  
  1. 在 [名字]**** 文字方塊中，輸入 **Britta**。
  2. 在 [姓氏]**** 文字方塊中輸入 **Simon**。
  3. 在 [顯示名稱]**** 文字方塊中輸入 **Britta Simon**。
  4. 在 [角色]**** 清單中選取 [使用者]****。
  5. 按 [下一步]****。

7. 在 **取得暫時密碼** 對話方塊頁面上，按一下 [ **建立**。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png)

8. 在 **取得暫時密碼** 對話方塊頁面上，執行下列步驟:<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png)
  1. 記下 [新密碼]**** 的值。
  2. 按一下 [完成]****。



### 建立 Alcumus Info Exchange 測試使用者

本節目標是在 Alcumus Info Exchange 中建立名為 Britta Simon 的使用者。

**若要在 Alcumus Info Exchange 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

1. 連絡 Alcumus 資訊 Exchange 支援小組透過 [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com),，


### 指派 Azure AD 測試使用者

本節目標是讓 Britta Simon，授予其存取權 Alcumus 資訊 Exchange 使用 Azure 單一登入。
<br><br>![指派給使用者][200]

**若要將 Britta Simon 指派到 Alcumus Info Exchange，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。<br>
<br><br>![指派給使用者][201]
2. 在應用程式清單中，選取 **Alcumus 資訊 Exchange**。
<br><br>![指派給使用者][202]
1. 在頂端功能表中，按一下 [ **使用者**。<br>
<br><br>![指派給使用者][203]
1. 在 [使用者] 清單中，選取 [Britta Simon]****。

2. 在底部工具列中，按一下 [ **指派**。
<br><br>![指派給使用者][205]



### 測試單一登入

本節目標是要測試您的 Azure AD 單一登入組態使用存取面板。<br>
當您在存取面板中按一下 [Alcumus Info Exchange] 磚時，應該會自動登入您的 Alcumus Info Exchange 應用程式。


## 其他資源

* [如何使用 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory?](active-directory-appssoaccess-whatis.md)



[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png 
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png 
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png 
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png 
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png 
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png 
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png 
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png 
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png 
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png 
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png 
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png 
[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png 
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png 
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png 
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png 
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png 
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png 
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png 

