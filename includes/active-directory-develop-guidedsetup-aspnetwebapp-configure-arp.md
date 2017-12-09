
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>Az alkalmazás regisztrációs adatokat az ASP.NET webalkalmazás konfigurálása

Ebben a lépésben konfigurálása az SSL használatát a projekthez, és az SSL URL-cím segítségével konfigurálhatja az alkalmazás regisztrációs adatokat. Ezt követően az alkalmazás hozzáadása "a megoldás a adatainak *web.config*.

1.  A Megoldáskezelőben, válassza ki a projektet, és tekintse meg a `Properties` (Ha nem látja a Tulajdonságok ablak az F4) ablak
2.  Változás `SSL Enabled` számára`True`
3.  Másolja az értéket `SSL URL` fent és illessze be a `Redirect URL` Ez a lap tetején mezőben, majd kattintson az *frissítés*:<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  Adja hozzá a következő `web.config` szakaszban legfelső mappában található fájl `configuration\appSettings`:

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
