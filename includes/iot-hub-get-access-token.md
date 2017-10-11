## <a name="obtain-an-azure-resource-manager-token"></a>Az Azure Resource Manager jogkivonat beszerzése
Az Azure Active Directory hitelesítenie kell a feladatokat hajt végre az Azure Resource Manager eszközzel. Az itt bemutatott példában jelszó-hitelesítést használ, a más megoldások lásd [kérelmek hitelesítéséhez az Azure Resource Manager][lnk-authenticate-arm].

1. Adja hozzá a következő kódot a **fő** metódus a Program.cs jogkivonatot beolvasni az Azure AD az alkalmazás-azonosító és jelszó használatával.
   
    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Hozzon létre egy **ResourceManagementClient** objektum, amely használja a jogkivonatot végén ad hozzá a következő kódot a **fő** módszert:
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Hozzon létre, vagy az erőforráscsoportot használ mutató hivatkozás beszerzése:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx