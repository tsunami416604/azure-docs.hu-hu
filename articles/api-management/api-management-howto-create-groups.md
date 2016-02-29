<properties 
    pageTitle="如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶" 
    description="了解如何在 Azure API 管理中使用群組來管理管理開發人員帳戶。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="sdanie"/>

# 如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶

在 API 管理中，群組的作用是管理產品對於開發人員的可見度。 產品是先設為可讓群組看見，然後群組中的開發人員就能檢視和訂閱與群組相關聯的產品。 

API 管理具有下列不可變的系統群組。

-   **系統管理員** -Azure 訂閱管理員是此群組的成員。 管理員可管理 API 管理服務執行個體、建立開發人員所使用的 API、作業和產品。
-   **開發人員** -已驗證的開發人員入口網站使用者屬於此群組。 開發人員是使用您的 API 建置應用程式的客戶。 開發人員獲授與開發人員入口網站的存取權，並建置呼叫 API 作業的應用程式。
-   **來賓** -未經驗證的開發人員入口網站的使用者，例如潛在客戶造訪開發人員入口網站的 API 管理執行個體會歸類到這個群組。 他們可獲得特定唯讀存取權限，例如他們可檢視 API 但無法進行呼叫。

除了這些系統群組，系統管理員可以建立自訂群組或 [運用 Azure Active Directory 相關租用戶中的外部群組][]。 自訂群組和外部群組可以與系統群組一起使用，提供開發人員 API 產品的能見度及存取權。 例如，您可以為與特定夥伴組織有關的開發人員建立一個自訂群組，並只允許他們存取來自含相關 API 之產品的 API。 使用者可以是多個群組的成員。

本指南說明 API 管理執行個體的管理員如何加入新的群組，並將這些群組與產品和開發人員建立關聯。

>[AZURE.NOTE] 除了建立並管理發行者入口網站中的群組，您可以建立和管理群組使用 API 管理 REST API [群組](https://msdn.microsoft.com/library/azure/dn776329.aspx) 實體。

## <a name="create-group"> </a>建立群組

若要建立新的群組，請按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。 這會帶您前往 API 管理發行者入口網站。

![發佈者入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

按一下 [ **群組** 從 **API 管理** 左邊的功能表，然後按一下 **加入群組**。

![Add new group][api-management-add-group]

輸入唯一的名稱和選擇性描述，然後按一下 [ **儲存**。

![Add new group][api-management-add-group-window]

新群組會顯示在 [群組] 索引標籤中。 若要編輯 **名稱** 或 **描述** 的群組中，按一下清單中群組的名稱。 若要刪除群組，按一下 [ **刪除**。

![Group added][api-management-new-group]

現在已建立群組，它可以與產品和開發人員建立關聯。

## <a name="associate-group-product"> </a>將群組與產品建立關聯

若要將群組與產品建立關聯，按一下 [ **產品** 從 **API 管理** 左邊的功能表，然後按一下所需產品的名稱。

![Set visibility][api-management-add-group-to-product]

選取 **可視性** 索引標籤來新增和移除群組，以及檢視產品的目前群組。 新增或移除群組，請核取或取消選取所需群組的核取方塊，按一下 **儲存**。

![Set visibility][api-management-add-group-to-product-visibility]

>[AZURE.NOTE] 若要加入 Azure Active Directory 群組，請參閱 [如何授權開發人員帳戶在 Azure API 管理中使用 Azure Active Directory](api-management-howto-aad.md)。
>
>若要設定群組 **可視性** 產品] 索引標籤上，按一下 [ **管理群組**。

當產品與群組建立關聯之後，該群組中的開發人員就可以檢視和訂閱產品。

## <a name="associate-group-developer"> </a>將群組與開發人員建立關聯

若要將群組與開發人員建立關聯，按一下 [ **使用者** 從 **API 管理** 左邊的功能表，然後檢查您想要與群組相關聯的開發人員旁邊的方塊。

![Add developer to group][api-management-add-group-to-developer]

所需的開發人員會核取之後，按一下所需的群組中 **新增到群組** 下拉式清單。 開發人員可以從群組移除使用 **從群組移除** 下拉式清單。 

![開發人員][api-management-add-group-to-developer-saved]

開發人員與群組之間加入關聯後，您可以檢視在 **使用者** ] 索引標籤。


## <a name="next-steps"> </a>後續步驟

-   當開發人員加入至群組之後，他們就可以檢視和訂閱與該群組相關聯的產品。 如需詳細資訊，請參閱 [如何建立和發行產品，在 Azure API 管理][],，
-   除了建立並管理發行者入口網站中的群組，您可以建立和管理群組使用 API 管理 REST API [群組](https://msdn.microsoft.com/library/azure/dn776329.aspx) 實體。


[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group

