---
title: SAML-alapú egyszeri bejelentkezés konfigurálása Microsoft Graph API-k használatával
titleSuffix: Azure Active Directory
description: SAML-alapú egyszeri bejelentkezést kell beállítania egy alkalmazás több példányához? Megtudhatja, hogyan takaríthat meg időt a Microsoft Graph API-k használatával az SAML-alapú egyszeri bejelentkezés konfigurációjának automatizálásához.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: luleon
ms.openlocfilehash: 50ee9e3c22c885931e2586f65ba2fa3353fccfeb
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85355845"
---
# <a name="automate-saml-based-sso-app-configuration-with-microsoft-graph-api"></a>SAML-alapú SSO-alkalmazások konfigurációjának automatizálása Microsoft Graph API-val

Ebből a cikkből megtudhatja, hogyan hozhat létre és konfigurálhat egy alkalmazást az Azure Active Directory (Azure AD) gyűjteményből. Ez a cikk az AWS-t használja példaként, de a jelen cikkben ismertetett lépéseket használhatja bármely SAML-alapú alkalmazáshoz az Azure AD-katalógusban.

**A Microsoft Graph API-k használatának lépései az SAML-alapú egyszeri bejelentkezés konfigurációjának automatizálásához**

| Lépés  | Részletek  |
|---------|---------|
| [1. a Gallery-alkalmazás létrehozása](#step-1-create-the-gallery-application) | Bejelentkezés az API-ügyfélbe <br> A gyűjtemény alkalmazás beolvasása <br> A Gallery-alkalmazás létrehozása|
| [2. az egyszeri bejelentkezés konfigurálása](#step-2-configure-single-sign-on) | Az alkalmazás-objektumazonosító és a szolgáltatásnév-objektum AZONOSÍTÓjának lekérése <br> Egyszeri bejelentkezési mód beállítása <br> Alapszintű SAML URL-címek (például azonosító, válasz URL-cím, bejelentkezési URL-cím) beállítása <br> Alkalmazás-Szerepkörök hozzáadása (nem kötelező)|
| [3. jogcím-hozzárendelés konfigurálása](#step-3-configure-claims-mapping) | Jogcím-hozzárendelési szabályzat létrehozása <br> Jogcím-hozzárendelési szabályzat hozzárendelése az egyszerű szolgáltatáshoz|
| [4. aláíró tanúsítvány konfigurálása](#step-4-configure-signing-certificate) | Tanúsítvány létrehozása <BR> Egyéni aláíró kulcs hozzáadása <br> Az egyéni aláíró kulcs aktiválása|
| [5. felhasználók kiosztása](#step-5-assign-users) | Felhasználók és csoportok hozzárendelése az alkalmazáshoz
| [6. az alkalmazás oldalának konfigurálása](#step-6-configure-the-application-side)| Azure AD SAML-metaadatok beszerzése

**A dokumentációban használt összes API listája**

Győződjön meg arról, hogy rendelkezik a megfelelő engedélyekkel a következő API-k meghívásához.

|Erőforrás típusa |Metódus |
|---------|---------|
|[applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta)|[ApplicationTemplate listázása](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) <br>[ApplicationTemplate példányának példányai](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)|
|[servicePrincipals](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)|[ServicePrincipal frissítése](https://docs.microsoft.com/graph/api/serviceprincipal-update?view=graph-rest-1.0&tabs=http) <br> [AppRoleAssignments létrehozása](https://docs.microsoft.com/graph/api/serviceprincipal-post-approleassignments?view=graph-rest-1.0&tabs=http) <br> [ClaimsMappingPolicies-hozzárendelés](https://docs.microsoft.com/graph/api/serviceprincipal-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)|
|[alkalmazások](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)|[Az alkalmazás frissítése](https://docs.microsoft.com/graph/api/application-update?view=graph-rest-1.0&tabs=http)|
|[claimsMappingPolicy](https://docs.microsoft.com/graph/api/resources/claimsmappingpolicy?view=graph-rest-beta)| [ClaimsMappingPolicy létrehozása](https://docs.microsoft.com/graph/api/claimsmappingpolicy-post-claimsmappingpolicies?view=graph-rest-beta&tabs=http)

>[!NOTE]
>Az ebben a cikkben látható válasz-objektumok az olvashatóság érdekében lerövidítve lehetnek. A rendszer az összes tulajdonságot visszaadja egy tényleges hívásból.

## <a name="step-1-create-the-gallery-application"></a>1. lépés: a gyűjtemény alkalmazás létrehozása

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Jelentkezzen be Microsoft Graph Explorer (ajánlott), Poster vagy bármely más API-ügyfél használatával

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
2. Válassza **a bejelentkezés a Microsofttal** lehetőséget, és jelentkezzen be az Azure ad globális rendszergazdai vagy alkalmazás-rendszergazdai hitelesítő adataival.
3. A sikeres bejelentkezés után a bal oldali ablaktáblán megjelenik a felhasználói fiók adatai.

### <a name="retrieve-the-gallery-application-template-identifier"></a>A katalógus alkalmazás sablon-azonosítójának beolvasása

Az Azure AD-alkalmazás-katalógusban található alkalmazások mindegyike rendelkezik egy [alkalmazás-sablonnal](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) , amely leírja az alkalmazás metaadatait. Ennek a sablonnak a használatával létrehozhatja az alkalmazás és az egyszerű szolgáltatásnév egy példányát a bérlőben a felügyelethez.

#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>A Gallery-alkalmazás létrehozása

Az utolsó lépésben az alkalmazáshoz beolvasott sablon AZONOSÍTÓjának használatával [hozzon létre egy példányt](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) az alkalmazás és a szolgáltatásnév számára a bérlőben.

> [!NOTE] 
> A applicationTemplate API használatával nem katalógusbeli [alkalmazásokat](add-non-gallery-app.md)hozhat létre. ApplicationTemplateId használata `8adf8e6e-67b2-4cf2-a259-e3dc5476c621` .
#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/8b1025e4-1dd2-430b-a150-2ef79cd700f5/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>Válasz


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-configure-single-sign-on"></a>2. lépés: az egyszeri bejelentkezés konfigurálása

### <a name="retrieve-app-object-id-and-service-principal-object-id"></a>Az alkalmazás-objektumazonosító és a szolgáltatásnév-objektum AZONOSÍTÓjának lekérése

Az előző hívás válaszával lekérheti és mentheti az alkalmazás-objektum AZONOSÍTÓját és a szolgáltatás egyszerű objektumának AZONOSÍTÓját.

```
"application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63"
}
"servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e"
}
```
### <a name="set-single-sign-on-mode"></a>Egyszeri bejelentkezési mód beállítása

Ebben a példában `saml` a [servicePrincipal erőforrástípus](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-1.0)egyszeri bejelentkezési módba állítja be a szolgáltatást. A konfigurálható egyéb SAML SSO-tulajdonságok a következők: `notificationEmailAddresses` , `loginUrl` és`samlSingleSignOnSettings.relayState`

#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: servicePrincipal/json

{
    "preferredSingleSignOnMode": "saml"
}
```

#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```

### <a name="set-basic-saml-urls-such-as-identifier-reply-url-sign-on-url"></a>Alapszintű SAML URL-címek (például azonosító, válasz URL-cím, bejelentkezési URL-cím) beállítása

Az AWS azonosító és válasz URL-címeinek beállítása az Application objektumban.

#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/applications/cbc071a6-0fa5-4859-8g55-e983ef63df63
Content-type: applications/json

{
    "web": {
        "redirectUris": [
            "https://signin.aws.amazon.com/saml"
        ] 
    },
    "identifierUris": [
        "https://signin.aws.amazon.com/saml"
    ]    
}
```
#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
} -->

```http
HTTP/1.1 204
```
### <a name="add-app-roles-optional"></a>Alkalmazás-Szerepkörök hozzáadása (nem kötelező)

Ha az alkalmazásnak szüksége van a szerepkör adataira a jogkivonatban, adja hozzá a szerepkörök definícióját az Application objektumban. Az AWS esetében [engedélyezheti a felhasználók üzembe](../app-provisioning/application-provisioning-configure-api.md) helyezését az AWS-fiók összes szerepkörének beolvasásához. 

További információért olvassa el az [SAML-jogkivonatban kiadott szerepkör-jogcím konfigurálása című részt](../develop/active-directory-enterprise-app-role-management.md) .

> [!NOTE] 
> Az alkalmazás szerepköreinek hozzáadásakor ne módosítsa az alapértelmezett alkalmazás-szerepköröket msiam_access. 

#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/serviceprincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e
Content-type: serviceprincipals/json

{
"appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "454dc4c2-8176-498e-99df-8c4efcde41ef",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-admin,arn:aws:iam::212743507312:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Finance,WAAD",
            "displayName": "Finance,WAAD",
            "id": "8642d5fa-18a3-4245-ab8c-a96000c1a217",
            "isEnabled": true,
            "value": "arn:aws:iam::212743507312:role/accountname-aws-finance,arn:aws:iam::212743507312:saml-provider/WAAD"
        }
    ]

}
```

#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-3-configure-claims-mapping"></a>3. lépés: a jogcímek leképezésének konfigurálása

### <a name="create-claims-mapping-policy"></a>Jogcím-hozzárendelési szabályzat létrehozása

Az alapszintű jogcímek mellett konfigurálja a következő jogcímeket az Azure AD-ben az SAML-jogkivonatban való kibocsátáshoz:

| Jogcím neve | Forrás  |
|---------|---------|
| `https://aws.amazon.com/SAML/Attributes/Role` | assignedroles| 
| `https://aws.amazon.com/SAML/Attributes/RoleSessionName` | userPrincipalName |
| `https://aws.amazon.com/SAML/Attributes/SessionDuration` | "900" |
| roles | assignedroles |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` | userPrincipalName |

További információ: [a jogkivonatban kibocsátott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies
Content-type: claimsMappingPolicies/json

{
    "definition":[
            "{\"ClaimsMappingPolicy\": {
                \"Version\": 1,
                \"IncludeBasicClaimSet\": \"true\",
                \"ClaimsSchema\": [
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/Role\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"900\",
                        \"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"assignedroles\",
                        \"SamlClaimType\": \"appRoles\"
                    },
                    {
                        \"Source\": \"user\",
                        \"ID\": \"userprincipalname\",
                        \"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"
                    }
                ]
            }
        }"

    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.claimsMappingPolicies",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: claimsMappingPolicies/json

{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#policies/claimsMappingPolicies/$entity",
    "id": "6b33aa8e-51f3-41a6-a0fd-d660d276197a",
    "definition": [
        "{\"ClaimsMappingPolicy\": {\"Version\": 1,\"IncludeBasicClaimSet\": \"true\",\"ClaimsSchema\": [{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"https://aws.amazon.com/SAML/Attributes/Role\"\r\n                    },{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/RoleSessionName\"},{\"Source\": \"user\",\"ID\": \"900\",\"SamlClaimType\": \"https://aws.amazon.com/SAML/Attributes/SessionDuration\"},{\"Source\": \"user\",\"ID\": \"assignedroles\",\"SamlClaimType\":\"appRoles\"},{\"Source\": \"user\",\"ID\": \"userprincipalname\",\"SamlClaimType\": \"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\"}]}}"
    ],
    "displayName": "AWS Claims policy",
    "isOrganizationDefault": false
}
```

### <a name="assign-claims-mapping-policy-to-service-principal"></a>Jogcím-hozzárendelési szabályzat hozzárendelése az egyszerű szolgáltatáshoz

#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/claimsMappingPolicies/$ref

Content-type: claimsMappingPolicies/json

{
  "@odata.id":"https://graph.microsoft.com/v1.0/policies/claimsMappingPolicies/6b33aa8e-51f3-41a6-a0fd-d660d276197a"
}
```

#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

## <a name="step-4-configure-signing-certificate"></a>4. lépés: aláíró tanúsítvány konfigurálása

A [applicationTemplate](https://docs.microsoft.com/graph/api/resources/applicationtemplate?view=graph-rest-beta) API használata alapértelmezés szerint nem hoz létre aláíró tanúsítványt. Hozza létre az egyéni aláíró tanúsítványát, és rendelje hozzá az alkalmazáshoz. 

### <a name="create-a-custom-signing-certificate"></a>Egyéni aláíró tanúsítvány létrehozása

A teszteléshez az alábbi PowerShell-paranccsal kérhet le önaláírt tanúsítványt. A vállalat legjobb biztonsági gyakorlatával létrehozhat egy aláíró tanúsítványt az éles környezethez.

```powershell
Param(
    [Parameter(Mandatory=$true)]
    [string]$fqdn,
    [Parameter(Mandatory=$true)]
    [string]$pwd,
    [Parameter(Mandatory=$true)]
    [string]$location
) 

if (!$PSBoundParameters.ContainsKey('location'))
{
    $location = "."
} 

$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx" 

Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile
```

### <a name="add-a-custom-signing-key"></a>Egyéni aláíró kulcs hozzáadása

Adja hozzá a következő információkat az egyszerű szolgáltatáshoz:

* Titkos kulcs
* Jelszó
* Nyilvános kulcs 

Bontsa ki a titkosítatlan titkos kulcsot a PFX-fájlból. Ha többet szeretne megtudni a tulajdonságokról, olvassa el a [következőt: a hitelesítő adatok erőforrástípus](https://docs.microsoft.com/graph/api/resources/keycredential?view=graph-rest-1.0).

Győződjön meg arról, hogy a "Sign" kifejezéshez használt keyId-azonosító megegyezik a passwordCredential keyId.

A-t a `customkeyIdentifier` tanúsítvány ujjlenyomatának lekérésével hozhatja meg.

```csharp
  public string GetSha256FromThumbprint(string thumbprint)
  {
      var message = Encoding.ASCII.GetBytes(thumbprint);
      SHA256Managed hashString = new SHA256Managed();
      return Convert.ToBase64String(hashString.ComputeHash(message));
  }
```

#### <a name="request"></a>Kérés

> [!NOTE]
> A hitelesítő adatok tulajdonságban szereplő "Key" érték rövidebb az olvashatóság érdekében. Az érték a 64 Base kódolású. A titkos kulcshoz a tulajdonság a `usage` "Sign". A nyilvános kulcs esetében a tulajdonság `usage` "ellenőrzés".

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=awsAPI"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=awsAPI"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "61891f4ee44d"
        }
    ]
}
```

#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```

### <a name="activate-the-custom-signing-key"></a>Az egyéni aláíró kulcs aktiválása

A tulajdonságot annak a `preferredTokenSigningKeyThumbprint` tanúsítványnak az ujjlenyomatára kell beállítania, amelyet az Azure ad az SAML-válasz aláírásához használni kíván. 

#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json

{
    "preferredTokenSigningKeyThumbprint": "AC09FEF18DDE6983EE2A164FBA3C4DD7518BD787"
}
```

#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 204
```
## <a name="step-5-assign-users"></a>5. lépés: felhasználók kiosztása

### <a name="assign-users-and-groups-to-the-application"></a>Felhasználók és csoportok hozzárendelése az alkalmazáshoz

Rendelje hozzá a következő felhasználót az egyszerű szolgáltatáshoz, és rendelje hozzá a AWS_Role1. 

| Name (Név)  | ID  |
|---------|---------|
| Felhasználói azonosító (principalId) | 6cad4079-4e79-4a3f-9efb-ea30a14bdb26 |
| Típus (principalType) | Felhasználó |
| Alkalmazás-szerepkör azonosítója (appRoleId) | 454dc4c2-8176-498e-99df-8c4efcde41ef |
| servicePrincipalID (resourceId) | 515f62cb-d18a-4dca-bec3-bb0bf31deeea |

#### <a name="request"></a>Kérés

<!-- {
  "blockType": "request",
  "name": "servicePrincipals"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e/appRoleAssignments

Content-type: appRoleAssignments/json

{
  "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
  "principalType": "User",
  "appRoleId":"454dc4c2-8176-498e-99df-8c4efcde41ef",
  "resourceId":"515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```
#### <a name="response"></a>Válasz

<!-- {
  "blockType": "response",
  "truncated": true,
} -->
```http
HTTP/1.1 201 
Content-type: appRoleAssignments/json

{
    "id": "rq7hyzl4yECaNZleMrTpDV-OCe5TEl5Ao_o76XMrRFU",
    "creationTimestamp": "2020-04-23T17:38:13.2508567Z",
    "appRoleId": "454dc4c2-8176-498e-99df-8c4efcde41ef",
    "principalDisplayName": "User 1",
    "principalId": "6cad4079-4e79-4a3f-9efb-ea30a14bdb26",
    "principalType": "User",
    "resourceDisplayName": "AWS API Created",
    "resourceId": "515f62cb-d18a-4dca-bec3-bb0bf31deeea"
}
```

További információ: [appRoleAssignment](https://docs.microsoft.com/graph/api/resources/approleassignment?view=graph-rest-1.0) erőforrástípus.

## <a name="step-6-configure-the-application-side"></a>6. lépés: az alkalmazás oldalának konfigurálása

### <a name="get-azure-ad-saml-metadata"></a>Azure AD SAML-metaadatok beszerzése

A következő URL-cím használatával lekérheti az Azure AD SAML-metaadatokat az adott konfigurált alkalmazáshoz. A metaadatok többek között olyan információkat tartalmaznak, mint például az aláíró tanúsítvány, az Azure AD entityID és az Azure AD SingleSignOnService.

https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id}

## <a name="next-steps"></a>További lépések
- [A felhasználók üzembe helyezésének konfigurálása Microsoft Graph API-k használatával](../app-provisioning/application-provisioning-configure-api.md)
- [Alkalmazások migrálása az Azure AD-be a AD FS alkalmazás-tevékenység jelentés használatával](migrate-adfs-application-activity.md)
