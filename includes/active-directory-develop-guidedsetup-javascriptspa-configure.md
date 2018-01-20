
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Több módon is hozzon létre egy alkalmazást, válasszon egyet a őket:

### <a name="option-1-register-your-application-express-mode"></a>1. lehetőség: Az alkalmazás (Expressz mód) regisztrálása
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:

1.  Az alkalmazás regisztrálása a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-maileket
3.  Ellenőrizze, hogy a beállítás *interaktív telepítés* be van jelölve
4.  Az utasítások beszerzéséhez, és illessze be a kódot

### <a name="option-2-register-your-application-advanced-mode"></a>2. lehetőség: Az alkalmazás (speciális módban) regisztrálása

1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása
2. Adjon meg egy nevet az alkalmazás és az e-maileket 
3. Ellenőrizze, hogy a beállítás *interaktív telepítés* nincs bejelölve
4.  Kattintson a `Add Platform`, majd jelölje be`Web`
5. Adja hozzá a `Redirect URL` , amelyek megfelelnek az alkalmazás URL-címet, a webkiszolgáló alapján. A szakaszok további tájékoztatást alább olvashat be / beszerzése az átirányítási URL-cím a Visual Studio és a Python.
6. Kattintson a *mentése*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>A Visual Studio utasításokat átirányítási URL-cím beszerzése
> Kövesse az utasításokat az átirányítási URL-cím beszerzése:
> 1.    A *Megoldáskezelőben*, válassza ki a projektet, és tekintse meg a `Properties` ablakban (Ha nem látja a Tulajdonságok ablak, nyomja le az `F4`)
> 2.    Másolja a értéket `URL` a vágólapra:<br/> ![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Váltás a *alkalmazásregisztrációs portálra* , majd illessze be az érték egy `Redirect URL` , és kattintson a "Mentés"

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Az átirányítási URL-ként Python
> A Python a web server port parancssorból állíthatja be. Ez az interaktív telepítés a 8080-hivatkozásban, de a kezelőfelület szabadon használhatja az összes rendelkezésre álló portot használ. Minden esetben kövesse az alábbi utasításokat egy átirányítási URL-címet az alkalmazás-regisztrációs adatainak beállításához:<br/>
> - Váltson vissza az a *alkalmazásregisztrációs portálra* , és `http://localhost:8080/` , egy `Redirect URL`, vagy használjon `http://localhost:[port]/` egyéni TCP-port használata (ahol *[port]* az egyéni TCP-port szám), és kattintson a "Mentés"


#### <a name="configure-your-javascript-spa"></a>Configure your JavaScript SPA

1.  Hozzon létre egy fájlt `msalconfig.js` tartalmazó az alkalmazás regisztrációs adatait. Ha a Visual Studio használ, válassza ki a project (projekt gyökérmappájában), kattintson a jobb gombbal és válassza ki: `Add`  >  `New Item`  >  `JavaScript File`. Nevezze el`msalconfig.js`
2.  Adja hozzá a következő kódot a `msalconfig.js` fájlt:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Cserélje le <code>Enter_the_Application_Id_here</code> az imént regisztrált alkalmazás azonosítójával
</li>
</ol>
