<properties
    pageTitle="處理行動服務 (iOS) 中的離線資料衝突 | 行動開發人員中心"
    description="了解您在 iOS 應用程式中同步離線資料時如何使用 Azure 行動服務處理衝突"
    documentationCenter="ios"
    authors="krisragh"
    manager="dwrede"
    editor=""
    services="mobile-services"/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2015"
    ms.author="krisragh;donnam"/>



# 處理行動服務中的離線資料同步衝突

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

本主題將說明在使用 Azure 行動服務的離線功能時，應如何同步處理資料及處理衝突。 本教學課程 [開始使用離線資料] 教學課程為基礎。
>[AZURE.NOTE] 若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。


## 下載 iOS 專案

本教學課程中，下載 [從 Github 更新的 Xcode 專案](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS)。 我們已經從 [開始使用離線資料] 教學課程結尾的 Xcode 專案做為起點，並再更新它允許您編輯的項目。 我們也已加入支援類別和方法，以便在下一節中新增衝突處理常式。

在本教學課程結尾處如果您執行此應用程式兩個電話變更這兩種行動電話在本機上的對相同項目和變更推送回伺服器，您將允許使用者選擇要保存哪個版本每支電話:
  * 保留用戶端版本 (這會覆寫伺服器上的版本)、
  * 保留伺服器版本 (這會更新用戶端本機資料表)，或
  * 兩個版本都不保留 (取消發送並擱置作業)。

現在我們要新增衝突處理常式，以啟用這項功能。

## <a name="add-conflict-handling"></a>將衝突處理常式新增至待辦事項清單檢視控制器

1. 在 **QSTodoListViewController.m** 中，編輯 **viewDidLoad**。 以對 **defaultServiceWithDelegate** 的呼叫取代對 **defaultService** 的呼叫︰

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. 在 **QSTodoListViewController.h** 中，將 **&lt;MSSyncContextDelegate&gt;** 新增至介面宣告，以實作 **MSSyncContextDelegate** 通訊協定。

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. 將下列匯入陳述式新增至 **QSTodoListViewController.m** 頂端：

        #import "QSUIAlertViewWithBlock.h"

4. 最後，我們要將下列兩項作業新增至 **QSTodoListViewController.m**，以使用此協助程式類別，並提示使用者以三種方式之一來協調衝突。

     - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
     {
         [self doOperation:operation complete:completion];
     }
    
     -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
     {
         [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {
    
             NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
    
             if (error.code == MSErrorPreconditionFailed) {
                 QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                     if (buttonIndex == 1) { // Client
                         NSMutableDictionary *adjustedItem = [operation.item mutableCopy];
    
                         [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                         operation.item = adjustedItem;
    
                         [self doOperation:operation complete:completion];
                         return;
    
                     } else if (buttonIndex == 2) { // Server
                         NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                         completion(serverItem, nil);
                     } else { // Cancel
                         [operation cancelPush];
                         completion(nil, error);
                     }
                 }];
    
                 NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];
    
                 [alert showAlertWithTitle:@"Server Conflict"
                                   message:message
                         cancelButtonTitle:@"Cancel"
                         otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
             } else {
                 completion(item, error);
             }
         }];
     }


## <a name="test-app"></a>測試應用程式

我們要對含有衝突的應用程式進行測試。 請在兩個同時執行或使用應用程式和 REST 用戶端的不同應用程式執行個體中，編輯相同的項目。

請從頂端拖曳，以在應用程式執行個體中執行重新整理動作。 您現在會看見調解衝突的提示︰

![][conflict-ui]




[update the app project to allow editing]: #update-app 
[update todo list view controller]: #update-list-view 
[add todo item view controller]: #add-view-controller 
[add todo item view controller and segue to storyboard]: #add-segue 
[add item details to todo item view controller]: #add-item-details 
[add support for saving edits]: #saving-edits 
[conflict handling problem]: #conflict-handling-problem 
[update qstodoservice to support conflict handling]: #service-add-conflict-handling 
[add ui alert view helper to support conflict handling]: #add-alert-view 
[add conflict handler to todo list view controller]: #add-conflict-handling 
[test the app]: #test-app 
[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png 
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png 
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png 
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png 
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png 
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png 
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png 
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png 
[segmented controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html 
[core data model editor help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html 
[creating an outlet connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html 
[build a user interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html 
[adding a segue between scenes in a storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1 
[adding a scene to a storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html 
[core data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html 
[download the preview sdk here]: http://aka.ms/Gc6fex 
[how to use the mobile services client library for ios]: mobile-services-ios-how-to-use-client-library.md 
[getting started offline ios sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611 
[get started with offline data]: mobile-services-ios-get-started-offline-data.md 
[get started with mobile services]: mobile-services-ios-get-started.md 

