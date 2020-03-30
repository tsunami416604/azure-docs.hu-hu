---
title: SAML tokentitkosítás az Azure Active Directoryban
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directory SAML token titkosítását.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0082d841faf22745e609d38444f4a97553b3c867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365866"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Útmutató: Az Azure AD SAML tokentitkosítás konfigurálása

> [!NOTE]
> A tokentitkosítás egy Azure Active Directory (Azure AD) prémium funkció. Ha többet szeretne megtudni az Azure AD-kiadásokról, -funkciókról és -díjszabásról, olvassa el az [Azure AD díjszabását.](https://azure.microsoft.com/pricing/details/active-directory/)

Az SAML token titkosítása lehetővé teszi a titkosított SAML-állítások használatát egy olyan alkalmazással, amely támogatja azt. Ha egy alkalmazás konfigurálása, az Azure AD titkosítja az adott alkalmazás saml-állításokat az Azure AD-ben tárolt tanúsítványból beszerzett nyilvános kulcs használatával. Az alkalmazásnak a megfelelő személyes kulcsot kell használnia a jogkivonat visszafejtéséhez, mielőtt a bejelentkezett felhasználó hitelesítésének bizonyítékaként használható lenne.

Az Azure AD és az alkalmazás közötti SAML-állítások titkosítása további biztosítékot nyújt arra nézve, hogy a jogkivonat tartalma nem érhető el, és a személyes vagy vállalati adatok feltörése.

Még jogkivonat-titkosítás nélkül is, az Azure AD SAML-tokenek soha nem adják át a hálózaton a tiszta. Az Azure AD megköveteli a token kérés/válasz cseréje titkosított HTTPS/TLS csatornákon keresztül, így az IDP, a böngésző és az alkalmazás közötti kommunikáció titkosított kapcsolatokon keresztül történik. Vegye figyelembe a jogkivonat-titkosítás értékét a helyzethez képest a további tanúsítványok kezelésének többletterhelésével.   

A token titkosításkonfigurálásához fel kell töltenie egy X.509 tanúsítványfájlt, amely tartalmazza a nyilvános kulcsot az Azure AD alkalmazásobjektumba, amely az alkalmazást képviseli. Az X.509 tanúsítvány beszerzéséhez letöltheti azt magából az alkalmazásból, vagy beszerezheti az alkalmazás szállítójától olyan esetekben, amikor az alkalmazás szállítója titkosítási kulcsokat biztosít, vagy olyan esetekben, amikor az alkalmazás elvárja, hogy személyes kulcsot adjon meg, kriptográfiai eszközök kel, az alkalmazás kulcstárolójára feltöltött személyes kulcsrész és az Azure AD-be feltöltött megfelelő nyilvános kulcsú tanúsítvány.

Az Azure AD a AES-256 titkosításához az SAML-állítási adatok titkosításához.

## <a name="configure-saml-token-encryption"></a>SAML tokentitkosítás konfigurálása

Az SAML tokentitkosítás konfigurálásához hajtsa végre az alábbi lépéseket:

1. Szerezzen be egy nyilvános kulcstanúsítványt, amely megfelel az alkalmazásban konfigurált személyes kulcsnak.

    Hozzon létre egy aszimmetrikus kulcspárt a titkosításhoz. Vagy ha az alkalmazás egy nyilvános kulcsot biztosít a titkosításhoz, kövesse az alkalmazás utasításait az X.509 tanúsítvány letöltéséhez.

    A nyilvános kulcsot X.509 tanúsítványfájlban kell tárolni .cer formátumban.

    Ha az alkalmazás egy kulcsot használ, amelyet a példányhoz hoz létre, kövesse az alkalmazás által az alkalmazás által az Azure AD-bérlőből származó jogkivonatok visszafejtésére használt titkos kulcs telepítéséhez megadott utasításokat.

1. Adja hozzá a tanúsítványt az alkalmazás konfigurációjához az Azure AD-ben.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Tokentitkosítás konfigurálása az Azure Portalon

A nyilvános tanúsítvány az Azure Portalon belül az alkalmazás konfigurációját hozzáadhatja.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)

1. Nyissa meg az **Azure Active Directory > Enterprise alkalmazások** panelt, majd válassza ki azt az alkalmazást, amelyhez jogkivonat-titkosítást kíván konfigurálni.

1. Az alkalmazás lapján válassza a **Token titkosítás**lehetőséget.

    ![Tokentitkosítási lehetőség az Azure Portalon](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > A **Token titkosítási** lehetőség csak az **azure-beli** portál vállalati alkalmazáspaneljéről beállított SAML-alkalmazások esetében érhető el, akár az alkalmazástárból, akár egy nem galéria alkalmazásból. Más alkalmazások esetében ez a menüpont le van tiltva. Az Azure Portalon az **alkalmazásregisztrációk** szolgáltatásán keresztül regisztrált alkalmazások esetében konfigurálhatja az SAML-tokenek titkosítását az alkalmazásjegyzék használatával, a Microsoft Graph-on keresztül vagy a PowerShellen keresztül.

1. A **Token titkosítás** lapján válassza a **Tanúsítvány importálása** lehetőséget a nyilvános X.509 tanúsítványt tartalmazó .cer fájl importálásához.

    ![Az X.509 tanúsítványt tartalmazó .cer fájl importálása](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Miután importálta a tanúsítványt, és a személyes kulcs az alkalmazás oldalán való használatra van konfigurálva, aktiválja a titkosítást az ujjlenyomat-állapot melletti **...** kiválasztásával, majd válassza a **Token titkosítás aktiválása lehetőséget** a legördülő menü beállításai között.

1. Válassza az **Igen** lehetőséget a tokentitkosítási tanúsítvány aktiválásának megerősítéséhez.

1. Ellenőrizze, hogy az alkalmazáshoz kibocsátott SAML-állítások titkosítva vannak-e.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Tokentitkosítás inaktiválása az Azure Portalon

1. Az Azure Portalon nyissa meg az **Azure Active Directory > Enterprise-alkalmazások,** majd válassza ki az alkalmazást, amelyen az SAML token titkosítás engedélyezve van.

1. Az alkalmazás lapján válassza a **Token titkosítás**lehetőséget, keresse meg a tanúsítványt, majd válassza a **...** lehetőséget a legördülő menü megjelenítéséhez.

1. Válassza **a Token titkosítás inaktiválása**lehetőséget.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>SAML tokentitkosítás konfigurálása Graph API, PowerShell vagy alkalmazásjegyzék használatával

A titkosítási tanúsítványok az azure-beli AD-ben az alkalmazásobjektumban tárolódnak egy `encrypt` használati címkével. Több titkosítási tanúsítványt is konfigurálhat, és a tokenek titkosításához `tokenEncryptionKeyID` aktívtanúsítványt az attribútum azonosítja.

A tokentitkosítás Microsoft Graph API vagy PowerShell használatával történő konfigurálásához szüksége lesz az alkalmazás objektumazonosítójára. Ezt az értéket programozott módon, vagy az alkalmazás **Tulajdonságok** lapján az Azure Portalon, és az **objektumazonosító** értékét.

Ha graph, PowerShell vagy az alkalmazásjegyzék használatával keyCredential konfigurál, létre kell hoznia egy GUID-ot a keyId használatához.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Tokentitkosítás konfigurálása a Microsoft Graph segítségével

1. Frissítse az alkalmazást `keyCredentials` egy X.509-es tanúsítvánnyal a titkosításhoz. A következő példa bemutatja, hogyan kell ezt megtenni.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Azonosítsa a jogkivonatok titkosításához aktív titkosítási tanúsítványt. A következő példa bemutatja, hogyan kell ezt megtenni.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Tokentitkosítás konfigurálása a PowerShell használatával

1. Használja a legújabb Azure AD PowerShell-modult a bérlőhöz való csatlakozáshoz.

1. Állítsa be a token titkosítási beállításokat a **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** paranccsal.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Olvassa el a tokentitkosítási beállításokat a következő parancsokkal.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Tokentitkosítás konfigurálása az alkalmazásjegyzék használatával

1. Az Azure Portalon nyissa meg az **Azure Active Directory > alkalmazásregisztrációk című lapban.**

1. Válassza az **Összes alkalmazás** lehetőséget a legördülő menüből az összes alkalmazás megjelenítéséhez, majd válassza ki a konfigurálni kívánt vállalati alkalmazást.

1. Az alkalmazás lapján válassza a **Jegyzékfájl** lehetőséget az [alkalmazásjegyzék](../develop/reference-app-manifest.md)szerkesztéséhez.

1. Állítsa be az `tokenEncryptionKeyId` attribútum értékét.

    A következő példa egy két titkosítási tanúsítvánnyal konfigurált alkalmazásjegyzéket mutat be, és a másodikat aktívként jelöli meg a tokenEnryptionKeyId használatával.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogy az Azure AD hogyan használja az SAML protokollt](../develop/active-directory-saml-protocol-reference.md)
* Ismerje meg az [SAML-tokenek](../develop/reference-saml-tokens.md) formátumát, biztonsági jellemzőit és tartalmát az Azure AD-ben