---
title: "Az Azure Active Directory B2C: Testreszabása az Azure AD B2C felhasználói felület (UI) dinamikusan egyéni házirendek használatával"
description: "Támogatja a HTML5/CSS tartalommal, amely dinamikusan változik a futásidőben több márkajelzési lép."
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: 342e82071778156477d216c9b624a938c48cb37f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-by-using-custom-policies"></a>Az Azure Active Directory B2C: A felhasználói felület konfigurálása dinamikus tartalom egyéni házirendek használatával
Azure Active Directory B2C segítségével (az Azure AD B2C) egyéni házirendek, elküldheti a paraméter egy lekérdezési karakterlánc. Úgy, hogy a paraméter a HTML-végpontot, dinamikusan módosíthatja az oldal tartalmát. Például a háttérkép, a Azure AD B2C regisztráció vagy bejelentkezés lapon adja meg a webkiszolgáló vagy a mobilalkalmazás paramétereinek alapján módosíthatja. 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk foglalkozik az Azure AD B2C felhasználói felület testreszabásával *dinamikus tartalom* egyéni házirendekkel. Első lépésként tekintse meg a [egyéni házirendet a felhasználói felület testreszabásával](active-directory-b2c-ui-customization-custom.md). 

>[!NOTE]
>Az Azure AD B2C cikk [konfigurálása a felhasználói felület testreszabása egyéni házirendet a](active-directory-b2c-ui-customization-custom.md), a következő alapjait ismerteti:
> * A lap felhasználói felület (UI) testreszabási szolgáltatás.
> * Alapvető eszközök lap felhasználói felületi testreszabási szolgáltatásának tesztelési minta tartalmat használatával.
> * Írja be az egyes lapok core felhasználói felületi elemeket.
> * Ez a funkció alkalmazása ajánlott eljárásai.

## <a name="add-a-link-to-html5css-templates-to-your-user-journey"></a>Hivatkozás hozzáadása a felhasználói használatában HTML5/CSS-sablonok

Egyéni házirend tartalom definíció határozza meg a HTML5 lap URI, amely egy megadott felhasználói felületi lépést (például a bejelentkezési és regisztrációs lapok) szolgál. A kiinduló házirend alapértelmezett megjelenését és működését egy URI a HTML5 a fájlokat (a CSS) jelzésével határozza meg. A bővítmény a házirend módosíthatja a HTML5 fájl LoadUri felülbírálásával megjelenését és működését. Tartalom definíciók létrehozásával HTML5/CSS fájlok, szükség szerint definiált külső tartalomra mutató URL-címeket tartalmaz. 

A `ContentDefinitions` szakasz sorozata `ContentDefinition` XML-elemeket. Az ID attribútuma a `ContentDefinition` elem határozza meg a oldal, amely a tartalom definition vonatkozik. Ez azt jelenti, hogy az elem határozza meg a környezetben, amelyet egy egyéni HTML5/CSS-sablont szeretne alkalmazni. A következő táblázat ismerteti a tartalom definíció azonosítóját, amely a IEF motor és a rájuk vonatkozó laptípusokat felismeri.

| Tartalmi azonosító | Alapértelmezett HTML5 sablon| Leírás | 
|-----------------------|--------|-------------|
| *API.Error* | [Exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Hibalap**. Ezen a lapon megjelenik, ha kivétel, vagy hiba történt. |
| *API.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identitás-szolgáltató kiválasztása lapon**. Ezen a lapon, amelyet a felhasználók a bejelentkezés során identitás-szolgáltatóktól sorolja fel. A beállítások általában vállalati identitás-szolgáltatóktól, például a Facebookhoz és Google + vagy helyi fiókok közösségi Identitásszolgáltatók működnek. |
| *API.idpselections.Signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Identity provider adatgyűjtésre vonatkozó felhasználói előfizetési**. Ez a lap felsorolja Identitásszolgáltatók, amelyet a felhasználók a regisztráció során. A lehetőségek állnak a vállalati identitás-szolgáltatóktól, például a Facebookhoz és a Google + közösségi Identitásszolgáltatók, vagy helyi fiók. |
| *API.localaccountpasswordreset* | [selfasserted.HTML](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Elfelejtett jelszó lap**. Ezen a lapon található felhasználók kell végeznie a jelszó alaphelyzetbe állítása kezdeményezése űrlapot.  |
| *API.localaccountsignin* | [selfasserted.HTML](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók bejelentkezési oldalának**. Ez a lap tartalmaz egy e-mail címet vagy egy felhasználónevet alapuló helyi fiókkal bejelentkezni az űrlap. Az űrlap egy bemeneti szövegmező és a jelszó mező tartalmazhat. |
| *API.localaccountsignup* | [selfasserted.HTML](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Helyi fiók bejelentkezési oldal**. Ezen a lapon található iratkozik fel egy helyi fiók, amely egy e-mail címet vagy egy felhasználónevet alapul szolgáló űrlap. Az űrlap különböző bemeneti vezérlőket, például a tartalmazhat: egy szöveges beviteli mezőbe, a jelszó mező, a megfelelő választógombra kattintva, egyetlen legördülő listák, és jelölje ki a jelölőnégyzeteket. |
| *API.phonefactor* | [többtényezős-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Többtényezős hitelesítés lap**. Ezen a lapon, a felhasználók a telefonszámok (segítségével ellenőrizheti szöveges vagy hangos) regisztráció vagy bejelentkezés során. |
| *API.selfasserted* | [selfasserted.HTML](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Közösségi fiók bejelentkezési oldalának**. Ezen a lapon található felhasználók kell végeznie, amikor azok az egy meglévő fiókkal a közösségi identitásszolgáltató regisztráljon űrlapot. Ezen a lapon hasonlít az előző közösségi fiók regisztrációs oldalon, a jelszó számbeviteli mezők kivételével. |
| *API.selfasserted.profileupdate* | [updateprofile.HTML](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Profil update lapon**. Ezen a lapon található felhasználók számára elérhető frissíteni a profilját űrlap. Ezen a lapon hasonlít a közösségi fiók regisztrációs oldalon, a jelszó számbeviteli mezők kivételével. |
| *API.signuporsignin* | [Unified.HTML](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Egyesített előfizetési vagy a bejelentkezési oldal**. Ezen a lapon a felhasználói regisztrációt és a bejelentkezési folyamat kezeli. A felhasználók a vállalati identitás-szolgáltatóktól, közösségi Identitásszolgáltatók, például a Facebook-on vagy a Google + és helyi fiókok.  |

## <a name="serving-dynamic-content"></a>Dinamikus tartalom
Az a [konfigurálása a felhasználói felület testreszabása egyéni házirendet a](active-directory-b2c-ui-customization-custom.md) cikk HTML5 fájlok feltöltése az Azure Blob storage. HTML5 fájlokhoz statikus és leképezési azonos HTML tartalom egyes kérésekre vonatkozóan. 

Ebben a cikkben egy ASP.NET web app alkalmazásban is fogadja el a lekérdezési karakterlánc paramétereket, és ennek megfelelően válaszol használja. 

Ebben a bemutatóban meg:
* Az ASP.NET Core webalkalmazás, amelyen a HTML5-sablonok létrehozása. 
* Vegyen fel egy egyéni sablont HTML5 _unified.cshtml_. 
* A webalkalmazás közzététele az Azure App Service. 
* Eltérő eredetű erőforrások megosztása (CORS) a webalkalmazás beállítása.
* Bírálja felül a `LoadUri` elemek a HTML5-fájlra mutat.

## <a name="step-1-create-an-aspnet-web-app"></a>1. lépés: Egy ASP.NET-webalkalmazás létrehozása

1. A Visual Studio-projekt létrehozása kiválasztásával **fájl** > **új** > **projekt**.

2. Az a **új projekt** ablakban válassza ki **Visual C#** > **webes** > **ASP.NET Core webes alkalmazás (.NET Core)**.

3. Adjon nevet az alkalmazásnak (például *Contoso.AADB2C.UI*), majd válassza ki **OK**.

    ![Új Visual Studio-projekt létrehozása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4. Válassza ki a **webalkalmazás** sablont.

5. A hitelesítés beállítása **nem hitelesítési**.

    ![Webalkalmazás-sablon kiválasztása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6. Válassza ki **OK** a projekt létrehozásához.

## <a name="step-2-create-mvc-view"></a>2. lépés: Az MVC-nézet létrehozása
### <a name="step-21-download-the-b2c-built-in-html5-template"></a>2.1. lépés: A B2C beépített HTML5 sablon letöltése
Az egyéni HTML5-sablon az Azure AD B2C beépített HTML5 sablon alapján. Letöltheti a [unified.html fájl](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) vagy a sablon letöltése [alapszintű csomag](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip). A HTML5-fájl egy egységes előfizetési vagy a bejelentkezési lap létrehozására használhatja.

### <a name="step-22-add-the-mvc-view"></a>2.2. lépés: Az MVC nézet hozzáadása
1. Kattintson a jobb gombbal a nézetek és az otthoni mappát, majd **Hozzáadás** > **új elem**.

    ![MVC új elem hozzáadása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)

2. Az a **új elem hozzáadása - Contoso.AADB2C.UI** ablakban válassza ki **Web > ASP.NET**.

3. Válassza ki **MVC a lapnak a megtekintésére**.

4. Az a **neve** változtassa meg a név **unified.cshtml**.

5. Válassza a **Hozzáadás** lehetőséget.

    ![MVC nézet hozzáadása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png)

6. Ha a *unified.cshtml* fájl még nincs nyitva, kattintson duplán a fájlra a megnyitásához, és törölje a fájl tartalmát.

7. Ez a forgatókönyv azt elrendezés-lap hivatkozásának eltávolítására. Adja hozzá a következő kódrészletet a _unified.cshtml_:

    ```C#
    @{
        Layout = null;
    }
    ```

8. Nyissa meg a _unified.cshtml_ letöltött fájlt, az Azure AD B2C beépített HTML5-sablonból.

9. Cserélje le az egyetlen kukac jelet (@) két kukac jelet (@).

10. Másolja a tartalmat a fájl, és illessze be az elrendezés definíció alatt. A kód hasonlóan kell kinéznie:

    ![Miután hozzáadta a HTML5 Unified.cshtml fájl](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)

### <a name="step-23-change-the-background-image"></a>2.3. lépés: Módosítsa a háttérkép

Keresse meg a `<img>` elem, amely tartalmazza a `ID` érték *background_background_image*, majd cserélje le a `src` rendelkező érték **https://kbdevstorage1.blob.core.windows.net/ eszköz-blobok/19889_en_1** vagy bármely más háttérkép szeretne használni.

![A lap hátterének módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)

### <a name="step-24-add-your-view-to-the-mvc-controller"></a>2.4. lépés: A nézet hozzáadása az MVC-vezérlő

1. Nyissa meg **Controllers\HomeController.cs**, és adja hozzá a következő metódust: 

    ```C
    public IActionResult unified()
    {
        return View();
    }
    ```
    Ez a kód határozza meg, hogy a metódust kell használnia egy *nézet* sablon fájl a böngésző válaszul megjelenítéséhez. Mivel jelenleg nem explicit módon adja meg a nevét a *nézet* sablonfájl, MVC alapértelmezés szerint használja a _unified.cshtml_ nézet fájlt a */nézetek és az otthoni* mappa.
    
    Miután hozzáadta a _egyesített_ metódus, a kód hasonlóan kell kinéznie:
    
    ![Módosítsa a tartományvezérlő a nézet leképezése](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

2. A webalkalmazás hibakeresése, és győződjön meg arról, hogy a _egyesített_ a lap (például `http://localhost:<Port number>/Home/unified`).

### <a name="step-25-publish-to-azure"></a>2.5. lépés: Az Azure-bA közzététele
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **Contoso.AADB2C.UI** projektre, majd válassza ki **közzététel**.

    ![A Microsoft Azure App Service közzététele](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2. Válassza ki a **Microsoft Azure App Service** csempére, majd válassza ki **közzététel**.

    ![Hozzon létre új Microsoft Azure App Service](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    A **létrehozása az App Service** ablak nyílik meg. Azt elkezdheti hozzon létre az összes szükséges Azure-erőforrást az Azure-ban az ASP.NET webalkalmazás futtatásához.

    > [!NOTE]
    > A közzététel kapcsolatos további információkért lásd: [egy ASP.NET-webalkalmazás létrehozása az Azure-ban](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure).

3. Az a **webalkalmazásnév** mezőbe írja be egy egyedi alkalmazásnévvel (érvényes karakterek: a – z, A-Z, 0-9 és kiskötőjel (-). A webalkalmazás URL-címe `http://<app_name>.azurewebsites.NET`, amelyben a `<app_name>` a webalkalmazás neve. Elfogadhatja az automatikusan létrehozott nevet is, amely már egyedi.

4. A **Create** (Létrehozás) gombra kattintva hozzákezdhet az Azure-erőforrások létrehozásához.

    ![Adja meg az App Service tulajdonságokat](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

    A létrehozási folyamat befejezése után a varázsló tesz közzé az ASP.NET webalkalmazás az Azure-ba, és ezután elindítja az alkalmazást, az alapértelmezett böngésző.

5. Másolja az URL-CÍMÉT a _egyesített_ lap (például _https://<app_name>.azurewebsites.net/home/unified_).

## <a name="step-3-configure-cors-in-azure-app-service"></a>3. lépés: A CORS konfigurálása az Azure App Service-ben
1. Az a [Azure-portálon](https://portal.azure.com/), jelölje be **alkalmazásszolgáltatások**, majd válassza ki az API-alkalmazás nevét.

    ![API-alkalmazás kiválasztása az Azure-portálon](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

2. Az a **beállítások** területen a **API** szakaszban jelölje be **CORS**.

    ![Válassza ki a CORS-beállítások](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

3. Az a **CORS** ablakban, a a **engedélyezett eredetet** tegye az alábbiak valamelyikét:

    * Adja meg az URL-cím vagy URL-címek, amely engedélyezi a JavaScript-hívásokat.
    * Adja meg a csillag (*) adja meg, hogy minden eredettartományból elfogadja a hívásokat.

4. Kattintson a **Mentés** gombra.

    ![A CORS ablak](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

    Miután kiválasztotta a **mentése**, az API-alkalmazás JavaScript-hívásokat a megadott URL-címekről származó fogad el. 

## <a name="step-4-html5-template-validation"></a>4. lépés: HTML5 sablon ellenőrzése
A HTML5 sablon használatra készen áll. Azonban nem érhető el a a `ContentDefinition` kódot. Mielőtt hozzáadhatna `ContentDefinition` az egyéni házirend biztosítja, hogy:
* A tartalma HTML5 megfelelő, és elérhető.
* A kiszolgáló CORS engedélyezve van.

    >[!NOTE]
    >Ellenőrizze, hogy hol tárolja a tartalmat a hely engedélyezte a CORS és tesztelheti a CORS-kérelmeket, keresse fel a [teszt-cors.org](http://test-cors.org/) webhelyet. 

* A biztonságos keresztül szolgálatban tartalma **HTTPS**.
* Használ *abszolút URL-CÍMEK*, például a *https://yourdomain/content*, az összes hivatkozások, a CSS-tartalom és a képeket.

## <a name="step-5-configure-your-content-definition"></a>5. lépés: A tartalom definition konfigurálása
Konfigurálása `ContentDefinition`, tegye a következőket:
1. Nyissa meg a házirend az alap fájlt (például *TrustFrameworkBase.xml*).

2. Keresse meg a `<ContentDefinitions>` elemet, majd másolja a teljes tartalmát a `<ContentDefinitions>` csomópont.

3. Nyissa meg a kiterjesztésű fájlt (például *TrustFrameworkExtensions.xml*) majd keresse meg a `<BuildingBlocks>` elemet. Ha az elem nem létezik, adja hozzá.

4. Illessze be a teljes tartalmát a `<ContentDefinitions>` csomópont gyermekeként másolt a `<BuildingBlocks>` elemet. 

5. Keresse meg a `<ContentDefinition>` tartalmazó csomópont `Id="api.signuporsignin"` a másolt XML-kódban.

6. Módosítsa az értéket a `LoadUri` a _~/tenant/default/unified_ való _https://<app_name>.azurewebsites.net/home/unified_.  
    Az egyéni házirendet kell a következőhöz hasonló:
    
    ![A tartalom meghatározása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6. lépés: Töltse fel a házirend a bérlő
1. A a [Azure-portálon](https://portal.azure.com), váltson a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd válassza ki **az Azure AD B2C**.

2. Válassza ki **identitás élmény keretrendszer**.

3. Válassza ki **házirend**.

4. Válassza ki **házirend feltöltése**.

5. Válassza ki a **felülírja a házirendet, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkExtensions.xml* fájlt, és győződjön meg arról, hogy ellenőrzése sikeres.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7. lépés: Az egyéni házirend tesztelése Futtatás most használatával
1. Válassza ki **az Azure AD B2C beállítások**, majd válassza ki **identitás élmény keretrendszer**.

    >[!NOTE]
    >Futtatás most a tenant preregistered kell legalább egy alkalmazás szükséges. Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött, és válassza **futtatása most**.  
    Az egyéni HTML5 a háttérben, korábban létrehozott látni kell lennie.

    ![A regisztráció vagy bejelentkezés házirend](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-add-dynamic-content"></a>8. lépés: A dinamikus tartalom hozzáadása
Módosítsa a lekérdezési karakterlánc-paraméter neve alapján háttérben _campaignId_. A függő Entitás alkalmazás (web- és mobilalkalmazások) küld az Azure AD B2C paraméter. A házirend beolvassa a paramétert, és elküldi az értékét a HTML5-sablon. 

### <a name="step-81-add-a-content-definition-parameter"></a>8.1. lépés: A tartalom definition paraméter hozzáadása

Adja hozzá a `ContentDefinitionParameters` elem a következő tevékenységek végrehajtásával:
1. Nyissa meg a *SignUpOrSignin* házirend fájl (például *SignUpOrSignin.xml*).

2. Keresse meg a `<DefaultUserJourney>` csomópont. 

3. Az a `<DefaultUserJourney>` csomópont, a következő XML-részletet hozzáadása:  

    ```XML
    <UserJourneyBehaviors>
        <ContentDefinitionParameters>
            <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ```

### <a name="step-82-change-your-code-to-accept-a-query-string-parameter-and-replace-the-background-image"></a>8.2. lépés: Módosítania kell a kódot, a lekérdezési karakterlánc paraméterként elfogadására, és cserélje le a háttérkép
Módosítsa a HomeController `unified` módszert fogadja el a campaignId paramétert. A metódus ellenőrzi a paraméter-értéket, és beállítja a `ViewData["background"]` változó ennek megfelelően.

1. Nyissa meg a *Controllers\HomeController.cs* fájlt, és módosítsa a `unified` metódus adja hozzá a következő kódrészletet:

    ```C#
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

2. Keresse meg a `<img>` azonosítójú elemet `background_background_image`, és cserélje le a `src` rendelkező érték `@ViewData["background"]`.

    ![A lap hátterének módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: töltse fel a módosításokat, és tegye közzé a házirend
1. A Visual Studio-projekt közzététele az Azure App Service.

2. Töltse fel a *SignUpOrSignin.xml* Azure AD B2C házirend.

3. Nyissa meg **B2C_1A_signup_signin**, a függő Entitás egyéni házirendet, amely feltöltött, és válassza **futtatása most**.  
    A korábban használt azonos háttérkép kell megjelennie.

4. Másolja az URL-címet a webböngésző cím sorából.

5. Adja hozzá a _campaignId_ lekérdezési karakterlánc az URI-azonosítóval. Adja hozzá például `&campaignId=hawaii`, az alábbi ábrán látható módon:

    ![A lap hátterének módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)

6. Válassza ki **Enter** a Hawaii háttérkép megjelenítéséhez.

    ![A lap hátterének módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)

7. Módosítsa a beállítást *Tokió*, majd válassza ki **Enter**.  
    A böngészőben megjelenik a Tokió háttérben.

    ![A lap hátterének módosítása](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>9. lépés: A felhasználó út részeinek módosítása
Ha bejelöli a **feliratkozás most** a bejelentkezési oldalon, a böngésző hivatkozás megjeleníti az alapértelmezett háttérkép, a lemezkép nincs definiálva. Ez a viselkedés merül fel, mert csak az előfizetési vagy a bejelentkezési oldal módosítása. Az önálló Assert tartalom definíciók részeinek módosítása:
1. Lépjen vissza a "2. lépésre", és tegye a következőket:

    a. Töltse le a *selfasserted* fájlt.

    b. Másolja a fájl tartalma.

    c. Hozzon létre egy új nézetet *selfasserted*.

    d. Adja hozzá *selfasserted* számára a **Home** vezérlő.

2. "4. lépés" visszatérhet, és tegye a következőket: 

    a. A bővítmény a házirend, keresse meg a `<ContentDefinition>` tartalmazó csomópont `Id="api.selfasserted"`, `Id="api.localaccountsignup"`, és `Id="api.localaccountpasswordreset"`.

    b. Állítsa be a `LoadUri` attribútumot a *selfasserted* URI.

3. Lépjen vissza "Lépés 8.2", és módosítsa a kódot a lekérdezési karakterlánc-paraméterrel, de ezúttal a fogadja el a *selfasserted* függvény. 

4. Töltse fel a *TrustFrameworkExtensions.xml* házirend, és győződjön meg arról, hogy ellenőrzése sikeres.

5. A házirend-teszt futtatása, és válassza **feliratkozás most** eredmény megjelenítéséhez.

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Választható) A teljes házirend fájlok és a kód letöltése
* Miután elvégezte a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a saját egyéni házirend-fájlok használatával történő létrehozása adott esetben. Referenciaként a adtunk [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).
* Letöltheti a teljes kód látható a [minta Visual Studio megoldás referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization).




