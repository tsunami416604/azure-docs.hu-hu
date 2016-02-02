<properties
    pageTitle="教學課程：Azure Active Directory 與 O.C.Tanner - AppreciateHub 整合 |Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 O.C.Tanner - AppreciateHub 之間的單一登入。"
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
    ms.date="10/09/2015"
    ms.author="markusvi"/>



# 教學課程：Azure Active Directory 與 O.C.Tanner - AppreciateHub 整合

本教學課程的目的是示範如何將 O.C.Tanner-AppreciateHub 與 Azure Active Directory (Azure AD)。<br>整合 O.C.Tanner - AppreciateHub 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 O.C.Tanner - AppreciateHub 的人員
- 您可以讓使用者使用其 Azure AD 帳戶自動登入到 O.C.Tanner - AppreciateHub (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

如果您想要知道更多詳細與 Azure AD 的 SaaS 應用程式整合，請參閱 [什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## 必要條件

若要設定 Azure AD 與 O.C.Tanner - AppreciateHub 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 O.C.Tanner - AppreciateHub 單一登入的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以取得一個月試用 [這裡](https://azure.microsoft.com/pricing/free-trial/)。


## 案例描述

本教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。 <br>
本教學課程中說明的案例包含三個主要建置區塊:

1. 從組件庫新增 O.C.Tanner - AppreciateHub
2. 設定並測試 Azure AD 單一登入


## 從組件庫新增 O.C.Tanner - AppreciateHub

若要設定將 O.C.Tanner - AppreciateHub 整合到 Azure AD，您需要從組件庫將 O.C.Tanner - AppreciateHub 新增至受管理的 SaaS 應用程式清單。

**若要新增 O.C.Tanner-AppreciateHub 從組件庫，請執行下列步驟:**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Active Directory]****。 
<br><br> ![Active Directory][1] <br>

2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。
<br><br> ![應用程式][2] <br>

4. 按一下頁面底部的 [新增]****。
<br><br> ![應用程式][3] <br>

5. 在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。
<br><br> ![應用程式][4] <br>

6. 在 [搜尋] 方塊中，輸入 **O.C.Tanner-AppreciateHub**。
<br><br> ![應用程式][5] <br>

7. 在 [結果] 窗格中，選取 **O.C.Tanner-AppreciateHub**, ，然後按一下 [ **完成** 加入應用程式。
<br><br> ![應用程式][25] <br>




## 設定並測試 Azure AD 單一登入

本節目標說明如何以名為 "Britta Simon" 的測試使用者來設定及測試 Azure AD 單一登入與 O.C.Tanner - AppreciateHub。

為使單一登入運作，Azure AD 必須知道 O.C.Tanner - AppreciateHub 與 Azure AD 中互相對應的使用者。換句話說，必須在 Azure AD 使用者和 O.C.  Tanner - AppreciateHub 中的相關使用者之間建立連結關聯性Tanner-AppreciateHub 必須建立。<br>
建立此連結關聯性的方法是將 Azure AD 中「使用者名稱」****的值指定為 O.C. Tanner - AppreciateHub 中 **Username** 的值Tanner - AppreciateHub 之間的單一登入。

若要設定和測試 Azure AD 單一登入與 O.C.Tanner - AppreciateHub，您必須完成下列建置組塊：

1. * *[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)* *-若要讓使用者可以使用這項功能。
2. * *[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)* *-Azure AD 單一登入與 Britta Simon 測試。
4. * *[建立 O.C.Tanner-AppreciateHub 測試使用者](#creating-a-halogen-software-test-user)* *-若要在 O.C.中有對應的 Britta Simon 的Tanner - AppreciateHub 中有對應 Britta Simon 的使用者，以連結到 Azure AD 中代表的使用者。
5. * *[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)* *-讓 Britta Simon，若要使用 Azure AD 單一登入。
5. * *[測試單一登入](#testing-single-sign-on)* *-若要確認設定是否可以運作。

### 設定 Azure AD 單一登入

本節目標是在 Azure AD 入口網站啟用 Azure AD 單一登入，並在您的 O.C.Tanner-AppreciateHub 應用程式。<br>

**若要設定 Azure AD 單一登入與 O.C.Tanner-AppreciateHub，執行下列步驟:**

1. 在 Azure AD 入口網站上 **O.C.Tanner-AppreciateHub** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 **設定單一登入**  ] 對話方塊。<br><br>
![設定單一登入][6]

2. 在 **您希望使用者如何登入 O.C.Tanner-AppreciateHub** 頁面上，選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。<br><br>
![Azure AD 單一登入][7]

3. 在 [設定 App 設定]**** 對話方塊頁面執行下列步驟：
<br><br>![設定應用程式設定][8]

     a. 開啟中繼資料檔案使用下列連結: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata)。

     b. 找出 [md:AssertionConsumerService]**** 節點。

     c. 複製的值 **位置** 屬性。 
        <br><br>![設定應用程式設定][12]

     d. 在[登入 URL] **** 文字方塊中，貼上您在上一個步驟中取得的值。
     > [AZURE.NOTE] 如果從中繼資料檔案取得回覆 URL 時發生問題，請透過以下電子郵件連絡 O.C.Tanner-AppreciateHub 支援小組透過 [sso@octanner.com](mailto:sso@octanner.com)。

     e. 按 [下一步]****。

4. 在 **O.C.在設定單一登入Tanner-AppreciateHub** 頁面上，按一下 **下載中繼資料**, ，然後將儲存在本機電腦上的中繼資料檔案。<br><br>![什麼是 Azure AD Connect][9]

5. 請透過 xyz 連絡 O.C.Tanner - AppreciateHub 支援小組，將中繼資料檔案提供給他們，讓他們知道他們應該為您啟用 SSO。

6. 在 Azure AD 入口網站上，選取單一登入設定確認，然後按 [下一步]****。<br><br>![什麼是 Azure AD Connect][10]

7. 在 [單一登入確認]**** 頁面上，按一下 [完成]****。<br><br>![什麼是 Azure AD Connect][11]




### 建立 Azure AD 測試使用者

本節的目標是名為 Britta Simon 在 Azure 入口網站中建立測試使用者。<br>
在 [使用者] 清單中選取 **Britta Simon**。<br><br>![建立 Azure AD 使用者][20]<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。<br>
![建立 Azure AD 測試使用者](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png)

2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 若要顯示在上方功能表中的使用者，清單中，按一下 [ **使用者**。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png)

4. 若要開啟 **新增使用者** ] 對話方塊的底部工具列上，按一下 [ **新增使用者**。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png)

5. 在 **告訴我們這位使用者** 對話方塊頁面上，執行下列步驟: <br>![建立 Azure AD 測試使用者](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png)

    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

    b. 在 [使用者名稱]**** 文字方塊中，輸入 **BrittaSimon**。

    c. 按 [下一步]****。

6.  在 **使用者設定檔** 對話方塊頁面上，執行下列步驟: <br>![建立 Azure AD 測試使用者](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)

    a. 在 [名字]**** 文字方塊中，輸入 **Britta**。

    b. 在 [姓氏]**** 文字方塊中，輸入 **Simon**。

    c. 在 [顯示名稱]**** 文字方塊中，輸入 **Britta Simon**。

    d. 在 [角色]**** 清單中選取 [使用者]****。
    e. 按 [下一步]****。

7. 在 **取得暫時密碼** 對話方塊頁面上，按一下 [ **建立**。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png)

8. 在 **取得暫時密碼** 對話方塊頁面上，執行下列步驟:<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png)

    a. 記下 [新密碼]**** 的值。

    b. 按一下 [完成]****。



### 建立 O.C.Tanner - AppreciateHub 測試使用者

本節目標是在 O.C.Tanner - AppreciateHub 中建立名為 Britta Simon 的使用者。

**若要建立名為 Britta Simon O.C.中的使用者Tanner-AppreciateHub，執行下列步驟:**

1. 要求您的 OC Tanner 支援小組建立一個 nameID 屬性與 Azure AD 中 Britta Simon 使用者名稱的值相同的使用者。


### 指派 Azure AD 測試使用者

本節目標是授與 Britta Simon 對 O.C. Tanner - AppreciateHub 的存取權，讓她能夠使用 Azure 單一登入。
<br><br>![指派給使用者][200]

**若要指派 Britta Simon 到 O.C.Tanner-AppreciateHub，執行下列步驟:**

1. 在 Azure 入口網站中，若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。<br>
<br><br>![指派給使用者][201]
2. 在應用程式清單中，選取 **O.C.Tanner-AppreciateHub**。
<br><br>![指派給使用者][202]
1. 在頂端功能表中，按一下 [ **使用者**。<br>
<br><br>![指派給使用者][203]
1. 在 [使用者] 清單中，選取 [Britta Simon]****。

2. 在底部工具列中，按一下 [ **指派**。
<br><br>![指派給使用者][205]



### 測試單一登入

本節目標是要測試您的 Azure AD 單一登入組態使用存取面板。<br>
當您在 [存取面板] 按一下 [O.C.Tanner - AppreciateHub] 磚時，應該會自動登入您的 O.C.Tanner - AppreciateHub 應用程式。


## 其他資源

* [如何使用 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory?](active-directory-appssoaccess-whatis.md)




[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png 
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png 
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png 
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png 
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png 
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png 
[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png 
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png 
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png 
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png 
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png 
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png 
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png 
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png 
[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png 
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png 
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png 
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png 
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png 
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png 

