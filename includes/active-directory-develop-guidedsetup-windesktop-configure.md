
## <a name="create-an-application-express"></a>Hozzon létre egy alkalmazást (Express)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Az alkalmazás regisztrálása a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-maileket
3.  Győződjön meg arról, hogy az interaktív telepítés beállítást
4.  Az utasítások beszerzéséhez, és illessze be a kódot

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Az alkalmazás regisztrációs adatokat hozzáadni a megoldás (speciális)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása
2. Adjon meg egy nevet az alkalmazás és az e-maileket 
3. Győződjön meg arról, hogy az interaktív telepítés beállítás nincs bejelölve
4. Kattintson a `Add Platform`, majd jelölje be `Native Application` , majd kattintson a Mentés
5. Másolja át a GUID azonosítója, lépjen vissza a Visual Studio, nyissa meg `App.xaml.cs` , és cserélje le `your_client_id_here` az imént regisztrált alkalmazás azonosítójával:

```csharp
private static string ClientId = "your_application_id_here";
```
