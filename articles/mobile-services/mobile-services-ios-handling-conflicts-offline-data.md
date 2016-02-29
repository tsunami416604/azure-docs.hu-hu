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

本主題將說明在使用 Azure 行動服務的離線功能時，應如何同步處理資料及處理衝突。 本教學課程以 [Get Started with Offline Data] 教學課程。

>[AZURE.NOTE] 若要完成本教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 免費試用</a>。


## 下載 iOS 專案

本教學課程中，下載 [從 Github 更新的 Xcode 專案](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS)。 我們會使用 Xcode 專案，從結尾 [Get Started with Offline Data] 作為起點的教學課程點，並再更新它允許您編輯的項目。 我們也已加入支援類別和方法，以便在下一節中新增衝突處理常式。

在本教學課程結尾處如果您執行此應用程式兩個電話變更這兩種行動電話在本機上的對相同項目和變更推送回伺服器，您將允許使用者選擇要保存哪個版本每支電話:
  * 保留用戶端版本 (這會覆寫伺服器上的版本)、
  * 保留伺服器版本 (這會更新用戶端本機資料表)，或
  * 兩個版本都不保留 (取消發送並擱置作業)。

現在我們要新增衝突處理常式，以啟用這項功能。

## <a name="add-conflict-handling"></a>將衝突處理常式新增至待辦事項清單檢視控制器

1. 在 **QSTodoListViewController.m**, ，編輯 **viewDidLoad**。 取代的呼叫 **defaultService** 呼叫 **defaultServiceWithDelegate** 改為:

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. 在 **QSTodoListViewController.h**, ，加入 **& l t;MSSyncContextDelegate & gt;** 至介面宣告，以實作 **MSSyncContextDelegate** 通訊協定。

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. 在頂端加入下列 import 陳述式 **QSTodoListViewController.m**:

        #import "QSUIAlertViewWithBlock.h"

4. 最後，我們要新增到下列兩項作業 **QSTodoListViewController.m** 以使用此協助程式類別，並提示使用者來協調衝突三種方式之一。

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

<!-- URLs. -->

[Update the App Project to Allow Editing]: #update-app
[Update Todo List View Controller]: #update-list-view
[Add Todo Item View Controller]: #add-view-controller
[Add Todo Item View Controller and Segue to Storyboard]: #add-segue
[Add Item Details to Todo Item View Controller]: #add-item-details
[Add Support for Saving Edits]: #saving-edits
[Conflict Handling Problem]: #conflict-handling-problem
[Update QSTodoService to Support Conflict Handling]: #service-add-conflict-handling
[Add UI Alert View Helper to Support Conflict Handling]: #add-alert-view
[Add Conflict Handler to Todo List View Controller]: #add-conflict-handling
[Test the App]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Segmented Controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Get Started with Offline Data]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md

