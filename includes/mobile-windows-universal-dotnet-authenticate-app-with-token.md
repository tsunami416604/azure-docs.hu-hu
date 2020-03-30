---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179800"
---
1. A MainPage.xaml.cs projektfájlban adja hozzá a következőket a következő utasítások **használatával:**
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Cserélje le a **AuthenticateAsync** metódust a következő kódra:
   
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
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
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
   
    A **AuthenticateAsync**ezen verziójában az alkalmazás megpróbálja a **PasswordVault-ban** tárolt hitelesítő adatokat használni a szolgáltatás eléréséhez. Rendszeres bejelentkezés is történik, ha nincs tárolt hitelesítő adatok.
   
   > [!NOTE]
   > A gyorsítótárazott jogkivonat lejárt, és a jogkivonat lejárata is előfordulhat a hitelesítés után, ha az alkalmazás használatban van. A jogkivonat lejártának megállapításáról a [Lejárt hitelesítési jogkivonatok ellenőrzése című témakörben](https://aka.ms/jww5vp)olvashat. A lejáró jogkivonatokhoz kapcsolódó engedélyezési hibák kezelésére szolgáló megoldást a [lejárt jogkivonatok gyorsítótárazása és kezelése című témakörben találja az Azure Mobile Services által kezelt SDK-ban.](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx) 
   > 
   > 
3. Indítsa újra kétszer az alkalmazást.
   
    Figyelje meg, hogy az első indításkor újra be kell jelentkeznie a szolgáltatóval. Azonban a második újraindítás a gyorsítótárazott hitelesítő adatokat használnak, és a bejelentkezési kerülik. 

