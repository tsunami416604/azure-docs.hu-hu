<properties 
    pageTitle="教學課程：Azure Active Directory 與 ShiftPlanning 整合 | Microsoft Azure" 
    description="了解如何使用 ShiftPlanning 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 ShiftPlanning 整合
  
本教學課程的目的是要示範 Azure 與 ShiftPlanning 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   已啟用 ShiftPlanning 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 ShiftPlanning 的 Azure AD 使用者將能夠登入位於您 ShiftPlanning 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ShiftPlanning 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenario")
##啟用 ShiftPlanning 的應用程式整合
  
本節的目的是要說明如何啟用 ShiftPlanning 的應用程式整合。

###若要啟用 ShiftPlanning 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **ShiftPlanning**。

    ![應用程式庫](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **ShiftPlanning**, ，然後按一下 [ **完成** 加入應用程式。

    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 ShiftPlanning 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **ShiftPlanning** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 ShiftPlanning** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **ShiftPlanning 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.shiftplanning.com/includes/saml/*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configure App URL")

4.  在 **設定單一登入在 ShiftPlanning** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，登入您 **ShiftPlanning** 系統管理員身分的公司網站。

6.  在頂端功能表中，按一下 [ **管理員**。

    ![Admin](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

7.  在 **整合**, ，按一下 [ **單一登入**。

    ![單一登入](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")

8.  在 **單一登入** 區段中，執行下列步驟:

    ![單一登入](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")

    1.  選取 **已啟用 SAML**。
    2.  選取 **允許密碼登入**
    3.  在 Azure 網站中，在 **ShiftPlanning 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **SAML 核發者 URL** 文字方塊。
    4.  在 Azure 網站中，在 **ShiftPlanning 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **遠端登出 URL** 文字方塊。
    5.  建立 **base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP]如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **X.509 憑證** 文字方塊
    7.  按一下 [ **儲存設定**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configure Single Sign-On")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 ShiftPlanning，則必須將他們佈建至 ShiftPlanning。  
ShiftPlanning 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **ShiftPlanning** 系統管理員身分的公司網站。

2.  按一下 [ **管理員**。

    ![Admin](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

3.  按一下 [ **人員**。

    ![職員](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Staff")

4.  在 **動作**, ，按一下 [ **加入員工**。

    ![新增員工](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Add Employees")

5.  在 **新增員工** 區段中，執行下列步驟:

    ![儲存員工](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Save Employees")

    1.  型別 **名字**, ，**姓氏** 和 **電子郵件** 您想要佈建到相關文字方塊之有效 AAD 帳戶。
    2.  按一下 [ **儲存員工**。

>[AZURE.NOTE]您可以使用任何其他 ShiftPlanning 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 ShiftPlanning 所提供。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 ShiftPlanning，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * ShiftPlanning * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
