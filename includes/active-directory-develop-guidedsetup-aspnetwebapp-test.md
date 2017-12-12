## <a name="test-your-code"></a>Tesztelheti a kódját

A Visual Studióban az alkalmazás teszteléséhez nyomja le az **F5** a projekt futtatásához. A böngésző megnyitja a http://<span></span>localhost: {port} helyre, és tekintse meg a **jelentkezzen be Microsoft** gombra. Kattintson a gombra, majd indítsa el a bejelentkezési folyamat.

Ha készen a teszt futtatása, használja a Microsoft Azure Active Directory (Azure AD-) fiók (munkahelyi vagy iskolai fiókkal) vagy személyes Microsoft-fiókkal (<span>élő.</span> COM vagy <span>outlook.</span> COM) jelentkezzen be.

![Jelentkezzen be Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Jelentkezzen be Microsoft-fiókja](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Alkalmazás-eredmények megtekintése
Miután bejelentkezik, a felhasználót a rendszer átirányítja a kezdőlapot, a webhely. A kezdőlap a Microsoft alkalmazás-regisztrálási portál alkalmazás regisztrációs adatait a megadott HTTPS URL-CÍMÉT. A kezdőlap üdvözlő üzenet tartalmazza a "Hello \<felhasználó >," a Kijelentkezés hivatkozásra, és megtekintheti a felhasználói jogcímek mutató hivatkozást. A hivatkozás a felhasználói jogcímek nyit a **engedélyezés** korábban létrehozott vezérlő.

### <a name="browse-to-see-the-users-claims"></a>Keresse ki a felhasználói jogcímek
A felhasználói jogcímeket, jelölje ki a hivatkozásra kattintva tallózással keresse meg a vezérlő nézet csak a hitelesített felhasználók számára elérhető.

#### <a name="view-the-claims-results"></a>A jogcímek eredmények megtekintése
Után keresse meg a vezérlő nézetet, a felhasználók alapszintű tulajdonságainak tartalmazó kell megjelennie:

|Tulajdonság |Érték |Leírás |
|---|---|---|
|**Name (Név)** |Felhasználó teljes neve | A felhasználó nagyapja vezeték- és keresztneve.
|**Felhasználónév** |felhasználó<span>@domain.com</span> | A felhasználónév, amely a felhasználó azonosítására szolgál.
|**Tárgy** |Tárgy |Egy karakterlánc, amely egyedileg azonosítja a felhasználó a weben.|
|**Bérlő azonosítója** |GUID | A **guid** , amely egyedi módon a felhasználó az Azure AD szervezete jelöli.|

Ezenkívül megjelenik egy tábla, amelyek a hitelesítési kérelem minden jogcím. További információkért lásd: a [listája, amelyeket egy Azure AD Azonosítót jogkivonatban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>(Választható) engedélyezés attribútummal rendelkezik: metódusra-hozzáférés tesztelése
Való hozzáférés tesztelése a **engedélyezés** vezérlő a felhasználói jogcímek névtelen felhasználóként, kövesse az alábbi lépéseket:
1. Jelölje ki a felhasználó kijelentkezik, majd fejezze be a kijelentkezési folyamat hivatkozást.
2. A böngészőben, írja be a http://<span></span>localhost: {port} / hitelesített a tartományvezérlő által védett eléréséhez a **engedélyezés** attribútum.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Kívánt eredmény elérése érdekében a hozzáférést a védett tartományvezérlő után
Használja a védett tartományvezérlő felé történő hitelesítésre kéri.

## <a name="additional-information"></a>További információ

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>A teljes webhelyet védelme
A teljes webhely védeni a **Global.asax** fájlt, adja hozzá a **AuthorizeAttribute** attribútumot a **GlobalFilters** szűrés a **alkalmazás _Induljon el** módszert:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>Az alkalmazás bejelentkezési elérésének korlátozása
Alapértelmezés szerint személyes fiókjait, mint az Outlook.com-os, live.com és mások is jelentkezzen be az alkalmazásba. Munkahelyi vagy iskolai fiókok a szervezeteknek, amelyek az Azure ad-vel integrált is bejelentkezés alapértelmezés szerint.

Az alkalmazás felhasználói bejelentkezési hozzáférés korlátozásához, több lehetőség is elérhető.

#### <a name="restrict-access-to-a-single-organization"></a>Egy szervezet való hozzáférés korlátozása
Korlátozhatja a csak az olyan felhasználói fiókot, amely egyetlen alkalmazás bejelentkezési hozzáférést az Azure AD-szervezet:
1. Az a **web.config** fájl, módosítsa a **bérlői** paraméter. Módosítsa az értéket **közös** a szervezet bérlő neve például **contoso.onmicrosoft.com**.
2. Az a **OWIN indítási** osztály, állítsa be a **ValidateIssuer** argumentumának **igaz**.

#### <a name="restrict-access-to-a-list-of-organizations"></a>A szervezetek listáját való hozzáférés korlátozása
Bejelentkezési hozzáférés csak felhasználói fiókot, amely szerepel a listában az engedélyezett szervezetek az Azure AD szervezetében korlátozhatja:
1. Az a **web.config** fájlban, az a **OWIN indítási** osztály, állítsa be a **ValidateIssuer** argumentumának **igaz**.
2. Állítsa a **ValidIssuers** paraméter engedélyezett szervezetek listájához.

#### <a name="use-a-custom-method-to-validate-issuers"></a>Egyéni módszer segítségével ellenőrizheti azokat kibocsátók
Megvalósíthat egy egyéni módszer használatával kiállítók érvényesítéséhez a **IssuerValidator** paraméter. Ez a paraméter használatával kapcsolatos további információkért olvassa el a [TokenValidationParameters osztály](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) az MSDN Webhelyén.

[!INCLUDE  [Help and support](./active-directory-develop-help-support-include.md)]
