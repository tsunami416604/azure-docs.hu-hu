<properties
   pageTitle="在 AAD 中建立工作或學校身分識別 | Microsoft Azure"
   description="了解如何在 Azure Active Directory 中建立工作或學校身分識別，以搭配使用資源管理員和傳統部署模型。"
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="12/08/2015"
   ms.author="rasquill"/>

# 在 Azure Active Directory 中建立工作或學校身分識別

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果您建立個人的 Azure 帳戶，或有個人的 MSDN 訂用帳戶並建立 Azure 帳戶來運用 MSDN Azure 點數--您已使用 *Microsoft 帳戶* 身分識別建立。 Azure 有許多絕佳功能 [資源群組範本](../resource-group-overview.md) 是一個範例-需要工作或學校帳戶 (由 Azure Active Directory 管理的身分識別) 才能運作。 幸運的是，預設的 Azure Active Directory 網域會提供您個人的 Azure 帳戶，讓您可以用來建立新的工作或學校帳戶，以搭配需要這類帳戶使用的 Azure 功能，因此您可以遵循下列指示來建立新的工作或學校帳戶。

不過，最近的變更讓您管理您的 Azure 帳戶使用任何類型的訂閱 `azure login` 所述的互動式登入方法 [這裡](../xplat-cli-connect.md)。 您可以使用同樣的機制，或者可以遵循接下來的指示。

> [AZURE.NOTE] 如果您已提供系統管理員使用者名稱和密碼，很可能您已經有工作或學校識別碼 (有時也稱為 *組織識別碼*)。 如果是這樣，您可以立即開始使用您的 Azure 帳戶來存取需要該帳戶的 Azure資源。 如果您發現無法使用這些資源，您可能需要返回本文以尋求協助。 如需詳細資訊，請參閱 [登入的帳戶，您可以使用](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) 和 [Azure 訂用帳戶與 Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir)。

步驟很簡單。 您必須在 Azure 傳統入口網站中找到您已註冊的身分識別、找出您的預設 Azure Active Directory 網域，然後以 Azure 共同管理員的身分新增網域的新使用者。

## 在 Azure 傳統入口網站中找到您的預設目錄

啟動登入 [Azure 傳統入口網站](https://manage.windowsazure.com) 使用個人的 Microsoft 帳戶身分識別。 您登入後，向下捲動左側的藍色面板，然後按一下 **ACTIVE DIRECTORY**。

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

讓我們開始在 Azure 中找出您身分識別的部分資訊。 您的主窗格應如下所示，顯示您有一個預設目錄。

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

讓我們找到更多的相關資訊。 按一下預設的目錄列，會將您帶往預設的目錄屬性。  

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

若要檢視的預設網域名稱，請按一下 [ **網域**。

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

您應該能夠在這裡看到 Azure 帳戶的建立時間，以及 Azure Active Directory 建立了個人的預設網域，此網域是用來做為 onmicrosoft.com 子網域的個人識別碼雜湊值 (這是從文字字串中產生的數字)。 這是您現在要新增新使用者的網域。

## 在預設網域中建立新的使用者

按一下 [ **使用者** ，並尋找您單一個人帳戶。 您應該會看到在 **源自** 資料行，它是 **Microsoft 帳戶**。 我們想要在您的預設 .onmicrosoft.com Azure Active Directory 網域中建立使用者。

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

我們將遵循 [這些指示](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) 接下來的幾個步驟，但會使用特定的範例。

在頁面底部，按一下 [ **+ 新增使用者**。 在出現的頁面中，輸入新的使用者名稱，並讓 **使用者類型**  **貴組織中的新使用者**。 在此範例中，使用者名稱為 `ahmet`。 選取您先前找到的預設網域，以做為 ahmet 電子郵件地址的網域。 完成時按一下 [下一步] 箭頭。

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Ahmet，新增更多詳細資料，但請務必選取適當 **角色** 值。 它很容易使用 **全域管理員** 才能確定事情一切，但如果您可以使用較少的角色，這是個不錯的主意。 這個範例會使用 **使用者** 角色。 (深入了解在 [系統管理員角色的權限](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1)。)除非您想要針對運作中的每個記錄使用多重要素驗證，否則請勿啟用多重要素驗證。 當您完成時，按一下 [下一步] 箭頭。

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

按一下 [ **建立** ] 按鈕以產生並顯示 ahmet 的暫時密碼。

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

複製使用者名稱的電子郵件地址，或使用 **傳送密碼透過電子郵件**。 您需要該資訊才能立即登入。

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

現在您應該會看到新的使用者， **Ahmet 開發人員**, ，源自 Azure Active Directory。 您已使用 Azure Active Directory 建立新的工作或學校身分識別。 不過，這個身分識別還沒有使用 Azure 資源的權限。

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

如果您使用 **傳送密碼透過電子郵件**, ，以下類型的電子郵件傳送。

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## 為訂用帳戶新增 Azure 的共同管理員權限

現在您必須將新的使用者新增成為訂用帳戶的共同管理員，新的使用者才能夠登入管理入口網站。 若要這樣做，請在左下角面板中按一下 **設定**。

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

在主要設定區域中，按一下 [ **管理員** 最上層，以及您應該會看到只個人 Microsoft 帳戶身分識別。 在頁面底部，按一下 [ **+ 加入** 以指定共同管理員。 在此處輸入您所建立新使用者的電子郵件地址，包括預設網域。 如下一個螢幕擷取畫面所示，綠色勾號會出現在預設目錄的使用者旁邊。 請記得選取您想要讓此使用者能夠管理的所有訂用帳戶。

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

完成後，您現在應會看到兩個使用者，包括新的共同管理員身分識別。 登出入口網站。

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## 登入並變更新使用者的密碼

以您建立的新使用者身分登入。

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

會立即提示您建立新的密碼。

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

您應該會看到如下的成功獎勵。

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## 後續步驟

您現在可以使用新的 Azure Active Directory 身分識別來使用 [Azure 資源群組範本](../xplat-cli-azure-resource-manager.md)。

    azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

