---
title: Azure Active Directory Identity Protection Microsoft Graph API
description: Ismerje meg, hogyan lehet lekérdezni Microsoft Graph kockázati észleléseket és a kapcsolódó információkat Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d88f841f76b367e83f0ae6b81e604e1b7f3e4b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950119"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>A Azure Active Directory Identity Protection és a Microsoft Graph első lépései

Microsoft Graph a Microsoft Unified API-végpontja és a [Azure Active Directory Identity Protection](./overview-identity-protection.md) API-k otthona. A kockázatos felhasználókról és a bejelentkezésekről három API található. Az első API ( **riskDetection**) lehetővé teszi Microsoft Graph lekérdezését a felhasználók és a bejelentkezéshez kapcsolódó kockázati észlelések, valamint az észleléssel kapcsolatos kapcsolódó információk listájának lekéréséhez. A második API, a **riskyUsers**lehetővé teszi, hogy lekérdezze Microsoft Graph a kockázatként észlelt felhasználói identitások védelmére vonatkozó információkat. A harmadik API, a **bejelentkezési**lehetővé teszi az Azure ad-bejelentkezésekre vonatkozó információk Microsoft Graph lekérdezését a kockázati állapottal, a részletekkel és a szinttel kapcsolatos konkrét tulajdonságokkal. 

Ebből a cikkből megtudhatja, hogyan csatlakozhat a Microsoft Graphhoz, és hogyan kérdezheti le ezeket az API-kat. A részletes bevezetést, a teljes dokumentációt és a Graph Explorerrel való hozzáférést a [Microsoft Graph webhelyén](https://graph.microsoft.io/) vagy az API-k konkrét dokumentációjában találja:

* [riskDetection API](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [signIn API](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Kapcsolódás a Microsoft graphhoz

Az Identity Protection-adatok elérésének négy lépése van Microsoft Graph:

- [Tartománynév lekérése](#retrieve-your-domain-name)
- [Új alkalmazás regisztrációjának létrehozása](#create-a-new-app-registration)
- [API-engedélyek konfigurálása](#configure-api-permissions)
- [Érvényes hitelesítő adat konfigurálása](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Tartománynév lekérése 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).  
1. Tallózással keresse meg **Azure Active Directory**  >  **Egyéni tartományneveket**. 
1. Jegyezze fel a `.onmicrosoft.com` tartományt, és ezt az információt egy későbbi lépésben kell megadnia.

### <a name="create-a-new-app-registration"></a>Új alkalmazás regisztrációjának létrehozása

1. A Azure Portal tallózással keresse meg **Azure Active Directory**  >  **Alkalmazásregisztrációk**.
1. Válassza az **új regisztráció**lehetőséget.
1. A **Létrehozás** oldalon hajtsa végre a következő lépéseket:
   1. A **név** szövegmezőbe írja be az alkalmazás nevét (például: Azure ad kockázati észlelési API).
   1. A **támogatott fiókok típusai**területen válassza ki az API-kat használó fiókok típusát.
   1. Válassza a **Regisztráció** lehetőséget.
1. Másolja ki az **alkalmazás azonosítóját**.

### <a name="configure-api-permissions"></a>API-engedélyek konfigurálása

1. A létrehozott **alkalmazásból** válassza az **API-engedélyek**lehetőséget.
1. A **konfigurált engedélyek** lapon, a felső eszköztáron kattintson az **engedély hozzáadása**lehetőségre.
1. Az **API-hozzáférés hozzáadása** lapon kattintson **az API kiválasztása**lehetőségre.
1. Az **API kiválasztása** lapon válassza a **Microsoft Graph**lehetőséget, majd kattintson a **kiválasztás**gombra.
1. Az **API-engedélyek kérése** oldalon: 
   1. Válassza ki az **alkalmazás engedélyeit**.
   1. Jelölje be a és a melletti jelölőnégyzeteket `IdentityRiskEvent.Read.All` `IdentityRiskyUser.Read.All` .
   1. Válassza az **engedélyek hozzáadása**lehetőséget.
1. Válassza **a rendszergazdai jóváhagyás megadása a tartományhoz** lehetőséget. 

### <a name="configure-a-valid-credential"></a>Érvényes hitelesítő adat konfigurálása

1. A létrehozott **alkalmazásból** válassza a **tanúsítványok & Secrets**elemet.
1. Az **ügyfél**titkos kulcsa területen válassza az **új ügyfél titka**lehetőséget.
   1. Adja meg az ügyfél titkos **leírását** , és állítsa be a lejárati időszakot a szervezeti szabályzatok szerint.
   1. Válassza a **Hozzáadás** elemet.

   > [!NOTE]
   > Ha elveszíti ezt a kulcsot, térjen vissza ehhez a szakaszhoz, és hozzon létre egy új kulcsot. Titkos kulcs megtartása: bárki, aki hozzáfér az adataihoz.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Hitelesítés Microsoft Graph és az identitás kockázati észlelése API lekérdezése

Ezen a ponton a következőket kell tennie:

- A bérlő tartományának neve
- Az alkalmazás (ügyfél) azonosítója 
- Az ügyfél titka vagy tanúsítványa 

A hitelesítéshez küldjön post-kérést a `https://login.microsoft.com` következő paraméterekkel a törzsben:

- grant_type: "**client_credentials**"
- erőforrás `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Ha ez sikeres, a kérelem egy hitelesítési jogkivonatot ad vissza.  
Az API meghívásához hozzon létre egy fejlécet a következő paraméterrel:

```
`Authorization`="<token_type> <access_token>"
```

A hitelesítés során megkeresheti a jogkivonat típusát és a hozzáférési jogkivonatot a visszaadott jogkivonatban.

Küldje el ezt a fejlécet kérelemként a következő API URL-címre: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

A válasz, ha a sikeres, az identitások kockázatának észlelése és a hozzájuk kapcsolódó adatok OData JSON formátumban való gyűjteménye, amely elemezhető és kezelhető, ahogy az illik.

### <a name="sample"></a>Sample

Az alábbi mintakód a PowerShell használatával történő hitelesítéshez és az API meghívásához.  
Csak adja hozzá az ügyfél-azonosítót, a titkos kulcsot és a bérlői tartományt.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>API-k lekérdezése

Ez a három API számos lehetőséget kínál a kockázatos felhasználók és a céges bejelentkezések adatainak lekérésére. Ezek az API-k és a kapcsolódó mintavételi kérelmek leggyakoribb használati esetei. Ezeket a lekérdezéseket a fenti mintakód vagy a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)használatával futtathatja.

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Az offline kockázatok észlelésének beolvasása (riskDetection API)

Az Identity Protection bejelentkezési kockázati házirendjeiben feltételt alkalmazhat, ha valós időben észleli a kockázatokat. Mi a helyzet a felderített észlelések offline állapotával? Annak megismeréséhez, hogy milyen észlelések történtek a kapcsolat nélküli üzemmódban, és így nem váltott ki a bejelentkezési kockázati házirendet, lekérdezheti a riskDetection API-t.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Az összes olyan felhasználó beolvasása, akik sikeresen átadtak a kockázatos bejelentkezési szabályzat által aktivált MFA-kihívásokat (riskyUsers API)

Annak megismeréséhez, hogy a szervezete milyen hatással van az Identity Protection kockázatkezelési házirendjeire, lekérdezheti az összes olyan felhasználót, aki sikeresen átadta a kockázatos bejelentkezési szabályzat által aktivált MFA-kihívásokat. Ezek az információk segíthetnek megérteni, hogy mely felhasználóknál lehet hamisan észlelni a személyazonosság védelmét, és hogy a legitim felhasználók milyen műveleteket végezhetnek el, ha a mesterséges intelligencia kockázatot jelent.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Következő lépések

Gratulálunk, most elkészítette az első hívását Microsoft Graph!  
Most már lekérdezheti az identitások kockázati észleléseit, és használhatja az adatok megjelenítését.

Ha többet szeretne megtudni a Microsoft Graphről, és hogyan hozhat létre alkalmazásokat a Graph API használatával, tekintse meg a [dokumentációt](/graph/overview) , és még sok más a [Microsoft Graph webhelyen](https://developer.microsoft.com/graph). 

A kapcsolódó információk a következő témakörben találhatók:

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [A Azure Active Directory Identity Protection által észlelt kockázati észlelések típusai](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [A Microsoft Graph áttekintése](https://developer.microsoft.com/graph/docs)
- [Azure AD Identity Protection szolgáltatás gyökerének](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)