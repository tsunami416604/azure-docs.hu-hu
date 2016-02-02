### (選用) 設定 Azure Active Directory 的 .NET 行動服務

>[AZURE.NOTE] 這些都是選用步驟，因為它們只適用於 Azure Active Directory 登入提供者。

1. 安裝 [WindowsAzure.MobileServices.Backend.Security NuGet 封裝](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend.Security)。

2. 在 Visual Studio 中，展開 App_Start，然後開啟 WebApiConfig.cs。 新增下列 `使用` 上方的陳述式:

        using Microsoft.WindowsAzure.Mobile.Service.Security.Providers;

3. 也在 WebApiConfig.cs 中，加入下列程式碼以 `註冊` 方法之後立即 `選項` 具現化:

        options.LoginProviders.Remove(typeof(AzureActiveDirectoryLoginProvider));
        options.LoginProviders.Add(typeof(AzureActiveDirectoryExtendedLoginProvider));






