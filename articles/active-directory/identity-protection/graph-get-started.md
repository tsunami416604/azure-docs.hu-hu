---
title: Az Azure Active Directory Identity Protection a Microsoft Graph |} A Microsoft Docs
description: Ismerje meg, hogyan kérdezhet le a Microsoft Graph kockázati eseményekről és kapcsolódó adatait az Azure Active Directory listáját.
services: active-directory
keywords: az Azure active directory identity protection, a kockázati esemény, a biztonsági rések, a biztonsági házirend, a Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d9935b5605bc09f000a81e7616e807a0e194e12
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295538"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Az Azure Active Directory Identity Protection és a Microsoft Graph használatának első lépései

A Microsoft Graph a Microsoft unified API-végpont és az otthoni [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API-k. Nincsenek kockázatos felhasználók és bejelentkezések közzétevő három API-kkal. Az első API **identityRiskEvents**, lehetővé teszi, hogy a Microsoft Graph lekérdezése listáját [kockázati események](../reports-monitoring/concept-risk-events.md) és a kapcsolódó adatokat. A második API-t, **riskyUsers**, lehetővé teszi, hogy a Microsoft Graph lekérdezési információ a felhasználók Identity Protection kockázatokat észleli. A harmadik API **bejelentkezési**, lehetővé teszi, hogy a Microsoft Graph lekérdezése az Azure AD bejelentkezési információk kapcsolatos kockázat állapota, az adott tulajdonságokkal rendelkező részletességi és szinten. Ez a cikk veheti használatba [csatlakozik a Microsoft Graph](#Connect-to-Microsoft-Graph) és [ezen API-k lekérdezése](#Query-the-APIs). Részletes bemutatása, teljes dokumentációt és a Graph Explorer a hozzáférést, tekintse meg a [Microsoft Graph-hely](https://graph.microsoft.io/) vagy API-k az adott segédanyagok:

* [identityRiskEvents API](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta)
* [riskyUsers API](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)
* [signIn API](https://docs.microsoft.com/graph/api/resources/signin?view=graph-rest-beta)


## <a name="connect-to-microsoft-graph"></a>Csatlakozás a Microsoft graph

Az Identity Protection-adatok elérése a Microsoft Graphon keresztül négy lépésben történik:

1. Beolvasni a tartomány nevét.
2. Hozzon létre egy új alkalmazás regisztrálása. 
3. A titkos kulcsot, és néhány további információt a Microsoft Graph, amikor egy hitelesítési tokent kap hitelesítéshez használ. 
4. A jogkivonat használatával kéréseket küld az API-végpont és az Identity Protection adatokat kaphat vissza.

Mielőtt elkezdené, szüksége lesz:

* Rendszergazdai jogosultságok az alkalmazás létrehozása az Azure ad-ben
* A bérlői tartomány (például contoso.onmicrosoft.com) neve


## <a name="retrieve-your-domain-name"></a>A tartománynév beolvasása 

1. [Jelentkezzen be a](https://portal.azure.com) az Azure Portalra rendszergazdaként. 

2. A bal oldali navigációs panelén kattintson **Active Directory**. 
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/41.png)


3. Az a **kezelés** területén kattintson **tulajdonságok**.

    ![Az alkalmazás létrehozása](./media/graph-get-started/42.png)

4. Copy your domain name.


## <a name="create-a-new-app-registration"></a>Hozzon létre egy új alkalmazás regisztrálása

1. Az a **Active Directory** lap a **kezelés** területén kattintson **alkalmazásregisztrációk**.

    ![Az alkalmazás létrehozása](./media/graph-get-started/42.png)


2. A felső menüben kattintson **új alkalmazásregisztráció**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/43.png)

3. Az a **létrehozás** lapon, a következő lépésekkel:
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/44.png)

    a. Az a **neve** szövegmezőbe írja be az alkalmazás nevét (például: AADIP kockázati esemény API-alkalmazás).
   
    b. Mint **típus**válassza **webalkalmazás és / vagy webes API-t**.
   
    c. Az a **bejelentkezési URL-** szövegmezőbe írja be `http://localhost`.

    d. Kattintson a **Create** (Létrehozás) gombra.

4. Megnyitásához a **beállítások** alkalmazásainak listájában kattintson az újonnan létrehozott alkalmazás regisztrációját. 

5. Másolás a **Alkalmazásazonosító**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Adja meg az alkalmazás számára az API-val

1. Az a **beállítások** kattintson **szükséges engedélyek**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/15.png)

2. Az a **szükséges engedélyek** oldalon, a felső eszköztáron kattintson **Hozzáadás**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/16.png)
   
3. Az a **API-hozzáférés hozzáadása** kattintson **API kiválasztása**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/17.png)

4. Az a **API kiválasztása** lapra, jelölje be **Microsoft Graph**, és kattintson a **kiválasztása**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/18.png)

5. Az a **API-hozzáférés hozzáadása** kattintson **engedélyek kiválasztása**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/19.png)

6. Az a **hozzáférés engedélyezése** kattintson **olvassa el az összes kockázati identitásadatok**, és kattintson a **válassza**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/20.png)

7. Az a **API-hozzáférés hozzáadása** kattintson **kész**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/21.png)

8. Az a **szükséges engedélyek** kattintson **engedélyek megadása**, és kattintson a **Igen**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/22.png)



## <a name="get-an-access-key"></a>Hívóbetű lekérése

1. Az a **beállítások** kattintson **kulcsok**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/23.png)

2. Az a **kulcsok** lapon, a következő lépésekkel:
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/24.png)

    a. Az a **kulcs leírása** szövegmezőbe be egy leírást (például *AADIP kockázati esemény*).
    
    b. Mint **időtartama**válassza **az 1 év**.

    c. Kattintson a **Save** (Mentés) gombra.
   
    d. Másolja a kulcs értékét, majd illessze be egy biztonságos helyre.   
   
   > [!NOTE]
   > Ha elveszíti a kulcsot, akkor ez a szakasz való visszatéréshez, és hozzon létre egy új kulcsot. Tartsa ezt a kulcsot egy titkos kulcsot: bárki, aki azt is elérhetők az adatok.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Microsoft Graph hitelesíteni, és az identitás kockázati események API lekérdezése
Ezen a ponton kell:

- A bérlői tartomány neve

- Az ügyfél-azonosító 

- A kulcs 


Hitelesítést végezni, küldjön egy post kérelmet `https://login.microsoft.com` törzsében a következő paraméterekkel:

- grant_type paraméter: "**client_credentials**"

-  erőforrás: `https://graph.microsoft.com`

- client_id: \<az ügyfél-azonosító\>

- titkos ügyfélkódot: \<a kulcs\>


Ha ez sikeres, ez egy hitelesítési tokent ad vissza.  
Hívja az API-t, hozzon létre egy fejléc a következő paramétert:

    `Authorization`=”<token_type> <access_token>"


A hitelesítéskor, a jogkivonat típusa és a hozzáférési jogkivonat megtalálhatja a visszaadott jogkivonat.

A fejléc küldése egy kérést az alábbi API URL-cím: `https://graph.microsoft.com/beta/identityRiskEvents`

A válasz sikeres művelet esetén olyan identitás kockázati események és az OData JSON formátumban, amely elemzi, és tetszés szerint kezelt kapcsolódó adatok gyűjteménye.

Itt látható mintakód a hitelesítés és a PowerShell-lel API meghívására.  
Adja hozzá az ügyfél-azonosító, a titkos kulcsot és a bérlő tartománya.

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

## <a name="query-the-apis"></a>Az API-k lekérdezése

Ezen három API-k számos lehetőséget kínál arra, a kockázatos felhasználók és bejelentkezések a szervezet adatainak beolvasásához adja meg. Ezen API-k és az ahhoz tartozó kérések az alábbiakban néhány gyakori felhasználási esetét. Ezeket a lekérdezéseket, azzal a mintakóddal, vagy a fenti futtathatja [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-the-high-risk-and-medium-risk-events-identityriskevents-api"></a>A magas kockázatú és közepes kockázatú események (identityRiskEvents API-t)

Közepes és magas kockázatú események képviselik, amelyek előfordulhat, hogy a funkció az Identity Protection bejelentkezési eseményindító vagy felhasználói kockázati szabályzat. Mivel rendelkeznek, közepes vagy nagy a valószínűsége annak, hogy a felhasználó bejelentkezési kísérlete ne legyen-e a azonosságát törvényes tulajdonosa, szervizelés ezeket az eseményeket kell lenniük. 

```
GET https://graph.microsoft.com/beta/identityRiskEvents?`$filter=riskLevel eq 'high' or riskLevel eq 'medium'" 
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Mindezt a felhasználók, akik sikeresen átadva az MFA-hitelesítést (riskyUsers API) kockázatos bejelentkezések szabályzat által aktivált

Tudni, hogy a szervezet rendelkezik kockázatalapú szabályzatok hatás Identity Protection lekérdezheti, ha a felhasználókat, akik sikeresen átadva az MFA-hitelesítést, a kockázatos bejelentkezések szabályzat által aktivált mindegyikét. Ez az információ segít, hogy mely felhasználók Identity Protection előfordulhat, hogy tévesen észleltük kockázatot és a jogosult felhasználók előfordulhat, hogy lehet műveleteket végrehajtani, amelyek a mesterséges Intelligencia megítélése kockázatos.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Minden kockázatos bejelentkezések beolvasása egy adott felhasználó (bejelentkezési API-t)

Ha úgy gondolja, hogy egy felhasználó sérült, jobban megérthetők a kockázati állapotának lekéri az összes, a kockázatos bejelentkezések. 
```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```




## <a name="next-steps"></a>További lépések

Gratulálunk, most végzett az első hívás a Microsoft Graph!  
Most identitás kockázati események, és használhatja az adatokat, de igény szerint.


A Microsoft Graph és hogyan hozhat létre alkalmazásokat a Graph API-val kapcsolatos további információkért tekintse meg a [dokumentáció](https://docs.microsoft.com/graph/overview) és sok mást a a [Microsoft Graph-hely](https://developer.microsoft.com/graph). 


Kapcsolódó tudnivalókért lásd:

-  [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

-  [Az Azure Active Directory Identity Protection által észlelt kockázati események típusai](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [A Microsoft Graph áttekintése](https://developer.microsoft.com/graph/docs)

- [Az Azure AD Identity Protection szolgáltatás legfelső szintű](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
