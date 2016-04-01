<properties 
    pageTitle="教學課程：Azure Active Directory 與 Zendesk 整合 | Microsoft Azure" 
    description="了解如何使用 Zendesk 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Zendesk 整合
  
本教學課程的目的是要示範 Azure 與 Zendesk 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Zendesk 租用戶
  
完成本教學課程之後, 您已指派給 Zendesk 的 Azure AD 使用者將能夠登入您的 Zendesk 公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Zendesk 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario")

##啟用 Zendesk 的應用程式整合
  
本節的目的是要說明如何啟用 Zendesk 的應用程式整合。

###若要啟用 Zendesk 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Zendesk**。

    ![應用程式庫](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Zendesk**, ，然後按一下 [ **完成** 加入應用程式。

    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Zendesk。  
設定 Zendesk 的單一登入需要您從憑證抓取憑證指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Zendesk** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![單一登入](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Single sign-on")

2.  在 **您希望使用者如何登入 Zendesk** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Zendesk 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>.zendesk.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configure app URL")

4.  在 **Zendesk 在設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機憑證檔當做 **c:\\zendesk.cer**。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configure single sign-on")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Zendesk 公司網站。

6.  按一下 [ **管理員**。

7.  在左的導覽窗格中，按一下 [ **設定**, ，然後按一下 [ **安全性**。

    ![安全性](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Security")

8.  在 **安全性** 頁面上，選取 **系統管理員和代理程式** ] 索引標籤。

9.  選取 **單一登入 (SSO) 和 SAML**, ，然後選取 **SAML**。

10. 在 Azure AD 入口網站上 **設定單一登入在 Zendesk** 頁面中，複製 **SAML SSO URL** 值，並接著將它貼入 **SAML SSO URL** 文字方塊。

11. 在 Azure AD 入口網站上 **設定單一登入在 Zendesk** 頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **遠端登出 URL** 文字方塊。

    ![單一登入](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Single sign-on")

12. 複製 **指紋** 值匯出的憑證，並接著將它貼入 **憑證指紋** 文字方塊。

    >[AZURE.TIP] 如需詳細資訊，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

13. 按一下 [ **儲存**。

14. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configure single sign-on")

##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 **Zendesk**, ，您必須佈建到 **Zendesk**。  
如果是 **Zendesk**, ，佈建須手動進行。

###若要佈建使用者帳戶到 Zendesk，請執行下列步驟：

1.  登入您 **Zendesk** 租用戶。

2.  選取 **客戶清單** ] 索引標籤。

3.  選取 **使用者** 索引標籤，然後按一下 [ **新增**。

    ![新增使用者](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Add user")

4.  輸入您想要佈建，然後再按一下的現有 Azure AD 帳戶的電子郵件地址 **儲存**。

    ![新增使用者](./media/active-directory-saas-zendesk-tutorial/IC773633.png "New user")

>[AZURE.NOTE] 您可以使用任何其他 Zendesk 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Zendesk。

##指派使用者
  
若要測試您的組態，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Zendesk，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Zendesk * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

