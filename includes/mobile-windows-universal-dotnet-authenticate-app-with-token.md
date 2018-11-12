
1. Projekt a MainPage.xaml.cs fájlban adja hozzá a következő **használatával** utasításokat:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Cserélje le a **AuthenticateAsync** módszer a következő kóddal:
   
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
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    Az ebben a verzióban **AuthenticateAsync**, az alkalmazás megpróbálja tárolt hitelesítő adatok használata a **PasswordVault** a szolgáltatás eléréséhez. Egy szokásos bejelentkezési is történik, amikor nincs semmilyen tárolt hitelesítő adatok.
   
   > [!NOTE]
   > Előfordulhat, hogy a gyorsítótárazott jogkivonat lejár, és a jogkivonat lejárati is előfordulhatnak a hitelesítés után, ha az alkalmazás használatban van. Ismerje meg, hogyan határozza meg, ha a jogkivonat lejárt, lásd: [keresése a lejárt érvényességű hitelesítési jogkivonatok](https://aka.ms/jww5vp). Lejáró jogkivonatok kapcsolódó engedélyezési hibák kezelési megoldást, tekintse meg a bejegyzését [gyorsítótár és az Azure Mobile Services lejárt jogkivonatok kezelése felügyelt SDK](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Kétszer indítsa újra az alkalmazást.
   
    Figyelje meg, hogy az első indításkor, jelentkezzen be a szolgáltató újra szükség. Azonban a második újraindítás a gyorsítótárazott hitelesítő adatokat használja, és jelentkezzen be elmarad. 

