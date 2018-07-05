---
title: Biztonságos a RESTful szolgáltatás ügyfél-tanúsítványok az Azure Active Directory B2C használatával |} A Microsoft Docs
description: Ügyféltanúsítványok gondoskodhat a REST API-val egyéni jogcímek cseréje az Azure AD B2C-ben
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0832b3b8e0b2b6d7459eeddb8d8e5a93a7f17d09
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448349"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Biztonságos a RESTful szolgáltatás ügyfél-tanúsítványok használatával

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Egy kapcsolódó cikk, [egy RESTful szolgáltatás létrehozásához](active-directory-b2c-custom-rest-api-netfw.md) , amely kommunikál az Azure Active Directory B2C (Azure AD B2C-vel).

Ebből a cikkből elsajátíthatja az Azure web app (RESTful API-t) való hozzáférés korlátozása egy ügyféltanúsítvány használatával. Ez a mechanizmus nevezzük TLS kölcsönös hitelesítést, vagy *ügyféltanúsítvány-alapú hitelesítés*. Csak a megfelelő tanúsítvány, például az Azure AD B2C-szolgáltatások érhetik el a szolgáltatást.

>[!NOTE]
>A RESTful szolgáltatás használatával is biztosíthatja [egyszerű HTTP-hitelesítés](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Azonban egyszerű HTTP-hitelesítés számít kevésbé biztonságos ügyféltanúsítvány keresztül. Azt javasoljuk, hogy a RESTful szolgáltatás védelem az ügyféltanúsítvány-alapú hitelesítés, ebben a cikkben leírtak szerint.

Ez a cikk ismerteti, hogyan:
* Állítsa be a webalkalmazás ügyféltanúsítvány-alapú hitelesítés használatára.
* A tanúsítvány feltöltése az Azure AD B2C szabályzat kulcsához.
* Az egyéni házirendet az ügyféltanúsítvány használatára konfigurálja.

## <a name="prerequisites"></a>Előfeltételek
* A lépések elvégzéséhez a [integrálása a REST API-val jogcím cseréje](active-directory-b2c-custom-rest-api-netfw.md) cikk.
* Szerezzen be egy érvényes tanúsítványt (egy titkos kulcsot .pfx kiterjesztésű fájl).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>1. lépés: Az ügyféltanúsítvány-alapú hitelesítés a webalkalmazás konfigurálása
Beállításához **Azure App Service** ügyféltanúsítványok megköveteléséhez, állítsa be a webalkalmazás `clientCertEnabled` beállítással hely *igaz*. Ezt a módosítást, az Azure Portalon, a webalkalmazás lapjának megnyitásához. A bal oldali navigációs területen **beállítások** kiválasztása **SSL-beállítások**. Az a **ügyféltanúsítványok** területen kapcsolja be a **bejövő ügyféltanúsítvány** lehetőséget.

>[!NOTE]
>Győződjön meg arról, hogy az Azure App Service-csomag Standard vagy nagyobb. További információkért lásd: [Azure App Service díjcsomagjainak részletes áttekintése](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

>[!NOTE]
>További információt a beállítás a **ügyféltanúsítvány engedélyezésével** tulajdonságot használja, lásd: [TLS konfigurálása kölcsönös hitelesítést webalkalmazásokhoz](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>2. lépés: A tanúsítvány feltöltése az Azure AD B2C-vel szabályzatbejegyzések
Miután beállította `clientCertEnabled` való *igaz*, ügyféltanúsítványt igényel a kommunikációt a RESTful API-val. Szerezze be, és töltse fel az ügyféltanúsítvány tárolása az Azure AD B2C-bérlő, tegye a következőket: 
1. Válassza ki az Azure AD B2C-bérlőben **B2C-beállítások** > **identitás-kezelőfelületi keretrendszer**.

2. Elérhető a bérlőben a kulcsok megtekintéséhez jelölje ki **Szabályzatbejegyzések**.

3. Válassza a **Hozzáadás** lehetőséget.  
    A **hozzon létre egy kulcsot** ablak nyílik meg.

4. Az a **beállítások** jelölje ki **feltöltése**.

5. Az a **neve** mezőbe írja be **B2cRestClientCertificate**.  
    Az előtag *B2C_1A_* a rendszer automatikusan hozzáadja.

6. Az a **fájlfeltöltés** jelölje ki a tanúsítvány .pfx fájlját és a egy titkos kulcs.

7. Az a **jelszó** mezőbe írja be a tanúsítvány jelszavát.

    ![Töltse fel a házirendjének kulcsa](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Kattintson a **Létrehozás** gombra.

8. Tekintse meg a kulcsokat, a bérlőben, és győződjön meg arról, hogy létrehozott a `B2C_1A_B2cRestClientCertificate` kulcs, jelölje be **Szabályzatbejegyzések**.

## <a name="step-3-change-the-technical-profile"></a>3. lépés: A technikai profil módosítása
Ügyféltanúsítvány-alapú hitelesítés támogatása az egyéni házirendek, a technikai profil módosítása az alábbiak szerint:

1. A munkakönyvtárban, nyissa meg a *TrustFrameworkExtensions.xml* bővítmény a házirend-fájl.

2. Keresse meg a `<TechnicalProfile>` tartalmazó csomópont `Id="REST-API-SignUp"`.

3. Keresse meg a `<Metadata>` elemet.

4. Módosítsa a *AuthenticationType* való *ClientCertificate*, az alábbiak szerint:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. A záró után azonnal `<Metadata>` elemben adja hozzá a következő XML-részletet: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    Miután hozzáadta a kódrészletet, a technikai profil a következő XML-kódhoz hasonlóan kell kinéznie:

    ![ClientCertificate hitelesítési XML-elem beállítása](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>4. lépés: A szabályzat feltöltése a bérlőhöz

1. Az a [az Azure portal](https://portal.azure.com), váltson át a [az Azure AD B2C-bérlője kontextusában](active-directory-b2c-navigate-to-b2c-context.md), majd válassza ki **Azure AD B2C-vel**.

2. Válassza ki **identitás-kezelőfelületi keretrendszer**.

3. Válassza ki **összes szabályzat**.

4. Válassza ki **szabályzat feltöltése**.

5. Válassza ki a **szabályzat felülírása, ha létezik** jelölőnégyzetet.

6. Töltse fel a *TrustFrameworkExtensions.xml* fájlt, és ezután győződjön meg arról, hogy érvényesítési továbbítja.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>5. lépés: Az egyéni házirend tesztelése a Futtatás most
1. Nyissa meg **Azure AD B2C-beállítások**, majd válassza ki **identitás-kezelőfelületi keretrendszer**.

    >[!NOTE]
    >Futtatás most kell előzetesen regisztrálva, a bérlő legalább egy alkalmazás szükséges. Megtudhatja, hogyan regisztrálja az alkalmazást, tekintse meg az Azure AD B2C [Ismerkedés](active-directory-b2c-get-started.md) cikk vagy a [alkalmazásregisztráció](active-directory-b2c-app-registration.md) cikk.

2. Nyissa meg **B2C_1A_signup_signin**, a függő entitásonkénti (RP) egyéni-szabályzattal, feltöltött, és válassza ki **Futtatás most**.

3. Írja be a folyamat teszteléséhez **teszt** a a **Utónév** mezőbe.  
    Az Azure AD B2C hibaüzenetet jelenít meg az ablak tetején.    

    ![Az identitás API tesztelése](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. Az a **Utónév** mezőbe írjon be egy nevet (nem a "Test").  
    Az Azure AD B2C a felhasználó regisztrál, és ezután elküldi a hűségprogramok használatán keresztül számnak az alkalmazáshoz. Megjegyzés: Ebben a példában JWT száma:

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
   >Ha a hibaüzenet kap *a név nem érvényes, adjon meg egy érvényes nevet*, az azt jelenti, hogy az Azure AD B2C-vel sikeresen neve a RESTful szolgáltatás, bár ez jelenik meg az ügyféltanúsítványt. A következő lépés, hogy a tanúsítvány érvényesítéséhez.

## <a name="step-6-add-certificate-validation"></a>6. lépés: Adja hozzá a tanúsítványok ellenőrzését
Az ügyféltanúsítvány, amely az Azure AD B2C-t küld a RESTful szolgáltatás nem lesznek az Azure Web Apps platformon, kivéve ellenőrizze, hogy létezik-e a tanúsítvány érvényesítése. A tanúsítvány érvényesítése feladata a webalkalmazás. 

Ebben a szakaszban adjon hozzá mintául szolgáló ASP.NET-kód, amely ellenőrzi a hitelesítési tanúsítvány tulajdonságai.

> [!NOTE]
>Az ügyféltanúsítvány hitelesítéséhez az Azure App Service konfigurálásával kapcsolatos további információkért lásd: [TLS konfigurálása kölcsönös hitelesítést webalkalmazásokhoz](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Alkalmazásbeállítások hozzáadása a projekthez web.config fájlban
A korábban létrehozott Visual Studio-projektben vegye fel a következő alkalmazás beállításait, hogy a *web.config* után a fájl a `appSettings` elem:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Cserélje le a tanúsítványt **tulajdonosnévvel**, **kiállító neve**, és **tanúsítvány-ujjlenyomat** értékeket a tanúsítvány értékeire.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 a IsValidClientCertificate függvény hozzáadása
Nyissa meg a *Controllers\IdentityController.cs* fájlt, és hozzáadhatja a `Identity` vezérlő osztályhoz a következő függvényt: 

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

A fenti mintakódban fogadunk a tanúsítvány érvényes, csak akkor, ha az alábbi feltételek mindegyike teljesül:
* A tanúsítvány nem járt le, és -e aktív az aktuális idő a kiszolgálón.
* A `Subject` a tanúsítvány neve megegyezik a `ClientCertificate:Subject` alkalmazás beállítás értékét.
* A `Issuer` a tanúsítvány neve megegyezik a `ClientCertificate:Issuer` alkalmazás beállítás értékét.
* A `thumbprint` a tanúsítvány megegyezik a `ClientCertificate:Thumbprint` alkalmazás beállítás értékét.

>[!IMPORTANT]
>A szolgáltatás érzékenysége függően szükség lehet adjon hozzá további ellenőrzéseket. Szüksége lehet például annak megállapítására, hogy a tanúsítvány kapcsolódik, egy megbízható legfelső szintű hitelesítésszolgáltatóval, kiállító szervezet nevének érvényesítése és így tovább.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 IsValidClientCertificate függvény hívása
Nyissa meg a *Controllers\IdentityController.cs* fájlt, és ezután már az elején a `SignUp()` működik, adja hozzá a következő kódrészletet: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

Miután hozzáadta a kódrészletben a `Identity` vezérlő a következő kódhoz hasonlóan kell kinéznie:

![Tanúsítvány érvényesítési kód hozzáadása](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>7. lépés: A projekt közzététele az Azure-ba, és a tesztelés közben
1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **Contoso.AADB2C.API** projektre, és válassza ki **közzététel**.

2. Ismételje meg a "6. lépés", és újra teszteléséhez az egyéni házirend és a tanúsítványok ellenőrzését. Próbálja meg futtatni a szabályzat, és győződjön meg arról, hogy minden működik az érvényesítés hozzáadása után.

3. Az a *web.config* fájl, módosítsa az értéket a `ClientCertificate:Subject` való **érvénytelen**. Futtassa újból a szabályzatot, és megjelenik egy hibaüzenet.

4. Módosítsa az értéket a biztonsági **érvényes**, és győződjön meg arról, hogy a házirend segítségével meghívhatja a REST API-t.

Ha ez a lépés hibaelhárítása van szüksége, tekintse meg [naplók gyűjtése az Application Insights segítségével](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Nem kötelező) A teljes házirend fájlok és a kód letöltése
* Miután elvégezte a [egyéni szabályzatok – első lépések](active-directory-b2c-get-started-custom.md) forgatókönyv, azt javasoljuk, hogy a forgatókönyv a saját egyéni házirend-fájlok használatával hozhat létre. Referenciaként adtunk meg [házirendfájljait minta](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Letöltheti a teljes kódját [mintát a Visual Studio-megoldás referenciaként](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
