---
title: Az Azure Active Directory Identity Protection a Microsoft Graph |} A Microsoft Docs
description: Ismerje meg, hogyan kérdezhet le a Microsoft Graph kockázati eseményekről és kapcsolódó adatait az Azure Active Directory listáját.
services: active-directory
keywords: az Azure active directory identity protection, a kockázati esemény, a biztonsági rések, a biztonsági házirend, a Microsoft Graph
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: a415258b8127bb78d8a8d1b5ef228234b34b3e26
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51287509"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Az Azure Active Directory Identity Protection és a Microsoft Graph használatának első lépései
A Microsoft Graph a Microsoft unified API-végpont és az otthoni [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) API-k. Az első API **identityRiskEvents**, lehetővé teszi, hogy a Microsoft Graph lekérdezése listáját [kockázati események](../reports-monitoring/concept-risk-events.md) és a kapcsolódó adatokat. Ez a cikk lépéseit, az API lekérdezése beolvasása. Részletes bemutatása, teljes dokumentációt és a Graph Explorer a hozzáférést, tekintse meg a [Microsoft Graph-hely](https://developer.microsoft.com/graph/).


Az Identity Protection-adatok elérése a Microsoft Graphon keresztül négy lépésben történik:

1. Beolvasni a tartomány nevét.
2. Hozzon létre egy új alkalmazás regisztrálása. 
2. A titkos kulcsot, és néhány további információt a Microsoft Graph, amikor egy hitelesítési tokent kap hitelesítéshez használ. 
3. A jogkivonat használatával kéréseket küld az API-végpont és az Identity Protection adatokat kaphat vissza.

Mielőtt elkezdené, szüksége lesz:

- Az Azure AD P2-bérlő

- Rendszergazdai jogosultságok az alkalmazás létrehozása az Azure ad-ben

- A bérlői tartomány (például contoso.onmicrosoft.com) neve


## <a name="retrieve-your-domain-name"></a>A tartománynév beolvasása 

1. [Jelentkezzen be a](https://portal.azure.com) az Azure Portalra rendszergazdaként. 

2. A bal oldali navigációs panelén kattintson **Active Directory**. 
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/41.png)

3. Kattintson a **egyéni tartománynevek**.

    ![Egyéni tartománynevek](./media/graph-get-started/71.png)

4. Tartománynevek listája másolja a tartomány nevét, amely meg van jelölve elsődlegesként.

    ![Egyéni tartománynevek](./media/graph-get-started/72.png)



## <a name="create-a-new-app-registration"></a>Hozzon létre egy új alkalmazás regisztrálása

1. Az a **Active Directory** lap a **kezelés** területén kattintson **alkalmazásregisztrációk**.

    ![Az alkalmazás létrehozása](./media/graph-get-started/42.png)


2. A felső menüben kattintson **új alkalmazásregisztráció**.
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/43.png)

3. Az a **létrehozás** lapon, a következő lépésekkel:
   
    ![Az alkalmazás létrehozása](./media/graph-get-started/44.png)

    a. Az a **neve** szövegmezőbe írja be az alkalmazás nevét (pl.: AADIP kockázati esemény API-alkalmazás).
   
    b. Mint **alkalmazástípus**válassza **webalkalmazás és / vagy webes API-t**.
   
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

-  erőforrás: "**https://graph.microsoft.com**"

- client_id: \<az ügyfél-azonosító\>

- titkos ügyfélkódot: \<a kulcs\>


Ha ez sikeres, ez egy hitelesítési tokent ad vissza.  
Hívja az API-t, hozzon létre egy fejléc a következő paramétert:

    `Authorization`="<token_type> <access_token>"


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


## <a name="next-steps"></a>További lépések

Gratulálunk, most végzett az első hívás a Microsoft Graph!  
Most identitás kockázati események, és használhatja az adatokat, de igény szerint.

A Microsoft Graph és hogyan hozhat létre alkalmazásokat a Graph API-val kapcsolatos további információkért tekintse meg a [dokumentáció](https://developer.microsoft.com/graph/docs) és sok mást a a [Microsoft Graph-hely](https://developer.microsoft.com/graph/). Emellett ügyeljen arra, hogy lássa el könyvjelzővel a [API Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root) oldal, amely felsorolja az összes, az Identity Protection API-k Graph. Hozzáadása új munkamódszereket az Identity Protection API-n keresztül, az adott oldalon láthatja őket.

Kapcsolódó tudnivalókért lásd:

-  [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

-  [Az Azure Active Directory Identity Protection által észlelt kockázati események típusai](../reports-monitoring/concept-risk-events.md)

- [Microsoft Graph](https://developer.microsoft.com/graph/)

- [A Microsoft Graph áttekintése](https://developer.microsoft.com/graph/docs)

- [Az Azure AD Identity Protection szolgáltatás legfelső szintű](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)

