<properties
    pageTitle="開始使用 Azure AD iOS | Microsoft Azure"
    description="如何建置 iOS 應用程式來與 Azure AD 整合進行登入，並使用 OAuth 呼叫受 Azure AD 保護的 API。"
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/13/2015"
    ms.author="brandwe"/>


# 將 Azure AD 整合至 iOS 應用程式

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD 提供 Active Directory 驗證程式庫 (ADAL) 給需要存取受保護資源的 iOS 用戶端。 ADAL 存在的唯一目的是為了讓您的應用程式輕鬆取得存取權杖。 為了示範它有多麼容易使用，我們會在此建置可達行下列作業的目標 C 待辦事項清單應用程式：

-   取得存取權杖來呼叫 Azure AD Graph API 使用 [OAuth 2.0 驗證通訊協定](https://msdn.microsoft.com/library/azure/dn645545.aspx)。
-   在目錄中搜尋具有指定別名的使用者。

若要建立完整可用的應用程式，您必須：

2. 向 Azure AD 註冊您的應用程式。
3. 安裝及設定 ADAL。
5. 使用 ADAL 來取得 Azure AD 的權杖。

若要開始， [下載應用程式基本架構](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) 或 [下載完整的範例](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。 您還需要一個可以建立使用者並註冊應用程式的 Azure AD 租用戶。 如果您還沒有租用戶， [了解如何取得租用](active-directory-howto-tenant.md)。

## *1.判斷您的重新導向 URI 將會針對 iOS*

為了安全地在特定 SSO 案例啟動您的應用程式，我們需要您以特定格式建立**「重新導向 URI」**。 重新導向 URI 可確保應用程式要求的權杖會正確地傳回給它們。

iOS 格式的重新導向 URI：

```
<app-scheme>://<bundle-id>
```

-   **aap-scheme** - This is registered in your XCode project. It is how other applications can call you. You can find this under Info.plist -> URL types -> URL Identifier. You should create one if you don't already have one or more configured.
-   **bundle-id** - This is the Bundle Identifier found under "identity" un your project settings in XCode.

An example for this QuickStart code would be: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## *2. Register the DirectorySearcher Application*
To enable your app to get tokens, you'll first need to register it in your Azure AD tenant and grant it permission to access the Azure AD Graph API:

-   Sign into the Azure Management Portal
-   In the left hand nav, click on **Active Directory**
-   Select a tenant in which to register the application.
-   Click the **Applications** tab, and click **Add** in the bottom drawer.
-   Follow the prompts and create a new **Native Client Application**.
    -   The **Name** of the application will describe your application to end-users
    -   The **Redirect Uri** is a scheme and string combination that Azure AD will use to return token responses.  Enter a value specific to your application based on the information above.
-   Once you've completed registration, AAD will assign your app a unique client identifier.  You'll need this value in the next sections, so copy it from the **Configure** tab.
- Also in **Configure** tab, locate the "Permissions to Other Applications" section.  For the "Azure Active Directory" application, add the **Access Your Organization's Directory** permission under **Delegated Permissions**.  This will enable your application to query the Graph API for users.

## *3. Install & Configure ADAL*
Now that you have an application in Azure AD, you can install ADAL and write your identity-related code.  In order for ADAL to be able to communicate with Azure AD, you need to provide it with some information about your app registration.
-   Begin by adding ADAL to the DirectorySearcher project using Cocapods.
```
$ vi Podfile
```
Add the following to this podfile:
```
來源 'https://github.com/CocoaPods/Specs.git'
link_with ['快速入門']
xcodeproj '快速入門 」

pod 'ADALiOS'
```

Now load the podfile using cocoapods. This will create a new XCode Workspace you will load.
```
$ pod 安裝
...
$ 開啟 QuickStart.xcworkspace
```

-   In the QuickStart project, open the plist file `settings.plist`.  Replace the values of the elements in the section to reflect the values you input into the Azure Portal.  Your code will reference these values whenever it uses ADAL.
    -   The `tenant` is the domain of your Azure AD tenant, e.g. contoso.onmicrosoft.com
    -   The `clientId` is the clientId of your application you copied from the portal.
    -   The `redirectUri` is the redirect url you registered in the portal.

## *4.  Use ADAL to Get Tokens from AAD*
The basic principle behind ADAL is that whenever your app needs an access token, it simply calls a completionBlock `+(void) getToken : `, and ADAL does the rest.  

-   In the `QuickStart` project, open `GraphAPICaller.m` and locate the `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` comment near the top.  This is where you pass ADAL the coordinates through a CompletionBlock to communicate with Azure AD and tell it how to cache tokens.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}
```

- 現在我們需要使用此權杖搜尋圖形中的使用者。 尋找 `/ / TODO: 實作 SearchUsersList` 註方法提出 GET 要求給 Azure AD Graph API 查詢 UPN 開頭為指定的搜尋詞彙的使用者。 但為了能夠查詢 Graph API，您必須包含 access_token `授權` 標頭的要求-這是 adal。

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
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

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)

                     //each object is a key value pair
                     NSDictionary *keyValuePairs;
                     NSMutableArray* Users = [[NSMutableArray alloc]init];

                     for(int i =0; i < graphDataArray.count; i++)
                     {
                         keyValuePairs = [graphDataArray objectAtIndex:i];

                         User *s = [[User alloc]init];
                         s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                         s.name =[keyValuePairs valueForKey:@"givenName"];

                         [Users addObject:s];
                     }

                     completionBlock(Users, nil);
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
- 當您的應用程式藉由呼叫要求語彙基元 `getToken(...)`, ，ADAL 會嘗試傳回的權杖，而不要求使用者認證。 如果 ADAL 決定使用者需要登入才能取得權杖，它會顯示登入對話方塊、收集使用者的認證，並在成功驗證後傳回權杖。 如果 ADAL 無法傳回權杖，因為任何原因，則會擲回 `AdalException`。
- 請注意， `AuthenticationResult` 物件包含 `tokenCacheStoreItem` 物件，可用來收集您的應用程式可能需要的資訊。 在 QuickStart 中， `tokenCacheStoreItem` 用來判斷驗證是否已經發生。


## 步驟 5：建置並執行應用程式

恭喜！ 您現在有一個可運作的 iOS 應用程式，能夠驗證使用者、使用 OAuth 2.0 安全地呼叫 Web API，以及取得使用者的基本資訊。 如果您還沒有這麼做，現在是將一些使用者植入租用戶的時候。 執行 QuickStart 應用程式，並使用其中一個使用者登入。 根據 UPN 搜尋其他使用者。 關閉並重新執行應用程式。 請注意，使用者工作階段會維持不變。

ADAL 可讓您輕鬆地將這些常見的身分識別功能全部納入您的應用程式。 它會為您處理一切麻煩的事，包括快取管理、OAuth 通訊協定支援、向使用者顯示登入 UI、重新整理過期權杖等等。 是單一 API 呼叫，您唯一需要知道 `getToken`。

提供完整的範例 (不含您的設定值) 是供您參考 [這裡](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)。 您現在可以繼續探索其他案例。 您可以嘗試：

[使用 Azure AD 保護 Node.JS Web API >>](../active-directory-devquickstarts-webapi-nodejst.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]




