<properties 
    pageTitle="教學課程：Azure Active Directory 與 TOPdesk - Secure 整合 | Microsoft Azure" description="了解如何使用 TOPdesk - Secure 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 TOPdesk - Secure 整合
  
本教學課程的目的是要示範 Azure 與 TOPdesk - Secure 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 TOPdesk - Secure 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 TOPdesk-Secure 的 Azure AD 使用者將能夠登入位於您 TOPdesk-Secure 公司網站 (服務提供者起始登入)，或使用應用程式的單一登入 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 TOPdesk - Secure 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scenario")

##啟用 TOPdesk - Secure 的應用程式整合
  
本節的目的是要說明如何啟用 TOPdesk - Secure 的應用程式整合。

###若要啟用 TOPdesk - Secure 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **TOPdesk-Secure**。

    ![應用程式庫](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **TOPdesk-Secure**, ，然後按一下 [ **完成** 加入應用程式。

    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 TOPdesk - Secure。  
設定 TOPdesk - Secure 的單一登入需要您上傳標誌的圖示檔。 若要取得圖示檔，請連絡 TOPdesk 支援小組。

###若要設定單一登入，請執行下列步驟：

1.  登入您 **TOPdesk-Secure** 系統管理員身分的公司網站。

2.  在 **TOPdesk** ] 功能表上，按一下 [ **設定**。

    ![設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Settings")

3.  按一下 [ **登入設定**。

    ![登入設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Login Settings")

4.  展開 **登入設定** 功能表，然後再按一下 **一般**。

    ![一般](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

5.  在 **Secure** 區段 **SAML 登入** 組態區段中，執行下列步驟:

    ![技術設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Technical Settings")

    1.  按一下 [ **下載** 下載公用中繼資料檔案，然後將它儲存在本機電腦上。
    2.  開啟中繼資料檔案，然後再找到 **AssertionConsumerService** 節點。
        ![判斷提示取用者服務](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Assertion Consumer Service")
    3.  複製 **AssertionConsumerService** 值。  

        >[AZURE.NOTE] 您將需要在值 **設定應用程式 URL** 本教學課程稍後的章節。

6.  在不同的網頁瀏覽器視窗中，登入您 **Azure Active Directory** 身為系統管理員入口網站。

7.  在 **TOPdesk-Secure** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configure Single Sign-On")

8.  在 **您希望使用者如何登入 TOPdesk-Secure** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configure Single Sign-On")

9.  在 **設定應用程式 URL** 頁面上，執行下列步驟:

    ![設定應用程式 URL](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configure App URL")

    1.  在 **TOPdesk-Secure 登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入 TOPdesk-安全的應用程式 (例如: 「*https://qssolutions.topdesk.net*」)。
    2.  在 **TOPdesk-Public 的回覆 URL** 文字方塊中，貼上 **TOPdesk-Secure AssertionConsumerService URL** (例如: 「*https://qssolutions.topdesk.net/tas/public/login/saml*」)
    3.  按一下 [ **下一步**。

10. 在 **設定單一登入 TOPdesk-Secure 在** 頁面上，來下載中繼資料檔中，按一下 **下載中繼資料**, ，然後儲存您的電腦上的本機檔案。

    ![設定單一登入](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configure Single Sign-On")

11. 若要建立憑證檔案，請執行下列步驟：

    ![憑證](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificate")

    1.  開啟下載的中繼資料檔案。
    2.  展開 **Securitytokenservicetype** 節點具有 **xsi: type** 的 **fed: ApplicationServiceType**。
    3.  複製的值 **X509Certificate** 節點。
    4.  儲存複製 **X509Certificate** 檔案在電腦上本機的值。

12. 在 TOPdesk-Secure 公司網站中 **TOPdesk** ] 功能表上，按一下 [ **設定**。

    ![設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Settings")

13. 按一下 [ **登入設定**。

    ![登入設定](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Login Settings")

14. 展開 **登入設定** 功能表，然後再按一下 **一般**。

    ![一般](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

15. 在 **公用** 區段中，按一下 **新增**。

    ![新增](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Add")

16. 在 **SAML 設定小幫手** 對話方塊頁面上，執行下列步驟:

    ![SAML 組態輔助程式](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "SAML Configuration Assistant")

    1.  若要上傳您下載的中繼資料檔案，在 **同盟中繼資料**, ，按一下 [ **瀏覽**。
    2.  在上傳憑證檔案， **憑證 (RSA)**, ，按一下 [ **瀏覽**。
    3.  上傳標誌檔底下所得 TOPdesk 支援小組， **標誌圖示**, ，按一下 [ **瀏覽**。
    4.  在 **使用者名稱屬性** 文字方塊中，輸入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    5.  在 **顯示名稱** 文字方塊中，輸入您的組態名稱。
    6.  按一下 [ **儲存**。

17. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configure Single Sign-On")

##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 TOPdesk - Secure，則必須將他們佈建到 TOPdesk - Secure。  
TOPdesk - Secure 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **TOPdesk-Secure** 系統管理員身分的公司網站。

2.  在頂端功能表中，按一下 [ **TOPdesk \ > 新增 \ > 支援檔案 \ > 運算子**。

    ![運算子](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operator")

3.  在 **New 運算子** ] 對話方塊中，執行下列步驟:

    ![新增操作員](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "New Operator")

    1.  按一下 [一般] 索引標籤。
    2.  在 **姓氏** 的文字方塊中 **一般** 區段中，輸入您想要佈建的有效 Azure Active Directory 帳戶的姓氏。
    3.  選取 **網站** 中的帳戶 **位置** 一節。
    4.  在 **登入名稱** 的文字方塊中 **TOPdesk 登入** 區段中，輸入您的使用者的登入名稱。
    5.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 TOPdesk-安全的使用者帳戶建立工具或 TOPdesk-Secure 佈建 AAD 使用者帳戶所提供的 Api。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 TOPdesk - Secure，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * TOPdesk-Secure * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
