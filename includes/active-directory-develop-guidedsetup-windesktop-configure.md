
## <a name="register-your-application"></a>Alkalmazás regisztrálása
Az alkalmazás az alábbi két módon is regisztrálhat.

### <a name="option-1-express-mode"></a>1. lehetőség: Expressz mód
Az alkalmazás a következőképpen gyorsan regisztrálhatja:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Válassza ki **hozzáadhat egy alkalmazást**.

3. Az a **alkalmazásnév** mezőbe írjon be egy nevet az alkalmazásnak.

4. Győződjön meg arról, hogy a **interaktív telepítés** jelölőnégyzet be kiválasztva, majd jelölje ki **létrehozása**.

5. Kövesse az utasításokat az beszerzése az Alkalmazásazonosító, és illessze be a kódját.

### <a name="option-2-advanced-mode"></a>2. lehetőség: Speciális módban
Az alkalmazás regisztrálása és az alkalmazás regisztrációs adatokat hozzáadni a megoldás, tegye a következőket:
1. Ha az alkalmazás már még nem regisztrált, folytassa a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app).

2. Válassza ki **hozzáadhat egy alkalmazást**.

3. Az a **alkalmazásnév** mezőbe írjon be egy nevet az alkalmazásnak. 

4. Győződjön meg arról, hogy a **interaktív telepítés** jelölőnégyzet nincs bejelölve, és jelölje ki **létrehozása**.

5. Válassza ki **hozzáadása Platform**, jelölje be **natív alkalmazás**, majd válassza ki **mentése**.

6. Az a **Alkalmazásazonosító** mezőbe GUID-azonosítóját.

7. Nyissa meg a Visual Studio, nyissa meg a *App.xaml.cs* fájlt, és lecseréli `your_client_id_here` az alkalmazás azonosítójával, amelyet csak regisztrált, és másolja.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
