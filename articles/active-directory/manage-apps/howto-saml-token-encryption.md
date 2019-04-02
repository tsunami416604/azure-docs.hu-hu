---
title: SAML-jogkivonat titkosítása az Azure Active Directoryban
description: Ismerje meg, hogyan konfigurálhatja az Azure Active Directory SAML-jogkivonat titkosítása.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: celested
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7de6705ad38133b8321caabb7b0f4093284af503
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793067"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption-preview"></a>Útmutató: Az Azure AD SAML-jogkivonat titkosítása (előzetes verzió) konfigurálása

> [!NOTE]
> Jogkivonat titkosítása az Azure Active Directory (Azure AD) prémium szintű szolgáltatása. Az Azure AD kiadás, funkciói és díjszabása kapcsolatos további információkért lásd: [az Azure AD díjszabási](https://azure.microsoft.com/pricing/details/active-directory/).

SAML-jogkivonat titkosítása egy azt támogató alkalmazással titkosított SAML helyességi feltételek használatát teszi lehetővé. Az alkalmazás konfigurálásakor az Azure AD titkosítani fog a SAML helyességi feltételek, az adott alkalmazáshoz az Azure AD-ben tárolt tanúsítvány származó nyilvános kulccsal bocsát ki. Az alkalmazás a megfelelő titkos kulcs használatával kell a jogkivonat visszafejteni, mielőtt használható hitelesítési bizonyítékaként a bejelentkezett felhasználó.

A SAML helyességi feltételek az Azure AD közötti titkosítása és az alkalmazás maga biztosítja a további garanciát a tartalom a jogkivonat nem foghatók el, és a személyes vagy vállalati adatok biztonsága sérült.

Token titkosítás nélkül is az Azure AD SAML-jogkivonatok soha nem továbbítódnak a titkosítatlan a hálózaton. Az Azure AD szükséges jogkivonat kérés/válasz cseréje, hogy végre lehessen hajtani a titkosított HTTPS/TLS-csatornákon keresztül, hogy az Identitásszolgáltató, a böngésző és az alkalmazás közötti kommunikáció titkosított kapcsolaton keresztül kerül sor. Fontolja meg az adott helyzetben a további tanúsítványok kezelésével járó többletterhelést képest tokentitkosítási értékét.   

Tokentitkosítási konfigurálásához kell egy X.509 tanúsítvány fájl, amely tartalmazza a nyilvános kulcsot az Azure AD alkalmazást képviselő objektum, amely az alkalmazás feltöltése. X.509-tanúsítvány beszerzése, letöltheti az alkalmazás saját maga, vagy a get, az alkalmazás gyártójának az esetekben, ahol az alkalmazás gyártójától biztosítja a titkosítási kulcsokat, vagy azokban az esetekben, ahol az alkalmazás vár, hogy a titkos kulcsot, lehet titkosítás eszközök használatával létrehozott, titkoskulcs feltöltött kulcs az alkalmazás-áruház, és a megfelelő nyilvános kulcsú tanúsítvány feltöltése az Azure ad-hez.

Azure ad-ben az SAML helyességi feltétel adatok titkosítása AES-256 algoritmust használ.

## <a name="configure-saml-token-encryption"></a>SAML-jogkivonat titkosítása konfigurálása

SAML-jogkivonat titkosítása konfigurálásához kövesse az alábbi lépéseket:

1. Szerezze be a nyilvános kulcsú tanúsítvány, amely megfelel egy titkos kulcsot, amely az alkalmazásban van konfigurálva.

    Hozzon létre a titkosításhoz használandó aszimmetrikus kulcspárt. Vagy, ha az alkalmazás megadta a titkosításhoz használandó nyilvános kulcsot, kövesse az alkalmazás utasításokat követve töltse le az X.509-tanúsítványt.

    A nyilvános kulcsot egy X.509 tanúsítvány .cer formátumú fájl kell tárolni.

    Ha az alkalmazás a példány létrehozott kulcsot használ, kövesse az utasításokat, az alkalmazás telepítéséhez a titkos kulcsot, amelynek használatával az alkalmazás az Azure AD-bérlőből jogkivonatokat által biztosított.

1. A tanúsítvány hozzáadása az Azure ad-ben az alkalmazás konfigurációját.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>A token titkosítás beállítása az Azure Portalon

A nyilvános tanúsítvány adhat hozzá az alkalmazás konfigurálása az Azure Portalon.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. Nyissa meg a **Azure Active Directory > Vállalati alkalmazások** panelen, majd az alkalmazást, amelyet szeretne beállítani a jogkivonat titkosítása.

1. Az alkalmazás oldalán válassza **jogkivonat-titkosítási**.

    ![Jogkivonat-titkosítási beállítás az Azure Portalon](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > A **jogkivonat-titkosítási** lehetőség csak érhető el a beállított SAML-alkalmazások esetén a **vállalati alkalmazások** panel az Azure Portalon, vagy az alkalmazáskatalógusból vagy a Katalógusban nem szereplő alkalmazást. Más alkalmazások Ez a menüpont le van tiltva. Az alkalmazások keresztül regisztrált az **alkalmazásregisztrációk** élmény az Azure Portalon megadható titkosítás SAML-jogkivonatok használatával az application manifest, a Microsoft Graphon keresztül vagy a Powershellen keresztül.

1. Az a **jogkivonat-titkosítási** lapon jelölje be **tanúsítvány importálása** a .cer fájlt, amely tartalmazza a nyilvános X.509-tanúsítvány importálásához.

    ![A .cer fájlt, amely tartalmazza az X.509-tanúsítvány importálása](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Miután a tanúsítvány importálása, és a titkos kulcsot használja az alkalmazás oldalán van konfigurálva, aktiválja a titkosítási kiválasztásával a **...**  tovább állapota az ujjlenyomatot, és adja meg **tokentitkosítási aktiválása** a legördülő menüből az közül.

1. Válassza ki **Igen** megerősítéséhez, hogy a jogkivonat-titkosítási tanúsítvány aktiválását.

1. Győződjön meg arról, hogy a az alkalmazás számára kibocsátott SAML helyességi feltételek vannak titkosítva.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Az Azure Portalon tokentitkosítási inaktiválása

1. Az Azure Portalon lépjen a **Azure Active Directory > Vállalati alkalmazások**, és válassza ki az alkalmazást, amely rendelkezik a SAML-jogkivonat titkosítása engedélyezve van.

1. Az alkalmazás oldalán válassza **jogkivonat-titkosítási**, keresse meg a tanúsítványt, és válassza a **...**  megjelenítését a legördülő menüből.

1. Válassza ki **tokentitkosítási inaktiválása**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>SAML-jogkivonat titkosítása a Graph API-t, a PowerShell vagy az alkalmazásjegyzék konfigurálása

Titkosítási tanúsítványok az Azure AD-ben tárolódnak az alkalmazásobjektum egy `encrypt` használati címke. Beállíthatja, hogy több titkosítási tanúsítványok és a egy aktív jogkivonatok titkosításához által azonosított a `tokenEncryptionKeyID` attribútum.

Az alkalmazás Objektumazonosító a Microsoft Graph API vagy a PowerShell használatával tokentitkosítási konfigurálásához lesz szüksége. Ezt az értéket talál programozott módon, vagy lépjen az alkalmazás **tulajdonságok** oldal az Azure portal és megállapítva a **Objektumazonosító** értéket.

Amikor konfigurál egy keyCredential Graph-hoz a PowerShell használatával vagy az alkalmazásjegyzékben, egy GUID Azonosítót használja a kulcsazonosító kell létrehoznia.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>A Microsoft Graph használatával tokentitkosítási konfigurálása

1. Az alkalmazás frissítéséhez `keyCredentials` titkosítási X.509 tanúsítvánnyal. Az alábbi példa bemutatja, hogyan ehhez.

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

1. Azonosítsa a jogkivonatok titkosításához aktív titkosítási tanúsítványt. Az alábbi példa bemutatja, hogyan ehhez.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>PowerShell-lel tokentitkosítási konfigurálása

Ez a funkció hamarosan elérhető lesz. 

<!--
1. Use the latest Azure AD PowerShell module to connect to your tenant.

1. Set the token encryption settings using the **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** command.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials “<KeyCredentialsObject>”  -TokenEncryptionKeyId <keyID>
    ```

1. Read the token encryption settings using the following commands.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

-->

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Token titkosítás használatával az alkalmazásjegyzék konfigurálása

1. Az Azure Portalon lépjen a **Azure Active Directory > alkalmazásregisztrációk**.

1. Válassza ki **minden alkalmazás** az összes alkalmazás megjelenítése, és válassza ki a vállalati alkalmazást, amelyet konfigurálni szeretne a legördülő listából.

1. Az alkalmazás oldalán válassza **Manifest** szerkesztése a [alkalmazásjegyzék](../develop/reference-app-manifest.md).

1. Állítsa az értékét a `tokenEncryptionKeyId` attribútum.

    Az alábbi példában látható alkalmazásjegyzéket két titkosítási tanúsítvány konfigurálva, és a második kijelölt azzal, aktív a tokenEnryptionKeyId használatával.

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

* Ismerje meg, [hogyan Azure ad-ben az SAML protokollt használja.](../develop/active-directory-saml-protocol-reference.md)
* Ismerje meg a formátum, a biztonsági jellemzőkkel és a tartalmát [SAML-jogkivonatok az Azure ad-ben](../develop/reference-saml-tokens.md)