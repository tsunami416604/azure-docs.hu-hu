---
title: Microsoft Graph API az Azure Active Directory identitásvédelméhez
description: A Microsoft Graph kockázatészlelésének és a kapcsolódó információknak lekérdezése az Azure Active Directoryból
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6fd62ccab4d6e32b23835d280732797e133ada8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671622"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Ismerkedés az Azure Active Directory identitásvédelemmel és a Microsoft Graph-tal

A Microsoft Graph a Microsoft egységes API-végpontja és az [Azure Active Directory identity protection](../active-directory-identityprotection.md) API-k otthona. Négy API-k, amelyek a kockázatos felhasználók és a bejelentkezések információkat. Az első API, **riskDetection**, lehetővé teszi, hogy lekérdezze a Microsoft Graph egy listát a felhasználó és a bejelentkezéshez csatolt kockázatészlelések és az észleléskapcsolódó információkat. A második API, **riskyUsers**, lehetővé teszi, hogy lekérdezze a Microsoft Graph a felhasználók identity protection kockázatként észlelt információkat. A harmadik API, **signIn**, lehetővé teszi, hogy lekérdezheti a Microsoft Graph információkat az Azure AD bejelentkezések a kockázati állapot, a részletek és a szint kapcsolatos adott tulajdonságokkal. A negyedik API, **identityRiskEvents**, lehetővé teszi a Microsoft Graph lekérdezését a [kockázatészlelések](../reports-monitoring/concept-risk-events.md) és a kapcsolódó információk listájához. Az identityRiskEvents API 2020. javasoljuk, hogy használja a **riskDetections** API helyett. Ez a cikk a Microsoft Graph-hoz való csatlakozás és az API-k lekérdezésének megkezdését ismerteti. A Részletes bevezetést, a teljes dokumentációt és a Graph Explorerhez való hozzáférést a [Microsoft Graph webhelyen](https://graph.microsoft.io/) vagy az api-k ra vonatkozó konkrét referenciadokumentációban találja:

* [riskDetection API](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](/graph/api/resources/signin?view=graph-rest-beta)
* [identityRiskEvents API](/graph/api/resources/identityriskevent?view=graph-rest-beta) *lesz elavult január 10, 2020*

## <a name="connect-to-microsoft-graph"></a>Csatlakozás a Microsoft graph-hoz

Az Identitásvédelem adatainak a Microsoft Graphon keresztül történő elérésének négy lépése van:

1. A tartománynév beolvasása.
2. Hozzon létre egy új alkalmazásregisztrációt. 
3. Használja ezt a titkos kulcsot és néhány más információt a Microsoft Graph hitelesítéséhez, ahol hitelesítési jogkivonatot kap. 
4. Ezzel a jogkivonatkal kéréseket az API-végpontra, és az Identity Protection adatok vissza.

Mielőtt elkezdene, a következőkre lesz szüksége:

* Rendszergazdai jogosultságok az alkalmazás létrehozásához az Azure AD-ben
* A bérlő tartományának neve (például contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>A tartománynév beolvasása 

1. [Jelentkezzen be](https://portal.azure.com) az Azure Portalon rendszergazdaként. 
1. A bal oldali navigációs ablakban kattintson az **Active Directory**elemre. 

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/41.png)

1. A **Kezelés** csoportban kattintson a **Tulajdonságok gombra.**

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/42.png)

1. Másolja a tartománynevét.

## <a name="create-a-new-app-registration"></a>Új alkalmazásregisztráció létrehozása

1. Az **Active Directory** lap **Kezelés** területén kattintson az **Alkalmazásregisztrációk gombra.**

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/42.png)

1. A felső menüben kattintson az **Új alkalmazás regisztrációja parancsra.**

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/43.png)

1. A **Létrehozás** lapon hajtsa végre az alábbi lépéseket:

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/44.png)

   1. A **Név** mezőbe írja be az alkalmazás nevét (például: Azure AD kockázatészlelési API-alkalmazás).

   1. **Típusként**válassza a **Web Application And / Or Web API**lehetőséget.

   1. A **Bejelentkezési URL-cím** mezőbe `http://localhost`írja be a következőt:

   1. Kattintson **a Létrehozás gombra.**
1. A **Beállítások** lap megnyitásához kattintson az alkalmazások listájában az újonnan létrehozott alkalmazásregisztrációra. 
1. Másolja az **alkalmazásazonosítót**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Az alkalmazás engedélyének megadása az API használatához

1. A **Beállítások** lapon kattintson a **Kötelező engedélyek gombra.**

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/15.png)

1. A **Kötelező engedélyek** lap tetején kattintson a **Hozzáadás**gombra a felső eszköztáron.

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/16.png)

1. Az **API-hozzáférés hozzáadása** lapon kattintson az **API kiválasztása**gombra.

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/17.png)

1. Az **API kiválasztása** lapon válassza a **Microsoft Graph**lehetőséget, majd kattintson a **Kijelölés gombra.**

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/18.png)

1. Az **API-hozzáférés hozzáadása** lapon kattintson az **Engedélyek kiválasztása gombra.**

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/19.png)

1. A **Hozzáférés engedélyezése** lapon kattintson az **Összes identitáskockázati információ olvasása**elemre, majd a **Kijelölés gombra.**

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/20.png)

1. Az **API-hozzáférés hozzáadása** lapon kattintson a **Kész**gombra.

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/21.png)

1. A **Szükséges engedélyek** lapon kattintson az **Engedélyek megadása gombra,** majd az **Igen**gombra.

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Hívóbetű lekérése

1. A **Beállítások** lapon kattintson a **Billentyűk gombra.**

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/23.png)

1. A **Kulcsok** lapon hajtsa végre az alábbi lépéseket:

   ![Alkalmazás létrehozása](./media/howto-identity-protection-graph-api/24.png)

   1. A **Kulcs leírása** mezőbe írjon be egy leírást (például *Az Azure AD-kockázatészlelése).*
   1. **Időtartamként**válassza **az Egy évben**lehetőséget.
   1. Kattintson a **Mentés** gombra.
   1. Másolja a kulcs értékét, majd illessze be egy biztonságos helyre.   
   
   > [!NOTE]
   > Ha elveszíti ezt a kulcsot, vissza kell térnie ebbe a szakaszba, és létre kell hoznia egy új kulcsot. Tartsa titokban ezt a kulcsot: bárki, aki rendelkezik vele, hozzáférhet az adataihoz.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Hitelesítés a Microsoft Graph számára, és lekérdezés az identitáskockázat-észlelési API-ról

Ezen a ponton, akkor kellett volna:

- A bérlő tartományának neve
- Az ügyfélazonosító 
- A kulcs 

A hitelesítéshez küldjön `https://login.microsoft.com` egy bejegyzéskérést a törzsben lévő következő paraméterekkel:

- grant_type: "**client_credentials**"
- Erőforrás:`https://graph.microsoft.com`
- client_id: \<az ügyfélazonosítója\>
- client_secret: \<a kulcs\>

Ha sikeres, ez egy hitelesítési jogkivonatot ad vissza.  
Az API hívásához hozzon létre egy fejlécet a következő paraméterrel:

```
`Authorization`="<token_type> <access_token>"
```

Hitelesítéskor megtalálhatja a jogkivonat típusát és a hozzáférési jogkivonatot a visszaadott jogkivonatban.

Küldje el ezt a fejlécet kérelemként a következő API-URL-címre:`https://graph.microsoft.com/beta/identityRiskEvents`

A válasz, ha sikeres, az identitáskockázat-észlelések és a kapcsolódó adatok gyűjteménye az OData JSON formátumban, amely elemezhető és kezelhető, ahogy ön jónak látja.

Az alábbiakban mintát olvashat az API-k PowerShell használatával történő hitelesítéséhez és hívásához.  
Csak adja hozzá az ügyfélazonosítóját, a titkos kulcsot és a bérlői tartományt.

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

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Az API-k lekérdezése

Ez a három API számos lehetőséget kínál a kockázatos felhasználókra és a szervezetben való bejelentkezésre vonatkozó információk lekérésére. Az alábbiakban néhány gyakori használati esetek ezen API-k és a kapcsolódó mintakérelmek. Ezeket a lekérdezéseket a fenti mintakóddal vagy a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)használatával futtathatja.

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Az összes offline kockázatészlelés (riskDetection API) beszerezni

Az Identity Protection bejelentkezési kockázati szabályzatok, feltételeket alkalmazhat, ha a kockázat észlelése valós időben. De mi a helyzet az offline felfedezett észlelésekkel? Ha tisztában van azzal, hogy milyen észlelések történt kapcsolat nélküli módban, és így nem váltotta ki a bejelentkezési kockázati házirend, lekérdezheti a riskDetection API-t.

```
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>A kockázatos bejelentkezési házirend (riskyUsers API) által kiváltott MFA-kihívás sikeresen átadott összes felhasználó lehívása

Az Identitásvédelem kockázatalapú szabályzatok a szervezetre gyakorolt hatásának megértéséhez lekérdezheti az összes olyan felhasználót, aki sikeresen teljesítette a kockázatos bejelentkezési szabályzat által kiváltott MFA-kihívást. Ez az információ segíthet annak megértésében, hogy az Identity Protection mely felhasználókat észlelte tévesen kockázatként, és hogy a jogos felhasználók közül mely műveleteket hajtják végre a megfelelő ben.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Egy adott felhasználó kockázatos bejelentkezésének beszereznie (signIn API)

Ha úgy gondolja, hogy egy felhasználó biztonsága sérülhetett, jobban megértheti a kockázat állapotát az összes kockázatos bejelentkezésük beolvasásával. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>További lépések

Gratulálunk, most hívta fel először a Microsoft Graphot!  
Most lekérdezheti az identitáskockázat-észleléseket, és úgy használhatja az adatokat, ahogy ön jónak látja.

Ha többet szeretne megtudni a Microsoft Graphról és arról, hogyan hozhat létre alkalmazásokat a Graph API-val, tekintse meg a [dokumentációt](/graph/overview) és még sok mást a [Microsoft Graph webhelyen.](https://developer.microsoft.com/graph) 

A kapcsolódó információkat lásd:

- [Azure Active Directory identitásvédelem](../active-directory-identityprotection.md)
- [Az Azure Active Directory identity Protection által észlelt kockázatészlelési típusok](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [A Microsoft Graph áttekintése](https://developer.microsoft.com/graph/docs)
- [Az Azure AD identity protection szolgáltatás gyökér](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
