<properties
    pageTitle="教學課程：Azure Active Directory 與 SilkRoad Life Suite 整合 | Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 SilkRoad Life Suite 之間的單一登入。"
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
    ms.date="10/23/2015"
    ms.author="markusvi"/>



# 教學課程：Azure Active Directory 與 SilkRoad Life Suite 整合



- 您可以在 Azure AD 中控制可存取 SilkRoad Life Suite 的人員
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 SilkRoad Life Suite (單一登入)



## 必要條件

若要設定與 SilkRoad Life Suite 的 Azure AD 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 SilkRoad Life Suite 單一登入的訂用帳戶


> [AZURE.NOTE] 若要測試本教學課程中的步驟，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 


## 案例描述




1. 從資源庫加入 SilkRoad Life Suite
2. 設定並測試 Azure AD 單一登入


## 從資源庫加入 SilkRoad Life Suite

若要設定 SilkRoad Life Suite 與 Azure AD 整合，您需要從資源庫將 SilkRoad Life Suite 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 SilkRoad Life Suite，請執行下列步驟：**

1. 


2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 

4. 

5. 

6. 

7. 



## 設定並測試 Azure AD 單一登入

本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，設定及測試對 SilkRoad Life Suite 的 Azure AD 單一登入。

單一登入若要運作，Azure AD 必須知道 SilkRoad Life Suite 與 Azure AD 中互相對應的使用者。
建立此連結關聯性的方法是將 Azure AD 中**使用者名稱**的值指定為 SilkRoad Life Suite 中 **Username** 的值。

若要設定及測試對 SilkRoad Life Suite 的 Azure AD 單一登入，您需要完成下列建置組塊：

1. 
2. 
4. 
5. 
5. 

### 設定 Azure AD 單一登入



**若要使用 SilkRoad Life Suite 設定 Azure AD 單一登入，請執行下列步驟：**

5. 以系統管理員身分登入您的 SilkRoad 公司網站。

    > [AZURE.NOTE] 若要取得用於設定與 Microsoft Azure AD 的同盟存取的 SilkRoad Life Suite 驗證應用程式，請連絡 SilkRoad 支援人員或您的 SilkRoad 服務代表。

6.  


1. 


3. 


2. 


3. 在 [設定 App 設定]**** 對話方塊頁面執行下列步驟：


 a.

 b.

 c.         
 

 d. 請將該值貼入 [回覆 URL]**** 文字方塊。

 e. 按 [下一步]****。

4. 


 a. 按一下 [下載憑證]，然後將檔案儲存在您的電腦上。

 b. 按 [下一步]****。

1. 


1.  


1.  


 a. 在 [選項 2 - 中繼資料檔案]**** 下，按一下 [瀏覽]**** 來上傳下載的中繼資料檔案。

 b. 按一下 [使用檔案資料建立身分識別提供者]****。

1.  


1.  


 a. 對 [已啟用]**** 選取 [是]****。

 b. 在 [IdP 描述]**** 文字方塊中，輸入您的組態描述 (例如：*Azure AD SSO*)。

 c. 在 [IdP 名稱]**** 文字方塊中，輸入組態特定的名稱 (例如：*Azure SP*)。

 d. 按一下 [儲存]****。

6.  


7.   
  

1. 在 [單一登入確認]**** 頁面上，按一下 [完成]****。
  


### 建立 Azure AD 測試使用者




**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**的左方瀏覽窗格中，按一下 [Active Directory]****。


2. 從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3. 


4.  


5.  


 a. 針對 [使用者類型]，選取 [您組織中的新使用者]。

 b. 在 [使用者名稱]**** 文字方塊中，輸入 **BrittaSimon**。

 c. 按 [下一步]****。

6.   


a. 在 [名字]**** 文字方塊中，輸入 **Britta**。

b. 在 [姓氏]**** 文字方塊中，輸入 **Simon**。

c. 在 [顯示名稱]**** 文字方塊中，輸入 **Britta Simon**。

d. 在 [角色]**** 清單中選取 [使用者]****。
e. 按 [下一步]****。

7. 


8. 


 a. 記下 [新密碼]**** 的值。

 b. 按一下 [完成]****。



### 建立 SilkRoad Life Suite 測試使用者

本節目標是在 SilkRoad Life Suite 中建立名為 Britta Simon 的使用者。 Britta 必須具備符合 Britta 在 Azure AD 中**電子郵件位址**的 SSO 識別碼 (有時稱為 *AuthParam*)。



1. 



### 指派 Azure AD 測試使用者




**若要將 Britta Simon 指派到 SilkRoad Life Suite，請執行以下步驟：**

1. 

2. 

1. 在頂端的功能表中，按一下 [使用者]****。

1. 在 [使用者] 清單中，選取 [Britta Simon]****。

2. 




### 測試單一登入


當您在 [存取面板] 中按一下 [SilkRoad Life Suite] 磚時，您應該會自動登入您的 SilkRoad Life Suite 應用程式。


## 其他資源

* 
* 





[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png 
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png 
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png 
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png 
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png 
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png 
[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png 
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png 
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png 
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png 
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png 
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png 
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png 
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png 
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png 
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png 
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png 
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png 
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png 
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png 
[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png 
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png 
[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png 
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png 
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png 
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png 
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png 
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png 

