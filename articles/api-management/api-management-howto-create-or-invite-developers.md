<properties 
    pageTitle="如何在 Azure API 管理中管理使用者帳戶" 
    description="了解如何在 Azure API 管理中建立或邀請使用者" 
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

# 如何在 Azure API 管理中管理使用者帳戶

在 API 管理中，開發人員是指您使用 API 管理所公開之 API 的使用者。 本指南示範如何建立並邀請開發人員使用您以 API 管理執行個體提供給他們的 API 和產品。

## <a name="create-developer"> </a>建立新的開發人員

若要建立新的開發人員，請按一下 [ **管理** API 管理服務的 Azure 傳統入口網站中。 這會帶您前往 API 管理發行者入口網站。 如果您尚未建立 API 管理服務執行個體，請參閱 [建立 API 管理服務執行個體][] 中 [開始使用 Azure API 管理][] 教學課程。

![發佈者入口網站][api-management-management-console]

按一下 [ **使用者** 從 **API 管理** 左邊的功能表，然後按一下 **將使用者加入**。

![Create developer][api-management-create-developer]

輸入 **電子郵件**, ，**密碼**, ，和 **名稱** 做為新的開發人員按一下 **儲存**。

![Create developer][api-management-add-new-user]

根據預設，新建立的開發人員帳戶為 **Active**, ，相關聯 **開發人員** 群組。

![New developer][api-management-new-developer]

開發人員帳戶中的 **active** 狀態可以用來存取所有他們已訂閱的 Api。 若要將新建立的開發人員與其他群組建立關聯，請參閱 [如何將群組與開發人員][]。

## <a name="invite-developer"> </a>邀請開發人員

若要邀請開發人員，請按一下 [ **使用者** 從 **API 管理** 左邊的功能表，然後按一下 **邀請使用者**。

![Invite developer][api-management-invite-developer]

輸入名稱和電子郵件地址，開發人員，然後按一下 [ **邀請**。

![Invite developer][api-management-invite-developer-window]

這時會顯示確認訊息，但剛獲邀的開發人員在尚未接受邀請之前，都不會出現在清單中。 

![Invite confirmation][api-management-invite-developer-confirmation]

邀請開發人員時，有一封電子郵件會傳送給開發人員。 此電子郵件是以範本產生，可自訂。 如需詳細資訊，請參閱 [設定電子郵件範本][]。

接受邀請時，帳戶就會變成作用中。

## <a name="block-developer"> </a> 停用或重新啟用開發人員帳戶

根據預設，新建立或邀請的開發人員帳戶為 **Active**。 若要停用開發人員帳戶，請按一下 [ **區塊**。 若要重新啟用已封鎖的開發人員帳戶，請按一下 [ **啟動**。 已封鎖的開發人員帳戶無法存取開發人員入口網站或呼叫任何 API。

![Block developer][api-management-new-developer]

## <a name="next-steps"> </a>後續步驟

建立開發人員帳戶之後，您可以將它與角色建立關聯，並讓它訂閱產品和 API。 如需詳細資訊，請參閱 [如何建立和使用群組][]。


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-]: ./media/api-management-howto-create-or-invite-developers/api-management-.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
