---
title: REST-szolgáltatás biztonságossá tétele Ügyféltanúsítványok használatával
titleSuffix: Azure AD B2C
description: Az ügyfél-tanúsítványok használatával biztonságossá teheti az egyéni REST API jogcímek cseréjét a Azure AD B2Cban
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 98606443707c183a15a4bacd26646c3eac6c8a69
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186913"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>A REST-szolgáltatás biztonságossá tétele Ügyféltanúsítványok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A kapcsolódó cikkekben [olyan Rest-szolgáltatást hozunk létre](rest-api-claims-exchange-dotnet.md) , amely a Azure Active Directory B2C (Azure ad B2C) szolgáltatással kommunikál.

Ebből a cikkből megtudhatja, hogyan korlátozhatja az Azure-webalkalmazásokhoz (REST-alapú API-hoz) való hozzáférést ügyféltanúsítvány használatával. Ezt a mechanizmust TLS kölcsönös hitelesítésnek vagy *ügyféltanúsítvány-alapú hitelesítésnek*nevezzük. Csak olyan szolgáltatások férhetnek hozzá a szolgáltatáshoz, amelyek megfelelő tanúsítvánnyal rendelkeznek (például Azure AD B2C).

>[!NOTE]
>A REST-szolgáltatás a [http alapszintű hitelesítés](secure-rest-api-dotnet-basic-auth.md)használatával is biztonságossá tehető. Az egyszerű HTTP-hitelesítés azonban kevésbé biztonságos, mint az ügyféltanúsítvány. Javasoljuk, hogy a jelen cikkben leírtak szerint az ügyféltanúsítvány-alapú hitelesítés használatával gondoskodjon a REST-szolgáltatás biztonságossá tételéről.

Ez a cikk a következőket ismerteti:
* Állítsa be a webalkalmazást az ügyféltanúsítvány-alapú hitelesítés használatára.
* Töltse fel a tanúsítványt Azure AD B2C szabályzat kulcsaiba.
* Konfigurálja az egyéni házirendet az ügyféltanúsítvány használatára.

## <a name="prerequisites"></a>Előfeltételek
* Hajtsa végre az [REST API jogcím-cserék integrálása](rest-api-claims-exchange-dotnet.md) című cikkben ismertetett lépéseket.
* Érvényes tanúsítvány (. pfx-fájl titkos kulccsal) beolvasása.

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>1\. lépés: webalkalmazás konfigurálása ügyféltanúsítvány-alapú hitelesítéshez
Ha **Azure app Servicet** szeretne beállítani az Ügyféltanúsítványok megköveteléséhez, állítsa a webalkalmazás `clientCertEnabled` hely beállítást *igaz*értékre. A módosítás elvégzéséhez a Azure Portal nyissa meg a webalkalmazás lapot. A bal oldali navigációs menü **Beállítások** területén válassza az **SSL-beállítások**elemet. Az **Ügyféltanúsítványok** szakaszban kapcsolja be a **bejövő ügyféltanúsítvány** beállítást.

>[!NOTE]
>Győződjön meg arról, hogy a Azure App Service terve standard vagy nagyobb. További információ: [Azure app Service tervek részletes áttekintése](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>A **információ** tulajdonság beállításával kapcsolatos további információkért lásd: a [TLS kölcsönös hitelesítés konfigurálása a Web Apps szolgáltatáshoz](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>2\. lépés: a tanúsítvány feltöltése Azure AD B2C szabályzat kulcsaiba
Miután beállította `clientCertEnabled` az *igaz*értékre, a REST API-val folytatott kommunikációhoz Ügyféltanúsítvány szükséges. Az ügyféltanúsítvány beszerzéséhez, feltöltéséhez és tárolásához a Azure AD B2C bérlőn tegye a következőket:
1. A Azure AD B2C-bérlőben válassza a **B2C-beállítások** > **identitási élmény keretrendszere**lehetőséget.

2. A bérlőben elérhető kulcsok megtekintéséhez válassza a **házirend kulcsok**elemet.

3. Válassza a **Hozzáadás** lehetőséget.
    Megnyílik a **kulcs létrehozása** ablak.

4. A **Beállítások** mezőben válassza a **feltöltés**lehetőséget.

5. A **név** mezőbe írja be a következőt: **B2cRestClientCertificate**.
    A rendszer automatikusan hozzáadja a *B2C_1A_* előtagot.

6. A **fájlfeltöltés** mezőben válassza ki a tanúsítvány. pfx fájlját, amely titkos kulccsal rendelkezik.

7. A **jelszó** mezőbe írja be a tanúsítvány jelszavát.

    ![Töltse fel a szabályzat kulcsot a kulcs létrehozása oldalon Azure Portal](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-cert-upload.png)

7. Kattintson a **Létrehozás** gombra.

8. Ha meg szeretné tekinteni a bérlőben elérhető kulcsokat, és győződjön meg arról, hogy létrehozta a `B2C_1A_B2cRestClientCertificate` kulcsot, válassza a **házirend-kulcsok**elemet.

## <a name="step-3-change-the-technical-profile"></a>3\. lépés: a technikai profil módosítása
Az ügyféltanúsítvány-alapú hitelesítés az egyéni házirendben való támogatásához módosítsa a technikai profilt a következő módon:

1. A munkakönyvtárában nyissa meg az *TrustFrameworkExtensions. XML* kiterjesztési házirend fájlját.

2. Keresse meg a `Id="REST-API-SignUp"`t tartalmazó `<TechnicalProfile>` csomópontot.

3. Keresse meg a `<Metadata>` elemet.

4. Módosítsa a *AuthenticationType* a *ClientCertificate*értékre a következőképpen:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Közvetlenül a záró `<Metadata>` elem után adja hozzá a következő XML-kódrészletet:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    A kódrészlet hozzáadása után a technikai profilnak a következő XML-kódhoz hasonlóan kell kinéznie:

    ![ClientCertificate-hitelesítés XML-elemeinek beállítása](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>4\. lépés: a szabályzat feltöltése a bérlőbe

1. A [Azure Portal](https://portal.azure.com)válassza ki a **címtár + előfizetés** ikont a portál eszköztárán, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

1. A Azure Portal keresse meg és válassza a **Azure ad B2C**lehetőséget.

1. Válassza az **identitási élmény keretrendszert**.

1. Válassza **az összes szabályzat**lehetőséget.

1. Válassza a **szabályzat feltöltése**lehetőséget.

1. Jelölje be a **házirend felülírása, ha létezik** jelölőnégyzetet.

1. Töltse fel a *TrustFrameworkExtensions. XML* fájlt, és ellenőrizze, hogy átadja-e az érvényesítést.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>5\. lépés: az egyéni házirend tesztelése a Futtatás most használatával
1. Nyissa meg **Azure ad B2C a beállításokat**, majd válassza az **identitási élmény keretrendszert**.

    >[!NOTE]
    >A futtatáshoz szükség van legalább egy alkalmazás előregisztrálására a bérlőn. Az alkalmazások regisztrálásának megismeréséhez tekintse meg a Azure AD B2C első [lépések](tutorial-create-tenant.md) című cikket, vagy az [alkalmazás regisztrációját](tutorial-register-applications.md) ismertető cikket.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitás (RP) egyéni házirendjét, majd válassza a **Futtatás most**lehetőséget.

3. Tesztelje a folyamatot úgy, hogy beírja a **teszt** kifejezést a **megadott név** mezőbe.
    Azure AD B2C hibaüzenet jelenik meg az ablak tetején.

    ![A megadott név szövegmező kiemelve és a bemeneti érvényesítési hiba látható](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-test.png)

4. A **megadott név** mezőbe írjon be egy nevet (a "test" helyett).
    Azure AD B2C aláírja a felhasználót, majd elküld egy lojalitási számot az alkalmazásnak. Jegyezze fel a JWT példában szereplő számot:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Ha a hibaüzenet jelenik meg, *a név érvénytelen, adjon meg egy érvényes nevet*, amely azt jelenti, hogy Azure ad B2C sikeresen meghívta a REST-szolgáltatást, miközben bemutatta az ügyféltanúsítványt. A következő lépés a tanúsítvány ellenőrzése.

## <a name="step-6-add-certificate-validation"></a>6\. lépés: tanúsítvány-ellenőrzés hozzáadása
Az Azure AD B2C által a REST-szolgáltatásnak küldött ügyféltanúsítványt nem a Azure App Service platform ellenőrzi, kivéve, hogy a tanúsítvány létezik-e. A tanúsítvány érvényesítése a webalkalmazás feladata.

Ebben a szakaszban olyan minta ASP.NET-kódot ad hozzá, amely ellenőrzi a tanúsítvány tulajdonságait a hitelesítési célokra.

> [!NOTE]
>Az ügyféltanúsítvány-alapú hitelesítés Azure App Service konfigurálásával kapcsolatos további információkért lásd: [a TLS kölcsönös hitelesítés konfigurálása a Web Apps szolgáltatáshoz](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6,1 Alkalmazásbeállítások hozzáadása a projekt web. config fájljához
A korábban létrehozott Visual Studio-projektben adja hozzá az alábbi Alkalmazásbeállítások a *web. config* fájlhoz a `appSettings` elem után:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Cserélje le a tanúsítvány **tulajdonosának nevét**, **kiállítójának nevét**és a tanúsítvány **ujjlenyomatának** értékeit a tanúsítvány értékeire.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6,2 a IsValidClientCertificate függvény hozzáadása
Nyissa meg a *Controllers\IdentityController.cs* fájlt, majd adja hozzá a `Identity` Controller osztályhoz a következő függvényt:

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }

    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

Az előző mintakód esetében a tanúsítványt csak akkor fogadjuk el érvényesnek, ha a következő feltételek teljesülnek:
* A tanúsítvány nem járt le, és a kiszolgáló aktuális idejénél aktív.
* A tanúsítvány `Subject` neve megegyezik a `ClientCertificate:Subject` alkalmazás beállításának értékével.
* A tanúsítvány `Issuer` neve megegyezik a `ClientCertificate:Issuer` alkalmazás beállításának értékével.
* A tanúsítvány `thumbprint`a megegyezik a `ClientCertificate:Thumbprint` alkalmazás beállításának értékével.

>[!IMPORTANT]
>A szolgáltatás érzékenységének függvényében előfordulhat, hogy további érvényesítéseket kell hozzáadnia. Előfordulhat például, hogy tesztelni szeretné, hogy a tanúsítványlánc megbízható legfelső szintű hitelesítésszolgáltatóhoz, kiállító szervezet nevének ellenőrzéséhez és így tovább.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6,3 hívja meg a IsValidClientCertificate függvényt
Nyissa meg a *Controllers\IdentityController.cs* fájlt, majd a `SignUp()` függvény elején adja hozzá a következő kódrészletet:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

A kódrészlet hozzáadása után a `Identity` vezérlőnek a következő kódhoz hasonlóan kell kinéznie:

![Tanúsítvány-ellenőrzési kód hozzáadása](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>7\. lépés: a projekt közzététele az Azure-ban és tesztelés
1. **Megoldáskezelő**kattintson a jobb gombbal a **contoso. AADB2C. API** projektre, majd válassza a **Közzététel**lehetőséget.

2. Ismételje meg a "6. lépés" műveletet, és tesztelje újra az egyéni szabályzatot a tanúsítvány érvényesítésével. Próbálja meg futtatni a szabályzatot, és ellenőrizze, hogy minden működik-e az érvényesítés hozzáadása után.

3. A *web. config* fájlban módosítsa a `ClientCertificate:Subject` értékét **érvénytelenre**. Futtassa újra a szabályzatot, és hibaüzenet jelenik meg.

4. Módosítsa az értéket vissza **érvényesre**, és győződjön meg arról, hogy a szabályzat hívhatja a REST API.

Ha ezt a lépést szeretné elhárítani, tekintse meg a [naplók gyűjtése a Application Insights használatával](troubleshoot-with-application-insights.md)című témakört.

## <a name="optional-download-the-complete-policy-files-and-code"></a>Választható A teljes szabályzat fájljainak és kódjának letöltése
* Miután elvégezte az [Egyéni szabályzatok használatának első lépései útmutatót](custom-policy-get-started.md) , javasoljuk, hogy a saját egyéni házirend-fájljaival hozza létre a forgatókönyvet. Az Ön referenciája alapján megadta a [minta házirend-fájlokat](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* A Visual Studio-megoldás teljes kódját a [következő hivatkozásra töltheti le: minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).
