---
title: 'Az Azure Active Directory B2C: Biztonságos a RESTful szolgáltatás ügyfél-tanúsítványok használatával'
description: Az egyéni REST API-jogcímek cseréjét az Azure AD B2C biztonságos ügyfél-tanúsítványok használatával
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.openlocfilehash: fb5c8a48a676a2909cce3c4d126218e48a4c8ce2
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Biztonságos a RESTful szolgáltatás ügyfél-tanúsítványok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A kapcsolódó cikkében akkor [hozzon létre egy RESTful szolgáltatás](active-directory-b2c-custom-rest-api-netfw.md) , amely együttműködik az Azure Active Directory B2C (az Azure AD B2C).

Ebből a cikkből megtanulhatja a hozzáférés korlátozása az Azure web app (RESTful API) ügyfél-tanúsítvány használatával. Ezt a módszert nevezik TLS kölcsönös hitelesítést, vagy *ügyféltanúsítvány-alapú hitelesítés*. Csak a megfelelő tanúsítványok, például az Azure AD B2C-ben szolgáltatások férhetnek hozzá a szolgáltatáshoz.

>[!NOTE]
>A RESTful szolgáltatás használatával is biztosíthassa [egyszerű HTTP-hitelesítés](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Azonban egyszerű HTTP-hitelesítés tekinthető kevésbé biztonságos keresztüli ügyfél-tanúsítványt. Azt javasoljuk, biztonságossá tételéhez a RESTful szolgáltatás ügyféltanúsítvány-alapú hitelesítés használatával, a cikkben leírtak szerint.

Ez a cikk részletek hogyan:
* A webalkalmazás beállítása ügyféltanúsítvány-alapú hitelesítés használatára.
* A tanúsítvány feltöltése az Azure AD B2C házirend kulcsokhoz.
* Az egyéni házirend számára, hogy az ügyféltanúsítvány használatára konfigurálja.

## <a name="prerequisites"></a>Előfeltételek
* Hajtsa végre a a [integrálja a REST API-t jogcímek cseréjét](active-directory-b2c-custom-rest-api-netfw.md) cikk.
* Szerezzen be egy érvényes tanúsítványt (egy titkos kulcsot .pfx fájl).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>1. lépés: Az ügyféltanúsítvány-alapú hitelesítés webalkalmazás konfigurálása
Beállítása **Azure App Service** ügyféltanúsítványok megköveteléséhez, állítsa be a webes alkalmazás `clientCertEnabled` beállítással hely *igaz*. Ezt a módosítást a REST API-t kell használnia. A beállítás a megoldást vezet be az Azure portálon keresztül érhető el. Állapítsa meg a beállítás a RESTful alkalmazás a **beállítások** menüben, a **Fejlesztőeszközök**, jelölje be **erőforrás-kezelő**.

>[!NOTE]
>Győződjön meg arról, hogy az Azure App Service-csomag Standard vagy nagyobb. További információkért lásd: [Azure App Service-csomagok részletes áttekintése](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).


Használjon [Azure erőforrás-kezelővel (előzetes verzió)](https://resources.azure.com) beállítása a **clientCertEnabled** tulajdonságot *igaz*, a következő ábrán látható módon:

![Az Azure erőforrás-kezelőben clientCertEnabled beállítása](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-resource-explorer.png)

>[!NOTE]
>További információt a beállítás a **clientCertEnabled** tulajdonság, lásd: [TLS konfigurálása kölcsönös hitelesítést a web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

>[!TIP]
>Azt is megteheti, hogy egyszerűbb létrehozható a REST API-hívás, használhatja a [ARMClient](https://github.com/projectkudu/ARMClient) eszköz.

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>2. lépés: A tanúsítvány feltöltése az Azure AD B2C házirend kulcsok
Miután beállította `clientCertEnabled` való *igaz*, a kommunikációt a RESTful API-val ügyféltanúsítványt igényel. Szerezze be, és töltse fel az ügyféltanúsítvány tárolása az Azure AD B2C-bérlő, tegye a következőket: 
1. Válassza ki az Azure AD B2C-bérlő **B2C beállítások** > **identitás élmény keretrendszer**.

2. Válassza ki, ha elérhetők a bérlői kulcsok **házirend kulcsok**.

3. Válassza a **Hozzáadás** lehetőséget.  
    A **hozható létre kulcs** ablak nyílik meg.

4. Az a **beállítások** mezőben válassza **feltöltése**.

5. Az a **neve** mezőbe írja be **B2cRestClientCertificate**.  
    Az előtag *B2C_1A_* a rendszer automatikusan hozzáadja.

6. Az a **fájlfeltöltés** mezőben adja meg a tanúsítvány .pfx fájlját és a titkos kulcs.

7. Az a **jelszó** mezőbe írja be a tanúsítvány jelszavát.

    ![Töltse fel a házirend-kulcs](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Kattintson a **Létrehozás** gombra.

8. Elérhető a bérlői kulcsok megtekintéséhez, és győződjön meg arról, hogy létrehozta a `B2C_1A_B2cRestClientCertificate` kulcs, jelölje be **házirend kulcsok**.

## <a name="step-3-change-the-technical-profile"></a>3. lépés: A műszaki profil módosítása
Az egyéni házirend ügyféltanúsítvány-alapú hitelesítés támogatásához a műszaki profil módosítása a következő módon:

1. A munkakönyvtár, nyissa meg a *TrustFrameworkExtensions.xml* bővítményfájl házirend.

2. Keresse meg a `<TechnicalProfile>` tartalmazó csomópont `Id="REST-API-SignUp"`.

3. Keresse meg a `<Metadata>` elemet.

4. Módosítsa a *AuthenticationType* való *ClientCertificate*, az alábbiak szerint:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. A záró után azonnal `<Metadata>` elemet, adja hozzá a következő XML-részletet: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Miután hozzáadta a kódrészletet, a műszaki profil a következő XML-kódot kell hasonlítania:

    ![Állítsa be a ClientCertificate hitelesítési XML-elemek](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>4. lépés: Töltse fel a házirend a bérlő

1. A a [Azure-portálon](https://portal.azure.com), váltson a [az Azure AD B2C-bérlő kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd válassza ki **az Azure AD B2C**.

2. Válassza ki **identitás élmény keretrendszer**.

3. Válassza ki **házirend**.

4. Válassza ki **házirend feltöltése**.

5. Válassza ki a **felülírja a házirendet, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkExtensions.xml* fájlt, és ezután győződjön meg arról, hogy teljesíti-e ellenőrző.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>5. lépés: Az egyéni házirend tesztelése Futtatás most használatával
1. Nyissa meg **az Azure AD B2C beállítások**, majd válassza ki **identitás élmény keretrendszer**.

    >[!NOTE]
    >Futtatás most a tenant preregistered kell legalább egy alkalmazás szükséges. Megtudhatja, hogyan kell regisztrálni az alkalmazások, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [regisztrációja](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni házirend feltöltött, és válassza **futtatása most**.

3. A folyamat ellenőrzéséhez írja be a **teszt** a a **Utónév** mezőbe.  
    Az Azure AD B2C hibaüzenet jelenik meg a az ablak tetején.    

    ![A azonossága API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Az a **Utónév** mezőbe írjon be egy nevet (nem a "Test").  
    Az Azure AD B2C a felhasználó regisztrál, és ezután elküldi az alkalmazás hűség számnak. Megjegyzés: Ebben a példában JWT száma:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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
   >Ha hibaüzenet érkezik *a név nem érvényes, adjon meg egy érvényes nevet*, az azt jelenti, hogy az Azure AD B2C sikeresen neve a RESTful szolgáltatás, miközben akkor jelenik meg az ügyféltanúsítványt. A következő lépés, hogy a tanúsítvány érvényesítéséhez.

## <a name="step-6-add-certificate-validation"></a>6. lépés: Tanúsítvány érvényesítése hozzáadása
Az Azure AD B2C küld a RESTful szolgáltatás tanúsítványt is nem az Azure Web Apps platformon, kivéve, ellenőrizze, hogy létezik-e a tanúsítvány érvényesítési változni. A tanúsítvány érvényesítése feladata a webalkalmazás. 

Ebben a szakaszban adja hozzá az ASP.NET mintakód, amely ellenőrzi a tanúsítvány tulajdonságainak hitelesítési célokra.

> [!NOTE]
>Ügyféltanúsítvány-alapú hitelesítés az Azure App Service konfigurálásával kapcsolatos további információkért lásd: [TLS konfigurálása kölcsönös hitelesítést a web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Alkalmazásbeállítások hozzáadása a projekthez web.config fájlhoz
A Visual Studio-projekt korábban létrehozott, adja hozzá a következő alkalmazás beállításait, hogy a *web.config* után fájlt a `appSettings` elem:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Cserélje le a tanúsítványt **tulajdonosnévvel**, **kibocsátónév**, és **tanúsítvány ujjlenyomata** értékek a tanúsítvány értékekkel.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 a IsValidClientCertificate függvény hozzáadása
Nyissa meg a *Controllers\IdentityController.cs* fájlt, és hozzáadhatja a `Identity` vezérlő a következő függvény osztályban: 

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

Előző példakód azt fogadja el a tanúsítványt érvényesnek, csak a következő feltételek teljesülése esetén:
* A tanúsítvány nem járt le és a kiszolgáló aktuális idejét aktív.
* A `Subject` a tanúsítvány neve megegyezik a `ClientCertificate:Subject` Alkalmazásbeállítás értéke.
* A `Issuer` a tanúsítvány neve megegyezik a `ClientCertificate:Issuer` Alkalmazásbeállítás értéke.
* A `thumbprint` a tanúsítvány megegyezik a `ClientCertificate:Thumbprint` Alkalmazásbeállítás értéke.

>[!IMPORTANT]
>Attól függően, hogy a szolgáltatás érzékenysége szükség lehet további ellenőrzések hozzáadása. Például előfordulhat, hogy kell-e a tanúsítvány kapcsolódik egy megbízható legfelső szintű hitelesítésszolgáltatóval, kiállító szervezet nevének érvényesítése és így tovább.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 hívja meg a IsValidClientCertificate függvényt
Nyissa meg a *Controllers\IdentityController.cs* fájlt, és ezt követően elején a `SignUp()` működik, adja hozzá a következő kódrészletet: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Miután hozzáadta a kódrészletet a `Identity` vezérlő a következő kódot hasonlóan kell kinéznie:

![Vegye fel a tanúsítvány érvényesítési kód](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>7. lépés: A projekt közzététele az Azure-ba, és tesztelik azt
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **Contoso.AADB2C.API** projektre, majd válassza ki **közzététel**.

2. Ismételje meg a "6. lépés", és újra tesztelése az egyéni házirend a tanúsítvány-érvényesítéssel. Próbálja meg futtatni a házirendet, és győződjön meg arról, hogy minden működik, miután hozzáadta az érvényesítés.

3. Az a *web.config* fájl, módosítsa az értéket a `ClientCertificate:Subject` való **érvénytelen**. Futtassa újra a házirendet, és megjelenik egy hibaüzenet.

4. Vissza az érték módosítása **érvényes**, és győződjön meg arról, hogy a házirend meghívhatja a REST API-t.

Ha ez a lépés hibaelhárítása van szüksége, tekintse meg a [naplók gyűjtésére Application Insights segítségével](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Választható) A teljes házirend fájlok és a kód letöltése
* Miután elvégezte a [Ismerkedés az egyéni házirendek](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a saját egyéni házirend-fájlok használatával történő létrehozása adott esetben. Referenciaként a adtunk [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Letöltheti a teljes kód látható a [minta Visual Studio megoldás referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
