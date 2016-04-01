## 取得資源管理員權杖

Azure Active Directory 必須驗證您在使用 Azure 資源管理員的資源上執行的所有工作。 此處顯示的範例會使用密碼驗證，如其他方法，請參閱 [驗證 Azure 資源管理員要求][lnk-authenticate-arm]。

1. 加入下列程式碼以 **Main** 方法來擷取權杖，從應用程式 id 和密碼，使用 Azure AD 的 Program.cs 中。

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. 建立 **ResourceManagementClient** 結尾加入下列程式碼使用權杖的物件 **Main** 方法 ︰

    ```
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken);
    var client = new ResourceManagementClient(creds);
    ```

3. 建立或取得您正在使用的資源群組參照：

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdateAsync(rgName,
        new ResourceGroup("East US")).Result;
    if (rgResponse.StatusCode != HttpStatusCode.Created
        && rgResponse.StatusCode != HttpStatusCode.OK)
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx

