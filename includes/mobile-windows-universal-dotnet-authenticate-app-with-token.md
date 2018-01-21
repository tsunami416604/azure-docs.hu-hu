
1. A MainPage.xaml.cs projekt fájlban adja hozzá a következő **használatával** utasításokat:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Cserélje le a **AuthenticateAsync** metódus a következő kóddal:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    Ezen verziója **AuthenticateAsync**, az alkalmazás megkísérli a tárolt hitelesítő adatok használatát a **PasswordVault** a szolgáltatás eléréséhez. Rendszeres bejelentkezés is történik, amikor nincs nem tárolt hitelesítő adatok.
   
   > [!NOTE]
   > Előfordulhat, hogy egy gyorsítótárazott token lejárt, és jogkivonat lejáratáról is esetén fordul elő a hitelesítés után az alkalmazás használatban van. Annak megállapítása, ha a jogkivonat lejárt-e további tudnivalókért lásd: [lejárt a hitelesítési tokenek keressen](http://aka.ms/jww5vp). Lejáró jogkivonatok kapcsolatban a hitelesítési hibák kezelési megoldást, lásd: a feladás egy vagy több [gyorsítótárazáshoz és kezelése az Azure Mobile Services lejárt jogkivonatok felügyelt SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Indítsa újra az alkalmazást kétszer.
   
    Figyelje meg, hogy az első indításkor, jelentkezzen be a szolgáltató újra szükség. Azonban a második újraindítás közben a gyorsítótárazott hitelesítő adatokat használja, és a bejelentkezési elmarad. 

