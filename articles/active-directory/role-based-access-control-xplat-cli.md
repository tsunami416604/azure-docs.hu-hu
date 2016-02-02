<properties
    pageTitle="使用 Azure 命令列介面管理角色型存取控制"
    description="使用 Azure 命令列介面管理角色型存取控制"
    services="active-directory"
    documentationCenter="na"
    authors="IHenkel"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/14/2015"
    ms.author="inhenk"/>


# 使用 Azure 命令列介面 (Azure CLI) 管理角色型存取控制

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](/role-based-access-control-xplat-cli-install.md)


Azure 入口網站以及 Azure 資源管理員 API 裡的角色型存取控制 (RBAC) 功能，可讓您深入地管理訂用帳戶與資源的存取。 透過這項功能，您可以為 Active Directory 使用者、群組或是服務主體指派特定範圍的一些角色，藉此賦予其存取權限。

在本教學課程中，您將學習如何使用 Azure CLI 來管理角色型存取控制。 本課程將帶您逐一了解建立與檢查角色指派的程序。

**預估完成時間：**15 分鐘

## 必要條件

在使用 Azure CLI 來管理 RBAC 之前，您必須具備以下項目：

- Azure CLI 0.8.8 版或更新版本。
- 

## 本教學課程內容

* 
* 
* 
* 
* 

## 

由於 RBAC 只能搭配 Azure 資源管理員一起運作，因此您必須先切換至 Azure 資源管理員模式。 輸入：

    azure config mode arm



若要連線至您的 Azure 訂閱，請輸入：

    azure login -u <username>

在命令列提示字元中，輸入您的 Azure 帳戶密碼 (僅使用組織帳戶)。 Azure CLI 會取得您在此帳戶上的所有訂閱項目，並將自己預設為使用第一個訂閱。 請注意，使用角色型存取控制時，只有當您具備共同管理員身分或是具有某些角色指派時，才能取得訂閱項目的一些權限。

如果您有多個訂閱，而且想要切換至另一個訂閱，請輸入：

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>



## 

現在，我們來看看訂閱中已有哪些角色指派。 輸入：

    azure role assignment list

如此會傳回訂閱中的所有角色指派。 請注意以下兩點：

1. 您需要擁有訂閱層級的讀取權限。
2. 如果訂閱內含許多角色指派，您可能需要一點時間才能取得所有訂閱資料。

您也可以針對特定使用者，檢查特定範圍內特定角色定義的現有角色指派。 輸入：

    azure role assignment list -g group1 --upn <user email> -o Owner

此指令會針對 Azure AD 目錄中具有資源群組「group1」之「Owner」角色指派的特定使用者，傳回該特定使用者的所有角色指派。 角色指派可能來自兩個地方：

1. 針對資源群組使用者的「Owner」角色指派。
2. 針對資源群組父系使用者的「Owner」角色指派 (在此案例中為該訂閱)，因為如果您在特定父系資源層級具有權限的話，您將對其所有子系具有相同的權限。

此 Cmdlet 的所有參數都是選擇性參數。 您可以組合運用這些參數與不同的篩選器，來檢查角色指派。

## 

若要建立角色指派，您需要思考：

- 要將角色指派給誰：您可以使用下列 Azure Active Directory Cmdlet 來查看目錄中有哪些使用者、群組及服務主體。

    ```
    azure ad user list  
    azure ad user show  
    azure ad group list  
    azure ad group show  
    azure ad group member list  
    azure ad sp list  
    azure ad sp show  
    ```

- 您想要指派什麼角色：您可以使用下列 Cmdlet 來查看支援的角色定義。

    

- 您想要指派的範圍：有三個範圍層級：

    - 目前的訂用帳戶
    - 資源群組。
    - 資源。

例如：

 - 此舉會在目前的訂閱層級中，為使用者建立讀取者的角色指派：

  

- 此舉會在資源群組層級中建立角色指派：



- 此舉會在資源層級中建立角色指派：



## 

確認您的帳戶內含一些角色指派之後，就能執行下列指令來實際看到這些角色指派授予您的權限：

    PS C:\> azure group list
    PS C:\> azure resource list

這兩組 Cmdlet 只會傳回您擁有讀取權限的資源群組或資源。 而且會同時顯示您擁有的權限。



## 

若要深入了解使用 Azure CLI 管理角色型存取控制的詳細資訊，請參閱下列相關主題：

- 
- 
- 
- 
- 
- 
- 





