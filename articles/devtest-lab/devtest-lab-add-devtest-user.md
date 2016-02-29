    <properties
    pageTitle="Add owners and users to a DevTest Lab | Microsoft Azure"
    description="Securely add a user who is not in your subscription to your Azure DevTest Lab."
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/01/2015"
    ms.author="tarcher"/>

# 將擁有者和使用者加入研發/測試實驗室

## 概觀

研發/測試實驗室的存取權是由 Azure 角色型存取控制 (RBAC) 所控制。 搜尋 [角色為基礎的存取控制 (RBAC)](https://azure.microsoft.com/searchresults?query=Role%20Based%20Access%20Control%20%28RBAC%29) 在 Azure 預覽入口網站中，若要深入了。

您可以透過下列兩個角色，將存取權授與您的研發/測試實驗室：

 - **擁有者**: 使用者指派給 **擁有者** Azure 訂用帳戶層級角色可以存取實驗室中，包括管理和監視功能。

     > [AZURE.NOTE] **擁有者** DevTest 實驗室中不支援在訂閱層級以外的 RBAC 層級指派的角色。 若要為使用者指派 **擁有者** DevTest 實驗室中的角色不支援。

 -  **DevTest 實驗室使用者**: 使用者指派給 **DevTest 實驗室使用者** 角色可以建立、 更新和刪除指定的實驗室中的 Vm。 使用者可以 *內部* (Azure Active Directory 訂用帳戶的成員)，或 *外部* (不是 Azure AD 中，例如夥伴組織的成員的成員的使用者)。
    -  A **DevTest 實驗室使用者** 必須透過指派角色 **新增使用者** 實驗室的磚。
    -  中的使用者 **DevTest 實驗室使用者** 角色可以執行這些作業僅內部指派給 「 實驗室。  
    例如， **DevTest 實驗室使用者** 無法建立虛擬機器使用虛擬機器服務的訂閱。 只允許從研發/測試實驗室帳戶建立虛擬機器。
    - *外部* 使用者必須擁有帳戶的其中一個 Microsoft 帳戶網域 (也就是 @hotmail.com、 @live.com、 @msn.com、 @passport.com、 @outlook.com 或特定國家/地區的任何變數)。

## 將擁有者加入您的實驗室

研發/測試實驗室會考慮將包含實驗室的 Azure 訂用帳戶擁有者當成這些實驗室的擁有者。 雖然您可以透過 Azure Preview 入口網站中實驗室的刀鋒視窗，將額外的擁有者加入研發/測試實驗室，但目前不支援此功能。 

若要將擁有者加入您已在其中建立實驗室或將在其中建立新實驗室的 Azure 訂用帳戶，請遵循下列步驟：

1. 登入 [Azure 預覽入口網站](http://portal.azure.com)。

1. 在左側導覽中，點選 **訂閱**。

    ![訂用帳戶連結](./media/devtest-lab-add-devtest-user/subscriptions.png)
    
1. 點選將包含實驗室的訂用帳戶。

1. 點選 **存取** 圖示。 

    ![存取使用者](./media/devtest-lab-add-devtest-user/access-users.png)

1. 在 **使用者** 刀鋒視窗中，點選 **新增**。

    ![新增使用者](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. 在 **選取角色** 刀鋒視窗中，點選 **擁有者**。

1. 輸入 **使用者** ] 文字方塊的使用者想要新增為擁有者的電子郵件。 如果找不到該使用者，您將會收到錯誤訊息來說明問題。 如果找到使用者，則該使用者資料庫會列示於 **使用者** 文字方塊。

1. 點選找到的使用者名稱。

1. 點選 **選取**。

1. 點選 **確定** 關閉 **新增存取** 刀鋒視窗。

1. 當您返回 **使用者** 刀鋒視窗中，您會看到使用者都已經加入成為一位擁有者。 這位人員現在將是此訂用帳戶下方建立之所有實驗室的擁有者，因而能夠執行擁有者工作。 

## 將研發/測試實驗室使用者加入您的實驗室

若要將研發/測試實驗室使用者加入您的實驗室，請遵循下列步驟：

1. 登入 [Azure 預覽入口網站](http://portal.azure.com)。

1. 點選 **瀏覽**。

1. 點選 **DevTest 實驗室**。

1. 從實驗室清單中，點選所需的實驗室。   

1. 點選 **存取** 圖示。

    ![使用者存取](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. 在 **使用者** 刀鋒視窗中，點選 **新增**。

    ![新增使用者](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. 在 **選取角色** 刀鋒視窗中，點選 **DevTest 實驗室使用者**

1. 在 **將使用者新增** ] 刀鋒視窗中:

    1.  **將使用者新增** ] 刀鋒視窗會顯示內建的使用者清單。 如果所需的使用者已在清單中，您只需點選該使用者列來選取它。 使用者左側出現核取記號表示已選取該使用者。 若要選取多個使用者，請按住 **& l t;Ctrl >** 鍵同時按一下每個使用者。 若要取消選取使用者，請按住 **& l t;Ctrl >** 鍵，然後按一下 [使用者]。 刀鋒視窗底部的計數器表示所選取的使用者數目。

    1. 如果所需的使用者不在清單中，請輸入有效的 Microsoft 電子郵件帳戶中 **使用者** 文字方塊。 如果電子郵件地址是有效的使用者將會顯示以下 **使用者** 文字方塊。 只需點選它就能加以選取。   

    1. 一旦選取您想要新增的實驗室中，點選的使用者 **選取**。

    1. 點選 **確定** 關閉 **新增存取** 刀鋒視窗。

    1.  **使用者** 刀鋒視窗會顯示新增的角色及使用者。
