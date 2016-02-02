<properties
    pageTitle="如何在 iOS 上使用 Engagement API"
    description="最新的 iOS SDK - 如何在 iOS 上使用 Engagement API"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2015"
    ms.author="piyushjo" />



# 如何在 iOS 上使用 Engagement API

本文件是＜如何在 iOS 上整合 Engagement＞文件的附加說明：有關如何使用 Engagement API 來報告您應用程式的統計資料，本文件提供了深入的詳細說明。

請記住，如果您只想 Engagement 報告您的應用程式工作階段、 活動、 當機和技術資訊，那麼最簡單的方法是讓所有自訂 `UIViewController` 物件繼承自對應 `EngagementViewController` 類別。

如果您想要執行的詳細資訊，例如，如果您需要報告應用程式特定事件、 錯誤和作業，或如果您需要報告應用程式的活動以不同方式中的一個實作 `EngagementViewController` 類別，則您需要使用 Engagement API。

Engagement API 由 `EngagementAgent` 類別。 可以擷取此類別的執行個體呼叫 `[共用的 EngagementAgent]` 靜態方法 (請注意， `EngagementAgent` 傳回物件為單一值)。

在任何 API 呼叫之前, `EngagementAgent` 物件必須先初始化呼叫方法 `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION。網域}。SdkKey = {YOUR_SDK_KEY};AppId = {YOUR_APPID}"];`

## Engagement 概念

以下部分簡要 [Mobile Engagement 概念](mobile-engagement-concepts.md) iOS 平台。

### `工作階段` 和 `活動`

*活動* 是通常與應用程式的某個畫面相關聯也就是說 *活動* 時開始，畫面會顯示，並在畫面關閉時停止: 使用整合 Engagement SDK 時，就以此 `EngagementViewController` 類別。

但您也可以透過 Engagement API 手動控制「活動」**。 這樣可以將指定的畫面分隔為數個子部分，以取得關於此畫面使用方式的詳細資料 (例如，可了解此畫面內對話方塊的使用頻率與使用時間長度)。

## 報告活動

### 使用者啟動新的活動

            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

您需要呼叫 `startActivity()` 每當使用者活動變更。 第一次呼叫此函式會啟動新的使用者工作階段。

### 使用者結束其目前的活動

            [[EngagementAgent shared] endActivity];

> [AZURE.WARNING] 您應該 **永不** 呼叫此函式，除了如果您想要將應用程式分成數個工作階段的一次使用分割: 呼叫此函式會結束目前的工作階段，因此，後續呼叫 `startActivity()` 會啟動新的工作階段。 當您的應用程式關閉時，SDK 會自動呼叫此函數。

## 報告事件

### 工作階段事件

工作階段事件通常用來報告在其工作階段期間由使用者所執行的動作。

**不含額外資料的範例：**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**含額外資料的範例：**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### 獨立事件

相對於工作階段事件，獨立事件可以在工作階段的內容之外使用。

**範例：**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## 報告錯誤

### 工作階段錯誤

工作階段錯誤通常用來報告在其工作階段期間影響使用者的錯誤。

**範例：**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### 獨立錯誤

相對於工作階段錯誤，獨立錯誤可以在工作階段的內容之外使用。

**範例：**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## 報告工作

**範例：**

假設您想要報告登入程序的持續時間：

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];
    
      [... sign in ...]
    
      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### 報告工作期間的錯誤

錯誤可能與正在執行的工作關聯，而不是與目前的使用者工作階段關聯。

**範例：**

假設您想要報告您登入程序期間的錯誤：

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];
    
      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;
    
        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];
    
          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }
    
      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### 工作期間的事件

事件可能與執行的工作相關，而不是與目前的使用者工作階段相關。

**範例：**

假設我們有社交網路，且使用工作來報告使用者連接到伺服器這段期間的總時間。 使用者可以接收來自朋友的訊息，這就是工作事件。

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## 額外的參數

可以將任意資料附加到事件、錯誤、活動及工作。

此資料可以結構化，它會使用 iOS 的 NSDictionary 類別。

請注意，額外項目可以包含 `陣列 (NSArray、 NSMutableArray)`, ，`數字 (NSNumber 類別)`, ，`字串 (NSString、 NSMutableString)`, ，`urls(NSURL)`, ，`資料 (NSData、 NSMutableData)` 或其他 `NSDictionary` 執行個體。
> [AZURE.NOTE] 額外的參數會以 JSON 序列化。 如果您想要傳遞與上述不同的物件，必須在類別中實作以下方法：
>
             -(NSString*); JSONRepresentation
>
> 方法應傳回您物件的 JSON 表示法。

### 範例

    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### 限制

#### 金鑰

在每個索引鍵 `NSDictionary` 必須符合下列規則運算式:

`^[a-zA-Z][a-zA-Z_0-9]*`

這表示索引鍵必須至少以一個字母為開頭，後面接著字母、數字或底線 (\_)。

#### 大小

額外項目限制為一次呼叫 **1024** 個字元 (由 Engagement 代理程式以 JSON 編碼之後)。

在上述範例中，傳送到伺服器的 JSON 會是 58 個字元：

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## 報告應用程式資訊

您可以手動報告追蹤資訊 (或是任何其他應用程式特定資訊) 使用 `sendAppInfo:` 函式。

請注意，這些資訊可以累加地傳送：只有指定的索引鍵的最新值會保留給指定的裝置。

像是事件額外項目， `NSDictionary` 類別用來摘錄應用程式資訊，請注意，陣列或子字典將被視為一般字串 (使用 JSON 序列化)。

**範例：**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### 限制

#### 金鑰

在每個索引鍵 `NSDictionary` 必須符合下列規則運算式:

`^[a-zA-Z][a-zA-Z_0-9]*`

這表示索引鍵必須至少以一個字母為開頭，後面接著字母、數字或底線 (\_)。

#### 大小

應用程式資訊限制為一次呼叫 **1024** 個字元 (由 Engagement 代理程式以 JSON 編碼之後)。

在上述範例中，傳送到伺服器的 JSON 會是 44 個字元：

    {"birthdate":"1983-12-07","gender":"female"}



