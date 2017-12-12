## <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz

Ebben a lépésben azt kell konfigurálja az átirányítási URL-cím az alkalmazás regisztrációs adatait, és adja meg az alkalmazásazonosítót a JavaScript SPA-alkalmazáshoz.

### <a name="configure-redirect-url"></a>Az átirányítási URL-CÍMEK konfigurálása

Konfigurálja a `Redirect URL` a index.html lap a webkiszolgáló URL-CÍMÉT a fenti mezőben, majd kattintson az *frissítés*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>A Visual Studio utasításokat átirányítási URL-cím beszerzése
> Az átirányítási URL-cím beszerzéséhez hajtsa végre az alábbi utasításokat:
> 1.    A *Megoldáskezelőben*, válassza ki a projektet, és tekintse meg a `Properties` ablakban (Ha nem látja a Tulajdonságok ablak, nyomja le az `F4`)
> 2.    Másolja a értéket `URL` a vágólapra:<br/> ![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Illessze be az érték egy `Redirect URL` Ez a lap tetején kattintson a`Update`

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Az átirányítási URL-ként Python
> A Python a web server port parancssorból állíthatja be. Ez az interaktív telepítés a 8080-hivatkozásban, de a kezelőfelület szabadon használhatja az összes rendelkezésre álló portot használ. Minden esetben kövesse az alábbi utasításokat egy átirányítási URL-címet az alkalmazás-regisztrációs adatainak beállításához:<br/>
> Állítsa be `http://localhost:8080/` , egy `Redirect URL` tetején a lap, vagy használjon `http://localhost:[port]/` egyéni TCP-port használata (Ha *[port]* a egyéni TCP-port száma), és kattintson a "Frissítés"

### <a name="configure-your-javascript-spa-application"></a>A JavaScript SPA-alkalmazások konfigurálása

1.  Hozzon létre egy fájlt `msalconfig.js` tartalmazó az alkalmazás regisztrációs adatait. Ha a Visual Studio használ, válassza ki a project (projekt gyökérmappájában), kattintson a jobb gombbal és válassza ki: `Add`  >  `New Item`  >  `JavaScript File`. Nevezze el`msalconfig.js`
2.  Adja hozzá a következő kódot a `msalconfig.js` fájlt:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
