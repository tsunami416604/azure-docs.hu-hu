<properties
    pageTitle="如何新增或變更 Azure 共同管理員、 服務系統管理員及帳戶管理員 |Microsoft Azure"
    description="說明如何新增或變更 Azure 共同管理員、服務管理員和帳戶管理員"
    services="billing"
    documentationCenter=""
    authors="genlin"
    manager="jarrettr"
    editor="meerak"
    tags="billing"
    />

<tags
    ms.service="billing"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/11/2015"
    ms.author="genli"/>

# 如何新增或變更 Azure 共同管理員、 服務管理員和帳戶管理員
## 系統管理員角色

Microsoft Azure 中的管理員角色有三種：

| 管理角色   | 限制  | 說明
| ------------- | ------------- |---------------|
|帳戶系統管理員  | 每個 Azure 帳戶 1 名  |這是註冊或購買 Azure 訂用帳戶的人員，經授權可存取帳戶中心和執行各種管理工作。 包括能夠建立訂用帳戶、取消訂用帳戶、變更訂用帳戶的計費方式以及變更服務管理員。
| 服務管理員 | 每個 Azure 訂用帳戶 1 名  |此人員經授權可存取所指派帳戶中所有訂用帳戶的 Azure 管理入口網站。 根據預設，新訂用帳戶的帳戶管理員也是服務管理員。|
|共同管理員|每個訂用帳戶 200 名 (服務管理員除外)|此人員的存取權限與服務管理員相同，但無法變更訂用帳戶與 Azure 目錄的關聯。|

## 加入訂用帳戶的共同管理員
1. 登入 [Azure 管理入口網站](https://manage.windowsazure.com/)。
2. 在導覽窗格中，選取 **設定**> **管理員**> **新增**。 </br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. 輸入您想要加入共同管理員的身分，然後選取您要共同管理員來存取訂閱者的電子郵件地址。</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

下列電子郵件地址可以新增為共同管理員：

* **Microsoft 帳戶** (先前稱為 Windows Live ID) </br>
 您可以使用 Microsoft 帳戶登入所有消費者導向的 Microsoft 產品和雲端服務，例如 Outlook (Hotmail)、 Skype (MSN)、 OneDrive、 Windows Phone 和 Xbox LIVE。
* **組織帳戶**</br>
 組織帳戶是建立在 Azure Active Directory 的帳戶。 組織帳戶地址如下所示:
    @ 使用者<your domain>。 onmicrosoft.com

**注意**

 * 如果您使用 Microsoft 帳戶登入，您可以只新增其他 Microsoft 帳戶共同管理員的身分。 這是安全性考量，以避免非組織帳戶探索如果某些帳戶 (例如 janedoe@contoso.com) 都是有效的帳戶。
 * 如果您以組織帳戶登入，就可以將組織中的其他組織帳戶新增為共同管理員。 比方說，abby@contoso.com 可將 bob@contoso.com 新增為服務系統管理員或共同管理員，但無法新增 john@notcontoso.com。 以組織帳戶登入的使用者，可以繼續將 Microsoft 帳戶使用者新增為服務管理員或共同管理員。
 * 現在可以使用組織帳戶登入至 Azure，以下是服務管理員和共同管理員帳戶需求的變更：

| 登入方法| 是否能將 Microsoft 帳戶新增為共同管理員或服務管理員？  |是否能在同一組織中將組織帳戶新增為共同管理員或服務管理員？ |是否能在不同組織中將組織帳戶新增為共同管理員或服務管理員？
| ------------- | ------------- |---------------|---------------|
|Microsoft 帳戶 |是|否|否|
|組織帳戶|是|是|否|

## 變更訂用帳戶的服務管理員
只有帳戶管理員可以變更訂用帳戶的服務管理員。

1. 登入 [帳戶管理入口網站](https://account.windowsazure.com/subscriptions) 使用 「 帳戶管理員。
2. 選取您想變更的訂用帳戶。
3. 在右側，按一下 [ **編輯訂閱** 詳細資料。 </br>
![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. 在 **服務系統管理員** 方塊中，輸入電子郵件地址的新服務的系統管理員。 ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## 變更帳戶管理員

若要將 Azure 帳戶的擁有權轉移給其他帳戶，請參閱 [傳輸 Azure 訂用帳戶](../billing-subscription-transfer.md)。

