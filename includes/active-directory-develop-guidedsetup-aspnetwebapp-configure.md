
## <a name="create-an-application-express"></a>Hozzon létre egy alkalmazást (Express)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Az alkalmazás regisztrálása a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-maileket
3.  Győződjön meg arról, hogy az interaktív telepítés beállítást
4.  Kövesse az utasításokat az alkalmazás átirányítási URL-cím hozzáadása

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Az alkalmazás regisztrációs adatokat hozzáadni a megoldás (speciális)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása
2. Adjon meg egy nevet az alkalmazás és az e-maileket 
3.  Győződjön meg arról, hogy az interaktív telepítés beállítás nincs bejelölve
4.  Kattintson a `Add Platform`, majd jelölje be`Web`
5.  Lépjen vissza a Visual Studio és a Megoldáskezelőben, válassza ki a projektet, és tekintse meg a Tulajdonságok ablak (Ha nem látja a Tulajdonságok ablak az F4)
6.  Módosítsa az SSL engedélyezve`True`
7.  Másolja az SSL URL-címet, és vegye fel az URL-cím az átirányítási URL-t átirányítási URL-t a regisztrációs portál közül:<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Adja hozzá a következő `web.config` a legfelső szintű mappában található a szakasz `configuration\appSettings`:

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Cserélje le `ClientId` az imént regisztrált alkalmazás azonosítójával
</li>
<li>
Cserélje le `redirectUri` a SSL URL-címet a projekt
</li>
</ol>
<!-- End Docs -->
