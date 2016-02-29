<properties 
    pageTitle="教學課程：Azure Active Directory 與 SuccessFactors 整合 | Microsoft Azure"
    description="了解如何使用 SuccessFactors 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 SuccessFactors 整合
  
本教學課程的目標在於說明 Azure 與 SuccessFactors 中的整合 **SP 起始單一登入模式**。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   在 [服務提供者起始模式] 中啟用 SuccessFactors 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 SuccessFactors 的 Azure AD 使用者將能夠登入位於您 SuccessFactors 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 SuccessFactors 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-successfactors-tutorial/IC791135.png "Scenario")

##啟用 SuccessFactors 的應用程式整合
  
本節的目的是概述如何啟用 SuccessFactors 的應用程式整合。

###若要啟用 SuccessFactors 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-successfactors-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-successfactors-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-successfactors-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **SuccessFactors**。

    ![應用程式庫](./media/active-directory-saas-successfactors-tutorial/IC791136.png "Appliation Gallery")

7.  在 [結果] 窗格中，選取 **SuccessFactors**, ，然後按一下 [ **完成** 加入應用程式。

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##設定單一登入
  
本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 SuccessFactors 中進行驗證。
  
若要設定單一登入，您必須連絡您的 SuccessFactors 支援小組。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **SuccessFactors** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 SuccessFactors** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，執行下列步驟，然後按 **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-successfactors-tutorial/IC791140.png "Configure App URL")

    1.  在 **SuccessFactors 登入 URL** 文字方塊中，輸入您的 URL，讓使用者中用來登入 SuccessFactors 應用程式 (例如: 「*https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*」)。
    2.  在 **SuccessFactors 轉送 URL** 文字方塊中，輸入 **https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**。

        >[AZURE.NOTE] 這個值會是暫時預留位置。  
        >您會從 SuccessFactors 支援小組取得實際的值。  
        >稍後在本教學課程中，您會找到連絡 SuccessFactors 支援小組的指示。  
        >在此交談內容中，您會收到實際 SuccessFactors 回覆 URL。

4.  在 **在 SuccessFactors 單一登入設定** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Configure Single Sign-On")

5.  若要設定以 SAML 為基礎的單一登入，請連絡您的 SuccessFactors 支援小組，並提供下列項目：

    1.  下載的憑證
    2.  遠端登入 URL
    3.  遠端登出 URL

    >[AZURE.IMPORTANT] 請要求您的 SuccessFactors 支援團隊以將 NameId 格式參數設定為 「*未指定*」。

    Successfactors 支援團隊會傳送給您正確 **Successfactors 轉送 URL** 需 **設定應用程式 URL** ] 對話方塊。

6.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Configure Single Sign-On")

##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 SuccessFactors，必須將他們佈建到 SuccessFactors。  
SuccessFactors 需以手動方式佈建。
  
若要在 SuccessFactors 建立使用者，您需要連絡 SuccessFactors 支援小組。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 SuccessFactors，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * SuccessFactors * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-successfactors-tutorial/IC791143.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-successfactors-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
