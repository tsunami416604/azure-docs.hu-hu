<properties
    pageTitle="教學課程：Azure Active Directory 與 PerformanceCentre 整合 | Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 PerformanceCentre 之間的單一登入。"
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
    ms.date="10/30/2015"
    ms.author="markusvi"/>


# 教學課程：Azure Active Directory 與 PerformanceCentre 整合

本教學課程的目標在於告訴您如何使用 Azure Active Directory (Azure AD) 整合 PerformanceCentre。<br>與 Azure AD 整合 PerformanceCentre 可以提供下列優點: 

- 您可以在 Azure AD 中控制可存取 PerformanceCentre 的人員 
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 PerformanceCentre (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

如果您想要知道更多詳細與 Azure AD 的 SaaS 應用程式整合，請參閱 [什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## 必要條件 

若要設定 Azure AD 與 PerformanceCentre 整合，您需要以下項目：

- Azure AD 訂用帳戶
- 啟用 PerformanceCentre 單一登入的訂用帳戶


> [AZURE.NOTE] 若要測試的步驟，本教學課程中，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以取得一個月試用 [這裡](https://azure.microsoft.com/pricing/free-trial/)。 

 
## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。 <br>
本教學課程中說明的案例包含三個主要建置區塊:

1. 從資源庫加入 PerformanceCentre 
2. 設定並測試 Azure AD 單一登入


## 從資源庫加入 PerformanceCentre
若要設定 PerformanceCentre 與 Azure AD 整合，您需要從資源庫將 PerformanceCentre 加入受管理 SaaS 應用程式的清單。

**若要從資源庫加入 PerformanceCentre，請執行下列步驟：**

1. 在 **Azure 管理入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。 <br><br>
![Active Directory][1]<br>

2. 從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。<br><br>
![應用程式][2]<br>
4. 按一下 [ **新增** 頁面的底部。<br><br>
![應用程式][3]<br>
5. 在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。<br><br>
![應用程式][4]<br>
6. 在 [搜尋] 方塊中，輸入 **PerformanceCentre**。<br><br>
![應用程式][5]<br>
7. 在 [結果] 窗格中，選取 **PerformanceCentre**, ，然後按一下 [ **完成** 加入應用程式。
<br><br>![應用程式][500]<br>


##  設定並測試 Azure AD 單一登入
本節的目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 PerformanceCentre 來設定及測試 Azure AD 單一登入。

單一登入若要運作，Azure AD 必須知道 PerformanceCentre 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和 PerformanceCentre 中的相關的使用者之間的連結關聯性。<br>
藉由指定的值建立此連結關聯性 **使用者名稱** 在 Azure AD 中的值為 **Username** PerformanceCentre 中。
 
若要使用 PerformanceCentre 設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** -若要讓使用者可以使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** -Azure AD 單一登入與 Britta Simon 測試。
4. **[建立測試使用者 PerformanceCentre](#creating-a-halogen-software-test-user)** -若要連結到她在 Azure AD 代表 PerformanceCentre 中有對應的 Britta Simon。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** -讓 Britta Simon，若要使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** -若要確認設定是否可以運作。

### 設定 Azure AD 單一登入

本節目標是要啟用 Azure AD 單一登入 Azure AD 入口網站中，並 PerformanceCentre 應用程式中設定單一登入。<br>

**若要使用 PerformanceCentre 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站上 **PerformanceCentre** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 **設定單一登入**  ] 對話方塊。
<br><br> ![設定單一登入][6] <br>

2. 在 **您希望使用者如何登入 PerformanceCentre** 頁面上，選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。
<br><br> ![Azure AD 單一登入][7] <br>

3. 在 **設定應用程式設定** 對話方塊頁面上，執行下列步驟:
<br><br>![Azure AD 單一登入][8] <br>
 
     a. 在 **登入 URL** 文字方塊中，輸入您的使用者用於登入 PerformanceCentre 網站使用的 URL (例如: *http://companyname.performancecentre.com/saml/SSO*)。
 
     b. 按一下 [ **下一步**。
 
4. 在 **PerformanceCentre 在設定單一登入** 頁面上，執行下列步驟:
<br><br>![Azure AD 單一登入][9] <br>

    a. 按一下 [ **下載中繼資料**, ，然後儲存您的電腦上的檔案。



1. 登入您 **PerformanceCentre** 系統管理員身分的公司網站。

2. 在左側索引標籤上，按一下 [ **設定**。
  <br><br>![Azure AD 單一登入][10]

2. 在左側索引標籤上，按一下 [ **其他**, ，然後按一下 [ **單一登入**。
  <br><br>![Azure AD 單一登入][11]

2. 做為 **通訊協定**, ，請選取 **SAML**。
  <br><br>![Azure AD 單一登入][12]

2. 在記事本中開啟下載的中繼資料檔案、 將內容複製、 貼上至 **身分識別提供者中繼資料** 文字方塊中，然後按一下 [ **儲存**。
  <br><br>![Azure AD 單一登入][13]

2. 確認值 **實體基底 URL** 和 **實體識別碼 URL** 正確無誤。
  <br><br>![Azure AD 單一登入][14]


6. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **下一步**。 
<br><br>![Azure AD 單一登入][15]<br>

7. 在 **單一登入確認** 頁面上，按一下 **完成**。  
  <br><br>![Azure AD 單一登入][16]




### 建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。<br>
在 [使用者] 清單中選取 **Britta Simon**。<br><br>![建立 Azure AD 使用者][20]<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png) <br> 

2. 從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示在上方功能表中的使用者，清單中，按一下 [ **使用者**。
<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) <br>
 
4. 若要開啟 **新增使用者** ] 對話方塊的底部工具列上，按一下 [ **新增使用者**。 
<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) <br>

5. 在 **告訴我們這位使用者** 對話方塊頁面上，執行下列步驟: 
<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png) <br> 

    a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

    b. 在 [使用者名稱 **文字方塊**, ，型別 **britta Simon**。

    c. 按一下 [ **下一步**。

6.  在 **使用者設定檔** 對話方塊頁面上，執行下列步驟: 
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) <br>
 
    a. 在 **名字** 文字方塊中，輸入 **Britta**。  

    b. 在 **姓氏** 文字方塊中，輸入， **Simon**。

    c. 在 **顯示名稱** 文字方塊中，輸入 **Britta Simon**。

    d. 在 **角色** 清單中，選取 **使用者**。
    e. 按一下 [ **下一步**。

7. 在 **取得暫時密碼** 對話方塊頁面上，按一下 [ **建立**。
<br><br> ![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) <br>
 
8. 在 **取得暫時密碼** 對話方塊頁面上，執行下列步驟:
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) <br>
  
    a. 請記下的值 **新密碼**。

    b. 按一下 [ **完整**。   

  
 
### 建立 PerformanceCentre 測試使用者

本節的目標是在 PerformanceCentre 中建立名為 Britta Simon 的使用者。

**若要在 PerformanceCentre 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

1. 以系統管理員身分登入您的 PerformanceCentre 公司網站。

2. 在左側功能表中，按一下 [ **Interrelate**, ，然後按一下 [ **建立參與者**。
<br><br>![建立使用者][400]<br>

4. 在 **之間的關係-建立參與者** ] 對話方塊中，執行下列步驟:
<br><br>![建立使用者][401]<br>

    a. 在相關的文字方塊中為 Britta Simon 輸入必要的屬性。
    
    > [AZURE.IMPORTANT] PerformanceCentre Britta 的使用者名稱屬性必須是相同的使用者名稱在 Azure AD 中。


    b. Select **Client Administrator** as **Choose Role**. 

    c. Click **Save**.   


### 指派 Azure AD 測試使用者

本節目標是讓 Britta Simon，授予其存取權 PerformanceCentre 使用 Azure 單一登入。
<br><br>![指派使用者][200] <br>

**若要指派 Britta Simon 到 PerformanceCentre，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。
<br><br>![將使用者指派][201] <br>
2. 在應用程式清單中，選取 **PerformanceCentre**。
<br><br>![將使用者指派][202] <br>
1. 在頂端功能表中，按一下 [ **使用者**。
<br><br>![將使用者指派][203] <br>
1. 在 [使用者] 清單中選取 **Britta Simon**。

2. 在底部工具列中，按一下 [ **指派**。
<br><br>![指派給使用者][205]



### 測試單一登入

本節目標是要測試您的 Azure AD 單一登入組態使用存取面板。<br>
當您在存取面板中按一下 PerformanceCentre 磚時，應該會自動登入您的 PerformanceCentre 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png




