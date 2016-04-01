<properties
    pageTitle="教學課程：Azure Active Directory 與 ImageRelay 整合 | Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 ImageRelay 之間的單一登入。"
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
    ms.date="11/16/2015"
    ms.author="markusvi"/>


# 教學課程：Azure Active Directory 與 ImageRelay 整合

本教學課程的目標在於告訴您如何使用 Azure Active Directory (Azure AD) 整合 ImageRelay。<br>與 Azure AD 整合 ImageRelay 可以提供下列優點 ︰

- 您可以在 Azure AD 中控制可存取 ImageRelay 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 ImageRelay (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

如果您想要知道更多詳細與 Azure AD 的 SaaS 應用程式整合，請參閱 [什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## 必要條件

若要設定 Azure AD 與 ImageRelay 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 啟用 ImageRelay 單一登入的訂用帳戶


> [AZURE.NOTE] 若要測試的步驟，本教學課程中，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以取得一個月試用 [這裡](https://azure.microsoft.com/pricing/free-trial/)。


## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。 <br>
本教學課程中說明的案例包含兩個主要建置區塊 ︰

1. 從組件庫加入 ImageRelay

2. 設定並測試 Azure AD 單一登入


## 從組件庫加入 ImageRelay
若要設定 ImageRelay 與 Azure AD 整合，您需要從資源庫將 ImageRelay 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 ImageRelay，請執行下列步驟：**

1. 在 Azure 入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。 <br><br>
![Active Directory][1]<br>

2. 從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。<br><br>
![應用程式][2]<br>
4. 按一下 [ **新增** 頁面的底部。<br><br>
![應用程式][3]<br>
5. 在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。<br><br>
![應用程式][4]<br>
6. 在 [搜尋] 方塊中，輸入 **ImageRelay**。<br><br>
![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)<br>
7. 在 [結果] 窗格中，選取 **ImageRelay**, ，然後按一下 [ **完成** 加入應用程式。
<br><br>

##  設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 ImageRelay 來設定及測試 Azure AD 單一登入。

要啟用單一登入，Azure AD 需要代表 ImageRelay 中相關的使用者的使用者帳戶。  換句話說，必須建立 Azure AD 使用者和 ImageRelay 中的相關的使用者之間的連結關聯性。<br>
藉由指定的值建立此連結關聯性 **使用者名稱** 在 Azure AD 中的值為 **Username** ImageRelay 中。

若要使用 ImageRelay 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** -若要讓使用者可以使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** -Azure AD 單一登入與 Britta Simon 測試。
4. **[建立測試使用者 ImageRelay](#creating-a-userecho-test-user)** -若要連結到她在 Azure AD 代表 ImageRelay 中有對應的 Britta Simon。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** -讓 Britta Simon，若要使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** -若要確認設定是否可以運作。

### 設定 Azure AD 單一登入

本節目標是在 Azure AD 入口網站啟用 Azure AD 單一登入，並在您的 ImageRelay 應用程式中設定單一登入。


**若要使用 ImageRelay 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站上 **ImageRelay** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 **設定單一登入** ] 對話方塊。

     ![設定單一登入][6] <br>

2. 在 **您希望使用者如何登入 ImageRelay** 頁面上，選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) <br>

3. 在 **設定應用程式設定** 對話方塊頁面上，執行下列步驟 ︰

     ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) <br>

    a. 在 **登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入 ImageRelay 應用程式 (例如 ︰ *https://fabrikam.ImageRelay.com/*)。

    b. 按一下 [ **下一步**。

4. 在 **ImageRelay 在設定單一登入** 頁面上，執行下列步驟 ︰

    ![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) <br>

    a. 按一下 [ **下載憑證**, ，然後儲存您的電腦上的檔案。

    b. 按一下 [ **下一步**。

5. 在另一個瀏覽器視窗中，以系統管理員身分登入您的 ImageRelay 公司網站。

    a. 在頂端工具列中，按一下 [ **使用者和權限** 工作負載。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) <br>

    b. 按一下 [ **建立新的權限**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) <br>

    c. 在 **單一登入設定** 工作負載，選取 **︰ 此群組可以只登入透過單一登入** 核取方塊，，然後按一下 [ **儲存**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) <br>

    d. 移至 **帳戶設定**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) <br>

    e. 移至 **單一登入設定** 工作負載。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)<br>

    f. 如同，填寫表單，然後按一下 **儲存**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)<br>

    - **登入 URL (SSO)**︰ 它是從 Azure Active Directory 的單一登入服務 URL。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_13.png)<br>

    - **登出服務 URL**︰ 它是從 Azure Active Directory 的單一登出服務。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_14.png)<br>

    - 在 **名稱識別碼格式**, ，請選取 **urn: oasis ︰ 名稱 ︰ tc: SAML:1.1:nameid-格式 ︰ emailAddress**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_15.png)<br>

    - 在 **繫結的選項，從服務提供者 （影像轉送） 要求**, ，請選取 **POST 繫結**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_16.png)<br>  

    - 在 **x.509 憑證**, ，按一下 [ **更新憑證**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)<br>

    - 在記事本中，開啟步驟 4 中從 Azure Active Directory 下載的憑證然後複製並貼上的憑證的內容。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)<br>

    - 在 **Just-In-Time 使用者佈建**, ，請選取 **啟用 Just-In-Time 佈建使用者** ] 核取方塊。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)<br>

    - 選取的權限群組 (例如， **SSO 基本**) 允許的登入只能透過單一登入。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)<br>

6. 在 Azure AD 入口網站中，選取單一登入設定確認，以及 [ **下一步**。

    ![Azure AD 單一登入][10]<br>

7. 在 **單一登入確認** 頁面上，按一下 **完成**。

    ![Azure AD 單一登入][11]


### 建立 Azure AD 測試使用者
本節的目標是在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。<br>
在 [使用者] 清單中選取 **Britta Simon**。<br><br>![建立 Azure AD 使用者][20]<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) <br>

2. 從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示在上方功能表中的使用者，清單中，按一下 [ **使用者**。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) <br>

4. 若要開啟 **新增使用者** ] 對話方塊的底部工具列上，按一下 [ **新增使用者**。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) <br>

5. 在 **告訴我們這位使用者** 對話方塊頁面上，執行下列步驟 ︰<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) <br>

    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

    b. 在 [使用者名稱 **文字方塊**, ，型別 **britta Simon**。

    c. 按一下 [ **下一步**。

6.  在 **使用者設定檔** 對話方塊頁面上，執行下列步驟 ︰<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) <br>

    a. 在 **名字** 文字方塊中，輸入 **Britta**。  

    b. 在 **姓氏** 文字方塊中，輸入， **Simon**。

    c. 在 **顯示名稱** 文字方塊中，輸入 **Britta Simon**。

    d. 在 **角色** 清單中，選取 **使用者**。

    e. 按一下 [ **下一步**。

7. 在 **取得暫時密碼** 對話方塊頁面上，按一下 [ **建立**。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) <br>

8. 在 **取得暫時密碼** 對話方塊頁面上，執行下列步驟 ︰<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) <br>

    a. 請記下的值 **新密碼**。

    b. 按一下 [ **完整**。   



### 建立 ImageRelay 測試使用者

本節目標是在 ImageRelay 中建立名為 Britta Simon 的使用者。

**若要在 ImageRelay 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

1. 以系統管理員身分登入您的 ImageRelay 公司網站。

1. 移至 **使用者和權限**    ，然後選取 **建立 SSO 使用者**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) <br>

1. 輸入 **電子郵件**, ，**名字**, ，**姓氏** 和 **公司** 您想要佈建和選取的權限群組 （例如，SSO 基本） 即可以登入，只能透過單一登入的群組的使用者。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) <br>

1. 按一下 [ **建立**。

### 指派 Azure AD 測試使用者

本節目標是讓 Britta Simon，授予其存取權 ImageRelay 使用 Azure 單一登入。
<br><br>![指派使用者][200] <br>

**若要將 Britta Simon 指派到 ImageRelay，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。<br><br>![指派給使用者][201] <br>

2. 在應用程式清單中，選取 **ImageRelay**。<br><br>![設定單一登入](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) <br>

1. 在頂端功能表中，按一下 [ **使用者**。<br><br>![指派給使用者][203] <br>

1. 在 [使用者] 清單中選取 **Britta Simon**。

2. 在底部工具列中，按一下 [ **指派**。<br><br>![指派給使用者][205]


### 測試單一登入

本節目標是要測試您的 Azure AD 單一登入組態使用存取面板。<br>
當您在存取面板中按一下 [ImageRelay] 磚時，應該會自動登入您的 ImageRelay 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png


