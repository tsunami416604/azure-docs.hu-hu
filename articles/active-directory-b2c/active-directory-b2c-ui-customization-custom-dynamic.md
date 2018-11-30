---
title: Az Azure Active Directory B2C felhasználói felületének (UI) egyéni szabályzatok használatával dinamikus testreszabása |} A Microsoft Docs
description: Támogatja a HTML5-alapú/CSS tartalom, amely dinamikusan változik a futásidőben több márkajelzési környezeteket.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 11a14bc8b593e5e7d81e9bdbd4ac4ee3b2bbecaa
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582887"
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Az Azure Active Directory B2C: Dinamikus tartalom a felhasználói felület konfigurálása egyéni szabályzatok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C használatával egyéni házirendek (Azure AD B2C-vel) el lehet küldeni egy lekérdezési karakterlánc paramétert. A paraméterek a HTML-végpontnak való átadásával dinamikusan változtathatja az oldal tartalmát. Például megváltoztathatja a háttérképet az Azure AD B2C regisztrációs vagy bejelentkezési oldalán egy olyan paraméter alapján, amelyet a web- vagy mobilalkalmazásából ad át. 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk foglalkozik az Azure AD B2C felhasználói felület testreszabása a *dinamikus tartalom* egyéni szabályzatok használatával. Első lépésként lásd [egyéni házirendet a felhasználói felületének testreszabását](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>Az Azure AD B2C-cikkben [konfigurálása a felhasználói felület testreszabása a egyéni házirendet](active-directory-b2c-ui-customization-custom.md), a következő alapjait ismerteti:
> * A lap felhasználói felületének (UI) testreszabási funkcióról.
> * Alapvető fontosságú eszközök lap felhasználói felületi testreszabási szolgáltatásának tesztelési minta tartalom használatával.
> * Írja be az egyes oldalak fő felhasználói felületi elemeket.
> * Ez a funkció alkalmazása ajánlott eljárásai.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Hivatkozás hozzáadása a felhasználói interakciósorozat HTML5/CSS-sablonok

Egyéni házirend tartalomdefiníció határozza meg a HTML5-alapú oldal URI-ja, amely egy megadott felhasználói felület lépést (például a bejelentkezési vagy regisztrációs oldal) szolgál. Az alap házirend alapértelmezett megjelenését és működését mutat, egy URI-t a HTML5-fájlok (az a CSS) határozza meg. A bővítmény a házirend módosíthatja a letiltásával a HTML5-fájl a LoadUri megjelenését és működését. Tartalomdefiníciók külső tartalomra, amely a HTML5-alapú/CSS-fájlokat, szükség szerint elvégezte határozzák URL-címeket tartalmazza. 

A `ContentDefinitions` szakasz tartalmaz egy sorozatát `ContentDefinition` XML-elemeket. Az azonosító attribútuma a `ContentDefinition` elem határozza meg, amely kapcsolódik a tartalomdefiníció az oldal. Azt jelenti az elem határozza meg a környezetet, amely a HTML5-alapú/CSS egyéni sablont fogja alkalmazni. A következő táblázat ismerteti a tartalomdefiníció IEF motor és a hozzájuk kapcsolódó lap típusok által elfogadottakkal azonosítók készletét.

| Tartalomdefiníció azonosítója | Alapértelmezett HTML5-sablon| Leírás | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hibalap**. Ez az oldal jelenik meg kivételt vagy hibát észlelt. |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitásszolgáltató kiválasztása oldal**. Ezen a lapon találhatók az identitás-szolgáltatóktól, amelyeket a felhasználók a bejelentkezés során. A beállítások általában: vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például a Facebook és a Google + vagy helyi fiókot. |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identity provider adatgyűjtésre vonatkozó felhasználói előfizetési**. Ezen a lapon találhatók az identitás-szolgáltatóktól, amelyeket a felhasználók a regisztráció során. A beállítások vagy a vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például a Facebook és a Google + vagy a helyi fiókok. |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Elfelejtett jelszó oldal**. Ez az oldal által felhasználók végrehajtandó kezdeményezni a jelszó-visszaállítás űrlap tartalmazza.  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók bejelentkezési oldal**. Ez az oldal számára egy egy e-mail-cím vagy felhasználónév alapján helyi fiókkal történő bejelentkezés űrlapot tartalmaz. Az űrlap egy bemeneti szövegmezőt és a jelszó mező tartalmazhat. |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók regisztrációs oldala**. Ez az oldal tartalmazza egy űrlap egy helyi fiókot, egy e-mail-cím vagy felhasználónév alapján. Az űrlap tartalmazhat különböző bemeneti vezérlők, például: egy szövegbeviteli mezőbe, a jelszó mező, választógomb, egyszeri kiválasztásos legördülő listák, és a többszörös kijelöléssel jelölje be. |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **A multi-factor authentication-oldal**. Ezen a lapon felhasználók ellenőrizheti azok telefonszámok (a szöveges vagy szóbeli) regisztrálási vagy bejelentkezési során. |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Közösségi fiók regisztrálására szolgáló oldal**. Ezen a lapon, hogy a felhasználók hajtsa végre, amikor regisztrál egy közösségi identitásszolgáltatót egy meglévő fiókot az űrlapot tartalmaz. Ezen a lapon a hasonló az előző közösségi fiók regisztrációs oldalon, a jelszó számbeviteli mezők kivételével. |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Frissítés profillapján**. Ezen a lapon, hogy a profil frissítése a felhasználók űrlapot tartalmaz. Ez az oldal hasonlít a közösségi fiók regisztrációs oldalon, a jelszó számbeviteli mezők kivételével. |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített regisztrálási vagy bejelentkezési oldal**. Ezen az oldalon a felhasználói regisztráció és a bejelentkezési folyamat kezeli. Felhasználók a vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például Facebook vagy a Google + vagy helyi fiókot.  |

## <a name="serving-dynamic-content"></a>Dinamikus tartalom kiszolgálása
Az a [konfigurálása a felhasználói felület testreszabása a egyéni házirendet](active-directory-b2c-ui-customization-custom.md) a cikkben az Azure Blob storage HTML5-fájlok feltöltése. HTML5-fájlokhoz statikus, és az azonos HTML tartalom az egyes kérések jelennek meg. 

Ebben a cikkben használata ASP.NET-webalkalmazás, amely fogadja el a lekérdezési karakterlánc paraméterei, és ennek megfelelően válaszolnak. 

Ez az útmutató akkor:
* Hozzon létre egy ASP.NET Core-webalkalmazás, amely futtatja a HTML5-sablonokat. 
* Adjon hozzá egy egyéni sablont HTML5 _unified.cshtml_. 
* A webalkalmazás közzététele az Azure App Service-ben. 
* Eltérő eredetű erőforrások megosztása (CORS) a webalkalmazás beállítása.
* Bírálja felül a `LoadUri` elemeit képeztük le a HTML5-fájlra mutat.

## <a name="step-1-create-an-aspnet-web-app"></a>1. lépés: Egy ASP.NET-webalkalmazás létrehozása

1. A Visual Studióban hozzon létre egy projektet kiválasztásával **fájl** > **új** > **projekt**.

2. Az a **új projekt** ablakban válassza **Visual C#** > **webes** > **ASP.NET Core-webalkalmazás (.NET Core)**.

3. Adjon nevet az alkalmazásnak (például *Contoso.AADB2C.UI*), majd válassza ki **OK**.

    ![Új Visual Studio-projekt létrehozása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Válassza ki a **webalkalmazás** sablont.

5. A hitelesítés beállítása **nincs hitelesítés**.

    ![Webalkalmazás-sablon kiválasztása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. A projekt létrehozásához válassza az **OK** lehetőséget.

## <a name="step-2-create-mvc-view"></a>2. lépés: Az MVC-nézet létrehozása
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>2.1. lépés: A B2C beépített HTML5-sablon letöltése
A HTML5-alapú egyéni sablont az Azure AD B2C-vel beépített HTML5 sablonon alapul. Letöltheti a [unified.html fájl](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) vagy töltse le a sablont a [alapszintű csomagja](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). Ez a HTML5-fájl használatával hozzon létre egy egyesített regisztrálási vagy bejelentkezési oldal.

### <a name="step-22-add-the-mvc-view"></a>2.2. lépés: Az MVC-nézet hozzáadása
1. Kattintson a jobb gombbal a nézetek készletkövetést mappát, majd **Hozzáadás** > **új elem**.

    ![MVC-új elem hozzáadása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Az a **új elem hozzáadása – Contoso.AADB2C.UI** ablakban válassza **Web > ASP.NET**.

3. Válassza ki **MVC-weblap megtekintéséhez**.

4. Az a **neve** változtassa a nevet a **unified.cshtml**.

5. Válassza a **Hozzáadás** lehetőséget.

    ![MVC-nézet hozzáadása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Ha a *unified.cshtml* fájlban még nem nyitott, kattintson duplán a fájlra a megnyitásához, és törölje a fájl tartalmát.

7. Ebben a bemutatóban elrendezés-lapra mutató hivatkozás eltávolítása. Adja hozzá a következő kódrészletet a _unified.cshtml_:

    ```csharp
    @{
        Layout = null;
    }
    ```

8. Nyissa meg a _unified.cshtml_ sablonból az Azure AD B2C-vel beépített HTML5 letöltött fájl.

9. Cserélje le az egyetlen kukacjelek (@) két jeleket (@).

10. Másolja ki a fájl tartalmát, és illessze be alább az elrendezés definíciója. A kód hasonlóan kell kinéznie:

    ![Miután hozzáadta a HTML5 Unified.cshtml fájl](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>2.3. lépés: A háttérkép módosítása

Keresse meg a `<img>` elem, amely tartalmazza a `ID` érték *background_background_image*, majd cserélje le a `src` értéket **https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1** vagy bármely más obrázek pozadí is használni szeretné.

![Az oldal háttér módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>2.4. lépés: A nézet hozzáadása az MVC-vezérlő

1. Nyissa meg **Controllers\HomeController.cs**, és adja hozzá az alábbi metódust: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Ez a kód határozza meg, hogy a módszert kell használnia egy *nézet* sablonfájl renderelni a böngészőnek adott válaszban. Mivel nincs explicit módon megadott neve a *nézet* sablonfájl, MVC alapértelmezés szerint az a _unified.cshtml_ a fájl megtekintése a */nézetek készletkövetést* mappa.
    
    Miután hozzáadta a _egységes_ metódus, a kód hasonlóan kell kinéznie:
    
    ![A nézet megjelenítése a vezérlő módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. A webalkalmazás hibakeresése, és ellenőrizze, hogy a _egységes_ lapon érhető el (például `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>2.5. lépés: Közzététel az Azure-bA
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **Contoso.AADB2C.UI** projektre, és válassza ki **közzététel**.

    ![A Microsoft Azure App Service-ben való közzététele](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Válassza ki a **Microsoft Azure App Service** csempére, majd válassza ki **közzététel**.

    ![Hozzon létre új Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    A **létrehozása App Service** ablak nyílik meg. Ez a megkezdéséhez az összes szükséges Azure-erőforrások létrehozása az ASP.NET-webalkalmazás futtatása az Azure-ban.

    > [!NOTE]
    > Közzétételre vonatkozó további információkért lásd: [ASP.NET-webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. Az a **webalkalmazás neve** mezőbe írja be egy egyedi névre (érvényes karakterek: a – z, A-Z, 0 – 9 és a kötőjel (-). A webalkalmazás URL-címe `http://<app_name>.azurewebsites.NET`, amelyben a `<app_name>` a webalkalmazás neve. Elfogadhatja az automatikusan létrehozott nevet is, amely már egyedi.

4. A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

    ![Adja meg a tulajdonságokat App Service-ben](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    A létrehozási folyamat befejezése után a varázsló közzéteszi az ASP.NET webalkalmazást az Azure-ba, és ezután elindítja az alkalmazást, az alapértelmezett böngészőben.

5. Másolja az URL-címét a _egységes_ lap (például _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>3. lépés: A CORS konfigurálása az Azure App Service-ben
1. Az a [az Azure portal](https://portal.azure.com/)válassza **App Services**, majd válassza ki az API-alkalmazás neve.

    ![API-alkalmazás kiválasztása az Azure Portalon](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Az a **beállítások** szakasz **API** szakaszban jelölje be **CORS**.

    ![Válassza ki a CORS-beállítások](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Az a **CORS** ablakban, a a **engedélyezett eredetek** tegye a következők egyikét:

    * Adja meg az URL-címe vagy URL-címek, amelyek számára engedélyezni a JavaScript-hívásokat. Csak kisbetűk használata a beírt URL-címeket kell.
    * Adja meg a csillag (*) adja meg, hogy minden eredettartományból elfogadja a hívásokat.

4. Kattintson a **Mentés** gombra.

    ![A CORS ablak](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Miután kiválasztotta **mentése**, az API-alkalmazásba, fogadja el a megadott URL-címekről jövő hívásokat. 

## <a name="step-4-html5-template-validation"></a>4. lépés: HTML5 sablon érvényesítése
A HTML5-alapú sablon készen áll a használatra. Azonban nem érhető el a a `ContentDefinition` kódot. Hozzáadása előtt `ContentDefinition` az egyéni házirend biztosítja, hogy:
* A tartalom HTML5 megfelelő és elérhető-e.
* A webtartalom-kiszolgáló a CORS engedélyezve van.

    >[!NOTE]
    >Győződjön meg arról, hogy a hely hol tárolja a tartalmat a CORS engedélyezve van, és tesztelheti a CORS-kérések, lépjen a [test-cors.org](http://test-cors.org/) webhelyén. 

* A biztonságos keresztül szolgálatban tartalma **HTTPS**.
* Használ *abszolút URL-CÍMEK*, mint például *https://yourdomain/content*, minden hivatkozások, a CSS-tartalom és a képeket.

## <a name="step-5-configure-your-content-definition"></a>5. lépés: A tartalom definíció konfigurálása
Konfigurálása `ContentDefinition`, tegye a következőket:
1. Nyissa meg a szabályzat alapszintű fájlt (például *TrustFrameworkBase.xml*).

2. Keresse meg a `<ContentDefinitions>` elemet, majd másolja ki a teljes tartalmát a `<ContentDefinitions>` csomópont.

3. Nyissa meg a kiterjesztésű fájlt (például *TrustFrameworkExtensions.xml*), és keressen rá a `<BuildingBlocks>` elemet. Ha az elem nem létezik, adja hozzá.

4. Illessze be a teljes tartalmát a `<ContentDefinitions>` csomópont gyermekeként kimásolt a `<BuildingBlocks>` elemet. 

5. Keresse meg a `<ContentDefinition>` tartalmazó csomópont `Id="api.signuporsignin"` XML-másolta.

6. Módosítsa az értéket a `LoadUri` a _~/tenant/default/unified_ való _https://<app_name>.azurewebsites.net/home/unified_.  
    Az egyéni házirendet a következőhöz hasonlóan kell kinéznie:
    
    ![A tartalom definition](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6. lépés: A szabályzat feltöltése a bérlőhöz
1. Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd válassza ki **Azure AD B2C-vel**.

2. Válassza ki **identitás-kezelőfelületi keretrendszer**.

3. Válassza ki **összes szabályzat**.

4. Válassza ki **szabályzat feltöltése**.

5. Válassza ki a **szabályzat felülírása, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkExtensions.xml* fájlt, és győződjön meg arról, hogy érvényesítési továbbítja.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7. lépés: Az egyéni házirend tesztelése a Futtatás most
1. Válassza ki **Azure AD B2C-beállítások**, majd válassza ki **identitás-kezelőfelületi keretrendszer**.

    >[!NOTE]
    >Futtatás most kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, feltöltött, és válassza ki **Futtatás most**.  
    Megtekintheti a háttérben történő, amelyet korábban hozott létre az egyéni HTML5 kell lennie.

    ![A regisztrálási vagy bejelentkezési szabályzat](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>8. lépés: A dinamikus tartalom hozzáadása
Módosítsa a lekérdezési sztring paramétereként nevű alapján háttérben _campaignId_. Az RP-alkalmazás (webes és mobilalkalmazások) Azure AD B2C-t küld a paraméter. A házirend beolvassa a paramétert, és elküldi az értékét a HTML5-sablont. 

### <a name="step-81-add-a-content-definition-parameter"></a>8.1. lépés: Tartalomdefiníció paraméter hozzáadása

Adja hozzá a `ContentDefinitionParameters` elem az alábbiak szerint:
1. Nyissa meg a *SignUpOrSignin* a házirend-fájlját (például *SignUpOrSignin.xml*).

2. Keresse meg a `<DefaultUserJourney>` csomópont. 

3. Az a `<DefaultUserJourney>` csomópont, adja hozzá a következő XML-részletet:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>8.2. lépés: Módosítania kell a kódot, fogadja el a lekérdezési sztring paramétereként, és cserélje le a háttérkép
Módosítsa a HomeController `unified` fogadja el a campaignId paraméter metódust. A metódus ezután ellenőrzi a paraméter értékét, és beállítja a `ViewData["background"]` változó ennek megfelelően.

1. Nyissa meg a *Controllers\HomeController.cs* fájlt, és módosítsa a `unified` metódus adja hozzá a következő kódrészletet:

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

2. Keresse meg a `<img>` element s ID `background_background_image`, és cserélje le a `src` értéket `@ViewData["background"]`.

    ![Az oldal háttér módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: töltse fel a módosításokat, és a házirend közzététele
1. A Visual Studio-projekt közzététele az Azure App Service-ben.

2. Töltse fel a *SignUpOrSignin.xml* szabályzatot az Azure AD B2C-t.

3. Nyissa meg **B2C_1A_signup_signin**, a helyreállítási pont Védettként egyéni házirendet, amelyet feltöltött, és válassza ki **Futtatás most**.  
    A korábban használt azonos háttérkép kell megjelennie.

4. Másolja az URL-címet a böngésző címsorában.

5. Adja hozzá a _campaignId_ lekérdezési karakterlánc paraméterének URI-ra. Hozzáadhat például `&campaignId=hawaii`, ahogy az alábbi képen is látható:

    ![Az oldal háttér módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Válassza ki **Enter** a Hawaii háttérkép megjelenítéséhez.

    ![Az oldal háttér módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Módosítsa az értéket *Tokió*, majd válassza ki **Enter**.  
    A böngészőben a Tokiói háttérben jeleníti meg.

    ![Az oldal háttér módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>9. lépés: Módosítsa a többi felhasználói interakciósorozat
Ha a **regisztráció** a bejelentkezési lapon, a böngésző megjeleníti az alapértelmezett háttérkép, a lemezkép nem meghatározott. Ez a viselkedés akkor merül fel, mert csak a regisztrálási vagy bejelentkezési oldal módosította. A helyi Vyhodnocení tartalomdefiníciók részeinek módosítása:
1. Lépjen vissza a "2. lépésre", és tegye a következőket:

    a. Töltse le a *selfasserted* fájlt.

    b. Másolja a fájlok tartalmát.

    c. Hozzon létre egy új nézetet, *selfasserted*.

    d. Adjon hozzá *selfasserted* , a **kezdőlap** vezérlő.

2. "4. lépés" lépjen vissza, és tegye a következőket: 

    a. A bővítmény a házirend, keresse meg a `<ContentDefinition>` tartalmazó csomópont `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, és `Id="api.localaccountpasswordreset"`.

    b. Állítsa be a `LoadUri` attribútumot a *selfasserted* URI-t.

3. Lépjen vissza "Lépés 8.2", és módosítsa a kódokat a lekérdezési karakterlánc paraméterei, de ezúttal a fogadja el a *selfasserted* függvény. 

4. Töltse fel a *TrustFrameworkExtensions.xml* szabályzatot, és győződjön meg arról, hogy érvényesítési továbbítja.

5. A házirend-teszt futtatása, és válassza ki **regisztráció** az eredmény megjelenítéséhez.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Nem kötelező) A teljes házirend fájlok és a kód letöltése
* Miután elvégezte a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a forgatókönyv a saját egyéni házirend-fájlok használatával hozhat létre. Referenciaként adtunk meg [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Letöltheti a teljes kódját [mintát a Visual Studio-megoldás referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




