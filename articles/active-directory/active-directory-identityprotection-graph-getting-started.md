---
title: "Ismerkedés az Azure Active Directory azonosító adatok védelmét és a Microsoft Graph |} Microsoft Docs"
description: "Tudnivalók a Microsoft Graph lekérdezni a kockázati eseményekről és a kapcsolódó adatokat az Azure Active Directory listáját."
services: active-directory
keywords: "az Azure active directory azonosító adatok védelmét, a kockázat esemény, a biztonsági rés, a biztonsági házirendet, a Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: fafad74f46baaf56a8220dab05028781b2f2258e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Ismerkedés az Azure Active Directory azonosító adatok védelmét és a Microsoft Graph
A Microsoft Graph a Microsoft unified API-végpont és az otthoni [Azure Active Directory Identity Protection](active-directory-identityprotection.md) API-k. Az első API **identityRiskEvents**, lehetővé teszi a Microsoft Graph listájának lekérdezési [kockázati események](active-directory-identityprotection-risk-events-types.md) és a kapcsolódó adatokat. Ez a cikk lekérdezi az API lekérdezése lépésekhez. Egy részletes bemutatása, teljes dokumentációját, és a Graph Explorer elérésére, tekintse meg a [Microsoft Graph hely](https://graph.microsoft.io/).


Identity Protection-adatok elérése a Microsoft Graph használatával négy lépésben történik:

1. A tartománynév beolvasása.
2. Hozzon létre egy új alkalmazás regisztrációja. 
2. A Microsoft Graph, amikor egy hitelesítési jogkivonatot kap hitelesítéshez használ a titkos kulcs és néhány egyéb adatot. 
3. Ez a token használatával kéréseket küld az API-végpontot, és vissza Identity Protection-adatok beolvasása.

Mielőtt hozzáfogna, szüksége lesz:

* Az alkalmazás létrehozása az Azure AD rendszergazdai jogosultságokat
* A bérlői tartomány (például contoso.onmicrosoft.com) neve


## <a name="retrieve-your-domain-name"></a>A tartománynév beolvasása 

1. [Jelentkezzen be a](https://portal.azure.com) az Azure portálra rendszergazdaként. 

2. A bal oldali navigációs ablaktábláján kattintson **Active Directory**. 
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. Az a **kezelése** kattintson **tulajdonságok**.

    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Másolja a tartomány nevét.


## <a name="create-a-new-app-registration"></a>Hozzon létre egy új alkalmazás regisztrálása

1. Az a **Active Directory** lap a **kezelése** kattintson **App regisztrációk**.

    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. Kattintson a felső menüben **új alkalmazás regisztrációja**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. Az a **létrehozása** lapon, a következő lépésekkel:
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. Az a **neve** szövegmező, írja be az alkalmazás nevét (pl.: AADIP kockázat esemény API-alkalmazás).
   
    b. Mint **típus**, jelölje be **webalkalmazás és / vagy webes API**.
   
    c. Az a **bejelentkezési URL-cím** szövegmezőhöz típus `http://localhost`.

    d. Kattintson a **Create** (Létrehozás) gombra.

4. Lehetőségre a **beállítások** oldalon, az alkalmazások listáját, kattintson az újonnan létrehozott alkalmazás regisztrációját. 

5. Másolás a **Alkalmazásazonosító**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Adja meg az alkalmazás számára az API-val

1. Az a **beállítások** kattintson **szükséges engedélyek**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. Az a **szükséges engedélyek** lapon, a felső eszköztáron kattintson **Hozzáadás**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. Az a **hozzáadása API-hozzáférés** kattintson **API kiválasztása**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. A a **API kiválasztása** lapon, hogy melyik **Microsoft Graph**, és kattintson a **válasszon**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. Az a **hozzáadása API-hozzáférés** kattintson **engedélyként válassza**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. A a **hozzáférés engedélyezése** lapján kattintson **olvassa el az összes kockázat identitásinformációi**, és kattintson a **válasszon**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. Az a **hozzáadása API-hozzáférés** kattintson **végzett**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. Az a **szükséges engedélyek** kattintson **engedélyt adjon**, és kattintson a **Igen**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Hívóbetű lekérése

1. Az a **beállítások** kattintson **kulcsok**.
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. Az a **kulcsok** lapon, a következő lépésekkel:
   
    ![Az alkalmazás létrehozása](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. Az a **kulcs leírását** szövegmezőhöz leírása a típust (például *AADIP kockázat esemény*).
    
    b. Mint **időtartam**, jelölje be **a 1 év**.

    c. Kattintson a **Save** (Mentés) gombra.
   
    d. Másolja a kulcs értékét, és illessze be egy biztonságos helyre.   
   
   > [!NOTE]
   > Ha elveszti a kulcsot, akkor ez a szakasz való visszatéréshez, és hozzon létre egy új kulcsot. Tartsa a kulcs titkos kulcs: bárki, aki hozzáférhet az adatokhoz.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Microsoft Graph hitelesítést és az identitás kockázati események API lekérdezése
Ezen a ponton rendelkeznie kell:

- A bérlői tartomány neve

- Az ügyfél-azonosító 

- A kulcs 


A hitelesítéshez a feladás egy vagy több vonatkozó kérés küldése `https://login.microsoft.com` törzsében a következő paraméterekkel:

- grant_type: "**client_credentials**"

-  erőforrás: "**https://graph.microsoft.com**"

- client_id: \<az ügyfél-azonosító\>

- client_secret: \<a kulcs\>


Ha sikeres, ez egy hitelesítési jogkivonatot ad vissza.  
Hívja az API-t, hogy hozzon létre egy fejléc a következő paraméter:

    `Authorization`=”<token_type> <access_token>"


Hitelesítésekor található a jogkivonat típusa és a hozzáférési jogkivonat a visszaküldött jogkivonat.

Ezt a fejlécet a kérés küldése a következő API URL-címe:`https://graph.microsoft.com/beta/identityRiskEvents`

A válasz, ha sikeres, gyűjteménye identitás kockázati eseményekről és kapcsolódó adatait, amely elemzése és kezelése, ahogyan szeretné OData JSON formátumban.

Itt látható mintakód a hitelesítés és a PowerShell használatával API felület meghívásakor.  
Csak adja hozzá az ügyfél-azonosító, a titkos kulcs és a bérlői tartományhoz.

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


## <a name="next-steps"></a>Következő lépések

Gratulálunk, a Microsoft Graph első hívás most végzett!  
Most identitás kockázati események és igényei azonban az adatok felhasználásával.

A Microsoft Graph és hogyan hozhat létre alkalmazásokat a Graph API-val kapcsolatos további tudnivalókért tekintse meg a [dokumentáció](https://graph.microsoft.io/docs) és sok más, a a [Microsoft Graph hely](https://graph.microsoft.io/). Bizonyosodjon meg arról, hogy lássa el könyvjelzővel a [Azure AD Identity Protection API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) oldal, amely felsorolja a identitás védelmi API-k gráfban. Azt hozzáadja az új módon történő együttműködésre Identity Protection API-n keresztül, adott oldalon láthatja azokat.

Kapcsolódó tudnivalókért lásd:

-  [Az Azure Active Directory azonosító adatok védelmét](active-directory-identityprotection.md)

-  [Azure Active Directory Identity Protection által észlelt kockázati események típusai](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [A Microsoft Graph áttekintése](https://graph.microsoft.io/docs)

- [Az Azure AD Identity Protection gyökerén](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

