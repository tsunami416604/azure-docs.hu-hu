<properties
    pageTitle="Azure AD B2C 預覽: 從 iOS 應用程式呼叫 Web API |Microsoft Azure"
    description="本文將說明如何建立 iOS "To-Do List" app that calls a node.js web API using OAuth 2.0 bearer tokens. Both the iOS app and web api use Azure AD B2C to manage user identities and authenticate users."
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="objectivec"
    ms.topic="article"
    ms.date="09/22/2015"
    ms.author="brandwe"/>


# Azure AD B2C 預覽：從 iOS 應用程式中呼叫 Web API

有了 Azure AD B2C，您可以將強大的自助式身分識別管理功能加入至 iOS 應用程式和 Web API，只要幾個簡短步驟即可完成。 本文將示範如何建立使用 OAuth 2.0 持有人權杖呼叫 node.js Web API 的 iOS「待辦事項清單」應用程式。 這兩個 iOS 應用程式與 web api 使用 Azure AD B2C 管理使用者的身分識別
和驗證使用者。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
> [AZURE.NOTE]
    使用此快速入門的先決條件為，您必須擁有受 Azure AD (含 B2C) 保護的 Web API，才可完整運作。 我們已經為 .NET 與 node.js 建立一個 Web API 供您使用。 本逐步解說會假設已設定 node.js Web API 範例。 
    請參閱 [Azure Active Directory Web API Node.js 範例] (作用中的目錄-b2c-devquickstarts-api-node.md'
)。

> [AZURE.NOTE]
    本文不涵蓋如何使用 Azure AD B2C 實作登入、註冊和管理設定檔。 而會著重在如何在使用者已通過驗證後呼叫 Web API。
如果您還沒有這麼做，您應該開始 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md) 來了解 Azure AD B2C 的基本概念。

## 1.取得 Azure AD B2C 目錄

您必須先建立目錄或租用戶，才可使用 Azure AD B2C。 目錄為所有使用者、應用程式、群組等項目的容器。 如果您沒有
其中一個，請移 [建立 B2C 目錄](active-directory-b2c-get-started.md) 之後再繼續。

## 2.建立應用程式

您現在需要在 B2C 目錄中建立應用程式，以提供一些必要資訊給 Azure AD，讓它與應用程式安全地通訊。 在此案例中，因為應用程式與 Web API 會組成一個邏輯應用程式，所以將由單一**應用程式識別碼**代表。 若要建立應用程式
請依照下列 [這些指示](active-directory-b2c-app-registration.md)。 請務必

- 在應用程式中加入 **Web 應用程式/Web API**
- 輸入 `http://localhost:3000//驗證/openid/傳回` 為 **回覆 URL** -它是此程式碼範例的預設 URL。
- 為您的應用程式建立**應用程式密碼**，並複製起來。 稍後您將會用到此資訊。
- 複製指派給您應用程式的**應用程式識別碼**。 稍後您也會用到此資訊。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3.建立您的原則

在 Azure AD B2C，每個使用者經驗來定義 [* * 原則 * *](active-directory-b2c-reference-policies.md)。 此應用程式包含三種 
身分識別遇到位註冊、 登入，以及使用 Facebook 登入。 您必須建立的每個類型，一個原則中所述 
[原則參考文件](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)。 建立您的三個原則時，請務必：

- 在註冊原則中，選擇 [**顯示名稱**] 和其他一些註冊屬性。
- 在每個原則中選擇 [**顯示名稱**] 和 [**物件識別碼**] 應用程式宣告。 您也可以選擇其他宣告。
- 建立每個原則後，請複製原則的**名稱**。 它應該會有前置詞 `b2c_1_`。 稍後您將需要這些原則名稱。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

當您成功建立三個原則後，就可以開始建置您的應用程式。

請注意，本文不會說明如何使用您剛才建立的原則。 如果您想要深入了解在 Azure AD B2C，原則的運作方式
您應該開始 [.NET Web 應用程式快速入門教學課程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## 4.下載程式碼

本教學課程的程式碼會維護 [GitHub 上](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS)。 當您建置範例，您可以 
[下載為.zip 的基本架構專案](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip) 或再製基本架構:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **若要完成此教學課程，必須下載基本架構。**由於在 iOS 上實作完整運作的應用程式有其複雜性，因此**基本架構**所包含的 UX 程式碼將於您完成下方教學課程之後執行。 如此將能節省開發人員的時間。 UX 程式碼與加入 B2C 至 iOS 應用程式的主題無關。

完成應用程式也是 [成為.zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) 或
`完整` 相同的儲存機制的分支。


現在使用 Cocoapods 載入該 Podfile。 這會建立您將載入的新 XCode Workspace。 如果您沒有 Podfile，請造訪 [網站來安裝 podfile](https://cocoapods.org)。

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5.設定 iOS 工作應用程式

為了讓 iOS 工作應用程式與 Azure AD B2C 通訊，您必須提供一些通用參數。在 `Microsoft 工作` 資料夾中，開啟 `settings.plist` 檔案位於專案的根目錄，並中的值取代 `< dict >` 一節。這些值將用於整個應用程式。

```
<dict>
    <key>authority</key>
    <string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
    <key>clientId</key>
    <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    <key>scopes</key>
    <array>
        <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    </array>
    <key>additionalScopes</key>
    <array>
    </array>
    <key>redirectUri</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>taskWebAPI</key>
    <string>http://localhost/tasks:3000</string>
    <key>emailSignUpPolicyId</key>
    <string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
    <key>faceBookSignInPolicyId</key>
    <string><your sign in policy for FB></string>
    <key>emailSignInPolicyId</key>
    <string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
    <key>fullScreen</key>
    <false/>
    <key>showClaims</key>
    <true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## 6.取得存取權杖和呼叫工作 API

本節將示範如何在 Web 應用程式中使用 Microsoft 的程式庫和架構完成 OAuth 2.0 權杖交換。 如果您是
熟悉 **授權碼** 和 **的存取權杖**, ，它可能是個不錯的主意，瀏覽 [OAuth 2.0 通訊協定參照](active-directory-b2c-reference-protocols.md)。

#### 建立標頭檔並加入我們將使用的方法。

我們將需要這些方法來取得權杖與我們選取的原則，然後呼叫我們工作伺服器。 現在就讓我們進行這些設定。

建立檔案，稱為 `samplesWebAPIConnector.h` / XCode 專案中的工作] 下

加入下列程式碼來定義我們需要做的事：

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

您會看到這些都是我們的 API，以及方法的簡單 CRUD 作業 `doPolicy` ，可讓您取得您想要的原則的語彙基元。

您也會看到還有另外兩個標頭檔需要定義，其中將保存我們的「工作項目」和「原則資料」。 讓我們馬上建立它們：

建立檔案，稱為 `samplesTaskItem.h` 為下列程式碼:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

我們還會建立檔案 `samplesPolicyData.h` 以保存原則資料:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### 加入工作和原則項目的實作

既然我們已經有了標頭檔，我們需要撰寫一些程式碼，以儲存我們將用於範例的資料。

建立檔案，稱為 `samplesPolicyData.m` 為下列程式碼:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

#### 撰寫設定程式碼來呼叫 ADAL for iOS

UI 方面用來儲存物件的快速程式碼已完成。 我們現在必須撰寫程式碼中存取 ADAL 使用參數，我們將放在 ios 我們 `settings.plist` 檔案以取得存取權杖，以提供給我們工作的伺服器。 過程可能很冗長，請保持注意力。

所有工作都是 `samplesWebAPIConnector.m`。

首先，讓我們建立我們 `doPolicy()` 所編寫的實作我們 `samplesWebAPIConnector.h` 標頭檔:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}
```

您會看到方法相當簡單。 它會做為輸入 `samplesPolicyData` 我們剛剛所建立的物件、 父 ViewController，和回呼。 此回呼很有趣，我們將逐步解說。

1. 您會看到 `completionBlock` 與將會被傳回的型別具有 ADProfileInfo `userInfo` 物件。 ADProfileInfo 這個類型保存來自伺服器的所有回應，特別是宣告。
2. 您會看到我們 `readApplicationSettings`。 這會讀取資料，我們提供了在 `settings.plist`
3. 最後，我們有相當大 `getClaimsWithPolicyClearingCache` 方法。 這就是我們真正需要撰寫來呼叫 ADAL for iOS 的方法。 我們稍後會這樣做。

現在我們要撰寫我們的大型方法 `getClaimsWithPolicyClearingCache`。 這大到足以自成一個段落

#### 建立對 ADAL for iOS 的呼叫

如果您從 GitHub 下載基本架構，您會看到我們已準備好幾個部分可協助執行範例應用程式。它們都遵循 `取得 (Claims|語彙基元) 與 < 動作 > ClearningCache`。採用 Objetive C 慣例時，讀起來就很像英文。例如，"get a Token with extra parameters I provide you and clear the cache" (以我為您提供的額外參數取得權杖並清除快取)。這是 `getTokenWithExtraParamsClearingCache()`。很簡單。

我們將撰寫 「 取得宣告與權杖與原則我提供您並不會清除快取 」 或 `getClaimsWithPolicyClearingCache`。 我們永遠從 ADAL 取回權杖，因此不需要在方法中指定「宣告和權杖」。 不過有時候您只希望且不會剖析宣告，因此我們提供的方法沒有宣告的權杖呼叫 `getTokenWithPolicyClearingCache` 基本架構中。

現在讓我們撰寫這段程式碼：

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}
```

第一個部分看起來應該很熟悉。 我們在載入中未提供的設定 `Settings.plist` 並將其指派給 `資料`。 接著我們設定 `ADAuthenticationError` ，帶任何錯誤來自 ADAL for iOS。 我們也建立 `authContext` 這設定 ADAL 我們呼叫。 一切從傳遞 *authority* 給它開始。 我們也提供 `authContext` 我們父控制器，使我們可以傳回給它的參考。 我們也將轉換我們 `redirectURI` 中的字串是我們 `settings.plist` 在 NSURL 類型 ADAL 預期。 最後我們設定 `correlationId` 即只可以遵循呼叫用戶端與伺服器之間的來回的 UUID。 這有助於偵錯。

現在，我們要實際呼叫 ADAL，在此，呼叫將迴異於您先前使用 ADAL for iOS 時所看見的情況：

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {
```

您在這裡可以看到呼叫相當簡單。

**SCOPES** - 是我們傳給伺服器的範圍，而在使用者登入時，我們想要向伺服器要求此範圍。 在 B2C 預覽中，我們傳遞 client_id。 不過，這在未來將寫成 scopes。 屆時會更新本文件。
**addtionalScopes** -這些是您可能想要用於您的應用程式的其他領域。 這將在未來使用
**clientId** -你從入口網站應用程式識別碼
**redirectURI** -我們要回傳的語彙基元所預期的重新導向。
**識別碼** -這個方式來識別使用者，因此我們可以查看是否有使用權杖快取和另一個權杖總是要求伺服器中。 您會看到這執行中的型別 `ADUserIdentifier` 而且我們可以指定我們要做為識別碼。 您應該使用使用者名稱。
**promptBehavior** -這已被取代，而且應該 AD_PROMPT_ALWAYS
**extraQueryParameters** -任何您想要傳遞到 URL 中伺服器的額外編碼格式。
**原則** -所叫用的原則。 本逐步解說的後續重要部分。

您所見的我們傳遞 completionBlock `ADAuthenticationResult` 擁有我們語彙基元和設定檔資訊 (如果呼叫成功)

使用上面的程式碼可以為您所提供的原則取得權杖。 我們將使用此權杖來呼叫 API。

#### 建立對伺服器的工作呼叫

既然我們已經有權杖，我們需要將它提供給 API 進行授權。

如果您還記得，我們有三個方法需要實作：

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

我們 `getTasksList` 提供陣列，代表我們的伺服器中的工作 
我們 `addTask` 和 `deleteTask` 執行後續動作，並傳回 TRUE 或 FALSE，如果已順利完成。

我們要撰寫我們 `getTaskList` 第一次:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}
```

若要討論的工作程式碼已超出本逐步解說的範圍，但是沒有您有趣的東西可能已經注意到: `craftRequest` 採用我們的任務 URL 方法。 我們利用這個方法，以我們收到的存取權杖，對伺服器建立要求。 現在我們就來撰寫：

讓我們將下列程式碼加入至 'samplesWebAPIConnector.m' 檔案：

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

您可以看到這是採用 web URI，將它與語彙基元 `持有人` 中 HTTP 標頭，然後將它傳回給我們。 我們會呼叫 `getTokenClearingCache` API 看起來怪怪在其第一個，但是我們只需使用這個呼叫來從快取中取得權杖，並確定它是否仍然有效 (getToken * 呼叫進行我們會要求 ADAL)。 我們在每次呼叫時都使用這段程式碼。 現在讓我們回去撰寫其他的「工作」方法。

我們要撰寫我們 `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

這個遵循相同模式，但會造成另一個 (及最終!) 我們必須實作的方法: `convertTaskToDictionary` 其中採用我們的陣列，並使其更輕鬆地為更動我們要傳遞至伺服器的查詢參數的字典物件。 此程式碼非常簡單：

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}
```

最後，我們要撰寫我們 `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### 將登出加入至應用程式。

最後一件要做的事就是實作應用程式的「登出」。 這相當簡單。 再次在我們 `sampleWebApiConnector.m` 檔案:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## 9.執行範例應用程式

最後，在 xCode 中建置並執行應用程式。 註冊或登入應用程式，並為登入的使用者建立工作。 登出後，再以不同使用者重新登入，並為該使用者建立工作。

請注意每位使用者的工作如何儲存於 API，因為 API 會從它收到的存取權杖中擷取使用者的身分識別。

已完成的範例供您參考 [依現狀的.zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip),，
或者，您可以從 GitHub 複製它:

`git 複製-分支完成 https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS`

## 後續步驟

您現在可以進入更進階的 B2C 主題。 您可以嘗試：

[從 node.js Web 應用程式呼叫 node.js Web API >>]()

[自訂 B2C 應用程式的 UX >>]()





