---
title: A Azure Active Directory B2C felhasználói felület (UI) dinamikus testreszabása Egyéni szabályzatok használatával | Microsoft Docs
description: Több védjegyezési tapasztalat támogatása a HTML5-/CSS-tartalommal, amely dinamikusan változik futásidőben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 43c0da3ca8fa4b2f74d48b0e202cc56bc8b9406c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227226"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Azure Active Directory B2C: A felhasználói felület konfigurálása dinamikus tartalommal egyéni házirendek használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) egyéni házirendek használatával a paramétereket egy lekérdezési karakterláncban küldheti el. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt ismerteti, hogyan szabhatja testre a Azure AD B2C felhasználói felületét a *dinamikus tartalommal* egyéni házirendek használatával. Első lépésként tekintse meg a [felhasználói felület testreszabása Egyéni szabályzatban](active-directory-b2c-ui-customization-custom.md)című témakört.

>[!NOTE]
>A Azure AD B2C cikk, a [felhasználói felület testreszabásának konfigurálása egyéni szabályzatokban](active-directory-b2c-ui-customization-custom.md)a következő alapvető tudnivalókat ismerteti:
> * Az oldal felhasználói felületének (UI) testreszabása funkció.
> * Alapvető eszközök az oldal felhasználói felületének testreszabási funkciójának teszteléséhez a minta tartalma alapján.
> * Az egyes lapok típusának alapvető felhasználói felületi elemei
> * Ajánlott eljárások a funkció alkalmazásához.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>A HTML5/CSS-sablonokra mutató hivatkozás hozzáadása a felhasználói útra

Egy egyéni szabályzatban a tartalom definíciója határozza meg a HTML5-oldal URI-JÁT, amelyet egy adott felhasználói felületi lépéshez használ (például a bejelentkezési vagy a regisztrációs lapokat). Az alapházirend határozza meg az alapértelmezett megjelenést és működést úgy, hogy a HTML5-fájlok URI-JÁT (a CSS-ben) mutat. A kiterjesztési szabályzatban módosíthatja a megjelenést és a működést úgy, hogy felülbírálja a HTML5-fájl Tartalomdefinícióban. A tartalmi definíciók a HTML5-/CSS-fájlok a megfelelő módon történő elvégzésével meghatározott külső tartalmak URL-jeit tartalmazzák.

A `ContentDefinitions` szakasz több `ContentDefinition` XML-elemet tartalmaz. Az `ContentDefinition` elem ID attribútuma határozza meg a tartalom-definícióhoz kapcsolódó oldal típusát. Vagyis az elem határozza meg azt a környezetet, amelyet egy egyéni HTML5/CSS-sablon fog alkalmazni. A következő táblázat a IEF motor által felismert tartalmi definíciós azonosítókat, valamint az azokhoz kapcsolódó lapokat ismerteti.

| Tartalom definíciójának azonosítója | Alapértelmezett HTML5-sablon| Leírás |
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hiba lap**. Ez az oldal akkor jelenik meg, ha kivételt vagy hibát észlel. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitás-szolgáltató kiválasztása lap**. Ezen a lapon megtekintheti azokat az identitás-szolgáltatókat, amelyeket a felhasználók a bejelentkezés során választhatnak. A lehetőségek általában a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook és a Google +, vagy a helyi fiókok. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Az identitás-szolgáltató kiválasztása a regisztrációhoz**. Ezen a lapon megtekintheti azokat az azonosítókat, amelyeket a felhasználók a regisztráció során választhatnak. A lehetőségek a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook, a Google + vagy a helyi fiókok. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Elfelejtett jelszó lap**. Ez az oldal olyan űrlapot tartalmaz, amelyet a felhasználóknak be kell fejezniük a jelszó-visszaállítás elindításához.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók bejelentkezési lapja**. Ez az oldal egy olyan űrlapot tartalmaz, amely egy e-mail-cím vagy egy Felhasználónév alapján helyi fiókkal jelentkezik be. Az űrlap tartalmazhatja a szövegbeviteli és a jelszó-beviteli mezőt is. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók regisztrációs lapja**. Ez az oldal egy olyan űrlapot tartalmaz, amely az e-mail-cím vagy a Felhasználónév alapján létrehozott helyi fiókra regisztrál. Az űrlap különböző beviteli vezérlőket tartalmazhat, például a következőket: szövegbeviteli mező, jelszó-beviteli mező, választógomb, egyszeres kijelölés legördülő lista és többszörös kijelölés jelölőnégyzet. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication oldal**. Ezen a lapon a felhasználók megtekinthetik a telefonszámokat (szöveg vagy hang használatával) a regisztráció vagy a bejelentkezés során. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Közösségi fiók regisztrálása oldal**. Ez az oldal egy olyan űrlapot tartalmaz, amelyet a felhasználóknak be kell fejezniük, amikor regisztrálnak egy meglévő fiókkal egy közösségi identitás-szolgáltatótól. Ez az oldal hasonló az előző közösségi fiók regisztrálási lapjához, a jelszó-beviteli mezők kivételével. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil frissítése lap**. Ez a lap egy űrlapot tartalmaz, amelyet a felhasználók frissíthetnek a profiljuk frissítéséhez. Ez az oldal hasonló a közösségi fiók regisztrációs oldalához, a jelszó-beviteli mezők kivételével. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrációs vagy bejelentkezési oldal**. Ez a lap kezeli a felhasználói regisztrációt és a bejelentkezési folyamatot. A felhasználók a vállalati identitás-szolgáltatók, a közösségi identitás-szolgáltatók, például a Facebook vagy a Google +, vagy a helyi fiókok használatával használhatják a felhasználókat.  |

## <a name="serving-dynamic-content"></a>Dinamikus tartalom kiszolgálása
A [felhasználói felület testreszabása Egyéni házirendben](active-directory-b2c-ui-customization-custom.md) című cikkben a HTML5-fájlokat az Azure Blob Storage-ba töltheti fel. Ezek a HTML5-fájlok statikusak, és ugyanazokat a HTML-tartalmakat jelenítik meg az egyes kérésekhez.

Ebben a cikkben egy ASP.NET-webalkalmazást használ, amely fogadja a lekérdezési karakterlánc paramétereit, és ennek megfelelően válaszol.

Ebben az útmutatóban a következőket végezheti el:
* Hozzon létre egy ASP.NET Core webalkalmazást, amely a HTML5-sablonokat tárolja.
* Egyéni HTML5-sablon hozzáadása, _Unified. cshtml_.
* A webalkalmazás közzététele Azure App Service.
* Állítsa be a CORS a webalkalmazáshoz.
* A `LoadUri` HTML5-fájlra mutató elemek felülbírálása.

## <a name="step-1-create-an-aspnet-web-app"></a>1\. lépés: ASP.NET-webapp létrehozása

1. A Visual Studióban hozzon létre egy projektet a **fájl** > **új** > **projekt**lehetőség kiválasztásával.

2. Az **új projekt** ablakban válassza  > a **Visual C#**  **web** > **ASP.net Core Web Application (.net Core)** elemet.

3. Nevezze el az alkalmazást (például *contoso. AADB2C. UI*), majd kattintson **az OK gombra**.

    ![Új Visual Studio-projekt létrehozása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Válassza ki  a webalkalmazás-sablont.

5. Állítsa be a hitelesítést hitelesítés **nélkül**.

    ![Webalkalmazás sablonjának kiválasztása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. A projekt létrehozásához válassza az **OK** lehetőséget.

## <a name="step-2-create-mvc-view"></a>2\. lépés: MVC-nézet létrehozása
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>2,1. lépés: A B2C beépített HTML5-sablon letöltése
Az egyéni HTML5-sablon a Azure AD B2C beépített HTML5-sablonon alapul. Letöltheti az [egyesített. html fájlt](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) , vagy letöltheti a sablont az alapszintű [csomagból](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Ezzel a HTML5-fájllal létrehozhat egy egyesített regisztrációs vagy bejelentkezési oldalt.

### <a name="step-22-add-the-mvc-view"></a>2,2. lépés: Az MVC-nézet hozzáadása
1. Kattintson a jobb gombbal a nézetek/Kezdőlap mappára, majd **vegyen fel** > **új elemeket**.

    ![Új elem menüpont hozzáadása a Visual Studióban](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Az **új elem hozzáadása – contoso. AADB2C. UI** ablakban válassza a **Web > ASP.net**lehetőséget.

3. Válassza az **MVC-nézet lapot**.

4. A **név** mezőben módosítsa a nevet az **Unified. cshtml**értékre.

5. Válassza a **Hozzáadás** lehetőséget.

    ![Új elem hozzáadása párbeszédpanel a Visual Studióban az MVC View oldalon kiemelve](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Ha az *egyesített. cshtml* fájl már nincs megnyitva, kattintson duplán a fájlra a megnyitásához, majd törölje a fájl tartalmát.

7. Ebben az útmutatóban eltávolítjuk az elrendezési oldalra mutató hivatkozást. Adja hozzá a következő kódrészletet az _Unified. cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Nyissa meg Azure AD B2C beépített HTML5-sablonból letöltött _Unified. cshtml_ fájlt.

9. Cserélje le az "a" jeleket (@) dupla jelre (@ @).

10. Másolja ki a fájl tartalmát, és illessze be az elrendezés definíciója alá. A kódnak így kell kinéznie:

    ![egységes. cshtml fájl a HTML5 hozzáadása után](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>2,3. lépés: Háttérkép módosítása

`ID` `src` **Keressemeg https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** azt az elemet, amely a background_background_image értéket tartalmazza, majd cserélje le az értéket az értékre, vagy bármely más háttérképet, amelyet használni szeretne `<img>` .

![IMG-elem egyéni background_background_image src értékkel](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>2,4. lépés: Nézet hozzáadása az MVC-vezérlőhöz

1. Nyissa meg a **Controllers\HomeController.cs**, és adja hozzá a következő metódust:

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Ez a kód azt adja meg, hogy a metódusnak meg kell-e *tekinteni a nézet* sablonját, hogy választ lehessen megjeleníteni a böngészőnek. Mivel nem explicit módon adjuk meg a *nézet* sablonjának nevét, az MVC alapértelmezett értéke az _egyesített. cshtml_ View fájl használata a */views/Home* mappában.

    Az _egyesített_ metódus hozzáadása után a kódnak a következőhöz hasonlóan kell kinéznie:

    ![A nézet megjelenítéséhez módosítsa a vezérlőt](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. Hibakeresés a webalkalmazásban, és győződjön meg arról, hogy az _egyesített_ oldal elérhető (például `http://localhost:<Port number>/Home/unified`:).

### <a name="step-25-publish-to-azure"></a>2,5. lépés: Közzététel az Azure platformon
1. **Megoldáskezelő**kattintson a jobb gombbal a **contoso. AADB2C. UI** projektre, majd válassza a **Közzététel**lehetőséget.

    ![Közzététel Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Válassza ki a **Microsoft Azure app Service** csempét, majd válassza a **Közzététel**lehetőséget.

    ![Új Microsoft Azure létrehozása App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    Megnyílik a **Create app Service (létrehozás** ) ablak. Ebben az esetben elkezdheti létrehozni az összes szükséges Azure-erőforrást a ASP.NET-webalkalmazás Azure-ban való futtatásához.

    > [!NOTE]
    > További információ a közzétételről: [ASP.net-Webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).

3. A **Web App Name (webalkalmazás neve** ) mezőben adjon meg egy egyedi nevet (érvényes karakterek: a-z, a-z, 0-9 és a kötőjel (-). A webalkalmazás URL-címe `http://<app_name>.azurewebsites.NET`, amelyben a `<app_name>` a webalkalmazás neve. Elfogadhatja az automatikusan létrehozott nevet is, amely már egyedi.

4. A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

    ![App Service tulajdonságainak megadása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    A létrehozási folyamat befejezése után a varázsló közzéteszi a ASP.NET-webalkalmazást az Azure-ban, majd elindítja az alkalmazást az alapértelmezett böngészőben.

5. Másolja az _egyesített_ lap URL-címét (például _https://< APP_NAME >. azurewebsites. net/Home/Unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>3\. lépés: CORS konfigurálása Azure App Serviceban
1. A [Azure Portal](https://portal.azure.com/)válassza a **app Services**lehetőséget, majd válassza ki az API-alkalmazás nevét.

    ![Válassza ki az API-alkalmazást a Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. A **Beállítások** szakasz **API** területén válassza a **CORS**lehetőséget.

    ![A CORS App Service menüjében Kiemelt menüelem Azure Portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. A **CORS** ablak **engedélyezett** források mezőjében tegye a következők egyikét:

    * Adja meg azt az URL-címet vagy URL-címet, amely számára engedélyezni szeretné a JavaScript-hívásokat. A beírt URL-címeken az összes kisbetűs betűt kell használnia.
    * A csillag (*) érték megadásával adhatja meg, hogy az összes forrás tartomány fogadva legyen-e.

4. Kattintson a **Mentés** gombra.

    ![CORS-beállítások lap, csillaggal kiemelve az engedélyezett Eredetekben](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    A **Mentés**gombra kattintva az API-alkalmazás a megadott URL-címekről fogadja a JavaScript-hívásokat.

## <a name="step-4-html5-template-validation"></a>4\. lépés: HTML5-sablon ellenőrzése
A HTML5-sablon használatra kész. Azonban a `ContentDefinition` kódban nem érhető el. Az egyéni szabályzathoz `ContentDefinition` való hozzáadás előtt győződjön meg az alábbiakról:
* A tartalom HTML5-kompatibilis és elérhető.
* A CORS engedélyezve van a tartalom-kiszolgáló.

    >[!NOTE]
    >A [test-CORS.org](https://test-cors.org/) webhelyén ellenőrizheti, hogy az a hely, ahol a tartalmat üzemelteti, engedélyezve van-e a CORS, és képes-e a CORS-kérelmek tesztelésére.

* A kiszolgált tartalom biztonságos **https**-kapcsolaton keresztül.
* *Abszolút URL-címeket*használ, például `https://yourdomain/content`az összes hivatkozáshoz, a CSS-tartalomhoz és a képekhez.

## <a name="step-5-configure-your-content-definition"></a>5\. lépés: A tartalom definíciójának konfigurálása
A konfigurálásához `ContentDefinition`tegye a következőket:
1. Nyissa meg a szabályzat alapfájlját (például *TrustFrameworkBase. XML*).

2. Keresse meg az `<ContentDefinitions>` elemet, majd másolja a `<ContentDefinitions>` csomópont teljes tartalmát.

3. Nyissa meg a kiterjesztési fájlt (például *TrustFrameworkExtensions. XML*), majd keresse meg `<BuildingBlocks>` az elemet. Ha az elem nem létezik, adja hozzá.

4. Illessze be annak a `<ContentDefinitions>` csomópontnak a teljes tartalmát, amelyet az `<BuildingBlocks>` elem gyermeke másolt.

5. Keresse meg a `<ContentDefinition>` csomópontot `Id="api.signuporsignin"` , amely a másolt XML-fájlban található.

6. Módosítsa a `LoadUri` _~/Tenant/default/Unified_ értéket a _https://< APP_NAME >. azurewebsites. net/Home/Unified_értékre.
    Az egyéni szabályzatnak a következőhöz hasonlóan kell kinéznie:

    ![Példa XML-kódrészletre Kiemelt Tartalomdefinícióban elemmel](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6\. lépés: Töltse fel a szabályzatot a bérlőbe
1. A [Azure Portal](https://portal.azure.com)váltson a [Azure ad B2C bérlő kontextusára](active-directory-b2c-navigate-to-b2c-context.md), majd válassza a **Azure ad B2C**lehetőséget.

2. Válassza az **identitási élmény keretrendszert**.

3. Válassza **az összes szabályzat**lehetőséget.

4. Válassza a **szabályzat feltöltése**lehetőséget.

5. Jelölje be a **házirend felülírása, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkExtensions. XML* fájlt, és ellenőrizze, hogy átadja-e az érvényesítést.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7\. lépés: Az egyéni házirend tesztelése a Futtatás most használatával
1. Válassza ki **Azure ad B2C beállításokat**, majd válassza az **identitási élmény keretrendszert**.

    >[!NOTE]
    >A futtatáshoz szükség van legalább egy alkalmazás előregisztrálására a bérlőn. Az alkalmazások regisztrálásának megismeréséhez tekintse meg a Azure AD B2C első [lépések](active-directory-b2c-get-started.md) című cikket, vagy az [alkalmazás regisztrációját](active-directory-b2c-app-registration.md) ismertető cikket.

2. Nyissa meg a **B2C_1A_signup_signin**, a függő entitás (RP) egyéni házirendjét, majd válassza a **Futtatás most**lehetőséget.
    A korábban létrehozott háttérrel megtekintheti az egyéni HTML5-t.

    ![A regisztrálási vagy bejelentkezési szabályzat](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>8\. lépés: Dinamikus tartalom hozzáadása
Módosítsa a hátteret a _campaignId_nevű lekérdezési karakterlánc paraméter alapján. Az RP-alkalmazás (webes és mobil alkalmazások) elküldi a paramétert Azure AD B2Cnak. A házirend beolvassa a paramétert, és elküldi az értékét a HTML5-sablonnak.

### <a name="step-81-add-a-content-definition-parameter"></a>8,1. lépés: Content definition paraméter hozzáadása

Adja hozzá `ContentDefinitionParameters` az elemet a következő módon:
1. Nyissa meg a szabályzat *SignUpOrSignin* -fájlját (például *SignUpOrSignin. XML*).

2. A csomópont alatt adja hozzá a `UserJourneyBehaviors` csomópontot: `<DefaultUserJourney>`

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ContentDefinitionParameters>
          <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>8,2. lépés: A kód módosításával fogadja el a lekérdezési karakterlánc paraméterét, és cserélje le a háttérképet
Módosítsa a HomeController `unified` metódust, hogy fogadja el a campaignId paramétert. A metódus ezután ellenőrzi a paraméter értékét, és ennek megfelelően `ViewData["background"]` állítja be a változót.

1. Nyissa meg a *Controllers\HomeController.cs* fájlt, majd módosítsa `unified` a metódust úgy, hogy hozzáadja a következő kódrészletet:

    ```csharp
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. Keresse meg `<img>` az azonosítóval `background_background_image`rendelkező elemet, `@ViewData["background"]`és `src` cserélje le a értéket a értékre.

    ![IMG elem Kiemelt src értékkel ](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8,3: Töltse fel a módosításokat, és tegye közzé a szabályzatot
1. Tegye közzé a Visual Studio-projektet Azure App Service.

2. Töltse fel a *SignUpOrSignin. XML* szabályzatot a Azure ad B2Cra.

3. Nyissa meg a **B2C_1A_signup_signin**, a feltöltött RP egyéni szabályzatot, majd válassza a **Futtatás most**lehetőséget.
    Ugyanazt a háttérképet kell látnia, amely korábban meg volt jelenítve.

4. Másolja az URL-címet a böngésző címsorába.

5. Adja hozzá a _campaignId_ lekérdezési karakterlánc paramétert az URI-hoz. Adja `&campaignId=hawaii`meg például a következő ábrán látható módon:

    ![CampaignId lekérdezési karakterlánc paraméterrel jelölt URI](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. A Hawaii háttérkép megjelenítéséhez válassza az **ENTER billentyűt** .

    ![Bejelentkezési oldal regisztrálása a Hawaii-rendszerkép egyéni hátterével](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Módosítsa az értéket *Tokióra*, majd válassza az **ENTER billentyűt**.
    A böngésző megjeleníti a tokiói hátteret.

    ![Bejelentkezési oldal regisztrálása a Tokyo-rendszerkép egyéni hátterével](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>9\. lépés: A felhasználó többi útvonalának módosítása
Ha bejelöli a **regisztráció most** hivatkozást a bejelentkezési oldalon, a böngésző az alapértelmezett háttérképet jeleníti meg, nem pedig a megadott képet. Ez a viselkedés azért fordul elő, mert csak a regisztrációs vagy bejelentkezési oldalt módosította. Az önérvényesítő tartalom további definícióinak módosítása:
1. Lépjen vissza a "2. lépés" kifejezésre, és tegye a következőket:

    a. Töltse le a *selfasserted* fájlt.

    b. Másolja a fájl tartalmát.

    c. Hozzon létre egy új nézetet, *selfasserted*.

    d. Adja hozzá a *selfasserted* a **saját** vezérlőhöz.

2. Lépjen vissza a "4. lépés" kifejezésre, és tegye a következőket:

    a. A bővítmény házirendjében keresse meg a `<ContentDefinition>` `Id="api.selfasserted"`, `Id="api.localaccountsignup"`a és `Id="api.localaccountpasswordreset"`a csomópontot.

    b. Állítsa az `LoadUri` attribútumot a *selfasserted* URI-ra.

3. Lépjen vissza a "Step 8,2" értékre, és módosítsa a kódot, hogy fogadja a lekérdezési karakterlánc paramétereit, de ezúttal a *selfasserted* függvénynek.

4. Töltse fel a *TrustFrameworkExtensions. XML* szabályzatot, és ellenőrizze, hogy átadja-e az érvényesítést.

5. Futtassa a házirend-tesztet, majd válassza a **regisztráció most** lehetőséget az eredmény megtekintéséhez.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Választható A teljes szabályzat fájljainak és kódjának letöltése
* Miután elvégezte az [Egyéni szabályzatok használatának első lépései útmutatót](active-directory-b2c-get-started-custom.md) , javasoljuk, hogy a saját egyéni házirend-fájljaival hozza létre a forgatókönyvet. Az Ön referenciája alapján megadta a [minta házirend-fájlokat](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* A Visual Studio-megoldás teljes kódját a [következő hivatkozásra töltheti le: minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




